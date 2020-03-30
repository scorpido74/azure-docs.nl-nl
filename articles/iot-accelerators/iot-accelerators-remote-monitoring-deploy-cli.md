---
title: De oplossing voor externe bewaking implementeren met CLI - Azure | Microsoft Documenten
description: Deze handleiding laat u zien hoe u de Remote Monitoring oplossingsversneller inricht met behulp van de CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 501ca51a9542229a14e98a56679837950a82891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258291"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>De remote monitoring oplossingsversneller implementeren met behulp van de CLI

Deze handleiding laat u zien hoe u de Remote Monitoring oplossingsversneller implementeren. U implementeert de oplossing met behulp van de CLI. U de oplossing ook implementeren met behulp van de webgebaseerde gebruikersinterface op azureiotsolutions.com, om meer te weten te komen over deze optie, zie snel start [van de accelerator voor externe bewaking-oplossingen](quickstart-remote-monitoring-deploy.md) implementeren.

## <a name="prerequisites"></a>Vereisten

Als u de oplossingsversneller voor externe bewaking wilt implementeren, hebt u een actief Azure-abonnement nodig.

Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

Om de CLI uit te voeren, moet [node.js](https://nodejs.org/) op uw lokale machine zijn geïnstalleerd.

## <a name="install-the-cli"></a>De CLI installeren

Als u de CLI wilt installeren, voert u de volgende opdracht uit in de opdrachtregelomgeving:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Log hier in

Voordat u de oplossingsversneller implementeren, moet u zich aanmelden bij uw Azure-abonnement met de CLI:

```cmd/sh
pcs login
```

Volg de instructies op het scherm om het aanmeldingsproces te voltooien.

## <a name="deployment-options"></a>Implementatieopties

Wanneer u de oplossingsversneller implementeert, zijn er verschillende opties die het implementatieproces configureren:

| Optie | Waarden | Beschrijving |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Een _basisimplementatie_ is bedoeld voor tests en demonstraties, het implementeert alle microservices naar één virtuele machine. Een _standaard_ implementatie is bedoeld voor productie, het implementeert de microservices naar verschillende virtuele machines. Een _lokale_ implementatie configureert een Docker-container om de microservices op uw lokale machine uit te voeren en maakt gebruik van Azure-cloudservices, zoals opslag en Cosmos DB. |
| Runtime | `dotnet`, `java` | Hiermee selecteert u de taalimplementatie van de microservices. |

Zie [Lokaal uitvoeren van de oplossing voor externe controle](iot-accelerators-remote-monitoring-deploy-local.md)voor meer informatie over het gebruik van de optie lokale implementatie .

## <a name="basic-and-standard-deployments"></a>Basis- en standaardimplementaties

In deze sectie worden de belangrijkste verschillen tussen een basis- en standaardimplementatie samengevat.

### <a name="basic"></a>Basic

U een basisimplementatie doen vanaf [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) of met de CLI.

De basisimplementatie is gericht op het presenteren van de oplossing. Om de kosten te verlagen, worden alle microservices geïmplementeerd in één virtuele machine. Deze implementatie maakt geen gebruik van een productieklare architectuur.

Met een basisimplementatie worden de volgende services gemaakt in uw Azure-abonnement:

| Count | Resource                       | Type         | Gebruikt voor |
|-------|--------------------------------|--------------|----------|
| 1     | [Virtuele Linux-machine](https://azure.microsoft.com/services/virtual-machines/) | Standaard D1 V2  | Hosting van microservices |
| 1     | [Azure IoT-hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Standaardlaag | Apparaatbeheer en -communicatie |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Configuratiegegevens, regels, waarschuwingen en andere koudeopslag opslaan |  
| 1     | [Azure-opslagaccount](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Opslag voor VM- en streamingcontrolepunten |
| 1     | [Webtoepassing](https://azure.microsoft.com/services/app-service/web/)        |                 | Hosting front-end webtoepassing |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gebruikersidentiteiten en beveiliging beheren |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Assetlocaties weergeven |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 eenheden              | Real-time analytics mogelijk maken |
| 1     | [Azure-service voor het inrichten van apparaten](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Inrichtingsapparatuur op schaal |
| 1     | [Inzichten in Azure-tijdreeksen](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 eenheid              | Opslag voor berichtengegevens en maakt deep-dive telemetrieanalyse mogelijk |

### <a name="standard"></a>Standard

U een standaardimplementatie alleen doen met de CLI.

Een standaardimplementatie is een productieklare implementatie die een ontwikkelaar kan aanpassen en uitbreiden. Gebruik de standaardimplementatieoptie wanneer u klaar bent om een productieklare architectuur aan te passen, gebouwd voor schaal en uitbreidbaarheid. Toepassingsmicroservices worden gebouwd als Docker-containers en geïmplementeerd met behulp van de Azure Kubernetes-service. De Kubernetes orchestrator implementeert, schaalt en beheert de microservices.

Met een standaardimplementatie worden de volgende services gemaakt in uw Azure-abonnement:

| Count | Resource                                     | SKU / Grootte      | Gebruikt voor |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Gebruik een volledig beheerde Kubernetes container orchestration-service, standaard voor 3 agents|
| 1     | [Azure IoT-hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – Standaardlaag | Apparaatbeheer, opdracht en besturing |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Configuratiegegevens en apparaattelemetrie opslaan, zoals regels, waarschuwingen en berichten |
| 5     | [Azure-opslagaccounts](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 voor VM-opslag en 1 voor de streamingcheckpoints |
| 1     | [App-service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Toepassingsgateway via TLS |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gebruikersidentiteiten en beveiliging beheren |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Assetlocaties weergeven |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 eenheden              | Real-time analytics mogelijk maken |
| 1     | [Azure-service voor het inrichten van apparaten](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Inrichtingsapparatuur op schaal |
| 1     | [Inzichten in Azure-tijdreeksen](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 eenheid              | Opslag voor berichtengegevens en maakt deep-dive telemetrieanalyse mogelijk |

> [!NOTE]
> U vindt prijsinformatie voor [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing)deze services op . U gebruiks- en factureringsgegevens voor uw abonnement vinden in de [Azure Portal.](https://portal.azure.com/)

## <a name="deploy-the-solution-accelerator"></a>De oplossingsversnellers implementeren

Voorbeelden van implementatie:

### <a name="example-deploy-net-version"></a>Voorbeeld: .NET-versie implementeren

In het volgende voorbeeld ziet u hoe u de basisversie .NET van de versneller met externe bewakingsoplossing implementeert:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Voorbeeld: Java-versie implementeren

In het volgende voorbeeld ziet u hoe u de standaardJava-versie van de remote monitoring-oplossingsversneller implementeert:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>opties voor pcs- opdrachten

Wanneer u `pcs` de opdracht uitvoert om een oplossing te implementeren, wordt u gevraagd:

- Een naam voor uw oplossing. Deze naam moet uniek zijn.
- Het te gebruiken Azure-abonnement.
- Een locatie.
- Referenties voor de virtuele machines die de microservices hosten. U deze referenties gebruiken om toegang te krijgen tot de virtuele machines voor het oplossen van problemen.

Wanneer `pcs` de opdracht is voltooid, wordt de URL van uw nieuwe oplossingsversneller weergegeven. De `pcs` opdracht maakt `{deployment-name}-output.json` ook een bestand dat informatie bevat, zoals de naam van de IoT-hub die het heeft gemaakt.

Voer voor meer informatie over de opdrachtregelparameters het als:

```cmd/sh
pcs -h
```

Zie [De CLI gebruiken](https://github.com/Azure/pcs-cli/blob/master/README.md)voor meer informatie over de CLI.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding leerde je hoe je:

> [!div class="checklist"]
> * De oplossingsversneller configureren
> * De oplossingsversnellers implementeren
> * Meld u aan bij de oplossingsversneller

Nu u de oplossing voor externe bewaking hebt geïmplementeerd, is de volgende stap het [verkennen van de mogelijkheden van het oplossingsdashboard.](./quickstart-remote-monitoring-deploy.md)

<!-- Next how-to guides in the sequence -->
