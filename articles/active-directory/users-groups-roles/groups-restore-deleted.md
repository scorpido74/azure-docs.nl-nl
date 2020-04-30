---
title: Een verwijderde Office 365-groep herstellen-Azure AD | Microsoft Docs
description: Een verwijderde groep herstellen, groepen weergeven die kunnen worden hersteld en een groep definitief verwijderen in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 934422b128fe591d16b9a1dbf73af2bc5264123e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582815"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Een verwijderde Office 365-groep herstellen in Azure Active Directory

Wanneer u een Office 365-groep verwijdert in Azure AD (Azure Active Directory), blijft de verwijderde groep behouden maar is deze gedurende 30 dagen na de verwijderingsdatum niet meer zichtbaar. Zo kunnen de groep en bijbehorende inhoud indien nodig nog worden hersteld. Deze functionaliteit is exclusief beperkt tot Office 365-groepen in Azure AD. Het is niet beschikbaar voor beveiligingsgroepen en distributiegroepen. Houd er rekening mee dat de herstel periode van de groep van 30 dagen niet kan worden aangepast.

> [!NOTE]
> Gebruik `Remove-MsolGroup` niet omdat de groep dan definitief wordt leeggemaakt. Gebruik altijd `Remove-AzureADMSGroup` om een Office 365-groep te verwijderen.

De machtigingen die zijn vereist om een groep te herstellen, kunnen zijn:

Rol | Machtigingen
--------- | ---------
Globale beheerder, groeps beheerder, partner Tier2 ondersteuning en intune-beheerder | Kan elke willekeurige Office 365-groep herstellen
Ondersteuning van gebruikers beheerder en partner Tier1 | Kan elke verwijderde Office 365-groep herstellen, behalve de groepen die zijn toegewezen aan de rol Bedrijfsbeheerder
Gebruiker | Kan alle verwijderde Office 365-groepen waarvan ze eigenaar zijn herstellen

## <a name="view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore"></a>De verwijderde Office 365-groepen weergeven en beheren die beschikbaar zijn om te herstellen

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een beheerders account van de gebruiker.

2. Selecteer **Groepen** en vervolgens **Verwijderde groepen** om de verwijderde groepen weer te geven die beschikbaar zijn om te herstellen.

    ![groepen weer geven die kunnen worden hersteld](media/groups-lifecycle/deleted-groups3.png)

3. Op de blade **Verwijderde groepen** kunt u:

   - De verwijderde groep en de inhoud ervan herstellen door **Groep herstellen** te selecteren.
   - De verwijderde groep definitief verwijderen door **Definitief verwijderen** te selecteren. U kunt een groep alleen definitief verwijderen als u een beheerder bent.

## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore-using-powershell"></a>De verwijderde Office 365-groepen weergeven die beschikbaar zijn om te herstellen met behulp van Powershell

De volgende cmdlets kunnen worden gebruikt om de verwijderde groepen weer te geven. Zo kunt u controleren of de groep of groepen waarin u geïnteresseerd bent, nog niet definitief zijn leeggemaakt. Deze cmdlets vormen onderdeel van de [Azure AD PowerShell-module](https://www.powershellgallery.com/packages/AzureAD/). In het artikel [Azure Active Directory PowerShell-versie 2](/powershell/azure/install-adv2?view=azureadps-2.0) vindt u meer informatie over deze module.

1.  Voer de volgende cmdlet uit om alle verwijderde Office 365-groepen in uw Azure AD-organisatie weer te geven die nog steeds beschikbaar zijn om te herstellen.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Als u de object-id van een specifieke groep weet (u kunt deze ophalen uit de cmdlet in stap 1) , kunt u de ook volgende cmdlet uitvoeren om te controleren dat de specifieke verwijderde groep nog niet definitief is leeggemaakt.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-office-365-group-using-powershell"></a>Uw verwijderde Office 365-groep herstellen met behulp van Powershell

Zodra u hebt gecontroleerd of de groep nog steeds beschikbaar is voor herstellen, herstelt u de verwijderde groep met een van de volgende stappen. Als de groep documenten, SP-sites of andere permanente objecten bevat, duurt het maximaal 24 uur om deze groep en de bijbehorende inhoud te herstellen.

1. Voer de volgende cmdlet uit om de groep en de bijbehorende inhoud te herstellen.
 

   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. U kunt ook de volgende cmdlet uitvoeren om de verwijderde groep definitief te verwijderen.
    

    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Hoe weet u of dit heeft gewerkt?

Voer de cmdlet `Get-AzureADGroup –ObjectId <objectId>` uit om informatie over de groep weer te geven als u wilt controleren of het herstellen van een Office 365-groep is geslaagd. Nadat de herstelaanvraag is voltooid, ziet u het volgende:

- De groep wordt in de linkernavigatiebalk in Exchange weergegeven
- Het plan voor de groep wordt weergegeven in Planner
- Alle SharePoint-sites en alle bijbehorende inhoud zijn beschikbaar
- Toegang tot de groep kan worden verkregen via elk Exchange-eindpunt en andere Office 365-workload die ondersteuning biedt voor Office 365-groepen

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure Active Directory-groepen.

* [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Leden van een groep beheren](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)
