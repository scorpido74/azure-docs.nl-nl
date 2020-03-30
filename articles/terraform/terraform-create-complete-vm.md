---
title: Quickstart - Terraform gebruiken om een complete Linux-vm in Azure te maken
description: In deze quickstart gebruikt u Terraform om een complete Linux-virtuele machineomgeving in Azure te maken en te beheren
keywords: azure devops terraform linux vm virtuele machine
ms.topic: quickstart
ms.date: 03/15/2020
ms.openlocfilehash: f262734cc16d97e4d73af371410403a4cbb8815e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79415458"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Snelstart: maak een complete Linux virtuele machine-infrastructuur in Azure met Terraform

Met Terraform u complete infrastructuurimplementaties definiëren en maken in Azure. U bouwt Terraform-sjablonen in een door de mens leesbare indeling die Azure-resources op een consistente, reproduceerbare manier maken en configureren. In dit artikel ziet u hoe u een complete Linux-omgeving maken en resources ondersteunen met Terraform. U ook leren hoe u [Terraform installeren en configureren.](terraform-install-configure.md)

> [!NOTE]
> Voor terraform specifieke ondersteuning u direct contact opnemen met Terraform via een van hun communitykanalen:
>
>    * Het [Terraform gedeelte](https://discuss.hashicorp.com/c/terraform-core) van het community portal bevat vragen, use cases en nuttige patronen.
>
>    * Voor vragen over providers u terecht in de sectie [Terraform Providers](https://discuss.hashicorp.com/c/terraform-providers) van het communityportal.


## <a name="create-azure-connection-and-resource-group"></a>Azure-verbinding en resourcegroep maken

Laten we elk gedeelte van een Terraform-sjabloon doornemen. U ook de volledige versie van de [Terraform-sjabloon](#complete-terraform-script) zien die u kopiëren en plakken.

De `provider` sectie vertelt Terraform om een Azure-provider te gebruiken. Als u `subscription_id`waarden `client_id` `client_secret`wilt `tenant_id`opvragen voor , , en , raadpleegt u [Terraform installeren en configureren](terraform-install-configure.md). 

> [!TIP]
> Als u omgevingsvariabelen maakt voor de waarden of de [Azure Cloud Shell Bash-ervaring](/azure/cloud-shell/overview) gebruikt, hoeft u de variabele declaratieniet in deze sectie op te nemen.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

In de volgende sectie `myResourceGroup` wordt `eastus` een resourcegroep gebenoemd in de locatie:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

In aanvullende secties verwijst u `${azurerm_resource_group.myterraformgroup.name}`naar de resourcegroep met .

## <a name="create-virtual-network"></a>Virtueel netwerk maken
In de volgende sectie `myVnet` wordt `10.0.0.0/16` een virtueel netwerk met de naam in de adresruimte genaamerd:

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

In de volgende sectie `mySubnet` wordt `myVnet` een subnet genamed in het virtuele netwerk:

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Openbaar IP-adres maken
Als u toegang wilt krijgen tot bronnen op internet, maakt en wijst u een openbaar IP-adres aan uw vm toe. In de volgende sectie wordt `myPublicIP`een openbaar IP-adres gemaakt met de naam:

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken
Netwerkbeveiligingsgroepen bepalen de stroom van netwerkverkeer in en uit uw vm. In de volgende sectie wordt `myNetworkSecurityGroup` een netwerkbeveiligingsgroep met de naam en definieert u een regel om SSH-verkeer toe te staan op TCP-poort 22:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Virtuele netwerkinterfacekaart maken
Een NIC (virtual network interface card) verbindt uw VM met een bepaald virtueel netwerk, openbaar IP-adres en netwerkbeveiligingsgroep. In de volgende sectie in een sjabloon `myNIC` Terraform wordt een virtuele NIC gemaakt met de naam die is verbonden met de virtuele netwerkbronnen die u hebt gemaakt:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```


## <a name="create-storage-account-for-diagnostics"></a>Opslagaccount maken voor diagnostische gegevens
Als u opstartdiagnoses voor een virtuele machine wilt opslaan, hebt u een opslagaccount nodig. Met deze opstartdiagnosekunt u problemen oplossen en de status van uw vm controleren. Het opslagaccount dat u maakt, is alleen om de opstartgegevens op te slaan. Aangezien elk opslagaccount een unieke naam moet hebben, genereert de volgende sectie een willekeurige tekst:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Nu u een opslagaccount maken. In de volgende sectie wordt een opslagaccount gemaakt, waarbij de naam is gebaseerd op de willekeurige tekst die in de vorige stap is gegenereerd:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Virtuele machine maken

De laatste stap is het maken van een VM en het gebruik van alle resources die zijn gemaakt. In de volgende sectie `myVM` wordt een VM `myNIC`met de naam benoemd en wordt de virtuele NIC met de naam . De `Ubuntu 16.04-LTS` nieuwste afbeelding wordt gebruikt `azureuser` en een gebruiker met de naam wordt gemaakt met wachtwoordverificatie uitgeschakeld.

 SSH-sleutelgegevens worden `ssh_keys` verstrekt in de sectie. Geef een openbare SSH-sleutel in het `key_data` veld.

```hcl
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Terraform-script voltooien

Als u al deze secties wilt samenbrengen en Terraform in actie wilt zien, maakt u een bestand dat de volgende inhoud aanroept `terraform_azure.tf` en plakt u de volgende inhoud:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn't exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>De infrastructuur bouwen en implementeren
Met uw Terraform template gemaakt, de eerste stap is het initialiseren terraform. Deze stap zorgt ervoor dat Terraform alle vereisten heeft om uw sjabloon in Azure te bouwen.

```bash
terraform init
```

De volgende stap is om Terraform te laten beoordelen en de sjabloon te valideren. Met deze stap worden de gevraagde resources vergeleken met de statusgegevens die door Terraform zijn opgeslagen en worden de geplande uitvoering uitgevoerd. De Azure-resources worden op dit moment niet gemaakt.

```bash
terraform plan
```

Nadat u de vorige opdracht hebt uitgevoerd, ziet u iets als het volgende scherm:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Als alles er goed uitziet en u klaar bent om de infrastructuur in Azure te bouwen, past u de sjabloon toe in Terraform:

```bash
terraform apply
```

Zodra Terraform is voltooid, is uw VM-infrastructuur klaar. Verkrijg het openbare IP-adres van uw VM met [az vm show:](/cli/azure/vm)

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

U dan SSH naar uw VM:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Meer informatie over het gebruik van Terraform in Azure](/azure/terraform)