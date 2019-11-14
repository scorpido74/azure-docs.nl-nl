---
title: Overzicht van Azure Resource Manager sjablonen
description: Beschrijft de voor delen van het gebruik van Azure Resource Manager sjablonen voor het implementeren van resources.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: tomfitz
ms.openlocfilehash: 956450e5a47e4d0e16de44b6bc72c6cb24d05889
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074388"
---
# <a name="azure-resource-manager-templates-overview"></a>Overzicht van Azure Resource Manager sjablonen

Dankzij de overstap naar de Cloud hebben veel teams flexibele ontwikkelings methoden aangenomen. Deze teams lopen snel uit. Ze moeten herhaaldelijk hun oplossingen implementeren in de Cloud en weten dat hun infra structuur een betrouw bare status heeft. Omdat de infra structuur is opgenomen in het iteratieve proces, is de verdeling tussen bewerkingen en ontwikkeling verdwenen. Teams moeten de infra structuur en toepassings code beheren via een uniform proces.

Om aan deze uitdagingen te voldoen, kunt u implementaties automatiseren en de infra structuur als code gebruiken. In code definieert u de infra structuur die moet worden geïmplementeerd. De code van de infra structuur wordt onderdeel van het project. Net als bij de toepassings code slaat u de infrastructuur code op in een bron opslagplaats en maakt u er een versie van. Een van uw team leden kan de code uitvoeren en vergelijk bare omgevingen implementeren.

Gebruik Azure Resource Manager sjablonen om de infra structuur te implementeren als code voor uw Azure-oplossingen. De sjabloon is een JavaScript Object Notation-bestand (JSON) waarmee de infra structuur en configuratie voor uw project worden gedefinieerd. De sjabloon maakt gebruik van declaratieve syntaxis, waarmee u kunt aangeven wat u wilt implementeren zonder dat u de volg orde van de programmeer opdrachten hoeft te schrijven om deze te maken. In de sjabloon geeft u de resources op die u wilt implementeren en de eigenschappen voor deze resources.

## <a name="why-choose-resource-manager-templates"></a>Waarom Resource Manager-sjablonen kiezen?

Als u probeert te kiezen tussen het gebruik van Resource Manager-sjablonen en een van de andere infra structuur als code Services, moet u rekening houden met de volgende voor delen van het gebruik van sjablonen:

* **Declaratieve syntaxis**: Resource Manager-sjablonen bieden u de mogelijkheid om een volledige Azure-infra structuur declaratief te maken en te implementeren. U kunt bijvoorbeeld niet alleen virtuele machines implementeren, maar ook de netwerk infrastructuur, opslag systemen en andere bronnen die u nodig hebt.

* **Herhaal bare resultaten**: implementeer herhaaldelijk uw infra structuur in de ontwikkelings levenscyclus en laat de betrouw baarheid van uw resources op een consistente manier worden geïmplementeerd. Sjablonen zijn idempotent. Dit betekent dat u dezelfde sjabloon meerdere keren kunt implementeren en dezelfde resource typen in dezelfde status krijgt. U kunt één sjabloon ontwikkelen die de gewenste status aangeeft, in plaats van het ontwikkelen van veel afzonderlijke sjablonen om updates te vertegenwoordigen.

* Indeling **: u**hoeft zich geen zorgen te maken over de complexiteit van het ordenen van bewerkingen. Resource Manager coördineert de implementatie van onderling afhankelijke resources, zodat deze in de juiste volg orde worden gemaakt. Als dat mogelijk is, implementeert Resource Manager bronnen parallel, zodat uw implementaties sneller worden uitgevoerd dan seriële implementaties. U implementeert de sjabloon via één opdracht, in plaats van via meerdere verplichte opdrachten.

   ![Vergelijking Sjabloonimlementatie](./media/template-deployment-overview/template-processing.png)

* **Ingebouwde validatie**: uw sjabloon wordt pas geïmplementeerd nadat de validatie is door gegeven. Resource Manager controleert de sjabloon voordat de implementatie wordt gestart om te controleren of de implementatie slaagt. Uw implementatie is minder waarschijnlijk in een halve status.

* **Modulaire bestanden**: u kunt uw sjablonen opdelen in kleinere, herbruikbare onderdelen en deze samen voegen tijdens de implementatie. U kunt ook een sjabloon in andere sjablonen nesten.

* **Een Azure-resource maken**: u kunt direct nieuwe Azure-Services en-functies in sjablonen gebruiken. Zodra een resource provider nieuwe resources introduceert, kunt u deze resources implementeren via sjablonen. U hoeft niet te wachten totdat de hulpprogram ma's of modules zijn bijgewerkt voordat u de nieuwe services gebruikt.

* **Bijgehouden implementaties**: In het Azure Portal kunt u de implementatie geschiedenis bekijken en informatie ophalen over de sjabloon implementatie. U kunt de geïmplementeerde sjabloon zien, de parameter waarden die zijn door gegeven en uitvoer waarden. Andere infra structuur als code services worden niet bijgehouden via de portal.

   ![Implementatiegeschiedenis](./media/template-deployment-overview/deployment-history.png)

* **Beleid als code**: [Azure Policy](../governance/policy/overview.md) is een beleid als code raamwerk waarmee governance wordt geautomatiseerd. Als u Azure-beleid gebruikt, wordt het door voeren van beleid uitgevoerd op niet-compatibele resources wanneer deze via sjablonen worden geïmplementeerd.

* **Implementatie blauw drukken**: u kunt gebruikmaken van [blauw drukken](../governance/blueprints/overview.md) van micro soft om te voldoen aan de normen van regelgeving en naleving. Deze blauw drukken bevatten vooraf gemaakte sjablonen voor verschillende architecturen.

* **CI/cd-integratie**: u kunt sjablonen integreren in de hulpprogram ma's voor continue integratie en continue implementatie (CI/cd), waarmee u uw release pijplijnen kunt automatiseren voor snelle en betrouw bare updates van toepassingen en infra structuur. Met Azure DevOps en Resource Manager-sjabloon taak kunt u Azure-pijp lijnen gebruiken om voortdurend Azure Resource Manager-sjabloon projecten te bouwen en implementeren. Voor meer informatie raadpleegt u [VS project met pijp lijnen](./vs-resource-groups-project-devops-pipelines.md) en [doorlopende integratie met Azure-pijp lijnen](./resource-manager-tutorial-use-azure-pipelines.md).

* **Exporteer bare code**: u kunt een sjabloon voor een bestaande resource groep ophalen door de huidige status van de resource groep te exporteren of door de sjabloon die wordt gebruikt voor een bepaalde implementatie weer te geven. Raadplegen van de [geëxporteerde sjabloon](export-template-portal.md) is een handige manier om de syntaxis van de sjabloon te leren kennen.

* **Hulp middelen voor ontwerpen**: u kunt sjablonen ontwerpen met [Visual Studio code](resource-manager-tools-vs-code.md) en de extensie van het sjabloon programma. U krijgt IntelliSense, syntaxis markering, in line Help en veel andere taal functies. Naast Visual Studio code kunt u ook [Visual Studio](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)gebruiken.

## <a name="template-file"></a>Sjabloon bestand

Binnen uw sjabloon kunt u [sjabloon expressies](template-expressions.md) schrijven waarmee de mogelijkheden van JSON worden uitgebreid. Deze expressies maken gebruik van de [functies](resource-group-template-functions.md) van Resource Manager.

De sjabloon heeft de volgende secties:

* [Para meters](template-parameters.md) : Geef waarden op tijdens de implementatie waarmee dezelfde sjabloon kan worden gebruikt voor verschillende omgevingen.

* [Variabelen](template-variables.md) : Definieer waarden die opnieuw worden gebruikt in uw sjablonen. Ze kunnen worden samengesteld op basis van parameter waarden.

* Door de [gebruiker gedefinieerde functies](template-user-defined-functions.md) : Maak aangepaste functies waarmee uw sjabloon wordt vereenvoudigd.

* [Resources](resource-group-authoring-templates.md#resources) : Geef de resources op die u wilt implementeren.

* [Outputs](template-outputs.md) : retourneert waarden van de geïmplementeerde resources.

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

* Zie [zelf studie: uw eerste Azure Resource Manager sjabloon maken en implementeren](template-tutorial-create-first-template.md)voor een stapsgewijze zelf studie waarin u door het proces van het maken van een sjabloon wordt geleid.
* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](resource-group-authoring-templates.md)voor meer informatie over de eigenschappen in sjabloon bestanden.
* Zie [Snelstartgids: Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md)voor meer informatie over het exporteren van sjablonen.
