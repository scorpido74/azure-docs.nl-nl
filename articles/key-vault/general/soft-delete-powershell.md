---
title: Azure Key Vault-het gebruik van voorlopig verwijderen met Power shell
description: Voorbeeld voorbeelden van het gebruik van zacht verwijderen met Power shell-code knipsels
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 79d8cb4b09ef547bf1c0b01f48872ddcb4f964ee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81616546"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>De Key Vault-functie voor voorlopig verwijderen gebruiken met PowerShell

Met de functie voor zacht verwijderen van Azure Key Vault kunt u verwijderde kluizen en kluis objecten herstellen. Met name de volgende scenario's worden door zacht verwijderen opgelost:

- Ondersteuning voor herstel bare verwijdering van een sleutel kluis
- Ondersteuning voor herstel bare verwijdering van sleutel kluis-objecten; sleutels, geheimen en certificaten

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 of hoger: als u dit al hebt ingesteld, installeert u Azure PowerShell en koppelt u dit aan uw Azure-abonnement. Zie [Azure PowerShell installeren en configureren voor meer informatie](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Er is een verouderde versie van het Key Vault Power shell-uitvoer indelings bestand dat in uw omgeving **kan** worden geladen in plaats van de juiste versie. We anticiperen een bijgewerkte versie van Power shell om de benodigde correctie voor de uitvoer indeling te bevatten en zullen dit onderwerp op dat moment bijwerken. De huidige tijdelijke oplossing: als dit probleem zich voordoet, kunt u het volgende doen.
> - Gebruik de volgende query als u merkt dat u de eigenschap voorlopig verwijderen is ingeschakeld in dit onderwerp niet ziet: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Zie [Azure Key Vault Power shell Reference](/powershell/module/az.keyvault)(Engelstalig) voor Key Vault specifieke Naslag informatie voor Power shell.

## <a name="required-permissions"></a>Vereiste machtigingen

Key Vault bewerkingen worden als volgt afzonderlijk beheerd via machtigingen op basis van op rollen gebaseerde toegangs beheer (RBAC):

| Bewerking | Beschrijving | Gebruikers machtiging |
|:--|:--|:--|
|Lijst|Een lijst met verwijderde sleutel kluizen.|Micro soft. deletedVaults/lezen|
|Herstellen|Hiermee herstelt u een verwijderde sleutel kluis.|Micro soft. de sleutel kluis/kluizen/schrijven|
|Opschonen|Verwijdert permanent een verwijderde sleutel kluis en alle bijbehorende inhoud.|Micro soft. sleutel kluis/locaties/deletedVaults/opschonen/actie|

Zie [uw sleutel kluis beveiligen](secure-your-key-vault.md)voor meer informatie over machtigingen en toegangs beheer.

## <a name="enabling-soft-delete"></a>Voorlopig verwijderen inschakelen

U schakelt ' voorlopig verwijderen ' in om het herstellen van een verwijderde sleutel kluis of objecten die zijn opgeslagen in een sleutel kluis, toe te staan.

> [!IMPORTANT]
> Het inschakelen van ' zacht verwijderen ' op een sleutel kluis is een onomkeerbaare actie. Zodra de eigenschap soft-delete is ingesteld op ' True ', kan deze niet worden gewijzigd of verwijderd.  

### <a name="existing-key-vault"></a>Bestaande sleutel kluis

Voor een bestaande sleutel kluis met de naam ContosoVault, schakelt u voorlopig verwijderen als volgt in. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nieuwe sleutel kluis

Het inschakelen van het voorlopig verwijderen van een nieuwe sleutel kluis wordt uitgevoerd tijdens de aanmaak tijd door de vlag voor zacht verwijderen toevoegen toe te voegen aan de opdracht Create.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Activering van voorlopig verwijderen controleren

Als u wilt controleren of een sleutel kluis voorlopig is ingeschakeld, voert u de opdracht *weer geven* uit en zoekt u naar ' voorlopig verwijderen ingeschakeld? ' geschreven

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Een met Soft verwijderde beveiligde sleutel kluis verwijderen

De opdracht voor het verwijderen van wijzigingen in de sleutel kluis, afhankelijk van het feit of het voorlopig verwijderen is ingeschakeld.

> [!IMPORTANT]
>Als u de volgende opdracht uitvoert voor een sleutel kluis waarvoor geen tijdelijke verwijdering is ingeschakeld, verwijdert u deze sleutel kluis en alle inhoud zonder opties voor herstel.

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hoe soft-delete uw sleutel kluizen beschermt

Met voorlopig verwijderen ingeschakeld:

- Een verwijderde sleutel kluis wordt verwijderd uit de resource groep en in een gereserveerde naam ruimte geplaatst, die is gekoppeld aan de locatie waar deze is gemaakt. 
- Verwijderde objecten, zoals sleutels, geheimen en certificaten, zijn niet toegankelijk als de sleutel kluis de status verwijderd heeft. 
- De DNS-naam voor een verwijderde sleutel kluis is gereserveerd, waardoor wordt voor komen dat er een nieuwe sleutel kluis met dezelfde naam wordt gemaakt.  

U kunt de sleutel kluizen voor verwijderde status weer geven die zijn gekoppeld aan uw abonnement met behulp van de volgende opdracht:

```powershell
Get-AzKeyVault -InRemovedState 
```

- De *id* kan worden gebruikt om de resource te identificeren bij het herstellen of verwijderen. 
- De *resource-id* is de oorspronkelijke resource-id van deze kluis. Omdat deze sleutel kluis nu een verwijderde status heeft, bestaat er geen resource met die resource-ID. 
- De *geplande opschoon datum* is wanneer de kluis permanent wordt verwijderd als er geen actie wordt ondernomen. De standaard retentie periode, die wordt gebruikt om de *geplande opschoon datum*te berekenen, is 90 dagen.

## <a name="recovering-a-key-vault"></a>Een sleutel kluis herstellen

Als u een sleutel kluis wilt herstellen, geeft u de naam van de sleutel kluis, de resource groep en de locatie op. Let op de locatie en de resource groep van de verwijderde sleutel kluis, wanneer u deze nodig hebt voor het herstel proces.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Wanneer een sleutel kluis wordt hersteld, wordt een nieuwe resource gemaakt met de oorspronkelijke resource-ID van de sleutel kluis. Als de oorspronkelijke resource groep is verwijderd, moet er een worden gemaakt met dezelfde naam voordat u probeert te herstellen.

## <a name="deleting-and-purging-key-vault-objects"></a>Sleutel kluis objecten verwijderen en verwijderen

Met de volgende opdracht wordt de sleutel ' ContosoFirstKey ' verwijderd in een sleutel kluis met de naam ' ContosoVault ', waarvoor het voorlopig verwijderen is ingeschakeld:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Als uw sleutel kluis is ingeschakeld voor zacht verwijderen, wordt er nog steeds een verwijderde sleutel verwijderd, tenzij u expliciet Verwijderde sleutels vermeldt. De meeste bewerkingen met een sleutel in de verwijderde status mislukken, behalve voor het weer geven van een lijst, het herstellen van een verwijderde sleutel. 

Met de volgende opdracht worden bijvoorbeeld Verwijderde sleutels in de sleutel kluis ' ContosoVault ' weer gegeven:

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Overgangs status 

Wanneer u een sleutel in een sleutel kluis verwijdert waarvoor het uitvoeren van zacht verwijderen is ingeschakeld, kan het enkele seconden duren voordat de overgang is voltooid. Tijdens deze overgang kan het lijken of de sleutel niet de status actief of verwijderd heeft. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Voorlopig verwijderen gebruiken met Key kluis objecten

Net als bij sleutel kluizen, behoudt een verwijderde sleutel, geheim of certificaat, de status verwijderd tot 90 dagen, tenzij u deze herstelt of opschoont. 

#### <a name="keys"></a>Sleutels

Een voorlopig verwijderde sleutel herstellen:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Een voorlopig verwijderde sleutel permanent verwijderen (ook wel het verwijderen genoemd):

> [!IMPORTANT]
> Als u een sleutel verwijdert, wordt deze definitief verwijderd en kan deze niet worden hersteld. 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

De **herstel** -en **opschoon** acties hebben hun eigen machtigingen die zijn gekoppeld aan een sleutel kluis toegangs beleid. Als een gebruiker of service-principal een **herstel** -of **opschoon** actie kan uitvoeren, moeten ze beschikken over de juiste machtiging voor die sleutel of dit geheim. Standaard wordt **leegmaken** niet toegevoegd aan het toegangs beleid van een sleutel kluis wanneer de snelkoppeling ' alle ' wordt gebruikt om alle machtigingen te verlenen. U moet de machtiging **verwijderen** specifiek verlenen. 

#### <a name="set-a-key-vault-access-policy"></a>Een sleutel kluis toegangs beleid instellen

Met de volgende opdracht user@contoso.com wordt een machtiging verleend voor het gebruik van verschillende bewerkingen op sleutels in *ContosoVault* , waaronder **opschonen**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Als u een bestaande sleutel kluis hebt die zojuist is ingeschakeld, hebt u mogelijk geen machtigingen voor **herstellen** en **verwijderen** .

#### <a name="secrets"></a>Geheimen

Net als sleutels worden geheimen beheerd met hun eigen opdrachten:

- Verwijder een geheim met de naam SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Alle verwijderde geheimen in een sleutel kluis weer geven: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Een geheim herstellen met de status verwijderd: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Verwijder een geheim met de status verwijderd: 

  > [!IMPORTANT]
  > Als u een geheim verwijdert, wordt dit permanent verwijderd en kan het niet worden hersteld.

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certificaten

U kunt certificaten beheren aan de hand van de onderstaande opdrachten:

- Een certificaat met de naam SQLPassword verwijderen: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Alle verwijderde certificaten in een sleutel kluis weer geven: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Herstellen van een certificaat met de status verwijderd: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Een certificaat leegmaken met de status verwijderd: 

  > [!IMPORTANT]
  > Als u een certificaat verwijdert, wordt dit definitief verwijderd en kan het niet worden hersteld.

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>Een tijdelijke beveiligde sleutel kluis verwijderen

> [!IMPORTANT]
> Als u een sleutel kluis of een van de objecten erin opschoont, wordt deze definitief verwijderd, wat betekent dat het niet kan worden hersteld.

De functie opschonen wordt gebruikt voor het permanent verwijderen van een sleutel kluis-object of een volledige sleutel kluis die voorheen zacht werd verwijderd. Zoals u in de vorige sectie hebt gedemonstreerd, kunnen objecten die zijn opgeslagen in een sleutel kluis met de functie voor het voorlopig verwijderen ingeschakeld, meerdere statussen door lopen:
- **Actief**: vóór verwijdering.
- **Zacht verwijderd**: na verwijdering kan deze worden weer gegeven en teruggezet naar de actieve status.
- **Permanent verwijderd**: na leegmaken, kan niet worden hersteld.


Dit geldt ook voor de sleutel kluis. Als u een voorlopig verwijderde sleutel kluis en de inhoud ervan permanent wilt verwijderen, moet u de sleutel kluis zelf leegmaken.

### <a name="purging-a-key-vault"></a>Een sleutel kluis verwijderen

Wanneer een sleutel kluis wordt leeg gemaakt, wordt de volledige inhoud definitief verwijderd, inclusief de sleutels, geheimen en certificaten. Als u een voorlopig verwijderde sleutel kluis wilt verwijderen, gebruikt u `Remove-AzKeyVault` de opdracht met de `-InRemovedState` optie en geeft u de locatie van de verwijderde sleutel kluis met `-Location location` het argument op. U kunt de locatie van een verwijderde kluis vinden met behulp `Get-AzKeyVault -InRemovedState`van de opdracht.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Machtigingen voor leegmaken vereist
- Als u een verwijderde sleutel kluis wilt verwijderen, moet de gebruiker RBAC-machtigingen hebben voor de bewerking *micro soft. sleutel kluis/locaties/deletedVaults/verwijderen/actie* . 
- Om een verwijderde sleutel kluis weer te geven, moet de gebruiker RBAC-machtigingen hebben voor de *micro soft. deletedVaults/Read-* bewerking. 
- Standaard heeft alleen een abonnements beheerder deze machtigingen. 

### <a name="scheduled-purge"></a>Gepland opschonen

Bij het vermelden van verwijderde sleutel kluis objecten wordt ook weer gegeven wanneer deze zijn gepland om te worden verwijderd door Key Vault. *Geplande opschoon datum* geeft aan wanneer een sleutel kluis object definitief wordt verwijderd als er geen actie wordt ondernomen. De Bewaar periode voor een verwijderd sleutel kluis object is standaard 90 dagen.

>[!IMPORTANT]
>Een verwijderd kluis object, geactiveerd door het veld *geplande opschoon datum* , wordt permanent verwijderd. Deze kan niet worden hersteld.

## <a name="enabling-purge-protection"></a>Leegmaken van beveiliging inschakelen

Wanneer het leegmaken van de beveiliging is ingeschakeld, kan een kluis of een object in de status verwijderd pas worden verwijderd als de retentie periode van 90 dagen is verstreken. Een dergelijke kluis of dit object kan nog steeds worden hersteld. Deze functie biedt een extra zekerheid dat een kluis of een object nooit permanent kan worden verwijderd totdat de Bewaar periode is verstreken.

U kunt het leegmaken van de beveiliging alleen inschakelen als de functie voor voorlopig verwijderen ook is ingeschakeld. 

Gebruik de cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) om de beveiliging bij het maken van een kluis van zacht verwijderen en leegmaken te wissen:

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Gebruik de cmdlets [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)en [set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) om een schone beveiliging toe te voegen aan een bestaande kluis (waarvoor al zacht verwijderen is ingeschakeld):

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Meer informatie

- Zie [Azure Key Vault overzicht van tijdelijke verwijdering](overview-soft-delete.md)voor een overzicht van de functie voor voorlopig verwijderen van Key Vault).
- Zie [Wat is Azure Key Vault?](overview.md)) voor een algemeen overzicht van Azure Key Vault gebruik.
