---
layout: default
title: Research - Evaluation Framework
permalink: /research/evaluation
---

# Research Evaluation Framework

This document details the evaluation framework used in TheSocioscope research project for testing and validating LLM outputs.

## Overview

The evaluation framework provides comprehensive tools for assessing the quality, accuracy, and reliability of LLM-generated responses. It includes multiple scoring methods, verbatim checking, statistical analysis, and RAG (Retrieval-Augmented Generation) capabilities.

## Architecture

### Core Components

```
evaluation/
├── main.py              # Main orchestration script
├── processing.py        # Data processing and corpus management
├── scorers.py           # Scoring functions for different question types
├── rag.py               # RAG implementation with Modal API
├── verbatim_checker.py  # Citation verification
├── statistic.py         # Statistical analysis and visualization
├── synonymy.py          # Synonym enrichment for scoring
└── datastructure.py     # Data models and structures
```

## Evaluation Pipeline

### Main Workflow

```python
# 1. Load questions from input JSON
corpus = process_data('evaluation/data_fixture.json')

# 2. Load transcripts for verbatim checking
transcripts = load_transcripts('evaluation/datasets/corpus.json')

# 3. Score each answer
for qa_pair in corpus.qa_pairs:
    for answer in qa_pair.answers:
        scorer = get_scorer(qa_pair.question.type)
        if scorer:
            answer.score = scorer(answer.answer, qa_pair.question.metric)

# 4. Check verbatims and assign existence scores
process_verbatims(qa_pair, transcripts)

# 5. Save results with timestamp
save_results(corpus, output_file)

# 6. Generate statistics and visualizations
transcript_avg, verbatim_avg = get_average_score(corpus)
plot_score(transcript_avg)
plot_score(verbatim_avg)
```

## Question Types and Scoring

### Supported Question Types

The framework supports six question types, each with specialized scoring logic:

#### 1. Yes/No Questions

**Type**: `Yes/No`

**Scoring Logic**:

- Binary match: 1.0 for correct, 0.0 for incorrect
- Handles variations: "yes", "Yes", "YES", "y", etc.

```python
def score_yes_no(answer: str, metric: str) -> float:
    """Score yes/no questions"""
    expected_answer = metric.strip().lower()
    normalized_answer = answer.strip().lower()
    return 1.0 if normalized_answer == expected_answer else 0.0
```

#### 2. ListOne (Single Selection from List)

**Type**: `ListOne`

**Scoring Logic**:

- Weighted scoring based on metric weights
- Synonym enrichment for partial matches
- Handles paraphrasing and variations

```python
def score_list_one(answer: str, metric: str) -> float:
    """Score single-choice list questions with synonym matching"""
    values, weights = enrich_and_parse_metric(metric)
    # Match answer against enriched values
    # Return corresponding weight
```

#### 3. ListMany (Multiple Selection from List)

**Type**: `ListMany`

**Scoring Logic**:

- Aggregates weights for all matched items
- Supports comma-separated or list-formatted answers
- Synonym-enriched matching

```python
def score_list_many(answer: str, metric: str) -> float:
    """Score multi-choice list questions"""
    values, weights = enrich_and_parse_metric(metric)
    # Parse answer into multiple items
    # Sum weights for all matches
```

#### 4. PickOne (Choose One Option)

**Type**: `PickOne`

**Scoring Logic**:

- Similar to ListOne
- Weighted scoring based on metric
- Fuzzy matching support

#### 5. PickMany (Choose Multiple Options)

**Type**: `PickMany`

**Scoring Logic**:

- Similar to ListMany
- Aggregated weighted scoring
- Multiple correct answers supported

#### 6. Yes/No with Rationale

**Type**: `Yes/No with Rationale`

**Scoring Logic**:

- Two-part scoring: binary answer + rationale quality
- Validates both correctness and explanation

```python
def score_yes_no_with_rationale(answer: str, metric: str) -> float:
    """Score yes/no questions with rationale"""
    # Parse answer and rationale
    # Score binary part
    # Assess rationale quality
```

## Advanced Scoring Features

### Synonym Enrichment

The framework enriches metrics with synonyms using WordNet to improve matching:

```python
def enrich_metric_with_synonyms(
    values: list[str],
    weights: list[float]
) -> tuple[list[str], list[float]]:
    """
    Enrich each value with its synonyms from WordNet

    Returns:
        Expanded lists with original values + synonyms
        Weights distributed across synonyms
    """
    enriched_values = []
    enriched_weights = []

    for value, weight in zip(values, weights):
        synonyms = get_synonyms(value)
        enriched_values.extend([value] + synonyms)
        # Distribute weight across synonyms
        enriched_weights.extend([weight] * (len(synonyms) + 1))
```

### Number Handling

Converts between textual and numeric representations:

```python
# "five" → 5
# 5 → "five"
# Handles both conversions for flexible matching
```

### Text Normalization

- Removes diacritics and special characters
- Lowercase normalization
- Whitespace trimming
- Punctuation handling

## Verbatim Checking

### Purpose

Verifies that LLM-generated citations actually exist in source transcripts.

### Implementation

```python
def check_verbatims(context: list, citations: list) -> list[int]:
    """
    Checks if citations are present in the provided context

    Args:
        context: list of documents with 'page_content'
        citations: list with 'source_index' and 'quote'

    Returns:
        List of 1 (found) or 0 (not found) for each citation
    """
    checks = [0] * len(citations)
    for i, citation in enumerate(citations):
        source_index = citation.get('source_index')
        quote = citation.get('quote')
        page_content = context[source_index].get('page_content', '')
        checks[i] = 1 if quote.strip() in page_content else 0

    return checks
```

### Existence Score

Each Q&A pair receives an `existence_score` indicating citation accuracy:

```python
existence_score = sum(checks) / len(checks)  # Percentage of valid citations
```

## RAG (Retrieval-Augmented Generation)

### Overview

The RAG system enhances LLM responses by retrieving relevant documents from a vector database before generation.

### API Integration

```python
def prompt(payload):
    """
    Send prompt to Socioscope evaluation API

    Args:
        payload (dict):
            - k (int): Number of documents to retrieve
            - filter (dict): Metadata filters
            - question (str): Question to answer
            - model (str): LLM model to use
            - auth (str): MongoDB auth string

    Returns:
        dict:
            - question: Original question
            - context: Retrieved documents
            - answer: LLM response with citations
    """
    api_url = "https://socioscope2--evaluation-prompt.modal.run"
    response = requests.post(api_url, json=payload)
    return response.json()
```

### Supported Models

Models follow LangChain `init_chat_model` API:

- **OpenAI**: `openai:gpt-4o-mini`, `openai:gpt-4`
- **Groq**: Various models listed in [GroqCloud docs](https://console.groq.com/docs/models)

### Metadata Filtering

Available metadata fields for filtering:

```python
{
    'PROJECT': 'DK-019',
    'YEAR': 2024,
    'MONTH': 'April',
    'NAME': 'HavHøst',
    'COUNTRY': 'Denmark',
    'GEOGRAPHY': 'Denmark',
    'FILE': 'DK-019_interview_audio.m4a.csv',
    'TYPE': 'interview',
    'RECORD LANG': 'en'
}
```

### Example Usage

```python
import os
auth = os.environ['EVALUATION_API_AUTH']

payload = {
    "k": 10,
    "filter": {'PROJECT': 'FR-068'},
    "question": "Who is Berenice?",
    "model": "openai:gpt-4o-mini",
    "auth": auth
}

response = prompt(payload)

# Verify citations
checks = check_verbatims(
    response['context'],
    response['answer']['citations']
)
```

## Statistical Analysis

### Average Score Calculation

```python
def get_average_score(corpus):
    """
    Calculate average scores for transcripts and verbatims

    Returns:
        tuple: (transcript_average_scores, verbatim_average_scores)
    """
    # Group scores by transcript
    # Calculate averages
    # Return structured results
```

### Visualization

```python
def plot_score(scores):
    """
    Generate bar plots for score visualization

    Uses matplotlib to create visual representations
    """
    # Create bar chart
    # Label axes
    # Display plot
```

## Data Structures

### Corpus

```python
class Corpus:
    def __init__(self):
        self.qa_pairs = []  # List of QuestionAnswerPair objects
```

### QuestionAnswerPair

```python
class QuestionAnswerPair:
    def __init__(self):
        self.question = Question()
        self.answers = []  # List of Answer objects
        self.existence_score = 0.0
```

### Question

```python
class Question:
    def __init__(self):
        self.text = ""
        self.type = ""  # Yes/No, ListOne, etc.
        self.metric = ""  # Expected answer/weights
```

### Answer

```python
class Answer:
    def __init__(self):
        self.answer = ""
        self.score = 0.0
        self.source = ""  # Transcript reference
```

## Dependencies

### Required Python Packages

```txt
spacy==3.8.3          # NLP processing
nltk==3.9.1            # Natural language toolkit
word2number==1.1       # Text-to-number conversion
num2words==0.5.14      # Number-to-text conversion
matplotlib==3.10.1     # Visualization
requests==2.32.3       # API calls
click==8.1.8           # CLI interface
```

### Required Models

```bash
# spaCy model
python -m spacy download en_core_web_sm

# NLTK data
python -m nltk.downloader punkt
python -m nltk.downloader stopwords
python -m nltk.downloader wordnet
```

## Usage Examples

### Basic Evaluation

```bash
python evaluation/main.py \
    evaluation/data_fixture.json \
    evaluation/datasets/corpus.json
```

### Custom Configuration

```python
from processing import process_data, save_results
from scorers import get_scorer

# Load custom data
corpus = process_data('my_questions.json')

# Custom scoring logic
for qa_pair in corpus.qa_pairs:
    # Apply custom transformations
    # Score with custom metrics
    pass

# Save results
save_results(corpus, 'my_results.json')
```

## Best Practices

### Metric Definition

- Use JSON format for metrics
- Include descriptive values
- Assign appropriate weights
- Consider synonym variations

### Answer Formatting

- Normalize text before comparison
- Handle edge cases (empty, null)
- Support multiple formats
- Validate before scoring

### Verbatim Checking

- Exact string matching for quotes
- Verify source indices are valid
- Handle missing context gracefully
- Report citation accuracy

## Performance Considerations

### Optimization Tips

- Cache synonym lookups
- Batch API requests
- Parallelize scoring when possible
- Use efficient data structures

### Scaling

- Process large corpora in chunks
- Implement progress tracking
- Use database for large datasets
- Monitor memory usage

## Related Documentation

- [Research Overview](/research/overview)
- [Data Processing](/research/processing)
- [Getting Started](/getting-started)
- [Glossary](/glossary)
