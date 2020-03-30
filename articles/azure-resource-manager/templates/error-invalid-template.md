---
title: Ongeldige sjabloonfouten
description: Beschrijft hoe u ongeldige sjabloonfouten oplossen bij het implementeren van Azure Resource Manager-sjablonen.
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.openlocfilehash: 65cd69d67933d117b51f37b587b276aec2bd635a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154054"
---
# <a name="resolve-errors-for-invalid-template"></a>Fouten met een ongeldige sjabloon oplossen

In dit artikel wordt beschreven hoe u ongeldige sjabloonfouten oplossen.

## <a name="symptom"></a>Symptoom

Bij het implementeren van een sjabloon ontvangt u een foutmelding dat aangeeft:

```
Code=InvalidTemplate
Message=<varies>
```

Het foutbericht is afhankelijk van het type fout.

## <a name="cause"></a>Oorzaak

Deze fout kan het gevolg zijn van verschillende soorten fouten. Er is meestal sprake van een syntaxis of structurele fout in de sjabloon.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Oplossing 1 - syntaxisfout

Als u een foutbericht ontvangt dat aangeeft dat de sjabloon is mislukt, hebt u mogelijk een syntaxisprobleem in uw sjabloon.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Deze fout is eenvoudig te maken omdat sjabloonexpressies ingewikkeld kunnen zijn. De volgende naamtoewijzing voor een opslagaccount bevat bijvoorbeeld één set haakjes, drie functies, drie sets haakjes, één set afzonderlijke aanhalingstekens en één eigenschap:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Als u de overeenkomende syntaxis niet verstrekt, produceert de sjabloon een waarde die anders is dan de bedoeling.

Wanneer u dit type fout ontvangt, controleert u zorgvuldig de syntaxis van expressie. Overweeg een JSON-editor te gebruiken, zoals [Visual Studio](create-visual-studio-deployment-project.md) of [Visual Studio Code,](use-vs-code-to-create-template.md)die u kan waarschuwen voor syntaxisfouten.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Oplossing 2 - onjuiste segmentlengtes

Een andere ongeldige sjabloonfout treedt op wanneer de resourcenaam niet in de juiste indeling is.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Een resource op basisniveau moet één segment minder in de naam hebben dan in het resourcetype. Elk segment wordt onderscheiden door een slash. In het volgende voorbeeld heeft het type twee segmenten en de naam één segment, dus het is een **geldige naam.**

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Maar het volgende voorbeeld is **geen geldige naam** omdat het hetzelfde aantal segmenten heeft als het type.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Voor onderliggende bronnen hebben het type en de naam hetzelfde aantal segmenten. Dit aantal segmenten is zinvol omdat de volledige naam en het volledige type voor het kind de bovenliggende naam en het bovenliggende type bevatten. Daarom heeft de volledige naam nog steeds een segment minder dan het volledige type.

```json
"resources": [
  {
    "type": "Microsoft.KeyVault/vaults",
    "name": "contosokeyvault",
    ...
    "resources": [
      {
        "type": "secrets",
        "name": "appPassword",
        ...
      }
    ]
  }
]
```

Als u de segmenten goed krijgt, kan dit lastig zijn met resourcebeheertypen die worden toegepast op resourceproviders. Voor het toepassen van een resourcevergrendeling op een website is bijvoorbeeld een type met vier segmenten vereist. Daarom is de naam drie segmenten:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Oplossing 3 - parameter is niet geldig

Als u een parameterwaarde opgeeft die niet een van de toegestane waarden is, ontvangt u een bericht dat vergelijkbaar is met de volgende fout:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Controleer de toegestane waarden in de sjabloon en geef er een tijdens de implementatie. Zie Parameters sectie van Azure [Resource Manager-sjablonen](template-syntax.md#parameters)voor meer informatie over toegestane parameterwaarden.

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Oplossing 4 - Te veel doelgroepen

Als u meer dan vijf doelgroepen in één implementatie opgeeft, ontvangt u deze fout. Overweeg het aantal resourcegroepen in uw implementatie te consolideren of een aantal sjablonen als afzonderlijke implementaties te implementeren. Zie [Azure-resources implementeren voor meer dan één abonnement of resourcegroep voor](cross-resource-group-deployment.md)meer informatie.

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Oplossing 5 - circulaire afhankelijkheid gedetecteerd

U ontvangt deze fout wanneer resources van elkaar afhankelijk zijn op een manier die voorkomt dat de implementatie wordt gestart. Een combinatie van onderlinge afhankelijkheden zorgt ervoor dat twee of meer resources wachten op andere resources die ook wachten. Resource1 is bijvoorbeeld afhankelijk van resource3, resource2 is afhankelijk van resource1 en resource3 is afhankelijk van resource2. U dit probleem meestal oplossen door onnodige afhankelijkheden te verwijderen.

Ga als lid van de kringloopafhankelijkheid met de andere:

1. Zoek in uw sjabloon de resource die is geïdentificeerd in de kringafhankelijkheid.
2. Voor die resource onderzoekt u de eigenschap **dependsOn** en het gebruik van de **referentiefunctie** om te zien van welke resources deze afhankelijk is.
3. Bekijk deze bronnen om te zien van welke bronnen ze afhankelijk zijn. Volg de afhankelijkheden totdat u een resource ziet die afhankelijk is van de oorspronkelijke resource.
5. Voor de resources die betrokken zijn bij de kringafhankelijkheid, zorgvuldig onderzoeken alle toepassingen van de **eigenschap dependsOn** om eventuele afhankelijkheden die niet nodig zijn te identificeren. Verwijder deze afhankelijkheden. Als u niet zeker weet of een afhankelijkheid nodig is, probeert u deze te verwijderen.
6. De sjabloon opnieuw implementeren.

Het verwijderen van waarden uit de eigenschap **dependsOn** kan fouten veroorzaken wanneer u de sjabloon implementeert. Als er een fout optreedt, voegt u de afhankelijkheid weer toe aan de sjabloon.

Als die benadering de kringafhankelijkheid niet oplost, u overwegen een deel van uw implementatielogica over te zetten naar onderliggende resources (zoals extensies of configuratie-instellingen). Configureer deze onderliggende resources om te implementeren na de resources die betrokken zijn bij de kringafhankelijkheid. Stel dat u twee virtuele machines implementeert, maar dat u eigenschappen moet instellen op elk machines die naar de andere verwijzen. U ze in de volgende volgorde implementeren:

1. vm1
2. vm2
3. Uitbreiding op vm1 is afhankelijk van vm1 en vm2. De extensie stelt waarden in op vm1 die het krijgt van vm2.
4. Uitbreiding op vm2 is afhankelijk van vm1 en vm2. De extensie stelt waarden in op vm2 die het krijgt van vm1.

Dezelfde aanpak werkt voor App Service-apps. Overweeg configuratiewaarden te verplaatsen naar een onderliggende bron van de app-bron. U twee web-apps in de volgende volgorde implementeren:

1. webapp1
2. webapp2
3. config voor webapp1 is afhankelijk van webapp1 en webapp2. Het bevat app-instellingen met waarden van webapp2.
4. config voor webapp2 is afhankelijk van webapp1 en webapp2. Het bevat app-instellingen met waarden uit webapp1.
