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

### Folder Purpose

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
### Verify Directory Creation

Run the following command:

```bash id="r8f2kp"
ls ~/eda
```

Expected output:

```bash id="7n2lwd"
tools  pdk
```
## Ngspice Installation
Ngspice is an open-source SPICE simulator used for analog and mixed-signal circuit analysis. It supports transient, AC, DC, and noise simulations and integrates seamlessly with Xschem for schematic-driven simulation.
### Clone Ngspice Repository

```bash id="k2d9pl"
cd ~/eda/tools
git clone https://git.code.sf.net/p/ngspice/ngspice ngspice
```

---

### Verify Clone

```bash id="m8xq2n"
ls ~/eda/tools
```

Expected output:

```bash id="z7c1vo"
ngspice
```

---

### Prepare Build (Autogen + Build Directory)

```bash id="t9w4bc"
cd ~/eda/tools/ngspice
./autogen.sh

mkdir release
cd release
```

---

### What this step does

* **autogen.sh** → Generates configuration scripts required for compilation
* **release/** → Keeps build files separate from source (clean workflow)
### Fix Common Error: libtool Missing

### Problem

While running:

```bash id="h3k9df"
./autogen.sh
```

You may see this error:

```bash id="k29s8a"
You must have libtool installed to compile ngspice.
```

---

### Why this happens

Ngspice uses the **GNU build system**, which depends on `libtool` to:

* Manage shared libraries
* Generate proper build configuration files
* Complete the `autogen.sh` process

---

### Solution

Install the missing dependency:

```bash id="w7p2sl"
sudo apt install -y libtool
```

---

### Important (Do this after installing)

You **must re-run autogen**, otherwise the build will still fail.

```bash id="x91mqp"
cd ~/eda/tools/ngspice
./autogen.sh
```

---

### Expected Result

* No “libtool missing” error
* Autogen completes successfully

---

### Note

This is a **common issue**, especially on fresh Linux installations.
It does not mean anything is broken—just a missing dependency.

---
### Configure Ngspice (Critical Step)

Now configure the build with proper options.

### Run Configuration

```bash id="k91s8m"
cd ~/eda/tools/ngspice/release

../configure \
--prefix=$HOME/eda/tools/ngspice-install \
--with-x \
--enable-xspice \
--disable-debug \
--enable-cider \
--with-readline=yes
```

---

### Why this step is important

* **--prefix** → Installs Ngspice inside your home directory (avoids system pollution)
* **--with-x** → Enables GUI support
* **--enable-xspice** → Required for advanced mixed-signal models
* **--enable-cider** → Enables device-level simulation (important for analog design)
* **--disable-debug** → Faster, optimized build
* **--with-readline** → Enables command-line editing inside Ngspice

---

### Expected Result

* Configuration completes without errors
* A `Makefile` is generated inside the `release/` folder
  
  ---
### Compile Ngspice

You are now at the build stage.

### Run Compilation

```bash id="q7xk2p"
make -j4
```

---

### What this step does

* Compiles the entire Ngspice engine
* Builds device models (BSIM, etc.)
* Generates the final simulator binary

---

### Time Estimate

* Typically **5–15 minutes**, depending on your CPU performance

---

### Important Note

* `-j4` uses **4 CPU cores** for faster compilation
* You can adjust this:

  * `-j2` → slower systems
  * `-j8` → high-performance CPUs
    
    ---
    
### Install Ngspice (Local Installation)

### Run Installation

```bash
make install
```

---

### What this step does

* Copies compiled files into your defined install directory
* Does **not require sudo** (safe local install)
* Keeps your system clean (no `/usr/local` usage)

---

### Installation Location

```bash
~/eda/tools/ngspice-install/
```

---

### Directory Structure Created

```bash
ngspice-install/
├── bin/        # Main executable (ngspice)
├── lib/        # Libraries
└── share/      # Models, configs, examples
```

---

### Key File

```bash
bin/ngspice   ← main executable
```

---

## Test Ngspice Installation

Run this command:

```bash
~/eda/tools/ngspice-install/bin/ngspice -v
```

---

### Expected Output

* Ngspice version (e.g., ngspice-45+)
* Build information
* Solver details (e.g., KLU enabled)

---

## What this confirms

* Ngspice is installed correctly
* Build completed successfully
* Simulator is ready to use

---
### Verify Correct Ngspice Binary (PATH Check)

Even after successful installation, you must ensure the system is using the **correct Ngspice binary**.

---

### Run this command

```bash id="p2k8sj"
which ngspice
```

---

### Expected Result

```bash id="u9x4mn"
/home/your-username/eda/tools/ngspice-install/bin/ngspice
```

---

## Why this step is important

Even if this works:

```bash id="z1q7rt"
ngspice -v
```

…it does **not guarantee** you are using your compiled version.

---

### What can go wrong

* System may use **APT-installed Ngspice** (`/usr/bin/ngspice`)
* Your compiled version may be ignored
* This can cause:

  * Feature mismatch (missing XSPICE, CIDER, etc.)
  * Compatibility issues with Xschem
  * Debugging confusion later

---

## What we are verifying

We are checking:

👉 **Which exact binary is being executed when you type `ngspice`**

---

### Goal

Ensure:

* System points to your custom build
* Not the default system-installed version

---

## If output is wrong (important note)

If you see something like:

```bash id="l7c2vb"
/usr/bin/ngspice
```

It means your system is **not using the correct version**.
