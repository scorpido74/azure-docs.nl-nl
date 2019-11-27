---
title: Toepassings typen die kunnen worden gebruikt in Azure Active Directory B2C
description: Meer informatie over de typen toepassingen die u kunt gebruiken met Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5643f1df6cefa9ca6c60453939be533b2c00eaf4
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533083"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Toepassings typen die kunnen worden gebruikt in Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ondersteunt verificatie voor verschillende moderne toepassings architecturen. Ze zijn allemaal gebaseerd op de protocollen volgens de industrienorm [OAuth 2.0](active-directory-b2c-reference-protocols.md) of [OpenID Connect](active-directory-b2c-reference-protocols.md). In dit artikel worden de typen toepassingen beschreven die u kunt maken, onafhankelijk van de taal of het platform dat u wilt gebruiken. Het biedt ook meer inzicht in de scenario's op hoog niveau voordat u begint met het bouwen van toepassingen.

Elke toepassing die gebruikmaakt van Azure AD B2C, moet worden geregistreerd in uw [Azure AD B2C Tenant](active-directory-b2c-get-started.md) met behulp van de [Azure Portal](https://portal.azure.com/). Tijdens het registratie proces voor de toepassing worden waarden verzameld en toegewezen, zoals:

* Een **toepassings-id** waarmee uw toepassing uniek wordt geïdentificeerd.
* Een **antwoord-URL** die kan worden gebruikt om antwoorden terug te sturen naar uw toepassing.

Elke aanvraag die wordt verzonden naar Azure AD B2C geeft een **gebruikers stroom** op (een ingebouwd beleid) of een **aangepast beleid** waarmee het gedrag van Azure AD B2C wordt bepaald. Beide beleids typen bieden u de mogelijkheid om een zeer aanpas bare set gebruikers ervaringen te maken.

De interactie van elke toepassing volgt in grote lijnen hetzelfde patroon:

1. De toepassing stuurt de gebruiker naar het v 2.0-eind punt om een [beleid](active-directory-b2c-reference-policies.md)uit te voeren.
2. De gebruiker voltooit het beleid volgens de beleidsdefinitie.
3. De toepassing ontvangt een beveiligings token van het v 2.0-eind punt.
4. De toepassing gebruikt het beveiligings token om toegang te krijgen tot beveiligde gegevens of een beveiligde bron.
5. De resource-server valideert het beveiligingstoken om te controleren of toegang kan worden verleend.
6. Het beveiligingstoken wordt regelmatig vernieuwd door de toepassing.

Deze stappen kunnen enigszins verschillen op basis van het type toepassing dat u wilt maken.

## <a name="web-applications"></a>Web-apps

Voor webtoepassingen (inclusief .NET, PHP, Java, Ruby, python en node. js) die worden gehost op een server en toegankelijk zijn via een browser, Azure AD B2C ondersteunt [OpenID Connect Connect](active-directory-b2c-reference-protocols.md) voor alle gebruikers ervaringen. In de Azure AD B2C implementatie van OpenID Connect Connect initieert uw webtoepassing gebruikers ervaringen door verificatie aanvragen uit te geven aan Azure AD. Het resultaat van de aanvraag is een `id_token`. Dit beveiligingstoken vertegenwoordigt de identiteit van de gebruiker. Het bevat ook informatie over de gebruiker in de vorm van claims:

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

Meer informatie over de typen tokens en claims die beschikbaar zijn voor een toepassing in de [Azure AD B2C-token verwijzing](active-directory-b2c-reference-tokens.md).

In een webtoepassing heeft elke uitvoering van een [beleid](active-directory-b2c-reference-policies.md) de volgende stappen op hoog niveau:

1. De gebruiker bladert naar de webtoepassing.
2. De webtoepassing leidt de gebruiker om naar Azure AD B2C die aangeeft dat het beleid moet worden uitgevoerd.
3. De gebruiker heeft het beleid voltooid.
4. Azure AD B2C retourneert een `id_token` naar de browser.
5. De `id_token` wordt naar de omleidings-URI verzonden.
6. De `id_token` wordt gevalideerd en een sessie cookie is ingesteld.
7. Er wordt een beveiligde pagina geretourneerd aan de gebruiker.

Validatie van het `id_token` met behulp van een openbare ondertekeningssleutel die is ontvangen van Azure AD is voldoende om de identiteit van de gebruiker te controleren. Dit proces stelt ook een sessie cookie in die kan worden gebruikt om de gebruiker te identificeren op volgende pagina-aanvragen.

Als u dit scenario in actie wilt zien, kunt u een van de voor beelden van een webtoepassing aanmelden in de [sectie aan](active-directory-b2c-overview.md)de slag.

Naast het vergemakkelijken van eenvoudige aanmelding moet een webserver toepassing mogelijk ook toegang hebben tot een back-end-webservice. In dit geval kan de webtoepassing een iets andere OpenID Connect- [verbindings stroom](active-directory-b2c-reference-oidc.md) uitvoeren en tokens verkrijgen met behulp van autorisatie codes en tokens vernieuwen. Dit scenario wordt beschreven in de volgende sectie over [Web-API's](#web-apis).

## <a name="web-apis"></a>Web-API's

U kunt Azure AD B2C gebruiken om webservices te beveiligen, zoals de REST Web-API van uw toepassing. Web-API's kunnen OAuth 2.0 gebruiken om hun gegevens te beveiligen door verificatie van binnenkomende HTTP-aanvragen via tokens. De aanroeper van een web-API voegt een token toe in de autorisatie-header van een HTTP-aanvraag:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

De web-API kan het token vervolgens gebruiken om de identiteit van de API-aanroeper te verifiëren en om informatie over de aanroeper af te leiden uit de claims die in het token zijn gecodeerd. In de [Naslaginformatie over Azure AD B2C-tokens](active-directory-b2c-reference-tokens.md) vindt u meer informatie over de typen tokens en claims die beschikbaar zijn voor een app.

Een web-API kan tokens ontvangen van een groot aantal typen clients, waaronder webtoepassingen, desktop-en mobiele toepassingen, toepassingen met één pagina, daemons aan server zijde en andere web-Api's. Hier volgt een voor beeld van de volledige stroom voor een webtoepassing die een web-API aanroept:

1. De webtoepassing voert een beleid uit en de gebruiker voltooit de gebruikers ervaring.
2. Azure AD B2C retourneert een `id_token` (OpenID Connect Connect) en een autorisatie code in de browser.
3. De browser post de `id_token` en autorisatie code naar de omleidings-URI.
4. De webserver valideert de `id_token` en stelt een sessie cookie in.
5. De webserver vraagt Azure AD B2C voor een `access_token` door de verificatie code, de client-ID van de toepassing en de client referenties op te geven.
6. De `access_token` en `refresh_token` worden geretourneerd naar de webserver.
7. De Web-API wordt aangeroepen met de `access_token` in een autorisatie-header.
8. De Web-API valideert het token.
9. Beveiligde gegevens worden geretourneerd naar de webtoepassing.

Voor meer informatie over autorisatiecodes, vernieuwingstokens en de stappen voor het ophalen van tokens, leest u over het [OAuth 2.0-protocol](active-directory-b2c-reference-oauth-code.md).

Voor meer informatie over het beveiligen van een web-API met behulp van Azure AD B2C bekijkt u de web-API-zelfstudies in de sectie [Aan de slag](active-directory-b2c-overview.md).

## <a name="mobile-and-native-applications"></a>Mobiele en systeem eigen toepassingen

Toepassingen die zijn geïnstalleerd op apparaten, zoals mobiele en desktop toepassingen, moeten vaak toegang hebben tot back-end-services of Web-Api's namens gebruikers. U kunt aangepaste ervaring voor identiteits beheer toevoegen aan uw systeem eigen toepassingen en back-end-services veilig aanroepen met behulp van Azure AD B2C en de [OAuth 2,0-autorisatie code stroom](active-directory-b2c-reference-oauth-code.md).

In deze stroom voert de toepassing [beleid](active-directory-b2c-reference-policies.md) uit en ontvangt een `authorization_code` van Azure AD nadat de gebruiker het beleid heeft voltooid. De `authorization_code` vertegenwoordigt de machtiging van de toepassing voor het aanroepen van back-end-services namens de gebruiker die momenteel is aangemeld. De toepassing kan vervolgens de `authorization_code` op de achtergrond van een `access_token` en een `refresh_token`uitwisselen.  De toepassing kan de `access_token` gebruiken om te verifiëren bij een back-end web-API in HTTP-aanvragen. Het `refresh_token` kan ook worden gebruikt om nieuwe `access_token` te verkrijgen wanneer de oudere zijn verlopen.

## <a name="current-limitations"></a>Huidige beperkingen

### <a name="unsupported-application-types"></a>Niet-ondersteunde toepassings typen

#### <a name="daemonsserver-side-applications"></a>Daemons/server toepassingen

Toepassingen die langlopende processen bevatten of die werken zonder de aanwezigheid van een gebruiker, hebben ook een manier nodig om toegang te krijgen tot beveiligde resources, zoals web-Api's. Deze toepassingen kunnen tokens verifiëren en ophalen met behulp van de identiteit van de toepassing (in plaats van de gedelegeerde identiteit van een gebruiker) en met behulp van de OAuth 2,0-client referenties stroom. De stroom van de client referenties is niet hetzelfde als namens flow en namens flow mag niet worden gebruikt voor server-naar-Server-verificatie.

Hoewel de client referentie stroom momenteel niet wordt ondersteund door Azure AD B2C, kunt u de client referentie stroom instellen met behulp van Azure AD. Een Azure AD B2C-Tenant deelt een aantal functies met Azure AD-tenants voor bedrijven.  De client referentie stroom wordt ondersteund met behulp van de Azure AD-functionaliteit van de Azure AD B2C Tenant.

Zie [Azure Active Directory v 2.0 en de OAuth 2,0-client referenties stroom voor het](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds)instellen van de client referentie stroom. Een geslaagde verificatie resulteert in de ontvangst van een token dat is geformatteerd zodat deze kan worden gebruikt door Azure AD, zoals beschreven in [Azure AD-token verwijzing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Web-API-ketens (namens-stroom)

Veel architecturen bevatten een web-API die een andere downstream web-API moet aanroepen, waarbij beide zijn beveiligd door Azure AD B2C. Dit scenario is gebruikelijk in native clients met een web API-back-end en roept een micro soft online service aan, zoals de Microsoft Graph-API of Azure AD-Graph API.

Dit scenario met web-API-keten kan worden ondersteund met behulp van de OAuth 2.0 JWT bearer-referentietoekenning, ook wel de namens-stroom genoemd.  De namens-stroom is momenteel echter niet geïmplementeerd in Azure AD B2C.

### <a name="faulted-apps"></a>Mislukte toepassingen

Bewerk Azure AD B2C toepassingen niet op de volgende manieren:

- Op andere portals voor toepassings beheer, zoals de [Portal voor toepassings registratie](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
- Graph API of Power shell gebruiken.

Als u de Azure AD B2C toepassing buiten de Azure Portal bewerkt, wordt het een mislukte toepassing en kan deze niet meer worden gebruikt met Azure AD B2C. Verwijder de toepassing en maak deze opnieuw.

Als u de toepassing wilt verwijderen, gaat u naar de [Portal voor toepassings registratie](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) en verwijdert u de toepassing daar. De toepassing is alleen zichtbaar als u de eigenaar van de toepassing bent (en niet een beheerder van de tenant).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ingebouwde beleid van [gebruikers stromen in azure Active Directory B2C](active-directory-b2c-reference-policies.md).
