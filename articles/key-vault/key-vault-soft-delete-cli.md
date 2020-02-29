---
title: Azure Key Vault-hoe u zacht verwijderen kunt gebruiken met CLI
description: Voor beelden van het gebruik van zacht verwijderen met CLI-code knipsels
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 7288e5d8c01122bea7650274cdaf358c7fc24cd0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197314"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Key Vault soft-delete gebruiken met CLI

Met de functie voor voorlopig verwijderen van Azure Key Vault kunt u verwijderde kluizen en kluis objecten herstellen. Met name de volgende scenario's worden door zacht verwijderen opgelost:

- Ondersteuning voor herstel bare verwijdering van een sleutel kluis
- Ondersteuning voor herstel bare verwijdering van sleutel kluis-objecten; sleutels, geheimen en certificaten

## <a name="prerequisites"></a>Vereisten

- Azure CLI: Zie [Key Vault beheren met Azure cli](key-vault-manage-with-cli2.md)als u deze instelling niet hebt voor uw omgeving.

Zie Naslag informatie voor [Azure cli-Key Vault](https://docs.microsoft.com/cli/azure/keyvault)voor Key Vault specifieke referentie gegevens voor cli.

## <a name="required-permissions"></a>Vereiste machtigingen

Key Vault bewerkingen worden als volgt afzonderlijk beheerd via machtigingen op basis van op rollen gebaseerde toegangs beheer (RBAC):

| Bewerking | Beschrijving | Gebruikers machtiging |
|:--|:--|:--|
|Lijst|Een lijst met verwijderde sleutel kluizen.|Microsoft.KeyVault/deletedVaults/read|
|Herstellen|Hiermee herstelt u een verwijderde sleutel kluis.|Microsoft.KeyVault/vaults/write|
|Opschonen|Verwijdert permanent een verwijderde sleutel kluis en alle bijbehorende inhoud.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Zie [uw sleutel kluis beveiligen](key-vault-secure-your-key-vault.md)voor meer informatie over machtigingen en toegangs beheer.

## <a name="enabling-soft-delete"></a>Voorlopig verwijderen inschakelen

U schakelt ' voorlopig verwijderen ' in om het herstellen van een verwijderde sleutel kluis of objecten die zijn opgeslagen in een sleutel kluis, toe te staan.

> [!IMPORTANT]
> Het inschakelen van ' zacht verwijderen ' op een sleutel kluis is een onomkeerbaare actie. Zodra de eigenschap soft-delete is ingesteld op ' True ', kan deze niet worden gewijzigd of verwijderd.  

### <a name="existing-key-vault"></a>Bestaande sleutel kluis

Voor een bestaande sleutel kluis met de naam ContosoVault, schakelt u voorlopig verwijderen als volgt in. 

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Nieuwe sleutel kluis

Het inschakelen van het voorlopig verwijderen van een nieuwe sleutel kluis wordt uitgevoerd tijdens de aanmaak tijd door de vlag voor zacht verwijderen toevoegen toe te voegen aan de opdracht Create.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Activering van voorlopig verwijderen controleren

Als u wilt controleren of een sleutel kluis voorlopig is ingeschakeld, voert u de opdracht *weer geven* uit en zoekt u naar ' voorlopig verwijderen ingeschakeld? ' geschreven

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Een met Soft verwijderde beveiligde sleutel kluis verwijderen

De opdracht voor het verwijderen van wijzigingen in de sleutel kluis, afhankelijk van het feit of het voorlopig verwijderen is ingeschakeld.

> [!IMPORTANT]
>Als u de volgende opdracht uitvoert voor een sleutel kluis waarvoor geen tijdelijke verwijdering is ingeschakeld, verwijdert u deze sleutel kluis en alle inhoud zonder opties voor herstel.

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hoe soft-delete uw sleutel kluizen beschermt

Met voorlopig verwijderen ingeschakeld:

- Een verwijderde sleutel kluis wordt verwijderd uit de resource groep en in een gereserveerde naam ruimte geplaatst, die is gekoppeld aan de locatie waar deze is gemaakt. 
- Verwijderde objecten, zoals sleutels, geheimen en certificaten, zijn niet toegankelijk als de sleutel kluis de status verwijderd heeft. 
- De DNS-naam voor een verwijderde sleutel kluis is gereserveerd, waardoor wordt voor komen dat er een nieuwe sleutel kluis met dezelfde naam wordt gemaakt.  

U kunt de sleutel kluizen voor verwijderde status weer geven die zijn gekoppeld aan uw abonnement met behulp van de volgende opdracht:

```azurecli
az keyvault list-deleted
```
- De *id* kan worden gebruikt om de resource te identificeren bij het herstellen of verwijderen. 
- De *resource-id* is de oorspronkelijke resource-id van deze kluis. Omdat deze sleutel kluis nu een verwijderde status heeft, bestaat er geen resource met die resource-ID. 
- De *geplande opschoon datum* is wanneer de kluis permanent wordt verwijderd als er geen actie wordt ondernomen. De standaard retentie periode, die wordt gebruikt om de *geplande opschoon datum*te berekenen, is 90 dagen.

## <a name="recovering-a-key-vault"></a>Een sleutel kluis herstellen

Als u een sleutel kluis wilt herstellen, geeft u de naam van de sleutel kluis, de resource groep en de locatie op. Let op de locatie en de resource groep van de verwijderde sleutel kluis, wanneer u deze nodig hebt voor het herstel proces.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Wanneer een sleutel kluis wordt hersteld, wordt een nieuwe resource gemaakt met de oorspronkelijke resource-ID van de sleutel kluis. Als de oorspronkelijke resource groep is verwijderd, moet er een worden gemaakt met dezelfde naam voordat u probeert te herstellen.

## <a name="deleting-and-purging-key-vault-objects"></a>Sleutel kluis objecten verwijderen en verwijderen

Met de volgende opdracht wordt de sleutel ' ContosoFirstKey ' verwijderd in een sleutel kluis met de naam ' ContosoVault ', waarvoor het voorlopig verwijderen is ingeschakeld:

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Als uw sleutel kluis is ingeschakeld voor zacht verwijderen, wordt er nog steeds een verwijderde sleutel weer gegeven, behalve wanneer u de verwijderde sleutels expliciet vermeldt of ophaalt. De meeste bewerkingen met een sleutel in de verwijderde status mislukken, behalve als een verwijderde sleutel wordt weer gegeven of verwijderd. 

Als u bijvoorbeeld wilt aanvragen om verwijderde sleutels in een sleutel kluis weer te geven, gebruikt u de volgende opdracht:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Overgangs status 

Wanneer u een sleutel in een sleutel kluis verwijdert waarvoor het uitvoeren van zacht verwijderen is ingeschakeld, kan het enkele seconden duren voordat de overgang is voltooid. Tijdens deze overgang kan het lijken alsof de sleutel niet de status actief of verwijderd heeft. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Voorlopig verwijderen gebruiken met Key kluis objecten

Net als bij sleutel kluizen, behoudt een verwijderde sleutel, geheim of certificaat, de status verwijderd tot 90 dagen, tenzij u deze herstelt of opschoont.

#### <a name="keys"></a>Sleutels

Een voorlopig verwijderde sleutel herstellen:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Een voorlopig verwijderde sleutel permanent verwijderen (ook wel het verwijderen genoemd):

> [!IMPORTANT]
> Als u een sleutel verwijdert, wordt deze definitief verwijderd en kan deze niet worden hersteld. 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

De **herstel** -en **opschoon** acties hebben hun eigen machtigingen die zijn gekoppeld aan een sleutel kluis toegangs beleid. Als een gebruiker of service-principal een **herstel** -of **opschoon** actie kan uitvoeren, moeten ze beschikken over de juiste machtiging voor die sleutel of dit geheim. Standaard wordt **leegmaken** niet toegevoegd aan het toegangs beleid van een sleutel kluis wanneer de snelkoppeling ' alle ' wordt gebruikt om alle machtigingen te verlenen. U moet de machtiging **verwijderen** specifiek verlenen. 

#### <a name="set-a-key-vault-access-policy"></a>Een sleutel kluis toegangs beleid instellen

Met de volgende opdracht wordt user@contoso.com toestemming verleend voor het gebruik van verschillende bewerkingen op sleutels in *ContosoVault* , waaronder **opschonen**:

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Als u een bestaande sleutel kluis hebt die zojuist is ingeschakeld, hebt u mogelijk geen machtigingen voor **herstellen** en **verwijderen** .

#### <a name="secrets"></a>Geheimen

Net als sleutels worden geheimen beheerd met hun eigen opdrachten:

- Verwijder een geheim met de naam SQLPassword: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- Alle verwijderde geheimen in een sleutel kluis weer geven: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Een geheim herstellen met de status verwijderd: 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Verwijder een geheim met de status verwijderd: 

  > [!IMPORTANT]
  > Als u een geheim verwijdert, wordt dit permanent verwijderd en kan het niet worden hersteld. 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
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

Wanneer een sleutel kluis wordt leeg gemaakt, wordt de volledige inhoud definitief verwijderd, inclusief de sleutels, geheimen en certificaten. Als u een tijdelijke, verwijderde sleutel kluis wilt verwijderen, gebruikt u de opdracht `az keyvault purge`. U kunt de locatie van de verwijderde sleutel kluizen van uw abonnement vinden met behulp van de opdracht `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
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

Gebruik de opdracht AZ-sleutel [kluis maken](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) om de beveiliging van zowel zacht verwijderen als opschonen van een kluis in te scha kelen:

```
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Als u een schone beveiliging wilt toevoegen aan een bestaande kluis (waarvoor al zacht verwijderen is ingeschakeld), gebruikt u de opdracht AZ-sleutel [kluis update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) :

```
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Meer informatie

- Zie [Azure Key Vault overzicht van tijdelijke verwijdering](key-vault-ovw-soft-delete.md)voor een overzicht van de functie voor voorlopig verwijderen van Key Vault.
- Zie [Wat is Azure Key Vault?](key-vault-overview.md)voor een algemeen overzicht van Azure Key Vault gebruik.

