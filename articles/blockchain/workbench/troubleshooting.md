---
title: Azure Blockchain Workbench-probleemoplossing
description: Een Azure Blockchain Workbench Preview-toepassing oplossen.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324302"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Azure Blockchain Workbench Preview probleemoplossing

Een PowerShell-script is beschikbaar om te helpen bij het debuggen of ondersteunen van ontwikkelaars. Het script genereert een overzicht en verzamelt gedetailleerde logboeken voor het oplossen van problemen. Verzamelde logboeken zijn:

* Blockchain-netwerk, zoals Ethereum
* Blockchain Workbench-microservices
* Application Insights
* Azure-controle (Azure Monitor-logboeken)

U de informatie gebruiken om de volgende stappen te bepalen en de hoofdoorzaak van problemen vast te stellen.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Script oplossen van problemen

Het PowerShell-probleemoplossingsscript is beschikbaar op GitHub. [Download een ZIP-bestand ](https://github.com/Azure-Samples/blockchain/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Het script uitvoeren
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Voer `collectBlockchainWorkbenchTroubleshooting.ps1` het script uit om logboeken te verzamelen en een ZIP-bestand te maken met een map met informatie over probleemoplossing. Bijvoorbeeld:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Het script accepteert de volgende parameters:

| Parameter  | Beschrijving | Vereist |
|---------|---------|----|
| Abonnement-ID | Abonnement-ID om alle bronnen te maken of te lokaliseren. | Ja |
| ResourceGroupName | Naam van de Azure Resource Group waar Blockchain Workbench is geïmplementeerd. | Ja |
| Uitvoermap | Pad om de uitvoer te maken . ZIP-bestand. Als dit niet is opgegeven, wordt de huidige map standaard weergegeven. | Nee |
| Terugblik | Aantal uren dat u moet gebruiken bij het trekken van telemetrie. De standaardwaarde is 24 uur. Maximale waarde is 90 uur | Nee |
| OmsAbonnementId | De abonnements-ID waarin Azure Monitor logboeken worden geïmplementeerd. Geef deze parameter alleen door als de Azure Monitor-logboeken voor het blockchain-netwerk buiten de brongroep van Blockchain Workbench worden geïmplementeerd.| Nee |
| OmsResourceGroup |De brongroep waarin Azure Monitor-logboeken worden geïmplementeerd. Geef deze parameter alleen door als de Azure Monitor-logboeken voor het blockchain-netwerk buiten de brongroep van Blockchain Workbench worden geïmplementeerd.| Nee |
| OmsWorkspaceName | De naam van de Log Analytics-werkruimte. Geef deze parameter alleen door als de Azure Monitor-logboeken voor het blockchain-netwerk buiten de brongroep van Blockchain Workbench worden geïmplementeerd | Nee |

## <a name="what-is-collected"></a>Wat wordt er verzameld?

Het zip-bestand uitvoer bevat de volgende mapstructuur:

| Map of bestand | Beschrijving  |
|---------|---------|
| \Samenvatting.txt | Samenvatting van het systeem |
| \Metrics\blockchain | Statistieken over de blockchain |
| \Statistieken\Werkbank | Statistieken over de werkbank |
| \Details\Blockchain | Gedetailleerde logboeken over de blockchain |
| \Details\Werkbank | Gedetailleerde logboeken over de werkbank |

Het overzichtsbestand geeft u een momentopname van de algemene status van de toepassing en de status van de toepassing. Het overzicht bevat aanbevolen acties, hoogtepunten topfouten en metagegevens over het uitvoeren van services.

De map **Metrics** bevat statistieken van verschillende systeemonderdelen in de loop van de tijd. Het uitvoerbestand `\Details\Workbench\apiMetrics.txt` bevat bijvoorbeeld een overzicht van verschillende antwoordcodes en reactietijden gedurende de hele incassoperiode. De map **Details** bevat gedetailleerde logboeken voor het oplossen van specifieke problemen met Workbench of het onderliggende blockchain-netwerk. Bevat bijvoorbeeld `\Details\Workbench\Exceptions.csv` een lijst met de meest recente uitzonderingen die zich in het systeem hebben voorgedaan, wat handig is voor het oplossen van fouten met slimme contracten of interacties met de blockchain. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Handleiding voor het oplossen van problemen met Azure Blockchain Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
