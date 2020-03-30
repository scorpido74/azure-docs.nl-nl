---
title: Aanbevolen procedures voor microsoft-identiteitsplatform | Azure
description: Meer informatie over aanbevolen procedures, aanbevelingen en algemene vergissingen bij de integratie met het Microsoft-identiteitsplatform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 56975cebbfe4f6dd6452c850c338d431faea27bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050500"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Aanbevolen procedures en aanbevelingen van microsoft-identiteitsplatform

In dit artikel worden aanbevolen procedures, aanbevelingen en algemene vergissingen belicht bij de integratie met het Microsoft-identiteitsplatform.  Deze checklist leidt u naar een hoogwaardige en veilige integratie. Bekijk deze lijst regelmatig om ervoor te zorgen dat u de kwaliteit en beveiliging van de integratie van uw app met het identiteitsplatform behoudt. De checklist is niet bedoeld om uw volledige toepassing te bekijken. De inhoud van de checklist kan worden gewijzigd naarmate we verbeteringen aanbrengen aan het platform.

Als u nog maar net bent begonnen, raadpleegt u de documentatie van het [Microsoft-identiteitsplatform](index.yml) om meer te weten te komen over de basisprincipes van verificatie, toepassingsscenario's in het Microsoft-identiteitsplatform en meer.

Gebruik de volgende checklist om ervoor te zorgen dat uw toepassing effectief is geïntegreerd met het [Microsoft-identiteitsplatform.](https://docs.microsoft.com/azure/active-directory/develop/)

## <a name="basics"></a>Basisbeginselen

|   |   |
|---|---|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Lees en begrijp het [Microsoft Platform-beleid](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Zorg ervoor dat uw toepassing voldoet aan de voorwaarden die zijn beschreven omdat ze zijn ontworpen om gebruikers en het platform te beschermen. |

## <a name="ownership"></a>Eigendom

|   |   |
|---|---|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Zorg ervoor dat de informatie die is gekoppeld aan het account dat u hebt gebruikt om apps te registreren en te beheren, up-to-date is. |

## <a name="branding"></a>Huisstijl

|   |   |
|---|---|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Houd u aan de [richtlijnen voor branding voor toepassingen.](howto-add-branding-in-azure-ad-apps.md) |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Geef een betekenisvolle naam en logo op voor uw toepassing. Deze informatie wordt weergegeven op de [toestemmingsprompt van](application-consent-experience.md)uw aanvraag. Zorg ervoor dat uw naam en logo representatief zijn voor uw bedrijf/product, zodat gebruikers weloverwogen beslissingen kunnen nemen. Zorg ervoor dat u geen handelsmerken schendt. |

## <a name="privacy"></a>Privacy

|   |   |
|---|---|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Geef links naar de servicevoorwaarden en privacyverklaring van uw app. |

## <a name="security"></a>Beveiliging

|   |   |
|---|---|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Uw omleidings-URI's beheren: <ul><li>Behoud het eigendom van al uw omleidings-URI's en houd de DNS-records voor hen up-to-date.</li><li>Gebruik geen jokertekens (*) in uw URI's.</li><li>Voor web-apps moet u ervoor zorgen dat alle URI's veilig en versleuteld zijn (bijvoorbeeld met behulp van https-schema's).</li><li>Voor openbare clients, gebruik platform-specifieke redirect URI's indien van toepassing (vooral voor iOS en Android). Gebruik anders omleidings-URI's met een hoge mate van willekeur om botsingen te voorkomen wanneer u terugbelt naar uw app.</li><li>Als uw app wordt gebruikt vanuit een geïsoleerde `https://login.microsoftonline.com/common/oauth2/nativeclient`webagent, u.</li><li>Bekijk en trim alle ongebruikte of onnodige omleiding surinoirs op een regelmatige basis.</li></ul> |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Als uw app is geregistreerd in een map, controleert en controleert u de lijst met eigenaren van app-registraties en controleert u deze handmatig. |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Schakel geen ondersteuning in voor de [impliciete subsidiestroom van OAuth2,](v2-oauth2-implicit-grant-flow.md) tenzij dit uitdrukkelijk vereist is. Meer informatie over het geldige scenario [vindt u hier](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Ga verder dan gebruikersnaam/wachtwoord. Gebruik geen [resource owner password credential flow (ROPC),](v2-oauth-ropc.md)die rechtstreeks de wachtwoorden van gebruikers verwerkt. Deze stroom vereist een hoge mate van vertrouwen en blootstelling van de gebruiker en mag alleen worden gebruikt wanneer andere, veiligere stromen niet kunnen worden gebruikt. Deze stroom is nog steeds nodig in sommige scenario's (zoals DevOps), maar pas op dat het gebruik ervan beperkingen op uw toepassing zal opleggen.  Lees [verificatiestromen en toepassingsscenario's voor](authentication-flows-app-scenarios.md)modernere benaderingen.|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Bescherm en beheer uw vertrouwelijke app-referenties voor web-apps, web-API's en daemon-apps. Gebruik [certificaatreferenties,](active-directory-certificate-credentials.md)geen wachtwoordreferenties (clientgeheimen). Als u een wachtwoordreferentie moet gebruiken, stelt u deze niet handmatig in. Sla geen referenties op in code of config en laat ze nooit door mensen worden behandeld. Gebruik indien mogelijk [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) of [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) om uw referenties op te slaan en regelmatig te roteren. |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Zorg ervoor dat uw toepassing de minste bevoegdheden aanvraagt. Vraag alleen om machtigingen die uw toepassing absoluut nodig heeft, en alleen wanneer u ze nodig hebt. Begrijp de verschillende [soorten machtigingen](v2-permissions-and-consent.md#permission-types). Gebruik indien nodig alleen toepassingsmachtigingen; waar mogelijk gedelegeerde machtigingen gebruiken. Zie deze [machtigingsverwijzing](https://docs.microsoft.com/graph/permissions-reference)voor microsoft graph voor een volledige lijst met machtigingen voor Microsoft Graph. |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Als u een API beveiligt met behulp van het Microsoft-identiteitsplatform, moet u goed nadenken over de machtigingen die moeten worden blootgesteld. Bedenk wat de juiste granulariteit is voor uw oplossing en welke toestemming(en) toestemming van de beheerder vereist. Controleer op verwachte machtigingen in de binnenkomende tokens voordat u autorisatiebeslissingen neemt. |

## <a name="implementation"></a>Implementatie

|   |   |
|---|---|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Gebruik moderne authenticatieoplossingen (OAuth 2.0, [OpenID Connect)](v2-protocols-oidc.md)om gebruikers veilig aan te melden. |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) |  Programmeer niet rechtstreeks tegen protocollen zoals OAuth 2.0 en Open ID. Gebruik in plaats daarvan de [Microsoft Authentication Library (MSAL).](msal-overview.md) De MSAL-bibliotheken wikkelen beveiligingsprotocollen veilig in een gebruiksvriendelijke bibliotheek en u krijgt ingebouwde ondersteuning voor voorwaardelijke toegangsscenario's, [Conditional Access](/azure/active-directory/conditional-access/overview) [apparaatbrede single sign-on (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)en ingebouwde ondersteuning voor tokencaching. Zie voor meer informatie de lijst met door Microsoft ondersteunde [clientbibliotheken](reference-v2-libraries.md#microsoft-supported-client-libraries) en [middleware-bibliotheken](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) en de lijst met [compatibele clientbibliotheken](reference-v2-libraries.md#compatible-client-libraries)van derden .<br/><br/>Als u code moet overhandigen voor de verificatieprotocollen, moet u een methodologie volgen zoals [Microsoft SDL.](https://www.microsoft.com/sdl/default.aspx) Besteed veel aandacht aan de beveiligingsoverwegingen in de standaardspecificaties voor elk protocol.|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) |  Migreer bestaande apps van [Azure Active Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) naar Microsoft Authentication [Library](msal-overview.md). MSAL is de nieuwste oplossing voor het identiteitsplatform van Microsoft en heeft de voorkeur boven ADAL. Het is beschikbaar op .NET, JavaScript, Android, iOS, macOS en is ook in openbare preview voor Python en Java. Lees meer over het migreren van [ADAL.NET,](msal-net-migration.md) [ADAL.js](msal-compare-msal-js-and-adal-js.md)en [ADAL.NET en iOS broker](msal-net-migration-ios-broker.md) apps.|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) |  Voor mobiele apps configureert u elk platform met behulp van de ervaring voor het registreren van toepassingen. Om ervoor te zorgen dat uw toepassing gebruik kan maken van de Microsoft Authenticator of Microsoft Company Portal voor eenmalige aanmelding, heeft uw app een geconfigureerde 'broker redirect URI' nodig. Hierdoor kan Microsoft de controle na verificatie terugsturen naar uw toepassing. Bij het configureren van elk platform leidt de ervaring voor app-registratie u door het proces. Gebruik de quickstart om een werkend voorbeeld te downloaden. Gebruik op iOS waar mogelijk brokers en system webview.|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) |  Houd in web-apps of web-API's één tokencache per account bij.  Voor web-apps moet de tokencache worden ingetoetst door de account-id.  Voor web-API's moet het account worden ingetoetst door de hash van het token dat wordt gebruikt om de API aan te roepen. MSAL.NET biedt aangepaste tokencacheserialisatie in de .NET Framework- en .NET Core-subplatforms. Om redenen van veiligheid en prestaties is het onze aanbeveling om één cache per gebruiker te serialiseren. Lees voor meer informatie over serialisatie van [tokencache.](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Als de gegevens die uw app nodig heeft, beschikbaar zijn via [Microsoft Graph,](https://developer.microsoft.com/graph)vraagt u machtigingen voor deze gegevens aan met het Microsoft Graph-eindpunt in plaats van de afzonderlijke API. |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) |Kijk niet naar de waarde van het toegangstoken of probeer deze niet te ontken als een client.  Ze kunnen waarden, indelingen wijzigen of zelfs zonder waarschuwing versleuteld worden - gebruik altijd de id_token als uw client iets over de gebruiker moet leren of Microsoft Graph moet bellen.  Alleen web-API's moeten toegangstokens ontleden (omdat zij degenen zijn die het formaat definiëren en de coderingssleutels instellen). |

## <a name="end-user-experience"></a>De ervaring voor de eindgebruiker

|   |   |
|---|---|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | [Begrijp de toestemmingservaring](application-consent-experience.md) en configureer de onderdelen van de toestemmingsprompt van uw app, zodat eindgebruikers en beheerders voldoende informatie hebben om te bepalen of ze uw app vertrouwen. |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimaliseer het aantal keren dat een gebruiker inloggegevens moet invoeren tijdens het gebruik van uw app door te proberen stille verificatie (stille tokenacquisitie) te proberen voordat interactieve stromen. |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Gebruik geen 'prompt=consent' voor elke aanmelding. Gebruik prompt=consent alleen als u hebt vastgesteld dat u toestemming moet vragen voor aanvullende machtigingen (bijvoorbeeld als u de vereiste machtigingen van uw app hebt gewijzigd). |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Verrijk uw toepassing waar van toepassing met gebruikersgegevens. Het gebruik van de [Microsoft Graph API](https://developer.microsoft.com/graph) is een eenvoudige manier om dit te doen. Het gereedschap [Grafiekverkenner](https://developer.microsoft.com/graph/graph-explorer) waarmee u aan de slag. |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Registreer de volledige set machtigingen die uw app nodig heeft, zodat beheerders gemakkelijk toestemming kunnen verlenen aan hun tenant. Gebruik [incrementele toestemming](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) bij het uitvoeren van de looptijd om gebruikers te laten begrijpen waarom uw app machtigingen aanvraagt of in verwarring brengt wanneer deze bij de eerste start worden aangevraagd. |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementeer een [schone single sign-out ervaring.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) Het is een privacy en een beveiligingsvereiste, en zorgt voor een goede gebruikerservaring. |

## <a name="testing"></a>Testen

|   |   |
|---|---|
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Test voor beleid voor [voorwaardelijke toegang](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) dat van invloed kan zijn op de gebruiksaanwijzing van uw toepassing door uw gebruikers. |
| ![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) | Test uw toepassing met alle mogelijke accounts die u wilt ondersteunen (bijvoorbeeld werk- of schoolaccounts, persoonlijke Microsoft-accounts, kinderaccounts en soevereine accounts). |

## <a name="additional-resources"></a>Aanvullende bronnen

Gedetailleerde informatie over v2.0 verkennen:

* [Microsoft-identiteitsplatform (v2.0-overzicht)](v2-overview.md)
* [Verwijzing naar microsoft-identiteitsplatformprotocollen](active-directory-v2-protocols.md)
* [Naslaginformatie voor Access-tokens](access-tokens.md)
* [Naslaginformatie voor id-tokens](id-tokens.md)
* [Verwijzing naar verificatiebibliotheken](reference-v2-libraries.md)
* [Machtigingen en toestemming in het Microsoft-identiteitsplatform](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
