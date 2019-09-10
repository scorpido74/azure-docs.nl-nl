---
title: Azure Active Directory Domain Services inschakelen met behulp van Power shell | Microsoft Docs
description: Meer informatie over het configureren en inschakelen van Azure Active Directory Domain Services met behulp van Azure AD Power shell en Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: 163259af3797b652c9605c171447f4a7d2576c87
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842712"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Azure Active Directory Domain Services inschakelen met behulp van Power shell

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U gebruikt deze domein Services zonder zelf domein controllers te implementeren, beheren en repareren. Azure AD DS integreert met uw bestaande Azure AD-Tenant. Met deze integratie kunnen gebruikers zich aanmelden met hun bedrijfs referenties, en kunt u bestaande groepen en gebruikers accounts gebruiken om de toegang tot bronnen te beveiligen.

Dit artikel laat u zien hoe u Azure AD DS kunt inschakelen met behulp van Power shell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om dit artikel te volt ooien:

* Installeer en configureer Azure PowerShell.
    * Als dat nodig is, volgt u de instructies om [de Azure PowerShell-module te installeren en verbinding te maken met uw Azure-abonnement](/powershell/azure/install-az-ps).
    * Zorg ervoor dat u zich aanmeldt bij uw Azure-abonnement met behulp van de cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Azure AD Power Shell installeren en configureren.
    * Als dat nodig is, volgt u de instructies voor [het installeren van de Azure AD Power shell-module en verbinding maken met Azure AD](/powershell/azure/active-directory/install-adv2).
    * Zorg ervoor dat u zich aanmeldt bij uw Azure AD-Tenant met de cmdlet [Connect-AzureAD][Connect-AzureAD] .
* U hebt *globale beheerders* bevoegdheden nodig in uw Azure AD-Tenant om Azure AD DS in te scha kelen.
* U hebt *Inzender* bevoegdheden nodig in uw Azure-abonnement om de vereiste Azure AD DS-resources te maken.

## <a name="create-required-azure-ad-resources"></a>Vereiste Azure AD-resources maken

Voor Azure AD DS is een Service-Principal en een Azure AD-groep vereist. Met deze resources kunnen de Azure AD DS beheerde domein gegevens synchroniseren en definiëren welke gebruikers beheerders machtigingen hebben in het beheerde domein.

Maak eerst een Azure AD-service-principal voor Azure AD DS om te communiceren en zichzelf te verifiëren. Een specifieke toepassings-ID wordt gebruikt met de naam *Domain Controller Services* met de id *2565bd9d-DA50-47d4-8b85-4c97f669dc36*. Wijzig deze toepassings-ID niet.

Maak een Azure AD-Service-Principal met behulp van de cmdlet [New-azureadserviceprincipal namelijk niet][New-AzureADServicePrincipal] :

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Maak nu een Azure AD-groep met de naam *Aad DC-Administrators*. Aan gebruikers die zijn toegevoegd aan deze groep worden machtigingen verleend voor het uitvoeren van beheer taken op het door Azure AD DS beheerde domein.

Maak de groep *Aad DC Administrators* met behulp van de cmdlet [New-AzureADGroup][New-AzureADGroup] :

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Wanneer de groep *Aad DC-Administrators* is gemaakt, voegt u een gebruiker aan de groep toe met behulp van de cmdlet [add-AzureADGroupMember][Add-AzureADGroupMember] . U krijgt eerst de groeps object-ID voor *Aad DC-Administrators* met behulp van de cmdlet [Get-AzureADGroup][Get-AzureADGroup] en vervolgens de object-id van de gewenste gebruiker met de cmdlet [Get-AzureADUser][Get-AzureADUser] .

In het volgende voor beeld is dit de gebruikers object-ID voor het account met `admin@contoso.onmicrosoft.com`een UPN van. Vervang dit gebruikers account door de UPN van de gebruiker die u wilt toevoegen aan de groep *Aad DC-Administrators* :

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

## <a name="create-supporting-azure-resources"></a>Ondersteunende Azure-resources maken

Registreer eerst de Azure AD Domain Services resource provider met behulp [van de cmdlet REGI ster-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Maak vervolgens een resource groep met behulp van de cmdlet [New-AzResourceGroup][New-AzResourceGroup] . In het volgende voor beeld heeft de resource groep de naam *myResourceGroup* en wordt deze gemaakt in de regio *westus* . Gebruik uw eigen naam en gewenste regio:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Maak het virtuele netwerk en de subnetten voor Azure AD Domain Services. Er zijn twee subnetten gemaakt: één voor *DomainServices*en één voor *werk belastingen*. Azure AD DS is geïmplementeerd in het toegewezen *DomainServices* -subnet. Implementeer geen andere toepassingen of workloads in dit subnet. Gebruik de afzonderlijke *werk belastingen* of andere subnetten voor de rest van uw vm's.

Maak de subnetten met behulp van de cmdlet [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] en maak het virtuele netwerk met behulp van de cmdlet [New-AzVirtualNetwork][New-AzVirtualNetwork] .

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-an-azure-ad-ds-managed-domain"></a>Een door Azure AD DS beheerd domein maken

Nu gaan we een beheerd domein van Azure AD DS maken. Stel de ID van uw Azure-abonnement in en geef een naam op voor het beheerde domein, zoals *contoso.com*. U kunt uw abonnements-ID ophalen met behulp van de cmdlet [Get-AzSubscription][Get-AzSubscription] .

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Het duurt enkele minuten om de resource te maken en het besturings element terug te sturen naar de Power shell-prompt. Het beheerde domein van Azure AD DS blijft op de achtergrond worden ingericht en kan tot een uur duren voordat de implementatie is voltooid. In de Azure Portal wordt op de pagina **overzicht** voor uw door Azure AD DS beheerde domein de huidige status weer gegeven in de gehele implementatie fase.

Wanneer de Azure Portal laat zien dat de inrichting van het beheerde Azure AD DS-domein is voltooid, moeten de volgende taken worden uitgevoerd:

* Werk de DNS-instellingen voor het virtuele netwerk bij, zodat de virtuele machines het beheerde domein kunnen vinden voor het lid worden van het domein of de verificatie.
    * Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **overzichts** venster wordt u gevraagd deze DNS-instellingen automatisch te configureren.
* [Wachtwoord synchronisatie inschakelen voor Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eind gebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfs referenties.

## <a name="complete-powershell-script"></a>Power shell-script volt ooien

In het volgende volledige Power shell-script worden alle taken gecombineerd die in dit artikel worden weer gegeven. Kopieer het script en sla het op in een bestand met `.ps1` een extensie. Voer het script uit in een lokale Power shell-console of in de [Azure Cloud shell][cloud-shell].

> [!NOTE]
> Als u Azure AD DS wilt inschakelen, moet u een globale beheerder zijn voor de Azure AD-Tenant. U hebt ook ten minste *Inzender* bevoegdheden nodig in het Azure-abonnement.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"

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
  -Force -Verbose
```

Het duurt enkele minuten om de resource te maken en het besturings element terug te sturen naar de Power shell-prompt. Het beheerde domein van Azure AD DS blijft op de achtergrond worden ingericht en kan tot een uur duren voordat de implementatie is voltooid. In de Azure Portal wordt op de pagina **overzicht** voor uw door Azure AD DS beheerde domein de huidige status weer gegeven in de gehele implementatie fase.

Wanneer de Azure Portal laat zien dat de inrichting van het beheerde Azure AD DS-domein is voltooid, moeten de volgende taken worden uitgevoerd:

* Werk de DNS-instellingen voor het virtuele netwerk bij, zodat de virtuele machines het beheerde domein kunnen vinden voor het lid worden van het domein of de verificatie.
    * Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **overzichts** venster wordt u gevraagd deze DNS-instellingen automatisch te configureren.
* [Wachtwoord synchronisatie inschakelen voor Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eind gebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfs referenties.

## <a name="next-steps"></a>Volgende stappen

Als u de Azure AD DS beheerde domein in actie wilt zien, kunt u [een domein toevoegen aan een Windows-VM, een][windows-join] [beveiligd LDAP configureren][tutorial-ldaps]en een [wachtwoord-hash-synchronisatie configureren][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
