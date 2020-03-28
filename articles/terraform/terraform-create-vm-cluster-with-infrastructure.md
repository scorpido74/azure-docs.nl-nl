---
title: Zelfstudie - Een Azure VM-cluster maken met Terraform en HCL
description: In deze zelfstudie gebruikt u Terraform en HCL om een Linux-cluster voor virtuele machines te maken met een load balancer in Azure
keywords: azure devops terraform vm virtuele machine cluster
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ae1b8eac15309ff27297d9472e70d32e68acaaac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945274"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Zelfstudie: Een Azure VM-cluster maken met Terraform en HCL

In deze zelfstudie ziet u hoe u een klein compute cluster maakt met Behulp van [HCL](https://www.terraform.io/docs/configuration/syntax.html). 

U leert hoe u de volgende taken uitvoert:

> [!div class="checklist"]
> * Azure-verificatie instellen.
> * Maak een Terraform configuratiebestand.
> * Gebruik een Terraform-configuratiebestand om een load balancer te maken.
> * Gebruik een Terraform-configuratiebestand om twee Linux-VM's in een beschikbaarheidsset te implementeren.
> * Initialiseer Terraform.
> * Maak een Terraform uitvoeringsplan.
> * Pas het uitvoeringsplan Terraform toe om de Azure-resources te maken.

## <a name="1-set-up-azure-authentication"></a>1. Azure-verificatie instellen

> [!NOTE]
> Als u [Terraform-omgevingsvariabelen](terraform-install-configure.md) gebruikt of deze zelfstudie uitvoert in de [Azure Cloud Shell](terraform-cloud-shell.md), slaat u deze sectie over.

In deze sectie genereert u een Azure service-principal en twee Terraform-configuratiebestanden die de referenties van de beveiligingsprincipal bevatten.

1. [Stel een Microsoft Azure AD-service-principal in](terraform-install-configure.md#set-up-terraform-access-to-azure) zodat via Terraform resources kunnen worden ingericht in Azure. Noteer tijdens het maken van de principal de waarden voor de abonnements-id, de tenant, de toepassings-id en het wachtwoord.

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
      version = "~>1.40"
     
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Maak een nieuw bestand voor de waarden voor uw Terraform-variabelen. Het is gebruikelijk om uw Terraform-variabele bestand `terraform.tfvars` een naam `terraform.tfvars` te geven, `*.auto.tfvars`omdat Terraform automatisch elk bestand met de naam (of volgens een patroon van ) laadt als het in de huidige map aanwezig is. 

7. Kopieer de volgende code in uw variabelenbestand. Vervang de tijdelijke aanduidingen als volgt: voor `subscription_id` gebruikt u de Azure-abonnements-id die u hebt opgegeven bij het uitvoeren van `az account set`. Voor `tenant_id` gebruikt u de waarde `tenant` die is geretourneerd vanuit `az ad sp create-for-rbac`. Voor `client_id` gebruikt u de waarde `appId` die is geretourneerd vanuit `az ad sp create-for-rbac`. Voor `client_secret` gebruikt u de waarde `password` die is geretourneerd vanuit `az ad sp create-for-rbac`.

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
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
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
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. 

De opdracht [terraform init](https://www.terraform.io/docs/commands/init.html) wordt gebruikt om een map voor de Terraform-configuratiebestanden te initialiseren. Dit zijn de bestanden die u in de vorige secties hebt gemaakt. Het is een goede gewoonte `terraform init` om altijd het commando uit te voeren na het schrijven van een nieuwe Terraform configuratie. 

> [!TIP]
> De opdracht `terraform init` is idempotent. Dit betekent dat deze herhaaldelijk kan worden aangeroepen met hetzelfde resultaat. Als u in een samenwerkingsomgeving werkt en u denkt dat de configuratiebestanden kunnen zijn gewijzigd, is het daarom altijd verstandig om de opdracht `terraform init` aan te roepen voordat u een plan uitvoert of toepast.

Voer de volgende opdracht uit om Terraform te initialiseren:

  ```bash
  terraform init
  ```

  ![Terraform initialiseren](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Maak een Terraform uitvoeringsplan

De opdracht [terraform plan](https://www.terraform.io/docs/commands/plan.html) wordt gebruikt om een uitvoeringsplan te maken. Voor het genereren van een uitvoeringsplan worden in Terraform alle bestanden `.tf` in de huidige map geaggregeerd. 

De [parameter -out](https://www.terraform.io/docs/commands/plan.html#out-path) slaat het uitvoeringsplan op in een uitvoerbestand. Deze functie lost gelijktijdigheidsproblemen op die veel voorkomen in omgevingen met meerdere ontwikkelaars. Een dergelijk probleem opgelost door de output bestand is het volgende scenario:

1. Dev 1 maakt het configuratiebestand.
1. Dev 2 wijzigt het configuratiebestand.
1. Dev 1 is van toepassing (voert) het configuratiebestand toe.
1. Dev 1 krijgt onverwachte resultaten niet wetende dat Dev 2 de configuratie gewijzigd.

Dev 1 die een uitvoerbestand opgeeft, voorkomt dat Dev 2 invloed heeft op Dev 1. 

Als u uw uitvoeringsplan niet hoeft op te slaan, voert u de volgende opdracht uit:

  ```bash
  terraform plan
  ```

Als u uw uitvoeringsplan wilt opslaan, voert u de volgende opdracht uit. Vervang de tijdelijke aanduidingen door de juiste waarden voor uw omgeving.

  ```bash
  terraform plan -out=<path>
  ```

Een andere nuttige parameter is [-var-bestand](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Terraform probeerde standaard het variabelenbestand als volgt te vinden:
- Bestand met de naam`terraform.tfvars`
- Bestand met de naam met behulp van het volgende patroon:`*.auto.tfvars`

Uw variabelenbestand hoeft echter geen van de twee voorgaande conventies te volgen. Geef in dat geval de bestandsnaam `-var-file` van de variabelen op bij de parameter waarbij de variabele bestandsnaam geen extensie draagt. Het volgende voorbeeld illustreert dit punt:

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform bepaalt de acties die nodig zijn om de status te bereiken die in het configuratiebestand is opgegeven.

![Een Terraform-uitvoeringsplan maken](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Het uitvoeringsplan terraform toepassen

De laatste stap van deze zelfstudie bestaat uit het gebruiken van de opdracht [terraform apply](https://www.terraform.io/docs/commands/apply.html) om de set acties toe te passen die zijn gegenereerd door de opdracht `terraform plan`.

Als u het meest recente uitvoeringsplan wilt toepassen, voert u de volgende opdracht uit:

  ```bash
  terraform apply
  ```

Als u een eerder opgeslagen uitvoeringsplan wilt toepassen, voert u de volgende opdracht uit. Vervang de tijdelijke aanduidingen door de juiste waarden voor uw omgeving:

  ```bash
  terraform apply <path>
  ```

![Een Terraform-uitvoeringsplan toepassen](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Een Azure-set voor virtuele machineschalen maken met Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)