---
title: On-premises virtueel netwerk maken met terraform in azure
description: Zelf studie waarin wordt uitgelegd hoe u een on-premises VNet in azure implementeert dat lokale bronnen bevat
services: terraform
ms.service: azure
keywords: terraform, hub en spoke, netwerken, hybride netwerken, devops, virtuele machine, azure, VNet-peering, on-premises
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 98c7c2450b4aa828f544ecab4c1e320eb74bab45
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169783"
---
# <a name="tutorial-create-on-premises-virtual-network-with-terraform-in-azure"></a>Zelfstudie: On-premises virtueel netwerk maken met terraform in azure

In deze zelf studie implementeert u een on-premises netwerk met behulp van een virtueel Azure-netwerk (VNet). Een Azure-VNet kan worden vervangen door uw eigen particuliere virtuele netwerk. U doet dit door de juiste IP-adressen toe te wijzen aan de subnetten.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gebruik de HCL (HashiCorp Language) voor het implementeren van een on-premises VNet in hub-spoke-topologie
> * Terraform gebruiken om resources van het netwerk apparaat te maken
> * Terraform gebruiken voor het maken van een on-premises virtuele machine
> * Terraform gebruiken voor het maken van een on-premises virtuele particuliere netwerk gateway

## <a name="prerequisites"></a>Vereisten

1. [Maak een hub-en-spoke hybride netwerk topologie met terraform in azure](./terraform-hub-spoke-introduction.md).

## <a name="create-the-directory-structure"></a>De directorystructuur maken

Als u een on-premises netwerk wilt simuleren, maakt u een virtueel Azure-netwerk. De demo VNet neemt de plaats van een echt privé on-premises netwerk. Als u hetzelfde wilt doen met uw bestaande on-premises netwerk, wijst u de juiste IP-adressen toe aan de subnetten.

1. Blader naar de [Azure-portal](https://portal.azure.com).

1. Open [Azure Cloud Shell](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

    ![Cloud Shell-prompt](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Ga naar de nieuwe map:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-on-premises-vnet"></a>De on-premises VNet declareren

Maak het terraform-configuratie bestand dat een on-premises VNet declareert.

1. Open in Cloud Shell een nieuw bestand met de `on-prem.tf`naam.

    ```bash
    code on-prem.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    locals {
      onprem-location       = "SouthCentralUS"
      onprem-resource-group = "onprem-vnet-rg"
      prefix-onprem         = "onprem"
    }

    resource "azurerm_resource_group" "onprem-vnet-rg" {
      name     = "${local.onprem-resource-group}"
      location = "${local.onprem-location}"
    }

    resource "azurerm_virtual_network" "onprem-vnet" {
      name                = "onprem-vnet"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
      address_space       = ["192.168.0.0/16"]

      tags {
        environment = "${local.prefix-onprem}"
      }
    }

    resource "azurerm_subnet" "onprem-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.onprem-vnet.name}"
      address_prefix       = "192.168.255.224/27"
    }

    resource "azurerm_subnet" "onprem-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.onprem-vnet.name}"
      address_prefix       = "192.168.1.128/25"
    }

    resource "azurerm_public_ip" "onprem-pip" {
        name                         = "${local.prefix-onprem}-pip"
        location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
        resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
        allocation_method   = "Dynamic"

        tags {
            environment = "${local.prefix-onprem}"
        }
    }

    resource "azurerm_network_interface" "onprem-nic" {
      name                 = "${local.prefix-onprem}-nic"
      location             = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-onprem}"
        subnet_id                     = "${azurerm_subnet.onprem-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.onprem-pip.id}"
      }
    }

    # Create Network Security Group and rule
    resource "azurerm_network_security_group" "onprem-nsg" {
        name                = "${local.prefix-onprem}-nsg"
        location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
        resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

        security_rule {
            name                       = "SSH"
            priority                   = 1001
            direction                  = "Inbound"
            access                     = "Allow"
            protocol                   = "Tcp"
            source_port_range          = "*"
            destination_port_range     = "22"
          source_address_prefix      = "*"
            destination_address_prefix = "*"
        }

        tags {
            environment = "onprem"
        }
    }

    resource "azurerm_subnet_network_security_group_association" "mgmt-nsg-association" {
      subnet_id                 = "${azurerm_subnet.onprem-mgmt.id}"
      network_security_group_id = "${azurerm_network_security_group.onprem-nsg.id}"
    }

    resource "azurerm_virtual_machine" "onprem-vm" {
      name                  = "${local.prefix-onprem}-vm"
      location              = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.onprem-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.onprem-nic.id}"]
      vm_size               = "${var.vmsize}"

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-onprem}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-onprem}"
      }
    }

    resource "azurerm_public_ip" "onprem-vpn-gateway1-pip" {
      name                = "${local.prefix-onprem}-vpn-gateway1-pip"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "onprem-vpn-gateway" {
      name                = "onprem-vpn-gateway1"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = "${azurerm_public_ip.onprem-vpn-gateway1-pip.id}"
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = "${azurerm_subnet.onprem-gateway-subnet.id}"
      }
      depends_on = ["azurerm_public_ip.onprem-vpn-gateway1-pip"]

    }
    ```

1. Sla het bestand op en sluit de editor af.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een hub-virtueel netwerk maken met terraform in azure](./terraform-hub-spoke-hub-network.md)