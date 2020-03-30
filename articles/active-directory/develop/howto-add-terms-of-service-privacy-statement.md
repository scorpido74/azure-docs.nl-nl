---
title: Servicevoorwaarden en privacyverklaring voor apps | Azure
description: Meer informatie over hoe u de servicevoorwaarden en privacyverklaring configureren voor apps die zijn geregistreerd om Azure AD te gebruiken.
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
ms.openlocfilehash: 8fc85781f139b45e9e37f6e0f7cc36974041352d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300006"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>How to: Servicevoorwaarden en privacyverklaring configureren voor een app

Ontwikkelaars die apps bouwen en beheren die zijn geïntegreerd met Azure Active Directory (Azure AD) en Microsoft-accounts, moeten koppelingen bevatten naar de servicevoorwaarden en privacyverklaring van de app. De servicevoorwaarden en privacyverklaring worden aan gebruikers opgedoken via de gebruikerstoestemmingservaring. Ze helpen uw gebruikers te weten dat ze uw app kunnen vertrouwen. De servicevoorwaarden en de privacyverklaring zijn vooral van cruciaal belang voor gebruikersgerichte multi-tenant-apps - apps die door meerdere mappen worden gebruikt of beschikbaar zijn voor een Microsoft-account.

U bent verantwoordelijk voor het maken van de servicevoorwaarden en privacyverklaringdocumenten voor uw app en voor het verstrekken van de URL's aan deze documenten. Voor apps met meerdere tenants die deze koppelingen niet bieden, wordt in de gebruikerstoestemmingservaring voor uw app een waarschuwing weergegeven, waardoor gebruikers kunnen worden ontmoedigd om in te stemmen met uw app.

> [!NOTE]
> * Apps met één huurder worden niet weergegeven.
> * Als een of beide koppelingen ontbreken, wordt er een waarschuwing weergegeven in uw app.

## <a name="user-consent-experience"></a>Gebruikerstoestemmingservaring

In de volgende voorbeelden wordt de gebruikerstoestemmingservaring weergegeven wanneer de servicevoorwaarden en de privacyverklaring zijn geconfigureerd en wanneer deze koppelingen niet zijn geconfigureerd.

![Screenshots met en zonder privacyverklaring en servicevoorwaarden](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Koppelingen naar de servicevoorwaarden en privacyverklaringdocumenten opmaken

Voordat u koppelingen toevoegt aan de servicevoorwaarden en privacyverklaringdocumenten van uw app, moet u ervoor zorgen dat de URL's deze richtlijnen volgen.

| Richtlijn     | Beschrijving                           |
|---------------|---------------------------------------|
| Indeling        | Geldige URL                             |
| Geldige schema's | HTTP en HTTPS<br/>Wij raden HTTPS aan |
| Max. lengte    | 2048 tekens                       |

Voorbeelden: `https://myapp.com/terms-of-service` en`https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Links toevoegen aan de servicevoorwaarden en privacyverklaring

Wanneer de servicevoorwaarden en de privacyverklaring klaar zijn, u koppelingen naar deze documenten toevoegen aan deze documenten in uw app met behulp van een van de volgende methoden:

* [Via de Azure-portal](#azure-portal)
* [Het app-object JSON gebruiken](#app-object-json)
* [De Microsoft Graph-API gebruiken](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>De Azure-portal gebruiken
Volg deze stappen in de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Navigeer naar de sectie **App-registraties** en selecteer uw app.
3. Open het deelvenster **Huisstijl.**
4. Vul de **URL-voorwaarden** en **de URL-voorwaarden van de privacyverklaring** in.
5. Sla uw wijzigingen op.

    ![App-eigenschappen bevatten servicevoorwaarden en privacy-URL's](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>Het app-object JSON gebruiken

Als u het app-object JSON rechtstreeks wilt wijzigen, u de manifesteditor in de Azure-portal of toepassingsregistratieportal gebruiken om koppelingen naar de servicevoorwaarden en privacyverklaring van uw app op te nemen.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>De Microsoft Graph-API gebruiken

Als u al uw apps programmatisch wilt bijwerken, u de Microsoft Graph API gebruiken om al uw apps bij te werken om koppelingen naar de servicevoorwaarden en privacyverklaringdocumenten op te nemen.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
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
> * Zorg ervoor dat u geen reeds bestaande waarden overschrijft die `supportUrl` `marketingUrl`u aan een van deze velden hebt toegewezen: , en`logoUrl`
> * De Microsoft Graph API werkt alleen wanneer u zich aanmeldt met een Azure AD-account. Persoonlijke Microsoft-accounts worden niet ondersteund.
