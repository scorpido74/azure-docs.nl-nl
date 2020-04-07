---
title: Inzicht in resourcevergrendeling
description: Meer informatie over de vergrendelingsopties in Azure Blueprints om resources te beschermen bij het toewijzen van een blauwdruk.
ms.date: 03/25/2020
ms.topic: conceptual
ms.openlocfilehash: 9c4e2f4c6fd8f5fb574002217ca71d1e7d130ff7
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676755"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Inzicht in bronvergrendeling in Azure Blueprints

Het creëren van consistente omgevingen op schaal is alleen echt waardevol als er een mechanisme is om die consistentie te behouden. In dit artikel wordt uitgelegd hoe resourcevergrendeling werkt in Azure Blueprints. Zie de zelfstudie [voor het beveiligen](../tutorials/protect-new-resources.md) van nieuwe resources voor een voorbeeld van resourcevergrendeling en toepassing van _weigeringstoewijzingen._

> [!NOTE]
> Resourcevergrendelingen die zijn geïmplementeerd door Azure Blueprints, worden alleen toegepast op resources die zijn geïmplementeerd door de blauwdruktoewijzing. Bestaande bronnen, zoals bronnen in resourcegroepen die al bestaan, hebben geen vergrendelingen toegevoegd.

## <a name="locking-modes-and-states"></a>Vergrendelingsmodi en -statussen

De vergrendelingsmodus is van toepassing op de blauwdruktoewijzing en heeft drie opties: **Niet vergrendelen,** **alleen lezen**of **niet verwijderen.** De vergrendelingsmodus wordt geconfigureerd tijdens de implementatie van artefacten tijdens een blauwdruktoewijzing. Een andere vergrendelingsmodus kan worden ingesteld door de blauwdruktoewijzing bij te werken.
Vergrendelingsmodi kunnen echter niet worden gewijzigd buiten Azure Blueprints.

Resources die zijn gemaakt door artefacten in een blauwdruktoewijzing hebben vier statussen: **Niet vergrendeld**, **alleen lezen**, kan niet bewerken **/ verwijderen**of kan niet **worden verwijderd**. Elk artefacttype kan zich in de **status Niet vergrendeld bevindt.** De volgende tabel kan worden gebruikt om de status van een resource te bepalen:

|Modus|Artefact-brontype|Status|Beschrijving|
|-|-|-|-|
|Niet vergrendelen|*|Niet vergrendeld|Resources worden niet beschermd door Azure Blueprints. Deze status wordt ook gebruikt voor resources die zijn toegevoegd aan een artefact voor **alleen-lezen** of **niet-verwijderen** van een brongroep van buiten een blauwdruktoewijzing.|
|Alleen-lezen|Resourcegroep|Kan niet bewerken / verwijderen|De brongroep wordt alleen gelezen en tags in de resourcegroep kunnen niet worden gewijzigd. **Niet vergrendelde** resources kunnen uit deze brongroep worden toegevoegd, verplaatst, gewijzigd of verwijderd.|
|Alleen-lezen|Niet-resourcegroep|Alleen-lezen|De bron kan op geen enkele manier worden gewijzigd, er worden geen wijzigingen aangebracht en kan niet worden verwijderd.|
|Niet verwijderen|*|Kan niet verwijderen|De bronnen kunnen worden gewijzigd, maar kunnen niet worden verwijderd. **Niet vergrendelde** resources kunnen uit deze brongroep worden toegevoegd, verplaatst, gewijzigd of verwijderd.|

## <a name="overriding-locking-states"></a>Dwingende vergrendelingstoestanden

Het is meestal mogelijk dat iemand met het juiste [op rollen gebaseerde toegangscontrole](../../../role-based-access-control/overview.md) (RBAC) op het abonnement, zoals de rol 'Eigenaar', een resource kan wijzigen of verwijderen. Deze toegang is niet het geval wanneer Azure Blueprints vergrendeling toepast als onderdeel van een geïmplementeerde toewijzing. Als de toewijzing is ingesteld met de optie **Alleen lezen** of **Niet verwijderen,** kan zelfs de eigenaar van het abonnement de geblokkeerde actie op de beveiligde bron niet uitvoeren.

Deze beveiligingsmaatregel beschermt de consistentie van de gedefinieerde blauwdruk en de omgeving die is ontworpen om te maken van toevallige of programmatische verwijdering of wijziging.

### <a name="assign-at-management-group"></a>Toewijzen aan beheergroep

Een extra optie om te voorkomen dat eigenaren van abonnementen een blauwdruktoewijzing verwijderen, is om de blauwdruk toe te wijzen aan een beheergroep. In dit scenario hebben alleen **eigenaren** van de beheergroep de machtigingen die nodig zijn om de blauwdruktoewijzing te verwijderen.

Als u de blauwdruk wilt toewijzen aan een beheergroep in plaats van een abonnement, wordt de REST API-aanroep als volgt gewijzigd:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

De beheergroep gedefinieerd `{assignmentMG}` door moet ofwel binnen de hiërarchie van de beheergroep of dezelfde beheergroep zijn waar de blauwdrukdefinitie wordt opgeslagen.

De aanvraagtekst van de blauwdrukopdracht ziet er als volgt uit:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

Het belangrijkste verschil in deze aanvraaginstantie en een `properties.scope` die aan een abonnement wordt toegewezen, is de eigenschap. Deze vereiste eigenschap moet worden ingesteld op het abonnement waarop de blauwdruktoewijzing van toepassing is. Het abonnement moet een direct onderliggend eis zijn van de hiërarchie van de beheergroep waarin de blauwdruktoewijzing is opgeslagen.

> [!NOTE]
> Een blauwdruk die is toegewezen aan het bereik van de beheergroep, werkt nog steeds als een blauwdruktoewijzing op abonnementsniveau. Het enige verschil is waar de blauwdruktoewijzing wordt opgeslagen om te voorkomen dat abonnementseigenaren de toewijzing en bijbehorende vergrendelingen verwijderen.

## <a name="removing-locking-states"></a>Vergrendelingstoestanden verwijderen

Als het nodig wordt om een resource die door een toewijzing wordt beschermd, te wijzigen of te verwijderen, zijn er twee manieren om dit te doen.

- De blauwdruktoewijzing bijwerken naar een vergrendelingsmodus van **Niet vergrendelen**
- De blauwdruktoewijzing verwijderen

Wanneer de toewijzing wordt verwijderd, worden de vergrendelingen die zijn gemaakt door Azure Blueprints verwijderd. De bron blijft echter achter en moet op normale wijze worden verwijderd.

## <a name="how-blueprint-locks-work"></a>Hoe blauwdrukvergrendelingen werken

Een [weigeringsactie voor RBAC-toewijzingen](../../../role-based-access-control/deny-assignments.md) wordt toegepast op artefactbronnen tijdens de toewijzing van een blauwdruk als de toewijzing de optie **Alleen lezen** of niet **verwijderen heeft** geselecteerd. De weigeringsactie wordt toegevoegd door de beheerde identiteit van de blauwdruktoewijzing en kan alleen met dezelfde beheerde identiteit uit de artefactbronnen worden verwijderd. Deze beveiligingsmaatregel dwingt het vergrendelingsmechanisme af en voorkomt het verwijderen van het blauwdrukslot buiten Azure Blueprints.

![Toewijzing voor blueprint weigeren op resourcegroep](../media/resource-locking/blueprint-deny-assignment.png)

De [eigenschappen van de toewijzing weigeren](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) van elke modus zijn als volgt:

|Modus |Machtigingen.Acties |Permissions.NotActions |Opdrachtgevers[i]. Type |Uitgesloten Principals[i]. Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Alleen-lezen |**\*** |**\*/lezen** |SystemDefined (Iedereen) |blauwdruktoewijzing en door de gebruiker gedefinieerd in **uitgesloten Principals** |Resourcegroep - _waar_; Resource - _false_ |
|Niet verwijderen |**\*/verwijderen** | |SystemDefined (Iedereen) |blauwdruktoewijzing en door de gebruiker gedefinieerd in **uitgesloten Principals** |Resourcegroep - _waar_; Resource - _false_ |

> [!IMPORTANT]
> Azure Resource Manager caches roltoewijzing details voor maximaal 30 minuten. Als gevolg hiervan is het mogelijk dat weigeren toewijzingen actie weigeren op blauwdrukbronnen niet onmiddellijk volledig effect hebben. Gedurende deze periode is het mogelijk om een bron te verwijderen die bedoeld is om te worden beschermd door blauwdrukvergrendelingen.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Een opdrachtgever uitsluiten van een weigeringsopdracht

In sommige ontwerp- of beveiligingsscenario's kan het nodig zijn om een opdrachtgever uit te sluiten van de [weigeringstoewijzing](../../../role-based-access-control/deny-assignments.md) die de blauwdruktoewijzing maakt. Deze stap wordt uitgevoerd in DE REST API door maximaal vijf waarden toe te voegen aan de array **excludedPrincipals** in de eigenschap **locks** bij [het maken van de toewijzing.](/rest/api/blueprints/assignments/createorupdate) De volgende **toewijzingsdefinitie**is een voorbeeld van een aanvraaginstantie die uitgesloten Principals omvat:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Een actie uitsluiten van een weigeringstoewijzing

Net als [bij het uitsluiten van een principal](#exclude-a-principal-from-a-deny-assignment) voor een [weigeringstoewijzing](../../../role-based-access-control/deny-assignments.md) in een blauwdruktoewijzing, u specifieke [RBAC-bewerkingen](../../../role-based-access-control/resource-provider-operations.md)uitsluiten. Binnen het blok **properties.locks** kan op dezelfde plaats als **Principals zijn uitgesloten,** een **uitgesloten Acties** worden toegevoegd:

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Hoewel uitgeslotenPrincipals expliciet moeten zijn, kunnen **uitgeslotenacties** `*` gebruik maken van wildcardmatching van RBAC-bewerkingen. **excludedPrincipals**

## <a name="next-steps"></a>Volgende stappen

- Volg de zelfstudie [voor nieuwe bronnen beveiligen.](../tutorials/protect-new-resources.md)
- Meer informatie over de [levenscyclus van de blauwdruk](lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](sequencing-order.md)aan te passen.
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Los problemen op tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing.](../troubleshoot/general.md)