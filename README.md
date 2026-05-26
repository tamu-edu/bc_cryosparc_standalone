# bc_cryosparc_standalone

## Overview

This repo contains components to install a CryoSPARC interactive app for use with Open OnDemand on HPC.

CryoSPARC is installed in a shared Singularity image using the --standalone install option where the master and worker nodes are on the same node.
<!-- 2-3 sentences: What does this app launch? Who is it for? What problem does it solve? -->
<!-- Pick the app type that matches yours: -->

<!-- Batch Connect: -->
bc_cryosparc_standalone is an Open OnDemand Batch Connect app that launches [CryoSPARC](https://cryosparc.com) as an interactive session on HPC clusters. It is designed for researchers and students who need CryoSPARC processing on a GPU node.

The singularity --fakeroot option is needed if root privileges are unavailable. The --fakeroot option is hardcoded in the INSTALL/build_cryosparc4ood.sh script.

## Screenshots

<!-- A screenshot helps deployers verify their installation and helps users understand what they'll get. -->
<!-- Place images in a screenshots/ or docs/ directory. -->

![Application running in browser](docs/screenshot.png)

## Features

<!-- List the key capabilities specific to THIS OOD app (not the upstream software). -->

- Launches CryoSPARC via VNC desktop on compute nodes
- Supports CPU and GPU execution but most CryoSPARC jobs require a GPU
- Configurable memory, cores, and wall time via the launch form
- Containerized via Singularity/Apptainer; build script included
- Lmod .lua file included for interacting with the CryoSPARC session via command line

## Requirements

### Compute Node Software

- Singularity or Apptainer
- XFCE Windows Manager
- Firefox module; or reconfigure to use SSH tunneling

### Open OnDemand

- Open OnDemand minimum version 4.0.1+ for dynamic labels
- Scheduler: Slurm
- Lmod. The provided module file is needed for interactive CLI commands but not required to launch the GUI.

### Optional

## App Installation

Run the INSTALL/build_cryosparc4ood.sh script to generate the Singularity image. (build_cryosparc.sh --help)

- Singularity image installation requires a GPU and internet access.
- The CryoSPARC GUI is launched using Firefox but can be reconfigured if SSH tunneling is allowed on your HPC

### 1. Clone the repository

```bash
# Batch Connect apps:
cd /var/www/ood/apps/sys

git clone https://github.com/tamu-edu/bc_cryosparc_standalone.git
cd bc_cryosparc_standalone

# Pin to a release (recommended)
git checkout v1.0.0
```

### 2. Configure for your site

<!-- Point deployers to the ONE place they need to edit. -->
#### form.yml Attributes

Edit `form.yml` and update these values for your cluster:

| Attribute | Description | Default |
|-----------|-------------|---------|
| `cluster` | Target cluster ID | `"my_cluster"` |
| `version` | CryoSPARC version | `"4.7.1"` |
| `num_hours` | Maximum wall time (hours) | `1` |
| `num_cores` | Number of cores | `1` |
| `mem_per_node` | GB memory for one node | `"64"` |

#### manifest.yml Attributes

Edit `manifest.yml` and update these values for your organization:

| Attribute | Change to |
|-----------|-----------|
| `description` | Your cluster and your documentation |


#### Environment variables

<!-- Only include this section if your scripts expect variables beyond what OOD provides. -->

| Variable | Required | Description |
|----------|----------|-------------|
| `SINGULARITYENV_http_proxy` | No | proxy IP if needed by compute nodes |
| `SINGULARITYENV_https_proxy` | No | proxy IP if needed by compute nodes |

<!-- Document ALL site-specific values and where they live. -->
<!-- This is the most important section for deployers at other sites. -->

<!-- Batch Connect apps: document form.yml attributes -->


### 3. Verify

<!-- Batch Connect: -->
No OOD restart is needed (Batch Connect apps are detected automatically). Visit your OOD dashboard and look for **CryoSPARC** under **Interactive Apps > Imaging**.


## Troubleshooting

### Job starts but app doesn't appear (Batch Connect)

1. Check the job's `output.log` in `~/ondemand/data/sys/bc_cryosparc_standalone/`
2. Verify the Firefox module loads correctly: `module load Firefox`
3. For VNC apps, verify the window manager is installed: `which xfwm4`

### "Module not found" error

The module name in `form.yml` doesn't match your system. Run `module spider software` to find the correct name and update the `modules` attribute.

### Connection timeout

The app may need more time to start. Increase the connection timeout or check that the compute node can open the required port.

<!-- Add real issues you've encountered during testing. -->

## Testing

<!-- Where has this app been deployed and verified? -->

| Site | OOD Version | Scheduler | Status |
|------|-------------|-----------|--------|
| Texas A&M University | 4.0.1 | Slurm 25.05.6 | Tested |

<!-- How can a deployer verify it works? -->

To verify your installation:

1. Launch the app from the OOD dashboard with default settings
2. Confirm the application loads in the browser
3. Can run one of the built-in CryoSPARC benchmark jobs

## Known Limitations

<!-- Be honest about what doesn't work or hasn't been tested. -->

- Multi-node jobs are not supported
- A new database is created for each major.minor version (one 4.7 db for v4.7.0 and v4.7.1) to reduce the chance of databases not updating properly when updating. 

## Contributing

Contributions are welcome. To contribute:

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/my-improvement`)
3. Submit a pull request with a description of your changes

For bugs or feature requests, [open an issue](https://github.com/tamu-edu/bc_cryosparc_standalone/issues).

This app is part of the [OOD Appverse](https://ondemand.connectci.org/affinity-groups/ood-appverse). Join the [Appverse Affinity Group](https://ondemand.connectci.org/affinity-groups/ood-appverse) to connect with other contributors.

## References

<!-- Credit upstream projects and any code you borrowed. -->

- [CryoSPARC](https://cryosparc.com) — the application launched by this OOD app
- [Open OnDemand](https://openondemand.org/) — the HPC portal framework

### Software Installation

The INSTALL/build_cryosparc4ood.sh script will create a Singularity image which will be used by all users but each user will have their own copy of the database and log files which will be stored in their $SCRATCH directory. Run "build_cryosparc4ood -h" for usage.
#### Prerequisites
You will need to review and update any of the following as needed
1. In the template/script.sh file: 
   1. Update the http_proxy variable if your compute nodes do not have internet access.
   2. Update the singularity_image= value to the path of the singulairty image.sif file
   3. Update the user_cryosparc_directory variable if your cluster does not use $SCRATCH
   4. the $TMPDIR is used on the compute node to write the .lock files where are automatically removed when the $TMPDIR is deleted after a job ends
   5. configure the ssdquota and quotamax variables
2. In the INSTALL/build_cryosparc4ood.sh script
   1. update nvidia/cuda base image version as needed from https://hub.docker.com/r/nvidia/cuda
   2. update the nvidia-driver and nvidia-dkms package versions if needed
   3. remove the --fakeroot singularity option if you do not have it enabled

## License

[MIT License](LICENSE)

<!-- If dual-licensing, specify: -->
<!-- Code: [MIT License](LICENSE) -->
<!-- Documentation: [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/) -->

## Acknowledgments

This work is supported by NSF award number 2112356.



## CryoSPARC on Open OnDemand
TAMU HPRC's implementation of an Open OnDemand CryoSPARC interactive app

This repo contains components to install a CryoSPARC interactive app for use with Open OnDemand. CryoSPARC is installed in a single shared Singularity image where the master and worker nodes are on the same node. The interactive app launches a job on a single node and the database and log files are saved to each user's $SCRATCH/.cryosparc-v4.7/ directory. The worker node configuration and randomly assigned port are automatically updated to match each new interactive session. CryoSPARC is accessed using Firefox which is launched on the compute node in the interactive app.

### Requirements
1. Singularity or Apptainer
2. CryoSPARC Academic License ID for the installation.
3. cryosparc_master.tar.gz and cryosparc_worker.tar.gz files downloaded from cryosparc.com into a directory named src located in the same directory as the build_cryosparc4ood.sh script
4. some dynamic form widgets in the form.yml.erb file such as dynamic labels require OOD v4.0.1+
5. installation requires an available GPU and internet access
6. Firefox on compute nodes is required or you can reconfigure how the CryoSPARC GUI is accessed if SSH tunneling is allowed

### Installation
The INSTALL/build_cryosparc4ood.sh script will create a Singularity image which will be used by all users but each user will have their own copy of the database and log files which will be stored in their $SCRATCH directory. Run "build_cryosparc4ood -h" for usage.

### Prerequisites
You will need to review and update any of the following as needed
1. In the template/script.sh file: 
   1. Update the http_proxy variable if your compute nodes do not have internet access.
   2. Update the singularity_image= value to the path of the singulairty image.sif file
   3. Update the user_cryosparc_directory variable if your cluster does not use $SCRATCH
   4. the $TMPDIR is used on the compute node to write the .lock files where are automatically removed when the $TMPDIR is deleted after a job ends
   5. configure the ssdquota and quotamax variables
2. In the INSTALL/build_cryosparc4ood.sh script
   1. update nvidia/cuda base image version as needed from https://hub.docker.com/r/nvidia/cuda
   2. update the nvidia-driver and nvidia-dkms package versions if needed
   3. remove the --fakeroot singularity option if you do not have it enabled

### Notes
* A new database will be created for each user for each major.minor CryoSPARC version

## Contact
For questions or assistance, please contact:
- Technical support: cmdickens@tamu.edu
