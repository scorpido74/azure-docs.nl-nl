---
title: Bron vergrendeling begrijpen
description: Meer informatie over de vergrendelings opties in azure blauw drukken om resources te beveiligen wanneer u een blauw druk toewijst.
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: 50f506cc57f67ca2ae2b07e342750d6c5099e739
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406413"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Meer informatie over het vergren delen van resources in azure-blauw drukken

Het maken van consistente omgevingen op schaal is alleen echt waardevol als er een mechanisme is om die consistentie te hand haven. In dit artikel wordt uitgelegd hoe resource vergrendeling werkt in azure-blauw drukken. Zie de zelf studie [nieuwe resources beveiligen](../tutorials/protect-new-resources.md) voor een voor beeld van het vergren delen van resources en het Toep assen van _toewijzingen weigeren_.

## <a name="locking-modes-and-states"></a>Vergrendelings modi en statussen

Vergrendelings modus is van toepassing op de blauw druk-toewijzing en er zijn drie opties: **niet vergren delen**, **alleen-lezen**of **niet verwijderen**. De vergrendelings modus wordt tijdens de toewijzing van een blauw druk geconfigureerd tijdens de implementatie van artefacten. Een andere vergrendelings modus kan worden ingesteld door de toewijzing van de blauw druk bij te werken.
Vergrendelings modi kunnen echter niet buiten blauw drukken worden gewijzigd.

Resources die zijn gemaakt door artefacten in een blauw druk-toewijzing, hebben vier statussen: **niet vergrendeld**, **alleen-lezen**, **niet bewerken/verwijderen**of **kan niet verwijderen**. Elk type artefact kan de status **niet vergrendeld** hebben. De volgende tabel kan worden gebruikt om de status van een resource te bepalen:

|Modus|Bron type voor artefacten|Status|Beschrijving|
|-|-|-|-|
|Niet vergren delen|*|Niet vergrendeld|Bronnen worden niet beveiligd door blauw drukken. Deze status wordt ook gebruikt voor resources die worden toegevoegd aan een **alleen-lezen** -of **verwijderings** artefact van een resource buiten een blauw druk-toewijzing.|
|Alleen-lezen|Resourcegroep|Kan niet bewerken/verwijderen|De resource groep is alleen-lezen en tags op de resource groep kunnen niet worden gewijzigd. **Niet-vergrendelde** resources kunnen worden toegevoegd, verplaatst, gewijzigd of verwijderd uit deze resource groep.|
|Alleen-lezen|Niet-resource groep|Alleen-lezen|De resource kan op geen enkele manier worden gewijzigd: geen wijzigingen en kan niet worden verwijderd.|
|Niet verwijderen|*|Kan niet verwijderen|De resources kunnen worden gewijzigd, maar kunnen niet worden verwijderd. **Niet-vergrendelde** resources kunnen worden toegevoegd, verplaatst, gewijzigd of verwijderd uit deze resource groep.|

## <a name="overriding-locking-states"></a>Vergrendelings status negeren

Het is doorgaans mogelijk dat iemand met het juiste op [rollen gebaseerde toegangs beheer](../../../role-based-access-control/overview.md) (RBAC) op het abonnement, zoals de rol ' eigenaar ', toestemming mag geven om een resource te wijzigen of te verwijderen. Deze toegang is niet het geval wanneer blauw drukken wordt toegepast als onderdeel van een geïmplementeerde toewijzing. Als de toewijzing is ingesteld met de optie **alleen-lezen** of **niet verwijderen** , niet zelfs de eigenaar van het abonnement kan de geblokkeerde actie uitvoeren op de beveiligde bron.

Deze beveiligings maatregel beveiligt de consistentie van de gedefinieerde blauw druk en de omgeving die is ontworpen om te worden gemaakt op basis van per ongeluk of programmatische verwijdering of wijziging.

## <a name="removing-locking-states"></a>Vergrendelings status verwijderen

Als het nodig is om een resource die wordt beveiligd door een toewijzing, te wijzigen of te verwijderen, kunt u dit op twee manieren doen.

- De blauw druk-toewijzing bijwerken naar een vergrendelings modus **zonder vergren deling**
- De blauw druk-toewijzing verwijderen

Wanneer de toewijzing wordt verwijderd, worden de vergren delingen die zijn gemaakt door blauw drukken verwijderd. De resource blijft echter achter en moet worden verwijderd via normale manier.

## <a name="how-blueprint-locks-work"></a>Hoe blauw drukken werkt

Een RBAC-actie voor het weigeren van [toewijzingen](../../../role-based-access-control/deny-assignments.md) wordt toegepast op artefact resources tijdens de toewijzing van een blauw druk als de toewijzing de optie **alleen-lezen** of **niet verwijderen** is geselecteerd. De actie voor weigeren wordt toegevoegd door de beheerde identiteit van de blauw druk toewijzing en kan alleen worden verwijderd uit de artefact resources met dezelfde beheerde identiteit. Deze beveiligings meting dwingt het vergrendelings mechanisme af en voor komt het verwijderen van de blauw druk buiten blauw drukken.

![Blauw druk weigeren toewijzing voor resource groep](../media/resource-locking/blueprint-deny-assignment.png)

De [Eigenschappen](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) voor het weigeren van toewijzingen van elke modus zijn als volgt:

|Modus |Machtigingen. acties |Machtigingen. intact |Principals [i]. Voert |ExcludePrincipals [i]. Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Alleen-lezen |**\*** |**\*/Read** |SystemDefined (iedereen) |blauw druk toewijzen en door de gebruiker gedefinieerd in **excludedPrincipals** |Resource groep- _True_; Resource- _False_ |
|Niet verwijderen |**\*/Delete** | |SystemDefined (iedereen) |blauw druk toewijzen en door de gebruiker gedefinieerd in **excludedPrincipals** |Resource groep- _True_; Resource- _False_ |

> [!IMPORTANT]
> Met Azure Resource Manager worden de gegevens van de roltoewijzing Maxi maal 30 minuten in de cache opgeslagen. Als gevolg hiervan is het weigeren van de toewijzingen voor het weigeren van een actie op blauw drukken-resources mogelijk niet onmiddellijk volledig van kracht. Tijdens deze periode is het mogelijk om een resource te verwijderen die is bedoeld om te worden beveiligd door de vergren delingen van blauw drukken.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Een principal uitsluiten van een weiger toewijzing

In sommige ontwerp-of beveiligings scenario's kan het nodig zijn om een principal uit te sluiten van de [toewijzing weigeren](../../../role-based-access-control/deny-assignments.md) die door de toewijzing van blauw drukken wordt gemaakt. Dit wordt gedaan in REST API door Maxi maal vijf waarden toe te voegen aan de **excludedPrincipals** -matrix in de eigenschap **Locks** bij [het maken van de toewijzing](/rest/api/blueprints/assignments/createorupdate). Dit is een voor beeld van een aanvraag tekst die **excludedPrincipals**bevat:

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

## <a name="next-steps"></a>Volgende stappen

- Volg de zelf studie [nieuwe resources beveiligen](../tutorials/protect-new-resources.md) .
- Meer informatie over de [levenscyclus van een blauwdruk](lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](sequencing-order.md) aanpast.
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).