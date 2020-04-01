---
title: Zelfstudie - Een Azure-schaalset voor virtuele machines maken op basis van een aangepaste Packer-afbeelding met Terraform
description: Met behulp van Terraform een virtuele Azure-machineschaalset maken van een aangepaste installatiekopie die is gegenereerd met Packer (compleet met een virtueel netwerk en beheerde gekoppelde schijven).
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 92a8221d625f8b6b73343f74b85fdfcf5e578b23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472198"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>Zelfstudie: Een Azure-schaalset voor virtuele machines maken op basis van een aangepaste packerafbeelding met Terraform

In deze zelfstudie gebruikt u [Terraform](https://www.terraform.io/) om een [Azure-set voor virtuele machineschaal](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) te maken en te implementeren die is gemaakt met een aangepaste afbeelding die is geproduceerd met [packer](https://www.packer.io/intro/index.html) met beheerde schijven die de [HashiCorp-configuratietaal](https://www.terraform.io/docs/configuration/syntax.html) (HCL) gebruiken. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw Terraform-implementatie in.
> * Gebruik variabelen en uitgangen voor terraformimplementatie.
> * Een netwerkinfrastructuur maken en implementeren.
> * Maak een aangepaste virtuele machineafbeelding met Packer.
> * Maak en implementeer een virtuele machineschaalset met behulp van de aangepaste afbeelding.
> * Maak en implementeer een jumpbox.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- **Terraform**: [Installeer Terraform en configureer toegang tot Azure](terraform-install-configure.md).
- **SSH-sleutelpaar**: [Maak een SSH-sleutelpaar.](/azure/virtual-machines/linux/mac-create-ssh-keys)
- **Verpakker**: [Installeer Packer](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>De bestandsstructuur maken

Maak drie nieuwe bestanden in een lege map met de volgende namen:

- `variables.tf`: Dit bestand bevat de waarden van de variabelen die in de sjabloon worden gebruikt.
- `output.tf`: Dit bestand beschrijft de instellingen die worden weergegeven na de implementatie.
- `vmss.tf`: Dit bestand bevat de code van de infrastructuur die u implementeert.

##  <a name="create-the-variables"></a>De variabelen maken 

In deze stap definieert u de variabelen waarmee de met Terraform gemaakte resources worden aangepast.

Bewerk `variables.tf` het bestand, kopieer de volgende code en sla de wijzigingen op.

```hcl
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> De standaardwaarde van de variabele resource_group_name is uitgeschakeld. Definieer je eigen waarde.

Sla het bestand op.

Wanneer u uw Terraform-sjabloon implementeert, wilt u de volledig gekwalificeerde domeinnaam die wordt gebruikt om toegang te krijgen tot de toepassing. Gebruik het resourcetype `output` van Terraform en haal de eigenschap `fqdn` van de resource op. 

Bewerk het bestand `output.tf` en kopieer de volgende code om de volledig gekwalificeerde domeinnaam voor de virtuele machines beschikbaar te maken. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>De netwerkinfrastructuur definiëren in een sjabloon 

In deze stap maakt u de volgende netwerkinfrastructuur in een nieuwe Azure-resourcegroep: 
  - Eén virtueel netwerk met de adresruimte van 10.0.0.0/16.
  - Eén subnet met de adresruimte van 10.0.2.0/24.
  - Twee openbare IP-adressen. Een wordt gebruikt door de virtuele machine schaal set load balancer. De andere wordt gebruikt om verbinding te maken met de SSH jumpbox.

U hebt ook een resourcegroep nodig waarin alle resources worden gemaakt. 

Bewerk en kopieer de volgende code naar het bestand `vmss.tf`: 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = var.resource_group_name
  location = var.location

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
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
  allocation_method            = "static"
  domain_name_label            = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Tag de resources die in Azure worden geïmplementeerd om hun identificatie in de toekomst te vergemakkelijken.

## <a name="create-the-network-infrastructure"></a>De netwerkinfrastructuur maken

Initialiseer de Terraform-omgeving door de volgende opdracht uit te voeren in de map waarin u de `.tf`-bestanden hebt gemaakt:

```bash
terraform init 
```
 
De provider plug-ins downloaden van de `.terraform` Terraform register in de map in de map waar u de opdracht uitgevoerd.

Voer de volgende opdracht uit om de infrastructuur in Azure te implementeren.

```bash
terraform apply
```

Controleer of de volledig gekwalificeerde domeinnaam van het openbare IP-adres overeenkomt met uw configuratie.

![Virtuele machine schaal set Terraform volledig gekwalificeerde domeinnaam voor openbare IP-adres](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

De resourcegroep bevat de volgende resources:

![Netwerkresources voor virtuele-machineschaalset Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Een Azure-afbeelding maken met Packer
Maak een aangepaste Linux-afbeelding door de stappen in de zelfstudie [te volgen Hoe packer te gebruiken om Linux-afbeeldingen voor virtuele machines in Azure te maken.](/azure/virtual-machines/linux/build-image-with-packer)
 
Volg de zelfstudie om een gedeprovisioneerde Ubuntu-afbeelding te maken waarbij Nginx is geïnstalleerd.

![Nadat u de Packer-afbeelding hebt gemaakt, hebt u een afbeelding](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Voor de toepassing van deze zelfstudie wordt in de Packer-afbeelding een opdracht uitgevoerd om Nginx te installeren. Tijdens het maken kunt u ook uw eigen script uitvoeren.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>De infrastructuur bewerken om de virtuele-machineschaalset toe te voegen

In deze stap maakt u de volgende resources in het netwerk dat eerder is geïmplementeerd:
- Een Azure load balancer om de toepassing te serveren. Voeg het toe aan het openbare IP-adres dat eerder is geïmplementeerd.
- Eén Azure-loadbalancer en regels om de toepassing te serveren. Voeg het toe aan het openbare IP-adres dat eerder is geconfigureerd.
- Een Azure back-end adresgroep. Wijs het toe aan de load balancer.
- Een statussondepoort die door de toepassing wordt gebruikt en is geconfigureerd op de load balancer.
- Een virtuele machineschaalset die achter de load balancer zit en wordt uitgevoerd op het virtuele netwerk dat eerder is geïmplementeerd.
- [Nginx](https://nginx.org/) op de knooppunten van de virtuele machine schaal geïnstalleerd vanuit een aangepaste afbeelding.


Voeg de volgende code aan het einde van het bestand `vmss.tf` toe.

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.vmss.id
  }

  tags {
    environment = "codelab"
  }
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

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = data.azurerm_resource_group.image.name
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
    id=data.azurerm_image.image.id
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
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
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
  
  tags {
    environment = "codelab"
  }
}

```

Pas de implementatie aan door de volgende code toe te voegen aan `variables.tf`:

```hcl
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>De virtuele-machineschaalset implementeren in Azure

Voer de volgende opdracht uit om de implementatie van de virtuele-machineschaalset te visualiseren:

```bash
terraform plan
```

De uitvoer van deze opdracht ziet er uit als op de volgende afbeelding:

![Terraform virtueel-machineschaalsetplan toevoegen](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

De aanvullende resources implementeren in Azure: 

```bash
terraform apply 
```

De inhoud van de resourcegroep ziet eruit zoals op de volgende afbeelding:

![Terraform virtuele-machineschaalset-resourcegroep](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Open een browser en maak verbinding met de volledig gekwalificeerde domeinnaam die is geretourneerd door de opdracht. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Een jumpbox toevoegen aan het bestaande netwerk 

Met deze optionele stap wordt SSH-toegang toegestaan tot de exemplaren van de virtuele-machineschaalset met behulp van een jumpbox.

Voeg de volgende resources toe aan uw bestaande implementatie:
- Een netwerkinterface die is verbonden met hetzelfde subnet als de virtuele machineschaalset
- Een virtuele machine met deze netwerkinterface

Voeg de volgende code aan het einde van het bestand `vmss.tf` toe:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
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

  tags {
    environment = "codelab"
  }
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
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Bewerk `outputs.tf` om de volgende code toe te voegen die de hostnaam van het jumpbox weergeeft wanneer de implementatie is voltooid:

```
output "jumpbox_public_ip" {
    value = azurerm_public_ip.jumpbox.fqdn
}
```

## <a name="deploy-the-jumpbox"></a>De jumpbox implementeren

Implementeer de jumpbox.

```bash
terraform apply 
```

Nadat de implementatie is voltooid, ziet de inhoud van de resourcegroep eruit als de volgende afbeelding:

![Terraform virtuele-machineschaalset-resourcegroep](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Aanmelden met een wachtwoord is uitgeschakeld op de jumpbox en de virtuele machineschaalset die u hebt geïmplementeerd. Meld u aan bij SSH om toegang te krijgen tot de VM's.

## <a name="clean-up-the-environment"></a>De omgeving opschonen

Met de volgende opdracht verwijdert u de resources die in deze zelfstudie hebt gemaakt:

```bash
terraform destroy
```

Voer *ja* in wanneer u wordt gevraagd de verwijdering van de resources te bevestigen. Het vernietigingsproces kan enkele minuten in beslag nemen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van Terraform in Azure](/azure/terraform)
