# Storage

## Overview

Every compute-unit in dxflow can have its own storage module, allowing for efficient data management across different compute units. The storage module provides a unified API to interact with various storage backends, making it easy to store, retrieve, and manage data.

## Usage

To use the storage module, you need to import it and create a storage client.

```python
from dxflow.session import Session

session = Session(username="your@email.com", password="your_password")
compute_manager = session.get_compute_manager()

compute_unit = compute_manager.get_unit(name="Demo")

compute_unit.storage.ls() # an ls like command to list files in the storage
```
```plaintext
d       4096 2025-02-08 03:51:41 .dx
d       4096 2024-12-12 10:07:47 .ipynb_checkpoints
d       4096 2024-06-25 07:00:17 simulation_outputs
d       4096 2025-04-10 22:01:07 boltz_inputs
d       4096 2024-08-16 22:55:39 data
d       4096 2024-05-13 11:07:00 support_files
d       4096 2024-12-14 09:24:10 mac
d       4096 2025-01-30 06:52:38 me
d       4096 2024-06-30 10:56:40 verified_results
d       4096 2025-05-05 23:26:22 transfer
-     137484 2024-12-12 09:31:58 preview.png
-     128229 2025-01-16 23:53:12 screenshot.png
```
### Methods:
- **list(path="/", recursive=False, return_info=False)**: Lists storage items at the specified path.
- **ls(path="/", recursive=False)**: Alias for the `list()` method.
- **upload(src, dst)**: Uploads a file to the storage using the TUS protocol.
- **download(src, dst, create_dir=True)**: Downloads a file from the storage.
- **mkdir(path)**: Creates a directory in the storage.
- **delete(path)**: Deletes a file or directory from the storage.
- **copy(src, dst)**: Copies a file or directory within the storage.
- **move(src, dst)**: Moves a file or directory within the storage.