---
title: Overzicht van sjablonen
description: Beschrijft de voordelen met Azure Resource Manager-sjablonen voor de implementatie van resources.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 02602b4d12ae4333c88b352e4c13923d67f2c591
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885732"
---
# <a name="what-are-arm-templates"></a>Wat zijn ARM-sjablonen?

Met de overstap naar de cloud hebben veel teams agile ontwikkelmethoden gebruikt. Deze teams herhalen snel. Ze moeten hun oplossingen herhaaldelijk implementeren in de cloud en weten dat hun infrastructuur in een betrouwbare staat verkeert. Nu de infrastructuur onderdeel is geworden van het iteratieve proces, is de scheiding tussen bedrijfsvoering en ontwikkeling verdwenen. Teams moeten infrastructuur en toepassingscode beheren via een uniform proces.

Om deze uitdagingen het hoofd te bieden, u implementaties automatiseren en de praktijk van infrastructuur als code gebruiken. In code definieert u de infrastructuur die moet worden geïmplementeerd. De infrastructuurcode wordt onderdeel van uw project. Net als toepassingscode slaat u de infrastructuurcode op in een bronopslagplaats en versie deze. Iedereen in uw team kan de code uitvoeren en vergelijkbare omgevingen implementeren.

Als u infrastructuur wilt implementeren als code voor uw Azure-oplossingen, gebruikt u Azure Resource Manager-sjablonen (ARM). De sjabloon is een JSON-bestand (JavaScript Object Notation) dat de infrastructuur en configuratie voor uw project definieert. De sjabloon maakt gebruik van declaratieve syntaxis, waarmee u aangeven wat u wilt implementeren zonder dat u de volgorde van programmeeropdrachten hoeft te schrijven om deze te maken. In de sjabloon geeft u de resources op die moeten worden geïmplementeerd en de eigenschappen voor deze resources.

## <a name="why-choose-arm-templates"></a>Waarom kiezen voor ARM-sjablonen?

Als u probeert te kiezen tussen het gebruik van ARM-sjablonen en een van de andere infrastructuur als codeservices, moet u rekening houden met de volgende voordelen van het gebruik van sjablonen:

* **Declaratieve syntaxis:** met ARM-sjablonen u een volledige Azure-infrastructuur declaratief maken en implementeren. U bijvoorbeeld niet alleen virtuele machines implementeren, maar ook de netwerkinfrastructuur, opslagsystemen en alle andere resources die u nodig heeft.

* **Herhaalbare resultaten:** implementeer herhaaldelijk uw infrastructuur gedurende de hele ontwikkelingslevenscyclus en heb er vertrouwen in dat uw resources op een consistente manier worden geïmplementeerd. Sjablonen zijn idempotent, wat betekent dat u dezelfde sjabloon vele malen implementeren en dezelfde resourcetypen in dezelfde status krijgen. U één sjabloon ontwikkelen die de gewenste status vertegenwoordigt, in plaats van veel afzonderlijke sjablonen te ontwikkelen om updates weer te geven.

* **Orchestration**: U hoeft zich geen zorgen te maken over de complexiteit van het bestellen van bewerkingen. Resourcemanager orkestreert de implementatie van onderling afhankelijke resources, zodat ze in de juiste volgorde worden gemaakt. Indien mogelijk implementeert Resource Manager resources parallel, zodat uw implementaties sneller verlopen dan seriële implementaties. U implementeert de sjabloon via één opdracht, in plaats van via meerdere dwingende opdrachten.

   ![Vergelijking van sjabloonimplementatie](./media/overview/template-processing.png)

* **Modulaire bestanden:** U uw sjablonen opsplitsen in kleinere, herbruikbare componenten en ze koppelen tijdens de implementatie. U ook een sjabloon in een andere sjabloon nesten.

* **Maak een Azure-bron:** u meteen nieuwe Azure-services en -functies gebruiken in sjablonen. Zodra een resourceprovider nieuwe bronnen introduceert, u deze bronnen implementeren via sjablonen. U hoeft niet te wachten tot gereedschappen of modules worden bijgewerkt voordat u de nieuwe services gebruikt.

* **Uitbreidbaarheid:** met [implementatiescripts](deployment-script-template.md)u PowerShell- of Bash-scripts toevoegen aan uw sjablonen. De implementatiescripts vergroten uw mogelijkheid om resources in te stellen tijdens de implementatie. Een script kan worden opgenomen in de sjabloon, of worden opgeslagen in een externe bron en waarnaar wordt verwezen in de sjabloon. Implementatiescripts bieden u de mogelijkheid om uw end-to-end-omgevingsetup in één ARM-sjabloon te voltooien.

* **Testen:** U ervoor zorgen dat uw sjabloon de aanbevolen richtlijnen volgt door deze te testen met de ARM-sjabloongereedschapsset (arm-ttk). Deze testkit is een PowerShell-script dat u downloaden van [GitHub.](https://github.com/Azure/arm-ttk) De toolkit maakt het makkelijker voor u om expertise te ontwikkelen met behulp van de sjabloontaal.

* **Voorbeeldwijzigingen**: U de [wat-als-bewerking](template-deploy-what-if.md) gebruiken om een voorbeeld van wijzigingen te krijgen voordat u de sjabloon implementeert. Met wat-als ziet u welke resources worden gemaakt, bijgewerkt of verwijderd, en welke resourceeigenschappen worden gewijzigd. De wat-als-bewerking controleert de huidige status van uw omgeving en elimineert de noodzaak om de status te beheren.

* **Ingebouwde validatie:** uw sjabloon wordt pas geïmplementeerd na het passeren van validatie. Resourcemanager controleert de sjabloon voordat de implementatie wordt gestart om te controleren of de implementatie slaagt. Uw implementatie is minder waarschijnlijk te stoppen in een half-afgewerkte staat.

* **Bijgehouden implementaties**: In de Azure-portal u de implementatiegeschiedenis bekijken en informatie krijgen over de sjabloonimplementatie. U de sjabloon zien die is geïmplementeerd, de parameterwaarden die zijn doorgegeven en eventuele uitvoerwaarden. Andere infrastructuur als codeservices worden niet bijgehouden via de portal.

   ![Implementatiegeschiedenis](./media/overview/deployment-history.png)

* **Beleid als code**: [Azure Policy](../../governance/policy/overview.md) is een beleid als codeframework om governance te automatiseren. Als u Azure-beleid gebruikt, wordt beleidsherstel uitgevoerd op niet-compatibele resources wanneer deze worden geïmplementeerd via sjablonen.

* **Blueprints voor implementatie:** u profiteren van [blauwdrukken](../../governance/blueprints/overview.md) die door Microsoft zijn geleverd om te voldoen aan de wettelijke en nalevingsnormen. Deze blauwdrukken bevatten vooraf gebouwde sjablonen voor verschillende architecturen.

* **CI/CD-integratie**: U sjablonen integreren in uw tools voor continue integratie en continue implementatie (CI/CD), die uw releasepijplijnen kunnen automatiseren voor snelle en betrouwbare applicatie- en infrastructuurupdates. Door azure devOps en resourcebeheersjabloontaak te gebruiken, u Azure Pipelines gebruiken om ARM-sjabloonprojecten continu te bouwen en te implementeren. Zie [VS-project met pijplijnen](add-template-to-azure-pipelines.md) en [continue integratie met Azure Pipelines](template-tutorial-use-azure-pipelines.md)voor meer informatie.

* **Exporteerbare code:** u een sjabloon voor een bestaande resourcegroep krijgen door de huidige status van de resourcegroep te exporteren of de sjabloon te bekijken die voor een bepaalde implementatie wordt gebruikt. Raadplegen van de [geëxporteerde sjabloon](export-template-portal.md) is een handige manier om de syntaxis van de sjabloon te leren kennen.

* **Ontwerptools**: U sjablonen maken met [Visual Studio Code](use-vs-code-to-create-template.md) en de extensie sjabloongereedschap. U krijgt intellisense, syntaxismarkering, in-line help en vele andere taalfuncties. Naast Visual Studio-code u ook [Visual Studio](create-visual-studio-deployment-project.md)gebruiken.

## <a name="template-file"></a>Sjabloonbestand

Binnen uw sjabloon u [sjabloonexpressies](template-expressions.md) schrijven die de mogelijkheden van JSON uitbreiden. Deze expressies maken gebruik van de [functies](template-functions.md) van Resource Manager.

De sjabloon heeft de volgende secties:

* [Parameters](template-parameters.md) : geef waarden op tijdens de implementatie waarmee dezelfde sjabloon met verschillende omgevingen kan worden gebruikt.

* [Variabelen](template-variables.md) : definieer waarden die opnieuw worden gebruikt in uw sjablonen. Ze kunnen worden opgebouwd uit parameterwaarden.

* [Door de gebruiker gedefinieerde functies](template-user-defined-functions.md) - Maak aangepaste functies die uw sjabloon vereenvoudigen.

* [Resources](template-syntax.md#resources) - Geef de resources op die moeten worden geïmplementeerd.

* Uitvoer - Waarden [retourneren](template-outputs.md) van de geïmplementeerde resources.

## <a name="template-deployment-process"></a>Implementatieproces voor sjablonen

Wanneer u een sjabloon implementeert, converteert Resource Manager de sjabloon in REST API-bewerkingen. Bijvoorbeeld wanneer Resource Manager een sjabloon ontvangt met de volgende resourcedefinitie:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
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
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>Sjabloonontwerp

U kunt helemaal zelf bepalen hoe u sjablonen en resourcegroepen definieert en hoe u de oplossing beheert. U kunt de toepassing met drie lagen bijvoorbeeld met een enkele sjabloon implementeren in een enkele resourcegroep.

![sjabloon met drie lagen](./media/overview/3-tier-template.png)

Maar u hoeft uw volledige infrastructuur niet te definiëren in één sjabloon. Vaak is het handiger om uw implementatievereisten te verdelen over een aantal gerichte sjablonen met een specifiek doel. U kunt deze sjablonen eenvoudig opnieuw gebruiken voor verschillende oplossingen. Voor het implementeren van een bepaalde oplossing kunt u een basissjabloon gebruiken die is gekoppeld aan alle vereiste sjablonen. In de volgende afbeelding ziet u hoe u een oplossing met drie lagen kunt implementeren via een bovenliggende sjabloon die drie geneste sjablonen bevat.

![sjabloon met geneste lagen](./media/overview/nested-tiers-template.png)

De lagen hebben afzonderlijke levenscycli, zodat u ze kunt toepassen op verschillende resourcegroepen. Opmerking: de resources kunnen nog steeds worden gekoppeld aan resources in andere resourcegroepen.

![sjabloon met lagen](./media/overview/tier-templates.png)

Zie [Using linked templates with Azure Resource Manager](linked-templates.md) (Gekoppelde sjablonen gebruiken met Azure Resource Manager) voor meer informatie over geneste sjablonen.

## <a name="next-steps"></a>Volgende stappen

* Zie [Zelfstudie: Uw eerste ARM-sjabloon maken en implementeren](template-tutorial-create-first-template.md)voor een stapsgewijze zelfstudie die u door het proces van het maken van een sjabloon leidt.
* Zie [De structuur en syntaxis van ARM-sjablonen begrijpen](template-syntax.md)voor informatie over de eigenschappen in sjabloonbestanden.
* Zie [Snelstart: ARM-sjablonen maken en implementeren met behulp van de Azure-portal](quickstart-create-templates-use-the-portal.md)voor meer informatie over het exporteren van sjablonen.
