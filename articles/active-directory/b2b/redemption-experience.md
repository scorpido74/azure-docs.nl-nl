---
title: Inwisseling van uitnodiging in B2B-samen werking-Azure AD
description: Hierin wordt de uitnodiging voor Azure AD B2B-samen werking voor eind gebruikers beschreven, met inbegrip van de overeenkomst met betrekking tot de privacy-voor waarden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e85b0ae298589c0e0e051a24e5db89eae81db62
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272139"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Inwisseling uitnodiging Azure Active Directory B2B-samen werking

In dit artikel worden de manieren beschreven waarop gast gebruikers toegang hebben tot uw resources en het toestemmings proces dat ze tegen komen. Als u een uitnodigings-e-mail naar de gast stuurt, bevat de uitnodiging een koppeling die de gast kan inwisselen om toegang te krijgen tot uw app of portal. Het e-mail bericht is slechts een van de manieren waarop gasten toegang kunnen krijgen tot uw resources. Als alternatief kunt u gasten toevoegen aan uw directory en ze een directe koppeling geven naar de portal of app die u wilt delen. Gasten worden door een proces voor de eerste toestemming begeleid, ongeacht de methode die ze gebruiken. Dit proces zorgt ervoor dat uw gasten akkoord gaan met de privacy-voor waarden en alle [gebruiks voorwaarden](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) accepteren die u hebt ingesteld.

Wanneer u een gast gebruiker aan uw Directory toevoegt, heeft het gast gebruikers account een toestemmings status (zichtbaar in Power shell) die in eerste instantie is ingesteld op **PendingAcceptance**. Deze instelling blijft actief totdat de gast uw uitnodiging aanvaardt en akkoord gaat met uw privacybeleid en gebruiks voorwaarden. Daarna wordt de status van de toestemming gewijzigd in **geaccepteerd**en worden de pagina's met toestemming niet meer aan de gast gepresenteerd.

## <a name="redemption-through-the-invitation-email"></a>Inwisselen via e-mail met uitnodiging

Wanneer u een gast gebruiker aan uw Directory toevoegt met [behulp van de Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), wordt er een e-mail uitnodiging verzonden naar de gast in het proces. U kunt er ook voor kiezen om e-mail berichten te verzenden wanneer u [Power shell gebruikt](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) om gast gebruikers toe te voegen aan uw Directory. Hier volgt een beschrijving van de ervaring van de gast bij het inwisselen van de koppeling in het e-mail bericht.

1. De gast ontvangt een [uitnodigings-e-mail](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) die wordt verzonden vanuit **uitnodigingen van micro soft**.
2. De gast selecteert **aan de slag** in het e-mail bericht.
3. Als de gast geen Azure AD-account, een micro soft-account (MSA) of een e-mail account in een federatieve organisatie heeft, wordt u gevraagd een MSA te maken (tenzij de functie voor [eenmalige wachtwoord code](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) is ingeschakeld, waarvoor geen MSA is vereist).
4. De gast wordt geleid door de [toestemming](#consent-experience-for-the-guest) die hieronder wordt beschreven.

## <a name="redemption-through-a-direct-link"></a>Inkopen via een directe koppeling

Als alternatief voor de uitnodigings-e-mail kunt u een gast een rechtstreekse koppeling geven naar uw app of portal. U moet eerst de gast gebruiker toevoegen aan uw directory via de [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) of [Power shell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Vervolgens kunt u een van de [aanpas bare manieren gebruiken om toepassingen te implementeren voor gebruikers](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), waaronder directe aanmeldings koppelingen. Wanneer een gast gebruikmaakt van een directe koppeling in plaats van het e-mail bericht met de uitnodiging, worden ze nog steeds door gegeven via de eerste toestemmings ervaring.

> [!IMPORTANT]
> De directe koppeling moet Tenant-specifiek zijn. Met andere woorden, het moet een Tenant-ID of geverifieerd domein bevatten zodat de gast kan worden geverifieerd in uw Tenant, waar de gedeelde app zich bevindt. Een gemeen schappelijke URL, zoals https://myapps.microsoft.com, werkt niet voor een gast omdat deze wordt omgeleid naar hun eigen Tenant voor authenticatie. Hier volgen enkele voor beelden van directe koppelingen met de context van de Tenant:
 > - Toegangs venster voor apps: https://myapps.microsoft.com/?tenantid=&lt; Tenant-id&gt; 
 > - Toegangs paneel voor apps voor een geverifieerd domein: https://myapps.microsoft.com/&lt; geverifieerde domein&gt;
 > - Azure Portal: https://portal.azure.com/&lt; Tenant-id&gt;
 > - Afzonderlijke app: zie een [koppeling voor direct aanmelden](../manage-apps/end-user-experiences.md#direct-sign-on-links) gebruiken

Er zijn enkele gevallen waarin de e-mail uitnodiging wordt aanbevolen via een directe koppeling. Als deze speciale gevallen belang rijk zijn voor uw organisatie, raden we u aan gebruikers uit te nodigen met behulp van methoden die nog steeds de uitnodigings-e-mail verzenden:
 - De gebruiker heeft geen Azure AD-account, een MSA of een e-mail account in een federatieve organisatie. Tenzij u de functie voor eenmalige wachtwoord code gebruikt, moet de gast de uitnodigings-e-mail inwisselen om door de stappen voor het maken van een MSA te worden geleid.
 - Soms heeft het uitgenodigde gebruikers object mogelijk geen e-mail adres vanwege een conflict met een contact object (bijvoorbeeld een Outlook-contact object). In dit geval moet de gebruiker op de opname-URL in het e-mail bericht met de uitnodiging klikken.
 - De gebruiker kan zich aanmelden met een alias van het e-mail adres dat is uitgenodigd. (Een alias is een aanvullend e-mail adres dat is gekoppeld aan een e-mail account.) In dit geval moet de gebruiker op de opname-URL in het e-mail bericht met de uitnodiging klikken.

## <a name="consent-experience-for-the-guest"></a>Bestemmings ervaring voor de gast

Wanneer een gast zich voor de eerste keer aanmeldt voor toegang tot resources in een partner organisatie, worden deze door de volgende pagina's geleid. 

1. De gast controleert de pagina **machtigingen controleren** met een beschrijving van de privacyverklaring van de uitnodigende organisatie. Een gebruiker moet het gebruik van hun gegevens in overeenstemming met het privacybeleid van de uitnodigende organisatie **accepteren** om door te gaan.

   ![Scherm opname met de pagina Machtigingen controleren](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Voor informatie over hoe u als een Tenant beheerder kunt koppelen aan de privacyverklaring van uw organisatie, raadpleegt u [How to: privacy-informatie van uw organisatie toevoegen in azure Active Directory](https://aka.ms/adprivacystatement).

2. Als de gebruiks voorwaarden zijn geconfigureerd, wordt de gast geopend en worden de gebruiks voorwaarden beoordeeld. vervolgens selecteert u **accepteren**. 

   ![Scherm opname met nieuwe gebruiks voorwaarden](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > U kunt de [gebruiks voorwaarden configureren voor](../governance/active-directory-tou.md) het **beheren** van > **organisatie relaties** > **Gebruiksvoorwaarden**.

3. Tenzij anders vermeld, wordt de gast omgeleid naar het toegangs venster voor apps, met daarin de toepassingen die de gast kan openen.

   ![Scherm opname van het toegangs paneel voor apps](media/redemption-experience/myapps.png) 

In uw Directory wordt de waarde van de **uitnodiging geaccepteerd** van de gast gewijzigd in **Ja**. Als er een MSA is gemaakt, wordt in de **bron** van de gast het **micro soft-account**weer gegeven. Zie [Eigenschappen van een Azure AD B2B-samenwerkings gebruiker](user-properties.md)voor meer informatie over eigenschappen van gast gebruikers accounts. 

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Azure Active Directory B2B-samenwerkings gebruikers toevoegen aan de Azure Portal](add-users-administrator.md)
- [Hoe voegen mede werkers B2B-samenwerkings gebruikers toe aan Azure Active Directory?](add-users-information-worker.md)
- [Azure Active Directory B2B-samenwerkings gebruikers toevoegen met behulp van Power shell](customize-invitation-api.md#powershell)
- [Een organisatie als gast gebruiker laten staan](leave-the-organization.md)
