---
title: Zelf studie-een schaalset voor virtuele Azure-machines maken met behulp van terraform
description: Meer informatie over het gebruik van terraform voor het configureren en instellen van een schaalset voor virtuele Azure-machines.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 6dcdad21eef003fe773a2c6ea3cb8a69b9175ecb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369470"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-using-terraform"></a>Zelf studie: een schaalset voor virtuele Azure-machines maken met behulp van terraform

Met [schaal sets voor virtuele Azure-machines](/azure/virtual-machine-scale-sets) kunt u identieke vm's configureren. Het aantal VM-exemplaren kan worden aangepast op basis van de vraag of een schema. Zie voor meer informatie [automatisch schalen van een schaalset voor virtuele machines in de Azure Portal](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal).

In deze zelf studie leert u hoe u [Azure Cloud shell](/azure/cloud-shell/overview) kunt gebruiken om de volgende taken uit te voeren:

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

- **Terraform installeren**: volg de aanwijzingen in het artikel [Terraform en toegang tot Azure configureren](/azure/virtual-machines/linux/terraform-install-configure)

- **Een SSH-sleutel paar maken**: Zie [een openbaar en persoonlijk SSH-sleutel paar maken en gebruiken voor Linux-vm's in azure](/azure/virtual-machines/linux/mac-create-ssh-keys)voor meer informatie.

## <a name="create-the-directory-structure"></a>De mapstructuur maken

1. Blader naar [Azure Portal](https://portal.azure.com).

1. Open [Azure Cloud Shell](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

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

Voer de volgende stappen uit in de Azure Cloud Shell:

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

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

## <a name="create-the-output-definitions-file"></a>Het bestand met uitvoerdefinities maken
In deze sectie maakt u het bestand dat de uitvoer na de implementatie beschrijft.

Voer de volgende stappen uit in de Azure Cloud Shell:

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

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

## <a name="define-the-network-infrastructure-in-a-template"></a>De netwerkinfrastructuur definiëren in een sjabloon
In deze sectie maakt u de volgende netwerkinfrastructuur in een nieuwe Azure-resourcegroep:

  - Eén virtueel netwerk (VNET) met de adresruimte 10.0.0.0/16
  - Eén subnet met de adresruimte 10.0.2.0/24
  - Twee openbare IP-adressen. Eén hiervan wordt gebruikt door de load balancer van de virtuele-machineschaalset, en het andere wordt gebruikt om verbinding te maken met de SSH-jumpbox.

Voer de volgende stappen uit in de Azure Cloud Shell:

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

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

## <a name="provision-the-network-infrastructure"></a>De netwerkinfrastructuur inrichten
Voer de volgende stappen uit met behulp van de Azure Cloud Shell uit de map waarin u de configuratie bestanden hebt gemaakt (. tf):

1. Initialiseer Terraform.

   ```bash
   terraform init
   ```

1. Voer de volgende opdracht uit om de gedefinieerde infrastructuur in Azure te implementeren.

   ```bash
   terraform apply
   ```

   Terraform vraagt u om een `location` waarde wanneer de `location` variabele in `variables.tf`is gedefinieerd, maar is nooit ingesteld. U kunt elke geldige locatie, bijvoorbeeld 'US - west', invoeren, gevolgd door Enter. (Plaats waarden die spaties bevatten tussen haakjes.)

1. Terraform drukt de uitvoer af zoals gedefinieerd in het bestand `output.tf`. Zoals in de volgende scherm afbeelding wordt weer gegeven, heeft de FQDN de volgende vorm: `<ID>.<location>.cloudapp.azure.com`. De ID is een berekende waarde en locatie is de waarde die u opgeeft wanneer u terraform uitvoert.

   ![Virtuele-machine schaalset Fully Qualified Domain Name voor openbaar IP-adres](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

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

Voer in Cloud Shell de volgende stappen uit:

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

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

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
Een SSH- *JumpBox* is een enkele server waarop u ' Jump ' kunt gebruiken om toegang te krijgen tot andere servers in het netwerk. In deze stap configureert u de volgende resources:

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

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

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
> [Meer informatie over het gebruik van terraform in azure](/azure/terraform)
