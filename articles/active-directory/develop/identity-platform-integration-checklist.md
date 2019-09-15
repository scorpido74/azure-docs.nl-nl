---
title: Integreren met micro soft Identity platform | Azure
description: Meer informatie over aanbevolen procedures en algemene toezicht bij de integratie met het micro soft Identity platform (v 2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c01f695210ebbd69a26b957e846ff645b1dea5bb
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999016"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Controle lijst voor integratie van micro soft Identity platform

De controle lijst voor integratie van micro soft Identity platform is bedoeld om u te helpen bij het verbeteren van een hoogwaardige en veilige integratie. Het markeert aanbevolen procedures en algemene toezichten wanneer u integreert met het micro soft Identity-platform, zodat de lijst regel matig wordt gecontroleerd om ervoor te zorgen dat u de kwaliteit en beveiliging van de integratie van uw app met het identiteits platform behoudt. De controle lijst is niet bedoeld om uw hele toepassing te controleren. De inhoud van de controle lijst kan worden gewijzigd terwijl er verbeteringen aan het platform worden aangebracht.

Als u net aan de slag gaat, raadpleegt u de [documentatie](index.yml) voor meer informatie over de basis principes van verificatie, toepassings scenario's in het micro soft Identity platform en meer.

## <a name="testing-your-integration"></a>Uw integratie testen

Gebruik de volgende controle lijst om ervoor te zorgen dat uw toepassing effectief wordt geïntegreerd met het [micro soft Identity-platform](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="basics"></a>Basics

|   |   |
|---|---|
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Lees en begrijp het [micro soft platform-beleid](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Zorg ervoor dat uw toepassing voldoet aan de voor waarden die worden beschreven, zoals ze zijn ontworpen om gebruikers en het platform te beveiligen. |

### <a name="ownership"></a>Beheer

|   |   |
|---|---|
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Zorg ervoor dat de informatie die is gekoppeld aan het account dat u hebt gebruikt voor het registreren en beheren van apps up-to-date is. |

### <a name="branding"></a>Huisstijl

|   |   |
|---|---|
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Voldoen aan de [huisstijl richtlijnen voor toepassingen](howto-add-branding-in-azure-ad-apps.md). |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Geef een herken bare naam en logo op voor uw toepassing. Deze informatie wordt weer gegeven op de toestemming prompt van uw toepassing. Zorg ervoor dat uw naam en logo representatief zijn voor uw bedrijf/product zodat gebruikers weloverwogen beslissingen kunnen nemen. Zorg ervoor dat u geen enkele handels merk hebt geschonden. |

### <a name="privacy"></a>Privacy

|   |   |
|---|---|
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Geef koppelingen op met de service voorwaarden en privacyverklaring van uw app. |

### <a name="security"></a>Beveiliging

|   |   |
|---|---|
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Behoud het eigendom van alle omleidings-Uri's en behoud de DNS-records voor deze. Gebruik geen joker tekens (*) in uw Uri's. Zorg ervoor dat alle Uri's beveiligd en versleuteld zijn (bijvoorbeeld met behulp van HTTPS-schema's) voor web-apps. Gebruik voor open bare clients platformspecifieke omleidings-Uri's, indien van toepassing (voornamelijk voor iOS en Android). Gebruik anders omleidings-Uri's met een grote hoeveelheid wille keurigheid om conflicten te voor komen bij het terugbellen naar uw app. Als uw app wordt gebruikt vanuit een geïsoleerde webagent, kunt u gebruiken https://login.microsoftonline.com/nativeclient. Alle ongebruikte of overbodige omleidings-Uri's regel matig controleren en bijsnijden. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Als uw app is geregistreerd in een directory, kunt u de lijst met app-registratie-eigen aren minimaliseren en hand matig bewaken. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Schakel de ondersteuning voor de [OAuth2 impliciete toekennings stroom](v2-oauth2-implicit-grant-flow.md) niet in, tenzij expliciet vereist. Meer informatie over het geldige [scenario.](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Gebruik geen [wacht woord](v2-oauth-ropc.md)voor de ROPC van de resource-eigenaar, waarmee de wacht woorden van gebruikers rechtstreeks worden verwerkt. Deze stroom vereist een hoge mate van vertrouwen en gebruikers belichting en mag alleen worden gebruikt als andere, veiliger, stromen niet kunnen worden gebruikt. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Uw app-referenties beveiligen en beheren. [Certificaat referenties](active-directory-certificate-credentials.md)gebruiken, niet wachtwoord referenties (client geheimen). Als u een wachtwoord referentie moet gebruiken, hoeft u deze niet hand matig in te stellen. Sla geen referenties op in code of config en laat deze nooit door de mens worden verwerkt. Gebruik, indien mogelijk, [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) of [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) om uw referenties op te slaan en regel matig te draaien. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Zorg ervoor dat uw toepassing de machtigingen voor de minste bevoegdheden aanvraagt. Vraag alleen om machtigingen die uw toepassing absoluut nodig heeft en alleen wanneer u ze nodig hebt. Meer informatie over de verschillende [soorten machtigingen](v1-permissions-and-consent.md#types-of-permissions). Gebruik indien nodig alleen toepassings machtigingen. Gebruik waar mogelijk gedelegeerde machtigingen. Zie deze [Naslag informatie voor machtigingen](https://docs.microsoft.com/graph/permissions-reference)voor een volledige lijst met Microsoft Graph machtigingen. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Als u een API wilt beveiligen met behulp van het micro soft-identiteits platform, kunt u de machtigingen die ze moeten openbaren zorgvuldig door lopen. Bedenk wat de juiste granulatie voor uw oplossing is en welke machtiging (en) toestemming van de beheerder nodig heeft. Controleer op de verwachte machtigingen in de inkomende tokens voordat u autorisatie beslissingen neemt. |

### <a name="implementation"></a>Implementatie

|   |   |
|---|---|
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Gebruik moderne verificatie oplossingen (OAuth 2,0, [OpenID Connect Connect](v2-protocols-oidc.md)) om gebruikers veilig aan te melden. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementeer de protocollen zelf niet zelf: gebruik [verificatie bibliotheken die door micro soft worden ondersteund](reference-v2-libraries.md) (MSAL, middleware van de server). Zorg ervoor dat u de nieuwste versie van de verificatie bibliotheek gebruikt die u hebt geïntegreerd met. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Als de gegevens die uw app nodig heeft, beschikbaar zijn via [Microsoft Graph](https://developer.microsoft.com/graph), kunt u machtigingen voor deze gegevens aanvragen met behulp van het Microsoft Graph-eind punt in plaats van de afzonderlijke API. |

### <a name="end-user-experience"></a>Eindgebruikerservaring

|   |   |
|---|---|
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Meer informatie over [de toestemming](application-consent-experience.md) van de goed keuring en het configureren van de vraag om de onderdelen van de app de toestemming te geven zodat eind gebruikers en beheerders voldoende gegevens hebben om te bepalen of ze uw app vertrouwen. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Beperk het aantal keren dat een gebruiker aanmeldings referenties moet invoeren terwijl uw app wordt gebruikt door een stille verificatie (Silent token Acquisition) voor interactieve stromen uit te voeren. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Gebruik ' prompt = toestemming ' niet voor elke aanmelding. Gebruik prompt = toestemming als u hebt vastgesteld dat u moet worden gevraagd om toestemming te geven voor aanvullende machtigingen (bijvoorbeeld als u de vereiste machtigingen van uw app hebt gewijzigd). |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Verrijk uw toepassing met gebruikers gegevens, indien van toepassing. Gebruik de [Microsoft Graph-API](https://developer.microsoft.com/graph) is een eenvoudige manier om dit te doen. Het hulp programma [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) waarmee u aan de slag kunt gaan. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Registreer de volledige set machtigingen die uw app nodig heeft zodat beheerders eenvoudig toestemming kunnen verlenen aan hun Tenant. Gebruik [incrementele toestemming](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) op het moment van uitvoering om gebruikers te helpen begrijpen waarom uw app machtigingen aanvraagt of gebruikers Verwar ring wanneer deze worden aangevraagd bij de eerste keer starten. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementeer een [schone ervaring voor eenmalige aanmelding](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Het is een privacy-en beveiligings vereiste, en zorgt voor een goede gebruikers ervaring. |

### <a name="testing"></a>Testen

|   |   |
|---|---|
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Test op [beleids regels voor voorwaardelijke toegang](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) die van invloed kunnen zijn op de mogelijkheid van gebruikers om uw toepassing te gebruiken. |
| ![PS/2-verbinding](./media/active-directory-integration-checklist/checkbox-two.svg) | Test uw toepassing met alle mogelijke accounts die u wilt ondersteunen (bijvoorbeeld werk-of school accounts, persoonlijke micro soft-accounts, onderliggende accounts en soevereine accounts). |

## <a name="additional-resources"></a>Aanvullende resources

Gedetailleerde informatie over v2.0 verkennen:

* [Overzicht van micro soft Identity platform (v 2.0)](v2-overview.md)
* [Naslag informatie over micro soft Identity platform-protocollen](active-directory-v2-protocols.md)
* [Naslaginformatie voor Access-tokens](access-tokens.md)
* [Naslaginformatie voor id-tokens](id-tokens.md)
* [Naslag informatie over verificatie bibliotheken](reference-v2-libraries.md)
* [Machtigingen en toestemming in het micro soft Identity-platform](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
