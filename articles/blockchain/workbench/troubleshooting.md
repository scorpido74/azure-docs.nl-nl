---
title: Problemen met Azure Block Chain Workbench oplossen
description: Problemen met een Azure Block Chain Workbench preview-toepassing oplossen.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324302"
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

Voer het `collectBlockchainWorkbenchTroubleshooting.ps1` script uit om logboeken te verzamelen en maak een ZIP-bestand met een map met informatie over het oplossen van problemen. Bijvoorbeeld:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Het script accepteert de volgende para meters:

| Parameter  | Beschrijving | Vereist |
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

| Map of bestand | Beschrijving  |
|---------|---------|
| \Summary.txt | Samen vatting van het systeem |
| \Metrics\blockchain | Metrische gegevens over de Block Chain |
| \Metrics\Workbench | Metrische gegevens over de workbench |
| \Details\Blockchain | Gedetailleerde logboeken over de Block Chain |
| \Details\Workbench | Gedetailleerde logboeken over de workbench |

Het samenvattingsbestand biedt u een momentopname van de algehele status van de toepassing. De samenvatting bevat aanbevolen acties, markeert de ernstigste fouten en toont metagegevens van actieve services.

De map **Metrics** bevat de metrische gegevens van verschillende systeem onderdelen gedurende een bepaalde periode. Het `\Details\Workbench\apiMetrics.txt` van het uitvoer bestand bevat bijvoorbeeld een samen vatting van de verschillende respons codes en de reactie tijden in de verzamelings periode. De map **Details** bevat gedetailleerde logboeken voor het oplossen van specifieke problemen met Workbench of het onderliggende Block chain-netwerk. `\Details\Workbench\Exceptions.csv` bevat bijvoorbeeld een lijst met de meest recente uitzonde ringen die zijn opgetreden in het systeem, wat handig is voor het oplossen van fouten met slimme contracten of interacties met de Block chain. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Probleemoplossings gids voor Azure Block Chain Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
