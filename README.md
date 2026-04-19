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
