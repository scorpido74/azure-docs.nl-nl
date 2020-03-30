---
title: Statistieken voor Azure Spring Cloud begrijpen
description: Meer informatie over het bekijken van statistieken in Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256755"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Statistieken voor Azure Spring Cloud begrijpen

Azure Metrics explorer is een onderdeel van de Microsoft Azure-portal waarmee grafieken kunnen worden uitgestippeld, trends visueel kunnen correleren en pieken en dalen in metrische gegevens kunnen worden onderzocht. Gebruik de statistiekenverkenner om de status en het gebruik van uw resources te onderzoeken. 

In Azure Spring Cloud zijn er twee weergavepunten voor statistieken.
* Grafieken op elke pagina met toepassingsoverzicht
* Pagina Algemene statistieken

 ![Grafieken met statistieken](media/metrics/metrics-1.png)

Grafieken in het **toepassingsoverzicht** bieden snelle statuscontroles voor elke toepassing. De algemene **pagina Statistieken** bevat alle statistieken die beschikbaar zijn voor referentie. U uw eigen grafieken maken op de algemene statistiekenpagina en deze vastmaken in dashboard.

## <a name="application-overview-page"></a>Pagina Toepassingsoverzicht
Selecteer een app in **App-beheer** om grafieken te vinden op de overzichtspagina.  

 ![Beheer van toepassingsstatistieken](media/metrics/metrics-2.png)

Op de pagina **Toepassingsoverzicht** van elke toepassing wordt een grafiek met statistieken weergegeven waarmee u een snelle statuscontrole van uw toepassing uitvoeren.  

 ![Overzicht van toepassingsstatistieken](media/metrics/metrics-3.png)

Azure Spring Cloud biedt deze vijf grafieken statistieken die elke minuut worden bijgewerkt:

* **Http-serverfouten**: aantal fouten voor HTTP-aanvragen voor uw app
* **Gegevens in**: Bytes die door uw app zijn ontvangen
* **Gegevens uit:** bytes die door uw app worden verzonden
* **Verzoeken**: Aanvragen ontvangen door uw app
* **Gemiddelde responstijd:** gemiddelde responstijd van uw app

Voor de grafiek u een tijdsbereik van één uur tot zeven dagen selecteren.

## <a name="common-metrics-page"></a>Pagina Algemene statistieken

De **statistieken** in het linkernavigatiedeelvenster worden gekoppeld aan de algemene statistiekenpagina.

Selecteer eerst statistieken die u wilt weergeven:

![Metrische weergave selecteren](media/metrics/metrics-4.png)

Details van alle statistieken optie kan worden gevonden in de [sectie](#user-metrics-options) hieronder.

Selecteer vervolgens aggregatietype voor elke statistiek:

![Metrische aggregatie](media/metrics/metrics-5.png)

Het aggregatietype geeft aan hoe metrische punten in de grafiek per tijd kunnen worden samengevoegd. Er is elke minuut één onbewerkt metriek punt en het type pre-aggregatie binnen een minuut wordt vooraf gedefinieerd door het type metrische gegevens.
* Som: Som alle waarden als doeluitvoer.
* Gemiddelde: Gebruik de gemiddelde waarde in de periode als doeluitvoer.
* Max/Min: Gebruik de waarde Max/Min in de periode als doeluitvoer.

Het tijdsbereik om weer te geven kan ook worden gewijzigd. Het tijdsbereik kan worden gekozen van de laatste 30 minuten tot de laatste 30 dagen, of een aangepast tijdsbereik.

![Metrische wijziging](media/metrics/metrics-6.png)

De standaardweergave bevat alle statistieken van de toepassing van een Azure Spring Cloud-service samen. Statistieken van één app of instantie kunnen in het display worden gefilterd.  Klik **op Filter toevoegen,** stel de eigenschap in op **App**en selecteer de doeltoepassing die u wilt controleren in het tekstvak **Waarden.** 

U twee soorten filters (eigenschappen) gebruiken:
* App: filteren op app-naam
* Instantie: filteren op app-instantie

![Metrische filters](media/metrics/metrics-7.png)

U ook de optie **Splitsen toepassen** gebruiken, waarbij meerdere regels voor één app worden getekend:

![Metrische splitsing](media/metrics/metrics-8.png)

>[!TIP]
> U uw eigen grafieken maken op de pagina met statistieken en deze vastmaken aan uw **dashboard.** Begin met het benoemen van uw grafiek.  Selecteer vervolgens **Vastmaken aan het dashboard in de rechterbovenhoek**. U uw aanvraag nu controleren op uw Portal **Dashboard.**

## <a name="user-metrics-options"></a>Opties voor gebruikersstatistieken

In de volgende tabellen worden de beschikbare statistieken en details weergegeven.

### <a name="error"></a>Fout
>[!div class="mx-tdCol2BreakAll"]
>| Name | Metrische naam veeractuator | Eenheid | Details |
>|----|----|----|------------|
>| Tomcat-globale fout | tomcat.global.error | Count | Aantal fouten treedt op bij verwerkte aanvragen |

### <a name="performance"></a>Prestaties
>[!div class="mx-tdCol2BreakAll"]
>| Name | Metrische naam veeractuator | Eenheid | Details |
>|----|----|----|------------|
>|Gebruikspercentage systeem-CPU-gebruik | system.cpu.usage system.cpu.usage system.cpu.usage system. | Percentage | Recent CPU-gebruik voor het hele systeem. Deze waarde is een dubbele in het interval [0,0,1,0]. Een waarde van 0,0 betekent dat alle CPU's in de afgelopen waargenomen periode niet actief waren, terwijl een waarde van 1,0 betekent dat alle CPU's actief 100% van de tijd actief waren tijdens de recente periode die werd waargenomen.|
>| Gebruikspercentage app-cpu's | Gebruikspercentage app-cpu's | Percentage | Recent CPU-gebruik voor het Java Virtual Machine-proces. Deze waarde is een dubbele in het interval [0,0,1,0]. Een waarde van 0,0 betekent dat geen van de CPU's in de recente waargenomen periode draden van het JVM-proces runde, terwijl een waarde van 1,0 betekent dat alle CPU's actief threads van de JVM liepen gedurende de recente periode die werd waargenomen. Threads van de JVM bevatten de toepassingsthreads en de interne THREADS van JVM.|
>| App-geheugen toegewezen | jvm.memory.committed | Bytes | Geeft de hoeveelheid geheugen weer die gegarandeerd beschikbaar is voor gebruik door de JVM. De JVM kan het geheugen vrijgeven aan het systeem en toegewijd kan minder dan init. toegewijd zal altijd groter zijn dan of gelijk is aan gebruikt. |
>| App-geheugen gebruikt | jvm.memory.gebruikt | Bytes | Geeft de hoeveelheid geheugen weer die momenteel in bytes wordt gebruikt. |
>| App-geheugen max. | jvm.memory.max | Bytes | Vertegenwoordigt de maximale hoeveelheid geheugen die kan worden gebruikt voor geheugenbeheer. De hoeveelheid gebruikt en vastgelegd geheugen zal altijd lager zijn dan of gelijk aan max als max is gedefinieerd. Een geheugentoewijzing kan mislukken als het probeert om het gebruikte geheugen zodanig te verhogen dat gebruikt > gepleegd, zelfs als gebruikt <= max zou nog steeds waar zijn (bijvoorbeeld wanneer het systeem is laag op virtueel geheugen). |
>| Max beschikbare oude generatie gegevensgrootte | jvm.gc.max.data.size | Bytes | Het piekgeheugengebruik van de oude generatie geheugenpool aangezien de virtuele machine van Java werd begonnen. |
>| Gegevensgrootte van oude generatie | jvm.gc.live.data.size | Bytes | Grootte van oude generatie geheugen pool na een volledige GC. |
>| Bevorderen tot gegevensgrootte van oude generatie | jvm.gc.memory.promoted | Bytes | Telling van positieve verhogingen in de grootte van de oude generatie geheugenpool vóór GC aan na GC. |
>| Bevorderen tot gegevensgrootte van jonge generaties | jvm.gc.memory.toegewezen | Bytes | Verhoogd voor een toename van de grootte van de jonge generatie geheugen pool na een GC tot vóór de volgende. |
>| GC-pauzetelling | jvm.gc.pause (totaal aantal) | Count | Totaal klassement som meer na deze JMV gestart, inclusief Jong en Oud GC. |
>| Totale tijd van GC-pauze | jvm.gc.pause (total-time) | Milliseconden | Total GC tijdrovend na deze JMV gestart, met inbegrip van Jong en Oud GC. |

### <a name="request"></a>Aanvraag
>[!div class="mx-tdCol2BreakAll"]
>| Name | Metrische naam veeractuator | Eenheid | Details |
>|----|----|----|------------|
>| Tomcat Totaal verzonden bytes | tomcat.global.sent | Bytes | Hoeveelheid gegevens Tomcat-webserver verzonden |
>| Tomcat Totaal ontvangen bytes | tomcat.global.received | Bytes | Hoeveelheid gegevens Tomcat-webserver ontvangen |
>| Totale tijd van Tomcat-aanvraag | tomcat.global.request (total-time) | Milliseconden | Totale tijd van Tomcat-webserver om de aanvragen te verwerken |
>| Totaal aantal aanvragen voor Tomcat-aanvragen | tomcat.global.request (totaal aantal) | Count | Totaal aantal verwerkte aanvragen voor Tomcat-webserver |
>| Tomcat Request Max Time | tomcat.global.request.max | Milliseconden | Maximale tijd van Tomcat-webserver om een aanvraag te verwerken |

### <a name="session"></a>Sessie
>[!div class="mx-tdCol2BreakAll"]
>| Name | Metrische naam veeractuator | Eenheid | Details |
>|----|----|----|------------|
>| Tomcat Session Max Active Count | tomcat.sessions.active.max | Count | Maximaal aantal sessies dat tegelijkertijd actief is geweest |
>| Tomcat Sessie Max Alive Time | tomcat.sessions.alive.max | Milliseconden | Langste tijd (in seconden) dat een verlopen sessie in leven was geweest |
>| Aantal gemaakt Tomcat-sessie | tomcat.sessions.created | Count | Aantal sessies dat is gemaakt |
>| Aantal verlopen tomcat-sessies | tomcat.sessions.verlopen | Count | Aantal sessies dat is verlopen |
>| Afgewezen aantal Tomcat-sessie | tomcat.sessions.rejected | Count | Aantal sessies dat niet is gemaakt omdat het maximum aantal actieve sessies is bereikt. |

## <a name="see-also"></a>Zie ook
* [Aan de slag met Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Logboeken en statistieken analyseren met diagnostische instellingen](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Volgende stappen
* [Zelfstudie: Cloudbronnen voor de lente controleren met waarschuwingen en actiegroepen](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Quota en serviceplannen voor Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

