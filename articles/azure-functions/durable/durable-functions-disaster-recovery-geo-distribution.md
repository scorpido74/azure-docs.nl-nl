---
title: Herstel na nood gevallen en geo-distributie in Durable Functions-Azure
description: Meer informatie over herstel na nood gevallen en geo-distributie in Durable Functions.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: ba459d2d2f3aaf595c1d834e2b29a231e3b2bb12
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232945"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Herstel bij noodgeval en geodistributie

## <a name="overview"></a>Overzicht

In Durable Functions wordt alle status persistent gemaakt in Azure Storage. Een [Task hub](durable-functions-task-hubs.md) is een logische container voor Azure storage resources die worden gebruikt voor Orchestrations. Orchestrator-en activiteit functies kunnen alleen communiceren wanneer ze tot dezelfde taak hub behoren.
In de beschreven scenario's worden implementatie opties Voorst Ellen om de beschik baarheid te verhogen en uitval tijd te minimaliseren tijdens nood herstel activiteiten.

Het is belang rijk om te zien dat deze scenario's zijn gebaseerd op actieve, passieve configuraties, omdat ze worden begeleid door het gebruik van Azure Storage. Dit patroon bestaat uit het implementeren van een back-uptoepassing (passieve) functie naar een andere regio. Traffic Manager kunt de primaire (actieve) functie-app bewaken voor Beschik baarheid. Er wordt een failover naar de back-upfunctie-app uitgevoerd als de primaire fout is opgetreden. Zie de methode voor de [route ring](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) van [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)van prioriteit voor meer informatie.

>[!NOTE]
>
> - De voorgestelde Active-passieve configuratie zorgt ervoor dat een client altijd nieuwe integraties via HTTP kan activeren. Als er echter twee functie-apps zijn die dezelfde opslag delen, wordt de achtergrond verwerking tussen beide functies gedistribueerd, zodat er berichten in dezelfde wacht rijen worden geplaatst. Deze configuratie maakt de extra kosten voor de secundaire functie-app toe.
> - Het onderliggende opslag account en de taak-hub worden gemaakt in de primaire regio en worden gedeeld door beide functie-apps.
> - Alle functie-apps die redundant worden geïmplementeerd, moeten dezelfde functie toegangs sleutels delen in het geval van activering via HTTP. Met de functions-runtime wordt een [beheer-API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) beschikbaar gemaakt waarmee gebruikers functie sleutels programmatisch kunnen toevoegen, verwijderen en bijwerken.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenario 1: berekening met gelijke taak verdeling met gedeelde opslag

Als de reken infrastructuur in azure mislukt, is de functie-app mogelijk niet meer beschikbaar. Dit scenario maakt gebruik van twee functie-apps die zijn geïmplementeerd in verschillende regio's om de kans op een dergelijke downtime tot een minimum te beperken.
Traffic Manager is geconfigureerd om problemen in de primaire functie-app op te sporen en verkeer automatisch om te leiden naar de functie-app in de secundaire regio. Deze functie-app deelt dezelfde Azure Storage account en taak hub. Daarom is de status van de functie-apps niet verloren gegaan en kan het werk gewoon worden hervat. Zodra de status is hersteld naar de primaire regio, wordt door Azure Traffic Manager automatisch routerings aanvragen naar die functie-app gestart.

![Diagram waarin scenario 1 wordt weer gegeven.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Er zijn verschillende voor delen bij het gebruik van dit implementatie scenario:

- Als de berekenings infrastructuur mislukt, kan de werk stroom in de failover-regio zonder verlies van de status worden hervat.
- Traffic Manager zorgt ervoor dat automatisch een failover wordt uitgevoerd naar de functie-app in orde.
- Traffic Manager wordt automatisch het verkeer naar de primaire functie-app hersteld nadat de storing is opgelost.

Het gebruik van dit scenario kan echter overwegen:

- Als de functie-app wordt geïmplementeerd met behulp van een toegewezen App Service-abonnement, verhoogt u de kosten voor het repliceren van de reken infrastructuur in het failover-Data Center.
- Dit scenario heeft betrekking op storingen in de reken infrastructuur, maar het opslag account blijft de Single Point of Failure voor de functie-app. Als er een opslag storing optreedt, heeft de toepassing een downtime.
- Als er een failover wordt uitgevoerd voor de functie-app, wordt er een hogere latentie gebruikt omdat het opslag account voor de verschillende regio's wordt geopend.
- Toegang tot de opslag service vanuit een andere regio waar deze zich bevindt in hogere kosten als gevolg van netwerk uitgaand verkeer.
- Dit scenario is afhankelijk van Traffic Manager. Het kan enige tijd duren voordat een client toepassing die gebruikmaakt van een duurzame functie, het functie-app-adres van Traffic Manager opnieuw moet opvragen. [Traffic Manager](../../traffic-manager/traffic-manager-how-it-works.md)

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenario 2: berekening met gelijke taak verdeling met regionale opslag

In het voor gaande scenario wordt alleen het geval van storing in de reken infrastructuur behandeld. Als de opslag service uitvalt, resulteert dit in een onderbreking van de functie-app.
Dit scenario maakt gebruik van een lokaal opslag account voor elke regio waarop de functie-apps zijn geïmplementeerd, om een continue werking van de duurzame functies te garanderen.

![Diagram waarin scenario 2 wordt weer gegeven.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Met deze benadering worden verbeteringen toegevoegd aan het vorige scenario:

- Als de functie-app mislukt, neemt Traffic Manager de failover naar de secundaire regio. Omdat de functie-app echter gebruikmaakt van een eigen opslag account, blijven de duurzame functies werken.
- Tijdens een failover wordt er geen extra latentie in het failover-gebied gevonden, aangezien de functie-app en het opslag account zich in de co-locatie bevinden.
- Uitval van de opslaglaag veroorzaakt storingen in de duurzame functies, waardoor een omleiding wordt geactiveerd naar het failovergebied. Omdat de functie-app en opslag per regio zijn geïsoleerd, blijven de duurzame functies werken.

Belang rijke overwegingen voor dit scenario:

- Als de functie-app wordt geïmplementeerd met behulp van een speciaal AppService-plan, verhoogt de kosten voor het repliceren van de reken infrastructuur in het Data Center failover.
- Er is geen failover uitgevoerd voor de huidige status, wat impliceert dat het uitvoeren van functies en taken in de controle punten mislukt. Het is de client toepassing om het werk opnieuw te proberen of opnieuw te starten.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenario 3: berekening met gelijke taak verdeling met GRS gedeelde opslag

Dit scenario is een wijziging in het eerste scenario, waarbij een gedeeld opslag account wordt geïmplementeerd. Het belangrijkste verschil dat het opslag account is gemaakt terwijl geo-replicatie is ingeschakeld.
Dit scenario biedt functioneel dezelfde voor delen als scenario 1, maar maakt bijkomende voor delen van gegevens herstel mogelijk:

- Geo-redundante opslag (GRS) en GRS met lees toegang (RA-GRS) maximaliseren de beschik baarheid voor uw opslag account.
- Als er sprake is van een onderbreking van de opslag service, is een van de mogelijkheden dat de Data Center-bewerkingen bepalen dat er een failover naar de secundaire regio moet worden uitgevoerd voor de opslag. In dit geval wordt de toegang tot opslag accounts transparant omgeleid naar de geo-gerepliceerde kopie van het opslag account, zonder tussen komst van de gebruiker.
- In dit geval wordt de status van de duurzame functies bewaard tot de laatste replicatie van het opslag account, die om de paar minuten plaatsvindt.

Net als bij de andere scenario's zijn er belang rijke overwegingen:

- Een failover naar de replica wordt uitgevoerd door Data Center-Opera tors en kan enige tijd duren. Tot die tijd duurt de functie-app een storing.
- Er zijn meer kosten verbonden aan het gebruik van geo-gerepliceerde opslag accounts.
- GRS vindt asynchroon plaats. Enkele van de meest recente trans acties zijn mogelijk verloren gegaan vanwege de latentie van het replicatie proces.

![Diagram waarin scenario 3 wordt weer gegeven.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [ontwerpen van Maxi maal beschik bare toepassingen met Ra-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
