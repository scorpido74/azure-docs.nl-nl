---
title: Azure Active Directory Domain Services inschakelen met behulp van Power shell | Microsoft Docs
description: Azure Active Directory Domain Services inschakelen met behulp van Power shell
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: iainfou
ms.openlocfilehash: c6572ab8bc2a10039f327233f983c2e822fba3b0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617217"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Azure Active Directory Domain Services inschakelen met behulp van Power shell
In dit artikel wordt beschreven hoe u Azure Active Directory (AD) Domain Services inschakelt met behulp van Power shell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="task-1-install-the-required-powershell-modules"></a>Taak 1: De vereiste Power shell-modules installeren

### <a name="install-and-configure-azure-ad-powershell"></a>Azure AD Power Shell installeren en configureren
Volg de instructies in het artikel om [de Azure AD Power shell-module te installeren en verbinding te maken met Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Azure PowerShell installeren en configureren
Volg de instructies in het artikel om [de Azure PowerShell-module te installeren en verbinding te maken met uw Azure-abonnement](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Taak 2: De vereiste service-principal maken in uw Azure AD-Directory
Typ de volgende Power shell-opdracht om de service-principal te maken die vereist is voor Azure AD Domain Services in uw Azure AD-adres lijst.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Taak 3: De groep AAD DC-Administrators maken en configureren
De volgende taak bestaat uit het maken van de groep Administrators die wordt gebruikt voor het delegeren van beheer taken in uw beheerde domein.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Nu u de groep hebt gemaakt, voegt u een aantal gebruikers toe aan de groep.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Taak 4: De Azure AD Domain Services resource provider registreren
Typ de volgende Power shell-opdracht om de resource provider voor Azure AD Domain Services te registreren:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Taak 5: Een resourcegroep maken
Typ de volgende Power shell-opdracht om een resource groep te maken:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

U kunt het virtuele netwerk en het beheerde domein Azure AD Domain Services in deze resource groep maken.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Taak 6: Het virtuele netwerk maken en configureren
Maak nu het virtuele netwerk waarin u Azure AD Domain Services inschakelt. Zorg ervoor dat u een toegewezen subnet voor Azure AD Domain Services maakt. Implementeer geen werk belasting Vm's in dit toegewezen subnet.

Typ de volgende Power shell-opdrachten voor het maken van een virtueel netwerk met een toegewezen subnet voor Azure AD Domain Services.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Taak 7: Het beheerde domein van Azure AD Domain Services inrichten
Typ de volgende Power shell-opdracht om Azure AD Domain Services in te scha kelen voor uw Directory:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Vergeet niet de aanvullende configuratie stappen na het inrichten van uw beheerde domein.**
> Nadat uw beheerde domein is ingericht, moet u nog steeds de volgende taken uitvoeren:
> * Werk de DNS-instellingen voor het virtuele netwerk bij, zodat de virtuele machines het beheerde domein kunnen vinden voor het lid worden van het domein of de verificatie. Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **overzichts** venster wordt u gevraagd deze DNS-instellingen automatisch te configureren.
> * Maak de vereiste regels voor de netwerk beveiligings groep om het binnenkomende verkeer voor het beheerde domein te beperken. Als u de regels voor de netwerk beveiligings groep wilt maken, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **overzichts** venster wordt u gevraagd automatisch de juiste regels voor de netwerk beveiligings groep te maken.
> * **[Wachtwoord synchronisatie inschakelen voor Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** , zodat eind gebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfs referenties.

## <a name="powershell-script"></a>PowerShell-script
Hieronder vindt u het Power shell-script dat wordt gebruikt voor het uitvoeren van alle taken die in dit artikel worden vermeld. Kopieer het script en sla het op in een bestand met de extensie '. ps1 '. Voer het script uit in Power shell of gebruik de Power shell Integrated Scripting Environment (ISE).

> [!NOTE]
> **Benodigde machtigingen voor het uitvoeren van dit script** Als u Azure AD Domain Services wilt inschakelen, moet u de globale beheerder zijn voor de Azure AD-adres lijst. Daarnaast moet u ten minste over Inzender bevoegdheden beschikken voor het virtuele netwerk waarin Azure AD Domain Services inschakelen.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Vergeet niet de aanvullende configuratie stappen na het inrichten van uw beheerde domein.**
> Nadat uw beheerde domein is ingericht, moet u nog steeds de volgende taken uitvoeren:
> * Werk de DNS-instellingen voor het virtuele netwerk bij, zodat de virtuele machines het beheerde domein kunnen vinden voor het lid worden van het domein of de verificatie. Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **overzichts** venster wordt u gevraagd deze DNS-instellingen automatisch te configureren.
> * Maak de vereiste regels voor de netwerk beveiligings groep om het binnenkomende verkeer voor het beheerde domein te beperken. Als u de regels voor de netwerk beveiligings groep wilt maken, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **overzichts** venster wordt u gevraagd automatisch de juiste regels voor de netwerk beveiligings groep te maken.
> * **[Wachtwoord synchronisatie inschakelen voor Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** , zodat eind gebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfs referenties.

## <a name="next-steps"></a>Volgende stappen
Nadat uw beheerde domein is gemaakt, voert u de volgende configuratie taken uit zodat u het beheerde domein kunt gebruiken:

* [De DNS-server instellingen voor het virtuele netwerk bijwerken zodat deze verwijzen naar uw beheerde domein](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
* [Wachtwoord synchronisatie met uw beheerde domein inschakelen](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)
