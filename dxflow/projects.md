# Projects

Projects can be summerized as a collection of containers. In order to run a project, you need to have a compute unit. 

 The project can be created from a template or from scratch.'
 
 Every compute-unit as a `UnitProjectsManager` which is used to manage projects.

 ## `UnitProjectsManager` Class
UnitProjectsManager is a class designed to manage unit projects within the dxflow SDK. It provides functionality for creating, retrieving, updating, and deleting projects, as well as interacting with project details and flows.

This class is typically accessed through a compute unit's project manager, allowing users to manage projects associated with that compute unit.

    
```python
from dxflow.session import Session

session = Session(username="your_username", password="your_password")
compute_manager = session.get_compute_manager()

compute_unit = compute_manager.get_unit(name="Demo")
projects_manager = compute_unit.projects 
compute_unit.projects.list() # or projects_manager.list() to be explicit
```
```plaintext

Name    | Creation date | Status   
--------+---------------+----------
coder-2 | 05-08-2025    | DESTROYED
coder-1 | 05-08-2025    | DESTROYED
coder   | 05-07-2025    | DESTROYED
```

Methods:

    list(update=True, return_info=False):
        Displays a table of projects and optionally returns project information.

    get(project_name: str) -> 'Project':
        Retrieves a project by its name.


    create(flow_name: str, project_name: str=None, variables: dict=None) -> 'Project':
        Creates a new project using a specified flow.

    create_from_flow(project_name: str=None, variables: dict=None, flow: Flow=None) -> 'Project':
        Creates a new project from a given flow object.

    create_from_compose_yaml(compose: yaml) -> 'Project':
        Creates a new project using a Docker Compose YAML configuration.

    delete(project_id: str) -> str | tuple:
        Deletes a project by its ID.

## `Project` Class

### `Project` Class

The `Project` class represents a project in the DXFlow SDK. It provides methods to interact with and manage the project's containers, services, templates, and status.

#### Attributes:
- `name`: The name of the project.
- `date`: The creation date of the project.
- `services`: A list of services associated with the project.
- `containers`: A list of containers within the project.
- `edges`: Connections between containers or services.
- `compose`: Docker Compose configuration for the project.
- `templates`: Templates used to define the project structure.


#### Methods:

- `detail(update_data=True, print_info=False)`:
    Retrieves and optionally updates the project's details.

- `status()`:
    Returns the current status of the project based on its containers.

- `list_containers(print_info=True, return_info=False)`:
    Lists all containers in the project and optionally returns their details.

- `logs(container_id=None, print_info=True, realtime=False, return_info=False)`:
    Retrieves logs for the project's containers. Supports real-time log streaming.

- `realtime_logs(container_id=None, time_interval=1)`:
    Streams real-time logs for a running container.

- `change_status(status: CommandStatus)`:
    Changes the status of the project (e.g., start, stop, pause, terminate).

- `start()`:
    Starts the project.

- `stop()`:
    Stops the project.

- `pause()`:
    Pauses the project.

- `unpause()`:
    Unpauses the project.

- `terminate()`:
    Terminates the project.

#### Example Usage:

```python
from dxflow.session import Session

session = Session(username="your_username", password="your_password")
compute_manager = session.get_compute_manager()
compute_unit = compute_manager.get_unit(name="Demo")
project = compute_unit.projects.get("example_project")

# Retrieve project details
project.detail(print_info=True)

# Start the project
project.start()

# Stream real-time logs
project.realtime_logs(time_interval=1)
```
