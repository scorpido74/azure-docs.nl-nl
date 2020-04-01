---
title: Zelfstudie - Een Azure-schaalset voor virtuele machines maken met Terraform
description: Leer Terraform gebruiken om een Azure-schaalset voor virtuele machines te configureren en te versien.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 4e445d5e6ae4b7fc4528c6d61ee2bc86870827b1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472227"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-using-terraform"></a>Zelfstudie: Een Azure-schaalset voor virtuele machines maken met Terraform

[Met azure-eenvoudigmaquettes](/azure/virtual-machine-scale-sets) voor virtuele machines u identieke VM's configureren. Het aantal VM-exemplaren kan worden aangepast op basis van de vraag of een planning. Zie [Een virtuele machineschaal die is ingesteld in de Azure-portal automatisch schalen in de Azure-portal.](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal)

In deze zelfstudie leert u hoe u [Azure Cloud Shell](/azure/cloud-shell/overview) gebruiken om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een Terraform-implementatie instellen
> * Variabelen en uitvoer voor Terraform-implementatie gebruiken
> * Netwerkinfrastructuur maken en implementeren
> * Een virtuele-machineschaalset maken en implementeren, en deze koppelen aan het netwerk
> * Een jumpbox maken en implementeren om verbinding te maken met de VM's via SSH

> [!NOTE]
> De meest recente versies van de Terraform-configuratiebestanden die in dit artikel worden gebruikt, bevinden zich in de [Awesome Terraform-opslagplaats op GitHub](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

- **Terraform installeren**: volg de aanwijzingen in het artikel [Terraform en toegang tot Azure configureren](terraform-install-configure.md)

- **Een SSH-sleutelpaar maken:** zie Voor meer informatie [een SSH-openbaar en privésleutelpaar maken en gebruiken voor Linux-VM's in Azure.](/azure/virtual-machines/linux/mac-create-ssh-keys)

## <a name="create-the-directory-structure"></a>De mapstructuur maken

1. Blader naar [Azure Portal](https://portal.azure.com).

1. Azure [Cloud Shell openen](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

    ![Cloud Shell-prompt](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Maak een map met de naam `vmss`.

    ```bash
    mkdir vmss
    ```

1. Maak de nieuwe directory de actieve directory:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Het definitiebestand voor variabelen maken
In deze sectie definieert u de variabelen waarmee de met Terraform gemaakte resources worden aangepast.

Ga in de Azure Cloud Shell de volgende stappen uit:

1. Maak een bestand met de naam `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Plak de volgende code in de editor:

   ```hcl
   variable "location" {
    description = "The location where resources will be created"
   }

   variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
   }

   variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
   }
   ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="create-the-output-definitions-file"></a>Het bestand met uitvoerdefinities maken
In deze sectie maakt u het bestand dat de uitvoer na de implementatie beschrijft.

Ga in de Azure Cloud Shell de volgende stappen uit:

1. Maak een bestand met de naam `output.tf`.

    ```bash
    code output.tf
    ```

1. Plak de volgende code in de editor om de volledig gekwalificeerde domeinnaam (FQDN) voor de virtuele machines beschikbaar te maken.
   :

   ```hcl
    output "vmss_public_ip" {
        value = azurerm_public_ip.vmss.fqdn
    }
   ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="define-the-network-infrastructure-in-a-template"></a>De netwerkinfrastructuur definiëren in een sjabloon
In deze sectie maakt u de volgende netwerkinfrastructuur in een nieuwe Azure-resourcegroep:

  - Eén virtueel netwerk (VNET) met de adresruimte 10.0.0.0/16
  - Eén subnet met de adresruimte 10.0.2.0/24
  - Twee openbare IP-adressen. Eén hiervan wordt gebruikt door de load balancer van de virtuele-machineschaalset, en het andere wordt gebruikt om verbinding te maken met de SSH-jumpbox.

Ga in de Azure Cloud Shell de volgende stappen uit:

1. Maak een bestand met de naam `vmss.tf` om de infrastructuur van de virtuele-machineschaalset te beschrijven.

    ```bash
    code vmss.tf
    ```

1. Plak de volgende code aan het einde van het bestand om de volledig gekwalificeerde domeinnaam (FQDN) voor de virtuele machines beschikbaar te maken.

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = var.resource_group_name
    location = var.location
    tags     = var.tags
   }

   resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
   }

   resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    tags                = var.tags
   }

   resource "azurerm_subnet" "vmss" {
    name                 = "vmss-subnet"
    resource_group_name  = azurerm_resource_group.vmss.name
    virtual_network_name = azurerm_virtual_network.vmss.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "vmss" {
    name                         = "vmss-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = random_string.fqdn.result
    tags                         = var.tags
   }
   ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="provision-the-network-infrastructure"></a>De netwerkinfrastructuur inrichten
Als u de Azure Cloud Shell gebruikt vanuit de map waar u de configuratiebestanden hebt gemaakt (.tf), u de volgende stappen uitvoeren:

1. Initialiseer Terraform.

   ```bash
   terraform init
   ```

1. Voer de volgende opdracht uit om de gedefinieerde infrastructuur in Azure te implementeren.

   ```bash
   terraform apply
   ```

   Terraform vraagt u `location` om een `location` waarde als `variables.tf`de variabele is gedefinieerd in , maar het is nooit ingesteld. U kunt elke geldige locatie, bijvoorbeeld 'US - west', invoeren, gevolgd door Enter. (Plaats waarden die spaties bevatten tussen haakjes.)

1. Terraform drukt de uitvoer af zoals gedefinieerd in het bestand `output.tf`. Zoals in de volgende schermafbeelding wordt weergegeven, neemt `<ID>.<location>.cloudapp.azure.com`de FQDN de volgende vorm aan: . De ID is een berekende waarde en locatie is de waarde die wordt verstrekt bij het uitvoeren van Terraform.

   ![Virtuele machineschaal set volledig gekwalificeerde domeinnaam voor Openbaar IP-adres](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Selecteer in Azure Portal **Resourcegroepen** in het hoofdmenu.

1. Selecteer **myResourceGroup** op het tabblad **Resourcegroepen** om de door Terraform gemaakte resources te bekijken.
   ![Netwerkresources voor virtuele-machineschaalset](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Een virtuele-machineschaalset toevoegen

In deze sectie leert u hoe u de volgende resources kunt toevoegen aan de sjabloon:

- Een Azure load balancer en regels voor het leveren van de toepassing en deze koppelen aan het eerder in dit artikel geconfigureerde openbare IP-adres
- Een Azure backend-adresgroep en deze toewijzen aan de load balancer
- Een statustestpoort die door de toepassing wordt gebruikt en wordt geconfigureerd op de load balancer
- Een virtuele machineschaalset die zich achter de load balancer bevindt die wordt uitgevoerd op het eerder in dit artikel geïmplementeerde VNET
- [Nginx](https://nginx.org/) op de knooppunten van de virtuele-machineschaalset met [cloud-init](https://cloudinit.readthedocs.io/en/latest/).

Ga in Cloud Shell als volgt te werk:

1. Open het configuratiebestand `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Ga naar het einde van het bestand en ga naar de toevoegmodus door de A-toets te selecteren.

1. Plak de volgende code aan het einde van het bestand:

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = azurerm_public_ip.vmss.id
    }

    tags = var.tags
   }

   resource "azurerm_lb_backend_address_pool" "bpepool" {
    resource_group_name = azurerm_resource_group.vmss.name
    loadbalancer_id     = azurerm_lb.vmss.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_lb_probe" "vmss" {
    resource_group_name = azurerm_resource_group.vmss.name
    loadbalancer_id     = azurerm_lb.vmss.id
    name                = "ssh-running-probe"
    port                = var.application_port
   }

   resource "azurerm_lb_rule" "lbnatrule" {
      resource_group_name            = azurerm_resource_group.vmss.name
      loadbalancer_id                = azurerm_lb.vmss.id
      name                           = "http"
      protocol                       = "Tcp"
      frontend_port                  = var.application_port
      backend_port                   = var.application_port
      backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
      frontend_ip_configuration_name = "PublicIPAddress"
      probe_id                       = azurerm_lb_probe.vmss.id
   }

   resource "azurerm_virtual_machine_scale_set" "vmss" {
    name                = "vmscaleset"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    upgrade_policy_mode = "Manual"

    sku {
      name     = "Standard_DS1_v2"
      tier     = "Standard"
      capacity = 2
    }

    storage_profile_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = ""
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    storage_profile_data_disk {
      lun          = 0
      caching        = "ReadWrite"
      create_option  = "Empty"
      disk_size_gb   = 10
    }

    os_profile {
      computer_name_prefix = "vmlab"
      admin_username       = var.admin_user
      admin_password       = var.admin_password
      custom_data          = file("web.conf")
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    network_profile {
      name    = "terraformnetworkprofile"
      primary = true

      ip_configuration {
        name                                   = "IPConfiguration"
        subnet_id                              = azurerm_subnet.vmss.id
        load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
        primary = true
      }
    }

    tags = var.tags
   }
   ```

1. Sla het bestand op en sluit vi Editor af met de volgende opdracht:

    ```bash
    :wq
    ```

1. Maak een bestand met de naam `web.conf` om te dienen als de cloud-init-configuratie voor de virtuele machines die deel uitmaken van de schaalset.

    ```bash
    code web.conf
    ```

1. Plak de volgende code in de editor:

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Sla het bestand op en sluit vi Editor af met de volgende opdracht:

     ```bash
     :wq
     ```

1. Open het configuratiebestand `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Ga naar het einde van het bestand en ga naar de toevoegmodus door de A-toets te selecteren.

1. Pas de implementatie aan door de volgende code aan het einde van het bestand te plakken:

    ```hcl
    variable "application_port" {
       description = "The port that you want to expose to the external load balancer"
       default     = 80
    }

    variable "admin_user" {
       description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
       default     = "azureuser"
    }

    variable "admin_password" {
       description = "Default password for admin account"
    }
    ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

1. Maak een Terraform-plan om de implementatie van de virtuele-machineschaalset te visualiseren. (U moet een wachtwoord van uw keuze opgeven, evenals de locatie voor uw resources.)

    ```bash
    terraform plan
    ```

    De uitvoer van de opdracht ziet er ongeveer uit als in deze schermafbeelding:

    ![Uitvoer van het maken van de virtuele-machineschaalset](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Implementeer de nieuwe resources in Azure.

    ```bash
    terraform apply
    ```

    De uitvoer van de opdracht ziet er ongeveer uit als in deze schermafbeelding:

    ![Terraform virtuele-machineschaalset-resourcegroep](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Open een browser en maak verbinding met de FQDN die is geretourneerd door de opdracht.

    ![Resultaten van bladeren naar de FQDN](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Een SSH-jumpbox toevoegen
Een SSH *jumpbox* is een enkele server die je "springen" door naar andere servers op het netwerk toegang. In deze stap configureert u de volgende resources:

- Een netwerkinterface (of jumpbox) verbonden met hetzelfde subnet als de virtuele-machineschaalset.

- Een virtuele machine die aan deze netwerkinterface wordt gekoppeld. Deze 'jumpbox' is extern toegankelijk. Wanneer u verbinding hebt, kunt u SSH gebruiken om toegang te krijgen tot elk van de van de virtuele machines in de schaalset.

1. Open het configuratiebestand `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Ga naar het einde van het bestand en ga naar de toevoegmodus door de A-toets te selecteren.

1. Plak de volgende code aan het einde van het bestand:

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = var.tags
   }

   resource "azurerm_network_interface" "jumpbox" {
    name                = "jumpbox-nic"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    ip_configuration {
      name                          = "IPConfiguration"
      subnet_id                     = azurerm_subnet.vmss.id
      private_ip_address_allocation = "dynamic"
      public_ip_address_id          = azurerm_public_ip.jumpbox.id
    }

    tags = var.tags
   }

   resource "azurerm_virtual_machine" "jumpbox" {
    name                  = "jumpbox"
    location              = var.location
    resource_group_name   = azurerm_resource_group.vmss.name
    network_interface_ids = [azurerm_network_interface.jumpbox.id]
    vm_size               = "Standard_DS1_v2"

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "jumpbox-osdisk"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    os_profile {
      computer_name  = "jumpbox"
      admin_username = var.admin_user
      admin_password = var.admin_password
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = var.tags
   }
   ```

1. Open het configuratiebestand `output.tf`.

   ```bash
   code output.tf
   ```

1. Ga naar het einde van het bestand en ga naar de toevoegmodus door de A-toets te selecteren.

1. Plak de volgende code aan het einde van het bestand om de hostnaam van de jumpbox weer te geven wanneer de implementatie is voltooid:

   ```hcl
   output "jumpbox_public_ip" {
      value = azurerm_public_ip.jumpbox.fqdn
   }
   ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

1. Implementeer de jumpbox.

   ```bash
   terraform apply
   ```

Als de implementatie is voltooid, lijkt de inhoud van de resourcegroep op wat wordt weergegeven in de volgende schermafbeelding:

![Terraform virtuele-machineschaalset-resourcegroep](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> De mogelijkheid om in te loggen met een wachtwoord wordt uitgeschakeld op de jumpbox en de virtuele-machineschaalset die u hebt geïmplementeerd. Meld u aan met SSH om toegang te krijgen tot de virtuele machine(s).

## <a name="environment-cleanup"></a>Opschonen van de omgeving

Voer in Cloud Shell de volgende opdracht in om de Terraform-resources te verwijderen die in deze zelfstudie zijn gemaakt:

```bash
terraform destroy
```

Het vernietigingsproces kan enkele minuten in beslag nemen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van Terraform in Azure](/azure/terraform)
