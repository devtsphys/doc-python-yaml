# Python YAML Complete Reference Card

## Installation

```bash
pip install PyYAML
```

## Basic Import

```python
import yaml
```

## Core Functions Overview

### Loading Functions

|Function              |Description                        |Returns             |Safety                                    |
|----------------------|-----------------------------------|--------------------|------------------------------------------|
|`yaml.load()`         |Load YAML from string/file         |Python object       |**Unsafe** - can execute arbitrary code   |
|`yaml.safe_load()`    |Load YAML safely                   |Python object       |**Safe** - recommended for untrusted input|
|`yaml.load_all()`     |Load multiple YAML documents       |Generator of objects|**Unsafe**                                |
|`yaml.safe_load_all()`|Load multiple YAML documents safely|Generator of objects|**Safe**                                  |
|`yaml.full_load()`    |Load with full Python tags         |Python object       |**Unsafe**                                |

### Dumping Functions

|Function              |Description                         |Input              |Output     |
|----------------------|------------------------------------|-------------------|-----------|
|`yaml.dump()`         |Convert Python object to YAML string|Python object      |YAML string|
|`yaml.dump_all()`     |Convert multiple objects to YAML    |Sequence of objects|YAML string|
|`yaml.safe_dump()`    |Safely convert to YAML              |Python object      |YAML string|
|`yaml.safe_dump_all()`|Safely convert multiple objects     |Sequence of objects|YAML string|

## Basic Examples

### Loading YAML

```python
import yaml

# From string
yaml_string = """
name: John Doe
age: 30
skills:
  - Python
  - YAML
  - Docker
"""

# Safe loading (recommended)
data = yaml.safe_load(yaml_string)
print(data)
# Output: {'name': 'John Doe', 'age': 30, 'skills': ['Python', 'YAML', 'Docker']}

# From file
with open('config.yaml', 'r') as file:
    config = yaml.safe_load(file)
```

### Dumping YAML

```python
data = {
    'name': 'Jane Smith',
    'age': 25,
    'projects': ['web-app', 'api-service'],
    'active': True
}

# Convert to YAML string
yaml_output = yaml.dump(data)
print(yaml_output)

# Write to file
with open('output.yaml', 'w') as file:
    yaml.dump(data, file)
```

## Advanced Parameters

### Common dump() Parameters

|Parameter           |Type     |Default|Description                     |
|--------------------|---------|-------|--------------------------------|
|`default_flow_style`|bool/None|None   |Use flow style (inline) format  |
|`indent`            |int      |2      |Number of spaces for indentation|
|`width`             |int      |80     |Maximum line width              |
|`allow_unicode`     |bool     |False  |Allow unicode characters        |
|`sort_keys`         |bool     |False  |Sort dictionary keys            |
|`explicit_start`    |bool     |False  |Add explicit document start (—) |
|`explicit_end`      |bool     |False  |Add explicit document end (…)   |

### Example with Parameters

```python
data = {'z_key': 1, 'a_key': 2, 'unicode': 'café'}

yaml_formatted = yaml.dump(
    data,
    default_flow_style=False,
    indent=4,
    width=120,
    allow_unicode=True,
    sort_keys=True,
    explicit_start=True
)
print(yaml_formatted)
# Output:
# ---
# a_key: 2
# unicode: café
# z_key: 1
```

## YAML Data Types and Python Equivalents

|YAML Type|Python Type       |Example YAML      |Python Result       |
|---------|------------------|------------------|--------------------|
|Scalar   |str/int/float/bool|`name: John`      |`"John"`            |
|Sequence |list              |`- item1\n- item2`|`["item1", "item2"]`|
|Mapping  |dict              |`key: value`      |`{"key": "value"}`  |
|Boolean  |bool              |`active: true`    |`True`              |
|Integer  |int               |`count: 42`       |`42`                |
|Float    |float             |`price: 19.99`    |`19.99`             |
|Null     |NoneType          |`value: null`     |`None`              |
|Multiline|str               |`text: |`         |String with newlines|

## YAML Syntax Examples

### Scalars

```yaml
# Strings
name: John Doe
quoted_name: "Jane Doe"
multiline: |
  This is a multiline
  string that preserves
  line breaks

folded: >
  This is a folded
  string that becomes
  a single line

# Numbers
integer: 42
float: 3.14159
scientific: 1.2e-3

# Booleans
active: true
disabled: false
null_value: null
```

### Sequences (Lists)

```yaml
# Block style
fruits:
  - apple
  - banana
  - orange

# Flow style
colors: [red, green, blue]

# Nested sequences
matrix:
  - [1, 2, 3]
  - [4, 5, 6]
```

### Mappings (Dictionaries)

```yaml
# Block style
person:
  name: John
  age: 30
  address:
    street: 123 Main St
    city: Anytown

# Flow style
coordinates: {x: 10, y: 20}
```

## Working with Multiple Documents

```python
# Multiple documents in one YAML file
yaml_content = """
---
name: Document 1
type: config
---
name: Document 2
type: data
settings:
  debug: true
"""

# Load all documents
documents = list(yaml.safe_load_all(yaml_content))
print(f"Loaded {len(documents)} documents")

# Dump multiple documents
docs = [
    {'name': 'Config', 'version': 1},
    {'name': 'Data', 'version': 2}
]

yaml_output = yaml.dump_all(docs, explicit_start=True)
print(yaml_output)
```

## Custom Classes and Tags

### Using Custom Constructors

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def __repr__(self):
        return f"Person(name='{self.name}', age={self.age})"

def person_constructor(loader, node):
    values = loader.construct_mapping(node)
    return Person(values['name'], values['age'])

def person_representer(dumper, person):
    return dumper.represent_mapping('!Person', {
        'name': person.name,
        'age': person.age
    })

# Register custom constructor and representer
yaml.add_constructor('!Person', person_constructor)
yaml.add_representer(Person, person_representer)

# Usage
yaml_with_person = """
employee: !Person
  name: John Doe
  age: 30
"""

data = yaml.load(yaml_with_person, Loader=yaml.FullLoader)
print(data['employee'])  # Person(name='John Doe', age=30)
```

## Error Handling

```python
import yaml

def safe_load_yaml(yaml_string):
    try:
        return yaml.safe_load(yaml_string)
    except yaml.YAMLError as e:
        print(f"YAML Error: {e}")
        return None
    except FileNotFoundError as e:
        print(f"File not found: {e}")
        return None

# Example with invalid YAML
invalid_yaml = """
name: John
  age: 30  # Invalid indentation
"""

result = safe_load_yaml(invalid_yaml)
```

## Common Patterns and Techniques

### Configuration Management

```python
class ConfigManager:
    def __init__(self, config_file):
        self.config_file = config_file
        self.config = self.load_config()
    
    def load_config(self):
        try:
            with open(self.config_file, 'r') as file:
                return yaml.safe_load(file)
        except FileNotFoundError:
            return self.get_default_config()
    
    def save_config(self):
        with open(self.config_file, 'w') as file:
            yaml.dump(self.config, file, default_flow_style=False)
    
    def get_default_config(self):
        return {
            'database': {'host': 'localhost', 'port': 5432},
            'debug': False,
            'logging': {'level': 'INFO'}
        }
    
    def get(self, key, default=None):
        keys = key.split('.')
        value = self.config
        for k in keys:
            value = value.get(k, default)
            if value is None:
                return default
        return value

# Usage
config = ConfigManager('app_config.yaml')
db_host = config.get('database.host')
```

### Environment Variable Substitution

```python
import os
import re

def resolve_env_vars(yaml_content):
    """Replace ${VAR_NAME} with environment variables"""
    pattern = re.compile(r'\$\{([^}]+)\}')
    
    def replacer(match):
        env_var = match.group(1)
        return os.environ.get(env_var, match.group(0))
    
    return pattern.sub(replacer, yaml_content)

# Example
yaml_with_env = """
database:
  host: ${DB_HOST}
  port: ${DB_PORT}
  username: ${DB_USER}
"""

# Set environment variables
os.environ['DB_HOST'] = 'localhost'
os.environ['DB_PORT'] = '5432'
os.environ['DB_USER'] = 'admin'

resolved_yaml = resolve_env_vars(yaml_with_env)
config = yaml.safe_load(resolved_yaml)
```

### Schema Validation

```python
def validate_config_schema(config):
    """Basic schema validation example"""
    required_keys = ['name', 'version', 'settings']
    
    for key in required_keys:
        if key not in config:
            raise ValueError(f"Missing required key: {key}")
    
    if not isinstance(config['version'], (int, float)):
        raise ValueError("Version must be a number")
    
    return True

# Usage
try:
    config = yaml.safe_load(yaml_content)
    validate_config_schema(config)
    print("Configuration is valid")
except (yaml.YAMLError, ValueError) as e:
    print(f"Configuration error: {e}")
```

## Performance Tips

1. **Use `safe_load()` instead of `load()`** for untrusted input
1. **Stream large files** using `load_all()` for multiple documents
1. **Cache parsed YAML** if loading the same file repeatedly
1. **Use `CLoader` and `CDumper`** for better performance:

```python
try:
    from yaml import CLoader as Loader, CDumper as Dumper
except ImportError:
    from yaml import Loader, Dumper

# Faster loading/dumping
data = yaml.load(yaml_string, Loader=Loader)
yaml_output = yaml.dump(data, Dumper=Dumper)
```

## Security Considerations

- **Always use `safe_load()`** for untrusted input
- **Avoid `load()`** unless you trust the source completely
- **Validate loaded data** against expected schema
- **Sanitize file paths** when loading configuration files
- **Use allowlists** for acceptable values in configuration

## Troubleshooting Common Issues

|Issue                         |Cause                     |Solution                                     |
|------------------------------|--------------------------|---------------------------------------------|
|`YAMLLoadWarning`             |Using unsafe load         |Use `safe_load()` or specify Loader          |
|Unicode errors                |Non-ASCII characters      |Set `allow_unicode=True`                     |
|Indentation errors            |Inconsistent spacing      |Use consistent spaces (not tabs)             |
|Type conversion issues        |Unexpected type conversion|Quote strings that look like numbers/booleans|
|Memory issues with large files|Loading entire file       |Use streaming with `load_all()`              |

This reference card covers the essential aspects of working with YAML in Python using the PyYAML library, from basic operations to advanced techniques and best practices.