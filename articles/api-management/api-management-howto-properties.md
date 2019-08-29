---
title: Benoemde waarden gebruiken in azure API Management-beleid
description: Meer informatie over het gebruik van benoemde waarden in azure API Management-beleid.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/22/2019
ms.author: apimpm
ms.openlocfilehash: d71d71c4d289235e5b67a5201c1f7417274b8fca
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072331"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Benoemde waarden gebruiken in azure API Management-beleid

API Management-beleid is een krachtige mogelijkheid van het systeem waarmee de Azure Portal het gedrag van de API via configuratie kan wijzigen. Beleidsregels zijn een verzameling instructies die sequentieel worden uitgevoerd op de aanvraag of het antwoord van een API. Beleids instructies kunnen worden samengesteld met behulp van letterlijke tekst waarden, beleids expressies en benoemde waarden.

Elk API Management service-exemplaar heeft een eigenschappen verzameling van sleutel/waarde-paren, die een benoemde waarde wordt genoemd en die globaal zijn voor het service-exemplaar. Er is geen limiet ingesteld voor het aantal items in de verzameling. Benoemde waarden kunnen worden gebruikt voor het beheren van constante teken reeks waarden voor alle API-configuraties en-beleid. Elke benoemde waarde kan de volgende kenmerken hebben:

| Kenmerk      | type            | Description                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | Gebruikt voor het verwijzen naar de eigenschap in beleid. Een teken reeks van 1 tot 256 tekens. Alleen letters, cijfers, punten en streepjes zijn toegestaan. |
| `Value`        | string          | Werkelijke waarde. Mag niet leeg zijn of alleen uit spaties bestaan. Maxi maal 4096 tekens lang.                                     |
| `Secret`       | boolean         | Hiermee wordt bepaald of de waarde een geheim is en moet worden versleuteld of niet.                                                            |
| `Tags`         | tekenreeksmatrix | Wordt gebruikt om de eigenschappen lijst te filteren. Maxi maal 32 tags.                                                                                    |

![Benoemde waarden](./media/api-management-howto-properties/named-values.png)

Benoemde waarden kunnen letterlijke teken reeksen en [beleids expressies](/azure/api-management/api-management-policy-expressions)bevatten. De waarde van `Expression` is bijvoorbeeld een beleids expressie die een teken reeks retourneert met de huidige datum en tijd. De benoemde waarde `Credential` is gemarkeerd als geheim, waardoor de waarde ervan niet standaard wordt weer gegeven.

| Name       | `Value`                      | `Secret` | Labels          |
| ---------- | -------------------------- | ------ | ------------- |
| Value      | 42                         | False  | cruciale cijfers |
| Referentie | ••••••••••••••••••••••     | Waar   | beveiliging      |
| Expressie | @(DateTime.Now.ToString()) | False  |               |

## <a name="to-add-and-edit-a-property"></a>Een eigenschap toevoegen en bewerken

![Een eigenschap toevoegen](./media/api-management-howto-properties/add-property.png)

1. Selecteer **API's** bij **API MANAGEMENT**.
2. Selecteer **benoemde waarden**.
3. Druk op **+ toevoegen**.

    Naam en waarde zijn vereiste waarden. Als deze eigenschaps waarde een geheim is, schakelt u het selectie vakje Dit is een geheim in. Voer een of meer optionele Tags in om u te helpen bij het ordenen van uw benoemde waarden en klik op opslaan.

4. Klik op **Create**.

Als de eigenschap eenmaal is gemaakt, kunt u deze bewerken door te klikken op de eigenschap. Als u de naam van de eigenschap wijzigt, worden alle beleids regels die verwijzen naar die eigenschap automatisch bijgewerkt voor gebruik van de nieuwe naam.

Zie [een eigenschap bewerken met behulp van de rest API](/rest/api/apimanagement/2019-01-01/property?patch)voor meer informatie over het bewerken van een eigenschap met behulp van de rest API.

## <a name="to-delete-a-property"></a>Een eigenschap verwijderen

Als u een eigenschap wilt verwijderen, klikt u op **verwijderen** naast de eigenschap die u wilt verwijderen.

> [!IMPORTANT]
> Als ernaar wordt verwezen door een beleids regels, kunt u de eigenschap pas verwijderen nadat u de eigenschap hebt verwijderd van alle beleids regels die deze gebruiken.

Zie [een eigenschap verwijderen met de rest API](/rest/api/apimanagement/2019-01-01/property/delete)voor meer informatie over het verwijderen van een eigenschap met behulp van de rest API.

## <a name="to-search-and-filter-named-values"></a>Benoemde waarden zoeken en filteren

Het tabblad **benoemde waarden** bevat Zoek-en filter functies waarmee u uw benoemde waarden kunt beheren. Als u de eigenschappen lijst wilt filteren op eigenschaps naam, voert u een zoek term in het tekstvak **Zoek eigenschap** in. Als u alle benoemde waarden wilt weer geven, schakelt u het tekstvak **Zoek eigenschap** uit en drukt u op ENTER.

Als u de eigenschappen lijst wilt filteren op label waarden, voert u een of meer tags in het tekstvak **filteren op labels** in. Als u alle benoemde waarden wilt weer geven, schakelt u het tekstvak **filteren op labels** uit en drukt u op ENTER.

## <a name="to-use-a-property"></a>Een eigenschap gebruiken

Als u een eigenschap in een beleid wilt gebruiken, plaatst u de naam van de eigenschap binnen een paar `{{ContosoHeader}}`accolades, zoals wordt weer gegeven in het volgende voor beeld:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In dit voor beeld `ContosoHeader` wordt gebruikt als de naam van een koptekst in een `set-header` beleid en `ContosoHeaderValue` wordt gebruikt als de waarde van die koptekst. Wanneer dit beleid wordt geëvalueerd tijdens een aanvraag of reactie op de API Management Gateway, `{{ContosoHeader}}` en `{{ContosoHeaderValue}}` worden vervangen door hun respectieve eigenschaps waarden.

Benoemde waarden kunnen worden gebruikt als volledige kenmerk-of element waarden, zoals wordt weer gegeven in het vorige voor beeld, maar ze kunnen ook worden ingevoegd in of gecombineerd met een letterlijke tekst expressie, zoals wordt weer gegeven in het volgende voor beeld:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Benoemde waarden kunnen ook beleids expressies bevatten. In het volgende voor beeld wordt `ExpressionProperty` de gebruikt.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Wanneer dit beleid wordt geëvalueerd, `{{ExpressionProperty}}` wordt vervangen door de waarde: `@(DateTime.Now.ToString())`. Omdat de waarde een beleids expressie is, wordt de expressie geëvalueerd en wordt het beleid voortgezet met de uitvoering ervan.

U kunt dit testen in de ontwikkelaars portal door een bewerking aan te roepen die een beleid bevat met benoemde waarden binnen het bereik. In het volgende voor beeld wordt een bewerking aangeroepen met de twee voor gaande `set-header` voorbeeld beleidsregels met benoemde waarden. Houd er rekening mee dat het antwoord twee aangepaste kopteksten bevat die zijn geconfigureerd met behulp van beleids regels met benoemde waarden.

![Ontwikkelaarsportal][api-management-send-results]

Als u de [API Inspector](api-management-howto-api-inspector.md) -tracering bekijkt voor een aanroep die de twee voor gaande voorbeeld beleidsregels met benoemde waarden bevat, ziet u `set-header` de twee beleids regels met de waarden van de eigenschap en de evaluatie van beleids expressies voor de eigenschap die bevat de beleids expressie.

![API Inspector-tracering][api-management-api-inspector-trace]

Hoewel eigenschaps waarden beleids expressies kunnen bevatten, kunnen eigenschaps waarden geen andere benoemde waarden bevatten. Als tekst met een eigenschaps verwijzing wordt gebruikt voor een eigenschaps waarde, `Property value text {{MyProperty}}`zoals, wordt die eigenschaps verwijzing niet vervangen en wordt deze opgenomen als onderdeel van de waarde van de eigenschap.

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over het werken met beleids regels
    -   [Beleid in API Management](api-management-howto-policies.md)
    -   [Naslaginformatie over beleidsregels](/azure/api-management/api-management-policies)
    -   [Beleidsexpressies](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
