---
title: Overzicht van uitgever verificatie-micro soft Identity-platform | Azure
description: Hierin wordt een overzicht gegeven van het Publisher-verificatie programma voor het micro soft Identity-platform. Een lijst met de voor delen, programma vereisten en veelgestelde vragen. Wanneer een toepassing is gemarkeerd als uitgever, betekent dit dat de uitgever zijn of haar identiteit heeft gecontroleerd met behulp van een Microsoft Partner Network account dat het verificatie proces heeft voltooid en dat dit MPN-account is gekoppeld aan de registratie van de toepassing.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 07e8f1d0cf173a8ab41f8feb105743a7559cdd2f
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "90005788"
---
# <a name="publisher-verification"></a>Uitgever verificatie

Verificatie van de uitgever helpt beheerders en eind gebruikers begrijpen van de echtheid van toepassings ontwikkelaars die integreren met het micro soft Identity-platform. Wanneer een toepassing is gemarkeerd als uitgever, betekent dit dat de uitgever zijn of haar identiteit heeft gecontroleerd met behulp van een [Microsoft Partner Network](https://partner.microsoft.com/membership) account dat het [verificatie](/partner-center/verification-responses) proces heeft voltooid en dat dit MPN-account is gekoppeld aan de registratie van de toepassing. 

Een ' geverifieerde ' badge wordt weer gegeven op de Azure AD-toestemming prompt en andere schermen: ![ toestemming vragen](./media/publisher-verification-overview/consent-prompt.png)

Deze functie is voornamelijk bedoeld voor ontwikkel aars die multi tenant-apps bouwen die gebruikmaken van [OAuth 2,0 en OpenID connect verbinding maken](active-directory-v2-protocols.md) met het [micro soft Identity-platform](v2-overview.md). Met deze apps kunnen gebruikers worden ondertekend via OpenID Connect Connect of kunnen ze OAuth 2,0 gebruiken om toegang tot gegevens te vragen met behulp van Api's zoals [Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Voordelen
Verificatie van de uitgever biedt de volgende voor delen:
- **Grotere transparantie en risico verlaging voor klanten**: met deze mogelijkheid kunnen klanten begrijpen welke apps in hun organisaties worden gebruikt, gepubliceerd door ontwikkel aars die ze vertrouwen. 

- **Verbeterde huis stijl**: een ' geverifieerde ' badge wordt weer gegeven op de Azure AD- [opdracht prompt voor toestemming](application-consent-experience.md), de pagina voor zakelijke apps en extra UX-Opper vlakken die worden gebruikt door eind gebruikers en beheerders. 

- **Vloeiendere ondernemings acceptatie**: beheerders kunnen het nieuwe beleid voor gebruikers toestemming configureren en de verificatie status van de uitgever is een van de primaire beleids criteria. 

- **Verbeterde risico beoordeling**: de micro soft-detecties voor toestemmings aanvragen voor ' Risk ante ' bevatten uitgevers verificatie als signaal. 

## <a name="requirements"></a>Vereisten
Er zijn enkele vereisten voor Publisher-verificatie, waarvan sommige micro soft-partners al zijn voltooid. Dit zijn: 

-  Een MPN-ID voor een geldig [Microsoft Partner Network](https://partner.microsoft.com/membership) account dat het [verificatie](/partner-center/verification-responses) proces heeft voltooid. Dit MPN-account moet het [globale account van de partner (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) voor uw organisatie zijn. 

-  Een app die is geregistreerd in een Azure AD-Tenant, waarbij een [Uitgever domein](howto-configure-publisher-domain.md) is geconfigureerd.

-  Het domein van het e-mail adres dat wordt gebruikt tijdens de verificatie van het MPN-account moet overeenkomen met het Uitgever domein dat is geconfigureerd op de app of een aangepast DNS- [domein](../fundamentals/add-custom-domain.md) dat is toegevoegd aan de Azure AD-Tenant. 

-  De gebruiker die de verificatie uitvoert, moet gemachtigd zijn om wijzigingen aan te brengen in de app-registratie in azure AD en het MPN-account in partner centrum. 

    -  In azure AD moet deze gebruiker lid zijn van een van de volgende [rollen](../users-groups-roles/directory-assign-admin-roles.md): toepassings beheerder, Cloud toepassings beheerder of globale beheerder. 

    -  In het partner centrum moet deze gebruiker over de volgende [rollen](/partner-center/permissions-overview)beschikken: MPN admin, accounts Administrator of een globale beheerder (dit is een gedeelde rol in azure AD).
    
-  De uitgever gaat akkoord met de [gebruiks voorwaarden van het micro soft-identiteits platform voor ontwikkel aars](/legal/microsoft-identity-platform/terms-of-use).

Ontwikkel aars die al aan deze vereisten hebben voldaan, kunnen binnen een paar minuten worden geverifieerd. Als niet aan de vereisten wordt voldaan, is het instellen van de installatie gratis. 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen 
Hieronder vindt u enkele veelgestelde vragen met betrekking tot het Uitgever verificatie programma. Zie [een app markeren als uitgever is geverifieerd](mark-app-as-publisher-verified.md)voor veelgestelde vragen met betrekking tot de vereisten en het proces.

- **Welke gegevens worden door de __Uitgever gecontroleerd?__**  Wanneer een toepassing is gemarkeerd als uitgever, geeft deze niet aan of de toepassing of de uitgever specifieke certificeringen heeft behaald, voldoet aan de industrie normen, voldoet aan de aanbevolen procedures, enzovoort. Andere micro soft-Program ma's geven deze informatie, waaronder [Microsoft 365 app-certificering](/microsoft-365-app-certification/overview).

- **Wat kost dit? Is er een licentie nodig?** Micro soft berekent geen ontwikkel aars voor Publisher-verificatie en vereist geen specifieke licentie. 

- **Wat is de relatie tussen Microsoft 365 Uitgever van uitgevers? Hoe zit het met Microsoft 365 app-certificering?** Dit zijn aanvullende Program ma's die ontwikkel aars kunnen gebruiken om betrouw bare apps te maken die door klanten kunnen worden goedgekeurd. Verificatie van de uitgever is de eerste stap in dit proces en moet worden voltooid door alle ontwikkel aars die apps maken die voldoen aan de bovenstaande criteria. 

  Ontwikkel aars die ook met Microsoft 365 integreren, kunnen extra voor delen ontvangen van deze Program ma's. Raadpleeg voor meer informatie [Microsoft 365 Uitgever verklaring](/microsoft-365-app-certification/docs/attestation) en [Microsoft 365 app-certificering](/microsoft-365-app-certification/docs/certification). 

- **Is dit hetzelfde als de Azure AD-toepassings galerie?** Geen uitgever verificatie is een complementair, maar afzonderlijk programma aan de [Azure Active Directory-toepassings galerie](../azuread-dev/howto-app-gallery-listing.md). Ontwikkel aars die voldoen aan de bovenstaande criteria, moeten het verificatie proces voor de uitgever onafhankelijk van deelname aan het programma volt ooien. 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [markeren van een app als uitgever gecontroleerd](mark-app-as-publisher-verified.md).
* [Problemen met](troubleshoot-publisher-verification.md) de uitgever verificatie oplossen.
