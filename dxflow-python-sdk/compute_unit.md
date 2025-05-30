# Compute-Unit

Use one or more units to manage computational workloads on a chosen platform. These units can be provisioned in local or cloud environments, allowing tasks to be scaled and deployed efficiently. Configuration and connectivity typically occur through command-line tools or interfaces provided by the hosting platform.

## `ComputeManager` Class

The `ComputeManager` class is responsible for managing compute units. It provides methods to create, list, update, and retrieve compute units.

How to get is though `Session.get_compute_manager()`

```python
from dxflow.session import Session

session = Session(username="your_username", password="your_password")
compute_manager = session.get_compute_manager()
compute_manager.list()
``` 

```markdown
Name        | Status     | IP            | Cluster Type         | CPU | Memory(GB) | Disk                        
------------+------------+---------------+----------------------+-----+------------+-----------------------------
Demo        | READY      | 54.211.250.47 | AWS:g6.2xlarge       | 8   | 32         | {boot: 512, volume: 256}    
TEST        | TERMINATED | 54.235.60.203 | AWS:T2 Medium        | 2   | 4          | {boot: 16, volume: 48}      
```

### Methods:
- **`get_unit(self, name: str = None, ip_address: str = None) -> 'ComputeUnit' | None`**: Retrieves a compute unit by name or IP address. It will return `ComputeUnit` object if found, or None if not found.
- **`list(self, table: bool = True, return_info: bool = False)`**: Lists all compute units in a tabular format or returns their information.

---

## `ComputeUnit` Class
The `ComputeUnit` class represents an individual compute unit and provides methods to manage its lifecycle and resources.

an example of how to get a compute unit:

```python
demo_unit = compute_manager.get_unit(name="Demo")
```

### Methods:
- *`detail()`*: Retrieves detailed information about the compute unit.
- *`start()`*: Starts the compute unit.
- *`stop()`*: Stops the compute unit.
- *`troubleshoot()`*: Troubleshoots the compute unit.
- *`terminate()`*: Terminates the compute unit.
- *`stats()`*: Retrieves statistics (CPU, memory, disk) about the compute unit.
- *`get_status()`*: Returns the current status of the compute unit (e.g., running, stopped, terminated).
- *`create_project(project_name, template)`*: Creates a project within the compute unit.

---

#### Properties:
- *`storage`*: The storage system associated with the compute unit.
- *`project`*: The project manager for handling projects within the compute unit.
- *`container`*: The container manager for managing containers within the compute unit.

Each compute unit has its own `UnitStorage`, `UnitContainersManager`, and `UnitProjectsManager`, which can be accessed through `self.storage`, `self.containers`, and `self.projects`, respectively. These components provide specialized functionality for managing storage, containers, and projects.

Details about the `UnitProjectsManager`, `UnitStorage`, and `UnitContainersManager` will be discussed in the following pages.