---
layout: default
title: Sleipnir - Creating a Module
permalink: /sleipnir/creating-a-module
---

# Sleipnir - Creating a Module

This guide provides step-by-step instructions for creating custom modules in Sleipnir.

## Overview

Sleipnir is built on a modular architecture that allows you to extend its functionality by creating custom modules. Modules encapsulate specific functionality and can be reused across different jobs and prompting stacks.

## Module Structure

A Sleipnir module in the Nuxt-based architecture consists of the following components:

```
modules/my-module/
├── src/
│   ├── module.ts           # Main module entry point
│   ├── runtime/
│   │   ├── composables/    # Module composables
│   │   ├── components/     # Module components
│   │   └── plugins/        # Module plugins
├── playground/             # Module testing environment
├── package.json            # Module metadata
└── README.md               # Module documentation
```

## Creating Your First Module

### Step 1: Set Up Module Directory

```bash
mkdir -p modules/my-module/src/runtime/{composables,components,plugins}
cd modules/my-module
```

### Step 2: Define Module Configuration

Create `package.json`:

```json
{
  "name": "@sleipnir/my-module",
  "version": "1.0.0",
  "description": "Brief description of module functionality",
  "main": "./src/module.ts",
  "type": "module",
  "exports": {
    ".": {
      "types": "./dist/types.d.ts",
      "import": "./dist/module.mjs",
      "require": "./dist/module.cjs"
    }
  },
  "dependencies": {},
  "devDependencies": {
    "@nuxt/kit": "latest",
    "@nuxt/schema": "latest"
  }
}
```

### Step 3: Implement Module Logic

Create `src/module.ts`:

```typescript
import {
  defineNuxtModule,
  addPlugin,
  addComponent,
  createResolver,
} from "@nuxt/kit";

export interface ModuleOptions {
  param1?: string;
  param2?: number;
}

export default defineNuxtModule<ModuleOptions>({
  meta: {
    name: "my-module",
    configKey: "myModule",
    compatibility: {
      nuxt: "^3.0.0",
    },
  },
  defaults: {
    param1: "default-value",
    param2: 10,
  },
  async setup(options, nuxt) {
    const resolver = createResolver(import.meta.url);

    // Add plugin
    addPlugin(resolver.resolve("./runtime/plugins/my-plugin"));

    // Add composables
    nuxt.hook("imports:dirs", (dirs) => {
      dirs.push(resolver.resolve("./runtime/composables"));
    });

    // Add components
    addComponent({
      name: "MyComponent",
      filePath: resolver.resolve("./runtime/components/MyComponent.vue"),
    });

    // Module initialization logic
    console.log("My module initialized with options:", options);
  },
});
```

### Step 4: Create Module Composables

Create `src/runtime/composables/useMyFeature.ts`:

```typescript
import { ref, computed } from "vue";

export const useMyFeature = () => {
  const state = ref<string>("");

  const processData = (input: string) => {
    // Your module logic here
    state.value = input.toUpperCase();
    return state.value;
  };

  const isProcessed = computed(() => state.value.length > 0);

  return {
    state,
    processData,
    isProcessed,
  };
};
```

### Step 5: Create Module Components

Create `src/runtime/components/MyComponent.vue`:

```vue
<template>
  <div class="my-component">
    <input v-model="inputValue" @input="handleInput" />
    <p>Processed: {{ processed }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { useMyFeature } from "../composables/useMyFeature";

const { processData, state } = useMyFeature();
const inputValue = ref("");
const processed = state;

const handleInput = () => {
  processData(inputValue.value);
};
</script>
```

### Step 6: Add Module Plugin

Create `src/runtime/plugins/my-plugin.ts`:

```typescript
export default defineNuxtPlugin((nuxtApp) => {
  // Plugin initialization logic
  return {
    provide: {
      myModuleFunction: () => {
        console.log("Module function called");
      },
    },
  };
});
```

### Step 7: Add Module Documentation

Create `README.md` with:

- Purpose and use cases
- Configuration parameters
- Installation instructions
- Usage examples
- API reference
- Known limitations

## Module Best Practices

### Design Principles

- **Single Responsibility** - Each module should have one clear purpose
- **Loose Coupling** - Minimize dependencies on other modules
- **High Cohesion** - Related functionality should be grouped together
- **Clear Interface** - Well-defined inputs and outputs

### Configuration

- Use sensible defaults for optional parameters
- Validate all required parameters
- Document all configuration options
- Support environment variable overrides

### Error Handling

- Provide clear error messages
- Log errors with context
- Implement graceful degradation where appropriate
- Use TypeScript for type safety

### Performance

- Optimize for common use cases
- Implement caching where beneficial
- Support lazy loading
- Monitor resource usage

## Integration with Sleipnir

### Registering Your Module

Add your module to `nuxt.config.ts`:

```typescript
export default defineNuxtConfig({
  modules: ["./modules/my-module/src/module"],
  myModule: {
    param1: "custom-value",
    param2: 20,
  },
});
```

### Using Your Module in Components

Reference your module in components:

```vue
<template>
  <div>
    <MyComponent />
  </div>
</template>

<script setup lang="ts">
import { useMyFeature } from "#imports";

const { processData, state } = useMyFeature();
</script>
```

## Example: AWS S3 Storage Module

The `sleipnir-storage-aws-s3` module is a real example from Sleipnir:

```
modules/sleipnir-storage-aws-s3/
├── src/
│   ├── module.ts
│   └── runtime/
│       ├── composables/
│       │   └── useS3Storage.ts
│       └── plugins/
│           └── s3.client.ts
└── package.json
```

This module provides:

- S3 client configuration
- File upload/download composables
- Pre-signed URL generation
- Integration with Sleipnir's media management

## Testing Your Module

### Unit Testing

Create tests in `tests/`:

```typescript
import { describe, it, expect } from "vitest";
import { useMyFeature } from "../src/runtime/composables/useMyFeature";

describe("useMyFeature", () => {
  it("processes data correctly", () => {
    const { processData } = useMyFeature();
    const result = processData("test");
    expect(result).toBe("TEST");
  });
});
```

### Integration Testing

Test module integration:

```bash
npm run dev:playground
```

## Publishing Your Module

1. Ensure all tests pass
2. Update version number
3. Document changes in changelog
4. Build the module: `npm run build`
5. Publish to npm: `npm publish`

## Related Documentation

- [Versioning System]({{ site.baseurl }}/sleipnir/versioning-system)
- [Prompting Stack]({{ site.baseurl }}/sleipnir/prompting-stack)
- [Jobs]({{ site.baseurl }}/sleipnir/jobs)
- [API Reference]({{ site.baseurl }}/sleipnir/api-reference)
- [Glossary]({{ site.baseurl }}/glossary)
