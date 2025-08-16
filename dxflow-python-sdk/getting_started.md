# Getting Started

## Installation

Install dxflow with pip:

```bash
pip install dxflow
```

## Authentication

The next step is to create a session using your DiPhyx email and password: 

```python
from dxflow import Session

# Option 1: Authenticate with credentials
session = Session(email="your_email@example.com", password="your_password")

# Option 2: Use pre-authenticated secrets (if available)
session = Session(secrets=your_secrets_dict)

# Option 3: Use stored credentials (after first authentication)
session = Session()  # Will use stored credentials from ~/.dpx/config.json
```

dxflow has four main components: 

- **Compute Unit**: A reserved cluster on a cloud platform for executing computational tasks. This can be on any cloud provider, such as AWS, Azure, or Google Cloud, or a local compute resource which is connected to the platform using the dxflow CLI. 
- **Project**: A collection of software tools or containers configured to work together for specific tasks.
-  **Storage** : A storage space for managing data and results associated with projects and compute units.
- **Flow**: Can be a single or a combination (flow) of multiple software, models or containers that can be executed on a compute unit. The definition of `flows` are defined in the same way as docker-compose, where each flow can have multiple steps or stages that are executed sequentially or in parallel.
## Working with Session Managers

Here is how to work with the session and its managers:

```python
from dxflow import Session

# Create a session
session = Session(email="email@example.com", password="your_password")

# Retrieve different managers from the session
compute_manager = session.get_compute_manager()
storage_manager = session.get_storage_manager()
flow_manager = session.get_flow_registery_manager()

# Use these managers to manage compute clusters, store data, register flows, and define compute types
```

## Quick Example

```python
from dxflow import Session

# Authenticate
session = Session(email="your_email@example.com", password="your_password")

# Get compute manager and list available units
compute_manager = session.get_compute_manager()
compute_manager.list()

# Get a specific compute unit
unit = compute_manager.get_unit(name="Demo")
if unit:
    print(f"Unit status: {unit.get_status()}")
    
    # Get unit statistics
    stats = unit.stats()
    print(f"CPU Usage: {stats['cpu_usage']}")
    
    # Access unit storage
    storage = unit.storage
    
    # Create a project
    unit.create_project("MyProject", "python-template")
```


