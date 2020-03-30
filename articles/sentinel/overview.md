---
title: Wat is Azure Sentinel?| Microsoft Documenten
description: Meer informatie over Azure Sentinel, de belangrijkste mogelijkheden en hoe het werkt.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 7d0f246a96072a6e433b0762256467f1c20a45fe
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77581715"
---
# <a name="what-is-azure-sentinel"></a>Wat is Azure Sentinel?

Microsoft Azure Sentinel is een schaalbare, cloud-native SIEM **(Security Information Event Management)** en **security orchestration automated response (SOAR)** oplossing. Azure Sentinel levert intelligente beveiligingsanalyses en bedreigingsinformatie in de hele onderneming en biedt één oplossing voor waarschuwingsdetectie, zichtbaarheid van bedreigingen, proactieve jacht en bedreigingsrespons. 

Azure Sentinel is uw vogelvlucht over de hele onderneming en verlicht de stress van steeds geavanceerdere aanvallen, toenemende volumes waarschuwingen en lange resolutietermijnen.

- **Verzamel gegevens op cloudschaal** voor alle gebruikers, apparaten, toepassingen en infrastructuur, zowel on-premises als in meerdere clouds. 

- **Detecteer eerder onopgemerkte bedreigingen**en minimaliseer false positives met behulp van Microsoft's analytics en ongeëvenaarde threat intelligence. 

- **Onderzoek bedreigingen met kunstmatige intelligentie**en jaag op verdachte activiteiten op schaal en maak gebruik van jarenlang cyberbeveiligingswerk bij Microsoft. 

- **Reageer snel op incidenten** met ingebouwde orkestratie en automatisering van veelvoorkomende taken.

![Azure Sentinel-kernmogelijkheden](./media/overview/core-capabilities.png)

Azure Sentinel bouwt voort op het volledige scala van bestaande Azure-services en bevat in native bewezen stichtingen, zoals Log Analytics en Logic Apps. Azure Sentinel verrijkt uw onderzoek en detectie met AI, biedt microsoft's threat intelligence stream en stelt u in staat om uw eigen bedreigingsinformatie mee te nemen. 

## <a name="connect-to-all-your-data"></a>Verbinding maken met al uw gegevens

Als u Azure Sentinel aan boord wilt hebben, moet u eerst [verbinding maken met uw beveiligingsbronnen.](connect-data-sources.md) Azure Sentinel wordt geleverd met een aantal connectors voor Microsoft-oplossingen, die uit de doos beschikbaar zijn en realtime integratie bieden, waaronder Microsoft Threat Protection-oplossingen en Microsoft 365-bronnen, waaronder Office 365, Azure AD, Azure ATP en Microsoft Cloud App-beveiliging en meer. Daarnaast zijn er ingebouwde connectors voor het bredere beveiligingsecosysteem voor niet-Microsoft-oplossingen. U ook algemene gebeurtenisindeling, Syslog of REST-API gebruiken om uw gegevensbronnen ook met Azure Sentinel te verbinden.  

![Gegevensverzamelaars](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Werkmappen

Nadat u [uw gegevensbronnen](quickstart-onboard.md) hebt verbonden met Azure Sentinel, u de gegevens controleren met behulp van de Azure Sentinel-integratie met Azure Monitor-werkmappen, die veelzijdigheid biedt bij het maken van aangepaste werkmappen. Hoewel werkmappen anders worden weergegeven in Azure Sentinel, kan het handig zijn om te zien hoe u [interactieve rapporten maken met Azure Monitor-werkmappen.](../azure-monitor/app/usage-workbooks.md) Met Azure Sentinel u aangepaste werkmappen maken voor uw gegevens en wordt ook ingebouwde werkmapsjablonen geleverd waarmee u snel inzicht krijgen in uw gegevens zodra u een gegevensbron aansluit.

![Dashboards](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analyse

Om u te helpen ruis te verminderen en het aantal waarschuwingen te minimaliseren dat u moet controleren en onderzoeken, gebruikt Azure Sentinel [analyses om waarschuwingen te correleren tot incidenten.](tutorial-detect-threats-built-in.md) **Incidenten** zijn groepen gerelateerde waarschuwingen die samen een bruikbare mogelijke bedreiging creëren die u onderzoeken en oplossen. Gebruik de ingebouwde correlatieregels zoals deze is, of gebruik ze als uitgangspunt om je eigen regels te bouwen. Azure Sentinel biedt ook machine learning-regels om uw netwerkgedrag in kaart te brengen en vervolgens te zoeken naar afwijkingen in uw bronnen. Deze analyses verbinden de punten door waarschuwingen met lage betrouwbaarheid over verschillende entiteiten te combineren tot potentiële high-fidelity beveiligingsincidenten.

![Incidenten](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Beveiligingsautomatisering & orkestratie

Automatiseer uw veelvoorkomende taken en [vereenvoudig beveiligingsorkestratie met playbooks](tutorial-respond-threats-playbook.md) die worden geïntegreerd met Azure-services en uw bestaande hulpprogramma's. De automatiserings- en orkestratieoplossing van Azure Sentinel is gebaseerd op de basis van Azure Logic Apps en biedt een zeer uitbreidbare architectuur die schaalbare automatisering mogelijk maakt naarmate nieuwe technologieën en bedreigingen ontstaan. Als u playbooks wilt maken met Azure Logic Apps, u kiezen uit een groeiende galerij met ingebouwde playbooks. Deze omvatten [meer dan 200 connectoren](https://docs.microsoft.com/azure/connectors/apis-list) voor services zoals Azure-functies. Met de connectors u elke aangepaste logica toepassen in code, ServiceNow, Jira, Zendesk, HTTP-aanvragen, Microsoft Teams, Slack, Windows Defender ATP en Cloud App Security.

Als u bijvoorbeeld het ServiceNow-ticketingsysteem gebruikt, u de meegeleverde hulpprogramma's gebruiken om Azure Logic Apps te gebruiken om uw werkstromen te automatiseren en elke keer dat een bepaalde gebeurtenis wordt gedetecteerd een ticket in ServiceNow te openen.

![Playbooks](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Onderzoek

Momenteel in preview, Azure Sentinel [deep investigation](tutorial-investigate-cases.md) tools helpen u om de reikwijdte te begrijpen en de hoofdoorzaak te vinden, van een potentiële bedreiging voor de veiligheid. U een entiteit in de interactieve grafiek kiezen om interessante vragen te stellen voor een specifieke entiteit en inzoomen op die entiteit en de verbindingen om naar de hoofdoorzaak van de bedreiging te gaan. 

![Onderzoek](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Zoeken

Gebruik de [krachtige zoek- en querytools](hunting.md)van Azure Sentinel op basis van het MITRE-framework, waarmee u proactief zoeken naar beveiligingsbedreigingen in de gegevensbronnen van uw organisatie voordat een waarschuwing wordt geactiveerd. Nadat u hebt ontdekt welke jachtquery waardevolle inzichten biedt in mogelijke aanvallen, u ook aangepaste detectieregels maken op basis van uw zoekopdracht en deze inzichten weergeven als waarschuwingen voor uw beveiligingsincidentresponders. Tijdens de jacht u bladwijzers maken voor interessante gebeurtenissen, zodat u er later op terugkomen, ze met anderen delen en ze groeperen met andere correlerende gebeurtenissen om een aantrekkelijk incident voor onderzoek te creëren.

![Zoeken](./media/overview/hunting.png)

## <a name="community"></a>Community

De Azure Sentinel-community is een krachtige bron voor bedreigingsdetectie en -automatisering. Onze Microsoft-beveiligingsanalisten maken en voegen voortdurend nieuwe werkmappen, playbooks, jachtquery's en meer toe en posten ze in de community die u in uw omgeving gebruiken. U voorbeeldinhoud downloaden uit de [repository](https://aka.ms/asicommunity) GitHub-opslagplaats voor privécommunity om aangepaste werkmappen, jachtquery's, notitieblokken en playbooks voor Azure Sentinel te maken. 

![Community](./media/overview/community.png)

## <a name="next-steps"></a>Volgende stappen

- Om aan de slag te gaan met Azure Sentinel, hebt u een abonnement op Microsoft Azure nodig. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/).
- Meer informatie over het [gebruik van uw gegevens naar Azure Sentinel](quickstart-onboard.md)en inzicht krijgen in uw gegevens en potentiële [bedreigingen.](quickstart-get-visibility.md)
