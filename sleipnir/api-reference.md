---
layout: default
title: Sleipnir - API Reference
permalink: /sleipnir/api-reference
---

# Sleipnir API Reference

This document provides a comprehensive reference for Sleipnir's composables, stores, and services.

## Composables

Composables are reusable functions that encapsulate reactive state and logic using Vue 3's Composition API.

### Authentication

#### `useAuth()`

Manages authentication state and operations.

```typescript
const {
  isAuthenticated, // Ref<boolean>
  user, // Ref<User | null>
  login, // (credentials) => Promise<void>
  logout, // () => Promise<void>
  checkAuth, // () => Promise<boolean>
} = useAuth();
```

### Prompt Management

#### `usePromptForm()`

Handles prompt sheet form state and operations.

```typescript
const {
  form, // Ref<PromptFormData>
  isValid, // ComputedRef<boolean>
  errors, // Ref<ValidationErrors>
  validate, // () => boolean
  reset, // () => void
  submit, // () => Promise<void>
} = usePromptForm();
```

#### `usePromptEditing()`

Manages prompt editing operations.

```typescript
const {
  content, // Ref<string>
  isDirty, // ComputedRef<boolean>
  save, // () => Promise<void>
  discard, // () => void
  undo, // () => void
  redo, // () => void
} = usePromptEditing();
```

#### `usePromptValidation()`

Validates prompt content and configuration.

```typescript
const {
  validate, // (prompt) => ValidationResult
  validateSyntax, // (content) => SyntaxResult
  validateConfig, // (config) => ConfigResult
  errors, // Ref<ValidationError[]>
} = usePromptValidation();
```

#### `usePromptStatus()`

Tracks prompt execution status.

```typescript
const {
  status, // Ref<'idle' | 'running' | 'success' | 'error'>
  progress, // Ref<number>
  startExecution, // () => void
  cancelExecution, // () => void
  reset, // () => void
} = usePromptStatus();
```

### Dataset Management

#### `useDatasetChips()`

Manages dataset selection and display.

```typescript
const {
  selectedDatasets, // Ref<Dataset[]>
  addDataset, // (dataset) => void
  removeDataset, // (id) => void
  clearAll, // () => void
} = useDatasetChips();
```

### Module Management

#### `useModuleManager()`

Manages Sleipnir modules.

```typescript
const {
  modules, // Ref<Module[]>
  searchModules, // (query, category?) => Promise<ModuleSearchResult[]>
  installModule, // (id, options) => Promise<boolean>
  uninstallModule, // (id) => Promise<boolean>
  activateModule, // (id) => Promise<boolean>
  configureModule, // (id, config) => Promise<void>
} = useModuleManager();
```

### Media Management

#### `useDownload()`

Handles file downloads.

```typescript
const {
  download, // (url, filename) => Promise<void>
  downloadMultiple, // (files) => Promise<void>
  isDownloading, // Ref<boolean>
  progress, // Ref<number>
} = useDownload();
```

### UI Components

#### `useModal()`

Manages modal dialogs.

```typescript
const {
  isOpen, // Ref<boolean>
  open, // (component, props?) => void
  close, // () => void
  confirm, // (message) => Promise<boolean>
} = useModal();
```

#### `useNotification()`

Displays toast notifications.

```typescript
const {
  notify, // (message, type?) => void
  success, // (message) => void
  error, // (message) => void
  warning, // (message) => void
  info, // (message) => void
} = useNotification();
```

#### `useGlobalLoading()`

Shows global loading indicator.

```typescript
const {
  isLoading, // Ref<boolean>
  startLoading, // () => void
  stopLoading, // () => void
} = useGlobalLoading();
```

### Navigation

#### `useKeyboardNavigation()`

Enables keyboard shortcuts.

```typescript
const {
  register, // (key, handler) => void
  unregister, // (key) => void
  enable, // () => void
  disable, // () => void
} = useKeyboardNavigation();
```

#### `useScrollSpy()`

Tracks scroll position for navigation.

```typescript
const {
  activeSection, // Ref<string>
  sections, // Ref<Section[]>
  scrollTo, // (id) => void
} = useScrollSpy();
```

### LLM Integration

#### `useLLMApi()`

Interacts with LLM APIs.

```typescript
const {
  sendPrompt, // (prompt, options) => Promise<Response>
  streamPrompt, // (prompt, callback) => Promise<void>
  listModels, // () => Promise<Model[]>
  getTokenCount, // (text) => number
} = useLLMApi();
```

### Form Validation

#### `useValidation()`

General form validation utilities.

```typescript
const {
  validate, // (value, rules) => ValidationResult
  required, // (message?) => Rule
  minLength, // (length, message?) => Rule
  maxLength, // (length, message?) => Rule
  email, // (message?) => Rule
  url, // (message?) => Rule
} = useValidation();
```

## Stores (Pinia)

### Main Store

```typescript
import { useMainStore } from "~/stores/main";

const mainStore = useMainStore();

// State
mainStore.promptSheets; // PromptSheet[]
mainStore.datasets; // Dataset[]
mainStore.jobs; // Job[]

// Actions
mainStore.fetchPromptSheets();
mainStore.createPromptSheet(data);
mainStore.updatePromptSheet(id, data);
mainStore.deletePromptSheet(id);
```

### Auth Store

```typescript
import { useAuthStore } from "~/stores/auth";

const authStore = useAuthStore();

// State
authStore.user; // User | null
authStore.token; // string | null
authStore.isAuthenticated; // boolean

// Actions
authStore.login(credentials);
authStore.logout();
authStore.refreshToken();
```

### Media Store

```typescript
import { useMediaStore } from "~/stores/media";

const mediaStore = useMediaStore();

// State
mediaStore.files; // MediaFile[]
mediaStore.selectedFiles; // MediaFile[]

// Actions
mediaStore.fetchFiles();
mediaStore.uploadFile(file);
mediaStore.deleteFile(id);
mediaStore.generatePresignedUrl(id);
```

### Modules Store

```typescript
import { useModulesStore } from "~/stores/modules";

const modulesStore = useModulesStore();

// State
modulesStore.installed; // Module[]
modulesStore.active; // Module[]

// Actions
modulesStore.loadModules();
modulesStore.activateModule(id);
modulesStore.deactivateModule(id);
```

## Services

### LLM Service

```typescript
import { llmService } from "~/services/llm.service";

// Send prompt to LLM
const response = await llmService.generate({
  prompt: "Your prompt here",
  model: "gpt-4",
  temperature: 0.7,
  maxTokens: 500,
});

// Stream response
await llmService.generateStream({
  prompt: "Your prompt here",
  onChunk: (chunk) => console.log(chunk),
});
```

## Server API Routes

### Authentication

```
POST   /api/auth/login
POST   /api/auth/logout
POST   /api/auth/refresh
GET    /api/auth/me
```

### Prompts

```
GET    /api/prompts
GET    /api/prompts/:id
POST   /api/prompts
PUT    /api/prompts/:id
DELETE /api/prompts/:id
```

### Datasets

```
GET    /api/datasets
GET    /api/datasets/:id
POST   /api/datasets
PUT    /api/datasets/:id
DELETE /api/datasets/:id
```

### Jobs

```
GET    /api/jobs
GET    /api/jobs/:id
POST   /api/jobs
POST   /api/jobs/:id/execute
GET    /api/jobs/:id/status
DELETE /api/jobs/:id
```

### Media

```
GET    /api/media
POST   /api/media/upload
GET    /api/media/:id
DELETE /api/media/:id
GET    /api/media/:id/presigned-url
```

## Type Definitions

### Prompt Sheet

```typescript
interface PromptSheet {
  id: string;
  name: string;
  version: string;
  content: string;
  variables: Variable[];
  config: PromptConfig;
  createdAt: Date;
  updatedAt: Date;
  author: string;
}
```

### Dataset

```typescript
interface Dataset {
  id: string;
  name: string;
  description: string;
  schema: DatasetSchema;
  rows: Record<string, any>[];
  createdAt: Date;
  updatedAt: Date;
}
```

### Job

```typescript
interface Job {
  id: string;
  name: string;
  promptSheetId: string;
  datasetId: string;
  config: JobConfig;
  status: "pending" | "running" | "completed" | "failed";
  progress: number;
  results: JobResult[];
  createdAt: Date;
  startedAt?: Date;
  completedAt?: Date;
}
```

### Module

```typescript
interface Module {
  id: string;
  name: string;
  version: string;
  category: "storage" | "rendering" | "integration" | "transformation";
  description: string;
  author: string;
  config: ModuleConfig;
  isActive: boolean;
}
```

## Events

### Global Events

```typescript
// Listen to events
nuxtApp.$bus.on("prompt:saved", (prompt) => {
  console.log("Prompt saved:", prompt);
});

// Emit events
nuxtApp.$bus.emit("prompt:saved", prompt);
```

### Available Events

- `prompt:saved` - Prompt sheet saved
- `prompt:deleted` - Prompt sheet deleted
- `job:started` - Job execution started
- `job:completed` - Job execution completed
- `job:failed` - Job execution failed
- `module:installed` - Module installed
- `module:activated` - Module activated

## Error Handling

### API Errors

```typescript
try {
  await mainStore.fetchPromptSheets();
} catch (error) {
  if (error.statusCode === 404) {
    // Handle not found
  } else if (error.statusCode === 401) {
    // Handle unauthorized
  } else {
    // Handle other errors
  }
}
```

### Validation Errors

```typescript
const { validate, errors } = useValidation();

const result = validate(value, [required(), minLength(3)]);

if (!result.isValid) {
  console.log(errors.value);
}
```

## Best Practices

### Composable Usage

- Always destructure only what you need
- Use composables at component setup level
- Don't call composables conditionally
- Reuse composables across components

### Store Usage

- Use stores for shared state
- Keep stores focused and modular
- Use actions for async operations
- Use getters for computed state

### Type Safety

- Always use TypeScript types
- Leverage type inference
- Define interfaces for complex data
- Use generics where appropriate

## Related Documentation

- [Architecture](/sleipnir/architecture)
- [Composables Guide](/sleipnir/composables)
- [Creating a Module](/sleipnir/creating-a-module)
- [Getting Started](/getting-started)
