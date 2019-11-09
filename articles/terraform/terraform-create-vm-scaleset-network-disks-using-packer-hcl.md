---
title: 'Zelf studie: een schaalset voor virtuele Azure-machines maken op basis van een aangepaste installatie kopie van een pakket met behulp van terraform'
description: Met behulp van Terraform een virtuele Azure-machineschaalset maken van een aangepaste installatiekopie die is gegenereerd met Packer (compleet met een virtueel netwerk en beheerde gekoppelde schijven).
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 080fda3077a10d0605f061aca5226783457348f9
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837528"
---
# <a name="tutorial-create-azure-virtual-machine-scale-set-from-a-packer-custom-image-using-terraform"></a>Zelf studie: een schaalset voor virtuele Azure-machines maken op basis van een aangepaste installatie kopie van een pakket met terraform

In deze zelfstudie gebruikt u [Terraform](https://www.terraform.io/) voor het maken en implementeren van een [virtuele Azure-machineschaalset](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) die is gemaakt met een aangepaste installatiekopie die is geproduceerd met [Packer](https://www.packer.io/intro/index.html) met beheerde schijven met behulp van de [HashiCorp-configuratietaal](https://www.terraform.io/docs/configuration/syntax.html) (HCL).  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Terraform-implementatie instellen
> * Variabelen en uitvoer voor Terraform-implementatie gebruiken 
> * Een netwerkinfrastructuur maken en implementeren
> * Een aangepaste installatiekopie van een virtuele machine maken met behulp van Packer
> * Een virtuele-machineschaalset maken met behulp van de aangepaste installatiekopie
> * Een jumpbox maken en implementeren 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- **Terraform**: [terraform installeren en toegang tot Azure configureren](/azure/virtual-machines/linux/terraform-install-configure)
- **SSH-sleutel paar**: [Maak een SSH-sleutel paar](/azure/virtual-machines/linux/mac-create-ssh-keys).
- **Verpakker**: [Installeer Packer](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>De bestandsstructuur maken

Maak drie nieuwe bestanden in een lege map met de volgende namen:

- `variables.tf`: dit bestand bevat de waarden van de variabelen die in de sjabloon worden gebruikt.
- `output.tf`: in dit bestand worden de instellingen beschreven die na de implementatie worden weer gegeven.
- `vmss.tf`: dit bestand bevat de code van de infra structuur die u implementeert.

##  <a name="create-the-variables"></a>De variabelen maken 

In deze stap definieert u de variabelen waarmee de met Terraform gemaakte resources worden aangepast.

Bewerk het bestand `variables.tf`, kopieer de volgende code en sla de wijzigingen op.

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
> De standaardwaarde van de variabele resource_group_name is niet ingesteld. Definieer uw eigen waarde.

Sla het bestand op.

Wanneer u de Terraform-sjabloon implementeert, wilt u de volledig gekwalificeerde domeinnaam die wordt gebruikt voor toegang tot de toepassing ophalen. Gebruik het resourcetype `output` van Terraform en haal de eigenschap `fqdn` van de resource op. 

Bewerk het bestand `output.tf` en kopieer de volgende code om de volledig gekwalificeerde domeinnaam voor de virtuele machines beschikbaar te maken. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>De netwerkinfrastructuur definiëren in een sjabloon 

In deze stap maakt u de volgende netwerkinfrastructuur in een nieuwe Azure-resourcegroep: 
  - Eén VNET met de adresruimte 10.0.0.0/16 
  - Eén subnet met de adresruimte 10.0.2.0/24
  - Twee openbare IP-adressen. Eén hiervan wordt gebruikt door de load balancer van de virtuele-machineschaalset, en het andere wordt gebruikt om verbinding te maken met de SSH-jumpbox

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
> Het is raadzaam om de resources die worden geïmplementeerd in Azure te taggen, zodat u ze in de toekomst eenvoudiger kunt herkennen.

## <a name="create-the-network-infrastructure"></a>De netwerkinfrastructuur maken

Initialiseer de Terraform-omgeving door de volgende opdracht uit te voeren in de map waarin u de `.tf`-bestanden hebt gemaakt:

```bash
terraform init 
```
 
De provider invoeg toepassingen worden gedownload van het terraform-REGI ster in de map `.terraform` in de directory waarin u de opdracht hebt uitgevoerd.

Voer de volgende opdracht uit om de infrastructuur in Azure te implementeren.

```bash
terraform apply
```

Controleer of de volledig gekwalificeerde domeinnaam van het openbare IP-adres overeenkomt met uw configuratie.

![Volledig gekwalificeerde domeinnaam Terraform van virtuele-machineschaalset voor openbaar IP-adres](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

De resourcegroep bevat de volgende resources:

![Netwerkresources voor virtuele-machineschaalset Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Een Azure-installatiekopie maken met behulp van Packer
Maak een aangepaste Linux-installatiekopie met behulp van de stappen die worden beschreven in de zelfstudie [Installatiekopieën voor virtuele Linux-machines maken in Azure met Packer](/azure/virtual-machines/linux/build-image-with-packer).
 
Volg de zelfstudie voor het maken van een Ubuntu-installatiekopie met NGINX geïnstalleerd waarvan de inrichting ongedaan is gemaakt.

![Zodra u de Packer-installatiekopie hebt gemaakt, hebt u een installatiekopie](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Voor het doel van deze zelfstudie wordt in de Packer-installatiekopie een opdracht uitgevoerd waarmee nginx wordt geïnstalleerd. Tijdens het maken kunt u ook uw eigen script uitvoeren.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>De infrastructuur bewerken om de virtuele-machineschaalset toe te voegen

In deze stap maakt u de volgende resources in het netwerk dat eerder is geïmplementeerd:
- Azure load balancer om de toepassing te leveren en te koppelen aan het open bare IP-adres dat u eerder hebt geïmplementeerd.
- Eén Azure load balancer en regels voor het leveren van de toepassing en deze koppelen aan het eerder geconfigureerde openbare IP-adres.
- Azure back-mailadres groep en wijs deze toe aan de load balancer.
- Een status test poort die door de toepassing wordt gebruikt en die is geconfigureerd op de load balancer.
- Een schaalset voor virtuele machines zit achter de load balancer, die wordt uitgevoerd op het VNET dat eerder is geïmplementeerd.
- [Nginx](https://nginx.org/) op de knoop punten van de schaal van de virtuele machine die is geïnstalleerd vanuit een aangepaste installatie kopie.


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
- Een netwerkinterface die is verbonden met hetzelfde subnet als de virtuele-machineschaalset
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

Bewerk `outputs.tf` om de volgende code toe te voegen die de hostnaam van de jumpbox weergeeft wanneer de implementatie is voltooid:

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

Als de implementatie is voltooid, lijkt de inhoud van de resourcegroep op wat wordt weergegeven op de volgende afbeelding:

![Terraform virtuele-machineschaalset-resourcegroep](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Aanmelding met een wachtwoord wordt uitgeschakeld op de jumpbox en de virtuele-machineschaalset die u hebt geïmplementeerd. Meld u aan met SSH om toegang te krijgen tot de virtuele machines.

## <a name="clean-up-the-environment"></a>De omgeving opschonen

Met de volgende opdracht verwijdert u de resources die in deze zelfstudie hebt gemaakt:

```bash
terraform destroy
```

Typ `yes` als u wordt gevraagd om het verwijderen van de resources te bevestigen. Het vernietigingsproces kan enkele minuten in beslag nemen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van terraform in azure](/azure/terraform)