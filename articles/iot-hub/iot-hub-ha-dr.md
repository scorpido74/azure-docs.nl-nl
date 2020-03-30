---
title: Azure IoT Hub hoge beschikbaarheid en herstel na noodgevallen | Microsoft Documenten
description: Beschrijft de Azure- en IoT Hub-functies waarmee u zeer beschikbare Azure IoT-oplossingen bouwen met mogelijkheden voor noodherstel.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: philmea
ms.openlocfilehash: 615dc1b7bd1a31069a542ebb7ea44693c404cb40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499109"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Hoge beschikbaarheid en herstel na noodgevallen van IoT Hub

Als eerste stap naar de implementatie van een veerkrachtige IoT-oplossing moeten architecten, ontwikkelaars en bedrijfseigenaren de uptime-doelen definiëren voor de oplossingen die ze bouwen. Deze doelen kunnen in de eerste plaats worden gedefinieerd op basis van specifieke bedrijfsdoelstellingen voor elk scenario. In dit kader wordt in het artikel [Azure Business Continuity Technical Guidance](https://docs.microsoft.com/azure/architecture/resiliency/) een algemeen framework beschreven om u te helpen na te denken over bedrijfscontinuïteit en disaster recovery. Het [document Disaster recovery en hoge beschikbaarheid voor Azure-toepassingen](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery) biedt architectuurrichtlijnen voor strategieën voor Azure-toepassingen om HA (High Availability) en Disaster Recovery (DR) te bereiken.

Dit artikel bespreekt de HA- en DR-functies die specifiek worden aangeboden door de IoT Hub-service. De brede gebieden die in dit artikel worden besproken zijn:

- Intraregio HA
- Cross regio DR
- Het bereiken van cross regio HA

Afhankelijk van de uptime-doelen die u definieert voor uw IoT-oplossingen, moet u bepalen welke van de onderstaande opties het beste bij uw bedrijfsdoelstellingen passen. Het opnemen van een van deze HA/DR-alternatieven in uw IoT-oplossing vereist een zorgvuldige evaluatie van de afwegingen tussen:

- Mate van veerkracht die u nodig hebt 
- Complexiteit van implementatie en onderhoud
- COGS impact

## <a name="intra-region-ha"></a>Intraregio HA

De IoT Hub-service biedt ha binnen de regio door redundantie in bijna alle lagen van de service te implementeren. De [SLA gepubliceerd door de IoT Hub service](https://azure.microsoft.com/support/legal/sla/iot-hub) wordt bereikt door gebruik te maken van deze redundantie. De ontwikkelaars van een IoT-oplossing hebben geen extra werk nodig om van deze HA-functies te kunnen profiteren. Hoewel IoT Hub een redelijk hoge uptime-garantie biedt, kunnen tijdelijke storingen nog steeds worden verwacht zoals bij elk gedistribueerd computerplatform. Als u nog maar net begonnen bent met het migreren van uw oplossingen naar de cloud vanuit een on-premises oplossing, moet uw focus verschuiven van het optimaliseren van "gemiddelde tijd tussen storingen" naar "gemiddelde tijd om te herstellen". Met andere woorden, tijdelijke fouten moeten als normaal worden beschouwd tijdens het werken met de cloud in de mix. Er moet een geschikt [beleid voor nieuwe try's](iot-hub-reliability-features-in-sdks.md) worden ingebouwd in de componenten die met een cloudtoepassing worden interactie om tijdelijke fouten op te lossen.

> [!NOTE]
> Sommige Azure-services bieden ook extra lagen van beschikbaarheid binnen een regio door te integreren met [Beschikbaarheidszones (AZ's).](../availability-zones/az-overview.md) AZ's worden momenteel niet ondersteund door de IoT Hub-service.

## <a name="cross-region-dr"></a>Cross regio DR

Er kunnen enkele zeldzame situaties zijn waarin een datacenter langdurige uitval ervaart als gevolg van stroomstoringen of andere storingen met fysieke assets. Dergelijke gebeurtenissen zijn zeldzaam waarbij de hierboven beschreven HA-capaciteit binnen de regio niet altijd kan helpen. IoT Hub biedt meerdere oplossingen voor het herstellen van dergelijke langdurige storingen. 

De herstelopties die beschikbaar zijn voor klanten in een dergelijke situatie zijn [door Microsoft geïnitieerde failover](#microsoft-initiated-failover) en [handmatige failover.](#manual-failover) Het fundamentele verschil tussen de twee is dat Microsoft de eerste initieert en de gebruiker initieert de laatste. Handmatige failover biedt ook een doelstelling voor een lagere hersteltijd (RTO) in vergelijking met de door Microsoft geïnitieerde failover-optie. De specifieke OTO's die bij elke optie worden aangeboden, worden in de onderstaande secties besproken. Wanneer een van deze opties voor het uitvoeren van failover van een IoT-hub vanuit de primaire regio wordt uitgeoefend, wordt de hub volledig functioneel in het overeenkomstige [geo-gekoppelde Azure-gebied.](../best-practices-availability-paired-regions.md)

Beide failoveropties bieden de volgende doelstellingen voor herstelpunten (RPO's):

| Gegevenstype | Doelstellingen herstelpunt (RPO) |
| --- | --- |
| Identiteitsregister |0-5 minuten verlies van gegevens |
| Dubbele apparaatgegevens |0-5 minuten verlies van gegevens |
| Cloud-to-device berichten<sup>1</sup> |0-5 minuten verlies van gegevens |
| Bovenliggende<sup>1-</sup> en apparaattaken |0-5 minuten verlies van gegevens |
| Apparaat-naar-cloud-berichten |Alle ongelezen berichten gaan verloren |
| Berichten voor het bewaken van bewerkingen |Alle ongelezen berichten gaan verloren |
| Feedbackberichten van cloud naar apparaat |Alle ongelezen berichten gaan verloren |

<sup>1.</sup> Cloud-to-device-berichten en bovenliggende taken worden niet hersteld als onderdeel van handmatige failover.

Zodra de failoverbewerking voor de IoT-hub is voltooid, wordt verwacht dat alle bewerkingen van het apparaat en back-endtoepassingen blijven werken zonder handmatige tussenkomst. Dit betekent dat uw device-to-cloud-berichten moeten blijven werken en dat het hele apparaatregister intact is. Gebeurtenissen die via Event Grid worden uitgezonden, kunnen worden verbruikt via hetzelfde abonnement(en) dat eerder is geconfigureerd, zolang deze Event Grid-abonnementen beschikbaar blijven.

> [!CAUTION]
> - De naam en het eindpunt van het ingebouwde eindpunt van de IoT Hub na failover en geconfigureerde consumentengroepen worden verwijderd (dit is een bug die vóór mei 2020 wordt opgelost). Wanneer u telemetrieberichten ontvangt van het ingebouwde eindpunt met behulp van de Event Hub-client of de host van de gebeurtenisprocessor, moet u [de IoT-hubverbindingstekenreeks gebruiken](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) om de verbinding tot stand te brengen. Dit zorgt ervoor dat uw back-end toepassingen blijven werken zonder handmatige interventie post failover. Als u de naam en het eindpunt van de Gebeurtenishub rechtstreeks in uw toepassing gebruikt, moet u [de consumentengroep die ze gebruiken opnieuw configureren en het nieuwe eindpunt voor gebeurtenishub-compatibel ophalen](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) na failover om de activiteiten voort te zetten. Als u Azure Functions of Azure Stream Analytics gebruikt om het ingebouwde eindpunt met elkaar te verbinden, moet u mogelijk een **opnieuw opstarten**uitvoeren.
>
> - Wanneer u naar opslag routert, raden we u aan de blobs of bestanden te vermelden en er vervolgens overheen te herhalen, om ervoor te zorgen dat alle blobs of bestanden worden gelezen zonder aannames van partitie te maken. Het partitiebereik kan mogelijk veranderen tijdens een door Microsoft geïnitieerde failover of handmatige failover. U de [API Lijstblobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) gebruiken om de lijst met blobs of [Lijst ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) op te sommen voor de lijst met bestanden. 

## <a name="microsoft-initiated-failover"></a>Door Microsoft geïnitieerde failover

Door Microsoft geïnitieerde failover wordt door Microsoft in zeldzame situaties uitgeoefend om alle IoT-hubs van een getroffen regio naar het overeenkomstige geo-gekoppelde gebied te failoveren. Dit proces is een standaardoptie (geen manier voor gebruikers om zich af te melden) en vereist geen tussenkomst van de gebruiker. Microsoft behoudt zich het recht voor om te bepalen wanneer deze optie zal worden uitgeoefend. Dit mechanisme houdt geen toestemming van de gebruiker in voordat de hub van de gebruiker is mislukt. Door Microsoft geïnitieerde failover heeft een hersteltijddoelstelling (RTO) van 2-26 uur. 

De grote RTO is omdat Microsoft de failover-bewerking moet uitvoeren namens alle getroffen klanten in die regio. Als u een minder kritieke IoT-oplossing uitvoert die een uitvaltijd van ongeveer een dag kan ondersteunen, is het goed dat u afhankelijk bent van deze optie om te voldoen aan de algemene doelen voor herstel van rampen voor uw IoT-oplossing. De totale tijd voor runtime-bewerkingen om volledig operationeel te worden zodra dit proces wordt geactiveerd, wordt beschreven in de sectie 'Tijd om te herstellen'.

## <a name="manual-failover"></a>Handmatige failover

Als uw zakelijke uptime-doelen niet worden gehaald door de RTO die door Microsoft is gestart met failover, u overwegen om handmatige failover te gebruiken om het failoverproces zelf te activeren. De RTO met behulp van deze optie kan overal tussen de 10 minuten tot een paar uur. De RTO is momenteel een functie van het aantal apparaten geregistreerd tegen de IoT-hub instantie wordt mislukt. U verwachten dat de RTO voor een hub hosting ongeveer 100.000 apparaten te worden in het honkbalveld van 15 minuten. De totale tijd voor runtime-bewerkingen om volledig operationeel te worden zodra dit proces wordt geactiveerd, wordt beschreven in de sectie 'Tijd om te herstellen'.

De handmatige failoveroptie is altijd beschikbaar voor gebruik, ongeacht of de primaire regio downtime ondervindt of niet. Daarom kan deze optie mogelijk worden gebruikt om geplande failovers uit te voeren. Een voorbeeld van het gebruik van geplande failovers is het uitvoeren van periodieke failover-oefeningen. Een waarschuwing is echter dat een geplande failoverbewerking resulteert in een downtime voor de hub voor de periode die door de RTO voor deze optie is gedefinieerd, en ook resulteert in een gegevensverlies zoals gedefinieerd in de bovenstaande RPO-tabel. U overwegen om een test IoT-hub-instantie in te stellen om de geplande failoveroptie periodiek uit te oefenen om vertrouwen te krijgen in uw vermogen om uw end-to-end-oplossingen operationeel te krijgen wanneer zich een echte ramp voordoet.

Zie [Zelfstudie: Handmatige failover uitvoeren voor een IoT-hub voor](tutorial-manual-failover.md) stapsgewijze instructies.

### <a name="running-test-drills"></a>Testoefeningen uitvoeren

Testoefeningen mogen niet worden uitgevoerd op IoT-hubs die worden gebruikt in uw productieomgevingen.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>Gebruik geen handmatige failover om IoT-hub naar een andere regio te migreren

Handmatige failover mag *niet* worden gebruikt als een mechanisme om uw hub permanent te migreren tussen de geo-gekoppelde azure-regio's. Hierdoor wordt de latentie verhoogd voor de bewerkingen die worden uitgevoerd ten opzichte van de IoT-hub van apparaten die zijn uitgerust in het oude primaire gebied.

## <a name="failback"></a>Failback

Als u niet terugkeert naar het oude primaire gebied, kan dit worden bereikt door de failover-actie een andere keer te activeren. Als de oorspronkelijke failoverbewerking is uitgevoerd om te herstellen van een langdurige uitval in het oorspronkelijke primaire gebied, raden we aan dat de hub niet meer naar de oorspronkelijke locatie moet worden uitgevoerd zodra die locatie is hersteld van de storingssituatie.

> [!IMPORTANT]
> - Gebruikers mogen slechts 2 succesvolle failover- en 2 succesvolle failback-bewerkingen per dag uitvoeren.
>
> - Back-to-back failover/ failback operaties zijn niet toegestaan. U moet 1 uur wachten tussen deze bewerkingen.

## <a name="time-to-recover"></a>Tijd om te herstellen

Terwijl de FQDN (en dus de verbindingstekenreeks) van de IoT-hub-instantie dezelfde failover na de failover blijft, verandert het onderliggende IP-adres. Daarom kan de totale tijd voor de runtime-bewerkingen die worden uitgevoerd tegen uw IoT-hub-instantie volledig operationeel worden nadat het failoverproces is geactiveerd, worden uitgedrukt met behulp van de volgende functie.

Tijd om te herstellen = RTO [10 min - 2 uur voor handmatige failover | 2 - 26 uur voor door Microsoft geïnitieerde failover] + DNS-propagatievertraging + Tijd die de clienttoepassing nodig heeft om een iot-hub-IP-adres in de cache te vernieuwen.

> [!IMPORTANT]
> De IoT-SDK's bewaren het IP-adres van de IoT-hub niet in de cache. We raden aan dat gebruikerscode die interfacing met de SDK's is, het IP-adres van de IoT-hub niet in de cache moet inhouden.

## <a name="achieve-cross-region-ha"></a>Bereik cross regio HA

Als uw zakelijke uptime-doelen niet worden voldaan door de RTO die door Microsoft geïnitieerde failover- of handmatige failoveropties bieden, moet u overwegen een automatisch cross-region failovermechanisme per apparaat te implementeren.
Een volledige behandeling van implementatietopologieën in IoT-oplossingen valt buiten het toepassingsgebied van dit artikel. Het artikel bespreekt het *regionale failoverimplementatiemodel* met het oog op hoge beschikbaarheid en disaster recovery.

In een regionaal failovermodel wordt de back-end van de oplossing voornamelijk op één datacenterlocatie uitgevoerd. Een secundaire IoT-hub en back-end worden geïmplementeerd op een andere datacenterlocatie. Als de IoT-hub in het primaire gebied een storing heeft of als de netwerkverbinding van het apparaat naar het primaire gebied wordt onderbroken, gebruiken apparaten een secundair serviceeindpunt. U de beschikbaarheid van de oplossing verbeteren door een failovermodel voor verschillende regio's te implementeren in plaats van binnen één regio te blijven. 

Als u op hoog niveau een regionaal failovermodel met IoT Hub wilt implementeren, moet u de volgende stappen nemen:

* **Een secundaire IoT-hub- en apparaatrouterlogica:** als de service in uw primaire regio is verstoord, moeten apparaten verbinding maken met uw secundaire regio. Gezien het staatsbewuste karakter van de meeste betrokken services, is het gebruikelijk dat oplossingsbeheerders het failoverproces tussen regio's activeren. De beste manier om het nieuwe eindpunt te communiceren naar apparaten, met behoud van controle over het proces, is om ze regelmatig een *conciërgeservice* te laten controleren op het huidige actieve eindpunt. De conciërgeservice kan een webtoepassing zijn die wordt gerepliceerd en bereikbaar wordt gehouden met behulp van DNS-omleidingstechnieken (bijvoorbeeld met [Azure Traffic Manager).](../traffic-manager/traffic-manager-overview.md)

   > [!NOTE]
   > IoT-hubservice is geen ondersteund eindpunttype in Azure Traffic Manager. De aanbeveling is om de voorgestelde conciërgeservice te integreren met Azure-verkeersbeheer door de API voor endpointhealthsonde te implementeren.

* **Replicatie van identiteitsregister:** om bruikbaar te zijn, moet de secundaire IoT-hub alle apparaatidentiteiten bevatten die verbinding kunnen maken met de oplossing. De oplossing moet geo-gerepliceerde back-ups van apparaatidentiteiten behouden en deze uploaden naar de secundaire IoT-hub voordat u het actieve eindpunt voor de apparaten overschakelt. De functionaliteit voor het exporteren van de identiteit van iot-hub is in deze context handig. Zie [IoT Hub-ontwikkelaarshandleiding - identiteitsregister](iot-hub-devguide-identity-registry.md)voor meer informatie.

* **Logica samenvoegen:** wanneer het primaire gebied weer beschikbaar is, moeten alle status en gegevens die in de secundaire site zijn gemaakt, worden gemigreerd naar het primaire gebied. Deze status en gegevens hebben meestal betrekking op apparaatidentiteiten en toepassingsmetagegevens, die moeten worden samengevoegd met de primaire IoT-hub en andere toepassingsspecifieke opslag in het primaire gebied. 

Om deze stap te vereenvoudigen, moet u idempotente bewerkingen gebruiken. Idempotente bewerkingen minimaliseren de bijwerkingen van de uiteindelijke consistente verdeling van gebeurtenissen en van duplicaten of out-of-order levering van gebeurtenissen. Bovendien moet de toepassingslogica zodanig zijn ontworpen dat potentiële inconsistenties of een enigszins verouderde status worden getolereerd. Deze situatie kan optreden als gevolg van de extra tijd die het duurt voor het systeem om te genezen op basis van recovery point doelstellingen (RPO).

## <a name="choose-the-right-hadr-option"></a>Kies de juiste HA/DR-optie

Hier volgt een overzicht van de HA/DR-opties die in dit artikel worden gepresenteerd en die kunnen worden gebruikt als referentiekader om de juiste optie te kiezen die voor uw oplossing werkt.

| HA/DR, optie | Rto | RPO | Vereist handmatige interventie? | Complexiteit implementatie | Extra kostenimpact|
| --- | --- | --- | --- | --- | --- |
| Door Microsoft geïnitieerde failover |2 - 26 uur|RPO-tabel hierboven doorverwijzen|Nee|Geen|Geen|
| Handmatige failover |10 min - 2 uur|RPO-tabel hierboven doorverwijzen|Ja|Heel laag. U hoeft deze bewerking alleen vanuit het portaal te activeren.|Geen|
| Cross regio HA |< 1 min|Afhankelijk van de replicatiefrequentie van uw aangepaste HA-oplossing|Nee|Hoog|> 1x de kosten van 1 IoT-hub|

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure IoT Hub?](about-iot-hub.md)
* [Aan de slag met IoT-hubs (Quickstart)](quickstart-send-telemetry-dotnet.md)
* [Zelfstudie: Handmatige failover uitvoeren voor een IoT-hub](tutorial-manual-failover.md)