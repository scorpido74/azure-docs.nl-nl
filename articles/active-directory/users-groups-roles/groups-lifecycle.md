---
title: Verval datum instellen voor Office 365-groepen-Azure Active Directory | Microsoft Docs
description: Verval datum instellen voor Office 365-groepen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 10/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a9e410be7a52291d19478dbfb8e9ad30f144ccb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74914079"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Het verloop beleid voor Office 365-groepen configureren

In dit artikel leest u hoe u de levens cyclus van Office 365-groepen kunt beheren door een verloop beleid in te stellen. U kunt het verloop beleid alleen instellen voor Office 365-groepen in Azure Active Directory (Azure AD).

Zodra u een groep hebt ingesteld op verlopen:

- Groepen met gebruikers activiteiten worden automatisch vernieuwd als de verlopen bijna
- Eigen aren van de groep worden gewaarschuwd om de groep te vernieuwen als de groep niet automatisch wordt vernieuwd
- Een groep die niet wordt vernieuwd, wordt verwijderd
- Elke Office 365-groep die wordt verwijderd, kan binnen 30 dagen worden hersteld door de groeps eigenaren of de beheerder

Op dit moment kan slechts één verloop beleid worden geconfigureerd voor alle Office 365-groepen in een Azure AD-organisatie.

> [!NOTE]
> Voor het configureren en gebruiken van het verloop beleid voor Office 365-groepen moet u echter wel beschikken over Azure AD Premium licenties voor de leden van alle groepen waarop het verloop beleid wordt toegepast.

Zie [Azure Active Directory Power shell for graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)voor meer informatie over het downloaden en installeren van de Azure AD Power shell-cmdlets.

## <a name="activity-based-automatic-renewal"></a>Automatische verlenging op basis van activiteit

Met Azure AD Intelligence worden groepen nu automatisch vernieuwd op basis van het feit of ze recent zijn gebruikt. Deze functie elimineert de nood zaak voor hand matige actie door groeps eigenaren, omdat deze is gebaseerd op gebruikers activiteit in groepen tussen Office 365-services zoals Outlook, share point, teams of Yammer. Als een eigenaar of een groepslid bijvoorbeeld een document uploadt in share point, gaat u naar een teams-kanaal of stuurt u een e-mail naar de groep in Outlook, wordt de groep automatisch vernieuwd en worden er geen meldingen over verlenging ontvangen.

### <a name="activities-that-automatically-renew-group-expiration"></a>Activiteiten die het verlopen van groepen automatisch vernieuwen

De volgende gebruikers acties veroorzaken automatische groeps verlenging:

- Share point: bestanden weer geven, bewerken, downloaden, verplaatsen, delen of uploaden
- Outlook: lid worden van groep, bericht van groep lezen/schrijven van groeps ruimte, zoals een bericht (in Outlook Web Access)
- Teams: een teams-kanaal bezoeken

### <a name="auditing-and-reporting"></a>Controle en rapportage

Beheerders kunnen een lijst met automatisch hernieuwde groepen ophalen uit de audit logboeken voor activiteiten in azure AD.

## <a name="roles-and-permissions"></a>Rollen en machtigingen

Hieronder vindt u rollen die het verlopen van Office 365-groepen in azure AD kunnen configureren en gebruiken.

Rol | Machtigingen
-------- | --------
Globale beheerder, groeps beheerder of gebruikers beheerder | Kan de beleids instellingen voor verloop van Office 365-groepen maken, lezen, bijwerken of verwijderen<br>Kan elke Office 365-groep vernieuwen
Gebruiker | Kan een Office 365-groep waarvan ze eigenaar zijn, vernieuwen<br>Kan een Office 365-groep herstellen waarvan ze eigenaar zijn<br>Kan de instellingen voor het verloop beleid lezen

Zie [een verwijderde Office 365-groep herstellen in azure Active Directory](groups-restore-deleted.md)voor meer informatie over machtigingen voor het herstellen van een verwijderde groep.

## <a name="set-group-expiration"></a>Verloopdatum van de groep instellen

1. Open het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat een globale beheerder is in uw Azure AD-organisatie.

2. Selecteer **groepen**en selecteer vervolgens **verval datum** om de verloop instellingen te openen.
  
   ![Verloop instellingen voor groepen](./media/groups-lifecycle/expiration-settings.png)

3. Op de pagina **verloop** kunt u het volgende doen:

    - Stel de levens duur van de groep in op dagen. U kunt een van de vooraf ingestelde waarden selecteren, of een aangepaste waarde (moet 31 dagen of langer zijn).
    - Geef een e-mail adres op waarnaar meldingen over verlenging en verloop moeten worden verzonden wanneer een groep geen eigenaar heeft.
    - Selecteer welke Office 365-groepen verlopen. U kunt de verval datum instellen voor:
      - **Alle** Office 365-groepen
      - Een lijst met **geselecteerde** Office 365-groepen
      - **Geen** om de verval datum voor alle groepen te beperken
    - Sla de instellingen op wanneer u klaar bent door **Opslaan**te selecteren.

> [!NOTE]
> Wanneer u de verval datum voor het eerst instelt, worden de groepen die ouder zijn dan het verloop interval ingesteld op 35 dagen tot de verval datum, tenzij de groep automatisch wordt vernieuwd of de eigenaar deze vernieuwt.
>
> Wanneer een dynamische groep wordt verwijderd en teruggezet, wordt deze weer gegeven als een nieuwe groep en op basis van de regel opnieuw ingevuld. Dit proces kan Maxi maal 24 uur duren.
>
> Verloop meldingen voor groepen die worden gebruikt in teams, worden weer gegeven in de team eigen aren van teams.

## <a name="email-notifications"></a>E-mailmeldingen

Als groepen niet automatisch worden vernieuwd, worden e-mail meldingen, zoals deze, 30 dagen, 15 dagen en 1 dag vóór de verval datum van de groep verzonden naar de groeps eigenaren van Office 365. De taal van het e-mail bericht wordt bepaald door de voorkeurs taal van de eigenaar van de groep of de Azure AD-taal instelling. Als de groeps eigenaar een voorkeurs taal heeft gedefinieerd of meerdere eigen aren dezelfde Voorkeurs taal hebben, wordt die taal gebruikt. Voor alle andere gevallen wordt de Azure AD-taal instelling gebruikt.

![E-mail meldingen over verloop](./media/groups-lifecycle/expiration-notification.png)

Vanuit het e-mail adres **groep vernieuwen** kunnen groeps eigenaren rechtstreeks toegang krijgen tot de pagina groeps Details in het toegangs venster. Daar kunnen de gebruikers meer informatie krijgen over de groep, zoals de beschrijving, wanneer deze voor het laatst werd vernieuwd, wanneer de app verloopt, en ook de mogelijkheid om de groep te vernieuwen. De pagina groeps gegevens bevat nu ook koppelingen naar de groeps resources van Office 365, zodat de groeps eigenaar de inhoud en activiteit in hun groep gemakkelijk kan bekijken.

Wanneer een groep verloopt, wordt de groep één dag na de verval datum verwijderd. Er wordt een e-mail melding, zoals deze, verzonden naar de groeps eigenaren van Office 365, die ze informeert over de verval datum en latere verwijdering van de Office 365-groep.

![E-mail meldingen over verwijderen van groep](./media/groups-lifecycle/deletion-notification.png)

De groep kan binnen 30 dagen na verwijdering worden hersteld door **groep herstellen** te selecteren of door Power shell-cmdlets te gebruiken, zoals beschreven in [een verwijderde Office 365-groep herstellen in azure Active Directory](groups-restore-deleted.md). Houd er rekening mee dat de herstel periode van de groep van 30 dagen niet kan worden aangepast.

Als de groep die u wilt herstellen, documenten, share point-sites of andere permanente objecten bevat, kan het tot 24 uur duren om de groep en de inhoud ervan volledig te herstellen.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>De verval datum van een Office 365-groep ophalen

Naast het toegangs venster waarin gebruikers groeps gegevens kunnen bekijken, zoals de verval datum en de datum van laatste vernieuwing, kan de verval datum van een Office 365-groep worden opgehaald uit Microsoft Graph REST API bèta. expirationDateTime als een groeps eigenschap is ingeschakeld in Microsoft Graph bèta. Het kan worden opgehaald met een GET-aanvraag. Raadpleeg [dit voor beeld](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example)voor meer informatie.

> [!NOTE]
> Als u groepslid maatschappen in het toegangs venster wilt beheren, moet u ' toegang tot groepen in het toegangs venster beperken ' op ' nee ' instellen in Azure Active Directory algemene instelling voor groepen.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Hoe verloopt de groep van Office 365 met een postvak in juridische bewaring

Wanneer een groep verloopt en wordt verwijderd, wordt 30 dagen na het verwijderen van de gegevens van de groep uit apps zoals planner, sites of teams permanent verwijderd, maar wordt het groeps postvak dat zich in de juridische bewaring bevindt, bewaard en wordt het niet definitief verwijderd. De beheerder kan Exchange-cmdlets gebruiken om het postvak te herstellen om de gegevens op te halen.

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Hoe verloopt de groep Office 365 met het Bewaar beleid

Het Bewaar beleid wordt geconfigureerd door middel van het beveiligings-en nalevings centrum. Als u een Bewaar beleid voor Office 365-groepen hebt ingesteld, wanneer een groep verloopt en wordt verwijderd, worden de groeps gesprekken in het postvak van de groep en de bestanden in de groeps site bewaard in de Bewaar container voor het specifieke aantal dagen dat is gedefinieerd in de retentie verslaggev. Gebruikers zien de groep of de inhoud niet na het verlopen, maar kunnen de site-en Postvak gegevens via e-discovery herstellen.

## <a name="powershell-examples"></a>Power shell-voor beelden

Hier volgen enkele voor beelden van hoe u Power shell-cmdlets kunt gebruiken voor het configureren van de verloop instellingen voor Office 365-groepen in uw Azure AD-organisatie:

1. Installeer de Power shell v 2.0-module en meld u aan bij de Power shell-prompt:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. De verloop instellingen configureren gebruik de cmdlet New-AzureADMSGroupLifecyclePolicy om de levens duur van alle Office 365-groepen in de Azure AD-organisatie tot 365 dagen in te stellen. Meldingen voor het vernieuwen van Office 365-groepen zonder eigen aars worden verzonden naar deemailaddress@contoso.com
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. De bestaande beleids Get-AzureADMSGroupLifecyclePolicy ophalen: met deze cmdlet worden de huidige instellingen voor de verval datum van de Office 365-groep opgehaald die zijn geconfigureerd. In dit voor beeld ziet u het volgende:

   - De beleids-ID
   - De levens duur voor alle Office 365-groepen in de Azure AD-organisatie is ingesteld op 365 dagen
   - Meldingen voor het vernieuwen van Office 365-groepen zonder eigen aars worden verzonden naaremailaddress@contoso.com.
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. De bestaande beleids set-AzureADMSGroupLifecyclePolicy bijwerken: deze cmdlet wordt gebruikt om een bestaand beleid bij te werken. In het onderstaande voor beeld wordt de levens duur van de groep in het bestaande beleid gewijzigd van 365 dagen naar 180 dagen.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Voeg specifieke groepen toe aan het beleid add-AzureADMSLifecyclePolicyGroup: met deze cmdlet wordt een groep toegevoegd aan het beleid voor levens cyclus. Bijvoorbeeld:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Verwijder het bestaande beleid Remove-AzureADMSGroupLifecyclePolicy: met deze cmdlet worden de instellingen van de groep voor Office 365-groepen verwijderd, maar is de beleids-ID vereist. Met deze cmdlet wordt de verval datum voor Office 365-groepen uitgeschakeld.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
De volgende cmdlets kunnen worden gebruikt om het beleid uitvoeriger te configureren. Zie [Power shell-documentatie](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups)voor meer informatie.

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure AD-groepen.

- [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Leden van een groep beheren](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)
