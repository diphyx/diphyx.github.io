# Examples and Use Cases

This page provides comprehensive examples of using the dxflow Python SDK for various scientific computing tasks.

## Complete Workflow Examples

### Example 1: Molecular Dynamics Simulation with GROMACS

```python
from dxflow import Session
import os

# Initialize session
session = Session(email="researcher@university.edu", password="secure_password")

# Get managers
compute_manager = session.get_compute_manager()
storage_manager = session.get_storage_manager()
flow_manager = session.get_flow_registery_manager()

# Step 1: Set up compute unit
compute_manager.list()
unit = compute_manager.get_unit(name="GPU-Cluster")

# Start the unit if needed
if unit.get_status() != "READY":
    unit.start()
    print("Starting compute unit...")

# Step 2: Upload input files
storage_manager.upload(
    src="/local/proteins/1AKI.pdb",
    dst="/simulations/lysozyme/",
    provider="AWS"
)

# Step 3: Get and configure GROMACS flow
gromacs_flow = flow_manager.get_by_name("GROMACS")
gromacs_flow.display_variables()

# Step 4: Create and run simulation project
project = gromacs_flow.create_project(
    project_name="lysozyme-water-sim",
    variables={
        "PROTEIN_FILE": "1AKI.pdb",
        "FORCE_FIELD": "amber99sb",
        "WATER_MODEL": "tip3p",
        "SIMULATION_TIME": "10000",  # 10 ns
        "TEMPERATURE": "300",
        "PRESSURE": "1"
    },
    compute_unit=unit
)

# Step 5: Monitor progress
import time
while True:
    stats = unit.stats()
    print(f"CPU: {stats['cpu_usage']}, Memory: {stats['memory_usage']}")
    time.sleep(60)
    # Check if simulation is complete
    if project.status == "COMPLETED":
        break

# Step 6: Download results
storage_manager.download(
    src="/simulations/lysozyme/output/",
    dst="/local/results/lysozyme/",
    provider="AWS"
)

print("Simulation completed successfully!")
```

### Example 2: Bioinformatics Pipeline with Multiple Tools

```python
from dxflow import Session
import json

session = Session(email="bioinformatician@lab.com", password="password")

# Get managers
compute_manager = session.get_compute_manager()
storage_manager = session.get_storage_manager()
flow_manager = session.get_flow_registery_manager()

# Create a custom bioinformatics flow
template = """
version: '3.8'
services:
  fastqc:
    image: biocontainers/fastqc:latest
    command: fastqc /data/*.fastq -o /results/fastqc
    volumes:
      - ./data:/data
      - ./results:/results
  
  trimmomatic:
    image: biocontainers/trimmomatic:latest
    depends_on:
      - fastqc
    command: |
      trimmomatic PE /data/${SAMPLE}_R1.fastq /data/${SAMPLE}_R2.fastq 
      /results/trimmed/${SAMPLE}_R1_trimmed.fastq /results/trimmed/${SAMPLE}_R1_unpaired.fastq
      /results/trimmed/${SAMPLE}_R2_trimmed.fastq /results/trimmed/${SAMPLE}_R2_unpaired.fastq
      ILLUMINACLIP:/adapters/TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
    environment:
      - SAMPLE=${SAMPLE}
    volumes:
      - ./data:/data
      - ./results:/results
      - ./adapters:/adapters
  
  bwa:
    image: biocontainers/bwa:latest
    depends_on:
      - trimmomatic
    command: |
      bwa mem -t ${THREADS:-4} /reference/${REFERENCE} 
      /results/trimmed/${SAMPLE}_R1_trimmed.fastq 
      /results/trimmed/${SAMPLE}_R2_trimmed.fastq 
      > /results/aligned/${SAMPLE}.sam
    environment:
      - SAMPLE=${SAMPLE}
      - REFERENCE=${REFERENCE}
      - THREADS=${THREADS}
    volumes:
      - ./results:/results
      - ./reference:/reference
"""

# Publish the custom flow
flow_manager.publish(
    name="NGS-Pipeline",
    description="Complete NGS analysis pipeline",
    properties={"template": template},
    tags=["Bioinformatics", "NGS", "Genomics"],
    arch="AMD64"
)

# Use the flow for multiple samples
samples = ["sample1", "sample2", "sample3"]
unit = compute_manager.get_unit(name="HighMem-Unit")

for sample in samples:
    # Upload sample data
    storage_manager.upload(
        src=f"/local/sequencing/{sample}_R1.fastq",
        dst=f"/ngs-data/{sample}/",
        provider="AWS"
    )
    storage_manager.upload(
        src=f"/local/sequencing/{sample}_R2.fastq",
        dst=f"/ngs-data/{sample}/",
        provider="AWS"
    )
    
    # Create project for each sample
    flow = flow_manager.get_by_name("NGS-Pipeline")
    project = flow.create_project(
        project_name=f"ngs-analysis-{sample}",
        variables={
            "SAMPLE": sample,
            "REFERENCE": "hg38.fa",
            "THREADS": "16"
        },
        compute_unit=unit
    )
    
    print(f"Started analysis for {sample}")

# Wait for all analyses to complete
# Download results
for sample in samples:
    storage_manager.download(
        src=f"/ngs-data/{sample}/results/",
        dst=f"/local/analyzed/{sample}/",
        provider="AWS"
    )
```

### Example 3: Computational Fluid Dynamics with OpenFOAM

```python
from dxflow import Session
import yaml

session = Session(email="engineer@company.com", password="password")

# Setup
compute_manager = session.get_compute_manager()
storage_manager = session.get_storage_manager()
flow_manager = session.get_flow_registery_manager()

# Get OpenFOAM flow
openfoam_flow = flow_manager.get_by_name("OpenFOAM")

# Upload mesh and case files
storage_manager.upload(
    src="/local/cfd/airfoil_case.zip",
    dst="/cfd/airfoil/",
    provider="AWS"
)

# Configure simulation parameters
simulation_params = {
    "CASE_NAME": "airfoil",
    "SOLVER": "simpleFoam",
    "TURBULENCE_MODEL": "kOmegaSST",
    "REYNOLDS": "1e6",
    "ANGLE_OF_ATTACK": "5",
    "MAX_ITERATIONS": "5000",
    "PARALLEL_CORES": "32"
}

# Create and run CFD simulation
unit = compute_manager.get_unit(name="HPC-Cluster")
project = openfoam_flow.create_project(
    project_name="airfoil-cfd-Re1e6",
    variables=simulation_params,
    compute_unit=unit
)

# Monitor convergence
import matplotlib.pyplot as plt
residuals = []

while project.status == "RUNNING":
    # Get residual data
    unit.storage.download(
        src=f"/projects/{project.name}/postProcessing/residuals.dat",
        dst="/tmp/residuals.dat"
    )
    
    with open("/tmp/residuals.dat", "r") as f:
        data = f.read()
        # Parse residuals
        # ... parsing code ...
        
    # Plot convergence
    plt.figure(figsize=(10, 6))
    plt.semilogy(residuals)
    plt.xlabel("Iteration")
    plt.ylabel("Residual")
    plt.title("CFD Convergence")
    plt.grid(True)
    plt.savefig("convergence.png")
    
    time.sleep(30)

# Download final results
storage_manager.download(
    src="/cfd/airfoil/results/",
    dst="/local/cfd_results/airfoil/",
    provider="AWS"
)
```

## Common Patterns

### Batch Processing

```python
def batch_process(session, data_files, flow_name, base_variables):
    """Process multiple files using the same flow."""
    compute_manager = session.get_compute_manager()
    storage_manager = session.get_storage_manager()
    flow_manager = session.get_flow_registery_manager()
    
    unit = compute_manager.get_unit(name="BatchProcessor")
    flow = flow_manager.get_by_name(flow_name)
    
    projects = []
    for data_file in data_files:
        # Upload data
        storage_manager.upload(
            src=data_file,
            dst=f"/batch/{os.path.basename(data_file)}/",
            provider="AWS"
        )
        
        # Create project
        variables = base_variables.copy()
        variables["INPUT_FILE"] = os.path.basename(data_file)
        
        project = flow.create_project(
            project_name=f"batch-{os.path.basename(data_file)}",
            variables=variables,
            compute_unit=unit
        )
        projects.append(project)
    
    return projects

# Usage
data_files = [f"/data/file_{i}.dat" for i in range(100)]
projects = batch_process(
    session,
    data_files,
    "DataProcessor",
    {"PROCESSING_MODE": "fast", "OUTPUT_FORMAT": "csv"}
)
```

### Resource Optimization

```python
def select_optimal_compute_unit(session, requirements):
    """Select the best compute unit based on requirements."""
    compute_manager = session.get_compute_manager()
    units = compute_manager.list(return_info=True)
    
    suitable_units = []
    for unit_info in units:
        if (unit_info['cpu'] >= requirements['min_cpu'] and
            unit_info['memory'] >= requirements['min_memory'] and
            unit_info['status'] == 'READY'):
            suitable_units.append(unit_info)
    
    # Sort by cost efficiency or other metrics
    suitable_units.sort(key=lambda x: x['cpu'] / x.get('cost', 1))
    
    if suitable_units:
        return compute_manager.get_unit(name=suitable_units[0]['name'])
    else:
        raise Exception("No suitable compute units available")

# Usage
unit = select_optimal_compute_unit(
    session,
    {"min_cpu": 8, "min_memory": 32}
)
```

### Error Handling and Retry

```python
from dxflow.exceptions import ComputeUnitError, StorageError
import time

def run_with_retry(func, max_retries=3, delay=60):
    """Run a function with automatic retry on failure."""
    for attempt in range(max_retries):
        try:
            return func()
        except (ComputeUnitError, StorageError) as e:
            print(f"Attempt {attempt + 1} failed: {e}")
            if attempt < max_retries - 1:
                print(f"Retrying in {delay} seconds...")
                time.sleep(delay)
            else:
                raise
    
def upload_large_dataset(storage_manager, local_path, remote_path):
    """Upload large dataset with chunking and retry."""
    import os
    
    # Get all files
    files = []
    for root, dirs, filenames in os.walk(local_path):
        for filename in filenames:
            files.append(os.path.join(root, filename))
    
    # Upload in chunks
    chunk_size = 10
    for i in range(0, len(files), chunk_size):
        chunk = files[i:i+chunk_size]
        
        def upload_chunk():
            for file_path in chunk:
                relative_path = os.path.relpath(file_path, local_path)
                remote_file_path = os.path.join(remote_path, relative_path)
                storage_manager.upload(
                    src=file_path,
                    dst=os.path.dirname(remote_file_path) + "/",
                    provider="AWS"
                )
        
        run_with_retry(upload_chunk)
        print(f"Uploaded {min(i+chunk_size, len(files))}/{len(files)} files")
```

### Parallel Execution

```python
import concurrent.futures
from threading import Lock

def run_parallel_simulations(session, parameter_sets):
    """Run multiple simulations in parallel."""
    compute_manager = session.get_compute_manager()
    flow_manager = session.get_flow_registery_manager()
    
    # Get available compute units
    units = []
    for i in range(len(parameter_sets)):
        unit = compute_manager.get_unit(name=f"Unit-{i % 4}")  # Use 4 units
        units.append(unit)
    
    flow = flow_manager.get_by_name("Simulation")
    results = []
    results_lock = Lock()
    
    def run_simulation(params, unit):
        project = flow.create_project(
            project_name=f"sim-{params['id']}",
            variables=params,
            compute_unit=unit
        )
        
        # Wait for completion
        while project.status != "COMPLETED":
            time.sleep(30)
        
        # Get results
        result = {
            "id": params['id'],
            "status": "success",
            "output": project.get_output()
        }
        
        with results_lock:
            results.append(result)
        
        return result
    
    # Run simulations in parallel
    with concurrent.futures.ThreadPoolExecutor(max_workers=4) as executor:
        futures = []
        for params, unit in zip(parameter_sets, units):
            future = executor.submit(run_simulation, params, unit)
            futures.append(future)
        
        # Wait for all to complete
        concurrent.futures.wait(futures)
    
    return results
```

## Integration Examples

### Jupyter Notebook Integration

```python
# In a Jupyter notebook
from dxflow import Session
import pandas as pd
import matplotlib.pyplot as plt
from IPython.display import display, HTML

# Initialize session
session = Session()

# Interactive compute unit selection
compute_manager = session.get_compute_manager()
units_df = pd.DataFrame(compute_manager.list(return_info=True))
display(units_df)

selected_unit = input("Enter unit name: ")
unit = compute_manager.get_unit(name=selected_unit)

# Real-time monitoring
def monitor_unit(unit, duration=300):
    """Monitor unit resources for specified duration."""
    import time
    from IPython.display import clear_output
    
    stats_history = []
    for _ in range(duration // 5):
        stats = unit.stats()
        stats_history.append(stats)
        
        clear_output(wait=True)
        
        # Plot stats
        fig, (ax1, ax2, ax3) = plt.subplots(1, 3, figsize=(15, 4))
        
        # CPU usage
        cpu_values = [float(s['cpu_usage'].strip('%')) for s in stats_history]
        ax1.plot(cpu_values)
        ax1.set_title('CPU Usage')
        ax1.set_ylabel('Percentage')
        ax1.set_ylim(0, 100)
        
        # Memory usage
        mem_values = [float(s['memory_usage'].strip('%')) for s in stats_history]
        ax2.plot(mem_values)
        ax2.set_title('Memory Usage')
        ax2.set_ylabel('Percentage')
        ax2.set_ylim(0, 100)
        
        # Disk usage
        disk_values = [float(s['disk_usage'].strip('%')) for s in stats_history]
        ax3.plot(disk_values)
        ax3.set_title('Disk Usage')
        ax3.set_ylabel('Percentage')
        ax3.set_ylim(0, 100)
        
        plt.tight_layout()
        plt.show()
        
        time.sleep(5)
    
    return stats_history

# Start monitoring
stats = monitor_unit(unit, duration=60)
```

## Best Practices

1. **Always use context managers for resources**:
   ```python
   with session.get_compute_manager() as compute_manager:
       # Use compute_manager
       pass
   ```

2. **Implement proper logging**:
   ```python
   import logging
   
   logging.basicConfig(level=logging.INFO)
   logger = logging.getLogger(__name__)
   
   logger.info("Starting simulation")
   ```

3. **Use environment variables for credentials**:
   ```python
   import os
   
   session = Session(
       email=os.environ.get('DXFLOW_EMAIL'),
       password=os.environ.get('DXFLOW_PASSWORD')
   )
   ```

4. **Clean up resources after use**:
   ```python
   try:
       # Run computations
       pass
   finally:
       unit.stop()
       storage_manager.cleanup()
   ```

5. **Version control your flows**:
   ```python
   flow_version = "v1.2.3"
   flow_manager.publish(
       name=f"MyFlow-{flow_version}",
       # ...
   )
   ```