---
title: Informatie over metrische gegevens voor Azure lente-Cloud
description: Meer informatie over het controleren van metrische gegevens in azure lente Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: 3f88857ea66f65ff90705bc1d1e3fb745cc33709
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982748"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Informatie over metrische gegevens voor Azure lente-Cloud

De functie Verkenner van Azure is een onderdeel van de Microsoft Azure-portal waarmee u grafieken kunt uitzetten, trends visueel kunt correleren en pieken en spannings dips in metrische gegevens kan onderzoeken. Gebruik metrische gegevens Verkenner om de status en het gebruik van uw resources te onderzoeken. 

In azure lente Cloud zijn er twee weergave punten voor metrische gegevens.
* Grafieken op elke toepassings overzichts pagina
* Pagina algemene metrische gegevens

 ![Grafieken met metrische gegevens](media/metrics/metrics-1.png)

Grafieken in het **overzicht** van de toepassing bieden snelle status controles voor elke toepassing. De pagina algemene **metrische gegevens** bevat alle metrische gegevens die beschikbaar zijn voor referentie. U kunt uw eigen grafieken bouwen op de pagina met algemene metrische gegevens en deze vastmaken aan het dash board.

## <a name="application-overview-page"></a>Pagina Toepassings overzicht
Selecteer een app in **app-beheer** om de grafieken op de pagina overzicht te vinden.  

 ![Beheer van metrische gegevens van toepassingen](media/metrics/metrics-2.png)

De **overzichts** pagina van elke toepassing bevat een grafiek met metrische gegevens waarmee u een snelle status controle van uw toepassing kunt uitvoeren.  

 ![Overzicht van metrische gegevens voor toepassingen](media/metrics/metrics-3.png)

Azure lente Cloud biedt de volgende vijf grafieken met metrische gegevens die elke minuut worden bijgewerkt:

* **Http-server fouten**: aantal fouten voor HTTP-aanvragen aan uw app
* **Gegevens in**: bytes ontvangen door uw app
* **Gegevens uit**: door uw app verzonden bytes
* **Aanvragen**: aanvragen ontvangen door uw app
* **Gemiddelde reactie tijd**: gemiddelde reactie tijd van uw app

Voor de grafiek kunt u een tijds bereik van één uur tot zeven dagen selecteren.

## <a name="common-metrics-page"></a>Pagina algemene metrische gegevens

De **metrische gegevens** in het navigatie deel venster aan de linkerkant worden gekoppeld aan de pagina algemene metrische gegevens.

Selecteer eerst de metrische gegevens die u wilt weer geven:

![Metrische weer gave selecteren](media/metrics/metrics-4.png)

Meer informatie over de optie voor metrische gegevens vindt u in de volgende [sectie](#user-metrics-options) .

Selecteer vervolgens aggregatie type voor elke metriek:

![Metrische aggregatie](media/metrics/metrics-5.png)

Het samenvoegings type geeft aan hoe meet punten in het diagram op tijd worden geaggregeerd. Er is elke minuut één onbewerkte meet punt en het vooraf aggregatie type binnen een minuut wordt vooraf gedefinieerd door het type metrische gegevens.
* Sum: alle waarden optellen als doel uitvoer.
* Gemiddelde: gebruik de gemiddelde waarde in de periode als doel uitvoer.
* Maximum/min: gebruik de waarde max/min in de periode als doel uitvoer.

Het weer te geven tijds bereik kan ook worden gewijzigd. Het tijds bereik kan worden gekozen van de laatste 30 minuten tot de laatste 30 dagen of een aangepast tijds bereik.

![Metrische wijzigingen](media/metrics/metrics-6.png)

De standaard weergave bevat alle application's-metrische gegevens van een Azure veer Cloud service samen. Metrische gegevens van één app of exemplaar kunnen worden gefilterd in de weer gave.  Klik op **filter toevoegen**, stel de eigenschap in op **app**en selecteer de doel toepassing die u wilt bewaken in het tekstvak **waarden** . 

U kunt twee soorten filters (eigenschappen) gebruiken:
* App: filteren op app-naam
* Exemplaar: filteren op app-exemplaar

![Metrische filters](media/metrics/metrics-7.png)

U kunt ook de optie **splitsing Toep assen** gebruiken om meerdere regels voor één app te tekenen:

![Metrische splitsing](media/metrics/metrics-8.png)

>[!TIP]
> U kunt uw eigen grafieken maken op de pagina metrische gegevens en deze vastmaken aan uw **dash board**. Maak eerst een naam voor de grafiek.  Selecteer vervolgens **vastmaken aan dash board in de rechter bovenhoek**. U kunt nu uw toepassing op het **dash board**van de portal controleren.

## <a name="user-metrics-options"></a>Opties voor metrische gegevens van gebruikers

In de volgende tabellen worden de beschik bare metrische gegevens en details weer gegeven.

### <a name="error"></a>Fout
>[!div class="mx-tdCol2BreakAll"]
>| Naam | Naam van bron van Lente aandrijving | Eenheid | Details |
>|----|----|----|------------|
>| TomcatErrorCount<br><br>Tomcat globale fout (afgeschaft) | Tomcat. Global. error | Aantal | Aantal fouten bij verwerkte aanvragen |
>| Tomcat. Global. error | Tomcat. Global. error | Aantal | Aantal fouten bij verwerkte aanvragen |

### <a name="performance"></a>Prestaties
>[!div class="mx-tdCol2BreakAll"]
>| Naam | Naam van bron van Lente aandrijving | Eenheid | Details |
>|----|----|----|------------|
>| SystemCpuUsagePercentage<br><br>CPU-gebruiks percentage van systeem (afgeschaft) | System. CPU. Usage | Percentage | Recent CPU-gebruik voor het hele systeem. Deze waarde is een double in het interval van [0,0, 1,0]. Een waarde van 0,0 betekent dat alle Cpu's inactief zijn geweest tijdens de meest recente periode, terwijl een waarde van 1,0 betekent dat alle Cpu's op dit moment 100% van de tijd actief werden tijdens de meest recente periode.|
>| System. CPU. Usage | System. CPU. Usage | Percentage | Recent CPU-gebruik voor het hele systeem. Deze waarde is een double in het interval van [0,0, 1,0]. Een waarde van 0,0 betekent dat alle Cpu's inactief zijn geweest tijdens de meest recente periode, terwijl een waarde van 1,0 betekent dat alle Cpu's op dit moment 100% van de tijd actief werden tijdens de meest recente periode.|
>| AppCpuUsagePercentage<br><br>CPU-gebruiks percentage van de app (afgeschaft) | CPU-gebruiks percentage van app | Percentage | Recent CPU-gebruik voor het Java Virtual Machine proces. Deze waarde is een double in het interval van [0,0, 1,0]. Een waarde van 0,0 betekent dat geen van de Cpu's threads uit het JVM-proces heeft uitgevoerd gedurende de recent gebruikte periode, terwijl een waarde van 1,0 betekent dat alle Cpu's actief threads van de JVM 100% van de tijd tijdens de laatste periode hebben uitgevoerd. Threads van de JVM bevatten de Application threads en de JVM interne threads.|
>| proces. CPU. Usage | CPU-gebruiks percentage van app | Percentage | Recent CPU-gebruik voor het Java Virtual Machine proces. Deze waarde is een double in het interval van [0,0, 1,0]. Een waarde van 0,0 betekent dat geen van de Cpu's threads uit het JVM-proces heeft uitgevoerd gedurende de recent gebruikte periode, terwijl een waarde van 1,0 betekent dat alle Cpu's actief threads van de JVM 100% van de tijd tijdens de laatste periode hebben uitgevoerd. Threads van de JVM bevatten de Application threads en de JVM interne threads.|
>| AppMemoryCommitted<br><br>Toegewezen app-geheugen (afgeschaft)) | JVM. Memory. committed | Bytes | Vertegenwoordigt de hoeveelheid geheugen die gegarandeerd beschikbaar is voor gebruik door de JVM. De JVM kan geheugen vrijgeven aan het systeem en de toegewezen kan minder zijn dan init. doorgevoerd is altijd groter dan of gelijk aan het gebruik. |
>| JVM. Memory. committed | JVM. Memory. committed | Bytes | Vertegenwoordigt de hoeveelheid geheugen die gegarandeerd beschikbaar is voor gebruik door de JVM. De JVM kan geheugen vrijgeven aan het systeem en de toegewezen kan minder zijn dan init. doorgevoerd is altijd groter dan of gelijk aan het gebruik. |
>| AppMemoryUsed <br><br>Gebruikt app-geheugen (afgeschaft) | JVM. Memory. used | Bytes | Vertegenwoordigt de hoeveelheid geheugen die momenteel wordt gebruikt in bytes. |
>| JVM. Memory. used | JVM. Memory. used | Bytes | Vertegenwoordigt de hoeveelheid geheugen die momenteel wordt gebruikt in bytes. |
>| AppMemoryMax<br><br>Maxi maal app-geheugen (afgeschaft) | JVM. Memory. Max | Bytes | Hiermee wordt de maximale hoeveelheid geheugen aangegeven die kan worden gebruikt voor geheugen beheer. De hoeveelheid gebruikt en toegewezen geheugen is altijd kleiner dan of gelijk aan maximum als maximum is gedefinieerd. Een geheugen toewijzing kan mislukken als er wordt geprobeerd het gebruikte geheugen te verg Roten, zoals het gebruik van > vastgelegd, zelfs als dit wordt gebruikt <= Max zou wel waar zijn (bijvoorbeeld wanneer het systeem weinig virtueel geheugen heeft). |
>| JVM. Memory. Max | JVM. Memory. Max | Bytes | Hiermee wordt de maximale hoeveelheid geheugen aangegeven die kan worden gebruikt voor geheugen beheer. De hoeveelheid gebruikt en toegewezen geheugen is altijd kleiner dan of gelijk aan maximum als maximum is gedefinieerd. Een geheugen toewijzing kan mislukken als er wordt geprobeerd het gebruikte geheugen te verg Roten, zoals het gebruik van > vastgelegd, zelfs als dit wordt gebruikt <= Max zou wel waar zijn (bijvoorbeeld wanneer het systeem weinig virtueel geheugen heeft). |
>| MaxOldGenMemoryPoolBytes<br><br>Maximale hoeveelheid beschik bare oude generatie gegevens (afgeschaft) | JVM. gc. max. data. size | Bytes | Het piek geheugen gebruik van de oude generatie geheugen groep sinds de virtuele Java-machine is gestart. |
>| JVM. gc. max. data. size | JVM. gc. max. data. size | Bytes | Het piek geheugen gebruik van de oude generatie geheugen groep sinds de virtuele Java-machine is gestart. |
>| OldGenMemoryPoolBytes<br><br>Grootte van oude generatie gegevens (afgeschaft) | JVM. gc. live. data. size | Bytes | Grootte van de geheugen groep van de oude generatie na een volledige GC. |
>| JVM. gc. live. data. size | JVM. gc. live. data. size | Bytes | Grootte van de geheugen groep van de oude generatie na een volledige GC. |
>| OldGenPromotedBytes<br><br>Promo veren tot oude generatie gegevens grootte (afgeschaft) | JVM. gc. Memory. bevorderd | Bytes | Aantal positieve toename van de omvang van de oude generatie geheugengroep vóór GC tot na GC. |
>| JVM. gc. Memory. bevorderd | JVM. gc. Memory. bevorderd | Bytes | Aantal positieve toename van de omvang van de oude generatie geheugengroep vóór GC tot na GC. |
>| YoungGenPromotedBytes<br><br>Promo veren tot jonge generatie gegevens grootte (afgeschaft) | JVM. gc. toegewezen geheugen | Bytes | Wordt opgehoogd voor een toename van de grootte van de Memory pool voor de jonge generatie na een GC tot de volgende. |
>| JVM. gc. toegewezen geheugen | JVM. gc. toegewezen geheugen | Bytes | Wordt opgehoogd voor een toename van de grootte van de Memory pool voor de jonge generatie na een GC tot de volgende. |
>| GCPauseTotalCount<br><br>Aantal gepauzeerde GC (afgeschaft) | JVM. gc. pause (totaal aantal) | Aantal | Totaal aantal GC-pogingen nadat deze JMV is gestart, inclusief Young en oud GC. |
>| JVM. gc. pause. Total. Count | JVM. gc. pause (totaal aantal) | Aantal | Totaal aantal GC-pogingen nadat deze JMV is gestart, inclusief Young en oud GC. |
>| GCPauseTotalTime<br><br>Totale tijd van de GC-onderbreking (afgeschaft) | JVM. gc. pause (Total-Time) | Milliseconden | Totale aantal geconsumeerde GC-tijd nadat deze JMV is gestart, met inbegrip van Young en oud GC. |
>| JVM. gc. pause. Total. time | JVM. gc. pause (Total-Time) | Milliseconden | Totale aantal geconsumeerde GC-tijd nadat deze JMV is gestart, met inbegrip van Young en oud GC. |
>| Tomcat. threads. config. Max | Tomcat. threads. config. Max | Aantal | Maximum aantal threads van Tomcat-configuratie |
>| Tomcat. threads. current | Tomcat. threads. current | Aantal | Aantal huidige threads van Tomcat |
>| Tomcat. Global. Request. Gem. tijd | Tomcat. Global. Request. Gem. tijd | Milliseconden | Gemiddelde tijd Tomcat-aanvraag |


### <a name="request"></a>Aanvraag
>[!div class="mx-tdCol2BreakAll"]
>| Naam | Naam van bron van Lente aandrijving | Eenheid | Details |
>|----|----|----|------------|
>| TomcatSentBytes<br><br>Tomcat totaal aantal verzonden bytes (afgeschaft) | Tomcat. Global. sent | Bytes | De hoeveelheid gegevens die Tomcat webserver heeft verzonden |
>| Tomcat. Global. sent | Tomcat. Global. sent | Bytes | De hoeveelheid gegevens die Tomcat webserver heeft verzonden |
>| TomcatReceivedBytes<br><br>Totaal aantal ontvangen Tomcat-bytes (afgeschaft) | Tomcat. Global. ontvangen | Bytes | De hoeveelheid gegevens die Tomcat webserver heeft ontvangen |
>| Tomcat. Global. ontvangen | Tomcat. Global. ontvangen | Bytes | De hoeveelheid gegevens die Tomcat webserver heeft ontvangen |
>| TomcatRequestTotalTime<br><br>Totale tijd van Tomcat-aanvraag (afgeschaft) | Tomcat. Global. Request (Total-Time) | Milliseconden | Totale tijd van de Tomcat-webserver om de aanvragen te verwerken |
>| TomcatRequestTotalCount<br><br>Aantal Tomcat-aanvragen (afgeschaft) | Tomcat. Global. Request (totaal aantal) | Aantal | Totaal aantal verwerkte aanvragen van Tomcat-webserver |
>| Tomcat. Global. Request. Total. Count | Tomcat. Global. Request (totaal aantal) | Aantal | Totaal aantal verwerkte aanvragen van Tomcat-webserver |
>| TomcatRequestMaxTime<br><br>Maximale duur van de Tomcat-aanvraag (afgeschaft) | Tomcat. Global. Request. Max | Milliseconden | Maximale tijd van de Tomcat-webserver voor het verwerken van een aanvraag |
>| Tomcat. Global. Request. Max | Tomcat. Global. Request. Max | Milliseconden | Maximale tijd van de Tomcat-webserver voor het verwerken van een aanvraag |

### <a name="session"></a>Sessie
>[!div class="mx-tdCol2BreakAll"]
>| Naam | Naam van bron van Lente aandrijving | Eenheid | Details |
>|----|----|----|------------|
>| TomcatSessionActiveMaxCount<br><br>Aantal actieve Tomcat-sessies (verouderd) | Tomcat. Sessions. Active. Max | Aantal | Het maximum aantal sessies dat tegelijkertijd actief is geweest |
>| Tomcat. Sessions. Active. Max | Tomcat. Sessions. Active. Max | Aantal | Het maximum aantal sessies dat tegelijkertijd actief is geweest |
>| TomcatSessionAliveMaxTime<br><br>Tijds duur van de Tomcat-sessie (afgeschaft) | Tomcat. Sessions. Alive. Max | Milliseconden | Langste tijd (in seconden) dat een verlopen sessie actief was |
>| Tomcat. Sessions. Alive. Max | Tomcat. Sessions. Alive. Max | Milliseconden | Langste tijd (in seconden) dat een verlopen sessie actief was |
>| TomcatSessionCreatedCount<br><br>Aantal gemaakte Tomcat-sessies (afgeschaft) | Tomcat. Sessions. created | Aantal | Aantal sessies dat is gemaakt |
>| Tomcat. Sessions. created | Tomcat. Sessions. created | Aantal | Aantal sessies dat is gemaakt |
>| TomcatSessionExpiredCount<br><br>Aantal verlopen Tomcat-sessies (afgeschaft) | Tomcat. Sessions. Expires | Aantal | Aantal sessies dat is verlopen |
>| Tomcat. Sessions. Expires | Tomcat. Sessions. Expires | Aantal | Aantal sessies dat is verlopen |
>| TomcatSessionRejectedCount<br><br>Aantal geweigerde Tomcat-sessies (afgeschaft) | Tomcat. Sessions. rejected | Aantal | Het aantal sessies dat niet is gemaakt, omdat het maximum aantal actieve sessies is bereikt. |
>| Tomcat. Sessions. rejected | Tomcat. Sessions. rejected | Aantal | Het aantal sessies dat niet is gemaakt, omdat het maximum aantal actieve sessies is bereikt. |
>| Tomcat. Sessions. Active. current | Tomcat. Sessions. Active. current | Aantal | Aantal actieve sessies van Tomcat |

## <a name="see-also"></a>Zie ook
* [Aan de slag met Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Volgende stappen
* [Zelf studie: lente-cloud resources bewaken met behulp van waarschuwingen en actie groepen](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Quota's en service plannen voor Azure lente-Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

