---
title: Statisch groepslidmaatschap wijzigen in dynamisch - Azure AD | Microsoft Documenten
description: Lidmaatschapsregels maken om groepen automatisch te vullen en een regelverwijzing.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34451fcc4597f77464e5e9566613e21e9fecdbc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027318"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Statisch groepslidmaatschap wijzigen in dynamisch in Azure Active Directory

U het lidmaatschap van een groep wijzigen van statisch naar dynamisch (of vice versa) in Azure Active Directory (Azure AD). Azure AD behoudt dezelfde groepsnaam en -id in het systeem, zodat alle bestaande verwijzingen naar de groep nog steeds geldig zijn. Als u in plaats daarvan een nieuwe groep maakt, moet u deze verwijzingen bijwerken. Dynamisch groepslidmaatschap elimineert het toevoegen en verwijderen van gebruikers in beheeroverhead. In dit artikel vindt u hoe u bestaande groepen converteert van statisch naar dynamisch lidmaatschap met Azure AD-beheercentrum of PowerShell-cmdlets.

> [!WARNING]
> Wanneer u een bestaande statische groep wijzigt in een dynamische groep, worden alle bestaande leden uit de groep verwijderd en vervolgens wordt de lidmaatschapsregel verwerkt om nieuwe leden toe te voegen. Als de groep wordt gebruikt om de toegang tot apps of bronnen te beheren, moet u er rekening mee houden dat de oorspronkelijke leden de toegang kunnen verliezen totdat de lidmaatschapsregel volledig is verwerkt.
>
> We raden u aan de nieuwe lidmaatschapsregel vooraf te testen om ervoor te zorgen dat het nieuwe lidmaatschap van de groep is zoals verwacht.

## <a name="change-the-membership-type-for-a-group"></a>Het lidmaatschapstype voor een groep wijzigen

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een account dat een globale beheerder of een gebruikersbeheerder in uw tenant is.
2. Selecteer **Groepen**.
3. Open in de lijst **Alle groepen** de groep die u wilt wijzigen.
4. Selecteer **Eigenschappen**.
5. Selecteer **op** de pagina Eigenschappen voor de groep een **lidmaatschapstype** toegewezen (statisch), Dynamische gebruiker of Dynamisch apparaat, afhankelijk van het gewenste lidmaatschapstype. Voor dynamisch lidmaatschap u de regelbouwer gebruiken om opties voor een eenvoudige regel te selecteren of zelf een lidmaatschapsregel te schrijven. 

De volgende stappen zijn een voorbeeld van het wijzigen van een groep van statisch naar dynamisch lidmaatschap voor een groep gebruikers.

1. Selecteer **op** de pagina Eigenschappen voor de geselecteerde groep een **lidmaatschapstype** **dynamische gebruiker**en selecteer Vervolgens Ja in het dialoogvenster waarin de wijzigingen in het groepslidmaatschap worden uitgelegd om door te gaan. 
  
   ![het lidmaatschapstype dynamische gebruiker selecteren](./media/groups-change-type/select-group-to-convert.png)
  
2. Selecteer **Dynamische query toevoegen**en geef de regel op.
  
   ![voer de regel in voor de dynamische groep](./media/groups-change-type/enter-rule.png)
  
3. Nadat u de regel hebt hebt hebt geselecteerd, selecteert u Query onder aan de pagina **toevoegen.**
4. Selecteer **Opslaan** op de pagina **Eigenschappen** voor de groep om uw wijzigingen op te slaan. Het **lidmaatschapstype** van de groep wordt onmiddellijk bijgewerkt in de groepslijst.

> [!TIP]
> Groepsconversie kan mislukken als de lidmaatschapsregel die u hebt ingevoerd onjuist was. Een melding wordt weergegeven in de rechterbovenhoek van het portaal dat het een uitleg bevat waarom de regel niet door het systeem kan worden geaccepteerd. Lees het zorgvuldig om te begrijpen hoe u de regel aanpassen om deze geldig te maken. Zie [Dynamische lidmaatschapsregels voor groepen in Azure Active Directory](groups-dynamic-membership.md)voor voorbeelden van syntaxis en een volledige lijst met de ondersteunde eigenschappen, operators en waarden voor een lidmaatschapsregel.

## <a name="change-membership-type-for-a-group-powershell"></a>Lidmaatschapstype voor een groep wijzigen (PowerShell)

> [!NOTE]
> Als u dynamische groepseigenschappen wilt wijzigen, moet u cmdlets gebruiken vanaf **de preview-versie van** [Azure AD PowerShell-versie 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). U de preview installeren vanuit de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview).

Hier is een voorbeeld van functies die het lidmaatschapsbeheer op een bestaande groep veranderen. In dit voorbeeld wordt ervoor gezorgd dat de eigenschap GroupTypes correct wordt gemanipuleerd en waarden behouden die geen verband houden met dynamisch lidmaatschap.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Ga als u een groep statisch maken:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Een groep dynamisch maken:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over groepen in Azure Active Directory.

* [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Een nieuwe groep maken en leden toevoegen](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)
