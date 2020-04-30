---
title: 'Quick Start: een Azure Analysis Services server-resource maken met Azure Resource Manager sjabloon'
description: Quick Start laat zien hoe een Azure Analysis Services server-resource met behulp van een Azure Resource Manager sjabloon.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: analysis-services
ms.topic: quickstart
ms.date: 04/14/2020
ms.custom: subject-armqs
ms.openlocfilehash: d292500c5e26d3c07ff2402964166b3928cc7e44
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81384255"
---
# <a name="quickstart-create-a-server---azure-resource-manager-template"></a>Snelstartgids: een server Azure Resource Manager sjabloon maken

In deze Quick Start wordt beschreven hoe u een Analysis Services server-resource maakt in uw Azure-abonnement met behulp van een resource manager-sjabloon.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: ga naar [gratis proefversie van Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) om een account te maken.
* **Azure Active Directory**: uw abonnement moet zijn gekoppeld aan een Azure Active Directory-tenant. En u moet zijn aangemeld bij Azure met een account in deze Azure Active Directory. Raadpleeg voor meer informatie [Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md).

## <a name="create-a-server"></a>Een server maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://azure.microsoft.com/resources/templates/101-analysis-services-create/)start-sjablonen.

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

Er wordt één [micro soft. AnalysisServices/servers-](https://docs.microsoft.com/azure/templates/microsoft.analysisservices/2017-08-01/servers) resource met een firewall regel gedefinieerd in de sjabloon. 

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende implementeren naar Azure-koppeling om u aan te melden bij Azure en een sjabloon te openen. De sjabloon wordt gebruikt om een Analysis Services server-resource te maken en de vereiste en optionele eigenschappen op te geven.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json"><img src="./media/analysis-services-create-template/deploy-azure.png" alt="deploy to azure"/></a>

2. Typ of selecteer de volgende waarden.

    Tenzij anders aangegeven, gebruikt u de standaard waarden.

    * **Subscription**: selecteer een Azure-abonnement.
    * **Resource groep**: Klik op **nieuwe maken**en voer een unieke naam in voor de nieuwe resource groep.
    * **Locatie**: Selecteer een standaard locatie voor resources die zijn gemaakt in de resource groep.
    * **Server naam**: Voer een naam in voor de server resource. 
    * **Locatie**: negeren voor Analysis Services. De locatie is opgegeven op de server locatie.
    * **Server locatie**: Voer de locatie van de Analysis Services-server in. Dit is vaak dezelfde regio als de standaard locatie die voor de resource groep is opgegeven, maar niet vereist. Bijvoorbeeld **Noord-Centraal VS**. Zie [Analysis Services Beschik baarheid per regio](analysis-services-overview.md#availability-by-region)voor ondersteunde regio's.
    * **SKU-naam**: Voer de naam in van de SKU voor de Analysis Services-server die u wilt maken. Kies uit: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. SKU-Beschik baarheid is afhankelijk van de regio. S0 of D1 wordt aanbevolen voor evaluatie en testen.
    * **Capaciteit**: Voer het totale aantal scale-out-exemplaren van de query replica in. Uitschalen van meer dan één exemplaar wordt alleen ondersteund in geselecteerde regio's.
    * **Firewall instellingen**: Voer binnenkomende firewall regels in om voor de server te definiëren. Als dit niet is opgegeven, wordt de firewall uitgeschakeld.
    * **URI van back-Upblob-container**: Voer de SAS-URI naar een persoonlijke Azure-Blob Storage container met lees-, schrijf-en lijst machtigingen. Alleen vereist als u [back-up/herstel](analysis-services-backup.md)wilt gebruiken.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: selecteer dit.

3. Selecteer **Aankoop**. Nadat de server is geïmplementeerd, ontvangt u een melding:

   ![Resource Manager-sjabloon, portal melding implementeren](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>De implementatie valideren

Gebruik de Azure Portal of Azure PowerShell om te controleren of de resource groep en de server resource zijn gemaakt.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, gebruikt u de Azure Portal, Azure CLI of Azure PowerShell om de resource groep en de server resource te verwijderen.

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

In deze Quick Start hebt u een Azure Resource Manager sjabloon gebruikt om een nieuwe resource groep en een Azure Analysis Services server-resource te maken. Nadat u een server resource hebt gemaakt met behulp van de sjabloon, moet u rekening houden met het volgende:
- [Snelstart: Een server maken - PowerShell](analysis-services-create-powershell.md)
- [Een voorbeeldmodel toevoegen via de portal](analysis-services-create-sample-model.md)
- [Serverbeheerder en gebruikersrollen configureren](tutorials/analysis-services-tutorial-roles.md)