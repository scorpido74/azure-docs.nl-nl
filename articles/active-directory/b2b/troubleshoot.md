---
title: Problemen met B2B-samenwerking oplossen - Azure Active Directory | Microsoft Documenten
description: Remedies voor veelvoorkomende problemen met Azure Active Directory B2B-samenwerking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 03/19/2020
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f8bafb04d0a5d9c6d25a7ed7e155888d492e9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050789"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B-samenwerking oplossen

Hier volgen enkele remedies voor veelvoorkomende problemen met Azure Active Directory (Azure AD) B2B-samenwerking.

   > [!IMPORTANT]
   > **Vanaf 31 maart 2021**ondersteunt Microsoft niet langer het inwisselen van uitnodigingen door onbeheerde Azure AD-accounts en tenants te maken voor B2B-samenwerkingsscenario's. Ter voorbereiding moedigen we klanten aan om te kiezen voor [eenmalige wachtwoordverificatie voor e-mail.](one-time-passcode.md) We zijn blij met uw feedback over deze openbare preview-functie en zijn verheugd om nog meer manieren te creëren om samen te werken.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Ik heb een externe gebruiker toegevoegd, maar zie deze niet in mijn globale adresboek of in de lijst met mensen

In gevallen waarin externe gebruikers niet in de lijst worden ingevuld, kan het enkele minuten duren voordat het object is gerepliceerd.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Een B2B-gastgebruiker wordt niet weergegeven in SharePoint Online/OneDrive-personenkiezer

De mogelijkheid om te zoeken naar bestaande gastgebruikers in de SharePoint Online (SPO)-personenkiezer is STANDAARD UITGESCHAKELD om het gedrag van oudere gegevens te matchen.

U deze functie inschakelen met de instelling 'ShowPeoplePickerSuggestionsForGuestUsers' op tenant- en siteverzamelingsniveau. U de functie instellen met de cmdlets Set-SPOTenant en Set-SPOSite, waarmee leden alle bestaande gastgebruikers in de map kunnen doorzoeken. Wijzigingen in het tenantbereik hebben geen invloed op reeds ingerichte SPO-sites.

## <a name="invitations-have-been-disabled-for-directory"></a>Uitnodigingen zijn uitgeschakeld voor directory

Als u een melding krijgt dat u geen machtigingen hebt om gebruikers uit te nodigen, controleert u of uw gebruikersaccount is geautoriseerd om externe gebruikers uit te nodigen onder Azure Active Directory > gebruikersinstellingen > Externe gebruikers > Instellingen voor externe samenwerking beheren:

![Schermafbeelding van de instellingen voor externe gebruikers](media/troubleshoot/external-user-settings.png)

Als u deze instellingen onlangs hebt gewijzigd of de rol Gastuitnodigingsuitnodiging aan een gebruiker hebt toegewezen, kan er een vertraging van 15-60 minuten optreden voordat de wijzigingen van kracht worden.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>De gebruiker die ik heb uitgenodigd, ontvangt een foutmelding tijdens het inwisselen

Veelvoorkomende fouten zijn:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>De beheerder van de genodigde heeft e-mailgeverifieerde gebruikers niet kunnen maken in hun tenant

Wanneer u gebruikers uitnodigt waarvan de organisatie Azure Active Directory gebruikt, maar waar het account van de specifieke gebruiker niet bestaat (de gebruiker bestaat bijvoorbeeld niet in Azure AD-contoso.com). De beheerder van contoso.com kan een beleid hebben dat voorkomt dat gebruikers worden gemaakt. De gebruiker moet contact opnemen met de beheerder om te bepalen of externe gebruikers zijn toegestaan. De beheerder van de externe gebruiker moet mogelijk e-mail geverifieerde gebruikers toestaan in hun domein (zie dit [artikel](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) over het toestaan van e-mail geverifieerde gebruikers).

![Fout met vermelding van de tenant staat e-mail geverifieerde gebruikers niet toe](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Externe gebruiker bestaat nog niet in een federatief domein

Als u federatieverificatie gebruikt en de gebruiker nog niet bestaat in Azure Active Directory, kan de gebruiker niet worden uitgenodigd.

Om dit probleem op te lossen, moet de beheerder van de externe gebruiker het account van de gebruiker synchroniseren met Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hoe synchroniseert ',\#wat normaal gesproken geen geldig teken is, met Azure AD?

"\#is een gereserveerd teken in UPN's voor Azure AD B2B-samenwerking of externe gebruikers, omdat het uitgenodigde account user@contoso.com wordt user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Daarom \# mogen UPN's die afkomstig zijn van on-premises niet inloggen bij de Azure-portal. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Ik ontvang een foutmelding bij het toevoegen van externe gebruikers aan een gesynchroniseerde groep

Externe gebruikers kunnen alleen worden toegevoegd aan 'toegewezen' of 'Beveiligingsgroepen' en niet aan groepen die on-premises worden gemasterd.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mijn externe gebruiker heeft geen e-mail ontvangen om in te wisselen

De genodigde moet contact opnemen met hun ISP of spam filter om ervoor te zorgen dat het volgende adres is toegestaan:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Ik merk dat het aangepaste bericht soms niet wordt opgenomen in uitnodigingsberichten

Om te voldoen aan de privacywetgeving, bevatten onze API's geen aangepaste berichten in de e-mailuitnodiging wanneer:

- De genodigde heeft geen e-mailadres in de uitnodigende tenant
- Wanneer een medewerker van de appservice de uitnodiging verzendt

Als dit scenario belangrijk voor u is, u onze e-mail met API-uitnodigingen onderdrukken en verzenden via het e-mailmechanisme van uw keuze. Raadpleeg de juridisch adviseur van uw organisatie om ervoor te zorgen dat elke e-mail die u op deze manier verzendt, ook voldoet aan de privacywetgeving.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>U ontvangt een foutmelding 'AADSTS65005' wanneer u zich probeert aan te melden bij een Azure-bron

Een gebruiker met een gastaccount kan zich niet aanmelden en ontvangt het volgende foutbericht:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

De gebruiker heeft een Azure-gebruikersaccount en is een virale tenant die is verlaten of onbeheerd. Bovendien zijn er geen globale of bedrijfsbeheerders in de tenant.

Om dit probleem op te lossen, moet u de verlaten tenant overnemen. Raadpleeg [Een niet-beheerde map overnemen als beheerder in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). U moet ook toegang krijgen tot het dns-internet voor het domeinachtervoegsel in kwestie om direct bewijs te leveren dat u de naamruimte beheert. Nadat de tenant is teruggebracht naar een beheerde status, bespreek dan met de klant of het verlaten van de gebruikers en geverifieerde domeinnaam de beste optie is voor hun organisatie.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Een gastgebruiker met een just-in-time of "virale" tenant kan zijn wachtwoord niet opnieuw instellen

Als de identiteitstenant een just-in-time (JIT) of virale tenant is (wat betekent dat het een aparte, onbeheerde Azure-tenant is), kan alleen de gastgebruiker zijn wachtwoord opnieuw instellen. Soms neemt een organisatie [het beheer over van virale tenants](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) die worden gemaakt wanneer werknemers hun e-mailadressen op het werk gebruiken om zich aan te melden voor services. Nadat de organisatie een virale tenant heeft overneemt, kan alleen een beheerder in die organisatie het wachtwoord van de gebruiker opnieuw instellen of SSPR inschakelen. Indien nodig u als uitnodigende organisatie het gastgebruikersaccount uit uw directory verwijderen en een uitnodiging opnieuw verzenden.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>Een gastgebruiker kan de AzureAD PowerShell V1-module niet gebruiken

Vanaf 18 november 2019 kunnen gastgebruikers in uw directory (gedefinieerd als gebruikersaccounts waarvan de eigenschap **userType** gelijk is aan **Gast)** de AzureAD PowerShell V1-module niet meer gebruiken. In de toekomst moet een gebruiker lid zijn (waarbij **userType** gelijk is aan **lid)** of de AzureAD PowerShell V2-module gebruiken.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>In een Azure Us Government-tenant kan ik geen gastgebruiker voor B2B-samenwerking uitnodigen

Binnen de Azure US Government-cloud wordt B2B-samenwerking momenteel alleen ondersteund tussen tenants die zich zowel in de Azure US Government-cloud bevinden als die beide B2B-samenwerking ondersteunen. Als u een gebruiker uitnodigt in een tenant die geen deel uitmaakt van de Azure US Government-cloud of die b2b-samenwerking nog niet ondersteunt, krijgt u een foutmelding. Zie Azure Active [Directory Premium P1- en P2-variaties](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)voor meer informatie en beperkingen.

## <a name="next-steps"></a>Volgende stappen

[Krijg ondersteuning voor B2B-samenwerking](get-support.md)
