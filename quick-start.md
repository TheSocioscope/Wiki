---
layout: default
title: Quick Start Guide
permalink: /quick-start
---

# Quick Start Guide

Get up and running with Sleipnir and the research tools in minutes.

## Prerequisites Checklist

- [ ] Node.js 18.x or higher
- [ ] Python 3.8 or higher
- [ ] Git installed
- [ ] Code editor (VS Code recommended)

## Sleipnir Quick Start

### 1. Clone and Install (5 minutes)

```bash
# Clone repository
git clone https://github.com/IEA-Paris/Sleipnir.git
cd Sleipnir

# Install dependencies
yarn install

# Set up environment
cp .env.example .env
```

### 2. Configure Environment (2 minutes)

Edit `.env`:

```env
# Minimum required configuration
NUXT_PUBLIC_API_URL=http://localhost:3000/api
```

### 3. Start Development Server (1 minute)

```bash
yarn dev
```

Visit `http://localhost:3000` 🎉

### 4. Create Your First Prompt (5 minutes)

1. Navigate to **Prompt Sheets**
2. Click **New Prompt Sheet**
3. Enter:
   ```yaml
   Name: My First Prompt
   Version: 1.0.0
   Content:
   Analyze the sentiment of this text: {text}
   Rate from 1-10 where 1 is very negative and 10 is very positive.
   ```
4. Save

### 5. Create a Dataset (3 minutes)

1. Go to **Datasets**
2. Click **New Dataset**
3. Add sample data:
   ```json
   [
     { "text": "I love this product!" },
     { "text": "This is terrible." },
     { "text": "It's okay, nothing special." }
   ]
   ```
4. Save as "Sentiment Test"

### 6. Run Your First Job (2 minutes)

1. Open your prompt sheet
2. Click **Execute**
3. Select "Sentiment Test" dataset
4. Click **Run**
5. View results

## Research Tools Quick Start

### 1. Setup Environment (3 minutes)

```bash
cd Research

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 2. Download NLP Models (2 minutes)

```bash
python -m spacy download en_core_web_sm
python -m nltk.downloader punkt stopwords wordnet
```

### 3. Run Sample Evaluation (1 minute)

```bash
python evaluation/main.py
```

Check `results/` directory for output.

### 4. Test RAG System (3 minutes)

Create `test_rag.py`:

```python
from evaluation.rag import prompt
import os

# Set authentication
auth = os.environ.get('EVALUATION_API_AUTH', 'user:pass')

# Send query
response = prompt({
    "k": 5,
    "filter": {},
    "question": "What are the main themes?",
    "model": "openai:gpt-4o-mini",
    "auth": auth
})

print(response['answer'])
```

Run:

```bash
python test_rag.py
```

## Common First Tasks

### Add AWS S3 Storage to Sleipnir

1. Add to `.env`:

   ```env
   AWS_ACCESS_KEY_ID=your_key
   AWS_SECRET_ACCESS_KEY=your_secret
   AWS_REGION=us-east-1
   AWS_S3_BUCKET=your-bucket
   ```

2. Module is already configured in `nuxt.config.ts`

3. Upload files via **Media** page

### Create a Custom Module

```bash
# Create module structure
mkdir -p modules/my-module/src/runtime
cd modules/my-module

# Create basic files
touch src/module.ts
touch package.json
```

See [Creating a Module]({{ site.baseurl }}/sleipnir/creating-a-module) for details.

### Process Interview Transcripts

```python
from evaluation.processing import process_data
from evaluation.rag import prompt

# Load transcript
corpus = process_data('datasets/interviews.json')

# Query with RAG
for qa in corpus.qa_pairs:
    response = prompt({
        "k": 10,
        "filter": {"TYPE": "interview"},
        "question": qa.question.text,
        "model": "openai:gpt-4o-mini",
        "auth": auth
    })
    print(response['answer'])
```

## Troubleshooting Quick Fixes

### Sleipnir won't start

```bash
# Clear cache and reinstall
rm -rf node_modules .nuxt
yarn install
yarn dev
```

### Python import errors

```bash
# Reinstall in virtual environment
pip install --force-reinstall -r requirements.txt
```

### Port already in use

```bash
# Change port in package.json or use:
yarn dev --port 3001
```

### Module not found

```bash
# Regenerate Nuxt
rm -rf .nuxt
yarn dev
```

## Next Steps

### For Sleipnir Users

1. ✅ [Architecture Overview]({{ site.baseurl }}/sleipnir/architecture)
2. ✅ [Prompting Stack Guide]({{ site.baseurl }}/sleipnir/prompting-stack)
3. ✅ [Module Development]({{ site.baseurl }}/sleipnir/creating-a-module)
4. ✅ [API Reference]({{ site.baseurl }}/sleipnir/api-reference)

### For Researchers

1. ✅ [Evaluation Framework]({{ site.baseurl }}/research/evaluation)
2. ✅ [Data Collection Process]({{ site.baseurl }}/data/collection)
3. ✅ [Storage Strategy]({{ site.baseurl }}/data/storage-backup)

### For Both

1. ✅ [Full Installation Guide]({{ site.baseurl }}/getting-started)
2. ✅ [Glossary]({{ site.baseurl }}/glossary)

## Getting Help

- 📖 Browse the [full documentation](/)
- 🔍 Check the [Glossary]({{ site.baseurl }}/glossary)
- 💬 Open an issue on GitHub
- 📧 Contact the team

---

**Stuck?** Most issues are covered in the [Getting Started]({{ site.baseurl }}/getting-started) guide or [Glossary]({{ site.baseurl }}/glossary).
