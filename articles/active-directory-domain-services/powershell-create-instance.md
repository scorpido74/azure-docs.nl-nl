---
title: Azure DS-domeinservices inschakelen met PowerShell | Microsoft Documenten
description: Meer informatie over het configureren en inschakelen van Azure Active Directory Domain Services met Azure AD PowerShell en Azure PowerShell.
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
ms.openlocfilehash: ee85002aea962dfa675ac6c09a6bfbaeba8e9e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613229"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Azure Active Directory Domain Services inschakelen met PowerShell

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U gebruikt deze domeinservices zonder zelf domeincontrollers te implementeren, te beheren en te patchen. Azure AD DS integreert met uw bestaande Azure AD-tenant. Met deze integratie kunnen gebruikers zich aanmelden met hun bedrijfsreferenties en u bestaande groepen en gebruikersaccounts gebruiken om de toegang tot bronnen te beveiligen.

In dit artikel ziet u hoe u Azure AD DS inschakelt met PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Om dit artikel te voltooien, hebt u de volgende bronnen nodig:

* Installeer en configureer Azure PowerShell.
    * Volg indien nodig de instructies om [de Azure PowerShell-module te installeren en verbinding te maken met uw Azure-abonnement.](/powershell/azure/install-az-ps)
    * Zorg ervoor dat u zich aanmeldt bij uw Azure-abonnement met de [cmdlet Connect-AzAccount.][Connect-AzAccount]
* Azure AD PowerShell installeren en configureren.
    * Volg indien nodig de instructies om [de Azure AD PowerShell-module](/powershell/azure/active-directory/install-adv2)te installeren en verbinding te maken met Azure AD.
    * Zorg ervoor dat u zich aanmeldt bij uw Azure AD-tenant met de cmdlet [Connect-AzureAD.][Connect-AzureAD]
* U hebt *algemene beheerdersrechten* nodig in uw Azure AD-tenant om Azure AD DS in te schakelen.
* U hebt *inzenderbevoegdheden* in uw Azure-abonnement nodig om de vereiste Azure AD DS-resources te maken.

## <a name="create-required-azure-ad-resources"></a>Vereiste Azure AD-resources maken

Azure AD DS vereist een serviceprincipal en een Azure AD-groep. Met deze bronnen kunnen het door Azure AD DS beheerde domein gegevens synchroniseren en bepalen welke gebruikers beheerdersmachtigingen hebben in het beheerde domein.

Maak eerst een Azure AD-serviceprincipal voor Azure AD DS om zichzelf te communiceren en te verifiëren. Een specifieke toepassings-ID wordt gebruikt met de naam *Domain Controller Services* met een ID van *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Wijzig deze toepassings-id niet.

Maak een Azure AD-serviceprincipal met de cmdlet [Nieuw-AzureADServicePrincipal:][New-AzureADServicePrincipal]

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Maak nu een Azure AD-groep met de naam *AAD DC Administrators*. Gebruikers die aan deze groep zijn toegevoegd, krijgen vervolgens machtigingen om beheertaken uit te voeren op het beheerde Azure AD DS-domein.

De groep *AAD DC-beheerders maken* met de cmdlet [Nieuw-AzureADGroup:][New-AzureADGroup]

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Als de groep *AAD DC-beheerders* is gemaakt, voegt u een gebruiker toe aan de groep met de cmdlet [Add-AzureADGroupMember.][Add-AzureADGroupMember] U krijgt eerst de groepsobject-id *van AAD DC Administrators* met de cmdlet [Get-AzureADGroup][Get-AzureADGroup] en vervolgens de object-id van de gewenste gebruiker met de cmdlet [Get-AzureADUser.][Get-AzureADUser]

In het volgende voorbeeld wordt de gebruikersnaam van het `admin@aaddscontoso.onmicrosoft.com`gebruikersobject voor het account met een UPN van . Vervang dit gebruikersaccount door de UPN van de gebruiker die u wilt toevoegen aan de groep *AAD DC-beheerders:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Ondersteunende Azure-bronnen maken

Registreer eerst de Azure AD Domain Services-bronprovider met de cmdlet [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Maak vervolgens een resourcegroep met de cmdlet [Nieuw-AzResourceGroep.][New-AzResourceGroup] In het volgende voorbeeld wordt de resourcegroep *myResourceGroup* genoemd en wordt deze gemaakt in de *regio Westus.* Gebruik uw eigen naam en gewenste regio:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Maak het virtuele netwerk en de subnetten voor Azure AD Domain Services. Er worden twee subnetten gemaakt: één voor *DomainServices*en één voor *Workloads*. Azure AD DS wordt geïmplementeerd in het speciale *subnet DomainServices.* Implementeer geen andere toepassingen of workloads in dit subnet. Gebruik de afzonderlijke *workloads* of andere subnetten voor de rest van uw VM's.

Maak de subnetten met behulp van de [nieuw-AzVirtualNetworkSubnetConfig-cmdlet][New-AzVirtualNetworkSubnetConfig] en maak vervolgens het virtuele netwerk met behulp van de cmdlet [Nieuw-AzVirtualNetwork.][New-AzVirtualNetwork]

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>Een beheerd Azure AD DS-domein maken

Laten we nu een door Azure AD DS beheerd domein maken. Stel uw Azure-abonnements-id in en geef vervolgens een naam op voor het beheerde domein, zoals *aaddscontoso.com*. U uw abonnements-ID krijgen met de [cmdlet Get-AzSubscription.][Get-AzSubscription]

Als u een regio kiest die beschikbaarheidszones ondersteunt, worden de Azure AD DS-resources verdeeld over zones voor extra redundantie.

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's.

U er niets voor configureren dat Azure AD DS over zones wordt verdeeld. Het Azure-platform verwerkt automatisch de zonedistributie van resources. Zie [Beschikbaarheidszones in Azure voor][availability-zones]meer informatie en voor de beschikbaarheid van de regio.

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Het duurt een paar minuten om de resource te maken en het besturingselement terug te geven aan de PowerShell-prompt. Het beheerde Azure AD DS-domein blijft op de achtergrond worden ingericht en kan tot een uur duren voordat de implementatie is voltooid. In de Azure-portal wordt op de **pagina Overzicht** voor uw door Azure AD DS beheerde domein de huidige status in deze implementatiefase weergegeven.

Wanneer de Azure-portal aangeeft dat het door Azure AD DS beheerde domein is voltooid, moeten de volgende taken worden voltooid:

* Dns-instellingen voor het virtuele netwerk bijwerken, zodat virtuele machines het beheerde domein kunnen vinden voor domeinjoin of verificatie.
    * Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **venster Overzicht** wordt u gevraagd deze DNS-instellingen automatisch te configureren.
* Als u een door Azure AD DS beheerd domein hebt gemaakt in een regio die beschikbaarheidszones ondersteunt, maakt u een netwerkbeveiligingsgroep om het verkeer in het virtuele netwerk voor het beheerde Azure AD DS-beheerde domein te beperken. Er wordt een Azure-standaardloadbalancer gemaakt waarvoor deze regels moeten worden geïnstalleerd. Deze netwerkbeveiligingsgroep beveiligt Azure AD DS en is vereist dat het beheerde domein correct werkt.
    * Als u de netwerkbeveiligingsgroep en vereiste regels wilt maken, selecteert u uw door Azure AD DS beheerde domein in de portal. In het venster **Overzicht** wordt u gevraagd om de netwerkbeveiligingsgroep automatisch te maken en te configureren.
* [Wachtwoordsynchronisatie inschakelen voor Azure AD Domain Services,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eindgebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfsreferenties.

## <a name="complete-powershell-script"></a>PowerShell-script voltooien

Het volgende volledige PowerShell-script combineert alle taken in dit artikel. Kopieer het script en sla het `.ps1` op in een bestand met een extensie. Voer het script uit in een lokale PowerShell-console of de [Azure Cloud Shell.][cloud-shell]

> [!NOTE]
> Als u Azure AD DS wilt inschakelen, moet u een globale beheerder zijn voor de Azure AD-tenant. U hebt ook ten minste *inzenderbevoegdheden* nodig in het Azure-abonnement.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

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

Het duurt een paar minuten om de resource te maken en het besturingselement terug te geven aan de PowerShell-prompt. Het beheerde Azure AD DS-domein blijft op de achtergrond worden ingericht en kan tot een uur duren voordat de implementatie is voltooid. In de Azure-portal wordt op de **pagina Overzicht** voor uw door Azure AD DS beheerde domein de huidige status in deze implementatiefase weergegeven.

Wanneer de Azure-portal aangeeft dat het door Azure AD DS beheerde domein is voltooid, moeten de volgende taken worden voltooid:

* Dns-instellingen voor het virtuele netwerk bijwerken, zodat virtuele machines het beheerde domein kunnen vinden voor domeinjoin of verificatie.
    * Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **venster Overzicht** wordt u gevraagd deze DNS-instellingen automatisch te configureren.
* Als u een door Azure AD DS beheerd domein hebt gemaakt in een regio die beschikbaarheidszones ondersteunt, maakt u een netwerkbeveiligingsgroep om het verkeer in het virtuele netwerk voor het beheerde Azure AD DS-beheerde domein te beperken. Er wordt een Azure-standaardloadbalancer gemaakt waarvoor deze regels moeten worden geïnstalleerd. Deze netwerkbeveiligingsgroep beveiligt Azure AD DS en is vereist dat het beheerde domein correct werkt.
    * Als u de netwerkbeveiligingsgroep en vereiste regels wilt maken, selecteert u uw door Azure AD DS beheerde domein in de portal. In het venster **Overzicht** wordt u gevraagd om de netwerkbeveiligingsgroep automatisch te maken en te configureren.
* [Wachtwoordsynchronisatie inschakelen voor Azure AD Domain Services,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eindgebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfsreferenties.

## <a name="next-steps"></a>Volgende stappen

Als u het door Azure AD DS beheerde domein in actie wilt zien, u [een Windows-vm in het domein invoeren,][windows-join] [beveiligde LDAP configureren][tutorial-ldaps]en [wachtwoordhashsynchronisatie configureren.][tutorial-phs]

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
[availability-zones]: ../availability-zones/az-overview.md
