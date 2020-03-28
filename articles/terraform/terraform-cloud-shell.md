---
title: Zelfstudie - Azure Cloud Shell configureren voor Terraform
description: In deze zelfstudie gebruikt u Terraform met Azure Cloud Shell om verificatie en sjabloonconfiguratie te vereenvoudigen.
keywords: azure devops terraform cloud shell
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945338"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Zelfstudie: Azure Cloud Shell configureren voor Terraform

Terraform werkt goed vanuit een Bash-opdrachtregel in macOS, Windows of Linux. Het uitvoeren van uw Terraform-configuraties in de Bash-ervaring van [Azure Cloud Shell](/azure/cloud-shell/overview) heeft een aantal unieke voordelen. In deze zelfstudie ziet u hoe u Terraform-scripts schrijven die met Cloud Shell naar Azure worden geïmplementeerd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatische configuratie van referenties

Terraform wordt geïnstalleerd en is direct beschikbaar in Cloud Shell. Terraform-scripts verifiëren met Azure wanneer ze zijn aangemeld bij Cloud Shell om de infrastructuur te beheren zonder extra configuratie. Automatische verificatie omzeilt twee handmatige processen:
- Een active directory-serviceprincipal maken
- De azure terraform-providervariabelen configureren


## <a name="use-modules-and-providers"></a>Modules en providers gebruiken

Azure Terraform-modules vereisen referenties om Azure-bronnen te openen en te wijzigen. Als u Terraform-modules in Cloud Shell wilt gebruiken, voegt u de volgende code toe:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Cloud Shell geeft vereiste `azurerm` waarden voor de provider door `terraform` middel van omgevingsvariabelen wanneer u een van de CLI-opdrachten gebruikt.

## <a name="other-cloud-shell-developer-tools"></a>Andere hulpmiddelen voor Cloud Shell-ontwikkelaars

Bestand en shellstatussen blijven bewaard in Azure Storage tussen verschillende Cloud Shell-sessies. Gebruik [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) om bestanden vanaf uw lokale computer naar Cloud Shell te kopiëren en te uploaden.

De Azure CLI is beschikbaar in Cloud Shell en is een `terraform apply` geweldig `terraform destroy` hulpmiddel voor het testen van configuraties en het controleren van uw werk na of afwerking.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een klein VM-cluster maken met behulp van het moduleregister](terraform-create-vm-cluster-module.md)