---
title: Onboarding in azure Sentinel preview | Microsoft Docs
description: Meer informatie over het verzamelen van gegevens in azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: rkarlin
ms.openlocfilehash: b609dc70c45941ec1132c7cdf614cf9bec8119ff
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019026"
---
# <a name="on-board-azure-sentinel-preview"></a>Azure Sentinel preview van on-Board

> [!IMPORTANT]
> Azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In deze Quick Start leert u hoe u aan de trein Azure-Sentinel kunt gaan. 

Als u Azure Sentinel wilt gebruiken, moet u eerst Azure Sentinel inschakelen en vervolgens verbinding maken met uw gegevens bronnen. Azure Sentinel wordt geleverd met een aantal connectors voor micro soft-oplossingen, die beschikbaar zijn in de doos en die realtime-integratie bieden, waaronder oplossingen voor micro soft Threat Protection, Microsoft 365 sources, waaronder Office 365, Azure AD, Azure ATP en Microsoft Cloud App Security, en nog veel meer. Daarnaast zijn er ingebouwde connectors voor het bredere beveiligings ecosysteem voor niet-micro soft-oplossingen. U kunt ook algemene gebeurtenis indeling, syslog of REST-API gebruiken om uw gegevens bronnen met Azure Sentinel te verbinden.  

Nadat u verbinding hebt gemaakt met uw gegevens bronnen, kiest u uit een galerie met deskundig gemaakte Dash boards op basis van uw gegevens. Deze Dash boards kunnen eenvoudig worden aangepast aan uw behoeften.


## <a name="global-prerequisites"></a>Globale vereisten

- Actief Azure-abonnement: als u er nog geen hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- Log Analytics werk ruimte. Meer informatie over het [maken van een log Analytics-werk ruimte](../log-analytics/log-analytics-quick-create-workspace.md)

-  Als u Azure Sentinel wilt inschakelen, hebt u Inzender machtigingen nodig voor het abonnement waarin de Azure Sentinel-werk ruimte zich bevindt. 
- Als u Azure Sentinel wilt gebruiken, hebt u Inzender-of lezer-machtigingen nodig voor de resource groep waartoe de werk ruimte behoort
- Er zijn mogelijk extra machtigingen nodig om verbinding te maken met specifieke gegevens bronnen
 
## Azure Sentinel inschakelen<a name="enable"></a>

1. Ga naar het Azure Portal.
2. Zorg ervoor dat het abonnement waarin Azure Sentinel is gemaakt, is geselecteerd. 
3. Zoeken naar de Azure-Sentinel. 
   ![opdracht](./media/quickstart-onboard/search-product.png)

1. Klik op **+ toevoegen**.
1. Selecteer de werk ruimte die u wilt gebruiken of maak een nieuwe. U kunt Azure Sentinel uitvoeren op meer dan één werk ruimte, maar de gegevens worden geïsoleerd voor één werk ruimte.

   ![zoeken](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Standaardwerk ruimten die zijn gemaakt door Azure Security Center worden niet weer gegeven in de lijst. u kunt geen Azure-Sentinel installeren.
   > - Azure Sentinel kan worden uitgevoerd op werk ruimten die in een van de volgende regio's worden geïmplementeerd: Australië-oost, Australië-zuidoost, Canada-centraal, Centraal-India, VS Oost, VS-Oost 2 EUAP (Canarische), Japan-Oost, Zuidoost-Azië, UK-zuid, Europa-west, VS-West 2, VS-West-Centraal, Frankrijk-centraal, Korea-centraal, Europa-noord, VS-Oost 2, Azië-oost, VS-West, centraal VS, VS Zuid-Centraal.


6. Klik op **Azure-Sentinel toevoegen**.
  

## <a name="connect-data-sources"></a>Verbinding maken met gegevensbronnen

Azure Sentinel maakt de verbinding met Services en apps door verbinding te maken met de service en de gebeurtenissen en logboeken door te sturen naar Azure Sentinel. Voor machines en virtuele machines kunt u de Azure Sentinel-agent installeren waarmee de logboeken worden verzameld en doorgestuurd naar Azure Sentinel. Voor firewalls en proxy's maakt Azure Sentinel gebruik van een Linux syslog-server. De agent is geïnstalleerd en van waaruit de agent de logboek bestanden verzamelt en doorstuurt naar Azure Sentinel. 
 
1. Klik op **gegevens verzameling**.
2. Er is een tegel voor elke gegevens bron waarmee u verbinding kunt maken.<br>
Klik bijvoorbeeld op **Azure Active Directory**. Als u deze gegevens bron verbindt, streamt u alle logboeken van Azure AD naar Azure Sentinel. U kunt selecteren welk type logboeken u in WAN wilt ontvangen van Logboeken en/of audit Logboeken. <br>
Aan de onderkant biedt Azure Sentinel aanbevelingen voor de Dash boards die u voor elke connector moet installeren, zodat u direct interessante inzichten kunt krijgen in uw gegevens. <br> Volg de installatie-instructies of [Raadpleeg de relevante verbindings gids](connect-data-sources.md) voor meer informatie. Zie [verbinding maken met micro soft-Services](connect-data-sources.md)voor meer informatie over gegevens connectors.

Nadat uw gegevens bronnen zijn verbonden, worden uw gegevens streamen naar Azure Sentinel en kunt u aan de slag gaan met. U kunt de logboeken weer geven in de [ingebouwde Dash boards](quickstart-get-visibility.md) en beginnen met het bouwen van query's in log Analytics om [de gegevens te onderzoeken](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u gegevens bronnen verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).
- Gegevens streamen van [algemene fout indelings toestellen](connect-common-event-format.md) in azure Sentinel.
