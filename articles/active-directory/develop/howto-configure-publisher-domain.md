---
title: Het uitgeversdomein van een app configureren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het configureren van het uitgeversdomein van een toepassing om gebruikers te laten weten waar hun gegevens worden verzonden.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697129"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>How to: Het uitgeversdomein van een toepassing configureren

Het uitgeversdomein van een toepassing wordt weergegeven aan gebruikers op de [toestemmingsprompt van](application-consent-experience.md) de toepassing om gebruikers te laten weten waar hun informatie wordt verzonden. Multi-tenant toepassingen die zijn geregistreerd na 21 mei 2019 en die geen uitgeversdomein hebben, worden weergegeven als **niet-geverifieerd.** Multitenant-toepassingen zijn toepassingen die accounts buiten één organisatiemap ondersteunen; Besteun bijvoorbeeld alle Azure AD-accounts of ondersteun alle Azure AD-accounts en persoonlijke Microsoft-accounts.

## <a name="new-applications"></a>Nieuwe toepassingen

Wanneer u een nieuwe app registreert, wordt het uitgeversdomein van uw app mogelijk ingesteld op een standaardwaarde. De waarde is afhankelijk van waar de app is geregistreerd, met name of de app is geregistreerd in een tenant en of de tenant domeinen heeft geverifieerd.

Als er domeinen met tenant zijn geverifieerd, wordt het uitgeversdomein van de app standaard ingesteld op het primaire geverifieerde domein van de tenant. Als er geen tenant geverifieerde domeinen zijn (wat het geval is wanneer de toepassing niet is geregistreerd in een tenant), wordt het uitgeversdomein van de app ingesteld op null.

In de volgende tabel wordt het standaardgedrag van de waarde van het uitgeversdomein samengevat.  

| Tenant-geverifieerde domeinen | Standaardwaarde van uitgeversdomein |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (primair) | domain2.com |

Als het uitgeversdomein van een multitenant-toepassing niet is ingesteld of als het is ingesteld op een domein dat eindigt op .onmicrosoft.com, wordt de toestemmingsprompt van de app **niet geverifieerd** in plaats van het uitgeversdomein weergegeven.

## <a name="grandfathered-applications"></a>Grandfathered toepassingen

Als uw app vóór 21 mei 2019 is geregistreerd, wordt de toestemmingsprompt van uw toepassing niet **geverifieerd** als u geen uitgeversdomein hebt ingesteld. We raden u aan de waarde van het uitgeversdomein in te stellen, zodat gebruikers deze informatie kunnen zien op de toestemmingsprompt van uw app.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Publisher-domein configureren met de Azure-portal

Voer deze stappen uit om het uitgeversdomein van uw app in te stellen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.

1. Als uw account aanwezig is in meer dan één Azure AD-tenant:
   1. Selecteer uw profiel in het menu rechtsboven op de pagina en **schakel de map over.**
   1. Wijzig uw sessie in de Azure AD-tenant waar u uw toepassing wilt maken.

1. Navigeer naar [Azure Active Directory >-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) om de app te zoeken en te selecteren die u wilt configureren.

   Zodra je de app hebt geselecteerd, zie je de **overzichtspagina** van de app.

1. Selecteer op de **pagina Overzicht** van de app de sectie **Branding.**

1. Zoek het **domeinveld Publisher** en selecteer een van de volgende opties:

   - Selecteer **Een domein configureren** als u nog geen domein hebt geconfigureerd.
   - Selecteer **Domein bijwerken** als een domein al is geconfigureerd.

Als uw app is geregistreerd in een tenant, ziet u twee tabbladen om uit te kiezen: **Selecteer een geverifieerd domein** en **Verifieer een nieuw domein.**

Als uw app niet is geregistreerd in een tenant, ziet u alleen de optie om een nieuw domein voor uw toepassing te verifiëren.

### <a name="to-verify-a-new-domain-for-your-app"></a>Een nieuw domein voor uw app verifiëren

1. Maak een `microsoft-identity-association.json` bestand met de naam en plak het volgende JSON-codefragment.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Vervang de tijdelijke aanduiding *{YOUR-APP-ID-HERE}* door de toepassings-id (client) id die overeenkomt met uw app.

1. Host het bestand `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`op: . Vervang de tijdelijke aanduiding *{YOUR-DOMAIN-HERE}* om overeen te komen met het geverifieerde domein.

1. Klik op de knop **Domein verifiëren en opslaan.**

### <a name="to-select-a-verified-domain"></a>Een geverifieerd domein selecteren

- Als uw tenant domeinen heeft geverifieerd, selecteert u een van de domeinen in de vervolgkeuzelijst **Een geverifieerd domein** selecteren.

>[!Note]
> De verwachte koptekst 'Inhoudstype' `application/json`die moet worden geretourneerd, is . U een fout krijgen zoals hieronder vermeld als u iets anders zoals`application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Implicaties voor de toestemmingsprompt voor apps

Het configureren van het uitgeversdomein heeft een impact op wat gebruikers zien op de toestemmingsprompt van de app. Zie De ervaringen met [de toestemmingstoestemming](application-consent-experience.md)begrijpen om de onderdelen van de toestemming volledig te begrijpen.

In de volgende tabel wordt het gedrag beschreven voor toepassingen die vóór 21 mei 2019 zijn gemaakt.

![Toestemmingsprompt voor apps die vóór 21 mei 2019 zijn gemaakt](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Het gedrag voor nieuwe toepassingen die na 21 mei 2019 zijn gemaakt, is afhankelijk van het uitgeversdomein en het type toepassing. In de volgende tabel worden de wijzigingen beschreven die u mag verwachten met de verschillende combinaties van configuraties.

![Toestemmingsprompt voor apps die na 21 mei 2019 zijn gemaakt](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicaties voor omleiding URI's

Toepassingen die zich aanmelden bij gebruikers met een werk- of schoolaccount of persoonlijke[Microsoft-accounts (multi-tenant)](single-and-multi-tenant-apps.md)zijn onderworpen aan weinig beperkingen bij het opgeven van omleidings-URI's.

### <a name="single-root-domain-restriction"></a>Beperking van één hoofddomein

Wanneer de waarde van het uitgeversdomein voor apps met meerdere tenant is ingesteld op null, zijn apps beperkt tot het delen van één hoofddomein voor de omleidings-URI's. De volgende combinatie van waarden is bijvoorbeeld niet toegestaan omdat het hoofddomein, contoso.com, niet overeenkomt met fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Beperkingen van subdomein

Subdomeinen zijn toegestaan, maar u moet het hoofddomein expliciet registreren. Terwijl de volgende URI's bijvoorbeeld één hoofddomein delen, is de combinatie niet toegestaan.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Als de ontwikkelaar echter expliciet het hoofddomein toevoegt, is de combinatie toegestaan.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Uitzonderingen

In de volgende gevallen geldt geen beperking van het enkele hoofddomein:

- Apps met één tenant of apps die accounts in één map targeten
- Gebruik van localhost als omleidings-URI's
- URI's omleiden met aangepaste schema's (niet-HTTP of HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Publisher-domein programmatisch configureren

Momenteel is er geen REST API- of PowerShell-ondersteuning om publisher-domein programmatisch te configureren.
