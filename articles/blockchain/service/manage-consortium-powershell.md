---
title: Azure Blockchain Service-consortiumleden beheren - PowerShell
description: Meer informatie over het beheren van Azure Blockchain Service-consortiumleden met Azure PowerShell.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505978"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Consortiumleden beheren in Azure Blockchain Service met PowerShell

U PowerShell gebruiken om leden van blockchain-consortiumleden te beheren voor uw Azure Blockchain-service. Leden met beheerdersbevoegdheden kunnen rollen uitnodigen, toevoegen, verwijderen en wijzigen voor alle deelnemers aan het blockchain-consortium. Leden die gebruikersrechten hebben, kunnen alle deelnemers aan het blockchain-consortium bekijken en de naam van hun ledenweergave wijzigen.

## <a name="prerequisites"></a>Vereisten

* Maak een blockchain-lid met behulp van de [Azure-portal.](create-member.md)
* Zie [Azure Blockchain Service consortium](consortium.md)voor meer informatie over consortia, leden en knooppunten.

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell openen

Azure Cloud Shell is een gratis interactieve shell die u gebruiken om de stappen in dit artikel uit te voeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

U Cloud Shell ook openen op een apart browsertabblad door naar [shell.azure.com/powershell](https://shell.azure.com/powershell). Selecteer **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en selecteer **Enter** om deze uit te voeren.

## <a name="install-the-powershell-module"></a>De PowerShell-module installeren

Installeer het Microsoft.AzureBlockchainService.ConsortiumManagement.PS pakket uit de PowerShell Gallery.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>De informatievoorkeur instellen

U meer informatie krijgen bij het uitvoeren van de cmdlets door de variabele informatievoorkeur in te stellen. Standaard is *$InformationPreference* ingesteld op *Stilgedrag*.

Voor meer uitgebreide informatie uit cmdlets, stelt u de voorkeur in de PowerShell als volgt:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Een Web3-verbinding tot stand brengen

Als u consortiumleden wilt beheren, maakt u een Web3-verbinding met het eindpunt van uw Blockchain-servicelid. U dit script gebruiken om globale variabelen in te stellen voor het aanroepen van de cmdlets voor consortiumbeheer.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Het * \<wachtwoord\> van het ledenaccount* vervangen door het wachtwoord van het ledenaccount dat u hebt gebruikt toen u het lid maakte.

Zoek de andere waarden in de Azure-portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar de pagina Overzicht van uw standaard Blockchain **Service-lid.**

    ![Overzicht van leden](./media/manage-consortium-powershell/member-overview.png)

    Vervang * \<het\> ledenaccount* en * \<het adres RootContract\> * door de waarden van de portal.

1. Selecteer voor het eindpuntadres **Transactieknooppunten**en selecteer vervolgens het **standaardtransactieknooppunt**. Het standaardknooppunt heeft dezelfde naam als het blockchain-lid.
1. Selecteer **Verbindingstekenreeksen**.

    ![Verbindingsreeksen](./media/manage-consortium-powershell/connection-strings.png)

    * \<Eindpuntadres vervangen\> * door de waarde van **HTTPS (Access-toets 1)** of **HTTPS (Access-toets 2)**.

## <a name="manage-the-network-and-smart-contracts"></a>Het netwerk en slimme contracten beheren

Gebruik het netwerk en slimme contractcmdlets om een verbinding tot stand te brengen met de slimme contracten van het blockchain-eindpunt die verantwoordelijk zijn voor consortiumbeheer.

### <a name="import-consortiummanagementcontracts"></a>Import-consortiumManagementContracten

Gebruik deze cmdlet om verbinding te maken met de slimme contracten van het consortiummanagement. Deze contracten worden gebruikt om leden binnen het consortium te beheren en af te dwingen.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| RootContractAddress | Hoofdcontractadres van slimme contracten voor consortiumbeheer | Ja |
| Web3Client | Web3Client-object verkregen uit New-Web3Connection | Ja |

#### <a name="example"></a>Voorbeeld

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Gebruik deze cmdlet om een object te maken om de informatie voor het beheeraccount van een extern knooppunt vast te houden.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| BeheerdAccountAdres | Blockchain-accountaccountadres | Ja |
| ManagedAccountPassword | Wachtwoord voor accountadres | Ja |

#### <a name="example"></a>Voorbeeld

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Nieuwe Web3Connection

Gebruik deze cmdlet om een verbinding tot stand te brengen met het RPC-eindpunt van een transactieknooppunt.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Eindpuntadres voor blockchain-leden | Ja |

#### <a name="example"></a>Voorbeeld

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>De consortiumleden beheren

Gebruik cmdlets voor consortiumleden om leden binnen het consortium te beheren. De beschikbare acties zijn afhankelijk van uw consortiumrol.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Gebruik deze cmdlet om ledengegevens te krijgen of leden van het consortium te vermelden.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| Name | De naam van het Blockchain Service-lid waarover u details wilt ophalen. Wanneer een naam wordt ingevoerd, worden de gegevens van het lid geretourneerd. Wanneer een naam wordt weggelaten, wordt een lijst met alle consortiumleden geretourneerd. | Nee |
| Leden | Leden bezwaar verkregen uit Import-ConsortiumManagementContracts | Ja |
| Web3Client | Web3Client-object verkregen uit New-Web3Connection | Ja |

#### <a name="example"></a>Voorbeeld

[Een Web3-verbinding instellen](#establish-a-web3-connection) om de $ContractConnection variabele in te stellen.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Voorbeelduitvoer

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Gebruik deze cmdlet om een blockchain-lid te verwijderen.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| Name | Naam van het lid te verwijderen | Ja |
| Leden | Leden bezwaar verkregen uit Import-ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account-object verkregen uit Import-Web3Account | Ja |
| Web3Client | Web3Client-object verkregen uit New-Web3Connection | Ja |

#### <a name="example"></a>Voorbeeld

[Een Web3-verbinding tot stand](#establish-a-web3-connection) brengen om de $ContractConnection en $MemberAccount variabelen in te stellen.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Gebruik deze cmdlet om blockchain-ledenkenmerken in te stellen, inclusief de weergavenaam en de rol van het consortium.

Consortiumbeheerders kunnen **DisplayName** en **Rol** instellen voor alle leden. Een consortiumlid met de gebruikersrol kan alleen de weergavenaam van zijn eigen lid wijzigen.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| Name | Naam van het blockchain-lid | Ja |
| DisplayName | Nieuwe weergavenaam | Nee |
| AccountAdres | Accountadres | Nee |
| Leden | Leden bezwaar verkregen uit Import-ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account-object verkregen uit Import-Web3Account | Ja |
| Web3Client |  Web3Client-object verkregen uit New-Web3Connection| Ja |

#### <a name="example"></a>Voorbeeld

[Een Web3-verbinding tot stand](#establish-a-web3-connection) brengen om de $ContractConnection en $MemberAccount variabelen in te stellen.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Uitnodigingen van de consortiumleden beheren

Gebruik de cmdlets voor uitnodigingen voor consortiumleden om uitnodigingen van consortiumleden te beheren. De beschikbare acties zijn afhankelijk van uw consortiumrol.

### <a name="new-blockchainmemberinvitation"></a>Uitnodiging voor nieuw-BlockchainMember

Gebruik deze cmdlet om nieuwe leden uit te nodigen voor het consortium.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-abonnements-ID van het lid om uit te nodigen | Ja |
| Rol | De rol van het consortium. Waarden kunnen ADMIN of GEBRUIKER zijn. ADMIN is de rol van consortiumbeheerder. USER is de rol van consortiumlid. | Ja |
| Leden | Leden bezwaar verkregen uit Import-ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account-object verkregen uit Import-Web3Account | Ja |
| Web3Client | Web3Client-object verkregen uit New-Web3Connection | Ja |

#### <a name="example"></a>Voorbeeld

[Een Web3-verbinding tot stand](#establish-a-web3-connection) brengen om de $ContractConnection en $MemberAccount variabelen in te stellen.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Gebruik deze cmdlet om de uitnodigingsstatus van een consortiumlid op te halen of weer te geven.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| SubscriptionId | De Azure-abonnements-id van het lid om uit te nodigen. Als de abonnements-ID is verstrekt, worden de uitnodigingsgegevens van de abonnements-ID geretourneerd. Als de abonnements-ID wordt weggelaten, wordt een lijst met alle uitnodigingen voor leden geretourneerd. | Nee |
| Leden | Leden bezwaar verkregen uit Import-ConsortiumManagementContracts | Ja |
| Web3Client | Web3Client-object verkregen uit New-Web3Connection | Ja |

#### <a name="example"></a>Voorbeeld

[Een Web3-verbinding instellen](#establish-a-web3-connection) om de $ContractConnection variabele in te stellen.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Voorbeelduitvoer

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Uitnodiging voor verwijderen-blockchainmember

Gebruik deze cmdlet om de uitnodiging van een consortiumlid in te trekken.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-abonnements-id van het lid om in te trekken | Ja |
| Leden | Leden bezwaar verkregen uit Import-ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account-object verkregen uit Import-Web3Account | Ja |
| Web3Client | Web3Client-object verkregen uit New-Web3Connection | Ja |

#### <a name="example"></a>Voorbeeld

[Een Web3-verbinding tot stand](#establish-a-web3-connection) brengen om de $ContractConnection en $MemberAccount variabelen in te stellen.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Uitnodiging voor set-BlockchainMember

Gebruik deze cmdlet om de **rol** voor een bestaande uitnodiging in te stellen. Alleen consortiumbeheerders kunnen uitnodigingen wijzigen.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-abonnements-ID van het lid om uit te nodigen | Ja |
| Rol | Nieuwe consortiumrol voor uitnodiging. Waarden kunnen **GEBRUIKER** of **BEHEERDER**zijn. | Ja |
| Leden |  Leden bezwaar verkregen uit Import-ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account-object verkregen uit Import-Web3Account | Ja |
| Web3Client | Web3Client-object verkregen uit New-Web3Connection | Ja |

#### <a name="example"></a>Voorbeeld

[Een Web3-verbinding tot stand](#establish-a-web3-connection) brengen om de $ContractConnection en $MemberAccount variabelen in te stellen.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Blockchain Service consortium](consortium.md) voor meer informatie over consortia, leden en knooppunten
