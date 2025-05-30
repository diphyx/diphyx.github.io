# dxflow Python SDK

**dxflow** is a powerful, domain-agnostic platform for executing, managing, and productionizing scientific models, from bioinformatics and cheminformatics to computational fluid dynamics and digital physics. Think of it as the **MLflow** for scientific computing.

Built by the DiPhyX team, dxflow is engineered to simplify the entire lifecycle of scientific workflows, making compute-intensive research reproducible, scalable, and accessible, whether on local machines or in the cloud.

## Key Features

* **Job Execution & Management (Cloud + Hybrid)**: Run scientific models on DiPhyX-managed cloud infrastructure or connect your own compute resources in a hybrid environment. dxflow supports dynamic integration of HPC clusters, on-prem systems, or third-party cloud environments to provide seamless execution and job management.
* **Experiment Tracking**: Group jobs into experiments to compare configurations, costs, and results.
* **Reproducibility First**: Every job run with dxflow is versioned, documented, and fully reproducible.
* **Production-Ready Pipelines**: Move scientific models from prototypes to scalable, production-grade executions with ease.
* **Integrated Post-Processing**: Visualize results or run analyses through integrated Jupyter notebooks.
* **Robust API Access**: Use the REST API and Python SDK to automate, extend, or integrate with external systems.
* **Command-Line Interface (CLI)**: Run and manage jobs via the CLI for a developer-friendly workflow.
* **Version Control and Documentation**: dxflow automatically tracks code, configurations, inputs, and results for every job.
* **Cost and Resource Optimization**: Run cost-effective simulations with built-in resource tracking and environment selection.

## Designed For

* **Scientific Researchers**
* **Engineers and Simulation Experts**
* **Bioinformaticians & Cheminformaticians**
* **AI/ML Researchers applying models to physical or biological systems**

## Supported Domains

dxflow supports compute-intensive scientific workflows across:

* **Bioinformatics/Cheminformatics**: e.g., GROMACS, AMBER
* **CFD (Computational Fluid Dynamics)**: e.g., OpenFOAM, ANSYS Fluent, SU2
* **Physics & Engineering Simulations**
* **Custom ML/AI Pipelines with physical models**

## How It Works

1. Define your model and environment
2. Use `dxflow` CLI or Python SDK to launch jobs
3. Monitor, adjust, and post-process in real time
4. Archive and share reproducible results

## Installation

To install dxflow, you can use pip:

```bash
pip install dxflow
```

## License

Proprietary – © DiPhyX Inc.

## More Info

* [DiPhyX Website](https://diphyx.com)
* [dxflow Docs (Coming Soon)](https://dxflow.org/docs)
* Contact: `hello@diphyx.com`
