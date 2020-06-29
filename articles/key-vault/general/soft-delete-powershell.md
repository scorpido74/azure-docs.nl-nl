---
title: 'Azure Key Vault: voorlopig verwijderen gebruiken met PowerShell'
description: Gebruiksvoorbeelden van voorlopig verwijderen met PowerShell-codefragmenten
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: f883b8527fff97ea3e16e7ffa7637c432dc33c2f
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783347"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>De Key Vault-functie voor voorlopig verwijderen gebruiken met PowerShell

Met de functie voor voorlopig verwijderen van Azure Key Vault kunt u verwijderde kluizen en kluisobjecten herstellen. Met name in de volgende scenario's is voorlopig verwijderen geschikt:

- Ondersteuning voor herstelbare verwijdering van een sleutelkluis
- Ondersteuning voor herstelbare verwijdering van sleutelkluisobjecten: sleutels, geheimen en certificaten

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 of hoger: als u dit nog niet hebt geïnstalleerd, installeert u Azure PowerShell en koppelt u het aan uw Azure-abonnement. Zie [Azure PowerShell installeren en configureren](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Er bestaat een verouderde versie van het PowerShell-uitvoerindelingsbestand voor Key Vault dat **mogelijk** in uw omgeving kan worden geladen in plaats van de juiste versie. We wachten op een bijgewerkte versie van PowerShell die de benodigde correctie voor de uitvoerindeling bevat en we zullen dit onderwerp op dat moment bijwerken. De huidige tijdelijke oplossing als dit probleem zich voordoet is:
> - Gebruik de volgende query als de eigenschap voor voorlopig verwijderen niet ziet die in dit onderwerp wordt beschreven: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Zie de [PowerShell-documentatie van Azure Key Vault](/powershell/module/az.keyvault) voor Key Vault-specifieke informatie voor PowerShell.

## <a name="required-permissions"></a>Vereiste machtigingen

Key Vault-bewerkingen worden als volgt afzonderlijk beheerd via machtigingen op basis van op rollen gebaseerd toegangsbeheer:

| Bewerking | Beschrijving | Gebruikersmachtiging |
|:--|:--|:--|
|Lijst|Geeft de verwijderde sleutelkluizen weer.|Microsoft.KeyVault/deletedVaults/read|
|Herstellen|Herstelt een verwijderde sleutelkluis.|Microsoft.KeyVault/vaults/write|
|Opschonen|Verwijdert permanent een verwijderde sleutelkluis en alle bijbehorende inhoud.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Zie [Uw Key Vault beveiligen](secure-your-key-vault.md) voor meer informatie over machtigingen en toegangsbeheer.

## <a name="enabling-soft-delete"></a>Voorlopig verwijderen inschakelen

U schakelt 'voorlopig verwijderen' in om herstellen van een verwijderde sleutelkluis of objecten die zijn opgeslagen in een sleutelkluis, toe te staan.

> [!IMPORTANT]
> Het inschakelen van 'zacht verwijderen' bij een sleutel kluis kan niet worden teruggedraaid. Zodra de eigenschap voorlopig verwijderen is ingesteld op 'waar', kan deze niet worden gewijzigd of verwijderd.  

### <a name="existing-key-vault"></a>Bestaande sleutelkluis

Voor een bestaande sleutelkluis met de naam ContosoVault schakelt u voorlopig verwijderen als volgt in. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nieuwe sleutelkluis

Voorlopig verwijderen kan voor een nieuwe sleutelkluis worden ingeschakeld tijdens de aanmaak door de vlag voor voorlopig verwijderen toe te voegen aan de maakopdracht.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Activering van voorlopig verwijderen controleren

Als u wilt controleren of voor een sleutelkluis zacht verwijderen is ingeschakeld, voert u de opdracht *weergeven* uit en zoekt u naar 'voorlopig verwijderen ingeschakeld?' kenmerk:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Een met voorlopig verwijderen beveiligde sleutelkluis verwijderen

Hoe de opdracht voor het verwijderen van een sleutelkluis wordt uitgevoerd, is afhankelijk van of voorlopig verwijderen is ingeschakeld.

> [!IMPORTANT]
>Als u de volgende opdracht uitvoert voor een sleutelkluis waarvoor voorlopig verwijderen niet is ingeschakeld, verwijdert u deze sleutelkluis en alle inhoud permanent, zonder dat u deze kunt herstellen.

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hoe voorlopig verwijderen uw sleutelkluizen beschermt

Met voorlopig verwijderen ingeschakeld:

- Een verwijderde sleutelkluis wordt verwijderd uit de resourcegroep en in een gereserveerde naamruimte geplaatst die is gekoppeld aan de locatie waar deze is gemaakt. 
- Verwijderde objecten, zoals sleutels, geheimen en certificaten, zijn niet toegankelijk als de bijbehorende sleutelkluis de status verwijderd heeft. 
- De DNS-naam voor een verwijderde sleutelkluis is gereserveerd, waardoor wordt voorkomen dat er een nieuwe sleutelkluis met dezelfde naam wordt gemaakt.  

U kunt de sleutelkluizen die zijn gekoppeld aan uw abonnement met de status verwijderd weergeven met behulp van de volgende opdracht:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *Id* kan worden gebruikt om de resource te identificeren bij het herstellen of opschonen. 
- *Resource-id* is de oorspronkelijke resource-id van deze kluis. Omdat deze sleutelkluis nu de status verwijderd heeft, bestaat er geen bron met die resource-id. 
- *Geplande opschoondatum* is wanneer de kluis permanent wordt verwijderd als er geen actie wordt ondernomen. De standaard bewaartermijn, die wordt gebruikt voor het berekenen van de *Geplande opschoondatum*, is 90 dagen.

## <a name="recovering-a-key-vault"></a>Een sleutelkluis herstellen

Als u een sleutelkluis wilt herstellen, geeft u de naam van de sleutelkluis, de resourcegroep en de locatie op. Noteer de locatie en de resourcegroep van de verwijderde sleutelkluis, aangezien u ze nodig hebt voor het herstelproces.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Wanneer een sleutelkluis wordt hersteld, wordt een nieuwe resource gemaakt met de oorspronkelijke resource-id van de sleutelkluis. Als de oorspronkelijke resourcegroep is verwijderd, moet er een resourcegroep met dezelfde naam worden gemaakt voordat u probeert te herstellen.

## <a name="deleting-and-purging-key-vault-objects"></a>Sleutelkluisobjecten verwijderen en opschonen

Met de volgende opdracht wordt de sleutel ContosoFirstKey verwijderd uit een sleutelkluis met de naam ContosoVault, waarvoor voorlopig verwijderen is ingeschakeld:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Als voorlopig verwijderen is ingeschakeld voor uw sleutelkluis, lijkt een verwijderde sleutel nog steeds te zijn verwijderd, tenzij u expliciet verwijderde sleutels weergeeft. De meeste bewerkingen met een sleutel met de status verwijderd mislukken, behalve het weergeven, herstellen en opschonen van een verwijderde sleutel. 

Met de volgende opdracht worden bijvoorbeeld Verwijderde sleutels in de sleutelkluis ContosoVault weergegeven:

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Overgangsstatus 

Wanneer u een sleutel uit een sleutelkluis verwijdert waarvoor voorlopig verwijderen is ingeschakeld, kan het enkele seconden duren voordat de overgang is voltooid. Tijdens deze overgang kan het lijken of de sleutel niet de status actief of verwijderd heeft. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Voorlopig verwijderen gebruiken met sleutelkluisobjecten

Net als sleutelkluizen, behouden verwijderde sleutels, geheimen of certificaten tot 90 dagen de status verwijderd, tenzij u ze herstelt of opschoont. 

#### <a name="keys"></a>Sleutels

Een voorlopig verwijderde sleutel herstellen:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Een voorlopig verwijderde sleutel permanent verwijderen (ook wel opschonen genoemd):

> [!IMPORTANT]
> Als u een sleutel opschoont, wordt deze definitief verwijderd en kan deze niet worden hersteld. 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

Aan de acties **herstellen** en **opschonen** zijn aparte machtigingen gekoppeld in het toegangsbeleid van een sleutelkluis. Een gebruiker of service-principal kan alleen de actie **herstellen** of **opschonen** uitvoeren als die beschikt over de juiste machtiging voor de sleutel of het geheim. **Opschonen** wordt niet standaard toegevoegd aan het toegangsbeleid van een sleutelkluis wanneer de snelkoppeling 'alle' wordt gebruikt om alle machtigingen te verlenen. U moet de machtiging voor **opschonen** specifiek toekennen. 

#### <a name="set-a-key-vault-access-policy"></a>Een toegangsbeleid voor een sleutelkluis instellen

Met de volgende opdracht wordt toestemming verleend aan user@contoso.com om verschillende bewerkingen voor sleutels te gebruiken in *ContosoVault*, waaronder **opschonen**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Als u een bestaande sleutelkluis hebt waarvoor voorlopig verwijderen zojuist is ingeschakeld, beschikt u mogelijk niet over de machtigingen voor **herstellen** en **opschonen**.

#### <a name="secrets"></a>Geheimen

Net als sleutels worden geheimen beheerd met hun eigen opdrachten:

- Een geheim met de naam SQLPassword verwijderen: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Alle verwijderde geheimen in een sleutelkluis weergeven: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Een geheim met de status verwijderd herstellen: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Een geheim met de status verwijderd opschonen: 

  > [!IMPORTANT]
  > Als u een geheim opschoont, wordt het permanent verwijderd en kan het niet worden hersteld.

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certificaten

U kunt certificaten beheren aan de hand van de onderstaande opdrachten:

- Een certificaat met de naam SQLPassword verwijderen: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Alle verwijderde certificaten in een sleutelkluis weergeven: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Een certificaat met de status verwijderd herstellen: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Een certificaat met de status verwijderd opschonen: 

  > [!IMPORTANT]
  > Als u een certificaat opschoont, wordt het permanent verwijderd en kan het niet worden hersteld.

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>Een met voorlopig verwijderen beveiligde sleutelkluis opschonen

> [!IMPORTANT]
> Als u een sleutelkluis of een van de objecten erin opschoont, worden ze definitief verwijderd, wat betekent dat ze niet kunnen worden hersteld.

De functie opschonen wordt gebruikt voor het permanent verwijderen van een sleutelkluisobject of een volledige sleutelkluis die eerder voorlopig is verwijderd. Zoals in de vorige sectie is gedemonstreerd, kunnen objecten die zijn opgeslagen in een sleutelkluis waarvoor de functie voorlopig verwijderen is ingeschakeld, meerdere statussen doorlopen:
- **Actief**: vóór verwijdering.
- **Voorlopig verwijderd**: na verwijdering, kan worden weergegeven en teruggezet naar de actieve status.
- **Permanent verwijderd**: na leegmaken, kan niet worden hersteld.


Dit geldt ook voor de sleutelkluis. Als u een voorlopig verwijderde sleutelkluis en de inhoud ervan permanent wilt verwijderen, moet u de sleutelkluis zelf opschonen.

### <a name="purging-a-key-vault"></a>Een sleutelkluis opschonen

Wanneer een sleutelkluis wordt opgeschoond, wordt de volledige inhoud permanent verwijderd, inclusief de sleutels, geheimen en certificaten. Als u een voorlopig verwijderde sleutelkluis wilt opschonen, gebruikt u de opdracht `Remove-AzKeyVault` met de optie `-InRemovedState` en geeft u de locatie van de verwijderde sleutelkluis op met het argument `-Location location`. U kunt de locatie van een verwijderde kluis vinden met behulp van de opdracht `Get-AzKeyVault -InRemovedState`.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Machtigingen voor leegmaken vereist
- Als u een verwijderde sleutelkluis wilt opschonen, moet de gebruiker machtigingen voor op rollen gebaseerde toegangsbeheer hebben voor de bewerking *Microsoft.KeyVault/locations/deletedVaults/purge/action*. 
- Om een verwijderde sleutel kluis weer te geven, moet de gebruiker machtigingen voor op rollen gebaseerd toegangsbeheer hebben voor de bewerking *Microsoft.KeyVault/deletedVaults/read*. 
- Standaard heeft alleen een abonnementsbeheerder deze machtigingen. 

### <a name="scheduled-purge"></a>Gepland opschonen

Als u verwijderde sleutelkluisobjecten weergeeft, kunt u ook zien wanneer ze zijn gepland om te worden opgeschoond door Key Vault. *Geplande opschoondatum* geeft aan wanneer een sleutelkluisobject definitief wordt verwijderd als er geen actie wordt ondernomen. De bewaartermijn voor een verwijderd sleutelkluisobject is standaard 90 dagen.

>[!IMPORTANT]
>Een opgeschoond kluisobject dat wordt geactiveerd door het veld *Geplande opschoondatum*, wordt permanent verwijderd. Het kan niet worden hersteld.

## <a name="enabling-purge-protection"></a>Beveiliging tegen opschonen inschakelen

Als beveiliging tegen opschonen is ingeschakeld, kan een kluis of een object met de status verwijderd pas worden opgeschoond als de bewaartermijn van 90 dagen is verstreken. Een dergelijke kluis of dergelijk object kan nog steeds worden hersteld. Deze functie biedt een extra zekerheid dat een kluis of een object nooit permanent kan worden verwijderd totdat de bewaartermijn is verstreken. De standaard bewaartermijn is 90 dagen, maar tijdens het maken van de sleutelkluis is het mogelijk om het interval voor het bewaarbeleid in te stellen op een waarde van 7 tot 90 dagen. Het bewaarbeleid voor de beveiliging tegen opschonen gebruikt hetzelfde interval. Zodra het is ingesteld, kan het interval voor het bewaarbeleid niet worden gewijzigd.

U kunt de beveiliging tegen opschonen alleen inschakelen als de functie voor voorlopig verwijderen ook is ingeschakeld. 

Gebruik de cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) tijdens het maken van een kluis om zowel voorlopig verwijderen als beveiliging tegen opschonen in te schakelen:

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Gebruik de cmdlets [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0) en [Set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) om beveiliging tegen opschonen toe te voegen aan een bestaande kluis (waarvoor al voorlopig verwijderen is ingeschakeld):

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Meer informatie

- Zie [Azure Key Vault: overzicht van voorlopig verwijderen](overview-soft-delete.md) voor een overzicht van de functie voor voorlopig verwijderen van Key Vault.
- Zie [Wat is Azure Key Vault?](overview.md) voor een algemeen overzicht van het gebruik van Azure Key Vault.
