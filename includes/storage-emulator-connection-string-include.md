---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67176239"
---
De opslag emulator ondersteunt één vast account en een bekende verificatie sleutel voor gedeelde sleutel verificatie. Dit account en deze sleutel zijn de enige gedeelde sleutel referenties die zijn toegestaan voor gebruik met de opslag emulator. Dit zijn:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> De verificatie sleutel die wordt ondersteund door de opslag emulator is alleen bedoeld voor het testen van de functionaliteit van de client verificatie code. Dit is geen beveiligings doel. U kunt uw productie-opslag account en-sleutel niet gebruiken met de opslag emulator. Gebruik het ontwikkelings account niet met productie gegevens.
> 
> De opslag emulator ondersteunt alleen verbinding via HTTP. HTTPS is echter het aanbevolen protocol voor het openen van bronnen in een Azure-opslag account voor productie.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Verbinding maken met het Emulator-account met behulp van een snelkoppeling
De eenvoudigste manier om verbinding te maken met de opslag emulator vanuit uw toepassing is door een connection string te configureren in het configuratie bestand van uw toepassing `UseDevelopmentStorage=true`dat verwijst naar de snelkoppeling. Hier volgt een voor beeld van een connection string voor de opslag emulator in een *app. config* -bestand: 

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
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Deze waarde is gelijk aan de hierboven weer gegeven snelkoppeling `UseDevelopmentStorage=true`,.

#### <a name="specify-an-http-proxy"></a>Een HTTP-proxy opgeven
U kunt ook een HTTP-proxy opgeven die moet worden gebruikt bij het testen van uw service op basis van de opslag emulator. Dit kan handig zijn voor het observeren van HTTP-aanvragen en-antwoorden terwijl u fouten opspoort voor de opslag Services. Als u een proxy wilt opgeven, `DevelopmentStorageProxyUri` voegt u de optie toe aan de Connection String en stelt u de waarde ervan in op de proxy-URI. Dit is bijvoorbeeld een connection string die naar de opslag emulator verwijst en een HTTP-proxy configureert:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

