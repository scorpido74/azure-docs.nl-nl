---
title: bestand opnemen
description: bestand opnemen
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176468"
---
## <a name="download-the-source-code"></a>De broncode downloaden

De broncoderepositories voor externe bewaking bevatten de broncode en de Docker-configuratiebestanden die u nodig hebt om de Docker-afbeeldingen van microservices uit te voeren.

Als u een lokale versie van de opslagplaats wilt klonen en maken, gebruikt u uw opdrachtregelomgeving om naar een geschikte map op uw lokale machine te navigeren. Voer vervolgens een van de volgende sets opdrachten uit om de .NET-opslagplaats te klonen:

Voer het als nodig op om de nieuwste versie van de .NET-microservice-implementaties te downloaden:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Deze opdrachten downloaden de broncode voor alle microservices naast de scripts die u gebruikt om de microservices lokaal uit te voeren. Hoewel u de broncode niet nodig hebt om de microservices in Docker uit te voeren, is de broncode handig als u later van plan bent de oplossingsversneller aan te passen en uw wijzigingen lokaal te testen.

## <a name="deploy-the-azure-services"></a>De Azure-services implementeren

Hoewel in dit artikel wordt uitgelegd hoe u de microservices lokaal uitvoeren, zijn deze afhankelijk van Azure-services die in de cloud worden uitgevoerd. Gebruik het volgende script om de Azure-services te implementeren. In de volgende scriptvoorbeelden wordt ervan uitgegaan dat u de .NET-repository op een Windows-machine gebruikt. Als u in een andere omgeving werkt, past u de paden, bestandsextensies en padscheidingen op de juiste manier aan.

### <a name="create-new-azure-resources"></a>Nieuwe Azure-bronnen maken

Als u nog niet de vereiste Azure-bronnen hebt gemaakt, voert u de volgende stappen uit:

1. Navigeer in uw opdrachtregelomgeving naar de **map \services\scripts\local\launch** in uw gekloonde kopie van de opslagplaats.

1. Voer de volgende opdrachten uit om het **CLI-hulpprogramma voor pc's** te installeren en u aan te melden bij uw Azure-account:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Voer het **start.cmd** script uit. Het script vraagt u om de volgende informatie:
   * Een oplossingsnaam.
   * Het te gebruiken Azure-abonnement.
   * De locatie van het Te gebruiken Azure-datacenter.

     Het script maakt resourcegroep in Azure met de naam van uw oplossing. Deze brongroep bevat de Azure-resources die de oplossingsversneller gebruikt. U deze brongroep verwijderen zodra u de bijbehorende resources niet meer nodig hebt.

     Het script voegt ook een set omgevingsvariabelen met een voorvoegsel **PCS** toe aan uw lokale machine. Deze omgevingsvariabelen bieden de details voor externe bewaking om te kunnen lezen vanuit een Azure Key Vault-bron. Deze Key Vault-bron is de plaats waar op afstand de configuratiewaarden worden gelezen.

     > [!TIP]
     > Wanneer het script is voltooid, worden de omgevingsvariabelen ook opgeslagen in een bestand dat ** \<\>\\de naam\\\<\>van de thuismap .pcs-oplossing .env wordt**genoemd. U ze gebruiken voor toekomstige implementaties van oplossingsversnellers. Houd er rekening mee dat alle omgevingsvariabelen die zijn ingesteld op uw lokale machine, waarden overschrijven in het **\\\\\\lokale .env-bestand van services** wanneer u **docker-compose uitvoert.**

1. Verlaat uw opdrachtlijnomgeving.

### <a name="use-existing-azure-resources"></a>Bestaande Azure-bronnen gebruiken

Als u al de vereiste Azure-resources hebt gemaakt, maakt u de bijbehorende omgevingsvariabelen op uw lokale machine.
Stel de omgevingsvariabelen in voor het volgende:
* **PCS_KEYVAULT_NAME** - Naam van de Azure Key Vault-bron
* **PCS_AAD_APPID** - De AAD-applicatie-id
* **PCS_AAD_APPSECRET** - Het AAD-applicatiegeheim

Configuratiewaarden worden gelezen uit deze Azure Key Vault-bron. Deze omgevingsvariabelen kunnen worden opgeslagen in de ** \<naam van de (pc's)-oplossingsnaam .env\>\\\\\<\>** van de implementatie in de naam van de thuismap .pcs-oplossing. Houd er rekening mee dat omgevingsvariabelen die zijn ingesteld op uw lokale machine, waarden overschrijven in het **\\\\\\lokale .env-bestand van services** wanneer u **docker-compose uitvoert.**

Een deel van de configuratie die nodig is voor de microservice, wordt opgeslagen in een exemplaar van **Key Vault** dat is gemaakt bij de eerste implementatie. De bijbehorende variabelen in keyvault moeten zo nodig worden gewijzigd.