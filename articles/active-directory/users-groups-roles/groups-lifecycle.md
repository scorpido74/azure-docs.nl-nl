---
title: Vervaldatum instellen voor Office 365-groepen - Azure Active Directory | Microsoft Documenten
description: Verlopen instellen voor Office 365-groepen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b551bc8a46a45135bf6a9a8e328b4b0e74f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048251"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Het verloopbeleid voor Office 365-groepen configureren

In dit artikel wordt uitgelegd hoe u de levenscyclus van Office 365-groepen beheren door een verloopbeleid voor deze groepen in te stellen. U alleen het verloopbeleid instellen voor Office 365-groepen in Azure Active Directory (Azure AD).

Zodra u hebt ingesteld dat een groep verloopt:

- Groepen met gebruikersactiviteiten worden automatisch verlengd naarmate de vervaldatum nadert.
- Eigenaren van de groep worden op de hoogte gesteld om de groep te vernieuwen, als de groep niet automatisch wordt verlengd.
- Elke groep die niet wordt verlengd, wordt verwijderd.
- Elke Office 365-groep die wordt verwijderd, kan binnen 30 dagen worden hersteld door de groepseigenaren of de beheerder.

Momenteel kan slechts één verloopbeleid worden geconfigureerd voor alle Office 365-groepen in een Azure AD-organisatie.

> [!NOTE]
> Voor het configureren en gebruiken van het verloopbeleid voor Office 365-groepen moet u Azure AD Premium-licenties bezitten, maar niet noodzakelijkerwijs toewijzen voor de leden van alle groepen waarop het verloopbeleid wordt toegepast.

Zie [Azure Active Directory PowerShell voor Grafiek 2.0.0.137 voor](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)informatie over het downloaden en installeren van de Azure AD PowerShell-cmdlets.

## <a name="activity-based-automatic-renewal"></a>Automatische verlenging op basis van activiteit

Met Azure AD-intelligentie worden groepen nu automatisch vernieuwd op basis van de vraag of ze onlangs zijn gebruikt. Deze functie elimineert de noodzaak van handmatige actie door groepseigenaren, omdat deze is gebaseerd op gebruikersactiviteit in groepen in Office 365-services zoals Outlook, SharePoint of Teams. Als een eigenaar of een groepslid bijvoorbeeld iets doet als een document uploaden in SharePoint, een Team-kanaal bezoeken of een e-mail naar de groep in Outlook sturen, wordt de groep automatisch verlengd en ontvangt de eigenaar geen verlengingsmeldingen.

### <a name="activities-that-automatically-renew-group-expiration"></a>Activiteiten die de vervaldatum van de groep automatisch verlengen

De volgende gebruikersacties veroorzaken automatische groepsvernieuwing:

- SharePoint: bestanden bekijken, bewerken, downloaden, verplaatsen, delen of uploaden
- Outlook: Groep deelnemen, groepsbericht lezen/schrijven vanuit groepsruimte, Vind een bericht leuk (in Outlook Web Access)
- Teams: Bezoek een teamkanaal

### <a name="auditing-and-reporting"></a>Controle en rapportage

Beheerders kunnen een lijst met automatisch vernieuwde groepen ophalen uit de activiteitencontrolelogboeken in Azure AD.

![Automatische verlenging van groepen op basis van activiteit](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Rollen en machtigingen

Hieronder volgen rollen die vervaldatums voor Office 365-groepen in Azure AD kunnen configureren en gebruiken.

Rol | Machtigingen
-------- | --------
Globale beheerder, groepsbeheerder of gebruikersbeheerder | Kan de beleidsinstellingen voor het verlopen van Office 365-groepen maken, lezen, bijwerken of verwijderen<br>Kan elke Office 365-groep vernieuwen
Gebruiker | Kan een Office 365-groep die ze bezitten vernieuwen<br>Kan een Office 365-groep herstellen waarvan ze eigenaar zijn<br>Kan de instellingen voor verloopbeleid lezen

Zie [Een verwijderde Office 365-groep herstellen in Azure Active Directory](groups-restore-deleted.md)voor meer informatie over machtigingen voor het herstellen van een verwijderde groep.

## <a name="set-group-expiration"></a>Verloopdatum van de groep instellen

1. Open het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een account dat een globale beheerder is in uw Azure AD-organisatie.

2. Selecteer **Groepen**en selecteer **Vervolgens Verloop** om de vervaldatum-instellingen te openen.
  
   ![Verloopinstellingen voor groepen](./media/groups-lifecycle/expiration-settings.png)

3. Op de **pagina Vervaldatum** u het als nog niet het betreffende jaar instellen:

    - Stel de groepslevensduur in dagen in. U een van de vooraf ingestelde waarden of een aangepaste waarde selecteren (moet 31 dagen of langer zijn).
    - Geef een e-mailadres op waar de verlengings- en vervaldatummeldingen moeten worden verzonden wanneer een groep geen eigenaar heeft.
    - Selecteer welke Office 365-groepen verlopen. U de vervaldatum instellen voor:
      - **Alles** Office 365-groepen
      - Een lijst **met geselecteerde** Office 365-groepen
      - **Geen** om de vervaldatum voor alle groepen te beperken
    - Sla uw instellingen op wanneer u klaar bent door **Opslaan te selecteren.**

> [!NOTE]
> - Wanneer u de vervaldatum voor het eerst instelt, worden groepen die ouder zijn dan het verloopinterval ingesteld op 35 dagen tot het verstrijken ervan, tenzij de groep automatisch wordt verlengd of de eigenaar deze verlengt.
> - Wanneer een dynamische groep wordt verwijderd en hersteld, wordt deze gezien als een nieuwe groep en opnieuw ingevuld volgens de regel. Dit proces kan tot 24 uur duren.
> - Verloopmeldingen voor groepen die in Teams worden gebruikt, worden weergegeven in de feed Teams-eigenaren.

## <a name="email-notifications"></a>E-mailmeldingen

Als groepen niet automatisch worden verlengd, worden e-mailmeldingen zoals deze 30 dagen, 15 dagen en 1 dag voor het verstrijken van de groep naar de eigenaren van de Office 365-groep verzonden. De taal van de e-mail wordt bepaald door de voorkeurstaal van de eigenaar van groepen of de instelling voor Azure AD-taal. Als de eigenaar van de groep een voorkeurstaal heeft gedefinieerd of meerdere eigenaren dezelfde voorkeurstaal hebben, wordt die taal gebruikt. Voor alle andere gevallen wordt de taalinstelling azure AD gebruikt.

![E-mailmeldingen voor verlopen](./media/groups-lifecycle/expiration-notification.png)

Via **de** e-mail met groepsmelding vernieuwen hebben groepseigenaren rechtstreeks toegang tot de pagina groepsgegevens in het [toegangspaneel](https://account.activedirectory.windowsazure.com/r#/applications). Daar kunnen de gebruikers meer informatie krijgen over de groep, zoals de beschrijving, wanneer deze voor het laatst is vernieuwd, wanneer deze verloopt en ook de mogelijkheid om de groep te vernieuwen. De pagina groepsdetails bevat nu ook koppelingen naar de bronnen van de Office 365-groep, zodat de groepseigenaar de inhoud en activiteit in zijn groep gemakkelijk kan bekijken.

Wanneer een groep verloopt, wordt de groep een dag na de vervaldatum verwijderd. Een e-mailmelding zoals deze wordt verzonden naar de eigenaren van de Office 365-groep die hen informeren over het verlopen en vervolgens verwijderen van hun Office 365-groep.

![E-mailmeldingen voor groepsverwijdering](./media/groups-lifecycle/deletion-notification.png)

De groep kan binnen 30 dagen na verwijdering worden hersteld door **de groep Herstellen** te selecteren of door PowerShell-cmdlets te gebruiken, zoals beschreven in Een verwijderde Office [365-groep herstellen in Azure Active Directory.](groups-restore-deleted.md) Houd er rekening mee dat de restauratieperiode van 30 dagen niet aanpasbaar is.

Als de groep die u herstelt documenten, SharePoint-sites of andere permanente objecten bevat, kan het tot 24 uur duren voordat de groep en de inhoud ervan volledig zijn hersteld.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Vervaldatum van office 365-groep ophalen

Naast het Access Panel waar gebruikers groepsgegevens kunnen bekijken, waaronder vervaldatum en laatste vernieuwde datum, kan de vervaldatum van een Office 365-groep worden opgehaald uit de Microsoft Graph REST API Beta. expiratieDatumtijd als groepseigenschap is ingeschakeld in de bètaversie van Microsoft Graph. Het kan worden opgehaald met een GET-aanvraag. Zie voor meer informatie [dit voorbeeld.](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example)

> [!NOTE]
> Als u groepslidmaatschappen wilt beheren in het Access Panel, moet 'Toegang tot groepen in het Access-paneel beperken' worden ingesteld op 'Nee' in de algemene instelling voor Azure Active Directory-groepen.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Hoe de vervaldatum van office 365-groepen werkt met een postvak in de wachtstand

Wanneer een groep verloopt en wordt verwijderd, worden de gegevens van de groep 30 dagen na het verwijderen van de gegevens van de groep uit apps zoals Planner, Sites of Teams permanent verwijderd, maar het groepspostvak dat in de wachtstaat staat, wordt bewaard en wordt niet permanent verwijderd. De beheerder kan Exchange-cmdlets gebruiken om het postvak te herstellen om de gegevens op te halen.

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Hoe de vervaldatum van office 365-groepen werkt met het bewaarbeleid

Het bewaarbeleid wordt geconfigureerd via het Security and Compliance Center. Als u een bewaarbeleid hebt ingesteld voor Office 365-groepen, worden de groepsgesprekken in het groepspostvak en de bestanden in de groepssite bewaard in de bewaarcontainer voor het specifieke aantal dagen dat is gedefinieerd in de retentieperiode Beleid. Gebruikers zien de groep of de inhoud ervan niet na afloop, maar kunnen de site- en postvakgegevens via e-discovery herstellen.

## <a name="powershell-examples"></a>PowerShell-voorbeelden

Hier volgen voorbeelden van hoe u PowerShell-cmdlets gebruiken om de vervaldatumsvoor Office 365-groepen in uw Azure AD-organisatie te configureren:

1. Installeer de PowerShell v2.0-module en meld u aan bij de PowerShell-prompt:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Configureer de verloopinstellingen Gebruik de cmdlet Nieuw-AzureADMSGroupLifecyclePolicy om de levensduur voor alle Office 365-groepen in de Azure AD-organisatie in te stellen op 365 dagen. Vernieuwingsmeldingen voor Office 365-groepen zonderemailaddress@contoso.comeigenaars worden verzonden naar '
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Het bestaande beleid Get-AzureADMSGroupLifecyclePolicy ophalen: met deze cmdlet worden de huidige office 365-groepsvervaldatums opgehaald die zijn geconfigureerd. In dit voorbeeld ziet u:

   - De beleids-ID
   - De levensduur voor alle Office 365-groepen in de Azure AD-organisatie is ingesteld op 365 dagen
   - Vernieuwingsmeldingen voor Office 365-groepen zonderemailaddress@contoso.comeigenaars worden verzonden naar ' .'
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Update het bestaande beleid Set-AzureADMSGroupLifecyclePolicy: deze cmdlet wordt gebruikt om een bestaand beleid bij te werken. In het onderstaande voorbeeld wordt de groepslevensduur in het bestaande beleid gewijzigd van 365 dagen naar 180 dagen.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Specifieke groepen toevoegen aan het beleid Add-AzureADMSLifecyclePolicyGroup: deze cmdlet voegt een groep toe aan het levenscyclusbeleid. Bijvoorbeeld:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Verwijder het bestaande beleid Remove-AzureADMSGroupLifecyclePolicy: met deze cmdlet worden de vervaldatums van de Office 365-groep verwijderd, maar is de beleids-id vereist. Met deze cmdlet wordt de vervaldatum voor Office 365-groepen uitgeschakeld.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
De volgende cmdlets kunnen worden gebruikt om het beleid in meer detail te configureren. Zie [PowerShell-documentatie](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups)voor meer informatie.

- Get-AzureADMSGroupLifecyclePolicy
- Nieuw AzureADMSGroupLifecycle-beleid
- Get-AzureADMSGroupLifecyclePolicy
- AzureADMSGroupLifecycle-beleid instellen
- AzureADMSGroupLifecyclePolicy verwijderen
- AzureADMSLifecyclePolicyGroup toevoegen-azureADM
- AzureADMSLifecyclePolicyGroup verwijderen
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure AD-groepen.

- [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Leden van een groep beheren](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)
