---
title: Aanmelden en aanmelden met OpenID Connect instellen
titleSuffix: Azure AD B2C
description: Aanmelden en aanmelden bij elke OpenID Connect-identiteitsprovider (IdP) instellen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c72abf79f0a420309ebe229673be9439fd99b74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188253"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met OpenID Connect instellen met Azure Active Directory B2C

[OpenID Connect](openid-connect.md) is een verificatieprotocol dat is gebouwd bovenop OAuth 2.0 en dat kan worden gebruikt voor veilige gebruikersaanmelding. De meeste identiteitsproviders die dit protocol gebruiken, worden ondersteund in Azure AD B2C. In dit artikel wordt uitgelegd hoe u aangepaste OpenID Connect-identiteitsproviders toevoegen aan uw gebruikersstromen.

## <a name="add-the-identity-provider"></a>De identiteitsprovider toevoegen

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door in het bovenste menu op het **filter Directory + abonnement** te klikken en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **vervolgens Nieuwe OpenID Connect-provider**.

## <a name="configure-the-identity-provider"></a>De identiteitsprovider configureren

Elke OpenID Connect-identiteitsprovider beschrijft een metagegevensdocument dat de meeste informatie bevat die nodig is om aanmelding uit te voeren. Dit omvat informatie zoals de URL's die moeten worden gebruikt en de locatie van de openbare ondertekeningssleutels van de service. Het metagegevensdocument van OpenID Connect bevindt `.well-known\openid-configuration`zich altijd op een eindpunt dat eindigt op . Voer de URL van de metagegevens in voor de OpenID Connect-identiteitsprovider die u wilt toevoegen.

## <a name="client-id-and-secret"></a>Client-ID en geheim

Om gebruikers in staat te stellen zich aan te melden, vereist de identiteitsprovider dat ontwikkelaars een toepassing registreren in hun service. Deze toepassing heeft een ID die wordt aangeduid als de **client-ID** en een **client geheim**. Kopieer deze waarden van de identiteitsprovider en voer ze in de bijbehorende velden in.

> [!NOTE]
> Het klantgeheim is optioneel. U moet echter een clientgeheim invoeren als u de [autorisatiecodestroom](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)wilt gebruiken, die het geheim gebruikt om de code voor het token te ruilen.

## <a name="scope"></a>Bereik

Scope definieert de informatie en machtigingen die u wilt verzamelen van uw aangepaste identiteitsprovider. OpenID Connect-aanvragen `openid` moeten de bereikwaarde bevatten om het ID-token van de identiteitsprovider te ontvangen. Zonder het ID-token kunnen gebruikers zich niet aanmelden bij Azure AD B2C met behulp van de aangepaste identiteitsprovider. Andere scopes kunnen worden toegevoegd gescheiden door ruimte. Raadpleeg de documentatie van de aangepaste identiteitsprovider om te zien welke andere scopes mogelijk beschikbaar zijn.

## <a name="response-type"></a>Reactietype

Het antwoordtype beschrijft wat voor soort informatie wordt `authorization_endpoint` teruggestuurd in de eerste oproep naar de leverancier van de aangepaste identiteit. De volgende antwoordtypen kunnen worden gebruikt:

* `code`: Volgens de [machtigingscodestroom](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)wordt een code teruggestuurd naar Azure AD B2C. Azure AD B2C gaat `token_endpoint` over tot het aanroepen van de code om de code voor het token te ruilen.
* `id_token`: Een ID-token wordt teruggestuurd naar Azure AD B2C van de aangepaste identiteitsprovider.

## <a name="response-mode"></a>Reactiemodus

De antwoordmodus definieert de methode die moet worden gebruikt om de gegevens terug te sturen van de aangepaste identiteitsprovider naar Azure AD B2C. De volgende antwoordmodi kunnen worden gebruikt:

* `form_post`: Deze reactiemodus wordt aanbevolen voor de beste beveiliging. Het antwoord wordt verzonden `POST` via de HTTP-methode, waarbij de code `application/x-www-form-urlencoded` of token wordt gecodeerd in de body met behulp van de indeling.
* `query`: De code of token wordt geretourneerd als queryparameter.

## <a name="domain-hint"></a>Domeinhint

De domeinhint kan worden gebruikt om rechtstreeks naar de aanmeldingspagina van de opgegeven identiteitsprovider over te slaan, in plaats van dat de gebruiker een selectie maakt uit de lijst met beschikbare identiteitsproviders. Als u dit soort gedrag wilt toestaan, voert u een waarde in voor de domeinhint. Als u naar de aangepaste identiteitsprovider `domain_hint=<domain hint value>` wilt gaan, sluit u de parameter toe aan het einde van uw aanvraag wanneer u Azure AD B2C aanroept voor aanmelding.

## <a name="claims-mapping"></a>Claimstoewijzing

Nadat de aangepaste identiteitsprovider een ID-token naar Azure AD B2C heeft teruggestuurd, moet Azure AD B2C de claims van het ontvangen token kunnen toewijzen aan de claims die Azure AD B2C herkent en gebruikt. Raadpleeg voor elk van de volgende toewijzingen de documentatie van de aangepaste identiteitsprovider om inzicht te krijgen in de claims die worden geretourneerd in de tokens van de identiteitsprovider:

* **Gebruikersnaam:** voer de claim in die de *unieke id* voor de aangemelde gebruiker biedt.
* **Weergavenaam:** voer de claim in die de *weergavenaam* of *volledige naam* voor de gebruiker opgeeft.
* **Voornaam:** Voer de claim in die de *voornaam* van de gebruiker bevat.
* **Achternaam**: Voer de claim in die de *achternaam* van de gebruiker bevat.
* **E-mail:** Voer de claim in die het *e-mailadres* van de gebruiker bevat.
