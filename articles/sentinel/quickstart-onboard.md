---
title: 'Quickstart: Onboarden in Azure Sentinel'
description: In deze quickstart ontdekt u hoe u Azure Sentinel kunt onboarden door eerst Sentinel in te schakelen en vervolgens gegevensbronnen te verbinden.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: e9d7c99a123bd92bf55a33c8d1faaf7da55d3e36
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889027"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Quickstart: Azure Sentinel onboarden

In deze Quick Start leert u hoe u Azure Sentinel kunt onboarden. 

Om Azure Sentinel te onboarden, moet u eerst Azure Sentinel inschakelen en vervolgens uw gegevensbronnen verbinden. Azure Sentinel wordt geleverd met een aantal connectors voor Microsoft-oplossingen, die direct beschikbaar zijn en realtime integratie bieden, inclusief Microsoft 365 Defender (voorheen Microsoft Threat Protection)-oplossingen, Microsoft 365-bronnen, waaronder Office 365, Azure AD, Microsoft Defender for Identity (voorheen Azure ATP), Microsoft Cloud App Security, Azure Defender-waarschuwingen van Azure Security Center, en meer. Daarnaast zijn er ingebouwde connectors voor het bredere beveiligingsecosysteem voor niet-Microsoft-oplossingen. U kunt ook Common Event Format (CEF), Syslog of REST-API gebruiken om uw gegevensbronnen met Azure Sentinel te verbinden. 

Nadat u verbinding hebt gemaakt met uw gegevensbronnen, kiest u uit een galerie van deskundig gemaakte werkboeken die op basis van uw gegevens inzichten aan het licht brengen. Deze werkmappen kunnen gemakkelijk worden aangepast aan uw behoeften.

>[!IMPORTANT] 
> Zie [Prijzen voor Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)voor meer informatie over de kosten voor het gebruik van Azure Sentinel.
  

## <a name="global-prerequisites"></a>Globale vereisten

- Een actief Azure-abonnement. Als u dit niet hebt, kunt u een [gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maken voordat u begint.

- Log Analytics-werkruimte. Leer [een Log Analytics-werkruimte maken](../log-analytics/log-analytics-quick-create-workspace.md). Zie [De implementatie van uw Azure Monitor-logboeken ontwerpen](../azure-monitor/platform/design-logs-deployment.md)voor meer informatie over Log Analytics-werkruimten.

- Om Azure Sentinel in te schakelen, hebt u inzendersmachtigingen nodig voor het abonnement waarin de Azure Sentinel-werkruimte zich bevindt. 
- Om Azure Sentinel te gebruiken, hebt u inzenders- of lezersmachtigingen nodig voor de resourcegroep waartoe de werkruimte behoort.
- Er zijn mogelijk extra machtigingen nodig om verbinding te maken met specifieke gegevensbronnen.
- Azure Sentinel is een betaalde service. Zie [Prijzen voor Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058) voor prijsinformatie.
 
## <a name="enable-azure-sentinel"></a>Azure Sentinel inschakelen <a name="enable"></a>

1. Meld u aan bij Azure Portal. Zorg ervoor dat het abonnement waarin Azure Sentinel wordt gemaakt, is geselecteerd.

1. Zoek en selecteer **Azure Sentinel**.

   ![Services zoeken](./media/quickstart-onboard/search-product.png)

1. Selecteer **Toevoegen**.

1. Selecteer de werkruimte die u wilt gebruiken of maak een nieuwe. U kunt Azure Sentinel uitvoeren op meer dan één werkruimte, maar de gegevens worden geïsoleerd in één werkruimte.

   ![Een werkruimte kiezen](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Door Azure Security Center gemaakte standaardwerkruimten worden niet weergegeven in de lijst; u kunt Azure Sentinel er niet op installeren.
   > - Azure Sentinel kan worden uitgevoerd op werkruimten in elke [GA-regio van Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), behalve de regio's China en Duitsland (soevereine regio's). Door Azure Sentinel gegenereerde gegevens (zoals incidenten, bladwijzers en waarschuwingsregels die klantgegevens uit deze werkruimten kunnen bevatten) worden opgeslagen in Europa (voor werkruimten in Europa) of in US - oost (voor werkruimten in andere regio's).

   >[!IMPORTANT]
   >
   > - Azure Sentinel **ondersteunt momenteel niet** de verplaatsing van een werkruimte naar andere resourcegroepen of abonnement na de implementatie in deze werkruimte. 
   >
   >   Als u de werkruimte al hebt verplaatst, moet u alle actieve regels onder **Analyses** uitschakelen en na vijf minuten opnieuw inschakelen. In de meeste gevallen is dit effectief, hoewel de methode niet wordt ondersteund en voor eigen risico wordt uitgevoerd.

1. Selecteer **Azure Sentinel toevoegen**.

## <a name="connect-data-sources"></a>Verbinding maken met gegevensbronnen

Azure Sentinel maakt de verbinding met services en apps door verbinding te maken met de service en de gebeurtenissen en logboeken door te sturen naar Azure Sentinel. Voor computers en virtuele machines kunt u de Azure Sentinel-agent installeren waarmee de logboeken worden verzameld en doorgestuurd naar Azure Sentinel. Voor firewalls en proxy's maakt Azure Sentinel gebruik van een Linux Syslog-server. De agent wordt daarop geïnstalleerd en verzamelt daar de logboekbestanden en stuurt ze door naar Azure Sentinel. 
 
1. Klik op **Gegevensconnectors**.
1. Er is een tegel voor elke gegevensbron waarmee u verbinding kunt maken.<br>
Klik bijvoorbeeld op **Azure Active Directory**. Als u deze gegevensbron verbindt, streamt u alle logboeken van Azure AD naar Azure Sentinel. U kunt selecteren welk type logboeken u wilt hebben: aanmeldingslogboeken en/of controlelogboeken. <br>
Aan de onderkant doet Azure Sentinel aanbevelingen voor de werkmappen die u voor elke connector moet installeren, zodat u direct interessante inzichten kunt krijgen in uw gegevens. <br> Volg de installatie-instructies of [raadpleeg de relevante verbindingshandleiding](connect-data-sources.md) voor meer informatie. Zie [Connect Microsoft services](connect-data-sources.md) (Microsoft-services verbinden) voor meer informatie over gegevensconnectors.

Nadat uw gegevensbronnen zijn verbonden, worden uw gegevens gestreamd naar Azure Sentinel en kunt u ermee aan de slag gaan. U kunt de logboeken in de [ingebouwde dashboards](quickstart-get-visibility.md) weergeven en beginnen met het bouwen van query's in Log Analytics om [de gegevens te onderzoeken](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u gegevensbronnen verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- Gegevens streamen van [Common Event Format-apparaten](connect-common-event-format.md) naar Azure Sentinel.
