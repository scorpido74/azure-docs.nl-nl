---
title: Azure Duurzame functies voor noodherstel en geodistributie
description: Meer informatie over noodherstel en geodistributie in duurzame functies.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 7951f216143bef0d48a6b751beff3f8f4316b9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433337"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Herstel na noodgevallen en geodistributie in duurzame azure-functies

In Duurzame functies blijft alle status bestaan in Azure Storage. Een [taakhub](durable-functions-task-hubs.md) is een logische container voor Azure Storage-resources die worden gebruikt voor orkestraties. Orchestrator- en activiteitsfuncties kunnen alleen met elkaar communiceren als ze tot dezelfde taakhub behoren.
De beschreven scenario's stellen implementatieopties voor om de beschikbaarheid te verhogen en downtime tijdens disaster recovery-activiteiten te minimaliseren.

Het is belangrijk om op te merken dat deze scenario's zijn gebaseerd op Active-Passive-configuraties, omdat ze worden geleid door het gebruik van Azure Storage. Dit patroon bestaat uit het implementeren van een back-up (passieve) functie-app naar een andere regio. Traffic Manager controleert de primaire (actieve) functie-app op beschikbaarheid. Het zal mislukken naar de back-up functie app als de primaire mislukt. Zie De [methode prioriteitsrouteringsmethode](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) voor [verkeersbeheer.](https://azure.microsoft.com/services/traffic-manager/)

>[!NOTE]
>
> - De voorgestelde Active-Passive configuratie zorgt ervoor dat een client altijd in staat is om nieuwe orkestraties via HTTP te activeren. Als gevolg van het hebben van twee functie-apps die dezelfde opslag delen, wordt de achtergrondverwerking echter over beide gedistribueerd, die concurreren om berichten in dezelfde wachtrijen. Deze configuratie brengt extra uitweidingskosten met zich mee voor de secundaire functie-app.
> - Het onderliggende opslagaccount en de taakhub worden gemaakt in het primaire gebied en worden gedeeld door beide functie-apps.
> - Alle functie-apps die redundant zijn geïmplementeerd, moeten dezelfde functietoegangssleutels delen in het geval dat deze via HTTP worden geactiveerd. Met De functie-runtime van functies wordt een [beheer-API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) blootgesteld waarmee consumenten functiesleutels programmatisch kunnen toevoegen, verwijderen en bijwerken.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenario 1 - Gebalanceerde rekenkracht laden met gedeelde opslag

Als de compute-infrastructuur in Azure mislukt, is de functie-app mogelijk niet meer beschikbaar. Om de kans op dergelijke downtime tot een minimum te beperken, maakt dit scenario gebruik van twee functie-apps die naar verschillende regio's zijn geïmplementeerd.
Traffic Manager is geconfigureerd om problemen in de primaire functie-app op te sporen en automatisch verkeer om te leiden naar de functie-app in de secundaire regio. Deze functie-app deelt hetzelfde Azure Storage-account en Taakhub. Daarom gaat de status van de functie-apps niet verloren en kan het werk normaal worden hervat. Zodra de status is hersteld naar het primaire gebied, start Azure Traffic Manager automatisch met het routeren van aanvragen naar die functie-app.

![Diagram met scenario 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Dit implementatiescenario zijn verschillende voordelen:

- Als de compute-infrastructuur mislukt, kan het werk in de fail over-regio worden hervat zonder verlies van de status.
- Traffic Manager zorgt voor de automatische fail over naar de gezonde functie app automatisch.
- Traffic Manager stelt het verkeer naar de primaire functie-app automatisch opnieuw in nadat de storing is verholpen.

Houd echter rekening met dit scenario:

- Als de functie-app wordt geïmplementeerd met een speciaal App Service-abonnement, verhoogt het repliceren van de compute-infrastructuur in het fail over-datacenter de kosten.
- Dit scenario heeft betrekking op uitval op de compute-infrastructuur, maar het opslagaccount blijft het enige storingspunt voor de functie-app. Als er een storing in de opslag is, heeft de toepassing te maken met een uitvaltijd.
- Als de functie-app is mislukt, wordt de latentie verhoogd omdat deze toegang krijgt tot het opslagaccount in verschillende regio's.
- Toegang tot de opslagservice vanuit een andere regio waar deze zich bevindt, brengt hogere kosten met zich mee als gevolg van netwerkverkeer.
- Dit scenario is afhankelijk van Traffic Manager. Gezien [de](../../traffic-manager/traffic-manager-how-it-works.md)werking van Traffic Manager kan het enige tijd duren voordat een clienttoepassing die een duurzame functie verbruikt, het functie-app-adres van Traffic Manager opnieuw moet opvragen.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenario 2 - Gebalanceerde rekenkracht laden met regionale opslag

Het vorige scenario heeft alleen betrekking op het geval van een storing in de rekeninfrastructuur. Als de opslagservice uitvalt, leidt dit tot een storing van de functie-app.
Om de duurzame functies continu te kunnen gebruiken, wordt in dit scenario gebruik gemaakt van een lokaal opslagaccount voor elke regio waarde functie-apps worden geïmplementeerd.

![Diagram met scenario 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Deze aanpak voegt verbeteringen toe aan het vorige scenario:

- Als de functie-app mislukt, zorgt Traffic Manager ervoor dat de functie-app niet naar de secundaire regio gaat. Omdat de functie-app echter afhankelijk is van een eigen opslagaccount, blijven de duurzame functies werken.
- Tijdens een fail-over is er geen extra latentie in de fail over-regio, omdat de functie-app en het opslagaccount naast elkaar zijn geplaatst.
- Als de opslaglaag niet optreedt, worden er storingen veroorzaakt in de duurzame functies, waardoor de omleiding naar de fail-over-regio wordt geactiveerd. Nogmaals, omdat de functie-app en -opslag per regio worden geïsoleerd, blijven de duurzame functies werken.

Belangrijke overwegingen voor dit scenario:

- Als de functie-app wordt geïmplementeerd met een speciaal AppService-abonnement, verhoogt het repliceren van de compute-infrastructuur in het fail over-datacenter de kosten.
- De huidige status is niet mislukt, wat betekent dat uitvoeren en controlepuntfuncties mislukken. Het is aan de clienttoepassing om het werk opnieuw te proberen/opnieuw te starten.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenario 3 - Gebalanceerde rekenkracht laden met gedeelde GRS-opslag

Dit scenario is een wijziging ten opzichte van het eerste scenario, waarbij een account voor gedeelde opslag wordt geïmplementeerd. Het belangrijkste verschil dat het opslagaccount is gemaakt met geo-replicatie ingeschakeld.
Functioneel biedt dit scenario dezelfde voordelen als scenario 1, maar het biedt extra voordelen voor gegevensherstel:

- Georedundante opslag (GRS) en Lees-toegang GRS (RA-GRS) maximaliseren de beschikbaarheid voor uw opslagaccount.
- Als er een regiostoring van de opslagservice is, is een van de mogelijkheden dat de datacenterbewerkingen bepalen dat de opslag moet worden mislukt naar de secundaire regio. In dit geval wordt toegang tot opslagaccounts transparant doorgestuurd naar de geo-gerepliceerde kopie van het opslagaccount, zonder tussenkomst van de gebruiker.
- In dit geval blijft de status van de duurzame functies behouden tot de laatste replicatie van het opslagaccount, dat om de paar minuten optreedt.

Net als bij de andere scenario's zijn er belangrijke overwegingen:

- Fail over naar de replica wordt gedaan door datacenter operators en het kan enige tijd duren. Tot die tijd heeft de functie-app last van een storing.
- Er zijn hogere kosten voor het gebruik van geo-gerepliceerde opslagaccounts.
- GRS treedt asynchroon op. Sommige van de laatste transacties kunnen verloren gaan als gevolg van de latentie van het replicatieproces.

![Diagram met scenario 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Volgende stappen

U meer lezen over [het ontwerpen van zeer beschikbare toepassingen met RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
