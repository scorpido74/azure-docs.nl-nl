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
ms.openlocfilehash: 0645807aa40557c163643f1393c310668518f9be
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535129"
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

## <a name="invitation-redemption-flow"></a>Stroom voor het inwisselen van uitnodigingen

Wanneer een gebruiker op de koppeling **Uitnodiging accepteren** in een [uitnodigingse-mail](invitation-email-elements.md)klikt, wisselt Azure AD de uitnodiging automatisch in op basis van de inwisselstroom zoals hieronder wordt weergegeven:

![Schermafbeelding van het diagram indewisselingsstroom](media/redemption-experience/invitation-redemption-flow.png)

1. Het inwisselproces controleert of de gebruiker een bestaand persoonlijk [Microsoft-account (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)heeft.

2. Als een beheerder [directe federatie](direct-federation.md)heeft ingeschakeld, controleert Azure AD of het domeinachtervoegsel van de gebruiker overeenkomt met het domein van een geconfigureerde SAML/WS-Fed-identiteitsprovider en wordt de gebruiker doorverwezen naar de vooraf geconfigureerde identiteitsprovider.

3. Als een beheerder [Google Federation](google-federation.md)heeft ingeschakeld, controleert Azure AD of het domeinachtervoegsel van de gebruiker is gmail.com of googlemail.com en wordt de gebruiker omgeleid naar Google.

4. Azure AD voert op gebruikers gebaseerde detectie uit om te bepalen of de gebruiker bestaat in een [bestaande Azure AD-tenant.](what-is-b2b.md#easily-add-guest-users-in-the-azure-ad-portal)

5. Zodra de **thuismap** van de gebruiker is geïdentificeerd, wordt de gebruiker naar de desbetreffende identiteitsprovider gestuurd om zich aan te melden.  

6. Als de stappen 1 tot en met 4 geen thuismap voor de uitgenodigde gebruiker vinden, bepaalt Azure AD of de uitnodigende tenant de [OTP-functie (E-mailonetime-toegangscode)](one-time-passcode.md) voor gasten heeft ingeschakeld.

7. Als [e-mail eenmalige toegangscode voor gasten is ingeschakeld,](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)wordt een toegangscode naar de gebruiker verzonden via de uitgenodigde e-mail. De gebruiker haalt deze toegangscode op en voert deze in op de aanmeldingspagina van Azure AD.

8. Als e-maileentoegangscode voor gasten is uitgeschakeld, controleert Azure AD het domeinachtervoegsel op een lijst met consumentendomeinen die door Microsoft worden onderhouden. Als het domein overeenkomt met een domein in de lijst met consumentendomeinen, wordt de gebruiker gevraagd een persoonlijk Microsoft-account te maken. Als dit niet het zo is, wordt de gebruiker gevraagd een [Azure AD-selfserviceaccount](../users-groups-roles/directory-self-service-signup.md) (viraal account) aan te maken.

9. Azure AD probeert een Azure AD-selfserviceaccount (viraal account) te maken door de toegang tot de e-mail te verifiëren. Het verifiëren van het account gebeurt door een code naar de e-mail te verzenden en de gebruiker deze op te laten halen en naar Azure AD te sturen. Als de tenant van de uitgenodigde gebruiker echter gefedereerd is of als het veld AllowEmailVerifiedUsers is ingesteld op false in de tenant van de uitgenodigde gebruiker, kan de gebruiker de inwisseling niet voltooien en resulteert de stroom in een fout. Raadpleeg Azure Active [Directory B2B-samenwerking](troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption)voor meer informatie.

10. De gebruiker wordt gevraagd om een persoonlijk Microsoft-account (MSA) aan te maken.

11. Nadat de gebruiker is geauthenticerd naar de juiste identiteitsprovider, wordt deze doorgestuurd naar Azure AD om de [toestemmingservaring](redemption-experience.md#consent-experience-for-the-guest)te voltooien.  

Voor just-in-time (JIT) aflossingen, waarbij inwisseling via een gekoppeldaan tenanttoepassing is, zijn de stappen 8 tot en met 10 niet beschikbaar. Als een gebruiker stap 6 bereikt en de functie Eenmalige toegangscode e-mail niet is ingeschakeld, ontvangt de gebruiker een foutbericht en kan hij de uitnodiging niet inwisselen. Om dit te voorkomen, moeten beheerders [e-mail eenmalige toegangscode inschakelen](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) of ervoor zorgen dat de gebruiker op een uitnodigingslink klikt.

## <a name="consent-experience-for-the-guest"></a>Toestemmingservaring voor de gast

Wanneer een gast zich voor het eerst aanmeldt voor toegang tot bronnen in een partnerorganisatie, wordt deze door de volgende pagina's geleid. 

1. De gast bekijkt de pagina **Machtigingen van de beoordeling** waarin de privacyverklaring van de uitnodigende organisatie wordt beschreven. Een gebruiker moet het gebruik van zijn gegevens **accepteren** in overeenstemming met het privacybeleid van de uitnodigende organisatie om door te gaan.

   ![Schermafbeelding van de pagina Machtigingen controleren](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Zie [How-to: De privacygegevens van uw organisatie toevoegen in Azure Active Directory](https://aka.ms/adprivacystatement)voor informatie over hoe u als tenantbeheerder koppelen aan de privacyverklaring van uw organisatie.

2. Als de gebruiksvoorwaarden zijn geconfigureerd, opent en beoordeelt de gast de gebruiksvoorwaarden en selecteert hij **Accepteren**. 

   ![Schermafbeelding van nieuwe gebruiksvoorwaarden](media/redemption-experience/terms-of-use-accept.png) 

   U [gebruiksvoorwaarden](../governance/active-directory-tou.md) configureren in > **Gebruiksvoorwaarden** > **Terms of use** **beheren.**

3. Tenzij anders aangegeven, wordt de gast doorgestuurd naar het toegangspaneel Apps, waarin de toepassingen worden weergegeven die de gast kan openen.

   ![Schermafbeelding van het deelvenster Apps-toegang](media/redemption-experience/myapps.png) 

In uw directory wordt de **geaccepteerde** waarde van de gast geaccepteerd in **Ja**. Als er een MSA is gemaakt, wordt in de **bron** van de gast **Microsoft-account weergegeven.** Zie Eigenschappen van een Azure [AD B2B-samenwerkingsgebruiker](user-properties.md)voor meer informatie over de eigenschappen van gastgebruikersaccount. 

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen aan de Azure-portal](add-users-administrator.md)
- [Hoe voegen informatiewerkers B2B-samenwerkingsgebruikers toe aan Azure Active Directory?](add-users-information-worker.md)
- [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen met PowerShell](customize-invitation-api.md#powershell)
- [Een organisatie verlaten als gastgebruiker](leave-the-organization.md)
