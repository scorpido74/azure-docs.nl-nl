---
title: Snelstart - Een Azure Analysis Services-serverbron maken met azure resource manager-sjabloon
description: Snel beginnen met het weergeven van een Azure Analysis Services-serverbron met behulp van een Azure Resource Manager-sjabloon.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: analysis-services
ms.topic: quickstart
ms.date: 04/14/2020
ms.custom: subject-armqs
ms.openlocfilehash: d292500c5e26d3c07ff2402964166b3928cc7e44
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384255"
---
# <a name="quickstart-create-a-server---azure-resource-manager-template"></a>Snelstart: een server maken - sjabloon Azure Resource Manager

In deze snelstart wordt beschreven hoe u een Analysis Services-serverbron maakt in uw Azure-abonnement met behulp van een resourcemanagersjabloon.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: ga naar [gratis proefversie van Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) om een account te maken.
* **Azure Active Directory**: uw abonnement moet zijn gekoppeld aan een Azure Active Directory-tenant. En u moet zijn aangemeld bij Azure met een account in deze Azure Active Directory. Raadpleeg voor meer informatie [Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md).

## <a name="create-a-server"></a>Een server maken

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/101-analysis-services-create/)

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

In de sjabloon wordt één [Microsoft.AnalysisServices/servers-bron](https://docs.microsoft.com/azure/templates/microsoft.analysisservices/2017-08-01/servers) met een firewallregel gedefinieerd. 

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende koppeling Implementeren naar Azure om u aan te melden bij Azure en een sjabloon te openen. De sjabloon wordt gebruikt om een Analysis Services-serverbron te maken en vereiste en optionele eigenschappen op te geven.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json"><img src="./media/analysis-services-create-template/deploy-azure.png" alt="deploy to azure"/></a>

2. Typ of selecteer de volgende waarden.

    Tenzij anders aangegeven, gebruikt u standaardwaarden.

    * **Subscription**: selecteer een Azure-abonnement.
    * **Resourcegroep**: klik op **Nieuw maken**en voer een unieke naam in voor de nieuwe resourcegroep.
    * **Locatie:** selecteer een standaardlocatie voor resources die zijn gemaakt in de resourcegroep.
    * **Servernaam:** voer een naam in voor de serverbron. 
    * **Locatie**: Negeren voor analyseservices. Locatie is opgegeven in Serverlocatie.
    * **Serverlocatie:** voer de locatie van de analysis services-server in. Dit is vaak hetzelfde gebied als de standaardlocatie die is opgegeven voor de resourcegroep, maar niet vereist. Bijvoorbeeld **North Central US**. Zie beschikbaarheid van [analyseservices per regio voor](analysis-services-overview.md#availability-by-region)ondersteunde regio's.
    * **Sku-naam:** voer de sku-naam in voor de server Analysis Services die u wilt maken. Kies uit: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. De beschikbaarheid van Sku is afhankelijk van de regio. S0 of D1 wordt aanbevolen voor evaluatie en testen.
    * **Capaciteit:** voer het totale aantal queryreplica-scale-out-exemplaren in. Het uitschalen van meer dan één instantie wordt alleen in bepaalde regio's ondersteund.
    * **Firewall-instellingen:** voer binnenkomende firewallregels in om te definiëren voor de server. Als dit niet is opgegeven, is de firewall uitgeschakeld.
    * **Back-up blob container uri:** voer de SAS URI in op een privé Azure Blob Storage-container met lees-, schrijf- en lijstmachtigingen. Vereist alleen als u van plan bent [back-up/herstel](analysis-services-backup.md)te gebruiken.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: selecteer dit.

3. Selecteer **Aankoop**. Nadat de server is geïmplementeerd, ontvangt u een melding:

   ![Resource Manager-sjabloon, portalmelding implementeren](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>De implementatie valideren

Gebruik de Azure-portal of Azure PowerShell om te controleren of de brongroep en serverbron zijn gemaakt.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, gebruikt u de Azure-portal, Azure CLI of Azure PowerShell om de brongroep en de serverbron te verwijderen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een Azure Resource Manager-sjabloon gebruikt om een nieuwe brongroep en een Azure Analysis Services-serverbron te maken. Nadat u een serverbron hebt gemaakt met behulp van de sjabloon, u het volgende overwegen:
- [Snelstart: Een server maken - PowerShell](analysis-services-create-powershell.md)
- [Een voorbeeldmodel toevoegen via de portal](analysis-services-create-sample-model.md)
- [Serverbeheerder en gebruikersrollen configureren](tutorials/analysis-services-tutorial-roles.md)