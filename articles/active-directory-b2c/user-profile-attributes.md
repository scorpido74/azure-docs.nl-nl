---
title: Gebruikersprofielkenmerken in Azure Active Directory B2C
description: Meer informatie over de kenmerken van het type gebruikersbron die worden ondersteund door het gebruikersprofiel van de Azure AD B2C-map. Meer informatie over ingebouwde kenmerken, extensies en hoe kenmerken worden toegewezen aan Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e921f0a40f53b1d08831047d1cb89ca26de41402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057297"
---
# <a name="user-profile-attributes"></a>Kenmerken van gebruikersprofielen

Uw Azure Active Directory (Azure AD) B2C-mapgebruikersprofiel wordt geleverd met een ingebouwde set kenmerken, zoals voornaam, achternaam, plaats, postcode en telefoonnummer. U het gebruikersprofiel uitbreiden met uw eigen toepassingsgegevens zonder dat u een extern gegevensarchief nodig hebt. De meeste kenmerken die kunnen worden gebruikt met Azure AD B2C-gebruikersprofielen worden ook ondersteund door Microsoft Graph. In dit artikel worden ondersteunde Azure AD B2C-gebruikersprofielkenmerken beschreven. Het merkt ook de kenmerken op die niet worden ondersteund door Microsoft Graph, evenals Microsoft Graph-kenmerken die niet mogen worden gebruikt met Azure AD B2C.

> [!IMPORTANT]
> U mag geen ingebouwde of extensiekenmerken gebruiken om gevoelige persoonlijke gegevens op te slaan, zoals accountgegevens, identificatienummers van de overheid, kaarthoudergegevens, financiële rekeninggegevens, gezondheidsinformatie of gevoelige achtergrondinformatie.

U ook integreren met externe systemen. U bijvoorbeeld Azure AD B2C gebruiken voor verificatie, maar delegeren aan een externe crm-database (customer relationship management) of klantenloyaliteit als de gezaghebbende bron van klantgegevens. Zie voor meer informatie de [externe profieloplossing.](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)

In de onderstaande tabel worden de kenmerken van het [type gebruikersbron](https://docs.microsoft.com/graph/api/resources/user) weergegeven die worden ondersteund door het gebruikersprofiel van de Azure AD B2C-map. Het geeft de volgende informatie over elk kenmerk:

- Kenmerknaam die wordt gebruikt door Azure AD B2C (gevolgd door de naam Microsoft Graph tussen haakjes, als deze verschilt)
- Gegevenstype Kenmerk
- Kenmerkbeschrijving
- Als het kenmerk beschikbaar is in de Azure-portal
- Als het kenmerk kan worden gebruikt in een gebruikersstroom
- Als het kenmerk kan worden gebruikt in een aangepast [azure AD-technisch profiel](active-directory-technical-profile.md) &lt;en in welke sectie (InputClaims&gt;&lt;&gt;, &lt;OutputClaims&gt;of PersistedClaims)

|Name     |Type     |Beschrijving|Azure Portal|Gebruikersstromen|Aangepast beleid|
|---------|---------|----------|------------|----------|-------------|
|accountIngeschakeld  |Booleaans|Of het gebruikersaccount is ingeschakeld of uitgeschakeld: **true** als het account is ingeschakeld, anders **false**.|Ja|Nee|Persistented, Output|
|ageGroup        |Tekenreeks|De leeftijdsgroep van de gebruiker. Mogelijke waarden: null, Ongedefinieerd, Minderjarig, Volwassen, NotAdult.|Ja|Nee|Persistented, Output|
|alternativeSecurityId ([Identiteiten](manage-user-accounts-graph-api.md#identities-property))|Tekenreeks|Eén gebruikersidentiteit van de externe identiteitsprovider.|Nee|Nee|Invoer, Persistent, Output|
|alternativeSecurityIds ([Identiteiten](manage-user-accounts-graph-api.md#identities-property))|alternatieve securityId-verzameling|Een verzameling gebruikersidentiteiten van externe identiteitsproviders.|Nee|Nee|Persistented, Output|
|city            |Tekenreeks|De stad waarin de gebruiker zich bevindt. Maximale lengte 128.|Ja|Ja|Persistented, Output|
|toestemmingProvidedForMinor|Tekenreeks|Of de toestemming is verleend voor een minderjarige. Toegestane waarden: null, verleend, geweigerd of nietVereist.|Ja|Nee|Persistented, Output|
|land         |Tekenreeks|Het land/de regio waarin de gebruiker zich bevindt. Voorbeeld: "VS" of "UK". Maximale lengte 128.|Ja|Ja|Persistented, Output|
|createdDateTime|DateTime|De datum waarop het gebruikersobject is gemaakt. Alleen lezen.|Nee|Nee|Persistented, Output|
|creationType    |Tekenreeks|Als het gebruikersaccount is gemaakt als een lokaal account voor een Azure Active Directory B2C-tenant, is de waarde LocalAccount of nameCoexistence. Alleen lezen.|Nee|Nee|Persistented, Output|
|datumOfBirth     |Date|Geboortedatum.|Nee|Nee|Persistented, Output|
|department      |Tekenreeks|De naam voor de afdeling waarin de gebruiker werkt. Maximale lengte 64.|Ja|Nee|Persistented, Output|
|displayName     |Tekenreeks|De weergavenaam voor de gebruiker. Maximale lengte 256.|Ja|Ja|Persistented, Output|
|facsimileTelefoonNummer<sup>1</sup>|Tekenreeks|Het telefoonnummer van de zakelijke faxvan de gebruiker.|Ja|Nee|Persistented, Output|
|givenName       |Tekenreeks|De voornaam (voornaam) van de gebruiker. Maximale lengte 64.|Ja|Ja|Persistented, Output|
|jobTitel        |Tekenreeks|De functietitel van de gebruiker. Maximale lengte 128.|Ja|Ja|Persistented, Output|
|onveranderlijkE id     |Tekenreeks|Een id die doorgaans wordt gebruikt voor gebruikers die zijn gemigreerd vanuit on-premises Active Directory.|Nee|Nee|Persistented, Output|
|legalAgeGroupClassificatie|Tekenreeks|Wettelijke leeftijdscategorieclassificatie. Alleen-lezen en berekend op basis van ageGroup en consentProvidedForMinor eigenschappen. Toegestane waarden: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult en adult.|Ja|Nee|Persistented, Output|
|legaalLand<sup>1</sup>  |Tekenreeks|Land voor juridische doeleinden.|Nee|Nee|Persistented, Output|
|mail            |Tekenreeks|Het SMTP-adres voor de gebruiker, bijvoorbeeld "bob@contoso.com". Alleen-lezen.|Nee|Nee|Persistented, Output|
|mailNickName    |Tekenreeks|De e-mailalias voor de gebruiker. Maximale lengte 64.|Nee|Nee|Persistented, Output|
|mobiel (mobiel) |Tekenreeks|Het primaire mobiele telefoonnummer voor de gebruiker. Maximale lengte 64.|Ja|Nee|Persistented, Output|
|Netid           |Tekenreeks|Netto-ID.|Nee|Nee|Persistented, Output|
|objectId        |Tekenreeks|Een wereldwijd unieke id (GUID) die de unieke id voor de gebruiker is. Voorbeeld: 12345678-9abc-def0-1234-56789abcde. Alleen lezen, onveranderlijk.|Alleen-lezen|Ja|Invoer, Persistent, Output|
|andereMails      |Tekenreeksverzameling|Een lijst met extra e-mailadressen voor de gebruiker. Voorbeeld: ["bob@contoso.comRobert@fabrikam.com", " "].|Ja (Alternatieve e-mail)|Nee|Persistented, Output|
|wachtwoord        |Tekenreeks|Het wachtwoord voor het lokale account tijdens het maken van de gebruiker.|Nee|Nee|Volhard|
|wachtwoordBeleid     |Tekenreeks|Beleid van het wachtwoord. Het is een tekenreeks die bestaat uit verschillende beleidsnaam gescheiden door komma' s. d.w.z. "DisablePasswordExpiration, DisableStrongPassword".|Nee|Nee|Persistented, Output|
|physicalDeliveryOfficeName (officeLocatie)|Tekenreeks|De kantoorlocatie in de plaats van de gebruiker. Maximale lengte 128.|Ja|Nee|Persistented, Output|
|Postcode      |Tekenreeks|De postcode voor het postadres van de gebruiker. De postcode is specifiek voor het land/de regio van de gebruiker. In de Verenigde Staten van Amerika bevat dit kenmerk de postcode. Maximale lengte 40.|Ja|Nee|Persistented, Output|
|voorkeurTaal    |Tekenreeks|De voorkeurstaal voor de gebruiker. Moet volgen ISO 639-1 Code. Voorbeeld: "en-US".|Nee|Nee|Persistented, Output|
|refreshTokensValidFromDateTime|DateTime|Alle vernieuwingstokens die vóór deze tijd zijn uitgegeven, zijn ongeldig en toepassingen krijgen een foutmelding wanneer u een ongeldig vernieuwingstoken gebruikt om een nieuw toegangstoken te verkrijgen. Als dit gebeurt, moet de toepassing een nieuw vernieuwingstoken aanschaffen door een aanvraag in te dienen bij het geautoriseerde eindpunt. Alleen-lezen.|Nee|Nee|Uitvoer|
|signInNames ([Identiteiten](manage-user-accounts-graph-api.md#identities-property)) |Tekenreeks|De unieke aanmeldingsnaam van de lokale accountgebruiker van elk type in de map. Gebruik dit om een gebruiker met aanmeldingswaarde te krijgen zonder het lokale accounttype op te geven.|Nee|Nee|Invoer|
|signInNames.userName[(Identiteiten](manage-user-accounts-graph-api.md#identities-property)) |Tekenreeks|De unieke gebruikersnaam van de lokale accountgebruiker in de directory. Gebruik dit om een gebruiker met een specifieke aanmeldingsgebruikersnaam te maken of te krijgen. Als u dit alleen in PersistedClaims opgeeft tijdens de bewerking Patch, worden andere typen signInNames verwijderd. Als u een nieuw type signInNames wilt toevoegen, moet u ook bestaande signInNames blijven ondertekenen.|Nee|Nee|Invoer, Persistent, Output|
|signInNames.phoneNumber ([Identiteiten](manage-user-accounts-graph-api.md#identities-property)) |Tekenreeks|Het unieke telefoonnummer van de lokale accountgebruiker in de directory. Gebruik dit om een gebruiker te maken of te krijgen met een specifiek aanmeldingstelefoonnummer. Als u dit alleen in PersistedClaims opgeeft tijdens de bewerking Patch, worden andere typen signInNames verwijderd. Als u een nieuw type signInNames wilt toevoegen, moet u ook bestaande signInNames blijven ondertekenen.|Nee|Nee|Invoer, Persistent, Output|
|signInNames.emailAddress[(Identiteiten](manage-user-accounts-graph-api.md#identities-property))|Tekenreeks|Het unieke e-mailadres van de lokale accountgebruiker in de directory. Gebruik dit om een gebruiker met een specifiek e-mailadres aan te melden of te krijgen. Als u dit alleen in PersistedClaims opgeeft tijdens de bewerking Patch, worden andere typen signInNames verwijderd. Als u een nieuw type signInNames wilt toevoegen, moet u ook bestaande signInNames blijven ondertekenen.|Nee|Nee|Invoer, Persistent, Output|
|state           |Tekenreeks|De staat of provincie op het adres van de gebruiker. Maximale lengte 128.|Ja|Ja|Persistented, Output|
|streetAddress   |Tekenreeks|Het adres van de plaats van de gebruiker van het bedrijfsleven. Maximale lengte 1024.|Ja|Ja|Persistented, Output|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Tekenreeks|Het secundaire telefoonnummer van de gebruiker, gebruikt voor multi-factor authenticatie.|Ja|Nee|Persistented, Output|
|strongAuthenticationEmailAddress<sup>1</sup>|Tekenreeks|Het SMTP-adres voor de gebruiker. Voorbeeld:bob@contoso.com" " Dit kenmerk wordt gebruikt voor aanmelding met gebruikersnaambeleid om het e-mailadres van de gebruiker op te slaan. Het e-mailadres dat vervolgens wordt gebruikt in een wachtwoordresetstroom.|Ja|Nee|Persistented, Output|
|strongAuthenticationPhoneNummer<sup>1</sup>|Tekenreeks|Het primaire telefoonnummer van de gebruiker, gebruikt voor multi-factor authenticatie.|Ja|Nee|Persistented, Output|
|surname         |Tekenreeks|De achternaam van de gebruiker (familienaam of achternaam). Maximale lengte 64.|Ja|Ja|Persistented, Output|
|telefoonNummer (eerste vermelding van businessPhones)|Tekenreeks|Het primaire telefoonnummer van de plaats van de gebruiker.|Ja|Nee|Persistented, Output|
|userPrincipalName    |Tekenreeks|De UPN (user Principal name) van de gebruiker. De UPN is een internet-stijl login naam voor de gebruiker op basis van de internet standaard RFC 822. Het domein moet aanwezig zijn in de verzameling geverifieerde domeinen van de tenant. Deze eigenschap is vereist wanneer een account wordt gemaakt. Onveranderlijke.|Nee|Nee|Invoer, Persistent, Output|
|gebruikLocatie   |Tekenreeks|Vereist voor gebruikers die licenties krijgen toegewezen vanwege de wettelijke verplichting om te controleren op beschikbaarheid van services in landen. Niet te niet te doen. Een landcode met twee letters (ISO-norm 3166). Voorbeelden: "VS", "JP" en "GB".|Ja|Nee|Persistented, Output|
|userType        |Tekenreeks|Een tekenreekswaarde die kan worden gebruikt om gebruikerstypen in uw map te classificeren. Waarde moet lid zijn. Alleen-lezen.|Alleen-lezen|Nee|Persistented, Output|
|userState (externalUserState)<sup>2</sup>|Tekenreeks|Geeft alleen voor Azure AD B2B-account aan of de uitnodiging in behandeling is Geaccepteerd of geaccepteerd.|Nee|Nee|Persistented, Output|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Toont de tijdstempel voor de laatste wijziging in de eigenschap UserState.|Nee|Nee|Persistented, Output|
|<sup>1.</sup> Niet ondersteund door Microsoft Graph<br><sup>2.</sup> Mag niet worden gebruikt met Azure AD B2C||||||


## <a name="extension-attributes"></a>Extensiekenmerken

U moet vaak uw eigen kenmerken maken, zoals in de volgende gevallen:

- Een klantgerichte toepassing moet blijven bestaan voor een kenmerk als **LoyaltyNumber.**
- Een identiteitsprovider heeft een unieke gebruikers-id zoals **uniekeUserGUID** die moet worden opgeslagen.
- Een aangepaste gebruikersreis moet blijven bestaan voor een status van een gebruiker, zoals **migratieStatus**.

Azure AD B2C breidt de set kenmerken die zijn opgeslagen op elk gebruikersaccount uit. Extensiekenmerken [breiden het schema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) van de gebruikersobjecten in de map uit. De extensiekenmerken kunnen alleen worden geregistreerd op een toepassingsobject, ook al kunnen ze gegevens voor een gebruiker bevatten. Het extensiekenmerk is gekoppeld aan de toepassing b2c-extensions-app. Wijzig deze toepassing niet, omdat deze wordt gebruikt door Azure AD B2C voor het opslaan van gebruikersgegevens. U deze toepassing vinden onder Azure Active Directory App-registraties.

> [!NOTE]
> - Er kunnen maximaal 100 extensiekenmerken naar elk gebruikersaccount worden geschreven.
> - Als de b2c-extensies-app-toepassing wordt verwijderd, worden deze extensiekenmerken verwijderd van alle gebruikers, samen met alle gegevens die ze bevatten.
> - Als een extensiekenmerk door de toepassing wordt verwijderd, wordt het verwijderd uit alle gebruikersaccounts en worden de waarden verwijderd.
> - De onderliggende naam van het extensiekenmerk wordt gegenereerd in de indeling "Extension_" + Toepassings-id + "_" + Kenmerknaam. Als u bijvoorbeeld een extensiekenmerk LoyaltyNumber maakt en de toepassing-id van b2c-extensies-app 831374b3-bd50-41bf-aa54-263ec9e050fc is, is de onderliggende naam van het extensiekenmerk: extension_831374b3bd5041bfaa54263ec9e050fc_ extension_831374b3bd5041bfaa54263ec9e050fc_ Loyaliteitsnummer. U gebruikt de onderliggende naam wanneer u API-query's in Grafiek uitvoert om gebruikersaccounts te maken of bij te werken.

De volgende gegevenstypen worden ondersteund bij het definiëren van een eigenschap in een schema-extensie:

|Eigenschappentype |Opmerkingen  |
|--------------|---------|
|Booleaans    | Mogelijke waarden: **waar** of **onwaar**. |
|DateTime   | Moet worden opgegeven in iso 8601-indeling. Wordt opgeslagen in UTC.   |
|Geheel getal    | 32-bits waarde.               |
|Tekenreeks     | Maximaal 256 tekens.     |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over extensiekenmerken:
- [Schema-uitbreidingen](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Aangepaste kenmerken definiëren met gebruikersstroom](user-flow-custom-attributes.md)
- [Aangepaste kenmerken definiëren met aangepast beleid](custom-policy-custom-attributes.md)
