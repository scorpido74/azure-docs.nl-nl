---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 873fd8cbc211f098c93b8fb3fbe701e4a34d8487
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320522"
---
De `Logging` instellingen beheren ASP.NET Core-registratieondersteuning voor uw container. U dezelfde configuratie-instellingen en -waarden voor uw container gebruiken als voor een ASP.NET Core-toepassing. 

De volgende logging providers worden ondersteund door de container:

|Provider|Doel|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|De ASP.NET `Console` Core logging provider. Alle ASP.NET Core-configuratie-instellingen en standaardwaarden voor deze logboekregistratieprovider worden ondersteund.|
|[Fouten opsporen](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|De ASP.NET `Debug` Core logging provider. Alle ASP.NET Core-configuratie-instellingen en standaardwaarden voor deze logboekregistratieprovider worden ondersteund.|
|[Schijf](#disk-logging)|De JSON logging provider. Deze logging provider schrijft loggegevens naar de uitvoer mount.|

Met deze containeropdracht worden logboekregistratiegegevens in de JSON-indeling opgeslagen in de uitvoerberg:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Met deze containeropdracht ziet u foutopsporingsgegevens, vooraf opgelost met `dbug`, terwijl de container wordt uitgevoerd:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Schijflogboekregistratie

De `Disk` logboekregistratieprovider ondersteunt de volgende configuratie-instellingen:

| Name | Gegevenstype | Beschrijving |
|------|-----------|-------------|
| `Format` | Tekenreeks | De uitvoerindeling voor logboekbestanden.<br/> **Let op:** Deze waarde moet `json` worden ingesteld om de logboekregistratieprovider in te schakelen. Als deze waarde is opgegeven zonder ook een uitvoerbevestiging op te geven tijdens het instantiën van een container, treedt er een fout op. |
| `MaxFileSize` | Geheel getal | De maximale grootte, in megabytes (MB), van een logboekbestand. Wanneer de grootte van het huidige logboekbestand aan deze waarde voldoet of deze overschrijdt, wordt een nieuw logboekbestand gestart door de logboekregistratieprovider. Als -1 is opgegeven, wordt de grootte van het logboekbestand alleen beperkt door de eventuele maximale bestandsgrootte voor de uitvoerberg. De standaardwaarde is 1. |

Zie [Bestandsconfiguratie Instellingen](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)voor meer informatie over het configureren van ASP.NET Core-registratieondersteuning.

