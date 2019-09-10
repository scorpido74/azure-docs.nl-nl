---
title: Preview-versie van Azure Block Chain Workbench oplossen
description: Problemen met een Azure Block Chain Workbench preview-toepassing oplossen.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8fec065b629f2f2b93e78a63521ea0ce4669dd4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844043"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Preview-versie van Azure Block Chain Workbench oplossen

Een PowerShell-script is beschikbaar ter ondersteuning van foutopsporing door de ontwikkelaar. Het script genereert een samen vatting en verzamelt gedetailleerde logboeken voor het oplossen van problemen. Verzamelde logboeken zijn onder andere:

* Block chain-netwerk, zoals Ethereum
* Micro services van Block Chain workbench
* Application Insights
* Azure-bewaking (Azure Monitor-Logboeken)

U kunt de informatie gebruiken om de volgende stappen te bepalen en de hoofdoorzaak van problemen te achterhalen.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Probleemoplossings script

Het PowerShell probleemoplossingsscript is beschikbaar op GitHub. [Download een ZIP-bestand ](https://github.com/Azure-Samples/blockchain/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Het script uitvoeren
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Voer het `collectBlockchainWorkbenchTroubleshooting.ps1` script voor het verzamelen van logboeken uit en maak een ZIP-bestand van de map met informatie over probleemoplossing. Bijvoorbeeld:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Het script accepteert de volgende para meters:

| Parameter  | Description | Vereist |
|---------|---------|----|
| Abonnements | Abonnement-id om alle bronnen te maken of te zoeken. | Ja |
| ResourceGroupName | De naam van de Azure-resourcegroep waar Blockchain Workbench is geïmplementeerd. | Ja |
| OutputDirectory | Pad voor het maken van het ZIP-bestand met uitvoer. Als dit niet is opgegeven, wordt standaard de huidige map gebruikt. | Nee |
| LookbackHours | Het aantal uren dat wordt gehanteerd bij het ophalen van telemetrie. De standaard waarde is 24 uur. De maximum waarde is 90 uur | Nee |
| OmsSubscriptionId | De abonnements-ID waar Azure Monitor logboeken worden geïmplementeerd. Geef deze para meter alleen door als de Azure Monitor logboeken voor het block chain-netwerk zijn geïmplementeerd buiten de resource groep Block Chain Workbench.| Nee |
| OmsResourceGroup |De resource groep waar Azure Monitor logboeken worden geïmplementeerd. Geef deze para meter alleen door als de Azure Monitor logboeken voor het block chain-netwerk zijn geïmplementeerd buiten de resource groep Block Chain Workbench.| Nee |
| OmsWorkspaceName | De naam van de Log Analytics werkruimte. Geef deze para meter alleen door als de Azure Monitor logboeken voor het block chain-netwerk zijn geïmplementeerd buiten de resource groep Block Chain workbench | Nee |

## <a name="what-is-collected"></a>Wat wordt er verzameld?

Het ZIP-bestand met output bevat de volgende mapstructuur:

| Map of bestand | Description  |
|---------|---------|
| \Summary.txt | Samen vatting van het systeem |
| \Metrics\blockchain | Metrische gegevens over de Block Chain |
| \Metrics\Workbench | Metrische gegevens over de workbench |
| \Details\Blockchain | Gedetailleerde logboeken over de Block Chain |
| \Details\Workbench | Gedetailleerde logboeken over de workbench |

Het samenvattingsbestand biedt u een momentopname van de algehele status van de toepassing. De samenvatting bevat aanbevolen acties, markeert de ernstigste fouten en toont metagegevens van actieve services.

De **metrische gegevens** map bevat metrische gegevens van verschillende onderdelen van het systeem in de tijd. Bijvoorbeeld, het uitvoerbestand `\Details\Workbench\apiMetrics.txt` bevat een overzicht van verschillende responscodes en reactietijden gedurende de periode van de verzameling. De map **Details** bevat gedetailleerde logboeken voor het oplossen van specifieke problemen met Workbench of het onderliggende Block chain-netwerk. Bijvoorbeeld, `\Details\Workbench\Exceptions.csv` bevat een overzicht van de meest recente excepties die zijn opgetreden in het systeem, wat nuttig kan zijn voor het oplossen van fouten met slimme contracten of interactie met de blockchain. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Probleemoplossings gids voor Azure Block Chain Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
