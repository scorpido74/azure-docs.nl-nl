---
title: Terraform-modules gebruiken om een ​​VM-cluster op Azure te maken
description: Leer hoe u Terraform-modules kunt gebruiken om een cluster met ​​virtuele Windows-machines in Azure te maken
services: terraform
ms.service: azure
keywords: terraform, devops, virtuele machine, netwerk, modules
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 6279b5c9022b448aea9b33a94fc1b2b35b6d23de
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169850"
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Een VM-cluster maken met Terraform met behulp van het moduleregister

Dit artikel begeleidt u bij het maken van een klein VM-cluster met de [Azure compute module](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2) van Terraform. In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Verificatie bij Azure instellen
> * De Terraform-sjabloon maken
> * De wijzigingen visualiseren met plan
> * De configuratie toepassen om het VM-cluster te maken

Zie voor meer informatie over Terraform de [documentatie van Terraform](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Verificatie bij Azure instellen

> [!TIP]
> Als u [Terraform-omgevingsvariabelen](/azure/virtual-machines/linux/terraform-install-configure) gebruikt of deze zelfstudie uitvoert in de [Azure Cloud Shell](/azure/cloud-shell/overview), slaat u deze stap over.

 Lees [Terraform installeren en de toegang tot Azure configureren](/azure/virtual-machines/linux/terraform-install-configure) om een ​​Azure-service-principal te maken. Gebruik deze service-principal om een ​​nieuw bestand, `azureProviderAndCreds.tf`, in een lege map te vullen met de volgende code:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>De sjabloon maken

Maak een nieuwe Terraform-sjabloon met de naam `main.tf` met de volgende code:

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Voer `terraform init` uit in uw configuratiedirectory. Als u Terraform-versie 0.10.6 of hoger gebruikt, ziet u de volgende uitvoer:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>De wijzigingen visualiseren met plan

Voer `terraform plan` uit om een ​​voorbeeld te bekijken van de virtuele machine-infrastructuur die door de sjabloon is gemaakt.

![Terraform Plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>De virtuele machines maken met apply

Voer `terraform apply` uit om de virtuele machines in te richten in Azure.

![Terraform Apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Volgende stappen

- Door de lijst met [Azure Terraform-modules](https://registry.terraform.io/modules/Azure) bladeren
- [Een virtuele-machineschaalset maken met Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
