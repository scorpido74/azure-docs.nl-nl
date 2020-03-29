---
title: Toestemmingsprotocollen voor beheerders van Microsoft-identiteitsplatform | Microsoft Documenten
description: Een beschrijving van autorisatie in het eindpunt van het Microsoft-identiteitsplatform, inclusief scopes, machtigingen en toestemming.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e642ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b9d6ab0fc2f2bf500f17161de7e090a6f60c0feb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700733"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Beheerderstoestemming op het Microsoft-identiteitsplatform

Voor sommige machtigingen is toestemming van een beheerder vereist voordat deze binnen een tenant kunnen worden verleend.  U ook het eindpunt voor beheerderstoestemming gebruiken om machtigingen toe te kennen aan een hele tenant.  

## <a name="recommended-sign-the-user-into-your-app"></a>Aanbevolen: de gebruiker aanmelden bij uw app

Wanneer u een toepassing bouwt die gebruikmaakt van het eindpunt voor beheerderstoestemming, heeft de app doorgaans een pagina of weergave nodig waarin de beheerder de machtigingen van de app kan goedkeuren. Deze pagina kan deel uitmaken van de aanmeldingsstroom van de app, een deel van de instellingen van de app, of het kan een speciale "connect"-stroom zijn. In veel gevallen is het zinvol dat de app deze 'connect'-weergave pas weergeeft nadat een gebruiker zich heeft aangemeld met een Microsoft-account op het werk of op school.

Wanneer u de gebruiker aanmeldt bij uw app, u de organisatie identificeren waartoe de beheerder behoort voordat u hem of haar vraagt de benodigde machtigingen goed te keuren. Hoewel het niet strikt noodzakelijk is, kan het u helpen een intuïtievere ervaring te creëren voor uw organisatiegebruikers. Als u de gebruiker wilt aanmelden, volgt u onze zelfstudies van het [Microsoft-identiteitsplatformprotocol](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>De machtigingen aanvragen bij een directorybeheerder

Wanneer u klaar bent om machtigingen aan te vragen bij de beheerder van uw organisatie, u de gebruiker doorverwijzen naar het eindpunt van de *toestemming van*de microsoft-identiteitsplatformbeheerder.

```
// Line breaks are for legibility only.
    GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
    &scope=
    https://graph.microsoft.com/calendars.read 
    https://graph.microsoft.com/mail.send
```


| Parameter     | Voorwaarde     | Beschrijving                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Vereist | De directorytenant waarvan u toestemming wilt vragen. Kan worden verstrekt in GUID of vriendelijke naam `organizations` formaat of generiek verwezen met zoals te zien in het voorbeeld. Gebruik geen 'gewoons', omdat persoonlijke accounts geen beheerderstoestemming kunnen geven, behalve in de context van een tenant. Gebruik de tenant-id waar mogelijk om de beste compatibiliteit met persoonlijke accounts die huurders beheren, zo goed mogelijk te beheren. |
| `client_id` | Vereist | De **toepassings-id (client)** die de [Azure-portal – App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring toegewezen aan uw app. |
| `redirect_uri` | Vereist |De omleiding URI waar u wilt dat het antwoord wordt verzonden voor uw app te verwerken. Het moet precies overeenkomen met een van de omleiding URI's die u geregistreerd in de app registratie portal. |
| `state` | Aanbevolen | Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van elke inhoud die u wilt. Gebruik de status om informatie te coderen over de status van de gebruiker in de app voordat het verificatieverzoek is ingediend, zoals de pagina of weergave waarop ze zich bevonden. |
|`scope`        | Vereist      | Hiermee definieert u de set machtigingen die door de toepassing worden aangevraagd. Dit kan statisch zijn (met behulp van /.default) of dynamische scopes.  Dit kan de OIDC`openid`scopes ( , `profile`, ) `email`omvatten. | 


Op dit moment vereist Azure AD dat een tenantbeheerder zich aanmeldt om de aanvraag te voltooien. De beheerder wordt gevraagd alle machtigingen goed te keuren `scope` die u in de parameter hebt aangevraagd.  Als u een statische`/.default`( ) waarde hebt gebruikt, werkt deze zoals het eindpunt v1.0-beheerderstoestemming en vraagt u toestemming voor alle scopes die in de vereiste machtigingen voor de app zijn gevonden.

### <a name="successful-response"></a>Succesvolle reactie

Als de beheerder de machtigingen voor uw app goedkeurt, ziet het geslaagde antwoord er als volgt uit:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parameter         | Beschrijving                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| De directorytenant die uw aanvraag de door deze toepassing gevraagde machtigingen heeft verleend, in GUID-indeling.|
| `state`           | Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van elke inhoud die u wilt. De status wordt gebruikt om informatie over de status van de gebruiker in de app te coderen voordat het verificatieverzoek is ingediend, zoals de pagina of weergave waarop ze zich bevonden.|
| `scope`          | De set machtigingen die toegang kregen tot, voor de toepassing.|
| `admin_consent`   | Zal worden `True`ingesteld op .|

### <a name="error-response"></a>Foutreactie

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Bij het toevoegen aan de parameters gezien in een succesvolle reactie, fout parameters worden gezien als hieronder.

| Parameter          | Beschrijving                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren.|
| `error_description`| Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een fout kan identificeren.|
| `tenant`| De directorytenant die uw aanvraag de door deze toepassing gevraagde machtigingen heeft verleend, in GUID-indeling.|
| `state`           | Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van elke inhoud die u wilt. De status wordt gebruikt om informatie over de status van de gebruiker in de app te coderen voordat het verificatieverzoek is ingediend, zoals de pagina of weergave waarop ze zich bevonden.|
| `admin_consent`   | Wordt ingesteld `True` om aan te geven dat dit antwoord heeft plaatsgevonden op een beheerderstoestemmingsstroom.|

## <a name="next-steps"></a>Volgende stappen
- Bekijk [hoe u een app converteert naar multitenant](howto-convert-app-to-be-multi-tenant.md)
- Lees hoe [toestemming wordt ondersteund op de OAuth 2.0-protocollaag tijdens de autorisatiecode-subsidiestroom](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Ontdek [hoe een multi-tenanttoepassing het toestemmingskader kan gebruiken](active-directory-devhowto-multi-tenant-overview.md) om toestemming voor 'gebruiker' en 'beheerder' te implementeren, ter ondersteuning van geavanceerdere toepassingspatronen met meerdere lagen.
- Inzicht in [azure AD-gebruikersdetoestemmingservaringen](application-consent-experience.md)
