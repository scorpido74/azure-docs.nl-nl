---
title: Validatieverschillen door ondersteunde accounttypen - Microsoft-identiteitsplatform | Azure
description: Meer informatie over de validatieverschillen van verschillende eigenschappen voor verschillende ondersteunde accounttypen bij het registreren van uw app bij het Microsoft-identiteitsplatform.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128863"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Validatieverschillen door ondersteunde accounttypen (signInAudience)

Wanneer u een toepassing registreert bij het Microsoft-identiteitsplatform voor ontwikkelaars, wordt u gevraagd te selecteren welke accounttypen uw toepassing ondersteunt. In het toepassingsobject en manifest `signInAudience`is deze eigenschap .

De opties zijn onder andere:

- *AzureADMyOrg*: Alleen accounts in de organisatiemap waar de app is geregistreerd (single-tenant)
- *AzureADMultipleOrgs:* Accounts in een organisatiemap (multi-tenant)
- *AzureADandPersonalMicrosoftAccount:* accounts in een organisatiemap (multi-tenant) en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox en Outlook.com)

Voor geregistreerde toepassingen vindt u de waarde voor ondersteunde accounttypen in de sectie **Verificatie** van een toepassing. U het ook `signInAudience` vinden onder het pand in het **Manifest**.

De waarde die u voor deze eigenschap selecteert, heeft gevolgen voor andere eigenschappen van app-objecten. Als u deze eigenschap wijzigt, moet u mogelijk eerst andere eigenschappen wijzigen.

Zie de volgende tabel voor de validatieverschillen van verschillende eigenschappen voor verschillende ondersteunde accounttypen.

| Eigenschap | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` en `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Applicatie-ID`identifierURIs`URI ( )  | Moet uniek zijn in de huurder <br><br> urn:// regelingen worden ondersteund <br><br> Jokertekens worden niet ondersteund <br><br> Querytekenreeksen en -fragmenten worden ondersteund <br><br> Maximale lengte van 255 tekens <br><br> Geen limiet* op aantal identifierURI's  | Moet wereldwijd uniek zijn <br><br> urn:// regelingen worden ondersteund <br><br> Jokertekens worden niet ondersteund <br><br> Querytekenreeksen en -fragmenten worden ondersteund <br><br> Maximale lengte van 255 tekens <br><br> Geen limiet* op aantal identifierURI's | Moet wereldwijd uniek zijn <br><br> urn:// regelingen worden niet ondersteund <br><br> Jokertekens, fragmenten en querytekenreeksen worden niet ondersteund <br><br> Maximale lengte van 120 tekens <br><br> Maximaal 50 identifierURI's |
| Certificaten`keyCredentials`( ) | Symmetrische ondertekeningstoets | Symmetrische ondertekeningstoets | Versleuteling en asymmetrische ondertekeningssleutel | 
| Klantgeheimen`passwordCredentials`( ) | Geen limiet* | Geen limiet* | Als liveSDK is ingeschakeld: Maximaal 2 klantgeheimen | 
| URI's`replyURLs`omleiden ( ) | Zie [URL-beperkingen en beperkingen voor uri/reply omleiden](reply-url.md) voor meer informatie. | | | 
| API-machtigingen`requiredResourceAccess`( ) | Geen limiet* | Geen limiet* | Maximaal 30 machtigingen per toegestane resource (bijvoorbeeld Microsoft Graph) | 
| Scopes gedefinieerd door deze`oauth2Permissions`API ( ) | Maximale bereiknaamlengte van 120 tekens <br><br> Geen limiet* op het aantal gedefinieerde scopes | Maximale bereiknaamlengte van 120 tekens <br><br> Geen limiet* op het aantal gedefinieerde scopes |  Maximale bereiknaamlengte van 40 tekens <br><br> Maximaal 100 omschreven scopes | 
| Geautoriseerde clienttoepassingen`preautorizedApplications`( ) | Geen limiet* | Geen limiet* | Totaal maximum van 500 <br><br> Maximaal 100 client-apps gedefinieerd <br><br> Maximaal 30 scopes gedefinieerd per client | 
| appRollen | Ondersteund <br> Geen limiet* | Ondersteund <br> Geen limiet* | Niet ondersteund | 
| Afmeldings-URL | http://localhostis toegestaan <br><br> Maximale lengte van 255 tekens | http://localhostis toegestaan <br><br> Maximale lengte van 255 tekens | <br><br> https://localhostis toegestaan, http://localhost mislukt voor MSA <br><br> Maximale lengte van 255 tekens <br><br> HTTP-regeling is niet toegestaan <br><br> Jokertekens worden niet ondersteund | 

*Er is een globale limiet van ongeveer 1000 items in alle verzameleigenschappen op het app-object

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [toepassingsregistratie](app-objects-and-service-principals.md)
- Meer informatie over het [toepassingsmanifest](reference-app-manifest.md)
