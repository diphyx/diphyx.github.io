# Storage

## Overview

Every compute-unit in dxflow can have its own storage module, allowing for efficient data management across different compute units. The storage module provides a unified API to interact with various storage backends, making it easy to store, retrieve, and manage data across AWS, Azure, Google Cloud, and other providers.

## Usage

### Session-Level Storage Manager

You can access storage at the session level for global storage operations:

```python
from dxflow import Session

session = Session(email="your@email.com", password="your_password")
storage_manager = session.get_storage_manager()

# List files in your storage
storage_manager.list(provider="AWS", path="/")

# Upload a file
storage_manager.upload(
    src="/local/file.txt",
    dst="/remote/path/",
    provider="AWS"
)

# Download a file
storage_manager.download(
    src="/remote/file.txt",
    dst="/local/downloads/",
    provider="AWS"
)
```

### Compute Unit Storage

Each compute unit has its own storage context:

```python
from dxflow import Session

session = Session(email="your@email.com", password="your_password")
compute_manager = session.get_compute_manager()

compute_unit = compute_manager.get_unit(name="Demo")

compute_unit.storage.ls() # an ls like command to list files in the storage
```
```markdown
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
## StorageManager Methods

### Core Methods:
- **`list(provider="AWS", path="/")`**: Lists storage items at the specified path in a formatted table.
- **`update_path_items(path="/", storage_type='private', provider='AWS')`**: Updates the cache of items at the specified path.
- **`update_all(provider="AWS", path="/")`**: Recursively updates all items including subdirectories.
- **`upload(src, dst, provider="AWS", utype='private')`**: Uploads a file to the storage.
  - `src`: Local file path
  - `dst`: Remote destination path
  - `provider`: Cloud provider (AWS, AZURE, GCP)
  - `utype`: Upload type ('private' or 'public')
- **`download(src, dst=os.getcwd(), provider="AWS")`**: Downloads a file from the storage.
  - `src`: Remote file path
  - `dst`: Local destination directory
  - `provider`: Cloud provider

## Unit Storage Methods

When accessing storage through a compute unit:

- **`list(path="/", recursive=False, return_info=False)`**: Lists storage items at the specified path.
- **`ls(path="/", recursive=False)`**: Alias for the `list()` method.
- **`upload(src, dst)`**: Uploads a file to the unit's storage.
- **`download(src, dst, create_dir=True)`**: Downloads a file from the unit's storage.
- **`mkdir(path)`**: Creates a directory in the storage.
- **`delete(path)`**: Deletes a file or directory from the storage.
- **`copy(src, dst)`**: Copies a file or directory within the storage.
- **`move(src, dst)`**: Moves a file or directory within the storage.

## Examples

### Working with Multiple Providers

```python
# AWS S3
storage_manager.upload(
    src="/local/data.csv",
    dst="/s3-bucket/data/",
    provider="AWS"
)

# Azure Blob Storage
storage_manager.upload(
    src="/local/model.pkl",
    dst="/container/models/",
    provider="AZURE"
)

# Google Cloud Storage
storage_manager.upload(
    src="/local/results.json",
    dst="/gcs-bucket/results/",
    provider="GCP"
)
```

### Batch Operations

```python
import os

# Upload all CSV files in a directory
data_dir = "/local/data/"
for filename in os.listdir(data_dir):
    if filename.endswith('.csv'):
        storage_manager.upload(
            src=os.path.join(data_dir, filename),
            dst="/project/datasets/",
            provider="AWS"
        )

# Download all files from a remote directory
storage_manager.update_path_items(path="/results/", provider="AWS")
content = storage_manager._get_content_from_path("AWS", "/results/")
if content:
    for item in content:
        if not item.get('directory'):
            storage_manager.download(
                src=f"/results/{item['name']}",
                dst="/local/results/",
                provider="AWS"
            )
```

## Error Handling

```python
from dxflow.exceptions import StorageError

try:
    storage_manager.upload(
        src="/local/file.txt",
        dst="/remote/",
        provider="AWS"
    )
except StorageError as e:
    print(f"Upload failed: {e}")
```