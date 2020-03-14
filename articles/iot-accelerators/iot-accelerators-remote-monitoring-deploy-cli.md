---
title: De oplossing voor externe controle implementeren met CLI-Azure | Microsoft Docs
description: In deze hand leiding vindt u informatie over het inrichten van de oplossings versneller voor externe bewaking met behulp van de CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: ea96b2b996ea79efacdcda50c6370f25e26e0aa2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271653"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>De oplossings versneller voor externe controle implementeren met behulp van de CLI

In deze hand leiding vindt u informatie over het implementeren van de oplossings versneller voor externe controle. U implementeert de oplossing met behulp van de CLI. U kunt de oplossing ook implementeren met behulp van de Web-UI op azureiotsolutions.com, voor meer informatie over deze optie raadpleegt u de Snelstartgids voor [het implementeren van de oplossing voor externe controle](quickstart-remote-monitoring-deploy.md) .

## <a name="prerequisites"></a>Vereisten

Voor het implementeren van de oplossings versneller voor externe controle hebt u een actief Azure-abonnement nodig.

Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

Als u de CLI wilt uitvoeren, moet [node. js](https://nodejs.org/) op uw lokale computer zijn geïnstalleerd.

## <a name="install-the-cli"></a>De CLI installeren

Als u de CLI wilt installeren, voert u de volgende opdracht uit in de opdracht regel omgeving:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Aanmelden bij de CLI

Voordat u de oplossings versneller kunt implementeren, moet u zich aanmelden bij uw Azure-abonnement met behulp van de CLI:

```cmd/sh
pcs login
```

Volg de instructies op het scherm om het aanmeldings proces te volt ooien.

## <a name="deployment-options"></a>Implementatieopties

Wanneer u de oplossings versneller implementeert, zijn er verschillende opties voor het configureren van het implementatie proces:

| Optie | Waarden | Beschrijving |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Een _basis_ implementatie is bedoeld voor testen en demonstraties, het implementeert alle micro Services op één virtuele machine. Een _standaard_ implementatie is bedoeld voor productie, de micro services worden geïmplementeerd op verschillende virtuele machines. Een _lokale_ implementatie configureert een docker-container voor het uitvoeren van de micro Services op uw lokale computer en maakt gebruik van Azure Cloud Services, zoals storage en Cosmos db. |
| Runtime | `dotnet`, `java` | Hiermee selecteert u de taal implementatie van de micro Services. |

Zie [de oplossing voor externe controle lokaal uitvoeren](iot-accelerators-remote-monitoring-deploy-local.md)voor meer informatie over het gebruik van de optie voor lokale implementatie.

## <a name="basic-and-standard-deployments"></a>Basis-en standaard implementaties

In deze sectie vindt u een overzicht van de belangrijkste verschillen tussen een basis-en standaard implementatie.

### <a name="basic"></a>Basic

U kunt een basis implementatie uitvoeren vanuit [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) of met behulp van de cli.

De basis implementatie is gericht op het presen teren van de oplossing. Om de kosten te verlagen, worden alle micro Services geïmplementeerd op één virtuele machine. Deze implementatie maakt geen gebruik van een architectuur die gereed is voor productie.

Bij een basis implementatie worden de volgende services in uw Azure-abonnement gemaakt:

| Count | Resource                       | Type         | Gebruikt voor |
|-------|--------------------------------|--------------|----------|
| 1     | [Virtuele Linux-machine](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 v2  | Micro services hosten |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Standard-laag | Apparaatbeheer en communicatie |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Configuratie gegevens, regels, waarschuwingen en andere koude opslag opslaan |  
| 1     | [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Opslag voor VM-en streaming-controle punten |
| 1     | [Webtoepassing](https://azure.microsoft.com/services/app-service/web/)        |                 | Front-end-webtoepassing hosten |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gebruikers identiteiten en beveiliging beheren |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Activum locaties weer geven |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 eenheden              | Real-time analyse inschakelen |
| 1     | [Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Inrichtings apparaten op schaal |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 eenheid              | Opslag voor berichten gegevens en maakt diep gaande telemetrie-analyse mogelijk |

### <a name="standard"></a>Standard

U kunt een standaard implementatie alleen uitvoeren met behulp van de CLI.

Een standaard implementatie is een implementatie die gereed is voor productie en die door een ontwikkelaar kan worden aangepast en uitgebreid. Gebruik de optie standaard implementatie wanneer u klaar bent voor het aanpassen van een architectuur die gereed is voor productie, gebouwd voor schaal en uitbreid baarheid. Toepassings micro services worden gebouwd als docker-containers en geïmplementeerd met behulp van de Azure Kubernetes-service. De Kubernetes Orchestrator implementeert, schaalt en beheert de micro Services.

Bij een standaard implementatie worden de volgende services in uw Azure-abonnement gemaakt:

| Count | Resource                                     | SKU/grootte      | Gebruikt voor |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Een volledig beheerde Kubernetes-container Orchestrator-service gebruiken, standaard ingesteld op 3 agents|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – Standard-laag | Apparaatbeheer, opdracht en controle |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Configuratie gegevens en telemetrie van apparaten opslaan zoals regels, waarschuwingen en berichten |
| 5     | [Azure Storage accounts](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 voor VM-opslag en 1 voor de controle punten voor streaming |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Toepassings gateway via SSL |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gebruikers identiteiten en beveiliging beheren |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Activum locaties weer geven |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 eenheden              | Real-time analyse inschakelen |
| 1     | [Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Inrichtings apparaten op schaal |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 eenheid              | Opslag voor berichten gegevens en maakt diep gaande telemetrie-analyse mogelijk |

> [!NOTE]
> U kunt de prijs informatie voor deze services vinden op [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing). U kunt het gebruik en de facturerings gegevens voor uw abonnement vinden in [Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>De oplossings versneller implementeren

Voor beelden van implementaties:

### <a name="example-deploy-net-version"></a>Voor beeld: .NET-versie implementeren

In het volgende voor beeld ziet u hoe u de Basic .NET-versie van de oplossing voor externe bewakings versneller implementeert:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Voor beeld: Java-versie implementeren

In het volgende voor beeld ziet u hoe u de standaard Java-versie van de oplossing voor externe bewakings versneller implementeert:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>opties voor de opdracht pc's

Wanneer u de `pcs` opdracht uitvoert om een oplossing te implementeren, wordt u gevraagd:

- Een naam voor uw oplossing. Deze naam moet uniek zijn.
- Het te gebruiken Azure-abonnement.
- Een locatie.
- Referenties voor de virtuele machines die fungeren als host voor de micro Services. U kunt deze referenties gebruiken om toegang te krijgen tot de virtuele machines om problemen op te lossen.

Wanneer de `pcs` opdracht is voltooid, wordt de URL van uw nieuwe oplossings versneller weer gegeven. Met de opdracht `pcs` wordt ook een bestands `{deployment-name}-output.json` gemaakt die informatie bevat, zoals de naam van de IoT Hub die het bestand heeft gemaakt.

Voer het volgende uit voor meer informatie over de opdracht regel parameters:

```cmd/sh
pcs -h
```

Zie [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md)(Engelstalig) voor meer informatie over de cli.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u het volgende geleerd:

> [!div class="checklist"]
> * De oplossingsversneller configureren
> * De oplossings versneller implementeren
> * Meld u aan bij de oplossings versneller

Nu u de oplossing voor controle op afstand hebt geïmplementeerd, is de volgende stap het [verkennen van de mogelijkheden van het dash board van de oplossing](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
