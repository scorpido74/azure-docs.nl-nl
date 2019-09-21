---
title: Een VM-cluster maken met Terraform en HCL
description: Gebruik Terraform en HashiCorp Configuration Language (HCL) om een cluster virtuele Linux-machines met een load balancer te maken in Azure
services: terraform
ms.service: azure
keywords: terraform, devops, virtuele machine, netwerk, modules
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: bf9539512961930a97d9dcfe86722d0103c1facc
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173458"
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Een VM-cluster maken met Terraform en HCL

In deze zelfstudie wordt gedemonstreerd hoe u een klein rekencluster maakt met behulp van de [HashiCorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL). Met deze configuratie maakt u een load balancer, twee Linux-VM's in een [beschikbaarheidsset](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) en alle vereiste netwerkresources.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Azure-verificatie instellen
> * Een Terraform-configuratiebestand maken
> * Terraform initialiseren
> * Een Terraform-uitvoeringsplan maken
> * Een Terraform-uitvoeringsplan toepassen

## <a name="1-set-up-azure-authentication"></a>1. Azure-verificatie instellen

> [!NOTE]
> Als u [Terraform-omgevingsvariabelen](/azure/virtual-machines/linux/terraform-install-configure) gebruikt of deze zelfstudie uitvoert in de [Azure Cloud Shell](terraform-cloud-shell.md), slaat u deze sectie over.

In deze sectie genereert u een Azure service-principal en twee Terraform-configuratiebestanden die de referenties van de beveiligingsprincipal bevatten.

1. [Stel een Microsoft Azure AD-service-principal in](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) zodat via Terraform resources kunnen worden ingericht in Azure. Noteer tijdens het maken van de principal de waarden voor de abonnements-id, de tenant, de toepassings-id en het wachtwoord.

2. Open een opdrachtprompt.

3. Maak een lege map om uw Terraform-bestanden in op te slaan.

4. Maak een nieuw bestand voor de declaraties voor de variabelen. U kunt dit bestand elke gewenste naam geven, met de extensie `.tf`.

5. Kopieer de volgende code in uw bestand met variabelendeclaraties:

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
   }
   ```

6. Maak een nieuw bestand voor de waarden voor uw Terraform-variabelen. Het is gebruikelijk om uw bestand met Terraform-variabelen de naam `terraform.tfvars` te geven, omdat in Terraform automatisch elk bestand met de naam `terraform.tfvars` (of met het patroon `*.auto.tfvars`) wordt geladen, indien aanwezig in de huidige map. 

7. Kopieer de volgende code in uw variabelenbestand. Zorg dat u de plaatsaanduidingen als volgt vervangt: Voor `subscription_id` gebruikt u de Azure-abonnements-id die u hebt opgegeven bij het uitvoeren van `az account set`. Voor `tenant_id` gebruikt u de waarde `tenant` die is geretourneerd vanuit `az ad sp create-for-rbac`. Voor `client_id` gebruikt u de waarde `appId` die is geretourneerd vanuit `az ad sp create-for-rbac`. Voor `client_secret` gebruikt u de waarde `password` die is geretourneerd vanuit `az ad sp create-for-rbac`.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Een Terraform-configuratiebestand maken

In deze sectie maakt u een bestand dat de resourcedefinities voor uw infrastructuur bevat.

1. Maak een nieuw bestand met de naam `main.tf`. 

2. Kopieer de volgende voorbeelden van resourcedefinities in het zojuist gemaakte bestand `main.tf`: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. Terraform initialiseren 

De opdracht [terraform init](https://www.terraform.io/docs/commands/init.html) wordt gebruikt om een map voor de Terraform-configuratiebestanden te initialiseren. Dit zijn de bestanden die u in de vorige secties hebt gemaakt. Het is raadzaam de opdracht `terraform init` altijd uit te voeren nadat u een nieuwe Terraform-configuratie hebt gemaakt. 

> [!TIP]
> De opdracht `terraform init` is idempotent. Dit betekent dat deze herhaaldelijk kan worden aangeroepen met hetzelfde resultaat. Als u in een samenwerkingsomgeving werkt en u denkt dat de configuratiebestanden kunnen zijn gewijzigd, is het daarom altijd verstandig om de opdracht `terraform init` aan te roepen voordat u een plan uitvoert of toepast.

Voer de volgende opdracht uit om Terraform te initialiseren:

  ```bash
  terraform init
  ```

  ![Terraform initialiseren](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Een Terraform-uitvoeringsplan maken

De opdracht [terraform plan](https://www.terraform.io/docs/commands/plan.html) wordt gebruikt om een uitvoeringsplan te maken. Voor het genereren van een uitvoeringsplan worden in Terraform alle bestanden `.tf` in de huidige map geaggregeerd. 

Als u in een samenwerkingsomgeving werkt waarin de configuratie kan worden gewijzigd tussen het tijdstip waarop u het uitvoeringsplan maakt en het tijdstip waarop u het uitvoert, moet u de [parameter -out bij de opdracht 'terraform plan'](https://www.terraform.io/docs/commands/plan.html#out-path) gebruiken om het uitvoeringsplan op te slaan in een bestand. Als u daarentegen in een omgeving met één persoon werkt, kunt u de parameter `-out` weglaten.

Als de naam van uw Terraform-variabelenbestand niet `terraform.tfvars` is en niet het patroon `*.auto.tfvars` volgt, moet u de bestandsnaam opgeven met de [parameter -var-file bij de opdracht 'terraform plan'](https://www.terraform.io/docs/commands/plan.html#var-file-foo) als u de opdracht `terraform plan` uitvoert.

Bij het verwerken van de opdracht `terraform plan` wordt Terraform vernieuwd en wordt bepaald welke acties nodig zijn om de gewenste status te bereiken die is opgegeven in uw configuratiebestanden.

Als u uw uitvoeringsplan niet wilt opslaan, voert u de volgende opdracht uit:

  ```bash
  terraform plan
  ```

Als u uw uitvoeringsplan wel wilt opslaan, voert u de volgende opdracht uit (vervang de tijdelijke aanduiding &lt;path> door het gewenste uitvoerpad):

  ```bash
  terraform plan -out=<path>
  ```

![Een Terraform-uitvoeringsplan maken](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Een Terraform-uitvoeringsplan toepassen

De laatste stap van deze zelfstudie bestaat uit het gebruiken van de opdracht [terraform apply](https://www.terraform.io/docs/commands/apply.html) om de set acties toe te passen die zijn gegenereerd door de opdracht `terraform plan`.

Als u het meest recente uitvoeringsplan wilt toepassen, voert u de volgende opdracht uit:

  ```bash
  terraform apply
  ```

Als u een eerder opgeslagen uitvoeringsplan wilt uitvoeren, voert u de volgende opdracht uit (vervang de tijdelijke aanduiding &lt;path> door het pad naar het opgeslagen uitvoeringsplan):

  ```bash
  terraform apply <path>
  ```

![Een Terraform-uitvoeringsplan toepassen](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Volgende stappen

- Door de lijst met [Azure Terraform-modules](https://registry.terraform.io/modules/Azure) bladeren
- [Een virtuele-machineschaalset maken met Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
