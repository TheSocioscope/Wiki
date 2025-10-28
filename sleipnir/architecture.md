---
layout: default
title: Sleipnir Architecture
permalink: /sleipnir/architecture
---

# Sleipnir Architecture

This document provides a comprehensive overview of Sleipnir's technical architecture, design decisions, and core components.

## Overview

Sleipnir is a modern web application built on **Nuxt 3** that provides a comprehensive platform for LLM-driven research workflows. It combines prompt engineering, version control, job execution, and module extensibility in a unified interface.

## Technology Stack

### Frontend

- **Framework**: Nuxt 3 (Vue 3 + TypeScript)
- **UI Library**: Vuetify 3 (Material Design components)
- **State Management**: Pinia
- **Routing**: Vue Router (via Nuxt)
- **Animations**: VueUse Motion
- **Icons**: Material Design Icons (@mdi/font)

### Content Management

- **Nuxt Content**: For managing datasets, prompts, and configurations
- **Markdown Support**: For documentation and content authoring

### Development Tools

- **TypeScript**: Type-safe development
- **ESLint**: Code linting with Nuxt ESLint config
- **Prettier**: Code formatting
- **Vitest**: Unit testing framework

### Integrations

- **AWS S3**: Media storage via custom module
- **i18n**: Multi-language support via @nuxtjs/i18n
- **TipTap**: Rich text editing for prompts

## Architecture Patterns

### Component-Based Architecture

Sleipnir follows a modular component-based architecture:

```
components/
├── common/          # Shared, reusable components
├── features/        # Feature-specific components
├── interpreters/    # Data interpretation components
├── layout/          # Layout components (header, sidebar, etc.)
├── media/           # Media handling components
├── navigation/      # Navigation components
└── ui/              # Base UI components
```

### Composition API Pattern

All components use Vue 3's Composition API with composables for business logic:

```typescript
// Example composable structure
export const usePromptForm = () => {
  const state = ref<PromptState>({...})

  const loadPrompt = async (id: string) => {
    // Logic
  }

  const savePrompt = async () => {
    // Logic
  }

  return {
    state,
    loadPrompt,
    savePrompt
  }
}
```

### Store Pattern (Pinia)

Global state management using Pinia stores:

```typescript
// stores/main.ts
export const useMainStore = defineStore("main", {
  state: () => ({
    promptSheets: [],
    datasets: [],
    // ...
  }),

  actions: {
    async fetchPromptSheets() {
      // Logic
    },
  },
});
```

## Core Modules

### 1. Prompt Management

**Location**: `pages/[id].vue`, `composables/usePrompt*.ts`

Handles creation, editing, and versioning of prompt sheets:

- Version control with semantic versioning
- Rich text editing with TipTap
- Prompt validation
- Template variables support

### 2. Dataset Management

**Location**: `pages/datasets.vue`, `content/datasets.json`

Manages data collections for processing:

- Dataset creation and import
- Schema definition
- Data validation
- Preview and exploration

### 3. Job Execution

**Location**: `pages/sheet/[id].vue`, `composables/usePromptStatus.ts`

Orchestrates prompt execution workflows:

- Job definition and configuration
- Batch processing
- Progress tracking
- Result aggregation

### 4. Media Management

**Location**: `pages/media.vue`, `stores/media.ts`, `composables/useDownload.ts`

Handles file storage and retrieval:

- AWS S3 integration via custom module
- File upload/download
- Pre-signed URL generation
- Metadata management

### 5. Versioning System

**Location**: `pages/versions.vue`, `composables/usePromptSheetSettings.ts`

Manages version control for prompts:

- Semantic versioning (MAJOR.MINOR.PATCH)
- Version comparison
- Rollback capabilities
- Change tracking

### 6. Module System

**Location**: `modules/`, `composables/useModuleManager.ts`

Extensible plugin architecture:

- **Storage Modules**: AWS S3, Google Drive, FTP
- **Rendering Modules**: Markdown, PDF, Mermaid
- **Integration Modules**: ChatGPT, APIs, GitHub
- **Transformation Modules**: Data format converters

## Custom Modules

### AWS S3 Storage Module

**Location**: `modules/sleipnir-storage-aws-s3/`

Provides S3 integration for media storage:

```typescript
// Module structure
├── src/
│   ├── module.ts              # Nuxt module definition
│   └── runtime/
│       ├── composables/
│       │   └── useS3Storage.ts  # S3 operations
│       └── plugins/
│           └── s3.client.ts     # S3 client setup
```

**Features**:

- Pre-signed URL generation
- Upload/download operations
- Bucket management
- Access control

## Key Composables

### Authentication

- `useAuth.ts`: Authentication state and operations
- `useCustomAuth.ts`: Custom auth logic

### Form Management

- `useCustomForm.ts`: Form state management
- `useValidation.ts`: Form validation logic
- `usePromptForm.ts`: Prompt-specific form handling

### Prompt Operations

- `usePromptEditing.ts`: Prompt editing operations
- `usePromptValidation.ts`: Prompt validation
- `usePromptStatus.ts`: Execution status tracking
- `usePromptOptions.ts`: Configuration options

### UI & Navigation

- `useModal.ts`: Modal management
- `useNotification.ts`: Toast notifications
- `useKeyboardNavigation.ts`: Keyboard shortcuts
- `useScrollSpy.ts`: Scroll position tracking

### LLM Integration

- `useLLMApi.ts`: LLM API interactions
- `usePromptSheetSettings.ts`: Prompt configuration

### Data Management

- `useDatasetChips.ts`: Dataset UI components
- `useFilters.ts`: Data filtering
- `useDownload.ts`: File downloads

## Server-Side Architecture

### API Routes

**Location**: `server/api/`

Server-side API endpoints for:

- Authentication
- Data persistence
- External API proxying
- File operations

### Services

**Location**: `services/`

Business logic services:

- `llm.service.ts`: LLM provider abstraction

## Data Flow

### Prompt Execution Flow

```
1. User creates/edits prompt sheet
   ↓
2. Prompt validated and saved
   ↓
3. User selects dataset
   ↓
4. Job configured with parameters
   ↓
5. Job submitted to execution engine
   ↓
6. Results processed and stored
   ↓
7. User views/exports results
```

### Module Installation Flow

```
1. User searches for module (npm registry)
   ↓
2. Module metadata displayed
   ↓
3. User initiates installation
   ↓
4. Module downloaded and registered
   ↓
5. Module configuration
   ↓
6. Module activated and available
```

## Configuration

### Nuxt Configuration

**File**: `nuxt.config.ts`

Key configurations:

- Module registration
- i18n setup
- Build settings
- Runtime config
- Component auto-import

### App Configuration

**File**: `app.config.ts`

Application metadata:

- Title and description
- Theme configuration
- Language settings
- Font configuration

### Content Configuration

**File**: `content.config.ts`

Content management settings:

- Markdown options
- Syntax highlighting
- Content sources

## Type System

### Core Types

**Location**: `types/`

Type definitions for:

- `auth.d.ts`: Authentication types
- `append.ts`, `prepend.ts`: Prompt modifiers
- `datasets.ts`: Dataset structures
- `enums.ts`: Application enums
- `modules.ts`: Module interfaces
- `prompts.ts`: Prompt definitions
- `versions.ts`: Version control types

## Internationalization (i18n)

**Location**: `i18n/translations/`

Multi-language support:

- English (en)
- French (fr)
- Translation keys organized by feature
- Lazy loading of translations

## Security

### Authentication

- Custom authentication system
- Token-based auth
- Session management
- Protected routes via middleware

### Data Protection

- Environment variable management
- Secure API key storage
- Input validation
- XSS protection

## Performance Optimizations

### Code Splitting

- Automatic page-level code splitting
- Lazy-loaded components
- Dynamic imports for large modules

### Caching

- Component caching
- API response caching
- Static asset caching

### SSR (Server-Side Rendering)

- Improved initial load time
- Better SEO
- Progressive enhancement

## Deployment

### Build Process

```bash
# Development
yarn dev

# Production build
yarn build

# Preview production build
yarn preview

# Generate static site
yarn generate
```

### Environment Variables

Required variables:

```env
NUXT_PUBLIC_API_URL
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_REGION
AWS_S3_BUCKET
NUXT_AUTH_SECRET
```

## Testing Strategy

### Unit Tests

- Vitest for component testing
- Composable testing
- Store testing

### Integration Tests

- Nuxt Test Utils
- API endpoint testing
- Module integration testing

## Design Principles

### Modularity

- Self-contained modules
- Clear interfaces
- Minimal coupling

### Type Safety

- TypeScript throughout
- Strict type checking
- Runtime validation

### Developer Experience

- Hot module replacement
- Type inference
- Clear error messages
- Comprehensive documentation

### User Experience

- Responsive design
- Smooth animations
- Intuitive navigation
- Accessibility compliance

## Future Architecture Considerations

### Scalability

- Worker threads for processing
- Queue system for jobs
- Distributed caching
- Load balancing

### Extensions

- Plugin marketplace
- Custom module development
- Webhook system
- API expansion

## Related Documentation

- [Creating a Module]({{ site.baseurl }}/sleipnir/creating-a-module)
- [API Reference]({{ site.baseurl }}/sleipnir/api-reference)
- [Composables]({{ site.baseurl }}/sleipnir/composables)
- [Getting Started]({{ site.baseurl }}/getting-started)
