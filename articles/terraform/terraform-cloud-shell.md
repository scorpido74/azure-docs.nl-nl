---
title: Zelf studie-Azure Cloud Shell configureren voor terraform
description: Gebruik Terraform met Azure Cloud Shell om verificatie en configuratie van sjablonen te vereenvoudigen.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 693ed462fb1ba3dfed079e8ae97152732c771253
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969584"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Zelf studie: Azure Cloud Shell configureren voor terraform

Terraform werkt goed vanuit een bash-opdracht regel in macOS, Windows of Linux. Het uitvoeren van uw terraform-configuraties in de bash-ervaring van de [Azure Cloud shell](/azure/cloud-shell/overview) heeft enkele unieke voor delen. Deze zelf studie laat zien hoe u terraform-scripts schrijft die met Cloud Shell worden geïmplementeerd in Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatische configuratie van referenties

Terraform wordt geïnstalleerd en is direct beschikbaar in Cloud Shell. Terraform-scripts worden geverifieerd bij Azure wanneer u zich aanmeldt bij Cloud Shell om de infrastructuur te beheren zonder extra configuratie. Automatische verificatie slaat twee hand matige processen over:
- Een service-principal voor Active Directory maken.
- de Azure terraform provider-variabelen worden geconfigureerd.


## <a name="using-modules-and-providers"></a>Modules en providers gebruiken

Voor Azure terraform-modules zijn referenties vereist om Azure-resources te openen en te wijzigen. Als u terraform-modules in Cloud Shell wilt gebruiken, voegt u de volgende code toe:


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

> [!div class="nextstepaction"]
> [Een klein VM-cluster maken met behulp van het module register](terraform-create-vm-cluster-module.md)