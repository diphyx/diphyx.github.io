# Flow

This is a concept we use at dxflow to describe one or more software tools or containers that are configured to work together to accomplish a specific task. A flow can be as simple as a single container running a script or as complex as a multi-container application with multiple services and dependencies. We use [docker-compose](https://docs.docker.com/compose/) to define flows, allowing for easy management and deployment of the containers involved.

Every `namespace` in DiPhyx has its own flow registery, which is a collection of flows that can be used to create projects. The flow registry is managed by the `FlowManager` class, which provides methods to create, retrieve, update, and delete flows.

### `FlowRegisteryManager` Class
## Usage

To use the flow module, you need to import it and create a flow manager.

```python
from dxflow.session import Session

session = Session(username="your@email.com", password="your_password")
flow_register = session.get_flow_registery_manager()
flow_register.list(filters={"tags": "Molecular Dynamics"})
```
```plaintext
Name    | Pointer                        | Tags                           | Status    | Verified
--------+--------------------------------+--------------------------------+-----------+---------
Boltz-1 | 7ea2b574-e962-4f18-bb54-7e233c | Molecular Dynamics, Biomolecul | PUBLISHED | True    
```

#### Methods:

- **publish(name, description=None, properties=None, tags=None, arch="AMD64", color=None, icon=None, logo=None, image=None, logo_url=None, image_url=None)**: Publishes a new flow package.
- **update_flow_package(flow_pointer, update_data)**: Updates an existing flow package.
- **update_template(flow_pointer, template_data)**: Updates the template of a flow package.
- **get(flow_pointer)**: Retrieves a flow package.
- **get_by_flow_name(name)**: Retrieves a flow package by name.
- **get_by_name(name, update_list=True)**: Retrieves a flow package by name within the current instance.
- **get_list(filters=None)**: Retrieves a list of flow packages.
- **list(filters={"tags": "AMD64", "verified": True}, table=True, return_info=False)**: Lists flow packages with optional filters and table display.
- **available_flows(name)**: Lists available flows in a namespace.
- **activate(flow_pointer)**: Activates a flow package.
- **deactivate(flow_pointer)**: Deactivates a flow package.
- **get_flow_pointer(name=None, update_list=True)**: Retrieves the pointer of a flow package by name.


### `Flow` Class
#### Methods:

- **`__init__(name, template, pointer=None, user={}, description=None, properties={}, tags=[], environments=[], visibility="PRIVATE", status=None, created_at=None, updated_at=None, state=None, verified=None)`**: Initializes a Flow object with the provided attributes. Raises `ValueError` if required fields are missing or invalid values are provided for `visibility` or `status`.

- **`generate_compose(variables: dict=None, project_name: str=None) -> yaml`**: Generates a Docker Compose YAML file based on the flow's template and provided variables. Filters services based on profiles and validates variables. Returns the updated YAML configuration.

- **`create_project(project_name=None, variables={}, compute_unit=None, **kwargs)`**: Creates a project using the flow's template and the provided compute unit. Raises `ValueError` if no template is available.

- **`get_variables()`**: Retrieves the variables defined in the flow's template.

- **`display_variables(table=True)`**: Displays the variables in the flow's template along with their descriptions, hints, default values, and options. Supports table or JSON format.

- **`__str__()`**: Returns a JSON string representation of the Flow object, including all its attributes.