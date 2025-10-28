# Sleipnir - Jobs

This document explains what a job is in Sleipnir and how to define and execute sets of prompts.

## Overview

A **job** in Sleipnir is a defined unit of work that executes a set of prompts to accomplish a specific task. Jobs orchestrate prompt execution, manage data flow, and handle results aggregation.

## What is a Job?

A job encapsulates:

- **Prompts** - One or more prompts to execute
- **Configuration** - Parameters and settings for execution
- **Data Sources** - Input data specifications
- **Output Handling** - How to process and store results
- **Error Handling** - What to do when things go wrong
- **Scheduling** - When and how often to run

## Job Definition

### Basic Job Structure

```yaml
job:
  name: sentiment_analysis_job
  version: 1.0.0
  description: Analyze sentiment of customer reviews
  
  # Input configuration
  input:
    source: database
    query: "SELECT review_text, review_id FROM reviews WHERE processed = false"
    batch_size: 100
  
  # Prompts to execute
  prompts:
    - name: sentiment_analyzer
      version: 2.1.0
      model: gpt-4
      temperature: 0.7
      max_tokens: 500
      parameters:
        include_reasoning: true
        confidence_threshold: 0.8
  
  # Output configuration
  output:
    destination: database
    table: sentiment_results
    format: json
    on_complete: notify
  
  # Execution settings
  execution:
    concurrency: 10
    retry_on_failure: 3
    timeout: 300
```

## Job Types

### Single-Prompt Jobs

Execute one prompt across input data:

```yaml
job:
  name: simple_classification
  prompts:
    - name: classifier
      version: 1.0.0
```

**Use cases:**
- Single-step classification
- Simple extractions
- Direct transformations

### Multi-Prompt Jobs

Execute multiple prompts in sequence:

```yaml
job:
  name: multi_stage_analysis
  prompts:
    - name: preprocessor
      version: 1.0.0
    - name: analyzer
      version: 2.0.0
    - name: validator
      version: 1.0.0
```

**Use cases:**
- Complex analysis pipelines
- Multi-stage transformations
- Validation workflows

### Stack-Based Jobs

Use prompting stacks for complex workflows:

```yaml
job:
  name: advanced_analysis
  stack: sentiment_analysis_stack
  stack_version: 1.0.0
```

**Use cases:**
- Reusable complex workflows
- Conditional processing
- Parallel processing paths

### Scheduled Jobs

Jobs that run on a schedule:

```yaml
job:
  name: daily_report
  schedule:
    cron: "0 9 * * *"  # Daily at 9 AM
    timezone: UTC
  prompts:
    - name: report_generator
      version: 1.0.0
```

**Use cases:**
- Periodic analysis
- Regular reports
- Data pipeline jobs

## Creating a Job

### Step 1: Define Job Scope

Clarify the job's purpose:
- What task should it accomplish?
- What data does it process?
- What output is expected?
- What are success criteria?

### Step 2: Select Prompts

Choose appropriate prompts:

```yaml
prompts:
  - name: text_cleaner
    version: 1.0.0
    purpose: Clean and normalize input text
    
  - name: sentiment_analyzer
    version: 2.1.0
    purpose: Extract sentiment and confidence scores
```

### Step 3: Configure Input

Specify data sources:

```yaml
input:
  # From database
  source: postgresql
  connection: prod_db
  query: "SELECT * FROM reviews"
  
  # Or from file
  source: file
  path: /data/reviews.json
  format: json
  
  # Or from API
  source: api
  endpoint: https://api.example.com/reviews
  auth: bearer_token
```

### Step 4: Configure Output

Define output handling:

```yaml
output:
  # To database
  destination: database
  table: results
  
  # Or to file
  destination: file
  path: /output/results.json
  
  # Or to API
  destination: api
  endpoint: https://api.example.com/results
  method: POST
```

### Step 5: Add Error Handling

Define error strategies:

```yaml
error_handling:
  on_prompt_failure:
    strategy: retry
    max_retries: 3
    backoff: exponential
    
  on_input_error:
    strategy: skip
    log: true
    
  on_output_error:
    strategy: fail
    alert: true
```

## Job Execution

### Running Jobs

Execute via Python API:

```python
from sleipnir import JobExecutor

executor = JobExecutor('sentiment_analysis_job')
results = executor.run()
```

Execute via CLI:

```bash
sleipnir run-job sentiment_analysis_job
```

Execute with custom input:

```bash
sleipnir run-job sentiment_analysis_job --input custom_data.json
```

### Monitoring Execution

Track job progress:

```python
executor = JobExecutor('my_job')

# Register callbacks
executor.on('start', lambda: print("Job started"))
executor.on('progress', lambda p: print(f"Progress: {p}%"))
executor.on('complete', lambda r: print(f"Completed: {r}"))
executor.on('error', lambda e: print(f"Error: {e}"))

results = executor.run()
```

### Async Execution

Run jobs asynchronously:

```python
import asyncio
from sleipnir import AsyncJobExecutor

async def run_job():
    executor = AsyncJobExecutor('my_job')
    results = await executor.run_async()
    return results

results = asyncio.run(run_job())
```

## Job Configuration

### Environment Variables

Use environment variables for sensitive data:

```yaml
job:
  name: api_analysis
  prompts:
    - name: analyzer
      api_key: ${OPENAI_API_KEY}
      endpoint: ${API_ENDPOINT}
```

### Configuration Profiles

Define different configurations:

```yaml
job:
  name: analysis_job
  
  profiles:
    development:
      model: gpt-3.5-turbo
      batch_size: 10
      
    production:
      model: gpt-4
      batch_size: 100
      
    cost_optimized:
      model: gpt-3.5-turbo
      batch_size: 500
```

Use profile:

```bash
sleipnir run-job analysis_job --profile production
```

### Parameter Overrides

Override parameters at runtime:

```bash
sleipnir run-job my_job --param temperature=0.5 --param max_tokens=1000
```

## Job Patterns

### Pattern: Batch Processing

Process large datasets in batches:

```yaml
job:
  name: batch_processor
  input:
    batch_size: 100
    batch_strategy: parallel
  execution:
    concurrency: 10
```

### Pattern: Map-Reduce

Distribute processing and aggregate:

```yaml
job:
  name: map_reduce_analysis
  phases:
    - name: map
      prompt: item_processor
      parallel: true
    - name: reduce
      prompt: aggregator
      parallel: false
```

### Pattern: Pipeline

Chain multiple jobs:

```yaml
pipeline:
  name: full_analysis_pipeline
  jobs:
    - collect_data_job
    - preprocess_job
    - analyze_job
    - report_job
```

### Pattern: Fan-Out/Fan-In

Split work, process, then merge:

```yaml
job:
  name: fan_out_analysis
  phases:
    - name: fan_out
      split_by: category
    - name: process
      prompt: category_analyzer
      parallel: true
    - name: fan_in
      prompt: result_merger
```

## Job Dependencies

### Sequential Dependencies

```yaml
pipeline:
  jobs:
    - id: job1
      name: data_collector
      
    - id: job2
      name: data_processor
      depends_on: [job1]
      
    - id: job3
      name: data_analyzer
      depends_on: [job2]
```

### Parallel Dependencies

```yaml
pipeline:
  jobs:
    - id: job1
      name: collector
      
    - id: job2a
      name: processor_a
      depends_on: [job1]
      
    - id: job2b
      name: processor_b
      depends_on: [job1]
      
    - id: job3
      name: aggregator
      depends_on: [job2a, job2b]
```

## Job Scheduling

### Cron-Based Scheduling

```yaml
job:
  name: daily_analysis
  schedule:
    cron: "0 2 * * *"     # Daily at 2 AM
    timezone: UTC
    enabled: true
```

### Event-Based Triggers

```yaml
job:
  name: new_data_processor
  triggers:
    - type: file_created
      path: /data/input/*.json
    - type: webhook
      endpoint: /trigger/process
```

### Conditional Execution

```yaml
job:
  name: conditional_job
  conditions:
    - type: data_available
      check: "SELECT COUNT(*) FROM new_data"
      threshold: 100
    - type: time_window
      start: "09:00"
      end: "17:00"
```

## Job Monitoring

### Metrics Collection

```yaml
job:
  name: monitored_job
  monitoring:
    metrics:
      - execution_time
      - token_usage
      - success_rate
      - error_rate
    export_to: prometheus
```

### Logging

```yaml
job:
  name: logged_job
  logging:
    level: INFO
    format: json
    destination: file
    path: /logs/job.log
    rotate: daily
```

### Alerts

```yaml
job:
  name: alerted_job
  alerts:
    - condition: error_rate > 0.05
      action: email
      recipients: [admin@example.com]
    - condition: execution_time > 600
      action: slack
      channel: "#ops"
```

## Best Practices

### Job Design

- **Single Responsibility** - Each job should do one thing well
- **Idempotency** - Jobs should be safely re-runnable
- **Resilience** - Handle failures gracefully
- **Observability** - Log and monitor job execution

### Configuration Management

- Version control job definitions
- Use configuration profiles for different environments
- Externalize sensitive data
- Document all parameters

### Resource Management

- Set appropriate timeouts
- Limit concurrency based on resources
- Implement rate limiting for APIs
- Monitor token usage and costs

### Testing

- Test with sample data before production
- Validate output formats
- Test error handling paths
- Measure performance under load

## Job Templates

Create reusable job templates:

```yaml
template:
  name: analysis_template
  parameters:
    - name: input_table
      type: string
      required: true
    - name: model_name
      type: string
      default: gpt-3.5-turbo
  
  job:
    name: ${job_name}
    input:
      source: database
      query: "SELECT * FROM ${input_table}"
    prompts:
      - name: analyzer
        model: ${model_name}
```

Use template:

```bash
sleipnir create-job-from-template analysis_template \
  --param input_table=reviews \
  --param model_name=gpt-4 \
  --name review_analysis
```

## Related Documentation

- [Sleipnir Creating a Module](Sleipnir-Creating-a-Module.md)
- [Sleipnir Versioning System](Sleipnir-Versioning-System.md)
- [Sleipnir Prompting Stack](Sleipnir-Prompting-Stack.md)
- [Glossary](Glossary.md)
