# Sleipnir - Creating a Module

This guide provides step-by-step instructions for creating custom modules in Sleipnir.

## Overview

Sleipnir is built on a modular architecture that allows you to extend its functionality by creating custom modules. Modules encapsulate specific functionality and can be reused across different jobs and prompting stacks.

## Module Structure

A Sleipnir module consists of the following components:

```
my_module/
├── __init__.py          # Module initialization
├── module.py            # Core module logic
├── config.yaml          # Module configuration
├── prompts/             # Module-specific prompts
│   └── prompt_template.txt
├── tests/               # Module tests
│   └── test_module.py
└── README.md            # Module documentation
```

## Creating Your First Module

### Step 1: Set Up Module Directory

```bash
mkdir -p modules/my_module/{prompts,tests}
cd modules/my_module
```

### Step 2: Define Module Configuration

Create `config.yaml`:

```yaml
name: my_module
version: 1.0.0
description: Brief description of module functionality
author: Your Name
dependencies:
  - required_module_1
  - required_module_2
parameters:
  param1:
    type: string
    required: true
    description: Description of parameter
  param2:
    type: integer
    default: 10
    description: Description with default value
```

### Step 3: Implement Module Logic

Create `module.py`:

```python
from sleipnir.core import BaseModule

class MyModule(BaseModule):
    """
    Module description and purpose
    """
    
    def __init__(self, config):
        super().__init__(config)
        self.param1 = config.get('param1')
        self.param2 = config.get('param2', 10)
    
    def execute(self, input_data):
        """
        Main execution method
        
        Args:
            input_data: Input data for processing
            
        Returns:
            Processed results
        """
        # Your module logic here
        result = self.process(input_data)
        return result
    
    def process(self, data):
        """
        Core processing logic
        """
        # Implementation details
        pass
    
    def validate(self):
        """
        Validate module configuration
        """
        if not self.param1:
            raise ValueError("param1 is required")
        return True
```

### Step 4: Create Module Initialization

Create `__init__.py`:

```python
from .module import MyModule

__version__ = '1.0.0'
__all__ = ['MyModule']
```

### Step 5: Add Module Documentation

Create `README.md` with:
- Purpose and use cases
- Configuration parameters
- Input/output specifications
- Usage examples
- Known limitations

### Step 6: Write Tests

Create `tests/test_module.py`:

```python
import unittest
from my_module import MyModule

class TestMyModule(unittest.TestCase):
    
    def setUp(self):
        self.config = {
            'param1': 'test_value',
            'param2': 20
        }
        self.module = MyModule(self.config)
    
    def test_initialization(self):
        self.assertEqual(self.module.param1, 'test_value')
        self.assertEqual(self.module.param2, 20)
    
    def test_execute(self):
        input_data = "test input"
        result = self.module.execute(input_data)
        self.assertIsNotNone(result)
    
    def test_validation(self):
        self.assertTrue(self.module.validate())

if __name__ == '__main__':
    unittest.main()
```

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

- Raise descriptive exceptions
- Log errors with context
- Implement graceful degradation where appropriate
- Provide clear error messages

### Performance

- Optimize for common use cases
- Implement caching where beneficial
- Support batch processing
- Monitor resource usage

## Integration with Sleipnir

### Registering Your Module

Add your module to the Sleipnir registry:

```python
from sleipnir.registry import register_module
from my_module import MyModule

register_module('my_module', MyModule)
```

### Using Your Module in Jobs

Reference your module in job definitions:

```yaml
job:
  name: my_job
  modules:
    - name: my_module
      params:
        param1: "value"
        param2: 15
```

## Advanced Topics

### Async Modules

For I/O-bound operations, implement async modules:

```python
class MyAsyncModule(BaseModule):
    async def execute(self, input_data):
        result = await self.async_process(input_data)
        return result
```

### Stateful Modules

For modules that maintain state across executions:

```python
class StatefulModule(BaseModule):
    def __init__(self, config):
        super().__init__(config)
        self.state = {}
    
    def execute(self, input_data):
        # Use and update self.state
        pass
```

### Module Composition

Combine multiple modules for complex workflows:

```python
class CompositeModule(BaseModule):
    def __init__(self, config):
        super().__init__(config)
        self.module1 = Module1(config['module1'])
        self.module2 = Module2(config['module2'])
    
    def execute(self, input_data):
        intermediate = self.module1.execute(input_data)
        result = self.module2.execute(intermediate)
        return result
```

## Testing Your Module

Run module tests:

```bash
python -m pytest modules/my_module/tests/
```

Integration testing:

```bash
sleipnir test-module my_module --integration
```

## Publishing Your Module

1. Ensure all tests pass
2. Update version number
3. Document changes in changelog
4. Submit module for review
5. Add to module repository

## Related Documentation

- [Sleipnir Versioning System](Sleipnir-Versioning-System.md)
- [Sleipnir Prompting Stack](Sleipnir-Prompting-Stack.md)
- [Sleipnir Jobs](Sleipnir-Jobs.md)
- [Glossary](Glossary.md)
