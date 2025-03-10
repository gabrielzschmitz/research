# Installing Hyperledger Fabric from Source

This guide explains how to install Hyperledger Fabric from source, configure
necessary dependencies, and build the project.

**Reference:** [Hyperledger Fabric Development Environment Setup](https://hyperledger-fabric.readthedocs.io/en/latest/dev-setup/devenv.html)

## Prerequisites

Before building Hyperledger Fabric, install the required dependencies based on
your operating system.

### Install Dependencies
#### Arch Linux
```shell
sudo pacman -S softhsm
```

#### Ubuntu
```shell
sudo apt install build-essential softhsm2
```

## Configure SoftHSM
SoftHSM is required for cryptographic operations. Follow these steps to
configure it:

1. Create the configuration directory:
   ```shell
   mkdir -p $HOME/.config/softhsm2
   ```
2. Copy the configuration file:
   - **Arch Linux:**
     ```shell
     cp /etc/softhsm2.conf $HOME/.config/softhsm2
     ```
   - **Ubuntu:**
     ```shell
     cp /etc/softhsm/softhsm2.conf $HOME/.config/softhsm2
     ```
3. Initialize a security token:
   ```shell
   softhsm2-util --init-token --slot 0 --label ForFabric --so-pin 1234 --pin 98765432
   ```

## Clone and Verify Hyperledger Fabric
1. Clone the repository:
   ```shell
   git clone https://github.com/hyperledger/fabric.git
   ```
2. Install Go tools:
   ```shell
   make gotools
   ```
3. Add Go binaries to your `PATH`:
   ```shell
   export PATH=$PATH:$HOME/go/bin
   ```
4. Perform basic checks and install integration test prerequisites:
   ```shell
   make basic-checks integration-test-prereqs
   ```
5. Run integration tests:
   ```shell
   ginkgo -r ./integration/nwo
   ```

## Build Hyperledger Fabric
To build the project, run:
```shell
make dist-clean all
```

## Build Documentation
If you want to build the project documentation:
```shell
sudo make docs
$BROWSER docs/build/html/index.html  # Open in your browser
```

## Run Unit Tests
To run unit tests, execute:
```shell
make unit-test
```

This guide provides a streamlined process to set up and build Hyperledger Fabric
from source. Follow each step carefully to ensure a successful installation.
