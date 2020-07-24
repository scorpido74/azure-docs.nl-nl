---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070546"
---
Azurite ondersteunt één vast account en een bekende verificatie sleutel voor gedeelde sleutel verificatie. Dit account en deze sleutel zijn de enige gedeelde sleutel referenties die zijn toegestaan voor gebruik met Azurite. Dit zijn:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> De verificatie sleutel die wordt ondersteund door Azurite is alleen bedoeld voor het testen van de functionaliteit van de client verificatie code. Dit is geen beveiligings doel. U kunt uw productie-opslag account en-sleutel niet gebruiken met Azurite. Gebruik het ontwikkelings account niet met productie gegevens.
> 
> Azurite ondersteunt alleen verbinding via HTTP. HTTPS is echter het aanbevolen protocol voor het openen van bronnen in een Azure-opslag account voor productie.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Verbinding maken met het Emulator-account met behulp van een snelkoppeling
De eenvoudigste manier om verbinding te maken met Azurite vanuit uw toepassing is het configureren van een connection string in het configuratie bestand van uw toepassing dat verwijst naar de snelkoppeling `UseDevelopmentStorage=true` . Hier volgt een voor beeld van een connection string naar Azurite in een *app.config* -bestand: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Verbinding maken met het Emulator-account met behulp van de bekende account naam en-sleutel
Als u een connection string wilt maken die verwijst naar de naam en sleutel van het Emulator-account, moet u de eind punten opgeven voor elk van de services die u wilt gebruiken in de connection string. Dit is nodig zodat de connection string verwijst naar de emulator-eind punten die verschillen van die voor een productie-opslag account. Zo ziet de waarde van uw connection string er als volgt uit:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Deze waarde is gelijk aan de hierboven weer gegeven snelkoppeling, `UseDevelopmentStorage=true` .
