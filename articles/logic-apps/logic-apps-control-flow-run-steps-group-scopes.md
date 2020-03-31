---
title: Acties groeperen en uitvoeren op bereik
description: Scoped-acties maken die worden uitgevoerd op basis van groepsstatus in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: b84db69f79b1611347a4c55d929e5426141e7ac6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74791486"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Acties uitvoeren op basis van groepsstatus met behulp van scopes in Azure Logic Apps

Als u acties alleen wilt uitvoeren nadat een andere groep acties is geslaagd of mislukt, groepeert u deze acties binnen een *bereik*. Deze structuur is handig wanneer u acties als een logische groep wilt organiseren, de status van die groep wilt evalueren en acties wilt uitvoeren die zijn gebaseerd op de status van het bereik. Nadat alle acties in een scope zijn uitgevoerd, krijgt het bereik ook een eigen status. U bijvoorbeeld scopes gebruiken wanneer u [uitzondering en foutafhandeling](../logic-apps/logic-apps-exception-handling.md#scopes)wilt implementeren. 

Als u de status van een bereik wilt controleren, u dezelfde criteria gebruiken die u gebruikt om de runstatus van een logische app te bepalen, zoals 'Geslaagd', 'Mislukt', 'Geannuleerd', enzovoort. Wanneer alle acties van het bereik slagen, wordt de status van het bereik standaard gemarkeerd als 'Geslaagd'. Maar wanneer een actie in het bereik mislukt of wordt geannuleerd, wordt de status van het bereik gemarkeerd als 'Mislukt'. Zie [Limieten en config](../logic-apps/logic-apps-limits-and-config.md)voor beperkingen op scopes. 

Hier is bijvoorbeeld een logica-app op hoog niveau die een bereik gebruikt om specifieke acties uit te voeren en een voorwaarde om de status van het bereik te controleren. Als acties in het bereik mislukken of onverwacht eindigen, wordt het bereik respectievelijk gemarkeerd als 'Mislukt' of 'Afgebroken' en stuurt de logische app een bericht 'Bereik mislukt'. Als alle acties met bereik slagen, stuurt de logische app een bericht 'Scope geslaagd'.

![Trigger 'Schema - herhaling' instellen](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Vereisten

Als u het voorbeeld in dit artikel wilt volgen, hebt u de volgende items nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Een e-mailaccount van elke e-mailprovider die wordt ondersteund door Logic Apps. In dit voorbeeld wordt Outlook.com gebruikt. Als u een andere provider gebruikt, blijft de algemene stroom hetzelfde, maar ziet uw gebruikersinterface er anders uit.

* Een Bing Maps-sleutel. Zie <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Een bing-kaarttoets kopen</a>om deze sleutel te krijgen.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Voorbeeldlogica-app maken

Maak eerst deze voorbeeldlogica-app, zodat u later een bereik toevoegen:

![Voorbeeldlogica-app maken](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Een **trigger voor een schema - herhaling** die de Bing Maps-service controleert met een interval dat u opgeeft
* Een **Bing Maps - Routeactie uitvoeren** die de reistijd tussen twee locaties controleert
* Een voorwaardelijke instructie die controleert of de reistijd uw opgegeven reistijd overschrijdt
* Een actie die u een e-mail stuurt waarin de huidige reistijd uw opgegeven tijd overschrijdt

U uw logica-app op elk gewenst moment opslaan, dus sla uw werk vaak op.

1. Meld u aan bij de <a href="https://portal.azure.com" target="_blank">Azure-portal,</a>als u dat nog niet hebt gedaan. Een lege, logische app maken.

1. De planning toevoegen **- Recidieftrigger** met deze instellingen: **Interval** = "1" en **Frequentie** = "Minuut"

   ![Trigger 'Schema - herhaling' instellen](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Als u uw weergave visueel wilt vereenvoudigen en de details van elke actie in de ontwerper wilt verbergen, vouwt u de vorm van elke actie samen terwijl u door deze stappen loopt.

1. Voeg de **actie Bing Maps toe - Routeactie opdoen.** 

   1. Als u nog geen Bing Maps-verbinding hebt, wordt u gevraagd een verbinding te maken.

      | Instelling | Waarde | Beschrijving |
      | ------- | ----- | ----------- |
      | **Verbindingsnaam** | BingMapsConnection | Geef een naam op voor uw verbinding. | 
      | **API-sleutel** | <*your-Bing-Maps-toets*> | Voer de sleutel voor Bing Kaarten in die u eerder hebt ontvangen. | 
      ||||  

   1. Stel de actie **Route ophalen** in zoals in de tabel onder deze afbeelding wordt weergegeven:

      ![Actie 'Bing Maps - Route ophalen' instellen](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Zie [Een route berekenen](https://msdn.microsoft.com/library/ff701717.aspx) voor meer informatie over deze parameters.

      | Instelling | Waarde | Beschrijving |
      | ------- | ----- | ----------- |
      | **Routepunt 1** | <*Start*> | Voer de oorsprong van uw route in. | 
      | **Routepunt 2** | <*Einde*> | Voer de bestemming van uw route in. | 
      | **Vermijden** | Geen | Voer items in die u wilt vermijden op uw route, zoals snelwegen, tolgelden, enzovoort. Zie [Een route berekenen voor](https://msdn.microsoft.com/library/ff701717.aspx)mogelijke waarden. | 
      | **Optimaliseren** | timeWithTraffic | Selecteer een parameter om uw route te optimaliseren, zoals afstand, tijd met actuele verkeersinformatie, enzovoort. In dit voorbeeld wordt deze waarde gebruikt: 'timeWithTraffic' | 
      | **Afstandseenheid** | <*uw voorkeur*> | Voer de afstandseenheid in om uw route te berekenen. In dit voorbeeld wordt deze waarde gebruikt: "Mijl" | 
      | **Vervoermiddel** | Auto | Voer de reismodus voor uw route in. In dit voorbeeld wordt deze waarde 'Rijden' gebruikt | 
      | **Datum/tijd openbaar vervoer** | Geen | Geldt alleen voor de transitmodus. | 
      | **Type transitdatum** | Geen | Geldt alleen voor de transitmodus. | 
      ||||  

1. [Voeg een voorwaarde toe](../logic-apps/logic-apps-control-flow-conditional-statement.md) die controleert of de huidige reistijd met verkeer een bepaalde tijd overschrijdt. 
   Volg in dit voorbeeld de volgende stappen:

   1. De naam van de voorwaarde wijzigen met deze beschrijving: **Als de verkeerstijd meer dan opgegeven tijd is**

   1. Klik in de meest linkse kolom in het vak **Een waarde kiezen** zodat de lijst met dynamische inhoud wordt weergegeven. Selecteer in die lijst het veld **Verkeer reisduur,** dat binnen enkele seconden is. 

      ![Voorwaarde bouwen](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Selecteer deze operator in het middelste vak: **is groter dan**

   1. Voer in de meest rechtse kolom deze vergelijkingswaarde in, die in seconden is en gelijk is aan 10 minuten: **600**

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Afgewerkte toestand](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. Voeg in de **optie Als waar** een actie 'E-mail verzenden' toe voor uw e-mailprovider. 
   Stel deze actie in door de stappen onder deze afbeelding te volgen:

   ![Actie 'Een e-mail verzenden' toevoegen aan de branch 'Als waar'.](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Voer in het veld **Aan** uw e-mailadres in voor testdoeleinden.

   1. Voer **in** het veld Onderwerp de tekst in:

      ```Time to leave: Traffic more than 10 minutes```

   1. Voer in het veld **Hoofdlichaam** deze tekst in met een naloopruimte: 

      ```Travel time:```

      Terwijl de cursor wordt weergegeven in het veld **Hoofdtekst,** blijft de lijst met dynamische inhoud geopend, zodat u alle parameters selecteren die op dit moment beschikbaar zijn.

   1. Kies in de lijst met dynamische inhoud voor **Expressie**.

   1. Zoek en selecteer de functie **div().** 
      Plaats de cursor in de haakjes van de functie.

   1. Terwijl de cursor zich in de haakjes van de functie bevindt, kiest u **Dynamische inhoud** zodat de lijst met dynamische inhoud wordt weergegeven. 
   
   1. Selecteer **in** de sectie Route ophalen het veld **Verkeer duurverkeer.**

      ![Selecteer 'Verkeer seinverkeer'](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Nadat het veld is opgelost in de JSON-indeling, ```60``` voegt u een **komma** (```,```) toe, gevolgd door het getal, zodat u de waarde in **Verkeersduurverkeer** van seconden naar minuten converteert. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Uw expressie ziet er nu uit als dit voorbeeld:

      ![Expressie voltooien](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Als u klaar bent, kiest u **Done**.

   <!-- markdownlint-disable MD038 -->
   1. Nadat de expressie is opgelost, voegt u deze tekst toe met een voorloopruimte:``` minutes```
  
       Het veld **Lichaam** ziet er nu als volgt uit:

       ![Afgewerkt veld 'Lichaam'](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Sla uw logische app op.

Voeg vervolgens een bereik toe, zodat u specifieke acties groeperen en de status ervan evalueren.

## <a name="add-a-scope"></a>Een bereik toevoegen

1. Als u dit nog niet hebt gedaan, opent u uw logische app in Logic App Designer. 

1. Voeg een bereik toe op de gewenste werkstroomlocatie. Voer bijvoorbeeld de volgende stappen uit om een bereik toe te voegen tussen bestaande stappen in de werkstroom van de logische app: 

   1. Beweeg de aanwijzer over de pijl waar u het bereik wilt toevoegen. 
   Kies het **plusteken** (**+**) > **Een actie toevoegen**.

      ![Een bereik toevoegen](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Voer in het zoekvak 'bereik' in als filter. 
   Selecteer de actie **Scope.**

## <a name="add-steps-to-scope"></a>Stappen toevoegen aan bereik

1. Voeg nu de stappen toe of sleep bestaande stappen die u binnen het bereik wilt uitvoeren. Sleep in dit voorbeeld deze acties naar het bereik:
      
   * **Route krijgen**
   * **Als de verkeerstijd meer dan opgegeven tijd is,** die zowel de **ware** als **de valse** vertakkingen omvat

   Uw logica-app ziet er nu als volgt uit:

   ![Bereik toegevoegd](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Voeg onder het bereik een voorwaarde toe die de status van het bereik controleert. De naam van de voorwaarde wijzigen met deze beschrijving: **Als het bereik is mislukt**

   ![Voorwaarde toevoegen om de status van het bereik te controleren](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Voeg in de voorwaarde deze expressies toe die controleren of de status van het bereik gelijk is aan 'Mislukt' of 'Afgebroken'. 

   1. Als u een andere rij wilt toevoegen, kiest u **Toevoegen**. 

   1. Klik in elke rij in het linkervak zodat de lijst met dynamische inhoud wordt weergegeven. 
   Kies **Expressie**in de lijst met dynamische inhoud . Voer deze expressie in het vak bewerken en kies **OK:** 
   
      `result('Scope')[0]['status']`

      ![Expressie toevoegen die de status van het bereik controleert](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Voor beide rijen is de selectie **gelijk aan** als operator. 
   
   1. Voer voor de vergelijkingswaarden in `Failed`de eerste rij . 
   Voer in de `Aborted`tweede rij . 

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Expressie toevoegen die de status van het bereik controleert](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Stel nu de eigenschap `runAfter` van de voorwaarde in, zodat de voorwaarde de scopestatus controleert en de overeenkomende actie uitvoert die u in latere stappen definieert.

   1. Kies in de voorwaarde **Als-bereik is mislukt** de knop **ellips (...)** en kies Uitvoeren configureren **na**.

      !['runAfter' configureren, eigenschap](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Selecteer al deze scopestatuss: **is geslaagd,** **is mislukt,** **wordt overgeslagen**en **er is een time-out**

      ![Bereikstatussen selecteren](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Wanneer u klaar bent, kiest u **Gereed**. 
   De voorwaarde toont nu een "informatie" pictogram.

1. Voeg in de **ware als** en **als onwaarvertakkingen** de acties toe die u wilt uitvoeren op basis van elke scopestatus, bijvoorbeeld een e-mail of bericht verzenden.

   ![Acties toevoegen die moeten worden uitgevoerd op basis van de status van het bereik](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Sla uw logische app op.

Uw voltooide logica-app ziet er nu als volgt uit:

![Voltooide logica-app met bereik](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Test je werk

Kies op de werkbalk van de ontwerper de optie **Uitvoeren**. Als alle acties met bereik slagen, krijgt u een bericht 'Scope geslaagd'. Als acties met scopen niet slagen, krijgt u een bericht 'Bereik mislukt'. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-definitie

Als u in de codeweergave werkt, u in plaats daarvan een scopestructuur definiëren in de JSON-definitie van uw logische app. Hier vindt u bijvoorbeeld de JSON-definitie voor trigger en acties in de vorige logische app:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Ga naar de [feedbacksite](https://aka.ms/logicapps-wish)van Azure Logic Apps om functies en suggesties in te dienen of te stemmen.

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van een voorwaarde (voorwaardelijke instructies)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Stappen uitvoeren op basis van verschillende waarden (schakelinstructies)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Stappen uitvoeren en herhalen (lussen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Parallelle stappen uitvoeren of samenvoegen (branches)](../logic-apps/logic-apps-control-flow-branches.md)
