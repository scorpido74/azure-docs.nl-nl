---
title: Azure Cloud Shell configureren voor terraform
description: Gebruik Terraform met Azure Cloud Shell om verificatie en configuratie van sjablonen te vereenvoudigen.
services: terraform
ms.service: azure
keywords: terraform, devops, schaalset, virtuele machine, netwerk, opslag, modules
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: e0a59697a3e4da97cf082c4c771fe93ad33b6035
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173553"
---
# <a name="terraform-cloud-shell-development"></a>Terraform Cloud Shell-ontwikkeling 

Terraform werkt goed vanaf een Bash-opdrachtregel zoals macOS Terminal of Bash op Windows of Linux. Wanneer u uw Terraform-configuraties uitvoert in de Bash-ervaring van de [Azure Cloud Shell](/azure/cloud-shell/overview), profiteert u van een aantal unieke voordelen om de ontwikkelingscyclus te versnellen.

In dit conceptartikel leest u meer over Cloud Shell-functies waarmee u Terraform-scripts kunt schrijven die naar Azure worden geïmplementeerd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatische configuratie van referenties

Terraform wordt geïnstalleerd en is direct beschikbaar in Cloud Shell. Terraform-scripts worden geverifieerd bij Azure wanneer u zich aanmeldt bij Cloud Shell om de infrastructuur te beheren zonder extra configuratie. Met automatische verificatie hoeft u niet langer handmatig een Directory-service-principal te maken en variabelen voor de Azure Terraform-provider te configureren.


## <a name="using-modules-and-providers"></a>Modules en providers gebruiken

Azure Terraform-modules hebben referenties nodig om toegang te krijgen tot en wijzigingen aan te brengen in de resources in uw Azure-abonnement. Wanneer u in de Cloud Shell werkt, moet u de volgende code aan uw scripts toevoegen om Azure Terraform-modules in de Cloud Shell te kunnen gebruik:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

De Cloud Shell geeft vereiste waarden voor de `azurerm`-provider door via omgevingsvariabelen wanneer een van de CLI-opdrachten van `terraform` wordt gebruikt.

## <a name="other-cloud-shell-developer-tools"></a>Andere hulpmiddelen voor Cloud Shell-ontwikkelaars

Bestand en shellstatussen blijven bewaard in Azure Storage tussen verschillende Cloud Shell-sessies. Gebruik [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) om bestanden te kopiëren en naar de Cloud Shell te uploaden vanaf uw lokale computer.

De Azure CLI is beschikbaar in de Cloud Shell; dit is een uitstekend hulpmiddel om configuraties te testen en uw werk te controleren zodra `terraform apply` of `terraform destroy` is voltooid.


## <a name="next-steps"></a>Volgende stappen

[Een klein VM-cluster maken met behulp van Module Registry](terraform-create-vm-cluster-module.md)
[Een klein VM-cluster maken met behulp van aangepaste HCL](terraform-create-vm-cluster-with-infrastructure.md)
