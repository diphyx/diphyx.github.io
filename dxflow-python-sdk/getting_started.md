# Getting Started

install dxflow with pip:

```bash
pip install dxflow
```

The next step is to create a session using the DiPhyx user name and password: 

```python
from dxflow.session import Session

session = Session(username="your_username", password="your_password")
```

dxflow has four main components: 

- **Compute Unit**: A reserved cluster on a cloud platform for executing computational tasks. This can be on any cloud provider, such as AWS, Azure, or Google Cloud, or a local computee resource which is connested to the platform using the dxflow CLI. 
- **Project**: A collection of software tools or containers configured to work together for specific tasks.
-  **Storage** : A storage space for managing data and results associated with projects and compute units.
-  **Flow**: can be a single or a combination (flow) of muultiple software, model or container that can be executed on a compute unit. The defintion of `flows` arre defined in the sampe way of docker-compose, where each flow can have multiple steps or stages that are executed sequentially or in parallel.
-  
Here is one way to work with the session and its managers:

```python
# Create a session with either a secrets dictionary or your credentials
from dxflow.session import Session

session = Session(email="email@example.com", password="your_password")

# Retrieve different managers from the session
compute_manager = session.get_compute_manager()
storage_manager = session.get_storage_manager()
flow_manager = session.get_flow_registery_manager()


# Use these managers to manage compute clusters, store data, register flows, and define compute types
```


