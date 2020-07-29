---
title: Omleidings-URI-& antwoord-URL-beperkingen-micro soft Identity-platform | Azure
description: Antwoord Url's/omleidings-Url's beperkingen & beperkingen
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/17/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 4fdeb0018e27a2557161b2ec1c4794d975403523
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87311616"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Beperkingen voor omleidings-URI en antwoord-URL

Een omleidings-URI of antwoord-URL is de locatie waarnaar de autorisatie server de gebruiker verzendt zodra de app is geautoriseerd en een autorisatie code of toegangs token heeft verleend. De code of het token bevindt zich in de omleidings-URI of het antwoord token. het is belang rijk dat u de juiste locatie registreert als onderdeel van het registratie proces van de app.

 De volgende beperkingen zijn van toepassing op antwoord-Url's:

* De antwoord-URL moet beginnen met het schema `https` .

* De antwoord-URL is hoofdletter gevoelig. Het hoofdletter gebruik moet overeenkomen met het URL-pad van de actieve toepassing. Als uw toepassing bijvoorbeeld een deel van het pad bevat `.../abc/response-oidc` , moet u niet opgeven `.../ABC/response-oidc` in de antwoord-URL. Omdat in de webbrowser paden worden beschouwd als hoofdletter gevoelig, kunnen cookies die zijn gekoppeld aan, `.../abc/response-oidc` worden uitgesloten als deze worden omgeleid naar de niet-overeenkomende `.../ABC/response-oidc` URL.
    
## <a name="maximum-number-of-redirect-uris"></a>Maximum aantal omleidings-Uri's

In de volgende tabel ziet u het maximum aantal omleidings-Uri's dat u kunt toevoegen wanneer u uw app registreert.

| Accounts waarbij wordt aangemeld | Maximum aantal omleidings-Uri's | Beschrijving |
|--------------------------|---------------------------------|-------------|
| Micro soft-werk-of school accounts in de Tenant van de Azure Active Directory van een organisatie (Azure AD) | 256 | `signInAudience`het veld in het toepassings manifest is ingesteld op *AzureADMyOrg* of *AzureADMultipleOrgs* |
| Persoonlijke micro soft-accounts en werk-en school accounts | 100 | `signInAudience`het veld in het manifest van de toepassing is ingesteld op *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximale URI-lengte

U kunt Maxi maal 256 tekens gebruiken voor elke omleidings-URI die u toevoegt aan een app-registratie.

## <a name="supported-schemes"></a>Ondersteunde schema's

Het Azure AD-toepassings model ondersteunt nu HTTP-en HTTPS-schema's voor apps die zich aanmelden bij micro soft-werk-of school accounts in de Azure Active Directory (Azure AD)-Tenant van een organisatie. Een `signInAudience` veld in het toepassings manifest is ingesteld op *AzureADMyOrg* of *AzureADMultipleOrgs*. Voor de apps die zich aanmelden persoonlijke micro soft-accounts en werk-en school accounts (die is `signInAudience` ingesteld op *AzureADandPersonalMicrosoftAccount*), is alleen https-schema toegestaan.

> [!NOTE]
> Met de nieuwe [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring kunnen ontwikkel aars geen uri's toevoegen met een HTTP-schema in de gebruikers interface. Het toevoegen van HTTP-Uri's voor apps die aanmelden op werk-of school accounts worden alleen ondersteund via de manifest editor van de app. Nieuwe apps kunnen geen HTTP-schema's gebruiken in de omleidings-URI. Oudere apps die HTTP-schema's in omleidings-Uri's bevatten, blijven echter werken. Ontwikkel aars moeten HTTPS-schema's gebruiken in de omleidings-Uri's.

## <a name="localhost-exceptions"></a>Localhost-uitzonde ringen

Per [RFC 8252-secties 8,3](https://tools.ietf.org/html/rfc8252#section-8.3) en [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), worden ' loop back-' of ' localhost ' omleidings-uri's geleverd met twee speciale overwegingen:

1. `http`URI-schema's zijn acceptabel omdat de omleiding nooit het apparaat verlaat.  Dit betekent dat dit `http://127.0.0.1/myApp` is toegestaan, maar `https://127.0.0.1/myApp` ook. 
1. Als gevolg van tijdelijke poortbereiken die vaak nodig zijn voor systeem eigen toepassingen, wordt het poort onderdeel (bijvoorbeeld `:5001` of `:443` ) genegeerd voor de doel einden van het afstemmen van een omleidings-URI.  Als gevolg hiervan, `http://127.0.0.1:5000/MyApp` en `http://127.0.0.1:1234/MyApp` beide overeenkomen `http://127.0.0.1/MyApp` , evenals`http://127.0.0.1:8080/MyApp`

Uit het oogpunt van ontwikkeling betekent dit een aantal dingen:

1. Registreer niet meerdere antwoord-Uri's waarbij alleen de poort verschilt.  De aanmeldings server kiest een wille keurige waarde en gebruikt het gedrag dat aan de antwoord-URI is gekoppeld (bijvoorbeeld of het een `web` , `native` , en `spa` -type omleiding is).
1. Als u meerdere omleidings-Uri's op localhost wilt registreren om verschillende stromen tijdens de ontwikkeling te testen, moet u deze onderscheiden met de *padcomponent* van de URI.  `http://127.0.0.1/MyWebApp`komt niet overeen `http://127.0.0.1/MyNativeApp` .  
1. Per RFC-richt lijn moet u niet gebruiken `localhost` in de omleidings-URI.  Gebruik in plaats daarvan het werkelijke loop back IP-adres- `127.0.0.1` . Hiermee wordt voor komen dat uw app wordt verbroken door onjuist geconfigureerde firewalls of de naam van netwerk interfaces.

>[!NOTE]
> Op dit moment wordt IPv6-loop back ( `[::1]` ) momenteel niet ondersteund.  Deze wordt op een later tijdstip toegevoegd.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Beperkingen met behulp van een Joker teken in Uri's

Joker teken-Uri's, zoals `https://*.contoso.com` , zijn handig, maar moeten worden vermeden. Het gebruik van joker tekens in de omleidings-URI heeft gevolgen voor de beveiliging. Volgens de OAuth 2,0-specificatie ([sectie 3.1.2 van RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) moet een omleidings EINDPUNT-URI een absolute URI zijn.

Het Azure AD-toepassings model biedt geen ondersteuning voor joker tekens voor apps die zijn geconfigureerd om persoonlijke micro soft-accounts en werk-of school accounts te ondertekenen. Joker teken-Uri's zijn echter toegestaan voor apps die zijn geconfigureerd om werk-of school accounts te registreren in de Azure AD-Tenant van een organisatie.

> [!NOTE]
> Met de nieuwe [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring kunnen ontwikkel aars geen joker tekens toevoegen aan de gebruikers interface. Het toevoegen van een Joker teken-URI voor apps die aanmelden op werk-of school accounts wordt alleen ondersteund via de manifest editor van de app. Door te gaan, kunnen nieuwe apps geen joker tekens gebruiken in de omleidings-URI. Oudere apps die joker tekens bevatten in omleidings-Uri's, blijven echter werken.

Als voor uw scenario meer omleidings-Uri's zijn vereist dan de Maxi maal toegestane limiet, moet u rekening houden met de volgende benadering, in plaats van het toevoegen van een Joker teken-URI.

### <a name="use-a-state-parameter"></a>Een para meter State gebruiken

Als u een aantal subdomeinen hebt, en als uw scenario vereist dat u gebruikers omleidt bij geslaagde verificatie op dezelfde pagina waar ze worden gestart, kan het handig zijn om een para meter State te gebruiken.

In deze benadering:

1. Maak een ' gedeelde ' omleidings-URI per toepassing voor het verwerken van de beveiligings tokens die u van het autorisatie-eind punt ontvangt.
1. Uw toepassing kan toepassingsspecifieke para meters (zoals subdomein-URL waar de gebruiker afkomstig is of iets zoals huismerk gegevens) verzenden in de para meter State. Wanneer u een para meter State gebruikt, beschermt u tegen CSRF-beveiliging zoals beschreven in [sectie 10,12 van RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. De toepassingsspecifieke para meters bevatten alle informatie die nodig is voor de toepassing om de juiste ervaring voor de gebruiker te genereren, dat wil zeggen, de juiste toepassings status te maken. De Azure AD-autorisatie-eind punt verwijdert HTML uit de para meter State, dus zorg ervoor dat u geen HTML-inhoud in deze para meter opgeeft.
1. Wanneer Azure AD een reactie verzendt naar de ' gedeelde ' omleidings-URI, wordt de para meter State teruggestuurd naar de toepassing.
1. De toepassing kan vervolgens de waarde in de para meter State gebruiken om te bepalen met welke URL de gebruiker verder moet worden verzonden. Zorg ervoor dat u valideert voor CSRF-beveiliging.

> [!NOTE]
> Met deze aanpak kan een aangetaste client de aanvullende para meters wijzigen die in de para meter State worden verzonden, waardoor de gebruiker wordt omgeleid naar een andere URL. Dit is de [Open redirector-bedreiging](https://tools.ietf.org/html/rfc6819#section-4.2.4) die wordt beschreven in RFC 6819. Daarom moet de client deze para meters beveiligen door de status te versleutelen of op een andere manier te verifiëren, zoals het valideren van de domein naam in de omleidings-URI op basis van het token.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [toepassings manifest](reference-app-manifest.md)
