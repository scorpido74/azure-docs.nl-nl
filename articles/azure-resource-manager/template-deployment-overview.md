---
title: Azure Resource Manager-sjablonen
description: Hierin wordt beschreven hoe u Azure Resource Manager sjablonen gebruikt voor het implementeren van resources.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802026"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Dankzij de overstap naar de Cloud hebben veel teams flexibele ontwikkelings methoden aangenomen. Deze teams lopen snel uit. Ze moeten herhaaldelijk hun oplossingen implementeren in de Cloud en weten dat hun infra structuur een betrouw bare status heeft. Omdat de infra structuur is opgenomen in het iteratieve proces, is de verdeling tussen bewerkingen en ontwikkeling verdwenen. Teams moeten de infra structuur en toepassings code beheren via een uniform proces.

Om aan deze uitdagingen te voldoen, kunt u implementaties automatiseren en de infra structuur als code gebruiken. In code definieert u de infra structuur die moet worden geïmplementeerd. De code van de infra structuur wordt onderdeel van het project. Net als bij de toepassings code slaat u de infrastructuur code op in een bron opslagplaats en maakt u er een versie van. Een van uw team leden kan de code uitvoeren en vergelijk bare omgevingen implementeren.

Gebruik Azure Resource Manager sjablonen om de infra structuur te implementeren als code voor uw Azure-oplossingen. De sjabloon is een JavaScript Object Notation-bestand (JSON) waarmee de infra structuur en configuratie voor uw project worden gedefinieerd. De sjabloon maakt gebruik van declaratieve syntaxis, waarmee u kunt aangeven wat u wilt implementeren zonder dat u de volg orde van de programmeer opdrachten hoeft te schrijven om deze te maken. In de sjabloon geeft u de resources op die u wilt implementeren en de eigenschappen voor deze resources.

## <a name="benefits-of-resource-manager-templates"></a>Voor delen van Resource Manager-sjablonen

Resource Manager-sjablonen bieden de volgende voor delen:

* U kunt alle resources voor uw oplossing implementeren, beheren en bewaken als groep, in plaats van deze resources afzonderlijk te verwerken.

* Implementeer uw oplossing herhaaldelijk in de ontwikkelings levenscyclus en laat de betrouw baarheid van uw resources in een consistente staat worden geïmplementeerd.

* Uw infra structuur beheren via declaratieve sjablonen in plaats van scripts.

Als u probeert te kiezen tussen het gebruik van Resource Manager-sjablonen of een van de andere infra structuur als code Services, moet u rekening houden met de volgende voor delen-sjablonen voor deze services:

* Nieuwe Azure-Services en-functies zijn onmiddellijk beschikbaar in sjablonen. Zodra een resource provider nieuwe resources introduceert, kunt u deze resources implementeren via sjablonen. Met een andere infra structuur als code Services moet u wachten tot derden interfaces voor de nieuwe bronnen implementeren.

* Sjabloon implementaties worden verwerkt door één inzending van de sjabloon, in plaats van via meerdere verplichte opdrachten. Resource Manager coördineert de implementatie van onderling afhankelijke resources, zodat deze in de juiste volg orde worden gemaakt. De sjabloon wordt geparseerd en de juiste volg orde voor de implementatie wordt bepaald op basis van verwijzingen tussen resources.

   ![Vergelijking Sjabloonimlementatie](./media/template-deployment-overview/template-processing.png)

* Sjabloon implementaties worden bijgehouden in de Azure Portal. U kunt de implementatie geschiedenis bekijken en informatie ophalen over de sjabloon implementatie. U kunt de geïmplementeerde sjabloon zien, de parameter waarden die zijn door gegeven en uitvoer waarden. Andere infra structuur als code services worden niet bijgehouden via de portal.

   ![Implementatiegeschiedenis](./media/template-deployment-overview/deployment-history.png)

* Sjabloon implementaties worden gevalideerd vóór de vlucht. Resource Manager controleert de sjabloon voordat de implementatie wordt gestart om te controleren of de implementatie slaagt. Uw implementatie is minder waarschijnlijk in een halve status.

* Als u Azure- [beleid](../governance/policy/overview.md)gebruikt, wordt het door voeren van beleid uitgevoerd op niet-compatibele resources wanneer deze via sjablonen worden geïmplementeerd.

* Micro soft biedt implementatie [blauw drukken](../governance/blueprints/overview.md) om te voldoen aan regelgevings-en nalevings normen. Deze blauw drukken bevatten vooraf gemaakte sjablonen voor verschillende architecturen.

## <a name="idempotent"></a>Idempotent

Idempotent betekent gewoon dat u dezelfde bewerkingen vaak kunt uitvoeren en dat hetzelfde resultaat ophaalt. Het implementeren van een resource manager-sjabloon is idempotent. U kunt dezelfde sjabloon meerdere keren implementeren en dezelfde resource typen in dezelfde staat ophalen. Dit concept is belang rijk omdat u consistente resultaten krijgt, ongeacht of u een sjabloon opnieuw implementeert voor een bestaande resource groep of een sjabloon implementeert voor een nieuwe resource groep.

Stel dat u drie resources hebt geïmplementeerd voor een resource groep en vervolgens besluit dat u een vierde resource moet toevoegen. In plaats van een nieuwe sjabloon te maken die alleen de nieuwe resource bevat, kunt u de vierde resource toevoegen aan uw bestaande sjabloon. Wanneer u de nieuwe sjabloon implementeert in de resource groep die al drie resources had, worden in Resource Manager de acties beschreven die moeten worden uitgevoerd.

Als de resource bestaat in de resource groep en de aanvraag geen updates voor de eigenschappen bevat, wordt geen actie ondernomen. Als de resource bestaat, maar de eigenschappen zijn gewijzigd, wordt de bestaande resource bijgewerkt. Als de resource niet bestaat, wordt de nieuwe resource gemaakt.

Wanneer de implementatie is voltooid, weet u zeker dat de resources altijd de verwachte status hebben.

## <a name="template-file"></a>Sjabloon bestand

Binnen uw sjabloon kunt u [sjabloon expressies](template-expressions.md) schrijven waarmee de mogelijkheden van JSON worden uitgebreid. Deze expressies maken gebruik van de [functies](resource-group-template-functions.md) van Resource Manager.

De sjabloon heeft de volgende secties:

* [Para meters](template-parameters.md) : Geef waarden op tijdens de implementatie waarmee dezelfde sjabloon kan worden gebruikt voor verschillende omgevingen.

* [Variabelen](template-variables.md) : Definieer waarden die opnieuw worden gebruikt in uw sjablonen. Ze kunnen worden samengesteld op basis van parameter waarden.

* Door de [gebruiker gedefinieerde functies](template-user-defined-functions.md) : Maak aangepaste functies waarmee uw sjabloon wordt vereenvoudigd.

* [Resources](resource-group-authoring-templates.md#resources) : Geef de resources op die u wilt implementeren.

* [Outputs](template-outputs.md) : retourneert waarden van de geïmplementeerde resources.

## <a name="template-features"></a>Sjabloon functies

Resource Manager analyseert afhankelijkheden om ervoor te zorgen dat resources in de juiste volg orde worden gemaakt. De meeste afhankelijkheden worden impliciet bepaald. U kunt echter expliciet een afhankelijkheid instellen om ervoor te zorgen dat één resource wordt geïmplementeerd vóór een andere bron. Zie voor meer informatie [Afhankelijkheden definiëren in Azure Resource Manager-sjablonen](resource-group-define-dependencies.md).

U kunt een resource toevoegen aan uw sjabloon en optioneel implementeren. Normaal gesp roken geeft u een parameter waarde door die aangeeft of de resource moet worden geïmplementeerd. Zie [voorwaardelijke implementatie in Resource Manager-sjablonen](conditional-resource-deployment.md)voor meer informatie.

In plaats van herhalende blokken JSON in uw sjabloon, kunt u een copy-element gebruiken om meer dan één exemplaar van een variabele, eigenschap of resource op te geven. Zie voor meer informatie [resource, eigenschap of variabele herhaling in azure Resource Manager sjablonen](resource-group-create-multiple.md).

## <a name="export-templates"></a>Sjablonen exporteren

U kunt een sjabloon voor een bestaande resource groep ophalen door de huidige status van de resource groep te exporteren of door de sjabloon die wordt gebruikt voor een bepaalde implementatie weer te geven. Raadplegen van de [geëxporteerde sjabloon](export-template-portal.md) is een handige manier om de syntaxis van de sjabloon te leren kennen.

Wanneer u een oplossing vanaf de portal maakt, bevat de oplossing automatisch een sjabloon voor de implementatie. U hoeft de sjabloon niet helemaal zelf te maken. U kunt beginnen met de sjabloon voor uw oplossing en deze aanpassen aan uw specifieke behoeften. Ga voor een voorbeeld naar [Snelstartgids: Azure Resource Manager-sjablonen maken en implementeren via Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="template-deployment-process"></a>Sjabloonimlementatie proces

Wanneer u een sjabloon implementeert, zet Resource Manager de sjabloon om in REST API bewerkingen. Bijvoorbeeld wanneer Resource Manager een sjabloon ontvangt met de volgende resourcedefinitie:

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
* Zie [Azure Resource Manager Overview](resource-group-overview.md)voor een inleiding tot de Resource Manager-service, met inbegrip van de beheer functies.

