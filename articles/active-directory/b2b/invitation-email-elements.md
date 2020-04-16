---
title: Elementen van de e-mail met B2B-uitnodigingen - Azure Active Directory | Microsoft Documenten
description: E-mailsjabloon voor uitnodiging voor Azure Active Directory B2B-uitnodiging
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407226"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>De elementen van de e-mail met uitnodiging voor B2B-samenwerking - Azure Active Directory

Uitnodigingse-mails zijn een essentieel onderdeel om partners aan boord te krijgen als B2B-samenwerkingsgebruikers in Azure AD. Hoewel het niet vereist is [dat u een e-mail stuurt om iemand uit te nodigen met behulp van B2B-samenwerking,](add-user-without-invite.md)geeft dit de gebruiker alle informatie die hij nodig heeft om een beslissing te nemen over het al dan niet accepteren van uw uitnodiging. Het geeft hen ook een link die ze altijd kunnen verwijzen naar in de toekomst wanneer ze nodig hebben om terug te keren naar uw middelen.

![Schermafbeelding van de e-mail met de B2B-uitnodiging](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Deze nieuwe e-mailsjabloon wordt nog steeds uitgerold naar alle tenants, dus sommige huurders gebruiken nog steeds een ouder ontwerp. Eind mei 2020 zullen uitnodigingen van alle huurders gebruik maken van deze sjabloon.

## <a name="explaining-the-email"></a>Uitleg over de e-mail

Laten we eens kijken naar een paar elementen van de e-mail, zodat u weet hoe het beste om hun mogelijkheden te gebruiken.

### <a name="subject"></a>Onderwerp

Het onderwerp van de e-mail volgt dit patroon:

&lt;gebruikersnaam&gt; nodigde u uit om toegang te krijgen tot toepassingen binnen hun organisatie.

### <a name="from-address"></a>Van-adres

We gebruiken een LinkedIn-achtig patroon voor het Van-adres. Dit patroon moet duidelijk maken dat, invites@microsoft.comhoewel de e-mail afkomstig is van, de uitnodiging is van een andere organisatie. De indeling is: <invites@microsoft.com> Microsoft Uitnodigingen &lt;of&gt; <invites@microsoft.com>Microsoft uitnodigingen namens tenantname . 

### <a name="reply-to"></a>Reageren op

De e-mail met antwoord wordt ingesteld op de e-mail van de genodigde wanneer deze beschikbaar is, zodat het beantwoorden van de e-mail een e-mail terugstuurt naar de genodigde.

### <a name="phishing-warning"></a>Waarschuwing voor phishing

De e-mail begint met een korte waarschuwing aan de gebruiker over phishing, hen te waarschuwen dat ze alleen uitnodigingen moeten accepteren die ze verwachten. Het is een goede gewoonte om ervoor te zorgen dat de partners die u uitnodigt niet verrast zullen zijn door uw uitnodiging door het van tevoren aan hen te vermelden.

![Afbeelding van de phishingwaarschuwing in de e-mail](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Informatie van genodigde

De e-mail bevat informatie over de genodigde en de organisatie van wie ze de uitnodiging verzenden. Dit omvat de naam en het e-mailadres van de afzender, evenals de naam en het primaire domein dat aan de organisatie is gekoppeld. Al deze informatie moet de genodigde helpen een weloverwogen beslissing te nemen over het accepteren van de uitnodiging.

![Afbeelding van de informatie van de genodigde in de e-mail](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Uitnodigingsbericht

Als de genodigde een bericht als onderdeel van zijn uitnodiging bevat wanneer hij [een gastgebruiker uitnodigt voor de directory, groep of app](add-users-administrator.md) of wanneer hij de [uitnodigings-API gebruikt,](customize-invitation-api.md)wordt het bericht gemarkeerd in het hoofdgedeelte van de e-mail. Ook de naam en profielafbeelding van de genodigde zijn inbegrepen als deze een afbeelding heeft ingesteld. Het bericht zelf is een tekstgebied, dus om veiligheidsredenen verwerkt het geen HTML-tags.

![Afbeelding van het uitnodigingsbericht in de e-mail](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Knop Accepteren en URL omleiden

Het volgende gedeelte van de e-mail bevat informatie over waar de genodigde zal worden genomen nadat ze de uitnodiging hebben geaccepteerd, evenals een knop om dit te doen.  In de toekomst kan de genodigde deze link altijd gebruiken om rechtstreeks terug te keren naar uw bronnen.

![Afbeelding van de knop Accepteren en URL omleiden in de e-mail](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Footer sectie

De voettekst bevat meer informatie over de uitnodiging die wordt verzonden. Er is altijd een optie voor de genodigde om toekomstige uitnodigingen te blokkeren. Als de organisatie [een privacyverklaring](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)heeft ingesteld, wordt de koppeling naar de verklaring hier weergegeven.  Anders geeft een notitie aan dat de organisatie geen privacyverklaring heeft ingesteld.

![Afbeelding van de footersectie in de e-mail](media/invitation-email-elements/footer-section.png)
 
## <a name="how-the-language-is-determined"></a>Hoe de taal wordt bepaald

De taal die aan de gastgebruiker wordt gepresenteerd in de uitnodigingse-mail wordt bepaald door de volgende instellingen. Deze instellingen worden weergegeven in volgorde van voorrang. Als een instelling niet is geconfigureerd, bepaalt de volgende instelling in de lijst de taal.

- De eigenschap **messageLanguage** van het object [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) als de uitnodigings-API maken wordt gebruikt
-   De eigenschap **preferredLanguage** die is opgegeven in het [gebruikersobject](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) van de gast
-   De **meldingstaal** die is ingesteld in de eigenschappen van de thuistenant van de gastgebruiker (alleen voor Azure AD-tenants)
-   De **notificatietaal** die is ingesteld in de eigenschappen van de resourcetenant

Als geen van deze instellingen is geconfigureerd, wordt de taal standaard ingesteld op Engels (VS).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over Azure AD B2B-samenwerking:

- [Wat is Azure AD B2B-samenwerking](what-is-b2b.md)
- [Hoe voegen Azure Active Directory-beheerders B2B-samenwerkingsgebruikers toe?](add-users-administrator.md)
- [Hoe voegen informatiewerkers B2B-samenwerkingsgebruikers toe?](add-users-information-worker.md)
- [B2B-uitnodigingsuitnodiging en inwisseling](redemption-experience.md)
- [B2B-samenwerkingsgebruikers toevoegen zonder uitnodiging](add-user-without-invite.md)
