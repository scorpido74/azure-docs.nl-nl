---
title: 'Snelstart: aan boord in Azure Sentinel'
description: Meer informatie over het verzamelen van gegevens in Azure Sentinel door dit Quickstart-document te volgen.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 11fecd875385d8ba044cbe44e2270eed11d61ce1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77581546"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Snelstart: Azure Sentinel aan boord

In deze quickstart leert u hoe u Azure Sentinel aan boord nemen. 

Als u Azure Sentinel aan boord wilt hebben, moet u Azure Sentinel eerst inschakelen en vervolgens uw gegevensbronnen verbinden. Azure Sentinel wordt geleverd met een aantal connectors voor Microsoft-oplossingen, die uit de doos beschikbaar zijn en realtime integratie bieden, waaronder Microsoft Threat Protection-oplossingen, Microsoft 365-bronnen, waaronder Office 365, Azure AD, Azure ATP en Microsoft Cloud App-beveiliging en meer. Daarnaast zijn er ingebouwde connectors voor het bredere beveiligingsecosysteem voor niet-Microsoft-oplossingen. U ook algemene gebeurtenisindeling, Syslog of REST-API gebruiken om uw gegevensbronnen te verbinden met Azure Sentinel.  

Nadat u uw gegevensbronnen hebt verbonden, u kiezen uit een galerij met vakkundig gemaakte werkmappen die inzichten op basis van uw gegevens weergeven. Deze werkmappen kunnen eenvoudig worden aangepast aan uw behoeften.

>[!IMPORTANT] 
> Zie [Azure Sentinel-prijzen](https://azure.microsoft.com/pricing/details/azure-sentinel/)voor informatie over de kosten die zijn gemaakt bij het gebruik van Azure Sentinel.
  

## <a name="global-prerequisites"></a>Globale vereisten

- Active Azure Subscription, als u er geen hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- Log Analytics-werkruimte. Meer informatie over het [maken van een Log Analytics-werkruimte](../log-analytics/log-analytics-quick-create-workspace.md). Zie Het ontwerpen van de [implementatie van Azure Monitor Logs](../azure-monitor/platform/design-logs-deployment.md)voor meer informatie over Logboekanalyse-werkruimten.

- Als u Azure Sentinel wilt inschakelen, hebt u machtigingen voor bijdragen nodig voor het abonnement waarin de Azure Sentinel-werkruimte zich bevindt. 
- Als u Azure Sentinel wilt gebruiken, hebt u inzender- of lezermachtigingen nodig voor de brongroep waartoe de werkruimte behoort.
- Mogelijk zijn er aanvullende machtigingen nodig om specifieke gegevensbronnen met elkaar te verbinden.
- Azure Sentinel is een betaalde service. Zie Informatie [over Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058)voor prijsinformatie .
 
## <a name="enable-azure-sentinel"></a>Azure Sentinel inschakelen<a name="enable"></a>

1. Meld u aan bij Azure Portal. Controleer of het abonnement waarin Azure Sentinel is gemaakt, is geselecteerd.

1. Azure **Sentinel**zoeken en selecteren .

   ![zoeken](./media/quickstart-onboard/search-product.png)

1. Selecteer **Toevoegen**.

1. Selecteer de werkruimte die u wilt gebruiken of maak een nieuwe werkruimte. U Azure Sentinel op meer dan één werkruimte uitvoeren, maar de gegevens worden geïsoleerd tot één werkruimte.

   ![zoeken](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Standaardwerkruimten die zijn gemaakt door Azure Security Center, worden niet weergegeven in de lijst. U Azure Sentinel er niet op installeren.
   > - Azure Sentinel kan worden uitgevoerd op werkruimten in elke [GA-regio van Log Analytics,](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) behalve de regio's China, Duitsland en Azure Government. Gegevens die worden gegenereerd door Azure Sentinel (zoals incidenten, bladwijzers en waarschuwingsregels, die sommige klantgegevens uit deze werkruimten kunnen bevatten) worden opgeslagen in West-Europa (voor werkruimten in Europa) of Oost-VS (voor alle werkruimten in de VS, evenals andere regio behalve Europa).

1. Selecteer **Azure Sentinel toevoegen**.
  

## <a name="connect-data-sources"></a>Verbinding maken met gegevensbronnen

Azure Sentinel maakt de verbinding met services en apps door verbinding te maken met de service en de gebeurtenissen en logboeken door te sturen naar Azure Sentinel. Voor machines en virtuele machines u de Azure Sentinel-agent installeren die de logboeken verzamelt en doorstuurt naar Azure Sentinel. Voor firewalls en proxy's maakt Azure Sentinel gebruik van een Linux Syslog-server. De agent is geïnstalleerd op het en van waaruit de agent verzamelt de logbestanden en stuurt ze door naar Azure Sentinel. 
 
1. Klik **op Gegevensverzameling**.
2. Er is een tegel voor elke gegevensbron die u verbinden.<br>
Klik bijvoorbeeld op **Azure Active Directory**. Als u deze gegevensbron verbindt, streamt u alle logboeken van Azure AD naar Azure Sentinel. U selecteren welk type logboeken u wilt krijgen - aanmeldingslogboeken en/of controlelogboeken. <br>
Onderaan geeft Azure Sentinel aanbevelingen voor welke werkmappen u voor elke connector moet installeren, zodat u onmiddellijk interessante inzichten over uw gegevens krijgen. <br> Volg de installatie-instructies of [raadpleeg de relevante verbindingsgids](connect-data-sources.md) voor meer informatie. Zie [Microsoft-services verbinden](connect-data-sources.md)voor informatie over gegevensconnectoren.

Nadat uw gegevensbronnen zijn verbonden, worden uw gegevens gestreamd naar Azure Sentinel en u klaar zijn om mee te werken. U de logboeken in de [ingebouwde dashboards](quickstart-get-visibility.md) bekijken en query's gaan bouwen in Log Analytics om de gegevens te [onderzoeken.](tutorial-investigate-cases.md)



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u informatie geleerd over het koppelen van gegevensbronnen aan Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- Gegevens van [apparaten met common eventformat-indeling](connect-common-event-format.md) en naar Azure Sentinel streamen.
