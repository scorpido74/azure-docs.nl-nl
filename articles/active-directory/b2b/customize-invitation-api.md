---
title: B2B Collaboration API en Customization-Azure Active Directory
description: Azure Active Directory B2B-samenwerking ondersteunt uw externe bedrijfsrelaties door zakelijke partners selectief toegang te verlenen tot uw zakelijke toepassingen
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9f62ece12732c45037bcad328eec162bebb4e6a
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85387198"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B-samenwerkings-API en-aanpassing

We hebben veel klanten verteld dat ze het uitnodigings proces willen aanpassen op een manier die geschikt is voor hun organisatie. Met onze API kunt u alleen dat doen. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Mogelijkheden van de API voor uitnodigingen

De API biedt de volgende mogelijkheden:

1. Een externe gebruiker uitnodigen met *een* e-mail adres.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Pas de locatie aan waar uw gebruikers zich bevinden nadat ze hun uitnodiging hebben geaccepteerd.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Kies voor het verzenden van het e-mail bericht met de standaard uitnodiging via ons

    ```
    "sendInvitationMessage": true
    ```

   met een bericht aan de ontvanger die u kunt aanpassen

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. En kies voor CC: personen die u in de loop wilt laten blijven over uw uitnodiging voor deze samen werker.

5. U kunt ook uw uitnodiging en de werk stroom voor onboarding volledig aanpassen door geen meldingen via Azure AD te verzenden.

    ```
    "sendInvitationMessage": false
    ```

   In dit geval krijgt u een back-upurl van de API die u kunt insluiten in een e-mail sjabloon, een chat bericht of een andere distributie methode van uw keuze.

6. Ten slotte, als u een beheerder bent, kunt u ervoor kiezen om de gebruiker als lid uit te nodigen.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Autorisatie model

De API kan worden uitgevoerd in de volgende autorisatie modi:

### <a name="app--user-mode"></a>App + gebruikers modus

In deze modus moet degene die de API gebruikt, de machtigingen hebben om B2B-uitnodigingen te maken.

### <a name="app-only-mode"></a>Modus alleen app

In alleen app-context moet de app de gebruiker. uitnodigen. het bereik van de uitnodiging is voltooid.

Raadpleeg voor meer informatie:https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

U kunt Power shell gebruiken om op eenvoudige wijze externe gebruikers toe te voegen en aan een organisatie uit te nodigen. Maak een uitnodiging met de cmdlet:

```powershell
New-AzureADMSInvitation
```

U kunt de volgende opties gebruiken:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Status van uitnodiging

Nadat u een uitnodiging voor een externe gebruiker hebt verzonden, kunt u de cmdlet **Get-AzureADUser** gebruiken om te zien of deze deze heeft geaccepteerd. De volgende eigenschappen van Get-AzureADUser worden ingevuld wanneer een externe gebruiker een uitnodiging verzendt:

* **UserState** geeft aan of de uitnodiging wordt **PendingAcceptance** of **geaccepteerd**.
* **UserStateChangedOn** toont de tijds tempel voor de laatste wijziging van de eigenschap **UserState** .

U kunt de **filter** optie gebruiken om de resultaten te filteren op **UserState**. In het onderstaande voor beeld ziet u hoe u resultaten filtert om alleen gebruikers weer te geven met een uitnodiging in behandeling. In het voor beeld wordt ook de optie voor de **indelings lijst** weer gegeven, waarmee u de eigenschappen kunt opgeven die moeten worden weer gegeven. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Zorg ervoor dat u beschikt over de nieuwste versie van de Power shell-module AzureAD of de Power shell-module AzureADPreview. 

## <a name="see-also"></a>Zie ook

Bekijk de API-verwijzing voor de uitnodiging in [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) .

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [De elementen van het e-mail adres uitnodiging voor B2B-samen werking](invitation-email-elements.md)
- [Inwisseling uitnodiging voor B2B-samen werking](redemption-experience.md)
- [Gebruikers van B2B-samen werking zonder uitnodiging toevoegen](add-user-without-invite.md)