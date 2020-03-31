---
title: Toepassingstypen die worden ondersteund door Azure AD B2C
titleSuffix: Azure AD B2C
description: Meer informatie over de typen toepassingen die u gebruiken met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e8328db12bde531c2e27936c09247611ff1a3583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190140"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Toepassingstypen die kunnen worden gebruikt in Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ondersteunt verificatie voor verschillende moderne toepassingsarchitecturen. Ze zijn allemaal gebaseerd op de protocollen volgens de industrienorm [OAuth 2.0](protocols-overview.md) of [OpenID Connect](protocols-overview.md). In dit artikel worden de typen toepassingen beschreven die u bouwen, onafhankelijk van de taal of het platform dat u verkiest. Het document geeft ook inzicht in geavanceerde scenario's voordat u toepassingen gaat ontwikkelen.

Elke toepassing die Azure AD B2C gebruikt, moet worden geregistreerd in uw [Azure AD B2C-tenant](tutorial-create-tenant.md) met behulp van de [Azure-portal.](https://portal.azure.com/) Het registratieproces van de toepassing verzamelt en wijst waarden toe, zoals:

* Een **toepassings-id** die uw toepassing op een unieke basis identificeert.
* Een **url voor het beantwoorden** die kan worden gebruikt om reacties terug te sturen naar uw toepassing.

Elk verzoek dat naar Azure AD B2C wordt verzonden, geeft een **gebruikersstroom** (een ingebouwd beleid) of een **aangepast beleid** op dat het gedrag van Azure AD B2C regelt. Met beide beleidstypen u een zeer aanpasbare set gebruikerservaringen maken.

De interactie van elke toepassing volgt in grote lijnen hetzelfde patroon:

1. De toepassing leidt de gebruiker naar het v2.0-eindpunt om een [beleid](user-flow-overview.md)uit te voeren.
2. De gebruiker voltooit het beleid volgens de beleidsdefinitie.
3. De toepassing ontvangt een beveiligingstoken van het v2.0-eindpunt.
4. De toepassing gebruikt het beveiligingstoken om toegang te krijgen tot beveiligde informatie of een beveiligde bron.
5. De resource-server valideert het beveiligingstoken om te controleren of toegang kan worden verleend.
6. Het beveiligingstoken wordt regelmatig vernieuwd door de toepassing.

Deze stappen kunnen enigszins verschillen op basis van het type toepassing dat u bouwt.

## <a name="web-applications"></a>Webtoepassingen

Voor webtoepassingen (waaronder .NET, PHP, Java, Ruby, Python en Node.js) die op een server worden gehost en toegankelijk zijn via een browser, ondersteunt Azure AD B2C [OpenID Connect](protocols-overview.md) voor alle gebruikerservaringen. In de Azure AD B2C-implementatie van OpenID Connect initieert uw webtoepassing gebruikerservaringen door verificatieverzoeken uit te geven aan Azure AD. Het resultaat van de aanvraag is een `id_token`. Dit beveiligingstoken vertegenwoordigt de identiteit van de gebruiker. Het bevat ook informatie over de gebruiker in de vorm van claims:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Meer informatie over de typen tokens en claims die beschikbaar zijn voor een toepassing in de [ab2C-tokenverwijzing](tokens-overview.md)voor Azure.

In een webtoepassing voert elke uitvoering van een [beleid](user-flow-overview.md) de volgende stappen op hoog niveau:

1. De gebruiker bladert naar de webapplicatie.
2. De webtoepassing leidt de gebruiker door naar Azure AD B2C, waarin het uit te voeren beleid wordt aangegeven.
3. De gebruiker vult het beleid in.
4. Azure AD B2C `id_token` retourneert een naar de browser.
5. Het `id_token` is geplaatst op de omleiding URI.
6. De `id_token` cookie wordt gevalideerd en een sessiecookie geplaatst.
7. Een beveiligde pagina wordt teruggestuurd naar de gebruiker.

Validatie van het `id_token` met behulp van een openbare ondertekeningssleutel die is ontvangen van Azure AD is voldoende om de identiteit van de gebruiker te controleren. Deze procedure stelt ook een sessiecookie in die kan worden gebruikt om de gebruiker te identificeren op volgende paginaverzoeken.

Als u dit scenario in actie wilt zien, probeert u een van de inlogcodevoorbeelden van de webtoepassing in [onze sectie Aan de slag.](overview.md)

Naast het vergemakkelijken van eenvoudige aanmelding, moet een webservertoepassing mogelijk ook toegang krijgen tot een back-endwebservice. In dit geval kan de webtoepassing een iets andere [OpenID Connect-stroom](openid-connect.md) uitvoeren en tokens verkrijgen met autorisatiecodes en vernieuwingstokens. Dit scenario wordt beschreven in de volgende sectie over [Web-API's](#web-apis).

## <a name="web-apis"></a>Web-API's

U Azure AD B2C gebruiken om webservices te beveiligen, zoals de RESTful-web-API van uw toepassing. Web-API's kunnen OAuth 2.0 gebruiken om hun gegevens te beveiligen door verificatie van binnenkomende HTTP-aanvragen via tokens. De aanroeper van een web-API voegt een token toe in de autorisatie-header van een HTTP-aanvraag:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

De web-API kan het token vervolgens gebruiken om de identiteit van de API-aanroeper te verifiëren en om informatie over de aanroeper af te leiden uit de claims die in het token zijn gecodeerd. In de [Naslaginformatie over Azure AD B2C-tokens](tokens-overview.md) vindt u meer informatie over de typen tokens en claims die beschikbaar zijn voor een app.

Een web-API kan tokens ontvangen van vele soorten clients, waaronder webtoepassingen, desktop- en mobiele toepassingen, toepassingen aan één pagina, daemons aan de serverzijde en andere web-API's. Hier is een voorbeeld van de volledige stroom voor een webtoepassing die een web-API aanroept:

1. De webtoepassing voert een beleid uit en de gebruiker voltooit de gebruikerservaring.
2. Azure AD B2C retourneert `id_token` een (OpenID Connect) en een autorisatiecode naar de browser.
3. De browser `id_token` plaatst de autorisatiecode op de omleidinguri.
4. De webserver valideert `id_token` de sessiecookie en stelt een sessiecookie in.
5. De webserver vraagt Azure AD B2C om een `access_token` door deze te voorzien van de autorisatiecode, toepassingsclient-id en clientreferenties.
6. De `access_token` `refresh_token` en worden teruggestuurd naar de webserver.
7. De web-API wordt `access_token` aangeroepen met de in een autorisatieheader.
8. De web-API valideert het token.
9. Beveiligde gegevens worden teruggestuurd naar de webtoepassing.

Voor meer informatie over autorisatiecodes, vernieuwingstokens en de stappen voor het ophalen van tokens, leest u over het [OAuth 2.0-protocol](authorization-code-flow.md).

Voor meer informatie over het beveiligen van een web-API met behulp van Azure AD B2C bekijkt u de web-API-zelfstudies in de sectie [Aan de slag](overview.md).

## <a name="mobile-and-native-applications"></a>Mobiele en native applicaties

Toepassingen die zijn geïnstalleerd op apparaten, zoals mobiele en desktoptoepassingen, moeten vaak namens gebruikers toegang krijgen tot back-endservices of web-API's. U aangepaste identiteitsbeheerervaringen toevoegen aan uw eigen toepassingen en veilig back-endservices bellen met Azure AD B2C en de [OAuth 2.0-autorisatiecodestroom.](authorization-code-flow.md)

In deze stroom voert [policies](user-flow-overview.md) de toepassing `authorization_code` beleid uit en ontvangt deze een van Azure AD nadat de gebruiker het beleid heeft voltooid. De `authorization_code` aanvraag geeft toestemming voor het bellen van back-endservices namens de gebruiker die momenteel is aangemeld. De toepassing kan `authorization_code` dan de op `access_token` de `refresh_token`achtergrond ruilen voor een en een .  De toepassing kan `access_token` de verificatie gebruiken voor een back-end web-API in HTTP-aanvragen. Het `refresh_token` kan ook worden gebruikt om nieuwe `access_token` te verkrijgen wanneer de oudere zijn verlopen.

## <a name="current-limitations"></a>Huidige beperkingen

### <a name="unsupported-application-types"></a>Niet-ondersteunde toepassingstypen

#### <a name="daemonsserver-side-applications"></a>Daemons/server-side applicaties

Toepassingen die langlopende processen bevatten of die werken zonder de aanwezigheid van een gebruiker, hebben ook een manier nodig om toegang te krijgen tot beveiligde bronnen zoals web-API's. Deze toepassingen kunnen tokens verifiëren en ontvangen met behulp van de identiteit van de toepassing (in plaats van de gedelegeerde identiteit van een gebruiker) en met behulp van de oauth 2.0-clientreferentiesstroom. Clientreferentiestroom is niet hetzelfde als voor de on-behalf-flow en on-behalf-flow mag niet worden gebruikt voor server-naar-server-verificatie.

Hoewel clientreferentiestroom momenteel niet wordt ondersteund door Azure AD B2C, u clientreferentiestroom instellen met Azure AD. Een Azure AD B2C-tenant deelt bepaalde functionaliteit met Azure AD-bedrijfstenantn.  De clientreferentiestroom wordt ondersteund met de Azure AD-functionaliteit van de Azure AD B2C-tenant.

Zie Azure Active Directory [v2.0 en de stroom van de clientreferenties van OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds)als u de clientreferentiesstroom wilt instellen. Een succesvolle verificatie resulteert in de ontvangst van een token die is opgemaakt zodat het door Azure AD kan worden gebruikt zoals beschreven in [Azure AD-tokenverwijzing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Web-API-ketens (namens-stroom)

Veel architecturen bevatten een web-API die een andere downstream web-API moet aanroepen, waarbij beide zijn beveiligd door Azure AD B2C. Dit scenario komt veel voor in native clients met een back-end van een Web API en roept een online service van Microsoft aan, zoals de Microsoft Graph API.

Dit scenario met web-API-keten kan worden ondersteund met behulp van de OAuth 2.0 JWT bearer-referentietoekenning, ook wel de namens-stroom genoemd.  De namens-stroom is momenteel echter niet geïmplementeerd in Azure AD B2C.

### <a name="faulted-apps"></a>Mislukte toepassingen

Bewerk Azure AD B2C-toepassingen niet op de volgende manieren:

- Op andere portalen voor applicatiebeheer, zoals het [Application Registration Portal.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
- Met behulp van Graph API of PowerShell.

Als u de Azure AD B2C-toepassing buiten de Azure-portal bewerkt, wordt deze een defecte toepassing en is deze niet langer bruikbaar met Azure AD B2C. Verwijder de toepassing en maak deze opnieuw.

Als u de toepassing wilt verwijderen, gaat u naar de [Portal voor toepassingsregistratie](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) en verwijdert u de toepassing daar. De toepassing is alleen zichtbaar als u de eigenaar van de toepassing bent (en niet een beheerder van de tenant).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ingebouwde beleid van [gebruikersstromen in Azure Active Directory B2C](user-flow-overview.md).
