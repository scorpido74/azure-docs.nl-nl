---
title: Toepassingstypen voor microsoft-identiteitsplatform | Azure
description: De typen apps en scenario's die worden ondersteund door het eindpunt van het Microsoft-identiteitsplatform (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: bdbda8bed38819ca2b4d2fb1ef3d9bf591269890
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535907"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Toepassingstypen voor Microsoft-identiteitsplatform

Het Microsoft Identity Platform (v2.0) endpoint ondersteunt authenticatie voor een verscheidenheid aan moderne app-architecturen, allemaal op basis van industriestandaard protocollen [OAuth 2.0 of OpenID Connect.](active-directory-v2-protocols.md) In dit artikel worden de typen apps beschreven die u bouwen met behulp van het Microsoft-identiteitsplatform, ongeacht uw voorkeurstaal of -platform. De informatie is ontworpen om u te helpen scenario's op hoog niveau te begrijpen voordat u [met de code gaat werken.](v2-overview.md#getting-started)

> [!NOTE]
> Het eindpunt van het Microsoft-identiteitsplatform biedt geen ondersteuning voor alle Azure Active Directory-scenario's en -functies (Azure AD). Als u wilt bepalen of u het eindpunt van het Microsoft-identiteitsplatform moet gebruiken, leest u over [de beperkingen van het Microsoft-identiteitsplatform.](active-directory-v2-limitations.md)

## <a name="the-basics"></a>De basisbeginselen

U moet elke app registreren die het eindpunt van het Microsoft-identiteitsplatform gebruikt in de nieuwe [portal voor app-registraties.](https://go.microsoft.com/fwlink/?linkid=2083908) Het registratieproces van de app verzamelt en wijst deze waarden toe voor uw app:

* Een **toepassings-id (client)** die uw app op een unieke basis identificeert
* Een **OMLEIDINGsURI** die u gebruiken om reacties terug te sturen naar uw app
* Een paar andere scenariospecifieke waarden, zoals ondersteunde accounttypen

Voor meer informatie, meer informatie over het [registreren van een app](quickstart-register-app.md).

Nadat de app is geregistreerd, communiceert de app met het identiteitsplatform van Microsoft door verzoeken naar het eindpunt te verzenden. Wij bieden open-source frameworks en bibliotheken die de details van deze verzoeken behandelen. U hebt ook de optie om de verificatielogica zelf te implementeren door aanvragen voor deze eindpunten te maken:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Apps met één pagina (JavaScript)

Veel moderne apps hebben een app-front-end van één pagina die voornamelijk in JavaScript is geschreven. Vaak wordt het geschreven met behulp van een framework zoals Angular, React of Vue. Het eindpunt van het Microsoft-identiteitsplatform ondersteunt deze apps met behulp van de [impliciete stroom van OAuth 2.0.](v2-oauth2-implicit-grant-flow.md)

In deze stroom ontvangt de app tokens rechtstreeks van het Microsoft-identiteitsplatform om het eindpunt te autoriseren, zonder dat er server-naar-server-uitwisselingen zijn. Alle verificatielogica en sessieafhandeling vindt volledig plaats in de JavaScript-client, zonder extra paginaomleidingen.

![Toont de impliciete verificatiestroom](./media/v2-app-types/convergence-scenarios-implicit.svg)

Als u dit scenario in actie wilt zien, probeert u een van de app-codevoorbeelden van één pagina in de sectie [Microsoft-identiteitsplatform aan de slag.](v2-overview.md#getting-started)

## <a name="web-apps"></a>Web-apps

Voor web-apps (.NET, PHP, Java, Ruby, Python, Node) die de gebruiker via een browser opent, u [OpenID Connect](active-directory-v2-protocols.md) gebruiken voor aanmelding door gebruikers. In OpenID Connect ontvangt de web-app een ID-token. Een ID-token is een beveiligingstoken dat de identiteit van de gebruiker verifieert en informatie over de gebruiker verstrekt in de vorm van claims:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Verdere details van verschillende soorten tokens die worden gebruikt in het eindpunt van het Microsoft-identiteitsplatform zijn beschikbaar in de referentie [voor toegangstokens](access-tokens.md) en [id_token](id-tokens.md)

In webserver-apps voert de verificatiestroom op hoog niveau de volgende stappen uit:

![Toont de verificatiestroom voor web-apps](./media/v2-app-types/convergence-scenarios-webapp.svg)

U de identiteit van de gebruiker waarborgen door het ID-token te valideren met een openbare ondertekeningssleutel die wordt ontvangen van het eindpunt van het Microsoft-identiteitsplatform. Er wordt een sessiecookie ingesteld die kan worden gebruikt om de gebruiker te identificeren op basis van volgende paginaverzoeken.

Als u dit scenario in actie wilt zien, probeert u een van de aanmeldingscodevoorbeelden van de web-app in de sectie [Microsoft-identiteitsplatform aan de slag.](v2-overview.md#getting-started)

Naast eenvoudige aanmelding moet een webserver-app mogelijk ook toegang krijgen tot een andere webservice, zoals een REST API. In dit geval voert de webserver-app een gecombineerde OpenID Connect- en OAuth 2.0-stroom uit met behulp van de [OAuth 2.0-autorisatiecodestroom.](active-directory-v2-protocols.md) Lees voor meer informatie over dit scenario over [aan de slag met web-apps en web-API's](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Web-API's

U het eindpunt van het Microsoft-identiteitsplatform gebruiken om webservices te beveiligen, zoals de RESTful-web-API van uw app. Web API's kunnen worden geïmplementeerd in tal van platforms en talen. Ze kunnen ook worden geïmplementeerd met HTTP Triggers in Azure-functies. In plaats van ID-tokens en sessiecookies gebruikt een web-API een OAuth 2.0-toegangstoken om de gegevens te beveiligen en inkomende aanvragen te verifiëren. De beller van een web-API voegt een toegangstoken toe in de autorisatiekop van een HTTP-aanvraag, zoals dit:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

De web-API gebruikt het toegangstoken om de identiteit van de API-beller te verifiëren en om informatie over de beller te extraheren uit claims die zijn gecodeerd in het toegangstoken. Verdere details van verschillende soorten tokens die worden gebruikt in het eindpunt van het Microsoft-identiteitsplatform zijn beschikbaar in de referentie [van het toegangstoken](access-tokens.md) en [id_token](id-tokens.md) referentie.

Een web-API kan gebruikers de mogelijkheid geven om zich aan te melden of af te melden voor specifieke functionaliteit of gegevens door machtigingen bloot te leggen, ook wel [scopes](v2-permissions-and-consent.md)genoemd. Voor een aanroepende app om toestemming te krijgen voor een bereik, moet de gebruiker toestemming geven voor het bereik tijdens een stroom. Het eindpunt van het Microsoft-identiteitsplatform vraagt de gebruiker om toestemming en registreert vervolgens machtigingen in alle toegangstokens die de web-API ontvangt. De web-API valideert de toegangstokens die het ontvangt bij elke oproep en voert autorisatiecontroles uit.

Een web-API kan toegangstokens ontvangen van alle soorten apps, waaronder webserver-apps, desktop- en mobiele apps, apps voor één pagina, daemons aan de serverzijde en zelfs andere web-API's. De stroom op hoog niveau voor een web-API ziet er als volgt uit:

![Toont de verificatiestroom voor web-API's](./media/v2-app-types/convergence-scenarios-webapi.svg)

Bekijk de web API-codevoorbeelden in de sectie [Microsoft-identiteitsplatform aan](v2-overview.md#getting-started) de slag voor meer informatie over het beveiligen van een web-API met OAuth2-toegangstokens.

In veel gevallen moeten web-API's ook uitgaande verzoeken indienen bij andere downstream web-API's die zijn beveiligd door het Microsoft-identiteitsplatform. Hiervoor kunnen web-API's profiteren van de **on-behalf-of-flow,** waarmee de web-API een binnenkomend toegangstoken kan uitwisselen voor een ander toegangstoken dat kan worden gebruikt in uitgaande aanvragen. Zie [Microsoft-identiteitsplatform en OAuth 2.0 On-Behalf-Of flow voor](v2-oauth2-on-behalf-of-flow.md)meer informatie.

## <a name="mobile-and-native-apps"></a>Mobiele en systeemeigen apps

Apparaatgeïnstalleerde apps, zoals mobiele en desktop-apps, moeten vaak toegang krijgen tot back-endservices of web-API's die gegevens opslaan en functies uitvoeren namens een gebruiker. Deze apps kunnen aanmelding en autorisatie toevoegen aan back-endservices met behulp van de [OAuth 2.0-autorisatiecodestroom](v2-oauth2-auth-code-flow.md).

In deze stroom ontvangt de app een autorisatiecode van het eindpunt van het Microsoft-identiteitsplatform wanneer de gebruiker zich aanmeldt. De autorisatiecode vertegenwoordigt de toestemming van de app om back-endservices te bellen namens de gebruiker die is aangemeld. De app kan de autorisatiecode op de achtergrond uitwisselen voor een OAuth 2.0-toegangstoken en een vernieuwingstoken. De app kan het toegangstoken gebruiken om zich te authenticeren tot web-API's in HTTP-aanvragen en het vernieuwingstoken gebruiken om nieuwe toegangstokens te krijgen wanneer oudere toegangstokens verlopen.

![Toont de verificatiestroom van de native app](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemons en server-apps

Apps die langlopende processen hebben of die werken zonder interactie met een gebruiker, hebben ook een manier nodig om toegang te krijgen tot beveiligde bronnen, zoals web-API's. Deze apps kunnen tokens verifiëren en ontvangen met behulp van de identiteit van de app, in plaats van de gedelegeerde identiteit van een gebruiker, met de oauth 2.0-clientreferentiesstroom. U de identiteit van de app bewijzen met behulp van een geheim of certificaat van een client. Zie [.NET Core daemon console-toepassing voor](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)meer informatie met behulp van het Microsoft-identiteitsplatform .

In deze stroom werkt de app `/token` rechtstreeks samen met het eindpunt om toegang te krijgen:

![Toont de verificatiestroom van de daemon-app](./media/v2-app-types/convergence-scenarios-daemon.svg)

Als u een daemon-app wilt maken, raadpleegt u de documentatie van de [clientreferenties](v2-oauth2-client-creds-grant-flow.md)of probeert u een [.NET-voorbeeld-app](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
