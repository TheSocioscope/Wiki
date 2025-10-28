---
layout: default
title: Sleipnir - Prompting Stack
permalink: /sleipnir/prompting-stack
---

# Sleipnir - Prompting Stack

This document explains what a prompting stack is and how to use it effectively in Sleipnir.

## Overview

A **prompting stack** is a structured sequence of prompts that work together to accomplish a complex task. Each prompt in the stack builds upon the outputs of previous prompts, creating a pipeline of transformations and analyses.

## Concept

Think of a prompting stack as a layered approach to problem-solving:

```
Input Data
    ↓
[Prompt Layer 1: Preprocessing]
    ↓
[Prompt Layer 2: Analysis]
    ↓
[Prompt Layer 3: Refinement]
    ↓
Final Output
```

Each layer can modify, enrich, or transform the data flowing through the stack.

## Stack Architecture

### Basic Structure

A prompting stack consists of:

1. **Input Layer** - Receives and validates initial data
2. **Processing Layers** - Transform and analyze data through multiple prompts
3. **Output Layer** - Formats and validates final results

### Stack Definition

Define a stack in YAML:

```yaml
stack:
  name: sentiment_analysis_stack
  version: 1.0.0
  description: Multi-stage sentiment analysis with validation

  layers:
    - name: preprocessing
      prompt: text_cleaner
      version: 1.0.0
      config:
        remove_urls: true
        normalize_whitespace: true

    - name: sentiment_extraction
      prompt: sentiment_analyzer
      version: 2.1.0
      config:
        granularity: sentence
        include_confidence: true

    - name: validation
      prompt: sentiment_validator
      version: 1.0.0
      config:
        threshold: 0.7

    - name: aggregation
      prompt: sentiment_aggregator
      version: 1.0.0
      config:
        method: weighted_average
```

## Stack Types

### Sequential Stacks

Process data linearly through each layer:

```yaml
type: sequential
flow: layer1 → layer2 → layer3 → output
```

**Use cases:**

- Text preprocessing → analysis → formatting
- Translation → summarization → validation
- Extraction → classification → aggregation

### Parallel Stacks

Process data through multiple independent paths:

```yaml
type: parallel
flow: input → [layer1a, layer1b, layer1c] → merge → output
```

**Use cases:**

- Multi-perspective analysis
- Ensemble predictions
- Redundancy and validation

### Conditional Stacks

Route data based on conditions:

```yaml
type: conditional
flow:
  input → layer1 → [if condition: layer2a, else: layer2b] → output
```

**Use cases:**

- Content-type specific processing
- Quality-based routing
- Error handling paths

### Hybrid Stacks

Combine multiple stack types:

```yaml
type: hybrid
flow: input → sequential_layers → parallel_layers → conditional_layer → output
```

## Creating a Prompting Stack

### Step 1: Define Stack Purpose

Clearly articulate what the stack should accomplish:

- Input data type and format
- Desired output format
- Intermediate transformations needed

### Step 2: Design Layer Sequence

Break down the task into logical steps:

1. Data validation and cleaning
2. Feature extraction
3. Primary analysis
4. Secondary enrichment
5. Quality validation
6. Output formatting

### Step 3: Select Prompts for Each Layer

Choose appropriate prompts for each layer:

```yaml
layers:
  - name: validation
    prompt: data_validator
    purpose: Ensure input data quality

  - name: extraction
    prompt: entity_extractor
    purpose: Extract key entities and concepts

  - name: analysis
    prompt: relationship_analyzer
    purpose: Analyze relationships between entities
```

### Step 4: Configure Data Flow

Specify how data flows between layers:

```yaml
data_flow:
  - from: input
    to: validation

  - from: validation
    to: extraction
    transform: filter_valid_only

  - from: extraction
    to: analysis
    transform: format_as_json
```

### Step 5: Add Error Handling

Define error handling strategy:

```yaml
error_handling:
  on_layer_failure:
    strategy: retry # or skip, fail, fallback
    max_retries: 3
    fallback_layer: alternative_analysis

  on_validation_failure:
    strategy: skip
    log: true
```

## Stack Execution

### Executing a Stack

Run a stack via Sleipnir:

```python
from sleipnir import StackExecutor

executor = StackExecutor('sentiment_analysis_stack')
result = executor.execute(input_data)
```

Via CLI:

```bash
sleipnir execute-stack sentiment_analysis_stack --input data.json
```

### Monitoring Execution

Track execution progress:

```python
executor = StackExecutor('my_stack')
executor.on('layer_complete', lambda layer, result: print(f"{layer} done"))
executor.on('stack_complete', lambda result: print("Stack finished"))
result = executor.execute(input_data)
```

## Stack Optimization

### Performance Optimization

- **Caching** - Cache layer outputs for repeated inputs
- **Parallelization** - Run independent layers in parallel
- **Batching** - Process multiple inputs together
- **Lazy Evaluation** - Only execute layers when needed

### Cost Optimization

- **Early Filtering** - Filter out invalid data early
- **Prompt Selection** - Use appropriate model sizes per layer
- **Result Reuse** - Share results across similar requests
- **Smart Routing** - Route simple cases to simpler prompts

## Stack Patterns

### Pattern: Extract-Transform-Load (ETL)

```yaml
layers:
  - name: extract
    prompt: data_extractor
  - name: transform
    prompt: data_transformer
  - name: load
    prompt: data_formatter
```

### Pattern: Validate-Process-Verify

```yaml
layers:
  - name: validate_input
    prompt: input_validator
  - name: process
    prompt: main_processor
  - name: verify_output
    prompt: output_verifier
```

### Pattern: Multi-Pass Refinement

```yaml
layers:
  - name: draft
    prompt: draft_generator
  - name: refine_1
    prompt: refiner
    config: { focus: clarity }
  - name: refine_2
    prompt: refiner
    config: { focus: accuracy }
  - name: finalize
    prompt: finalizer
```

### Pattern: Ensemble Analysis

```yaml
type: parallel
layers:
  - name: analyzer_1
    prompt: method_a_analyzer
  - name: analyzer_2
    prompt: method_b_analyzer
  - name: analyzer_3
    prompt: method_c_analyzer
  - name: ensemble
    prompt: ensemble_aggregator
    inputs: [analyzer_1, analyzer_2, analyzer_3]
```

## Stack Testing

### Unit Testing Layers

Test each layer independently:

```python
def test_preprocessing_layer():
    layer = stack.get_layer('preprocessing')
    output = layer.execute(test_input)
    assert output.is_valid()
```

### Integration Testing

Test complete stack execution:

```python
def test_full_stack():
    result = executor.execute(test_input)
    assert result.meets_requirements()
```

### Performance Testing

Measure stack performance:

```bash
sleipnir benchmark-stack my_stack --iterations 100
```

## Best Practices

### Design Principles

- **Modularity** - Each layer has a single, clear purpose
- **Composability** - Layers can be reused in different stacks
- **Transparency** - Track data transformations at each layer
- **Resilience** - Handle failures gracefully

### Configuration

- Version control stack definitions
- Document layer dependencies
- Provide example inputs/outputs
- Include performance benchmarks

### Maintenance

- Monitor layer performance metrics
- Update prompts independently
- Test after any layer changes
- Maintain backward compatibility

## Advanced Topics

### Dynamic Stacks

Build stacks programmatically:

```python
from sleipnir import StackBuilder

builder = StackBuilder()
builder.add_layer('preprocessing', text_cleaner)

if complex_analysis_needed:
    builder.add_layer('deep_analysis', advanced_analyzer)
else:
    builder.add_layer('simple_analysis', basic_analyzer)

stack = builder.build()
```

### Recursive Stacks

Stacks that call themselves:

```yaml
layers:
  - name: process
    prompt: recursive_processor
    config:
      max_depth: 5
      recurse_on: needs_more_processing
```

### Streaming Stacks

Process data streams through stacks:

```python
async def process_stream(input_stream):
    async for item in input_stream:
        result = await stack.execute_async(item)
        yield result
```

## Related Documentation

- [Creating a Module]({{ site.baseurl }}/sleipnir/creating-a-module)
- [Versioning System]({{ site.baseurl }}/sleipnir/versioning-system)
- [Jobs]({{ site.baseurl }}/sleipnir/jobs)
- [Glossary]({{ site.baseurl }}/glossary)
