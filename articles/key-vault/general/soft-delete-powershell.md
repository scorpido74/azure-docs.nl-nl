---
title: Azure Key Vault - Soft-delete gebruiken met PowerShell
description: Voorbeelden van hoofdletters gebruiken voor soft-delete met PowerShell-codeknipsels
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 79d8cb4b09ef547bf1c0b01f48872ddcb4f964ee
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616546"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>De Key Vault-functie voor voorlopig verwijderen gebruiken met PowerShell

De soft-delete-functie van Azure Key Vault maakt het mogelijk om verwijderde kluizen en kluisobjecten te herstellen. Met name soft-delete behandelt de volgende scenario's:

- Ondersteuning voor herstelbare verwijdering van een sleutelkluis
- Ondersteuning voor herstelbare verwijdering van belangrijke vault-objecten; sleutels, geheimen en certificaten

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 of hoger - Als u dit nog niet hebt ingesteld, installeert u Azure PowerShell en koppelt u het aan uw Azure-abonnement, zie [Azure PowerShell installeren en configureren.](https://docs.microsoft.com/powershell/azure/overview) 

>[!NOTE]
> Er is een verouderde versie van ons Key Vault PowerShell-uitvoeropmaakbestand dat in uw omgeving **kan** worden geladen in plaats van de juiste versie. We verwachten een bijgewerkte versie van PowerShell om de benodigde correctie voor de uitvoeropmaak te bevatten en zullen dit onderwerp op dat moment bijwerken. De huidige tijdelijke oplossing, mocht u dit opmaakprobleem tegenkomen, is:
> - Gebruik de volgende query als u merkt dat u de eigenschap soft-delete die in dit onderwerp is beschreven, niet ziet: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Zie [Azure Key Vault PowerShell-referentie](/powershell/module/az.keyvault)voor key vault-specifieke referentiegegevens voor PowerShell.

## <a name="required-permissions"></a>Vereiste machtigingen

Key Vault-bewerkingen worden afzonderlijk beheerd via RBAC-machtigingen (Role-based access control):

| Bewerking | Beschrijving | Gebruikersmachtigingen |
|:--|:--|:--|
|Lijst|Hiermee worden verwijderde sleutelkluizen weergegeven.|Microsoft.KeyVault/deletedVaults/read|
|Herstellen|Hiermee herstelt u een verwijderd sleutelkluis.|Microsoft.KeyVault/kluizen/schrijven|
|Opschonen|Hiermee verwijdert u permanent een verwijderde sleutelkluis en alle inhoud ervan.|Microsoft.KeyVault/locaties/verwijderdeVaults/purge/action|

Zie [Uw sleutelkluis beveiligen](secure-your-key-vault.md)voor meer informatie over machtigingen en toegangscontrole).

## <a name="enabling-soft-delete"></a>Soft-delete inschakelen

U schakelt 'soft-delete' in om herstel van een verwijderde sleutelkluis of objecten die zijn opgeslagen in een sleutelkluis mogelijk te maken.

> [!IMPORTANT]
> Het inschakelen van 'soft-delete' op een key vault is een onomkeerbare actie. Zodra de eigenschap soft-delete is ingesteld op 'true', kan deze niet meer worden gewijzigd of verwijderd.  

### <a name="existing-key-vault"></a>Bestaande sleutelkluis

Schakel soft-delete als volgt in voor een bestaande sleutelkluis met de naam ContosoVault. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nieuwe sleutelkluis

Het inschakelen van soft-delete voor een nieuwe sleutelkluis gebeurt tijdens het maken door de soft-delete-vlag toe te voegen aan de opdracht Maken.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Soft-delete-enablement verifiëren

Als u wilt controleren of een sleutelkluis is ingeschakeld voor soft-delete, voert u de opdracht *Show uit* en zoekt u naar de 'Soft Delete Enabled?' Kenmerk:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Een beveiligde sleutelkluis verwijderen

De opdracht om een sleutelkluis te verwijderen, verandert in gedrag, afhankelijk van of soft-delete is ingeschakeld.

> [!IMPORTANT]
>Als u de volgende opdracht uitvoert voor een sleutelkluis die geen soft-delete is ingeschakeld, verwijdert u deze sleutelkluis en alle inhoud ervan permanent zonder herstelopties!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hoe soft-delete uw sleutelkluizen beschermt

Met soft-delete ingeschakeld:

- Een verwijderd sleutelkluis wordt uit de brongroep verwijderd en in een gereserveerde naamruimte geplaatst, gekoppeld aan de locatie waar het is gemaakt. 
- Verwijderde objecten zoals sleutels, geheimen en certificaten zijn ontoegankelijk zolang de sleutelkluis zich in de verwijderde status bevindt. 
- De DNS-naam voor een verwijderde sleutelkluis is gereserveerd, zodat een nieuwe sleutelkluis met dezelfde naam niet wordt gemaakt.  

U verwijderde statussleutelkluizen, die aan uw abonnement zijn gekoppeld, bekijken met behulp van de volgende opdracht:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *ID* kan worden gebruikt om de bron te identificeren bij het herstellen of zuiveren. 
- *Resource-ID* is de oorspronkelijke bron-ID van deze kluis. Aangezien deze sleutelkluis zich nu in een verwijderde status bevindt, bestaat er geen resource met die resource-id. 
- *Geplande zuiveringsdatum* is wanneer de kluis permanent wordt verwijderd als er geen actie wordt ondernomen. De standaardbewaarperiode, die wordt gebruikt om de *geplande zuiveringsdatum*te berekenen, is 90 dagen.

## <a name="recovering-a-key-vault"></a>Een sleutelkluis herstellen

Als u een sleutelkluis wilt herstellen, geeft u de naam, de resourcegroep en de locatie van de sleutelkluis op. Let op de locatie en de brongroep van de verwijderde sleutelkluis, omdat u ze nodig hebt voor het herstelproces.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Wanneer een sleutelkluis wordt hersteld, wordt een nieuwe bron gemaakt met de oorspronkelijke resource-id van de sleutelkluis. Als de oorspronkelijke brongroep wordt verwijderd, moet deze met dezelfde naam worden gemaakt voordat u probeert te herstellen.

## <a name="deleting-and-purging-key-vault-objects"></a>Belangrijke kluisobjecten verwijderen en zuiveren

Met de volgende opdracht wordt de sleutel 'ContosoFirstKey' verwijderd in een sleutelkluis met de naam 'ContosoVault', die soft-delete heeft ingeschakeld:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Als uw sleutelkluis is ingeschakeld voor soft-delete, lijkt een verwijderde sleutel nog steeds te worden verwijderd, tenzij u expliciet verwijderde sleutels opgeeft. De meeste bewerkingen op een sleutel in de verwijderde status mislukken, behalve voor vermelding, herstellen, verwijderen van een verwijderde sleutel. 

In de volgende opdracht worden bijvoorbeeld verwijderde sleutels in de sleutelkluis 'ContosoVault' weergegeven:

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Overgangsstatus 

Wanneer u een sleutel in een sleutelkluis verwijdert met soft-delete ingeschakeld, kan het enkele seconden duren voordat de overgang is voltooid. Tijdens deze overgang kan het lijken alsof de sleutel zich niet in de actieve status of de verwijderde status bevindt. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Soft-delete gebruiken met belangrijke vault-objecten

Net als sleutelkluizen, een verwijderde sleutel, geheim of certificaat, blijft in verwijderd staat voor maximaal 90 dagen, tenzij u het herstellen of verwijderen. 

#### <a name="keys"></a>Sleutels

Ga als lid van de brexit met een zachte toets:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Een vooraf verwijderde sleutel permanent verwijderen (ook wel zuivering genoemd):

> [!IMPORTANT]
> Het zuiveren van een sleutel zal het permanent verwijderen, en het zal niet kunnen worden hersteld! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

De **herstel-** en **zuiveringsacties** hebben hun eigen machtigingen die zijn gekoppeld aan een belangrijk toegangsbeleid voor kluizen. Om een gebruiker of serviceprincipal in staat te stellen een **herstel-** of **zuiveringsactie** uit te voeren, moet hij of zij de desbetreffende toestemming voor die sleutel of geheim hebben. Standaard wordt **zuivering** niet toegevoegd aan het toegangsbeleid van een sleutelkluis, wanneer de snelkoppeling 'alles' wordt gebruikt om alle machtigingen te verlenen. Je moet specifiek **zuiveringstoestemming** verlenen. 

#### <a name="set-a-key-vault-access-policy"></a>Een toegangsbeleid voor belangrijke kluizen instellen

Met de user@contoso.com volgende opdracht wordt toestemming verleend om verschillende bewerkingen te gebruiken op sleutels in *ContosoVault,* waaronder **zuivering:**

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Als u een bestaande sleutelkluis hebt die zojuist soft-delete heeft ingeschakeld, hebt u mogelijk geen **machtigingen voor herstellen** en **verwijderen.**

#### <a name="secrets"></a>Geheimen

Net als sleutels worden geheimen beheerd met hun eigen opdrachten:

- Een geheim met de naam SQLPassword verwijderen: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Alle verwijderde geheimen in een sleutelkluis: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Een geheim herstellen in de verwijderde status: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Een geheim in verwijderde toestand verwijderen: 

  > [!IMPORTANT]
  > Het zuiveren van een geheim zal het permanent verwijderen, en het zal niet worden hersteld!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certificaten

U certificaten beheren met onderstaande opdrachten:

- Een certificaat met de naam SQLPassword verwijderen: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Alle verwijderde certificaten in een sleutelkluis weergeven: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Een certificaat herstellen in de verwijderde status: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Een certificaat in verwijderde status wissen: 

  > [!IMPORTANT]
  > Het zuiveren van een certificaat zal het permanent verwijderen en het zal niet kunnen worden hersteld!

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>Een beveiligde sleutelkluis met softdelete verwijderen

> [!IMPORTANT]
> Het zuiveren van een sleutelkluis of een van de opgenomen objecten, zal het permanent verwijderen, wat betekent dat het niet kan worden hersteld!

De zuiveringsfunctie wordt gebruikt om een sleutelkluisobject of een volledig sleutelkluisobject, dat eerder werd verwijderd, permanent te verwijderen. Zoals in de vorige sectie is aangetoond, kunnen objecten die zijn opgeslagen in een sleutelkluis met de functie soft-delete zijn ingeschakeld, door meerdere toestanden gaan:
- **Actief**: voor verwijdering.
- **Soft-Deleted**: na verwijdering, in staat om te worden vermeld en terug te herstellen naar actieve staat.
- **Permanent verwijderd**: na zuivering, niet in staat om te worden hersteld.


Hetzelfde geldt voor de sleutelkluis. Om een verwijderdsleutelkluis en de inhoud ervan permanent te verwijderen, moet u de sleutelkluis zelf verwijderen.

### <a name="purging-a-key-vault"></a>Een sleutelkluis zuiveren

Wanneer een sleutelkluis wordt verwijderd, wordt de volledige inhoud definitief verwijderd, inclusief sleutels, geheimen en certificaten. Als u een verwijderd sleutelkluis wilt `Remove-AzKeyVault` verwijderen, `-InRemovedState` gebruikt u de opdracht met de optie `-Location location` en geeft u de locatie van de verwijderde sleutelkluis met het argument op. U de locatie van een verwijderde `Get-AzKeyVault -InRemovedState`kluis vinden met behulp van de opdracht.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Vereiste machtigingen wissen
- Om een verwijderde sleutelkluis te wissen, heeft de gebruiker RBAC-toestemming nodig voor de *bewerking Microsoft.KeyVault/locations/deletedVaults/purge/action.* 
- Als u een verwijderde sleutelkluis wilt weergeven, heeft de gebruiker RBAC-toestemming nodig voor de *bewerking Microsoft.KeyVault/deletedVaults/read.* 
- Standaard heeft alleen een abonnementsbeheerder deze machtigingen. 

### <a name="scheduled-purge"></a>Geplande zuivering

Vermelding verwijderde key vault objecten toont ook wanneer ze zijn gepland om te worden verwijderd door Key Vault. *De geplande zuiveringsdatum* geeft aan wanneer een sleutelkluisobject permanent wordt verwijderd als er geen actie wordt ondernomen. Standaard is de bewaartermijn voor een verwijderd sleutelkluisobject 90 dagen.

>[!IMPORTANT]
>Een gewist kluisobject, geactiveerd door het veld *Geplande zuiveringsdatum,* wordt definitief verwijderd. Het is niet terug te winnen!

## <a name="enabling-purge-protection"></a>Zuiveringsbeveiliging inschakelen

Wanneer de zuiveringsbeveiliging is ingeschakeld, kan een kluis of een object in de verwijderde status pas worden verwijderd als de bewaartermijn van 90 dagen is verstreken. Een dergelijke kluis of object kan nog steeds worden hersteld. Deze functie geeft extra zekerheid dat een kluis of een object nooit permanent kan worden verwijderd totdat de bewaartermijn is verstreken.

U zuiveringsbeveiliging alleen inschakelen als soft-delete ook is ingeschakeld. 

Als u zowel soft-delete- als zuiveringsbeveiliging wilt inschakelen bij het maken van een kluis, gebruikt u de cmdlet [Nieuw-AzKeyVault:](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0)

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Als u zuiveringsbeveiliging wilt toevoegen aan een bestaande kluis (die al is ingeschakeld voor softdelete), gebruikt u de cmdlets [Get-AzKeyVault,](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0) [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)en [Set-AzResource:](/powershell/module/az.resources/set-azresource?view=azps-1.5.0)

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Meer informatie

- Zie [het overzicht soft-delete](overview-soft-delete.md)van Azure Key Vault voor een overzicht van de soft-delete-functie van Key Vault).
- Zie Wat is Azure Key Vault voor een algemeen overzicht van azure key [vault- gebruik?](overview.md)).
