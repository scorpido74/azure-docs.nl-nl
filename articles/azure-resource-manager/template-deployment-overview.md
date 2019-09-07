---
title: Azure Resource Manager-sjablonen
description: Hierin wordt beschreven hoe u Azure Resource Manager sjablonen gebruikt voor het implementeren van resources.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4f273a04322246a2b4112c0b5b8a062732bab555
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390710"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Dankzij de overstap naar de Cloud hebben veel teams flexibele ontwikkelings methoden aangenomen. Deze teams lopen snel uit. Ze moeten hun oplossingen herhaaldelijk en betrouwbaar implementeren in de Cloud. De verdeling tussen bewerkingen en ontwikkeling is verdwenen naarmate teams de infra structuur en bron code in één proces moeten beheren.

Een oplossing voor deze uitdagingen is het automatiseren van de implementatie en het gebruik van de infra structuur als code. U gebruikt code om te definiëren welke moet worden geïmplementeerd en u kunt deze code beheren via hetzelfde proces als de bron code. U slaat de infra structuur op als code in een bron opslagplaats en-versie.

Met Azure Resource Manager kunt u de infra structuur als code implementeren via Resource Manager-sjablonen. De sjabloon is een JavaScript Object Notation (JSON)-bestand dat de infra structuur en configuratie voor uw Azure-oplossing bevat. De sjabloon maakt gebruik van declaratieve syntaxis, waarmee u kunt aangeven wat u wilt implementeren zonder dat u de volg orde van de programmeer opdrachten hoeft te schrijven om deze te maken. In de sjabloon geeft u de resources op die u wilt implementeren en de eigenschappen voor deze resources.

## <a name="benefits-of-resource-manager-templates"></a>Voor delen van Resource Manager-sjablonen

Resource Manager-sjablonen bieden verschillende voor delen. U kunt:

* U kunt alle resources voor uw oplossing implementeren, beheren en bewaken als groep, in plaats van deze resources afzonderlijk te verwerken.

* Implementeer uw oplossing herhaaldelijk in de ontwikkelings levenscyclus en laat de betrouw baarheid van uw resources in een consistente staat worden geïmplementeerd.

* Uw infra structuur beheren via declaratieve sjablonen in plaats van scripts.

* Definieer de afhankelijkheden tussen resources, zodat deze in de juiste volg orde worden geïmplementeerd.

* Maak onmiddellijk gebruik van nieuwe versies en Services omdat er geen tussenliggende werkzaamheden vereist zijn door andere partijen.

## <a name="template-file"></a>Sjabloon bestand

Binnen uw sjabloon kunt u [sjabloon expressies](template-expressions.md) schrijven waarmee de mogelijkheden van JSON worden uitgebreid. Deze expressies maken gebruik van de [functies](resource-group-template-functions.md) van Resource Manager.

De sjabloon heeft de volgende secties:

* [Para meters](template-parameters.md) : Geef waarden op tijdens de implementatie waarmee dezelfde sjabloon kan worden gebruikt voor verschillende omgevingen.

* [Variabelen](template-variables.md) : Geef waarden op die in uw sjablonen worden gebruikt.

* Door de [gebruiker gedefinieerde functies](template-user-defined-functions.md) : Maak aangepaste functies waarmee uw sjabloon wordt vereenvoudigd.

* [Resources](resource-group-authoring-templates.md#resources) : Geef de resources op die u wilt implementeren.

* [Outputs](template-outputs.md) : retourneert waarden van de geïmplementeerde resources.

## <a name="dependencies"></a>Afhankelijkheden

Azure Resource Manager analyseert afhankelijkheden om ervoor te zorgen dat de resources in de juiste volgorde worden gemaakt. Als een resource afhankelijk is van een waarde uit een andere resource (zoals een virtuele machine die een opslagaccount nodig heeft voor schijven), stelt u een afhankelijkheid in. Zie voor meer informatie [Afhankelijkheden definiëren in Azure Resource Manager-sjablonen](resource-group-define-dependencies.md).

## <a name="conditional-deployment"></a>Voorwaardelijke implementatie

U kunt een resource toevoegen aan uw sjabloon en optioneel implementeren. Normaal gesp roken geeft u een parameter waarde door die aangeeft of de resource moet worden geïmplementeerd. Zie [voorwaardelijke implementatie in Resource Manager-sjablonen](conditional-resource-deployment.md)voor meer informatie.

## <a name="create-multiple-instances"></a>Meerdere exemplaren maken

In plaats van herhalende blokken JSON in uw sjabloon, kunt u een copy-element gebruiken om meer dan één exemplaar van een variabele, eigenschap of resource op te geven. Zie voor meer informatie [resource, eigenschap of variabele herhaling in azure Resource Manager sjablonen](resource-group-create-multiple.md).

## <a name="export-templates"></a>Sjablonen exporteren

U kunt een sjabloon voor een bestaande resource groep ophalen door de huidige status van de resource groep te exporteren of door de sjabloon die wordt gebruikt voor een bepaalde implementatie weer te geven. Raadplegen van de [geëxporteerde sjabloon](export-template-portal.md) is een handige manier om de syntaxis van de sjabloon te leren kennen.

Wanneer u een oplossing vanaf de portal maakt, bevat de oplossing automatisch een sjabloon voor de implementatie. U hoeft de sjabloon niet helemaal zelf te maken. U kunt beginnen met de sjabloon voor uw oplossing en deze aanpassen aan uw specifieke behoeften. Ga voor een voorbeeld naar [Snelstartgids: Azure Resource Manager-sjablonen maken en implementeren via Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="template-deployment-process"></a>Sjabloonimlementatie proces

Wanneer u een sjabloon implementeert, parseert Resource Manager de sjabloon en zet de syntaxis om in REST API bewerkingen. Bijvoorbeeld wanneer Resource Manager een sjabloon ontvangt met de volgende resourcedefinitie:

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

Als de resource al bestaat in de resource groep en de aanvraag geen updates voor de eigenschappen bevat, wordt er geen actie ondernomen. Als de resource bestaat, maar de eigenschappen zijn gewijzigd, wordt de bestaande resource bijgewerkt. Als de resource niet bestaat, wordt de nieuwe resource gemaakt. Met deze methode kunt u een sjabloon op een veilige manier opnieuw implementeren en weet u zeker dat de resources een consistente status hebben.

## <a name="template-design"></a>Sjabloon ontwerp

U kunt helemaal zelf bepalen hoe u sjablonen en resourcegroepen definieert en hoe u de oplossing beheert. U kunt de toepassing met drie lagen bijvoorbeeld met een enkele sjabloon implementeren in een enkele resourcegroep.

![sjabloon met drie lagen](./media/template-deployment-overview/3-tier-template.png)

Maar u hoeft uw volledige infrastructuur niet te definiëren in één sjabloon. Vaak is het handiger om uw implementatievereisten te verdelen over een aantal gerichte sjablonen met een specifiek doel. U kunt deze sjablonen eenvoudig opnieuw gebruiken voor verschillende oplossingen. Voor het implementeren van een bepaalde oplossing kunt u een basissjabloon gebruiken die is gekoppeld aan alle vereiste sjablonen. In de volgende afbeelding ziet u hoe u een oplossing met drie lagen kunt implementeren via een bovenliggende sjabloon die drie geneste sjablonen bevat.

![sjabloon met geneste lagen](./media/template-deployment-overview/nested-tiers-template.png)

De lagen hebben afzonderlijke levenscycli, zodat u ze kunt toepassen op verschillende resourcegroepen. Opmerking: de resources kunnen nog steeds worden gekoppeld aan resources in andere resourcegroepen.

![sjabloon met lagen](./media/template-deployment-overview/tier-templates.png)

Zie [Using linked templates with Azure Resource Manager](resource-group-linked-templates.md) (Gekoppelde sjablonen gebruiken met Azure Resource Manager) voor meer informatie over geneste sjablonen.

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](resource-group-authoring-templates.md)voor meer informatie over de eigenschappen in sjabloon bestanden.
* Zie [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](resource-manager-tools-vs-code.md)om aan de slag te gaan met het ontwikkelen van een sjabloon.


