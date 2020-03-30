---
title: Gebruikersmachtigingen verlenen voor specifiek labbeleid | Microsoft Documenten
description: Meer informatie over het verlenen van gebruikersmachtigingen voor specifiek labbeleid in DevTest Labs op basis van de behoeften van elke gebruiker
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: spelluru
ms.openlocfilehash: 9b31f3e68fbabc32f301fdcd8066a3bfbf1c2dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284211"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Gebruikersmachtigingen verlenen aan specifiek labbeleid
## <a name="overview"></a>Overzicht
In dit artikel wordt uitgelegd hoe u PowerShell gebruiken om gebruikers machtigingen te verlenen voor een bepaald labbeleid. Op die manier kunnen machtigingen worden toegepast op basis van de behoeften van elke gebruiker. U een bepaalde gebruiker bijvoorbeeld de mogelijkheid bieden om de VM-beleidsinstellingen te wijzigen, maar niet het kostenbeleid.

## <a name="policies-as-resources"></a>Beleid als resources
Zoals besproken in het op [Azure Role-gebaseerde toegangscontroleartikel,](../role-based-access-control/role-assignments-portal.md) maakt RBAC fijnkorrelig toegangsbeheer van resources voor Azure mogelijk. Met RBAC u taken binnen uw DevOps-team scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers die ze nodig hebben om hun taken uit te voeren.

In DevTest Labs is een beleid een resourcetype waarmee de **RBAC-actie Microsoft.DevTestLab/labs/policySets/policies/**. Elk labbeleid is een resource in het resourcetype Beleid en kan worden toegewezen als een bereik aan een RBAC-rol.

Als u gebruikers bijvoorbeeld lees-/schrijftoestemming wilt geven aan het beleid **voor toegestane VM-formaten,** maakt u een aangepaste rol die werkt met het **Microsoft.DevTestLab/labs/policySets/policies/action,** en wijst u vervolgens de juiste gebruikers toe aan deze aangepaste rol in het bereik van **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Zie het [toegangscontrolebeheer aangepaste rollen](../role-based-access-control/custom-roles.md)voor meer informatie over aangepaste rollen in RBAC.

## <a name="creating-a-lab-custom-role-using-powershell"></a>Een aangepaste labrol maken met PowerShell
Om aan de slag te gaan, moet u [Azure PowerShell installeren.](/powershell/azure/install-az-ps) 

Zodra u de Azure PowerShell-cmdlets hebt ingesteld, u de volgende taken uitvoeren:

* Alle bewerkingen/acties voor een resourceprovider weergeven
* Acties in een bepaalde rol vermelden:
* Een aangepaste rol maken

In het volgende PowerShell-script worden voorbeelden geïllustreerd van het uitvoeren van deze taken:

    # List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    # List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    # Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Machtigingen toewijzen aan een gebruiker voor een specifiek beleid met aangepaste rollen
Zodra u uw aangepaste rollen hebt gedefinieerd, u deze toewijzen aan gebruikers. Als u een aangepaste rol aan een gebruiker wilt toewijzen, moet u eerst de **ObjectId** verkrijgen die die gebruiker vertegenwoordigt. Gebruik daarvoor de **get-azaduser-cmdlet.**

In het volgende voorbeeld is de **ObjectId** van de *SomeUser-gebruiker* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Zodra u de **ObjectId** voor de gebruiker en een aangepaste rolnaam hebt, u die rol aan de gebruiker toewijzen met de cmdlet **Nieuw-AzRoleAssignment:**

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

In het vorige voorbeeld wordt het **beleid AllowedVmSizesInLab** gebruikt. U een van de volgende polities gebruiken:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Zodra u gebruikersmachtigingen hebt verleend voor specifiek labbeleid, volgen hier enkele volgende stappen om rekening mee te houden:

* [Toegang tot een lab beveiligen](devtest-lab-add-devtest-user.md)
* [Labbeleid instellen](devtest-lab-set-lab-policy.md)
* [Een labsjabloon maken](devtest-lab-create-template.md)
* [Aangepaste artefacten maken voor uw VM's](devtest-lab-artifact-author.md)
* [Een virtuele machine aan een lab toevoegen](devtest-lab-add-vm.md)

