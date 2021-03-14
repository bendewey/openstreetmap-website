# Docs

- Read [README.md](README.md), [INSTALL.md](INSTALL.md), and [DOCKER.md](DOCKER.md)

# Azure Environment Setup

## Prequistes
* Already created Azure tenant, subscription, & resource group
* Azure CLI  (https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)

## VM Setup
1. Create an Ubuntu VM using Azure CLI.
    ```
    az vm create \
      --resource-group <existing-resource-group> \
      --name <vm-name> \
      --nsg <network-security-group-name> \
      --admin-username <admin-username> \
      --admin-password <admin-password> \
      --image UbuntuLTS \
      --authentication-type password \
      --public-ip-address-allocation static
    ```
    Replace the following:
    * <b>\<existing-resource-group>:</b> Name of the resource group you want to create the VM under. Replace 
    * <b>\<vm-name>:</b> Desired name for VM.
    * <b>\<network-security-group-name>:</b> Desired name for the network security group.
    * <b>\<admin-username>:</b> Admin username.
    * <b>\<admin-password>:</b> Admin password.

    After a few minutes you should get an output showing VM metadata. The following example shows a VM operation was successful (make note of the public IP address):
    ```
    {
      "fqdns": "",
      "id": "/subscriptions/<guid>/resourceGroups/<existsing-resource-group>/providers/Microsoft.Compute/virtualMachines/<vm-name>",
      "location": "eastus",
      "macAddress": "00-0D-3A-23-9A-49",
      "powerState": "VM running",
      "privateIpAddress": "10.0.0.4",
      "publicIpAddress": "40.68.254.142",
      "resourceGroup": "<existsing-resource-group>"
    }
    ```

1. SSH Into VM using SSH client of your choice confirm connectivity using the credentials specified in step #1.

1. Open the appropriate ports on the VM. Replace the following:
    * <b>\<existing-resource-group>:</b> Name of the resource group you want to create the VM under. Replace 
    * <b>\<network-security-group-name>:</b> Name for the network security group specified in step #1.

    ```
    az network nsg rule create \
      --resource-group <existing-resource-group> \
      --nsg-name <network-security-group-name> \
      --name OSM-WEB-PORT \
      --protocol tcp \
      --priority 1100 \
      --destination-port-range 3000
    ```

    ```
    az network nsg rule create \
      --resource-group <existing-resource-group> \
      --nsg-name <network-security-group-name> \
      --name OSM-SMTP-PORT \
      --protocol tcp \
      --priority 1300 \
      --destination-port-range 8025
    ```

## Configure DNS


# Install  OSM
## Prequistes
* An already existing VM you can SSH into. See [Azure Environment Setup](#azure-environment-setup) for steps.
* Open TCP Ports on 3000 & 8025

## Instructions
1. SSH into Azure VM using SSH client of your choice.

1. Become Root:
    ```
    sudo -i
    ```

1. Clone repository:
    ```
    git clone --depth=1 https://github.com/bendewey/openstreetmap-website.git
    ```

1. Install  [Install Docker](https://docs.docker.com/install/) & [Install Docker Compose](https://docs.docker.com/compose/install/)

1. Change working directory to the `openstreetmap-website`:

    ```
    cd openstreetmap-website
    ```

1. Follow from Docker instructions: [DOCKER.md](DOCKER.md#user-content-initial-setup)

1. Open the `settings.local.yml` file:
    ```
    nano config/settings.local.yml
    ```

1. Add the following configurations and save:
    ```
    server_url: <vm-public-ip>:3000
    ```
    * Replace the `<vm-public-ip>` with the public ip address for the Azure VM.

1. Restart docker-compose services:
    ```
    docker-compose restart
    ```

1.  Launch `http://<vm-public-ip>:3000`
    * Replace the `<vm-public-ip>` with the public ip address for the Azure VM.

1.  Register and create an account, check your email address for verification email link.

1. Configure OAuth Consumer Keys. Refer to [CONFIGURE.md](CONFIGURE.md#oauth-consumer-keys) for steps.
    * There are two bullets that state: 
    </br>
    `"Check the 'modify the map' box"` AND `"Everything else can be left with the default blank values."` </br>
    Instead of following those steps check all the boxes, otherwise, you will get an error when attempting to edit the map using the iD editor.
    * Replace all instances of `http://localhost:3000` with `http://<vm-public-ip>:3000`

1. Edit the map and confirm you can save.