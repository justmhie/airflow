# Apache Airflow Breeze Installation Guide for Windows

This guide provides Windows-specific instructions for installing and running Apache Airflow Breeze development environment.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Installation Methods](#installation-methods)
- [Step-by-Step Installation](#step-by-step-installation)
- [Common Issues and Solutions](#common-issues-and-solutions)
- [Performance Optimization](#performance-optimization)
- [Troubleshooting](#troubleshooting)

## Prerequisites

### System Requirements
- **Windows 10/11** (Home, Pro, Enterprise, or Education)
- **Memory**: Minimum 8GB RAM (16GB recommended)
- **Disk Space**: At least 40GB free space
- **PowerShell**: Windows PowerShell 5.1 or PowerShell 7+

### Required Software

#### 1. WSL 2 (Windows Subsystem for Linux)
WSL 2 is **strongly recommended** for the best Breeze experience on Windows.

**Installation:**
```powershell
# Run as Administrator in PowerShell
wsl --install
```

**After restart, set up Ubuntu:**
```bash
# Update packages
sudo apt update && sudo apt upgrade -y

# Install essential tools
sudo apt install -y git curl wget
```

#### 2. Docker Desktop for Windows
Download and install from [Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

**Important Docker Settings:**
- Enable WSL 2 integration in Docker Desktop settings
- Allocate at least 4GB RAM to Docker (8GB recommended)
- Enable integration with your Ubuntu distribution

![Docker WSL Integration](images/docker_wsl_integration.png)

#### 3. Python (via uv - Recommended)
Install the `uv` tool for Python management:

**In WSL 2:**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source $HOME/.cargo/env
```

**In Windows PowerShell (alternative):**
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

## Installation Methods

### Method 1: WSL 2 Installation (Recommended)

This is the **preferred method** for Windows users as it provides the best performance and compatibility.

#### Step 1: Clone Airflow Repository
```bash
# In WSL 2 terminal
cd ~
git clone https://github.com/apache/airflow.git
cd airflow
```

⚠️ **Important**: Clone in WSL 2 filesystem (`~` directory), NOT in Windows filesystem (`/mnt/c/`). This prevents path length issues and improves performance.

#### Step 2: Install Breeze
```bash
# Install Breeze using uv
uv tool install -e ./dev/breeze

# Set up autocomplete
breeze setup autocomplete
```

#### Step 3: Verify Installation
```bash
# Check Breeze version
breeze setup version

# Run resource check
breeze ci resource-check
```

### Method 2: Native Windows Installation (Alternative)

If you cannot use WSL 2, you can install Breeze natively on Windows.

#### Step 1: Clone Repository
```powershell
# In PowerShell, navigate to desired directory (NOT C:\Users\username)
cd C:\dev  # Create this directory if it doesn't exist
git clone https://github.com/apache/airflow.git
cd airflow
```

#### Step 2: Install Breeze
```powershell
# Using uv (recommended)
uv tool install -e dev\breeze

# OR using pipx (alternative)
pip install --user "pipx>=1.4.1"
pipx install -e dev\breeze
```

## Common Issues and Solutions

### Issue 1: Path Length Limitations
**Problem**: Windows has a 260-character path limit that can cause issues.

**Solution**:
- Enable long path support in Windows:
  ```powershell
  # Run as Administrator
  New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" -Name "LongPathsEnabled" -Value 1 -PropertyType DWORD -Force
  ```
- Use WSL 2 filesystem instead of Windows filesystem

### Issue 2: Docker Mount Errors
**Problem**: Errors like `mount through procfd: not a directory: unknown:`

**Solution**:
- Use WSL 2 filesystem for development
- Consider installing Docker directly in WSL 2 instead of Docker Desktop

### Issue 3: Memory Issues (Vmmem Process)
**Problem**: WSL 2 consuming excessive memory.

**Solution**:
```bash
# Clear cached memory in WSL 2
sudo sysctl -w vm.drop_caches=3

# Shutdown WSL when not in use (in Windows PowerShell)
wsl --shutdown
```

### Issue 4: Docker Socket Permissions
**Problem**: Permission denied when accessing Docker.

**Solution**:
- Ensure "Allow the default Docker socket to be used" is checked in Docker Desktop settings
- In WSL 2, add user to docker group:
  ```bash
  sudo usermod -aG docker $USER
  newgrp docker
  ```

## Performance Optimization

### WSL 2 Configuration
Create or edit `C:\Users\<username>\.wslconfig`:

```ini
[wsl2]
memory=8GB
processors=4
swap=2GB
```

### Docker Configuration
In Docker Desktop settings:
- **Memory**: 6-8GB
- **CPUs**: 4+ cores
- **Disk Image Size**: 60GB+

### File System Performance
- **DO**: Work in WSL 2 filesystem (`~/airflow`)
- **DON'T**: Work in Windows filesystem (`/mnt/c/Users/...`)

## Development Workflow

### Starting Breeze
```bash
# In WSL 2, navigate to airflow directory
cd ~/airflow

# Start Breeze environment
breeze

# Or with specific options
breeze --python 3.11 --backend postgres
```

### Using VS Code with WSL 2
1. Install VS Code on Windows
2. Install "Remote - WSL" extension
3. In WSL 2 terminal:
   ```bash
   cd ~/airflow
   code .
   ```

### Cleaning Up
```bash
# Stop Breeze
breeze down

# Clean up Docker
breeze cleanup

# Free up WSL 2 memory
sudo sysctl -w vm.drop_caches=3
```

## Troubleshooting

### Checking Resources
```bash
# Check system resources
breeze ci resource-check

# Check Docker status
docker info
```

### Common Commands
```bash
# Restart Breeze installation
uv tool install --force -e ./dev/breeze

# Check Breeze configuration
breeze setup config

# View logs
breeze logs
```

### Getting Help
- Check Docker Desktop is running and WSL 2 integration is enabled
- Verify you're in the correct directory (`~/airflow` in WSL 2)
- Run `breeze setup version` to check installation
- Use `breeze --help` for command options

## Best Practices

1. **Always use WSL 2** for development when possible
2. **Clone repository in WSL 2 filesystem** (`~` directory)
3. **Allocate sufficient resources** to Docker Desktop
4. **Regularly clean up** Docker images and containers
5. **Use VS Code with Remote-WSL** for the best development experience
6. **Shutdown WSL 2** when not in use to free memory

## Next Steps

After successful installation:
1. Follow the [Customizing Guide](02_customizing.rst) to customize your environment
2. Read the [Contributors Quick Start Guide](03_contributors_quick_start.rst)
3. Check out the main [Breeze documentation](01_installation_and_usage.rst) for advanced usage

---

## Need Help?

- **GitHub Issues**: [Apache Airflow Issues](https://github.com/apache/airflow/issues)
- **Slack**: Join #development channel on Apache Airflow Slack
- **Documentation**: [Full Breeze Documentation](https://github.com/apache/airflow/tree/main/dev/breeze/doc)
