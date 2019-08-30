---
title: Azure Resource Manager-sjablonen
description: Hierin wordt beschreven hoe u Azure Resource Manager sjablonen gebruikt voor het implementeren van resources.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 95c127b3a7c9c47c96b292066bf1597a02896806
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166522"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Met Azure Resource Manager kunt u sjablonen maken die bepalen wat u wilt implementeren in Azure. De sjabloon is een JavaScript Object Notation (JSON)-bestand dat de infra structuur en configuratie van uw Azure-oplossing bevat. Door het gebruik van een sjabloon kunt u gedurende de levenscyclus de oplossing herhaaldelijk implementeren en erop vertrouwen dat uw resources consistent worden geïmplementeerd.

De sjabloon maakt gebruik van declaratieve syntaxis, waarmee u kunt aangeven wat u wilt implementeren zonder dat u de volg orde van de programmeer opdrachten hoeft te schrijven om deze te maken. In de sjabloon geeft u de resources op die u wilt implementeren en de eigenschappen voor deze resources.

## <a name="benefits-of-resource-manager-templates"></a>Voor delen van Resource Manager-sjablonen

Resource Manager-sjablonen bieden diverse voor delen:

* U kunt alle resources voor uw oplossing implementeren, beheren en bewaken als groep, in plaats van deze resources afzonderlijk te verwerken.

* U kunt gedurende de ontwikkelingscyclus uw oplossing herhaaldelijk implementeren en erop vertrouwen dat uw resources consistent worden geïmplementeerd.

* U kunt uw infrastructuur beheren via declaratieve sjablonen in plaats van scripts.

* U kunt de afhankelijkheden tussen resources zo definiëren dat deze in de juiste volgorde worden geïmplementeerd.

Met de volgende tips kunt u profiteren van alle mogelijkheden die Resource Manager voor uw oplossingen biedt.

* Definieer en implementeer uw infrastructuur via de declaratieve syntaxis in de Resource Manager-sjablonen, in plaats van via imperatieve opdrachten.

* Definieer alle implementatie- en configuratiestappen in de sjabloon. Handmatige stappen voor het installeren van uw oplossing zijn niet nodig.

* Voer imperatieve opdrachten uit voor het beheren van uw resources, zoals het starten of stoppen van een app of machine.

* Volg de [Aanbevolen procedures voor Azure Resource Manager sjablonen](template-best-practices.md).

## <a name="template-deployment-process"></a>Sjabloonimlementatie proces

Wanneer u een sjabloon implementeert, parseert Resource Manager de sjabloon en zet de syntaxis om in REST API bewerkingen. Deze bewerkingen worden verzonden naar de juiste resource providers. Bijvoorbeeld wanneer Resource Manager een sjabloon ontvangt met de volgende resourcedefinitie:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

De definitie wordt geconverteerd naar de volgende REST API-bewerking, die wordt verzonden naar de Microsoft.Storage-resourceprovider:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-structure"></a>Sjabloonstructuur

U kunt helemaal zelf bepalen hoe u sjablonen en resourcegroepen definieert en hoe u de oplossing beheert. U kunt de toepassing met drie lagen bijvoorbeeld met een enkele sjabloon implementeren in een enkele resourcegroep.

![sjabloon met drie lagen](./media/resource-group-overview/3-tier-template.png)

Maar u hoeft uw volledige infrastructuur niet te definiëren in één sjabloon. Vaak is het handiger om uw implementatievereisten te verdelen over een aantal gerichte sjablonen met een specifiek doel. U kunt deze sjablonen eenvoudig opnieuw gebruiken voor verschillende oplossingen. Voor het implementeren van een bepaalde oplossing kunt u een basissjabloon gebruiken die is gekoppeld aan alle vereiste sjablonen. In de volgende afbeelding ziet u hoe u een oplossing met drie lagen kunt implementeren via een bovenliggende sjabloon die drie geneste sjablonen bevat.

![sjabloon met geneste lagen](./media/resource-group-overview/nested-tiers-template.png)

De lagen hebben afzonderlijke levenscycli, zodat u ze kunt toepassen op verschillende resourcegroepen. Opmerking: de resources kunnen nog steeds worden gekoppeld aan resources in andere resourcegroepen.

![sjabloon met lagen](./media/resource-group-overview/tier-templates.png)

Zie [Using linked templates with Azure Resource Manager](resource-group-linked-templates.md) (Gekoppelde sjablonen gebruiken met Azure Resource Manager) voor meer informatie over geneste sjablonen.

Azure Resource Manager analyseert afhankelijkheden om ervoor te zorgen dat de resources in de juiste volgorde worden gemaakt. Als een resource afhankelijk is van een waarde uit een andere resource (zoals een virtuele machine die een opslagaccount nodig heeft voor schijven), stelt u een afhankelijkheid in. Zie voor meer informatie [Afhankelijkheden definiëren in Azure Resource Manager-sjablonen](resource-group-define-dependencies.md).

U kunt de sjabloon ook gebruiken voor updates aan de infrastructuur. U kunt bijvoorbeeld een resource toevoegen aan uw oplossing en configuratieregels toevoegen voor de resources die al zijn geïmplementeerd. Als de sjabloon een resource definieert die al bestaat, werkt Resource Manager de bestaande resource bij in plaats van een nieuwe te maken.

Resource Manager biedt uitbreidingen voor scenario's waarin aanvullende bewerkingen moeten worden uitgevoerd, zoals het installeren van bepaalde software die niet is opgenomen in de installatie. Als u al een configuratiebeheerservice gebruikt, zoals DSC, Chef of Puppet, kunt u via uitbreidingen met deze service blijven werken. Zie voor meer informatie over de extensies van virtuele machines [Informatie over extensies en functies van virtuele machines](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Wanneer u een oplossing vanaf de portal maakt, bevat de oplossing automatisch een sjabloon voor de implementatie. U hoeft de sjabloon niet helemaal zelf te maken. U kunt beginnen met de sjabloon voor uw oplossing en deze aanpassen aan uw specifieke behoeften. Ga voor een voorbeeld naar [Snelstartgids: Azure Resource Manager-sjablonen maken en implementeren via Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md). U kunt ook een sjabloon voor een bestaande resourcegroep ophalen door de huidige status van de resourcegroep te exporteren of door de sjabloon die is gebruikt voor een bepaalde implementatie weer te geven. Raadplegen van de [geëxporteerde sjabloon](./manage-resource-groups-portal.md#export-resource-groups-to-templates) is een handige manier om de syntaxis van de sjabloon te leren kennen.

De sjabloon wordt uiteindelijk onderdeel van de broncode van uw app. U kunt de broncode inchecken in uw broncodeopslag en deze bijwerken naarmate uw app zich verder ontwikkelt. U kunt de sjabloon bewerken met Visual Studio.

## <a name="next-steps"></a>Volgende stappen

Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](resource-group-authoring-templates.md)voor meer informatie over sjabloon bestanden.

Nadat u de sjabloon hebt gedefinieerd, kunt u de resources in Azure implementeren. Zie voor het implementeren van de resources:

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](resource-group-template-deploy.md)
* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](resource-group-template-deploy-cli.md)
* [Resources implementeren met Resource Manager-sjablonen en Azure Portal](resource-group-template-deploy-portal.md)
* [Resources implementeren met Resource Manager-sjablonen en Resource Manager REST API](resource-group-template-deploy-rest.md)

