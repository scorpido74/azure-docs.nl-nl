---
title: Zelf studie-Azure Cloud Shell configureren voor terraform
description: Gebruik Terraform met Azure Cloud Shell om verificatie en configuratie van sjablonen te vereenvoudigen.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: db9edfadbe01edc1ee9df09c284e3895ee11f3d3
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159138"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Zelf studie: Azure Cloud Shell configureren voor terraform

Terraform werkt goed vanuit een bash-opdracht regel in macOS, Windows of Linux. Het uitvoeren van uw terraform-configuraties in de bash-ervaring van [Azure Cloud shell](/azure/cloud-shell/overview) heeft enkele unieke voor delen. In deze zelf studie ziet u hoe u terraform-scripts schrijft die in Azure worden geïmplementeerd met behulp van Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatische configuratie van referenties

Terraform wordt geïnstalleerd en is direct beschikbaar in Cloud Shell. Terraform-scripts worden geverifieerd met Azure wanneer ze zijn aangemeld bij Cloud Shell om de infra structuur te beheren zonder aanvullende configuratie. Automatische verificatie slaat twee hand matige processen over:
- Een Active Directory-Service-Principal maken
- De variabelen van de Azure terraform-provider configureren


## <a name="use-modules-and-providers"></a>Modules en providers gebruiken

Voor Azure terraform-modules zijn referenties vereist om Azure-resources te openen en te wijzigen. Als u terraform-modules in Cloud Shell wilt gebruiken, voegt u de volgende code toe:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Cloud Shell geeft de vereiste waarden voor de `azurerm` provider door middel van omgevings variabelen wanneer u een van de `terraform` CLI-opdrachten gebruikt.

## <a name="other-cloud-shell-developer-tools"></a>Andere hulpmiddelen voor Cloud Shell-ontwikkelaars

Bestand en shellstatussen blijven bewaard in Azure Storage tussen verschillende Cloud Shell-sessies. Gebruik [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) om bestanden te kopiëren en te uploaden naar Cloud shell vanaf uw lokale computer.

De Azure CLI is beschikbaar in Cloud Shell en is een uitstekend hulp programma voor het testen van configuraties en het controleren van uw werk nadat `terraform apply` of `terraform destroy` is voltooid.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een klein VM-cluster maken met behulp van het module register](terraform-create-vm-cluster-module.md)
