---
title: 'Azure ExpressRoute: MACsec configureren'
description: Met dit artikel u MACsec configureren om de verbindingen tussen uw edgerouters en de edgerouters van Microsoft te beveiligen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 572147ca43e9a4dea9d9601dfa1dac8ba1c97ed0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458229"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>MACsec configureren op ExpressRoute Direct-poorten

Met dit artikel u MACsec configureren om de verbindingen tussen uw edgerouters en microsofts edgerouters te beveiligen met PowerShell.

## <a name="before-you-begin"></a>Voordat u begint

Bevestig het volgende voordat u de configuratie start:

* U begrijpt [ExpressRoute Direct inrichten van workflows.](expressroute-erdirect-about.md)
* U hebt een [ExpressRoute Direct-poortbron gemaakt.](expressroute-howto-erdirect.md)
* Als u PowerShell lokaal wilt uitvoeren, controleert u of de nieuwste versie van Azure PowerShell op uw computer is geïnstalleerd.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Aanmelden en het juiste abonnement selecteren

Als u de configuratie wilt starten, meldt u zich aan bij uw Azure-account en selecteert u het abonnement dat u wilt gebruiken.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Azure Key Vault, MACsec-geheimen en gebruikersidentiteit maken

1. Maak een Key Vault-exemplaar om MACsec-geheimen op te slaan in een nieuwe resourcegroep.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Als u al een sleutelkluis of een resourcegroep hebt, u deze opnieuw gebruiken. Het is echter van cruciaal belang dat u de functie [ **soft-delete** ](../key-vault/general/overview-soft-delete.md) inschakelt op uw bestaande sleutelkluis. Als soft-delete niet is ingeschakeld, u de volgende opdrachten gebruiken om deze in te schakelen:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Maak een gebruikersidentiteit.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Als Nieuw-AzUserAssignedIdentity niet wordt herkend als een geldige PowerShell-cmdlet, installeert u de volgende module (in de administratormodus) en voert u de bovenstaande opdracht opnieuw uit.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Maak een verbindingskoppelingssleutel (CAK) en een sleutelnaam van de verbindingskoppeling (CKN) en bewaar deze in de sleutelkluis.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Wijs de GET-toestemming toe aan de gebruikersidentiteit.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Nu kan deze identiteit de geheimen, bijvoorbeeld CAK en CKN, uit de sleutelkluis halen.
5. Stel deze gebruikersidentiteit in om te worden gebruikt door ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. MACsec configureren op ExpressRoute Direct-poorten

### <a name="to-enable-macsec"></a>Macsec inschakelen

Elke Instantie ExpressRoute Direct heeft twee fysieke poorten. U ervoor kiezen om MACsec op beide poorten tegelijk in te schakelen of MACsec in te schakelen op één poort tegelijk. Als u dit één poort tegelijk doet (door het verkeer over te schakelen naar een actieve poort terwijl u de andere poort onderhoudt) kan dit helpen de onderbreking te minimaliseren als uw ExpressRoute Direct al in gebruik is.

1. Stel MACsec-geheimen in en vercijfer en koppel de gebruikersidentiteit aan de poort, zodat de ExpressRoute-beheercode indien nodig toegang heeft tot de MACsec-geheimen.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (Optioneel) Als de poorten in de status Bestuursrechter staan, u de volgende opdrachten uitvoeren om de poorten weer te geven.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    Op dit moment is MACsec ingeschakeld op de ExpressRoute Direct-poorten aan Microsoft-zijde. Als u het niet hebt geconfigureerd op uw edge-apparaten, u deze configureren met dezelfde MACsec-geheimen en -code.

### <a name="to-disable-macsec"></a>MACsec uitschakelen

Als MACsec niet langer gewenst is op uw ExpressRoute Direct-exemplaar, u de volgende opdrachten uitvoeren om deze uit te schakelen.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

Op dit moment is MACsec uitgeschakeld op de ExpressRoute Direct-poorten aan de Microsoft-kant.

### <a name="test-connectivity"></a>Connectiviteit testen
Nadat u MACsec (inclusief MACsec-sleutelupdate) hebt geconfigureerd op uw ExpressRoute Direct-poorten, [controleert u of](expressroute-troubleshooting-expressroute-overview.md) de BGP-sessies van de circuits actief zijn. Als u nog geen circuit op de poorten hebt, maakt u er eerst een en stelt u Azure Private Peering of Microsoft Peering van het circuit in. Als MACsec verkeerd is geconfigureerd, inclusief de mismatch van DE MACsec-sleutel, tussen uw netwerkapparaten en de netwerkapparaten van Microsoft, ziet u geen ARP-resolutie op laag 2 en BGP-instelling in laag 3. Als alles goed is geconfigureerd, ziet u de BGP-routes correct geadverteerd in beide richtingen en uw applicatiegegevens stromen dienovereenkomstig over ExpressRoute.

## <a name="next-steps"></a>Volgende stappen
1. [Maak een ExpressRoute-circuit op ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Een ExpressRoute-circuit koppelen aan een virtueel Azure-netwerk](expressroute-howto-linkvnet-arm.md)
3. [ExpressRoute-connectiviteit verifiëren](expressroute-troubleshooting-expressroute-overview.md)
