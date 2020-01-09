---
title: 'Zelf studie: een Azure VM-cluster maken met terraform en HCL'
description: Gebruik terraform en HCL voor het maken van een virtuele Linux-machine cluster met een load balancer in azure
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 1ff13f05a5be463ed7477b4bbbc3e1f977a04a75
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665357"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Zelf studie: een Azure VM-cluster maken met terraform en HCL

In deze zelf studie ziet u hoe u een klein berekenings cluster maakt met behulp van [HCL](https://www.terraform.io/docs/configuration/syntax.html). 

U leert hoe u de volgende taken kunt uitvoeren:

> [!div class="checklist"]
> * Stel Azure-verificatie in.
> * Een terraform-configuratie bestand maken.
> * Gebruik een terraform-configuratie bestand om een load balancer te maken.
> * Gebruik een terraform-configuratie bestand voor het implementeren van twee virtuele Linux-machines in een beschikbaarheidsset.
> * Initialiseer Terraform.
> * Maak een terraform-uitvoerings plan.
> * Pas het uitvoerings plan terraform toe om de Azure-resources te maken.

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
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Maak een nieuw bestand voor de waarden voor uw Terraform-variabelen. Het is gebruikelijk om uw terraform-variabele bestand een naam te geven `terraform.tfvars` als terraform automatisch een bestand met de naam `terraform.tfvars` (of een patroon van `*.auto.tfvars`) laadt indien aanwezig in de huidige map. 

7. Kopieer de volgende code in uw variabelenbestand. Vervang de tijdelijke aanduidingen als volgt: voor `subscription_id` gebruikt u de Azure-abonnements-id die u hebt opgegeven bij het uitvoeren van `az account set`. Voor `tenant_id` gebruikt u de waarde `tenant` die is geretourneerd vanuit `az ad sp create-for-rbac`. Voor `client_id` gebruikt u de waarde `appId` die is geretourneerd vanuit `az ad sp create-for-rbac`. Voor `client_secret` gebruikt u de waarde `password` die is geretourneerd vanuit `az ad sp create-for-rbac`.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. een terraform-configuratie bestand maken

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
      load_balancer_backend_address_pools_ids = [azurerm_lb_backend_address_pool.test.id]
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

## <a name="3-initialize-terraform"></a>3. Initialiseer terraform 

De opdracht [terraform init](https://www.terraform.io/docs/commands/init.html) wordt gebruikt om een map voor de Terraform-configuratiebestanden te initialiseren. Dit zijn de bestanden die u in de vorige secties hebt gemaakt. Het is een goed idee om altijd de `terraform init` opdracht uit te voeren nadat u een nieuwe terraform-configuratie hebt geschreven. 

> [!TIP]
> De opdracht `terraform init` is idempotent. Dit betekent dat deze herhaaldelijk kan worden aangeroepen met hetzelfde resultaat. Als u in een samenwerkingsomgeving werkt en u denkt dat de configuratiebestanden kunnen zijn gewijzigd, is het daarom altijd verstandig om de opdracht `terraform init` aan te roepen voordat u een plan uitvoert of toepast.

Voer de volgende opdracht uit om Terraform te initialiseren:

  ```bash
  terraform init
  ```

  ![Terraform initialiseren](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Maak een terraform-uitvoerings plan

De opdracht [terraform plan](https://www.terraform.io/docs/commands/plan.html) wordt gebruikt om een uitvoeringsplan te maken. Voor het genereren van een uitvoeringsplan worden in Terraform alle bestanden `.tf` in de huidige map geaggregeerd. 

De [-out-para meter](https://www.terraform.io/docs/commands/plan.html#out-path) slaat het uitvoerings plan op in een uitvoer bestand. Deze functie behandelt de gelijktijdig voorkomende problemen in omgevingen met meerdere ontwikkel aars. Een dergelijk probleem dat door het uitvoer bestand wordt opgelost, is het volgende scenario:

1. Dev 1 maakt het configuratie bestand.
1. Dev 2 wijzigt het configuratie bestand.
1. Dev 1 is van toepassing (voert) het configuratie bestand uit.
1. Dev 1 haalt onverwachte resultaten op die niet weten dat dev 2 de configuratie heeft gewijzigd.

Dev 1 waarmee een uitvoer bestand wordt voor komen dat dev 2 invloed heeft op dev 1. 

Als u het uitvoerings plan niet hoeft op te slaan, voert u de volgende opdracht uit:

  ```bash
  terraform plan
  ```

Als u het uitvoerings plan wilt opslaan, voert u de volgende opdracht uit. Vervang de tijdelijke aanduidingen door de juiste waarden voor uw omgeving.

  ```bash
  terraform plan -out=<path>
  ```

Een andere nuttige para meter is [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Terraform heeft het bestand met variabelen standaard als volgt gevonden:
- Bestand met de naam `terraform.tfvars`
- Bestand met de naam met het volgende patroon: `*.auto.tfvars`

Het variabelen bestand hoeft echter niet te voldoen aan een van de twee voor gaande conventies. In dat geval geeft u de bestands naam van uw variabelen op met de para meter `-var-file`, waarbij de naam van uw variabele bestand geen extensie heeft. In het volgende voor beeld ziet u dit punt:

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform bepaalt de acties die nodig zijn om de status te krijgen die is opgegeven in het configuratie bestand.

![Een Terraform-uitvoeringsplan maken](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. het uitvoerings plan terraform Toep assen

De laatste stap van deze zelfstudie bestaat uit het gebruiken van de opdracht [terraform apply](https://www.terraform.io/docs/commands/apply.html) om de set acties toe te passen die zijn gegenereerd door de opdracht `terraform plan`.

Als u het meest recente uitvoeringsplan wilt toepassen, voert u de volgende opdracht uit:

  ```bash
  terraform apply
  ```

Als u een eerder opgeslagen uitvoerings plan wilt Toep assen, voert u de volgende opdracht uit. Vervang de tijdelijke aanduidingen door de juiste waarden voor uw omgeving:

  ```bash
  terraform apply <path>
  ```

![Een Terraform-uitvoeringsplan toepassen](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Een schaalset voor virtuele Azure-machines maken met behulp van terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
