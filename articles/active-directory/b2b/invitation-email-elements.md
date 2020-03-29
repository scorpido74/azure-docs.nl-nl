---
title: Elementen van de e-mail met B2B-uitnodigingen - Azure Active Directory | Microsoft Documenten
description: E-mailsjabloon voor uitnodiging voor Azure Active Directory B2B-uitnodiging
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65768370"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>De elementen van de e-mail met uitnodiging voor B2B-samenwerking - Azure Active Directory

Uitnodigingse-mails zijn een essentieel onderdeel om partners aan boord te krijgen als B2B-samenwerkingsgebruikers in Azure AD. U ze gebruiken om het vertrouwen van de ontvanger te vergroten. u legitimiteit en sociaal bewijs toevoegen aan de e-mail, om ervoor te zorgen dat de ontvanger zich op zijn gemak voelt bij het selecteren van de knop **Aan de slag** om de uitnodiging te accepteren. Dit vertrouwen is een belangrijk middel om het delen van frictie te verminderen. En je wilt ook de e-mail er geweldig uitzien!

![Schermafbeelding van de e-mail met de B2B-uitnodiging](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Uitleg over de e-mail
Laten we eens kijken naar een paar elementen van de e-mail, zodat u weet hoe het beste om hun mogelijkheden te gebruiken.

### <a name="subject"></a>Onderwerp
Het onderwerp van de e-mail volgt het &lt;volgende&gt; patroon: U wordt uitgenodigd voor de tenantnameorganisatie

### <a name="from-address"></a>Van-adres
We gebruiken een LinkedIn-achtig patroon voor het Van-adres.  U moet duidelijk zijn wie de genodigde is en van welk bedrijf, en ook verduidelijken dat de e-mail afkomstig is van een Microsoft-e-mailadres. De indeling is: <invites@microsoft.com> &lt;Microsoft-uitnodigingen&gt; of &lt;Weergavenaam van genodigden van tenantnaam&gt; (via Microsoft). <invites@microsoft.com>

### <a name="reply-to"></a>Reageren op
De e-mail met antwoord wordt ingesteld op de e-mail van de genodigde wanneer deze beschikbaar is, zodat het beantwoorden van de e-mail een e-mail terugstuurt naar de genodigde.

### <a name="branding"></a>Huisstijl
De uitnodigingse-mails van uw tenant maken gebruik van de bedrijfsbranding die u mogelijk hebt ingesteld voor uw tenant. Als u wilt profiteren van deze mogelijkheid, [hier](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) zijn de details over hoe u het configureren. Het bannerlogo verschijnt in de e-mail. Volg [hier](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) de afbeeldingsgrootte en kwaliteitsinstructies voor de beste resultaten. Daarnaast duikt de bedrijfsnaam ook op in de call to action.

### <a name="call-to-action"></a>Oproep tot actie
De call-to-action bestaat uit twee delen: uitleggen waarom de ontvanger de e-mail heeft ontvangen en wat de ontvanger wordt gevraagd om eraan te doen.
- De sectie 'waarom' kan worden aangepakt met het volgende patroon: U &lt;bent&gt; uitgenodigd om toegang te krijgen tot toepassingen in de tenantnameorganisatie

- En de sectie "wat u wordt gevraagd om te doen" wordt aangegeven door de aanwezigheid van de knop **Aan de slag.** Wanneer de ontvanger is toegevoegd zonder uitnodigingen nodig te zijn, wordt deze knop niet weergegeven.

### <a name="inviters-information"></a>Informatie van genodigde
De weergavenaam van de genodigde is opgenomen in de e-mail. En als u bovendien een profielfoto voor uw Azure AD-account hebt ingesteld, bevat de uitnodigende e-mail die afbeelding ook. Beide zijn bedoeld om het vertrouwen van uw ontvanger in de e-mail te vergroten.

Als je je profielfoto nog niet hebt ingesteld, wordt een pictogram met de initialen van de genodigde in plaats van de foto weergegeven:

  ![Schermafbeelding van de uitnodiging met de initialen van de genodigde weergegeven](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Hoofdtekst
De instantie bevat het bericht dat de genodigde samenstelt wanneer hij [een gastgebruiker uitnodigt voor de directory, groep of app](add-users-administrator.md) of met behulp van de [uitnodigings-API.](customize-invitation-api.md) Het is een tekstgebied, dus het verwerkt geen HTML-tags om veiligheidsredenen.

  ![Schermafbeelding van de hoofdtekst van de uitnodigingse-mail](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Footer sectie
De voettekst bevat het microsoft-bedrijfsmerk en laat de ontvanger weten of de e-mail is verzonden vanuit een niet-bewaakte alias. 

Bijzondere gevallen:

- De genodigde heeft geen e-mailadres in de uitnodigende huurovereenkomst

  ![Schermafbeelding wanneer een genodigde geen e-mail heeft in de uitnodigende huurovereenkomst](media/invitation-email-elements/inviter-no-email.png)


- De ontvanger hoeft de uitnodiging niet in te wisselen

  ![Schermafbeelding wanneer de ontvanger de uitnodiging niet hoeft in te wisselen](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

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
