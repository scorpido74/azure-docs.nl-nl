---
title: Json-werkstroomdefinities voor logische apps maken, bewerken of uitbreiden
description: De JSON-werkstroomdefinities van uw logische app schrijven, bewerken en uitbreiden in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 0f5f01c757bf651beddaa76fc3eb8046b21b31eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979397"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>Json maken, bewerken of uitbreiden voor definities van logische app-werkstroom in Azure Logic Apps

Wanneer u oplossingen voor bedrijfsintegratie maakt met geautomatiseerde werkstromen in [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)gebruiken de onderliggende definities van logische app eenvoudige en declaratieve JavaScript-objectnotatie (JSON) samen met het [WDL-schema (Workflow Definition Language)](../logic-apps/logic-apps-workflow-definition-language.md) voor de beschrijving en validatie ervan. Deze indelingen maken logica app definities gemakkelijker te lezen en te begrijpen zonder veel te weten over code.
Wanneer u het maken en implementeren van logische apps wilt automatiseren, u logische app-definities opnemen als [Azure-bronnen](../azure-resource-manager/management/overview.md) in [Azure Resource Manager-sjablonen.](../azure-resource-manager/templates/overview.md)
Als u logische apps wilt maken, beheren en implementeren, u azure [PowerShell,](https://docs.microsoft.com/powershell/module/az.logicapp) [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)of de [API's van Azure Logic Apps REST](https://docs.microsoft.com/rest/api/logic/)gebruiken.

Als u wilt werken met logische app-definities in JSON, opent u de codeweergaveeditor wanneer u in de Azure-portal of in Visual Studio werkt of kopieert u de definitie naar een gewenste editor.
Als u nieuw bent in logische apps, leest u [hoe u uw eerste logische app maakt.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

> [!NOTE]
> Sommige Azure Logic Apps-mogelijkheden, zoals het definiëren van parameters en meerdere triggers in logische app-definities, zijn alleen beschikbaar in JSON, niet in de Logic Apps Designer.
> Voor deze taken moet u dus werken in de codeweergave of een andere editor.

## <a name="edit-json---azure-portal"></a>JSON bewerken - Azure-portal

1. Meld u aan bij <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. Kies **Alle services**in het linkermenu.
Zoek in het zoekvak 'logische apps' en selecteer vervolgens in de resultaten uw logische app.

3. Selecteer in het menu van uw logische app onder **Hulpmiddelen voor ontwikkeling**de optie Logische **app-codeweergave**.

   De codeweergaveeditor wordt geopend en toont de definitie van uw logische app in JSON-indeling.

## <a name="edit-json---visual-studio"></a>Bewerk JSON - Visual Studio

Voordat u werken aan de definitie van uw logische app in Visual Studio, moet u ervoor zorgen dat u de vereiste hulpprogramma's hebt [geïnstalleerd.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)
Als u een logische app wilt maken met Visual Studio, bekijkt u [Quickstart: Taken en processen automatiseren met Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

In Visual Studio u logische apps openen die rechtstreeks vanuit de Azure-portal zijn gemaakt en geïmplementeerd, of als Azure Resource Manager-projecten vanuit Visual Studio.

1. Open de Visual Studio-oplossing of het Azure [Resource Group-project](../azure-resource-manager/management/overview.md) dat uw logische app bevat.

2. De definitie van uw logische app zoeken en openen, die standaard wordt weergegeven in een [resourcemanagersjabloon](../azure-resource-manager/templates/overview.md)met de naam **LogicApp.json**.
U deze sjabloon gebruiken en aanpassen voor implementatie naar verschillende omgevingen.

3. Open het snelmenu voor de definitie en sjabloon van uw logische app.
Selecteer **Openen met Logic App-ontwerpfunctie**.

   ![Logische app openen in een Visual Studio-oplossing](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Als je deze opdracht niet hebt in Visual Studio 2019, controleer dan of je de nieuwste updates voor Visual Studio hebt.

4. Kies onder aan de ontwerper **de codeweergave**.

   De codeweergaveeditor wordt geopend en toont de definitie van uw logische app in JSON-indeling.

5. Als u wilt terugkeren naar de ontwerpweergave, kiest u onder aan de editor codeweergave de optie **Ontwerpen**.

## <a name="parameters"></a>Parameters

De implementatielevenscyclus heeft meestal verschillende omgevingen voor ontwikkeling, test, fasering en productie. Wanneer u waarden hebt die u in uw logische app wilt hergebruiken zonder harde codering of die variëren op basis van uw implementatiebehoeften, u een [Azure Resource Manager-sjabloon](../azure-resource-manager/management/overview.md) maken voor uw werkstroomdefinitie, zodat u ook de implementatie van logische apps automatiseren.

Volg deze algemene stappen om deze waarden te *parameteriseren*of te definiëren en te gebruiken voor deze waarden. Vervolgens u de waarden opgeven in een afzonderlijk parameterbestand dat deze waarden doorgeeft aan uw sjabloon. Op die manier u deze waarden gemakkelijker wijzigen zonder dat u uw logische app hoeft bij te werken en opnieuw te implementeren. Zie [Overzicht: Implementatie automatiseren voor logische apps met Azure Resource Manager-sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)voor meer informatie.

1. Definieer in uw sjabloon sjabloonparameters en werkstroomdefinitieparameters voor het accepteren van de waarden die respectievelijk bij implementatie en runtime moeten worden gebruikt.

   Sjabloonparameters worden gedefinieerd in een parameterssectie die buiten uw werkstroomdefinitie valt, terwijl de parameters voor werkstroomdefinitie worden gedefinieerd in een parameterssectie die binnen uw werkstroomdefinitie valt.

1. Vervang de hardcoded waarden door expressies die verwijzen naar deze parameters. Sjabloonexpressies gebruiken syntaxis die afwijkt van werkstroomdefinitieexpressies.

   Vermijd het compliceren van uw code door geen sjabloonexpressies te gebruiken, die bij implementatie worden geëvalueerd, in werkstroomdefinitieexpressies, die tijdens runtime worden geëvalueerd. Gebruik alleen sjabloonexpressies buiten de werkstroomdefinitie. Gebruik alleen werkstroomdefinitieexpressies binnen de werkstroomdefinitie.

   Wanneer u de waarden voor uw werkstroomdefinitieparameters opgeeft, u sjabloonparameters verwijzen met behulp van de sectie parameters die buiten uw werkstroomdefinitie valt, maar nog steeds binnen de resourcedefinitie voor uw logische app. Op die manier u sjabloonparameterwaarden doorgeven aan de parameters voor werkstroomdefinitie.

1. Sla de waarden voor uw parameters op in een afzonderlijk [parameterbestand](../azure-resource-manager/templates/parameter-files.md) en voeg dat bestand toe aan uw implementatie.

## <a name="process-strings-with-functions"></a>Tekenreeksen verwerken met functies

Logic Apps heeft verschillende functies voor het werken met tekenreeksen.
Stel dat u een bedrijfsnaam wilt doorgeven van een bestelling naar een ander systeem.
U bent echter niet zeker over de juiste behandeling voor tekencodering.
U base64-codering op deze tekenreeks uitvoeren, maar om ontsnappingen in de URL te voorkomen, u in plaats daarvan verschillende tekens vervangen. Ook hebt u alleen een substring nodig voor de bedrijfsnaam, omdat de eerste vijf tekens niet worden gebruikt.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

In deze stappen wordt beschreven hoe dit voorbeeld deze tekenreeks verwerkt en van binnen naar buiten werkt:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Krijg [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) de voor de bedrijfsnaam, zodat u het totale aantal tekens krijgt.

2. Als u een kortere `5`tekenreeks wilt krijgen, trekt u af .

3. Nu krijgen [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md)een .
Begin bij `5`index en ga naar de rest van de tekenreeks.

4. Converteer deze [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) subtekenreeks naar een tekenreeks.

5. Nu [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) alle `+` personages `-` met personages.

6. Tot [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) slot, `/` alle `_` personages met personages.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Lijstitems toewijzen aan eigenschapswaarden en vervolgens kaarten als parameters gebruiken

Als u verschillende resultaten wilt krijgen op basis van de waarde van een eigenschap, u een kaart maken die elke eigenschapswaarde overeenkomt met een resultaat en die kaart vervolgens als parameter gebruiken.

In deze werkstroom worden bijvoorbeeld bepaalde categorieën gedefinieerd als parameters en een kaart die overeenkomt met die categorieën met een specifieke URL.
Eerst krijgt de werkstroom een lijst met artikelen. Vervolgens gebruikt de werkstroom de kaart om de URL te vinden die overeenkomt met de categorie voor elk artikel.

*   De [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) functie controleert of de categorie overeenkomt met een bekende gedefinieerde categorie.

*   Nadat u een overeenkomende categorie hebt gekregen, haalt het voorbeeld het item van de kaart met vierkante haakjes:`parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Gegevens verzamelen met datumfuncties

Als u gegevens wilt ophalen uit een gegevensbron die *triggers*niet native ondersteunt, u datumfuncties gebruiken voor het werken met tijden en datums.
Deze expressie geeft bijvoorbeeld aan hoe lang de stappen van deze werkstroom duren en werkt van binnen naar buiten:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Haal `order` uit de `startTime`actie de .
2. Krijg de huidige `utcNow()`tijd met .
3. Trek één seconde af:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   U andere eenheden van `minutes` `hours`de tijd gebruiken, zoals of.

3. Nu u deze twee waarden vergelijken.

   Als de eerste waarde lager is dan de tweede waarde, is er meer dan een seconde verstreken sinds de order voor het eerst werd geplaatst.

Als u datums wilt opmaken, u tekenreeksformatters gebruiken. Als u bijvoorbeeld de RFC1123 [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md)wilt krijgen, gebruikt u .
Meer informatie over [datumopmaak](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van een voorwaarde (voorwaardelijke instructies)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Stappen uitvoeren op basis van verschillende waarden (schakelinstructies)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Stappen uitvoeren en herhalen (lussen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Parallelle stappen uitvoeren of samenvoegen (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Stappen uitvoeren op basis van gegroepeerde actiestatus (scopes)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Meer informatie over het [taalschema voor werkstroomdefinitie voor Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Meer informatie over [werkstroomacties en triggers voor Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
