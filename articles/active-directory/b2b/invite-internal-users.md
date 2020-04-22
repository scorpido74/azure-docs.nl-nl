---
title: Interne gebruikers uitnodigen voor B2B-samenwerking - Azure AD
description: Als u interne gebruikersaccounts hebt voor partners, distributeurs, leveranciers, leveranciers en andere gasten, u overstappen op Azure AD B2B-samenwerking door hen uit te nodigen om zich aan te melden met hun eigen externe referenties of aan te melden. Gebruik PowerShell of de Microsoft Graph-uitnodigings-API.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 783fc0fa6f6c4e6c918fa3ff5fe0b53a71fa0178
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680179"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Interne gebruikers uitnodigen voor B2B-samenwerking

|     |
| --- |
| Het uitnodigen van interne gebruikers om B2B-samenwerking te gebruiken is een openbare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over voorvertoningen. |
|     |

Vóór de beschikbaarheid van Azure AD B2B-samenwerking kunnen organisaties samenwerken met distributeurs, leveranciers, leveranciers en andere gastgebruikers door interne referenties voor hen in te stellen. Als u dergelijke interne gastgebruikers hebt, u hen uitnodigen om B2B-samenwerking te gebruiken, zodat u profiteren van Azure AD B2B-voordelen. Uw B2B-gastgebruikers kunnen hun eigen identiteit en referenties gebruiken om zich aan te melden en u hoeft geen wachtwoorden te onderhouden of de levenscyclus van accounts te beheren.

Als u een uitnodiging verzendt naar een bestaand intern account, u de object-id, UPN, groepslidmaatschappen en app-toewijzingen van die gebruiker behouden. U hoeft de gebruiker niet handmatig te verwijderen en opnieuw uit te nodigen of resources opnieuw toe te wijzen. Als u de gebruiker wilt uitnodigen, gebruikt u de uitnodigings-API om zowel het interne gebruikersobject als het e-mailadres van de gastgebruiker samen met de uitnodiging door te geven. Wanneer de gebruiker de uitnodiging accepteert, wijzigt de B2B-service het bestaande interne gebruikersobject in een B2B-gebruiker. In de toekomst moet de gebruiker zich aanmelden bij cloudresourcesservices met behulp van zijn B2B-referenties. Ze kunnen nog steeds hun interne referenties gebruiken om toegang te krijgen tot on-premises bronnen, maar u dit voorkomen door het wachtwoord van het interne account opnieuw in te stellen of te wijzigen.

> [!NOTE]
> Uitnodiging is eenrichtingsverkeer. U interne gebruikers uitnodigen om B2B-samenwerking te gebruiken, maar u de B2B-referenties niet verwijderen zodra ze zijn toegevoegd. Als u de gebruiker wilt wijzigen in een gebruiker die alleen in een interne gebruiker is, moet u het gebruikersobject verwijderen en een nieuw object maken.

Terwijl in openbare preview, de methode beschreven in dit artikel voor het uitnodigen van interne gebruikers voor B2B-samenwerking kan niet worden gebruikt in deze gevallen:

- De interne gebruiker heeft al een Exchange-licentie toegewezen gekregen.
- De gebruiker komt uit een domein dat is ingesteld voor directe federatie in uw directory.
- De interne gebruiker is een cloudaccount en het hoofdaccount bevindt zich niet in Azure AD.

Als de interne gebruiker in deze gevallen moet worden gewijzigd in een B2B-gebruiker, moet u het interne account verwijderen en de gebruiker een uitnodiging sturen voor B2B-samenwerking.

**On-premises gesynchroniseerde gebruikers**: Voor gebruikersaccounts die worden gesynchroniseerd tussen on-premises en de cloud, blijft de on-premises directory de bron van autoriteit nadat ze zijn uitgenodigd om B2B-samenwerking te gebruiken. Alle wijzigingen die u aanbrengt in het on-premises account, worden gesynchroniseerd met het cloudaccount, inclusief het uitschakelen of verwijderen van het account. Daarom u niet voorkomen dat de gebruiker zich aanmeldt bij zijn on-premises account terwijl hij zijn cloudaccount behoudt door simpelweg het on-premises account te verwijderen. In plaats daarvan u het on-premises accountwachtwoord instellen op een willekeurige GUID of andere onbekende waarde.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Interne gebruikers uitnodigen voor B2B-samenwerking

U PowerShell of de uitnodigings-API gebruiken om een B2B-uitnodiging naar de interne gebruiker te sturen. Controleer of het e-mailadres dat u voor de uitnodiging wilt gebruiken, is ingesteld als het externe e-mailadres van het interne gebruikersobject.

- Voor een gebruiker die alleen in de cloud is, gebruikt u het e-mailadres in de eigenschap User.OtherMails voor de uitnodiging.
- Voor een on-premises gesynchroniseerde gebruiker moet u de waarde in de eigenschap User.Mail voor de uitnodiging gebruiken.
- Het domein in de eigenschap Mail van de gebruiker moet overeenkomen met het account dat ze gebruiken om zich aan te melden. Anders kunnen sommige services, zoals Teams, de gebruiker niet verifiëren.

Standaard stuurt de uitnodiging de gebruiker een e-mail waarin hij of zij laat weten dat hij of zij is uitgenodigd, maar u deze e-mail onderdrukken en in plaats daarvan uw eigen e-mail verzenden.

> [!NOTE]
> Als u uw eigen e-mail of andere communicatie wilt verzenden, u Nieuw-AzureADMSInvitation gebruiken met -SendInvitationMessage:$false om gebruikers in stilte uit te nodigen en vervolgens uw eigen e-mailbericht naar de geconverteerde gebruiker te verzenden. Zie [Azure AD B2B-samenwerkingsAPI en -aanpassing](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>PowerShell gebruiken om een B2B-uitnodiging te verzenden

Gebruik de volgende opdracht om de gebruiker uit te nodigen voor B2B-samenwerking:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>De uitnodigings-API gebruiken om een B2B-uitnodiging te verzenden

In het onderstaande voorbeeld wordt uitgelegd hoe u de uitnodigings-API aanroepen om een interne gebruiker als B2B-gebruiker uit te nodigen.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

Het antwoord op de API is hetzelfde antwoord dat u krijgt wanneer u een nieuwe gastgebruiker uitnodigt voor de directory.

## <a name="next-steps"></a>Volgende stappen

- [B2B-uitnodigingsuitnodiging en inwisseling](redemption-experience.md)
