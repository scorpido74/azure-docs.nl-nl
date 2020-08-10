---
title: Azure DS Domain Services inschakelen met behulp van PowerShell | Microsoft Docs
description: Leer hoe u Azure Active Directory Domain Services configureert en inschakelt met behulp van Azure AD PowerShell en Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 9c2345c93a163464ea735400c9269e2e3fc27ecf
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488139"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Azure Active Directory Domain Services inschakelen met behulp van PowerShell

Azure AD DS (Active Directory Domain Services) biedt beheerde domeinservices - zoals domeindeelname, groepsbeleid, LDAP, Kerberos/NTLM-verificatie - die volledig compatibel zijn met Windows Server Active Directory. U gebruikt deze domeinservices zonder zelf domeincontrollers te implementeren, te beheren en er patches op toe te passen. Azure AD DS kan met uw bestaande Azure AD-tenant worden geïntegreerd. Met deze integratie kunnen gebruikers zich aanmelden met behulp van hun bedrijfsreferenties, en u kunt bestaande groepen en gebruikersaccounts gebruiken om de toegang tot resources te beveiligen.

Dit artikel laat zien hoe u Azure AD DS kunt inschakelen met behulp van PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om dit artikel te voltooien:

* Installeer en configureer Azure PowerShell.
    * Indien nodig, volgt u de instructies voor [installeren van de Azure PowerShell-module en verbinding maken met uw Azure-abonnement](/powershell/azure/install-az-ps).
    * Zorg ervoor dat u zich bij uw Azure-abonnement aanmeldt met behulp van de [Connect-AzAccount][Connect-AzAccount]-cmdlet.
* Installeer en configureer Azure AD PowerShell.
    * Indien nodig, volgt u de instructies voor het [installeren van de Azure AD PowerShell-module en verbinding maken met Azure AD](/powershell/azure/active-directory/install-adv2).
    * Zorg ervoor dat u zich bij de Azure-tenant aanmeldt met behulp van de [Connect-AzureAD][Connect-AzureAD]-cmdlet.
* U hebt *bevoegdheden van een globale beheerder* voor de Azure AD-tenant nodig om Azure AD DS in te schakelen.
* U hebt bevoegdheden van een *Inzender* voor uw Azure-abonnement nodig om de vereiste Azure AD DS-resources te maken.

## <a name="create-required-azure-ad-resources"></a>Vereiste Azure AD-resources maken

Voor Azure AD DS is een service-principal en een Azure AD-groep vereist. Met deze resources kunnen via het met Azure AD DS beheerde domein gegevens worden gesynchroniseerd, en worden gedefinieerd welke gebruikers beheerdersbevoegdheden hebben in het beheerde domein.

Maak eerst een service-principal in Azure AD voor Azure AD DS voor communicatie en zelf-verificatie. Er wordt een specifieke toepassings-id gebruikt met de naam *Domeincontrollerservices* met de id *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Wijzig deze toepassings-id niet.

Maak een service-principal in Azure AD met behulp van de [New-AzureADServicePrincipal][New-AzureADServicePrincipal]-cmdlet:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Maak nu een Azure AD-groep met de naam *AAD DC-beheerders*. Aan gebruikers die zijn toegevoegd aan deze groep, worden vervolgens machtigingen verleend voor het uitvoeren van beheertaken in het beheerde domein.

Maak de groep *AAD DC-beheerders* met behulp van de [New-AzureADGroup][New-AzureADGroup]-cmdlet:

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Nu de groep *AAD DC-beheerders* is gemaakt, voegt u een gebruiker toe aan de groep met behulp van de cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember]. U haalt eerst de object-id voor de groep *AAD DC Administrators* op met behulp van de [Get-AzureADGroup][Get-AzureADGroup]-cmdlet. Vervolgens haalt u de object-id van de gewenste gebruiker op met behulp van de [Get-AzureADUser][Get-AzureADUser]-cmdlet.

In het volgende voorbeeld ziet u de gebruikersobject-id voor het account met een UPN van `admin@contoso.onmicrosoft.com`. Vervang dit gebruikersaccount door de UPN van de gebruiker die u wilt toevoegen aan de groep *AAD DC Administrators*:

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

Registreer eerst de Azure AD Domain Services-resourceprovider met behulp van de [Register-AzResourceProvider][Register-AzResourceProvider]-cmdlet:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Maak vervolgens een resourcegroep met behulp van de [New-AzResourceGroup][New-AzResourceGroup]-cmdlet. De resourcegroep in het volgende voorbeeld heet *myResourceGroup* en is gemaakt in de regio *westus*. Gebruik uw eigen naam en gewenste regio:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Maak het virtuele netwerk en de subnetten voor Azure AD Domain Services. Er zijn twee subnetten gemaakt: één voor *DomainServices* en één voor *Workloads*. Azure AD DS wordt geïmplementeerd in het toegewezen subnet *DomainServices*. Implementeer geen andere toepassingen of workloads in dit subnet. Gebruik de afzonderlijke *workloads* of andere subnetten voor de rest van de VM's.

Maak de subnetten met behulp van de [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig]-cmdlet. Maak vervolgens het virtuele netwerk met behulp van de [New-AzVirtualNetwork][New-AzVirtualNetwork]-cmdlet.

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

## <a name="create-a-managed-domain"></a>Een beheerd domein maken

Nu gaan we een beheerd domein maken. Stel uw Azure-abonnements-id in en geef vervolgens een naam op voor het beheerde domein, bijvoorbeeld *aaddscontoso.com*. U kunt uw abonnements-id ophalen met behulp van de [Get-AzSubscription][Get-AzSubscription]-cmdlet.

Als u een regio kiest die beschikbaarheidszones ondersteunt, worden de Azure AD DS-resources gedistribueerd over zones voor extra redundantie.

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's.

U hoeft niets te configureren voor Azure AD DS om te worden gedistribueerd over zones. De distributie van resources over zones wordt automatisch afgehandeld op het Azure-platform. Zie [Wat zijn beschikbaarheidszones in Azure?][availability-zones] voor meer informatie en om de beschikbaarheid van regio’s te zien.

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

Het duurt enkele minuten om de resource te maken en het beheer terug te geven aan de PowerShell-prompt. Het inrichten van het beheerde domein wordt op de achtergrond voortgezet. Het kan een uur duren voordat de implementatie is voltooid. Op de **Overzichtspagina** voor uw beheerde domein in de Azure-portal wordt de huidige status weergegeven tijdens deze implementatiefase.

Wanneer in de Azure-portal wordt weergegeven dat inrichten van het beheerde domein gereed is, moeten de volgende taken worden voltooid:

* Werk de DNS-instellingen voor het virtuele netwerk bij, zodat virtuele machines het beheerde domein kunnen vinden voor domeindeelname of verificatie.
    * Selecteer het beheerde domein in de portal om DNS te configureren. In het **Overzichtsvenster** wordt u gevraagd om deze DNS-instellingen automatisch te configureren.
* Maak een netwerkbeveiligingsgroep om het verkeer in het virtuele netwerk voor het beheerde domein te beperken. Er wordt een Standard Load Balancer van Azure gemaakt waarvoor deze regels moeten worden uitgevoerd. Deze netwerkbeveiligingsgroep beveiligt Azure AD DS en is vereist voor een juiste werking van het beheerde domein.
    * Als u de netwerkbeveiligingsgroep en de vereiste regels wilt maken, installeert u eerst het `New-AzureAddsNetworkSecurityGroup`-script met de opdracht `Install-Script -Name New-AaddsNetworkSecurityGroup` en voert u vervolgens `New-AaddsNetworkSecurityGroup`uit. De vereiste regels voor het beheerde domein worden voor u gemaakt.
* U moet [Wachtwoordsynchronisatie in Azure AD DS inschakelen](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eindgebruikers zich bij het beheerde domein kunnen aanmelden met hun bedrijfsreferenties.

## <a name="complete-powershell-script"></a>PowerShell-script voltooien

In het volgende voltooide PowerShell-script worden alle taken gecombineerd die in dit artikel worden weergegeven. Kopieer het script en sla het op in een bestand met de extensie `.ps1`. Voer het script uit in een lokale PowerShell-console of in [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Als u Azure AD DS wilt inschakelen, moet u een globale beheerder zijn voor de Azure AD-tenant. U hebt ook minstens de bevoegdheden van een *Inzender* nodig voor het Azure-abonnement.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
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

Het duurt enkele minuten om de resource te maken en het beheer terug te geven aan de PowerShell-prompt. Het inrichten van het beheerde domein wordt op de achtergrond voortgezet. Het kan een uur duren voordat de implementatie is voltooid. Op de **Overzichtspagina** voor uw beheerde domein in de Azure-portal wordt de huidige status weergegeven tijdens deze implementatiefase.

Wanneer in de Azure-portal wordt weergegeven dat inrichten van het beheerde domein gereed is, moeten de volgende taken worden voltooid:

* Werk de DNS-instellingen voor het virtuele netwerk bij, zodat virtuele machines het beheerde domein kunnen vinden voor domeindeelname of verificatie.
    * Selecteer het beheerde domein in de portal om DNS te configureren. In het **Overzichtsvenster** wordt u gevraagd om deze DNS-instellingen automatisch te configureren.
* Maak een netwerkbeveiligingsgroep om het verkeer in het virtuele netwerk voor het beheerde domein te beperken. Er wordt een Standard Load Balancer van Azure gemaakt waarvoor deze regels moeten worden uitgevoerd. Deze netwerkbeveiligingsgroep beveiligt Azure AD DS en is vereist voor een juiste werking van het beheerde domein.
    * Als u de netwerkbeveiligingsgroep en de vereiste regels wilt maken, installeert u eerst het `New-AzureAddsNetworkSecurityGroup`-script met de opdracht `Install-Script -Name New-AaddsNetworkSecurityGroup` en voert u vervolgens `New-AaddsNetworkSecurityGroup`uit. De vereiste regels voor het beheerde domein worden voor u gemaakt.
* U moet [Wachtwoordsynchronisatie in Azure AD DS inschakelen](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eindgebruikers zich bij het beheerde domein kunnen aanmelden met hun bedrijfsreferenties.

## <a name="next-steps"></a>Volgende stappen

Als u het beheerde domein in actie wilt zien, kunt u [een Windows-VM koppelen][windows-join], [beveiligde LDAP configureren][tutorial-ldaps] en [wachtwoordhashsynchronisatie configureren][tutorial-phs].

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
