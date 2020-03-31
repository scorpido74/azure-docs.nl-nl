---
title: API en aanpassing van B2B-samenwerking - Azure Active Directory
description: Azure Active Directory B2B-samenwerking ondersteunt uw externe bedrijfsrelaties door zakelijke partners selectief toegang te verlenen tot uw zakelijke toepassingen
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a090ee3f9588ff6bff01e12db469bf04407a7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263463"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>API en aanpassing van Azure Active Directory B2B-samenwerking

We hebben veel klanten ons laten vertellen dat ze het uitnodigingsproces willen aanpassen op een manier die het beste werkt voor hun organisaties. Met onze API u precies dat doen. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Mogelijkheden van de uitnodigings-API

De API biedt de volgende mogelijkheden:

1. Nodig een externe gebruiker uit met *een* e-mailadres.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Pas aan waar u wilt dat uw gebruikers landen nadat ze hun uitnodiging hebben geaccepteerd.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Kies ervoor om de standaard uitnodigingsmail via ons te verzenden

    ```
    "sendInvitationMessage": true
    ```

   met een bericht aan de ontvanger dat u aanpassen

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. En kies voor cc: mensen die je op de hoogte wilt houden van je uitnodigende medewerker.

5. Of pas uw uitnodigings- en onboarding-workflow volledig aan door ervoor te kiezen geen meldingen via Azure AD te verzenden.

    ```
    "sendInvitationMessage": false
    ```

   In dit geval krijgt u een inwissel-URL terug uit de API die u insluiten in een e-mailsjabloon, chatbericht of andere distributiemethode naar keuze.

6. Tot slot, als je een beheerder bent, kun je ervoor kiezen om de gebruiker uit te nodigen als lid.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Autorisatiemodel

De API kan worden uitgevoerd in de volgende autorisatiemodi:

### <a name="app--user-mode"></a>App + Gebruikersmodus

In deze modus moet degene die de API gebruikt, de machtigingen hebben om B2B-uitnodigingen te maken.

### <a name="app-only-mode"></a>App only-modus

In de context van app only heeft de app de User.Invite.All-ruimte nodig om de uitnodiging te laten slagen.

Zie voor meer informatie:https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

U PowerShell eenvoudig gebruiken om externe gebruikers toe te voegen en uit te nodigen voor een organisatie. Maak een uitnodiging met de cmdlet:

```powershell
New-AzureADMSInvitation
```

U kunt de volgende opties gebruiken:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -Uitnodigingsbericht verzenden
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Uitnodigingsstatus

Nadat u een externe gebruiker een uitnodiging hebt verzonden, u de cmdlet **Get-AzureADUser** gebruiken om te zien of deze persoon deze heeft geaccepteerd. De volgende eigenschappen van Get-AzureADUser worden ingevuld wanneer een externe gebruiker een uitnodiging ontvangt:

* **UserState** geeft aan of de uitnodiging **in behandeling isGeaccepteerd** of **geaccepteerd**.
* **UserStateChangedOn** toont de tijdstempel voor de laatste wijziging in de eigenschap **UserState.**

U de optie **Filter** gebruiken om de resultaten te filteren op **UserState.** In het onderstaande voorbeeld ziet u hoe u resultaten filtert om alleen gebruikers weer te geven die een uitnodiging in behandeling hebben. In het voorbeeld wordt ook de optie **Opmaaklijst** weergegeven, waarmee u de eigenschappen opgeven die moeten worden weergegeven. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Zorg ervoor dat u de nieuwste versie van de AzureAD PowerShell-module of AzureADPreview PowerShell-module hebt. 

## <a name="see-also"></a>Zie ook

Bekijk de referentie van [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)de uitnodigings-API in .

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [De elementen van de b2b samenwerkingsuitnodiginge-mail](invitation-email-elements.md)
- [B2B-uitnodigingsuitnodiging en inwisseling](redemption-experience.md)
- [B2B-samenwerkingsgebruikers toevoegen zonder uitnodiging](add-user-without-invite.md)