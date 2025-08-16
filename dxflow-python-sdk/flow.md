# Flow

## Overview

A **Flow** in dxflow represents one or more software tools or containers that are configured to work together to accomplish a specific scientific computing task. Flows can range from simple single-container scripts to complex multi-container applications with multiple services and dependencies. We use [docker-compose](https://docs.docker.com/compose/) style definitions for flows, enabling easy management and deployment of containerized scientific workflows.

## Key Concepts

- **Flow Definition**: A template that describes containers, their configurations, and relationships
- **Flow Registry**: A collection of flows available in your namespace
- **Variables**: Configurable parameters that customize flow behavior
- **Profiles**: Different execution configurations for the same flow
- **Projects**: Instances of flows running on compute units

## `FlowRegisteryManager` Class

The `FlowRegisteryManager` manages all flows in your namespace, providing methods to publish, retrieve, update, and manage flow packages.

### Usage

```python
from dxflow import Session

session = Session(email="your@email.com", password="your_password")
flow_register = session.get_flow_registery_manager()
flow_register.list(filters={"tags": "Molecular Dynamics"})
```
```markdown
Name    | Pointer                        | Tags                           | Status    | Verified
--------+--------------------------------+--------------------------------+-----------+---------
Boltz-1 | 7ea2b574-e962-4f18-bb54-7e233c | Molecular Dynamics, Biomolecul | PUBLISHED | True    
```

#### Methods:

- *`publish(name, description=None, properties=None, tags=None, arch="AMD64", color=None, icon=None, logo=None, image=None, logo_url=None, image_url=None)`*: Publishes a new flow package.
- *`update_flow_package(flow_pointer, update_data)`*: Updates an existing flow package.
- *`update_template(flow_pointer, template_data)`*: Updates the template of a flow package.
- *`get(flow_pointer)`*: Retrieves a flow package.
- *`get_by_flow_name(name)`*: Retrieves a flow package by name.
- *`get_by_name(name, update_list=True)`*: Retrieves a flow package by name within the current instance.
- *`get_list(filters=None)`*: Retrieves a list of flow packages.
- *`list(filters={"tags": "AMD64", "verified": True}, table=True, return_info=False)`*: Lists flow packages with optional filters and table display.
- *`available_flows(name)`*: Lists available flows in a namespace.
- *`activate(flow_pointer)`*: Activates a flow package.
- *`deactivate(flow_pointer)`*: Deactivates a flow package.
- *`get_flow_pointer(name=None, update_list=True)`*: Retrieves the pointer of a flow package by name.

### Examples

#### Publishing a New Flow

```python
# Define a flow template (docker-compose style)
template = """
version: '3.8'
services:
  analyzer:
    image: python:3.9
    command: python analyze.py
    environment:
      - THREADS=${THREADS:-4}
      - INPUT_FILE=${INPUT_FILE}
    volumes:
      - ./data:/data
"""

# Publish the flow
flow_register.publish(
    name="DataAnalyzer",
    description="Analyzes scientific data using Python",
    properties={"template": template},
    tags=["Data Analysis", "Python"],
    arch="AMD64"
)
```

#### Filtering and Finding Flows

```python
# Find flows by tags
molecular_flows = flow_register.list(
    filters={"tags": "Molecular Dynamics", "verified": True}
)

# Get a specific flow by name
flow = flow_register.get_by_name("Boltz-1")

# Get flow pointer for creating projects
flow_pointer = flow_register.get_flow_pointer(name="GROMACS")
```


## `Flow` Class

The `Flow` class represents an individual flow package with its template, variables, and metadata.
#### Methods:

- *`__init__(name, template, pointer=None, user={}, description=None, properties={}, tags=[], environments=[], visibility="PRIVATE", status=None, created_at=None, updated_at=None, state=None, verified=None)`*: Initializes a Flow object with the provided attributes. Raises `ValueError` if required fields are missing or invalid values are provided for `visibility` or `status`.

- *`generate_compose(variables: dict=None, project_name: str=None) -> yaml`*: Generates a Docker Compose YAML file based on the flow's template and provided variables. Filters services based on profiles and validates variables. Returns the updated YAML configuration.

- *`create_project(project_name=None, variables={}, compute_unit=None, **kwargs)`*: Creates a project using the flow's template and the provided compute unit. Raises `ValueError` if no template is available.

- *`get_variables()`*: Retrieves the variables defined in the flow's template.

- *`display_variables(table=True)`*: Displays the variables in the flow's template along with their descriptions, hints, default values, and options. Supports table or JSON format.

- *`__str__()`*: Returns a JSON string representation of the Flow object, including all its attributes.

### Flow Examples

#### Creating a Project from a Flow

```python
# Get a flow
flow = flow_register.get_by_name("GROMACS")

# Display available variables
flow.display_variables()

# Create a project with custom variables
compute_unit = compute_manager.get_unit(name="GPU-Unit")
project = flow.create_project(
    project_name="protein-simulation",
    variables={
        "PROTEIN_FILE": "1AKI.pdb",
        "SIMULATION_TIME": "100",
        "TEMPERATURE": "300"
    },
    compute_unit=compute_unit
)
```

#### Working with Flow Variables

```python
# Get flow variables
variables = flow.get_variables()

# Display in table format
flow.display_variables(table=True)

# Display as JSON
flow.display_variables(table=False)
```

#### Generating Docker Compose Configuration

```python
# Generate compose file with variables
compose_yaml = flow.generate_compose(
    variables={
        "CPU_CORES": "8",
        "MEMORY": "16G",
        "INPUT_PATH": "/data/input"
    },
    project_name="my-analysis"
)

# Save to file
import yaml
with open("docker-compose.yml", "w") as f:
    yaml.dump(compose_yaml, f)
```

## Common Flow Patterns

### Single Container Flow

```yaml
version: '3.8'
services:
  main:
    image: scientific/tool:latest
    command: ${COMMAND}
    environment:
      - PARAM1=${PARAM1}
    volumes:
      - ./input:/input
      - ./output:/output
```

### Multi-Container Pipeline

```yaml
version: '3.8'
services:
  preprocessor:
    image: preprocess:latest
    command: preprocess --input /data/raw --output /data/processed
    volumes:
      - ./data:/data
  
  analyzer:
    image: analyze:latest
    depends_on:
      - preprocessor
    command: analyze --input /data/processed --output /data/results
    volumes:
      - ./data:/data
  
  visualizer:
    image: visualize:latest
    depends_on:
      - analyzer
    command: visualize --input /data/results
    volumes:
      - ./data:/data
    ports:
      - "8080:8080"
```

### GPU-Enabled Flow

```yaml
version: '3.8'
services:
  gpu_compute:
    image: nvidia/cuda:11.0-runtime
    runtime: nvidia
    environment:
      - NVIDIA_VISIBLE_DEVICES=all
    command: python gpu_simulation.py
    volumes:
      - ./models:/models
```

## Best Practices

1. **Use Variables**: Make flows configurable with variables for reusability
2. **Tag Appropriately**: Use descriptive tags for easy discovery
3. **Document Variables**: Provide clear descriptions and defaults
4. **Version Control**: Track flow templates in version control
5. **Test Locally**: Test flows with docker-compose before publishing
6. **Resource Limits**: Define resource constraints for predictable behavior
7. **Health Checks**: Include health checks for service reliability