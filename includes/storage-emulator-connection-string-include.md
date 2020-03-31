---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176239"
---
De opslagemulator ondersteunt één vast account en een bekende verificatiesleutel voor Shared Key-verificatie. Dit account en deze sleutel zijn de enige shared key-referenties die zijn toegestaan voor gebruik met de opslagemulator. Dit zijn:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> De verificatiesleutel die wordt ondersteund door de opslagemulator is alleen bedoeld voor het testen van de functionaliteit van uw clientverificatiecode. Het dient geen enkel veiligheidsdoel. U uw productieopslagaccount en sleutel niet gebruiken met de opslagemulator. U mag het ontwikkelingsaccount niet gebruiken met productiegegevens.
> 
> De opslagemulator ondersteunt alleen verbinding via HTTP. HTTPS is echter het aanbevolen protocol voor toegang tot bronnen in een Azure-opslagaccount voor productie.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Verbinding maken met het emulatoraccount via een snelkoppeling
De eenvoudigste manier om verbinding te maken met de opslagemulator van uw toepassing is door `UseDevelopmentStorage=true`een verbindingstekenreeks in het configuratiebestand van uw toepassing te configureren die verwijst naar de snelkoppeling. Hier is een voorbeeld van een verbindingstekenreeks met de opslagemulator in een *app.config-bestand:* 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Maak verbinding met het emulator-account met de bekende accountnaam en -sleutel
Als u een verbindingstekenreeks wilt maken die verwijst naar de naam en de sleutel van het emulatoraccount, moet u de eindpunten opgeven voor elk van de services die u vanuit de emulator wilt gebruiken in de verbindingstekenreeks. Dit is nodig zodat de verbindingstekenreeks verwijst naar de emulatoreindpunten, die anders zijn dan die voor een productieopslagaccount. De waarde van uw verbindingstekenreeks ziet er bijvoorbeeld als volgt uit:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Deze waarde is identiek aan `UseDevelopmentStorage=true`de bovenstaande snelkoppeling.

#### <a name="specify-an-http-proxy"></a>Een HTTP-proxy opgeven
U ook een HTTP-proxy opgeven die u wilt gebruiken wanneer u uw service test tegen de opslagemulator. Dit kan handig zijn voor het observeren van HTTP-verzoeken en -antwoorden terwijl u bewerkingen debugt tegen de opslagservices. Als u een proxy `DevelopmentStorageProxyUri` wilt opgeven, voegt u de optie toe aan de verbindingstekenreeks en stelt u de waarde ervan in op de proxy-URI. Hier is bijvoorbeeld een verbindingstekenreeks die naar de opslagemulator verwijst en een HTTP-proxy configureert:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

