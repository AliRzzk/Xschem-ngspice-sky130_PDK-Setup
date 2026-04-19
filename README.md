# Xschem-ngspice-sky130_PDK-Setup
A clean and minimal setup guide for integrating the SKY130 PDK with Xschem and ngspice for analog circuit design and simulation. This repository provides a reproducible environment, correct model linking, working netlist examples, and troubleshooting steps to help users avoid common setup issues and quickly start designing and verifying analog circuits using the SKY130 technology.
# System Requirement
This setup must be performed on a native Linux system. Do not use Windows or WSL for this workflow.

### Why native Linux is required

* **PDK compatibility (Sky130):**
  Many scripts and dependencies assume a native Linux filesystem. WSL can break path handling and permissions.

* **Toolchain stability:**
  Tools like Xschem, Ngspice, Magic, and Open_PDKs integrate tightly with Linux libraries. WSL often introduces GUI and dependency issues.

* **Performance:**
  Simulation and layout tools rely heavily on file I/O. Native Linux provides better performance than WSL.

* **GUI reliability:**
  Xschem and Magic require stable X11/Wayland support. WSL GUI is inconsistent.

* **Industry relevance:**
  Professional analog/VLSI environments use Linux systems, not Windows.
## Workspace Setup

Create a structured workspace in your home directory to organize all analog design files.

### Create Project Directory

```bash
mkdir ~/analog_projects
cd ~/analog_projects

mkdir circuits
mkdir layouts
mkdir models
mkdir simulations
```

### Verify Directory Structure

```bash
pwd
ls
```

Expected output:

```
/home/your-username/analog_projects
circuits  layouts  models  simulations
```

---

## Folder Purpose

* **circuits/** → Store Xschem schematic files
* **layouts/** → Store layout designs (Magic/KLayout)
* **models/** → Store Sky130 PDK files and custom models
* **simulations/** → Store Ngspice netlists and output results

---
## EDA Tools & PDK Directories

Create separate directories for tools and PDK to keep your design work isolated from installations.

### Create Directories

```bash
mkdir -p ~/eda/tools
mkdir -p ~/eda/pdk
```

---

### Final Directory Structure

```bash
/home/your-username/
│
├── analog_projects/        # Your design workspace
│   ├── circuits/
│   ├── layouts/
│   ├── models/
│   └── simulations/
│
└── eda/
    ├── tools/              # Xschem, Ngspice, Magic, etc.
    └── pdk/                # Sky130 PDK files
```
