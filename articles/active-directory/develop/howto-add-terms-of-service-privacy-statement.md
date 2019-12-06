---
title: Service voorwaarden en privacyverklaring voor apps | Azure
description: Meer informatie over hoe u de service voorwaarden en de privacyverklaring kunt configureren voor apps die zijn geregistreerd voor gebruik van Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca59120bcfadc6c75e3687be84a0e70f97fc3c2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842799"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Procedure: Service voorwaarden en de privacyverklaring voor een app configureren

Ontwikkel aars die apps die integreren met Azure Active Directory (Azure AD) en micro soft-accounts maken en beheren, moeten koppelingen bevatten naar de service voorwaarden van de app en de privacyverklaring. De service voorwaarden en de privacyverklaring worden aan gebruikers door gegeven via de toestemming van de gebruiker. Ze helpen uw gebruikers te weten dat ze uw app kunnen vertrouwen. De service voorwaarden en de privacyverklaring zijn met name van belang voor gebruikers gerichte multi tenant-apps, apps die worden gebruikt door meerdere mappen of die beschikbaar zijn voor elke Microsoft-account.

U bent verantwoordelijk voor het maken van de service voorwaarden en documenten voor de privacyverklaring voor uw app, en voor het leveren van de Url's voor deze documenten. Voor apps met meerdere tenants die deze koppelingen niet bieden, wordt door de gebruikers toestemming voor uw app een waarschuwing weer gegeven. Dit kan ertoe leiden dat gebruikers niet kunnen worden teruggestuurd naar uw app.

> [!NOTE]
> * Voor apps met één Tenant wordt geen waarschuwing weer gegeven.
> * Als een van de twee koppelingen ontbreekt, wordt een waarschuwing weer gegeven in uw app.

## <a name="user-consent-experience"></a>Gebruikers ervaring met toestemming

In de volgende voor beelden ziet u de toestemming van de gebruiker wanneer de service voorwaarden en de privacyverklaring zijn geconfigureerd en wanneer deze koppelingen niet zijn geconfigureerd.

![Scherm afbeeldingen met en zonder een privacyverklaring en de geleverde service voorwaarden](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Opmaak van koppelingen naar de documenten voor de service voorwaarden en de privacyverklaring

Zorg ervoor dat de Url's voldoen aan de volgende richt lijnen voordat u koppelingen naar de documenten voor de service voorwaarden en de privacyverklaring van uw app toevoegt.

| Richtlijn     | Beschrijving                           |
|---------------|---------------------------------------|
| Indeling        | Geldige URL                             |
| Geldige schema's | HTTP en HTTPS<br/>U kunt het beste HTTPS |
| Max. lengte    | 2048 tekens                       |

Voor beelden: `https://myapp.com/terms-of-service` en `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Koppelingen toevoegen aan de service voorwaarden en de privacyverklaring

Wanneer de service voorwaarden en de privacyverklaring gereed zijn, kunt u aan de hand van een van de volgende methoden koppelingen naar deze documenten toevoegen aan uw app:

* [Via de Azure Portal](#azure-portal)
* [De JSON van het app-object gebruiken](#app-object-json)
* [De MSGraph bèta-REST API gebruiken](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>De Azure Portal gebruiken
Volg deze stappen in de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Ga naar de sectie **app-registraties** en selecteer uw app.
3. Open het deel venster **huis stijl** .
4. Vul de URL-velden **voor de service voorwaarden** en de **privacyverklaring** in.
5. Sla uw wijzigingen op.

    ![App-eigenschappen bevatten de Url's van de service voorwaarden en de privacyverklaring](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>De JSON van het app-object gebruiken

Als u de JSON van het app-object liever rechtstreeks wilt wijzigen, kunt u de manifest editor in het Azure Portal of de portal voor toepassings registratie gebruiken om koppelingen op te geven naar de service voorwaarden van uw app en de privacyverklaring.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>De MSGraph bèta-REST API gebruiken

Als u al uw apps op een programmatische manier wilt bijwerken, kunt u de MSGraph Beta REST API gebruiken om alle apps bij te werken zodat deze koppelingen bevatten naar de service voorwaarden en de privacyverklaring.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Zorg ervoor dat u geen vooraf bestaande waarden overschrijft die u hebt toegewezen aan een van deze velden: `supportUrl`, `marketingUrl`en `logoUrl`
> * De MSGraph Beta REST API werkt alleen wanneer u zich aanmeldt met een Azure AD-account. Persoonlijke micro soft-accounts worden niet ondersteund.
