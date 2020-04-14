---
title: Benoemde waarden gebruiken in Azure API Management-beleid
description: Meer informatie over het gebruik van benoemde waarden in azure API-beheerbeleid.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 4362d0875ac2c20fc6963d404f86898a12387dad
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260918"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Benoemde waarden gebruiken in Azure API Management-beleid

API-beheerbeleid is een krachtige mogelijkheid van het systeem waarmee de Azure-portal het gedrag van de API kan wijzigen door middel van configuratie. Beleidsregels zijn een verzameling instructies die sequentieel worden uitgevoerd op de aanvraag of het antwoord van een API. Beleidsinstructies kunnen worden samengesteld met letterlijke tekstwaarden, beleidsexpressies en benoemde waarden.

Elke API Management-serviceinstantie heeft een verzameling sleutel-/waardeparen, die waarden worden genoemd, die globaal zijn voor de service-instantie. Er is geen limiet voor het aantal items in de collectie. Benoemde waarden kunnen worden gebruikt om constante tekenreekswaarden te beheren voor alle API-configuratie en -beleidsregels. Elke benoemde waarde kan de volgende kenmerken hebben:

| Kenmerk      | Type            | Beschrijving                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | tekenreeks          | Wordt gebruikt voor het verwijzen naar de benoemde waarde in beleid. Een reeks van één tot 256 tekens. Alleen letters, cijfers, stip jes en streepjes zijn toegestaan. |
| `Value`        | tekenreeks          | Werkelijke waarde. Mag niet leeg zijn of alleen uit witruimte bestaan. Maximaal 4096 tekens lang.                                        |
| `Secret`       | booleaans         | Hiermee bepaalt u of de waarde een geheim is en al dan niet moet worden versleuteld.                                                               |
| `Tags`         | tekenreeksmatrix | Wordt gebruikt om de lijst met benoemde waarden te filteren. Maximaal 32 tags.                                                                                    |

![Benoemde waarden](./media/api-management-howto-properties/named-values.png)

Benoemde waarden kunnen letterlijke tekenreeksen en [beleidsexpressies](/azure/api-management/api-management-policy-expressions)bevatten. De waarde van `Expression` bijvoorbeeld is een beleidsexpressie die een tekenreeks retourneert die de huidige datum en tijd bevat. De benoemde `Credential` waarde is gemarkeerd als een geheim, zodat de waarde niet standaard wordt weergegeven.

| Name       | Waarde                      | Geheim | Tags          |
| ---------- | -------------------------- | ------ | ------------- |
| Waarde      | 42                         | False  | vitale nummers |
| Referentie | ••••••••••••••••••••••     | True   | security      |
| Expressie | @(DateTime.Now.ToString()) | False  |               |

> [!NOTE]
> In plaats van benoemde waarden die zijn opgeslagen in een API-beheerservice, u waarden gebruiken die zijn opgeslagen in de [Azure Key Vault-service,](https://azure.microsoft.com/services/key-vault/) zoals blijkt uit dit [voorbeeld.](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)

## <a name="to-add-and-edit-a-named-value"></a>Een benoemde waarde toevoegen en bewerken

![Een benoemde waarde toevoegen](./media/api-management-howto-properties/add-property.png)

1. Selecteer **API's** bij **API MANAGEMENT**.
2. Selecteer **Benoemde waarden**.
3. Druk op **+Toevoegen**.

    Naam en waarde zijn vereiste waarden. Als waarde een geheim is, schakelt u het _selectievakje Dit is een geheim_ selectievakje in. Voer een of meer optionele tags in om te helpen bij het ordenen van de benoemde waarden en klik op Opslaan.

4. Klik **op Maken**.

Zodra de benoemde waarde is gemaakt, u deze bewerken door erop te klikken. Als u de benoemde waardenaam wijzigt, worden alle beleidsregels die verwijzen naar de benoemde waarde automatisch bijgewerkt om de nieuwe naam te gebruiken.

Zie Een benoemde waarde bewerken met de REST API voor informatie over het bewerken van een benoemde waarde met [behulp van de REST-API.](/rest/api/apimanagement/2019-12-01/property?patch)

## <a name="to-delete-a-named-value"></a>Een benoemde waarde verwijderen

Als u een benoemde waarde wilt verwijderen, klikt u op **Verwijderen** naast de benoemde waarde die u wilt verwijderen.

> [!IMPORTANT]
> Als de benoemde waarde wordt verwezen door een beleid, u deze niet verwijderen totdat u de benoemde waarde verwijdert uit alle beleidsregels die deze gebruiken.

Zie Een benoemde waarde verwijderen met behulp van de REST API voor informatie over het verwijderen van een benoemde waarde met [behulp van de REST-API.](/rest/api/apimanagement/2019-12-01/property/delete)

## <a name="to-search-and-filter-named-values"></a>Benoemde waarden zoeken en filteren

Het tabblad **Benoemde waarden** bevat zoek- en filtermogelijkheden om u te helpen bij het beheren van de benoemde waarden. Als u de lijst met benoemde waarden wilt filteren op naam, voert u een zoekterm in het tekstvak van de **eigenschap Zoeken** in. Als u alle benoemde waarden wilt weergeven, geeft u het tekstvak van de **eigenschap Zoeken** uit en drukt u op Enter.

Als u de lijst wilt filteren op tag, voert u een of meer tags in het tekstvak **Filter door tags** in. Als u alle benoemde waarden wilt weergeven, verwijdert u het tekstvak **Filteren op tags** en drukt u op Enter.

## <a name="to-use-a-named-value"></a>Een benoemde waarde gebruiken

Als u een benoemde waarde in een beleid wilt gebruiken, `{{ContosoHeader}}`plaatst u de naam in een dubbelpaar zoals , zoals in het volgende voorbeeld wordt weergegeven:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In dit `ContosoHeader` voorbeeld wordt gebruikt als de `set-header` naam van `ContosoHeaderValue` een koptekst in een beleid en wordt het gebruikt als de waarde van die koptekst. Wanneer dit beleid wordt geëvalueerd tijdens een aanvraag `{{ContosoHeader}}` of `{{ContosoHeaderValue}}` antwoord op de API-beheergateway en wordt vervangen door hun respectieve waarden.

Benoemde waarden kunnen worden gebruikt als volledige attribuut- of elementwaarden zoals in het vorige voorbeeld, maar ze kunnen ook worden ingevoegd in of gecombineerd met een deel van een letterlijke tekstexpressie zoals weergegeven in het volgende voorbeeld:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Benoemde waarden kunnen ook beleidsexpressies bevatten. In het volgende `ExpressionProperty` voorbeeld wordt het gebruikt.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Wanneer dit beleid `{{ExpressionProperty}}` wordt geëvalueerd, wordt `@(DateTime.Now.ToString())`vervangen door de waarde ervan: . Aangezien de waarde een beleidsexpressie is, wordt de expressie geëvalueerd en wordt het beleid uitgevoerd.

U dit testen in de ontwikkelaarsportal door een bewerking aan te roepen met een beleid met benoemde waarden in het bereik. In het volgende voorbeeld wordt een bewerking `set-header` aangeroepen met de twee vorige voorbeeldbeleidsregels met benoemde waarden. Houd er rekening mee dat het antwoord twee aangepaste kopteksten bevat die zijn geconfigureerd met behulp van beleidsregels met benoemde waarden.

![ontwikkelaarsportal][api-management-send-results]

Als u het [API-controlespoor](api-management-howto-api-inspector.md) bekijkt voor een aanroep met de twee vorige `set-header` voorbeeldbeleidsregels met benoemde waarden, ziet u de twee beleidsregels met de benoemde waarden die zijn ingevoegd, evenals de evaluatie van de beleidsexpressie voor de benoemde waarde die de beleidsexpressie bevatte.

![API-controletracering][api-management-api-inspector-trace]

Hoewel benoemde waarden beleidsexpressies kunnen bevatten, kunnen ze geen andere benoemde waarden bevatten. Als tekst met een benoemde waardeverwijzing wordt gebruikt `Text: {{MyProperty}}`voor een waarde, zoals, wordt die verwijzing niet opgelost en vervangen.

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over werken met beleid
    -   [Beleid in API-beheer](api-management-howto-policies.md)
    -   [Naslaginformatie over beleidsregels](/azure/api-management/api-management-policies)
    -   [Beleidsexpressies](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
