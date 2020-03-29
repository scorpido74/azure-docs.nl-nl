---
title: Gebruik op rollen gebaseerd toegangscontrole voor StorSimple | Microsoft Documenten
description: Beschrijft hoe u RBAC (Azure Role-based Access Control) gebruiken in de context van StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: a79753a897a62e194a759c23a9c0acc45c5f36c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66159157"
---
# <a name="role-based-access-control-for-storsimple"></a>Op rollen gebaseerd toegangscontrole voor StorSimple

In dit artikel vindt u een korte beschrijving van hoe RBAC (Azure Role-Based Access Control) kan worden gebruikt voor uw StorSimple-apparaat. RBAC biedt fijnkorrelig toegangsbeheer voor Azure. Gebruik RBAC om precies de juiste hoeveelheid toegang tot de StorSimple-gebruikers te verlenen om hun werk te doen in plaats van iedereen onbeperkte toegang te geven. Zie Aan de slag met toegangsbeheer op basis van [rollen in de Azure-portal](../role-based-access-control/overview.md)voor meer informatie over de basisprincipes van toegangsbeheer in Azure.

Dit artikel is van toepassing op apparaten uit de StorSimple 8000-serie met Update 3.0 of hoger in de Azure-portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>RBAC rollen voor StorSimple

RBAC kan worden toegewezen op basis van de rollen. De rollen zorgen voor bepaalde machtigingsniveaus op basis van de beschikbare bronnen in de omgeving. Er zijn twee soorten rollen waaruit StorSimple-gebruikers kunnen kiezen: ingebouwd of aangepast.

* **Ingebouwde rollen** - De ingebouwde rollen kunnen eigenaar, bijdrager, lezer of gebruikerstoegangsbeheerder zijn. Zie [Ingebouwde rollen voor Azure Role-based Access Control voor](../role-based-access-control/built-in-roles.md)meer informatie.

* **Aangepaste rollen** - Als de ingebouwde rollen niet aan uw behoeften voldoen, u aangepaste RBAC-rollen voor StorSimple maken. Als u een aangepaste RBAC-rol wilt maken, begint u met een ingebouwde rol, bewerkt u deze en importeert u deze terug in de omgeving. Het downloaden en uploaden van de rol wordt beheerd met Azure PowerShell of Azure CLI. Zie [Aangepaste rollen maken voor toegangsbeheer op basis van rollen voor](../role-based-access-control/custom-roles.md)meer informatie .

Als u de verschillende rollen wilt bekijken die beschikbaar zijn voor een StorSimple-apparaatgebruiker in de Azure-portal, gaat u naar uw StorSimple Device Manager-service en gaat u naar > Rollen naar **Access control (IAM).**


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Een aangepaste rol maken voor StorSimple-infrastructuurbeheerder

In het volgende voorbeeld beginnen we met de ingebouwde **rolReader** waarmee gebruikers alle resourcescopes kunnen bekijken, maar deze niet kunnen bewerken of nieuwe kunnen maken. Vervolgens breiden we deze rol uit om een nieuwe aangepaste rol StorSimple Infrastructure-beheerder te maken. Deze rol wordt toegewezen aan gebruikers die de infrastructuur voor de StorSimple-apparaten kunnen beheren.

1. Voer Windows PowerShell uit als beheerder.

2. Aanmelden bij Azure.

    `Connect-AzAccount`

3. Exporteer de readerrol als JSON-sjabloon op uw computer.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Open het JSON-bestand in Visual Studio. U ziet dat een typische RBAC-rol bestaat uit drie hoofdsecties, **Acties**, **NotActions**en **AssignableScopes**.

    In de sectie **Actie** worden alle toegestane bewerkingen voor deze rol vermeld. Elke actie wordt toegewezen door een resourceprovider. Gebruik de `Microsoft.StorSimple` resourceprovider voor een Infrastructuurbeheerder van StorSimple.

    Gebruik PowerShell om alle beschikbare en geregistreerde resourceproviders in uw abonnement te bekijken.

    `Get-AzResourceProvider`

    U ook controleren of alle beschikbare PowerShell-cmdlets de resourceproviders kunnen beheren.

    In de secties **NotActions** worden alle beperkte acties voor een bepaalde RBAC-rol vermeld. In dit voorbeeld zijn geen acties beperkt.
    
    Onder de **AssignableScopes**worden de abonnements-id's weergegeven. Zorg ervoor dat de RBAC-rol de expliciete abonnements-ID bevat waar deze wordt gebruikt. Als de juiste abonnements-ID niet is opgegeven, mag u de rol in uw abonnement niet importeren.

    Bewerk het bestand met rekening houdend met de voorgaande overwegingen.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importeer de aangepaste RBAC-rol terug in de omgeving.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Deze rol moet nu worden weergegeven in de lijst met rollen in het **toegangscontroleblad.**

![RBAC-rollen weergeven](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Ga voor meer informatie naar [Aangepaste rollen](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Voorbeelduitvoer voor het maken van aangepaste rollen via de PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Gebruikers toevoegen aan de aangepaste rol

U verleent toegang vanuit de resource en resourcegroep die of het abonnement dat het bereik van de roltoewijzing is. Houd er bij het verstrekken van toegang rekening mee dat de toegang die wordt verleend op het bovenliggende knooppunt door het kind wordt overgenomen. Ga voor meer informatie naar [op rollen gebaseerdtoegangscontrole](../role-based-access-control/overview.md).

1. Ga naar **Toegangscontrole (IAM)**. Klik op + Voeg het toegangscontroleblad **toe.**

    ![Toegang toevoegen tot De Rol RBAC](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Selecteer de rol die u wilt toewijzen, in dit geval is dit de **StorSimple-infrastructuurbeheerder**.

3. Selecteer de gebruiker, groep of toepassing in de directory aan wie/waaraan u toegang wilt verlenen. U kunt zoeken in de directory met weergavenamen, e-mailadressen en object-id's.

4. Selecteer **Opslaan** om de toewijzing te maken.

    ![Machtigingen toevoegen aan de Rol RBAC](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Een **melding van een gebruiker toevoegen** houdt de voortgang bij. Nadat de gebruiker is toegevoegd, wordt de lijst met gebruikers in Toegangsbeheer bijgewerkt.

## <a name="view-permissions-for-the-custom-role"></a>Machtigingen voor de aangepaste rol weergeven

Zodra deze rol is gemaakt, u de machtigingen bekijken die aan deze rol zijn gekoppeld in de Azure-portal.

1. Als u de machtigingen wilt weergeven die aan deze rol zijn gekoppeld, gaat u naar **> Rollen > StorSimple-infrastructuurbeheerder**. De lijst met gebruikers in deze rol wordt weergegeven.

2. Selecteer een gebruiker van StorSimple-infrastructuurbeheerder en klik op **Machtigingen**.

    ![Machtigingen weergeven voor de rol StorSimple Infra Admin](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. De machtigingen die aan deze rol zijn gekoppeld, worden weergegeven.

    ![Gebruikers weergeven in de rol StorSimple Infra Admin](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [toewijzen van aangepaste rollen voor interne en externe gebruikers](../role-based-access-control/role-assignments-external-users.md).
