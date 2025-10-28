---
layout: default
title: Getting Started
permalink: /getting-started
---

# Getting Started with TheSocioscope

This guide will help you get up and running with Sleipnir and the research tools.

## Prerequisites

### For Sleipnir

- **Node.js** 18.x or higher
- **Yarn** 1.22.x or npm 9.x
- **Git** for version control
- **AWS Account** (optional, for S3 storage integration)

### For Research Tools

- **Python** 3.8 or higher
- **pip** package manager
- **Virtual environment** (recommended: venv or conda)

## Sleipnir Installation

### 1. Clone the Repository

```bash
git clone https://github.com/IEA-Paris/Sleipnir.git
cd Sleipnir
```

### 2. Install Dependencies

```bash
yarn install
# or
npm install
```

### 3. Configure Environment

Create a `.env` file based on `.env.example`:

```bash
cp .env.example .env
```

Edit `.env` with your configuration:

```env
# API Configuration
NUXT_PUBLIC_API_URL=http://localhost:3000/api

# AWS S3 Configuration (if using S3 storage module)
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_REGION=us-east-1
AWS_BUCKET_NAME=your-bucket-name

# Authentication (optional)
AUTH_SECRET=your-secret-key
```

### 4. Run Development Server

```bash
yarn dev
# or
npm run dev
```

The application will be available at `http://localhost:3000`

### 5. Build for Production

```bash
yarn build
yarn preview
# or
npm run build
npm run preview
```

## Research Tools Installation

### 1. Navigate to Research Directory

```bash
cd Research
```

### 2. Create Virtual Environment

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Download Required NLP Models

```bash
python -m spacy download en_core_web_sm
python -m nltk.downloader punkt
python -m nltk.downloader stopwords
```

### 5. Verify Installation

```bash
python evaluation/main.py --help
```

## Quick Start Guide

### Sleipnir Quick Start

#### 1. Create Your First Prompt Sheet

Navigate to the Prompt Sheets section and create a new sheet:

```yaml
name: my_first_prompt
version: 1.0.0
description: A simple sentiment analysis prompt
content: |
  Analyze the sentiment of the following text and provide a score from 0 (negative) to 10 (positive):

  {text}
```

#### 2. Create a Dataset

Upload or define a dataset with text samples to process.

#### 3. Run Your First Job

Create a job that applies your prompt to the dataset and execute it.

### Research Tools Quick Start

#### 1. Prepare Your Data

Place your data in the appropriate format:

```json
{
  "samples": [
    {
      "id": "1",
      "text": "Sample text for analysis",
      "expected": "expected_result"
    }
  ]
}
```

#### 2. Run Evaluation

```bash
python evaluation/main.py --input datasets/main.json --output results/
```

#### 3. View Results

Check the `results/` directory for evaluation metrics and analysis.

## Project Structure

### Sleipnir Structure

```
Sleipnir/
├── assets/          # Static assets (CSS, images)
├── components/      # Vue components
│   ├── common/      # Shared components
│   ├── features/    # Feature-specific components
│   └── layout/      # Layout components
├── composables/     # Vue composables
├── content/         # Content files (datasets, settings)
├── layouts/         # Nuxt layouts
├── pages/           # Application pages (routes)
├── modules/         # Custom Nuxt modules
├── plugins/         # Nuxt plugins
├── server/          # Server-side API routes
├── stores/          # Pinia stores
└── types/           # TypeScript type definitions
```

### Research Structure

```
Research/
├── datasets/        # Data collections
├── evaluation/      # Evaluation scripts
│   ├── main.py      # Main evaluation entry point
│   ├── rag.py       # RAG implementation
│   ├── scorers.py   # Scoring functions
│   └── processing.py # Data processing
├── jobs/            # Job results and outputs
└── results/         # Evaluation results
```

## Core Concepts

### Sleipnir Concepts

- **Prompt Sheets**: Versioned templates for LLM prompts
- **Datasets**: Collections of data to process
- **Jobs**: Execution workflows that apply prompts to datasets
- **Modules**: Extensible plugins for storage, rendering, and integrations
- **Versions**: Semantic versioning for prompts and configurations

### Research Concepts

- **Corpus**: Collection of documents for analysis
- **Features**: Extracted characteristics from text
- **Evaluation**: Testing and validation of LLM outputs
- **RAG**: Retrieval-Augmented Generation for enhanced responses
- **Scoring**: Metrics for evaluating response quality

## Common Tasks

### Add a New Module to Sleipnir

1. Create module directory: `modules/my-module/`
2. Implement module using Nuxt Kit
3. Register in `nuxt.config.ts`
4. Configure module options

See [Creating a Module](/sleipnir/creating-a-module) for details.

### Process New Dataset

1. Upload dataset to Sleipnir
2. Define or select prompt sheet
3. Create and configure job
4. Execute and monitor job
5. Review results

### Run Custom Evaluation

1. Prepare dataset in required format
2. Customize evaluation parameters
3. Run evaluation script
4. Analyze results and metrics

## Configuration

### Sleipnir Configuration

Edit `nuxt.config.ts` for application-wide settings:

```typescript
export default defineNuxtConfig({
  // Your configuration
  runtimeConfig: {
    public: {
      apiUrl: process.env.NUXT_PUBLIC_API_URL,
    },
  },
});
```

Edit `app.config.ts` for application metadata:

```typescript
export default defineAppConfig({
  title: "Sleipnir",
  description: "LLM-driven research tool",
});
```

### Research Configuration

Edit `evaluation/main.py` or create custom configuration files.

## Troubleshooting

### Common Issues

**Sleipnir won't start**

- Check Node.js version: `node --version`
- Clear node_modules and reinstall: `rm -rf node_modules && yarn install`
- Check for port conflicts

**Module not found errors**

- Run `yarn install` to ensure all dependencies are installed
- Check import paths in your code

**S3 connection issues**

- Verify AWS credentials in `.env`
- Check bucket permissions
- Ensure region is correct

**Python import errors**

- Activate virtual environment
- Reinstall requirements: `pip install -r requirements.txt`
- Check Python version compatibility

## Next Steps

- [Sleipnir Architecture](/sleipnir/architecture) - Understand the technical architecture
- [Creating a Module](/sleipnir/creating-a-module) - Extend Sleipnir with custom modules
- [Prompting Stack](/sleipnir/prompting-stack) - Learn about advanced prompt workflows
- [Research Evaluation](/research/evaluation) - Deep dive into evaluation frameworks
- [API Reference](/sleipnir/api-reference) - Complete API documentation

## Getting Help

- Check the [Glossary](/glossary) for term definitions
- Review documentation for specific topics
- Check GitHub issues for known problems
- Contact the development team

## Contributing

Contributions are welcome! Please refer to the contributing guidelines in each repository.

---

**Need help?** Refer to the specific documentation sections or reach out to the project maintainers.
