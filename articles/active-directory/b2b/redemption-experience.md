---
title: Inwisseling van uitnodigingen in B2B-samenwerking - Azure AD
description: Beschrijft de azure AD B2B-uitnodigingsuitnodigingservaring voor eindgebruikers, inclusief de overeenkomst met privacyvoorwaarden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043e0f3a0ff2c1c642c63a387c571b575f77cf7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050842"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Inwisseling van Azure Active Directory B2B-uitnodigingvoor samenwerkingsuitnodiging

In dit artikel wordt beschreven hoe gastgebruikers toegang hebben tot uw bronnen en het toestemmingsproces dat ze tegenkomen. Als u een uitnodigingse-mail naar de gast stuurt, bevat de uitnodiging een link die de gast kan inwisselen om toegang te krijgen tot uw app of portal. De uitnodiginge-mail is slechts een van de manieren waarop gasten toegang kunnen krijgen tot uw bronnen. Als alternatief u gasten toevoegen aan uw directory en hen een directe link geven naar de portal of app die u wilt delen. Ongeacht de methode die ze gebruiken, worden gasten begeleid door een eerste toestemmingsproces. Dit proces zorgt ervoor dat uw gasten akkoord gaan met privacyvoorwaarden en alle [gebruiksvoorwaarden](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) accepteren die u hebt ingesteld.

Wanneer u een gastgebruiker toevoegt aan uw directory, heeft het gastgebruikersaccount een toestemmingsstatus (zichtbaar in PowerShell) die in eerste instantie is ingesteld **op In behandeling accepteren**. Deze instelling blijft totdat de gast uw uitnodiging accepteert en akkoord gaat met uw privacybeleid en gebruiksvoorwaarden. Daarna worden de toestemmingsstatus gewijzigd in **Geaccepteerd**en worden de toestemmingspagina's niet meer aan de gast gepresenteerd.

   > [!IMPORTANT]
   > **Vanaf 31 maart 2021**ondersteunt Microsoft niet langer het inwisselen van uitnodigingen door onbeheerde Azure AD-accounts en tenants te maken voor B2B-samenwerkingsscenario's. Ter voorbereiding moedigen we klanten aan om te kiezen voor [eenmalige wachtwoordverificatie voor e-mail.](one-time-passcode.md) We zijn blij met uw feedback over deze openbare preview-functie en zijn verheugd om nog meer manieren te creëren om samen te werken.

## <a name="redemption-through-the-invitation-email"></a>Inwisseling via de uitnodigingse-mail

Wanneer u een gastgebruiker toevoegt aan uw directory [met behulp van de Azure-portal,](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)wordt er een uitnodigingse-mail verzonden naar de gast in het proces. U er ook voor kiezen om uitnodigingse-mails te verzenden wanneer u [PowerShell gebruikt](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) om gastgebruikers aan uw directory toe te voegen. Hier is een beschrijving van de ervaring van de gast wanneer ze de link in de e-mail inwisselen.

1. De gast ontvangt een [uitnodigingsmail](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) die is verzonden vanuit **Microsoft-uitnodigingen.**
2. De gast selecteert **Aan de slag** in de e-mail.
3. Als de gast geen Azure AD-account, een Microsoft-account (MSA) of een e-mailaccount in een federatieve organisatie heeft, wordt hij of zij gevraagd een MSA te maken (tenzij de [eenmalige toegangscodefunctie](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) is ingeschakeld, waarvoor geen MSA vereist is).
4. De gast wordt begeleid door de [onderstaande toestemmingservaring.](#consent-experience-for-the-guest)

## <a name="redemption-through-a-direct-link"></a>Inwisselen via een directe link

Als alternatief voor de uitnodigingse-mail u een gast een directe link naar uw app of portal geven. U moet eerst de gastgebruiker toevoegen aan uw directory via de [Azure-portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) of [PowerShell.](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) Vervolgens u een van de aanpasbare manieren gebruiken [om toepassingen te implementeren voor gebruikers,](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)inclusief directe aanmeldingskoppelingen. Wanneer een gast een directe link gebruikt in plaats van de uitnodigingse-mail, wordt hij/zij nog steeds door de eerste toestemmingservaring geleid.

> [!IMPORTANT]
> De directe link moet tenant-specifiek zijn. Met andere woorden, het moet een tenant-id of geverifieerd domein bevatten, zodat de gast kan worden geverifieerd in uw tenant, waar de gedeelde app zich bevindt. Een gemeenschappelijke https://myapps.microsoft.com URL zoals werkt niet voor een gast omdat deze wordt omgeleid naar zijn of haar thuistenant voor verificatie. Hier volgen enkele voorbeelden van directe koppelingen met de tenantcontext:
 > - Toegangspaneel voor https://myapps.microsoft.com/?tenantid=&ltapps: ;tenant-id&gt; 
 > - Toegangspaneel voor apps voor https://myapps.microsoft.com/&lteen geverifieerd domein: ;geverifieerd domein&gt;
 > - Azure-portal: https://portal.azure.com/&lt;tenant-id&gt;
 > - Individuele app: bekijk hoe u een [directe aanmeldingskoppeling gebruikt](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Er zijn enkele gevallen waarin de uitnodiging e-mail wordt aanbevolen via een directe link. Als deze speciale gevallen belangrijk zijn voor uw organisatie, raden we u aan gebruikers uit te nodigen met methoden die de uitnodigingse-mail nog steeds verzenden:
 - De gebruiker heeft geen Azure AD-account, een MSA of een e-mailaccount in een federatieve organisatie. Tenzij u de eenmalige toegangscodefunctie gebruikt, moet de gast de uitnodigingse-mail inwisselen om door de stappen voor het maken van een MSA te worden geleid.
 - Soms heeft het uitgenodigde gebruikersobject mogelijk geen e-mailadres vanwege een conflict met een contactobject (bijvoorbeeld een Outlook-contactobject). In dit geval moet de gebruiker op de inwissel-URL in de uitnodigingse-mail klikken.
 - De gebruiker kan zich aanmelden met een alias van het e-mailadres dat is uitgenodigd. (Een alias is een extra e-mailadres dat is gekoppeld aan een e-mailaccount.) In dit geval moet de gebruiker op de inwissel-URL in de uitnodigingse-mail klikken.

## <a name="consent-experience-for-the-guest"></a>Toestemmingservaring voor de gast

Wanneer een gast zich voor het eerst aanmeldt voor toegang tot bronnen in een partnerorganisatie, wordt deze door de volgende pagina's geleid. 

1. De gast bekijkt de pagina **Machtigingen van de beoordeling** waarin de privacyverklaring van de uitnodigende organisatie wordt beschreven. Een gebruiker moet het gebruik van zijn gegevens **accepteren** in overeenstemming met het privacybeleid van de uitnodigende organisatie om door te gaan.

   ![Schermafbeelding van de pagina Machtigingen controleren](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Zie [How-to: De privacygegevens van uw organisatie toevoegen in Azure Active Directory](https://aka.ms/adprivacystatement)voor informatie over hoe u als tenantbeheerder koppelen aan de privacyverklaring van uw organisatie.

2. Als de gebruiksvoorwaarden zijn geconfigureerd, opent en beoordeelt de gast de gebruiksvoorwaarden en selecteert hij **Accepteren**. 

   ![Schermafbeelding van nieuwe gebruiksvoorwaarden](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > U [gebruiksvoorwaarden](../governance/active-directory-tou.md) configureren in > **Gebruiksvoorwaarden** > **Terms of use** **beheren.**

3. Tenzij anders aangegeven, wordt de gast doorgestuurd naar het toegangspaneel Apps, waarin de toepassingen worden weergegeven die de gast kan openen.

   ![Schermafbeelding van het deelvenster Apps-toegang](media/redemption-experience/myapps.png) 

In uw directory wordt de **geaccepteerde** waarde van de gast geaccepteerd in **Ja**. Als er een MSA is gemaakt, wordt in de **bron** van de gast **Microsoft-account weergegeven.** Zie Eigenschappen van een Azure [AD B2B-samenwerkingsgebruiker](user-properties.md)voor meer informatie over de eigenschappen van gastgebruikersaccount. 

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen aan de Azure-portal](add-users-administrator.md)
- [Hoe voegen informatiewerkers B2B-samenwerkingsgebruikers toe aan Azure Active Directory?](add-users-information-worker.md)
- [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen met PowerShell](customize-invitation-api.md#powershell)
- [Een organisatie verlaten als gastgebruiker](leave-the-organization.md)
