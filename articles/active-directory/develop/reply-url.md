---
title: Omleidings-URI-& antwoord-URL-beperkingen-micro soft Identity-platform | Azure
description: Antwoord Url's/omleidings-Url's beperkingen & beperkingen
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 7e289b83daa9c30703d94a7f4c0ff459f96256c0
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702518"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Beperkingen voor omleidings-URI en antwoord-URL

Een omleidings-URI of antwoord-URL is de locatie waarnaar de autorisatie server de gebruiker verzendt zodra de app is geautoriseerd en een autorisatie code of toegangs token heeft verleend. De code of het token bevindt zich in de omleidings-URI of het antwoord token. het is belang rijk dat u de juiste locatie registreert als onderdeel van het registratie proces van de app.

## <a name="maximum-number-of-redirect-uris"></a>Maximum aantal omleidings-Uri's

In de volgende tabel ziet u het maximum aantal omleidings-Uri's dat u kunt toevoegen wanneer u uw app registreert.

| Accounts waarbij wordt aangemeld | Maximum aantal omleidings-Uri's | Beschrijving |
|--------------------------|---------------------------------|-------------|
| Micro soft-werk-of school accounts in de Tenant van de Azure Active Directory van een organisatie (Azure AD) | 256 | `signInAudience` veld in het toepassings manifest is ingesteld op ofwel *AzureADMyOrg* of *AzureADMultipleOrgs* |
| Persoonlijke micro soft-accounts en werk-en school accounts | 100 | `signInAudience` veld in het manifest van de toepassing is ingesteld op *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximale URI-lengte

U kunt Maxi maal 256 tekens gebruiken voor elke omleidings-URI die u toevoegt aan een app-registratie.

## <a name="supported-schemes"></a>Ondersteunde schema's
Het Azure AD-toepassings model ondersteunt nu HTTP-en HTTPS-schema's voor apps die zich aanmelden bij micro soft-werk-of school accounts in de Azure Active Directory (Azure AD)-Tenant van een organisatie. Dat is `signInAudience` veld in het toepassings manifest is ingesteld op *AzureADMyOrg* of *AzureADMultipleOrgs*. Voor de apps die persoonlijke micro soft-accounts en werk-en school accounts aanmelden (die `signInAudience` is ingesteld op *AzureADandPersonalMicrosoftAccount*), is alleen https-schema toegestaan.

> [!NOTE]
> Met de nieuwe [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring kunnen ontwikkel aars geen uri's toevoegen met een HTTP-schema in de gebruikers interface. Het toevoegen van HTTP-Uri's voor apps die aanmelden op werk-of school accounts worden alleen ondersteund via de manifest editor van de app. Nieuwe apps kunnen geen HTTP-schema's gebruiken in de omleidings-URI. Oudere apps die HTTP-schema's in omleidings-Uri's bevatten, blijven echter werken. Ontwikkel aars moeten HTTPS-schema's gebruiken in de omleidings-Uri's.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Beperkingen met behulp van een Joker teken in Uri's

Joker teken-Uri's, zoals `https://*.contoso.com`, zijn handig, maar moeten worden vermeden. Het gebruik van joker tekens in de omleidings-URI heeft gevolgen voor de beveiliging. Volgens de OAuth 2,0-specificatie ([sectie 3.1.2 van RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) moet een omleidings EINDPUNT-URI een absolute URI zijn. 

Het Azure AD-toepassings model biedt geen ondersteuning voor joker tekens voor apps die zijn geconfigureerd om persoonlijke micro soft-accounts en werk-of school accounts te ondertekenen. Joker teken-Uri's zijn echter toegestaan voor apps die zijn geconfigureerd om werk-of school accounts te registreren in de Azure AD-Tenant van een organisatie. 
 
> [!NOTE]
> Met de nieuwe [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring kunnen ontwikkel aars geen joker tekens toevoegen aan de gebruikers interface. Het toevoegen van een Joker teken-URI voor apps die aanmelden op werk-of school accounts wordt alleen ondersteund via de manifest editor van de app. Door te gaan, kunnen nieuwe apps geen joker tekens gebruiken in de omleidings-URI. Oudere apps die joker tekens bevatten in omleidings-Uri's, blijven echter werken.

Als uw scenario meer omleidings-Uri's vereist dan de Maxi maal toegestane limiet, kunt u een van de volgende benaderingen gebruiken in plaats van het toevoegen van een URI voor het omleiden van joker tekens.

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

### <a name="add-redirect-uris-to-service-principals"></a>Omleidings-Uri's toevoegen aan service-principals

Een andere manier is om omleidings-Uri's toe te voegen aan de [service-principals](app-objects-and-service-principals.md#application-and-service-principal-relationship) die de registratie van uw app in een Azure AD-Tenant vertegenwoordigen. U kunt deze aanpak gebruiken wanneer u geen para meter State of uw scenario gebruikt om nieuwe omleidings-Uri's toe te voegen aan de registratie van uw app voor elke nieuwe Tenant die u ondersteunt. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [toepassings manifest](reference-app-manifest.md)
