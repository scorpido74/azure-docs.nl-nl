---
title: Azure Monitor voor configuratie van containersstatusmonitors | Microsoft Documenten
description: In dit artikel vindt u inhoud waarin de gedetailleerde configuratie van de statusmonitors in Azure Monitor voor containers wordt beschreven.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055705"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Configuratiehandleiding voor Azure Monitor voor statusmonitor van containers

Monitoren zijn het belangrijkste element voor het meten van de status en het detecteren van fouten in Azure Monitor voor containers. In dit artikel u inzicht krijgen in de concepten van de manier waarop de gezondheid wordt gemeten en de elementen die het gezondheidsmodel omvatten om de status van uw Kubernetes-cluster te controleren en te rapporteren met de functie [Gezondheid (voorbeeld).](container-insights-health.md)

>[!NOTE]
>De functie Gezondheid is op dit moment in openbare preview.
>

## <a name="monitors"></a>Monitors

Een monitor meet de status van een bepaald aspect van een beheerd object. Monitoren hebben elk twee of drie gezondheidstoestanden. Een monitor zal in een en slechts een van de potentiële staten op een bepaald moment. Wanneer een monitor geladen door de containeragent, wordt het geïnitialiseerd tot een gezonde staat. De status wordt alleen gewijzigd als de opgegeven voorwaarden voor een andere status worden gedetecteerd.

De algehele status van een bepaald object wordt bepaald aan de hand van de status van elk van zijn monitoren. Deze hiërarchie wordt geïllustreerd in het deelvenster Statushiërarchie in Azure Monitor voor containers. Het beleid voor het oprollen van de status maakt deel uit van de configuratie van de geaggregeerde monitoren.

## <a name="types-of-monitors"></a>Typen beeldschermen

|Controleren | Beschrijving | 
|--------|-------------|
| Eenheidsmonitor |Een eenheidsmonitor meet een bepaald aspect van een resource of toepassing. Dit kan het controleren van een prestatieteller om de prestaties van de resource of de beschikbaarheid ervan te bepalen. |
|Aggregaatmonitor | Geaggregeerde monitors groeperen meerdere monitoren om één status van de status van de status in de status van de status te bieden. Eenheidsmonitors worden doorgaans geconfigureerd onder een bepaalde aggregaatmonitor. Een aggregaatmonitor van knooppunt rolt bijvoorbeeld de status van het gebruik van de node-CPU, het geheugengebruik en de status Knooppunt op.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Geaggregeerdbeleid voor het oprollen van monitorstatus

Elke geaggregeerde monitor definieert een statusrollupbeleid, dat is de logica die wordt gebruikt om de status van de aggregaatmonitor te bepalen op basis van de status van de monitoren eronder. Het mogelijke beleid voor het oprollen van de gezondheid voor een geaggregeerde monitor is als volgt:

#### <a name="worst-state-policy"></a>Slechtste staatsbeleid

De status van de geaggregeerde monitor komt overeen met de status van de onderliggende monitor met de slechtste status. Dit is het meest voorkomende beleid dat wordt gebruikt door geaggregeerde monitoren.

![Voorbeeld van de slechtste status van de totale monitor](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Percentagebeleid

Het bronobject komt overeen met de slechtste status van één lid van een bepaald percentage doelobjecten in de beste status. Dit beleid wordt gebruikt wanneer een bepaald percentage doelobjecten in orde moet zijn om het doelobject als gezond te laten worden beschouwd. Percentagebeleid sorteert de monitors in aflopende volgorde van de ernst van de status en de status van de aggregaatmonitor wordt berekend als de slechtste status van N% (N wordt bepaald door de configuratieparameter *StateThresholdPercentage).*

Stel dat er vijf containerexemplaren van een containerafbeelding zijn en dat de afzonderlijke **statussen kritiek**, **waarschuwing**, **gezond**, **gezond**, gezond **, gezond**zijn .  De status van de container CPU-gebruikmonitor is **kritiek**, omdat de slechtste toestand van 90% van de containers **kritiek** is wanneer deze wordt gesorteerd in aflopende volgorde van ernst.

## <a name="understand-the-monitoring-configuration"></a>De bewakingsconfiguratie begrijpen

Azure Monitor voor containers bevat een aantal belangrijke bewakingsscenario's die als volgt zijn geconfigureerd.

### <a name="unit-monitors"></a>Eenheidsmonitoren

|**Monitornaam** | Monitortype | **Beschrijving** | **Parameter** | **Waarde** |
|-----------------|--------------|-----------------|---------------|-----------|
|Gebruik van knooppuntgeheugen |Eenheidsmonitor |Deze monitor evalueert het geheugengebruik van een knooppunt elke minuut, met behulp van de cadvisor gerapporteerde gegevens. |Opeenvolgende monstersforstatetransition<br> FailifgreaterthanPercentage<br> WarnIfGreaterthanPercentage | 3<br> 90<br> 80  ||
|Gebruik van knooppunt-CPU's |Eenheidsmonitor |Deze monitor controleert het CPU-gebruik van het knooppunt elke minuut, met behulp van de cadvisor gerapporteerde gegevens. | Opeenvolgende monstersforstatetransition<br> FailifgreaterthanPercentage<br> WarnIfGreaterthanPercentage | 3<br> 90<br> 80  ||
|Knooppuntstatus |Eenheidsmonitor |Deze monitor controleert knooppuntvoorwaarden die door Kubernetes zijn gerapporteerd.<br> Momenteel worden de volgende knooppuntvoorwaarden gecontroleerd: Schijfdruk, geheugendruk, PID-druk, Out of Disk, Netwerk niet beschikbaar, Status klaar voor het knooppunt.<br> Als Niet-beschikbaar is *als niet-beschikbaar* **is,** verandert de monitor **in** kritieke status als *niet-beschikbaar* is voor niet-beschikbaar zijn.<br> Als andere voorwaarden **gelijk zijn**aan true , anders dan een status **Gereed,** wordt de monitor gewijzigd in de status **Waarschuwing.** | NodeConditionTypeForFailedState | outofdisk, netwerk niet beschikbaar ||
|Gebruik containergeheugen |Eenheidsmonitor |Deze monitor rapporteert de gecombineerde status van het geheugengebruik (RSS) van de exemplaren van de container.<br> Het voert een eenvoudige vergelijking uit die elk monster vergelijkt met één drempel waardewaarde en wordt opgegeven door de configuratieparameter **ConsecutiveSamplesForStateTransition**.<br> De status ervan wordt berekend als de slechtste status van 90% van de containerexemplaren (StateThresholdPercentage), gesorteerd in aflopende volgorde van de ernst van de containerstatus (dat wil zeggen kritiek, waarschuwing, gezond).<br> Als er geen record wordt ontvangen van een containerinstantie, wordt de status van de containerinstantie gerapporteerd als **Onbekend**en heeft deze hogere prioriteit in de sorteervolgorde boven de **status Kritieke** status.<br> De status van elke afzonderlijke containerinstantie wordt berekend aan de hand van de drempelwaarden die in de configuratie zijn opgegeven. Als het gebruik boven de kritieke drempel waarde (90%) is, bevindt de instantie zich in een **kritieke** toestand, als deze lager is dan kritieke drempelwaarde (90%) maar groter dan de waarschuwingsdrempel (80%), dan is de instantie in een **waarschuwingstaat.** Anders is het in **gezonde** staat. |Opeenvolgende monstersforstatetransition<br> Failiflessthanpercentage<br> StateThresholdPercentage<br> WarnIfGreaterthanPercentage| 3<br> 90<br> 90<br> 80 ||
|Gebruik van container-CPU's |Eenheidsmonitor |Deze monitor rapporteert de gecombineerde status van het CPU-gebruik van de exemplaren van de container.<br> Het voert een eenvoudige vergelijking uit die elk monster vergelijkt met één drempel waardewaarde en wordt opgegeven door de configuratieparameter **ConsecutiveSamplesForStateTransition**.<br> De status ervan wordt berekend als de slechtste status van 90% van de containerexemplaren (StateThresholdPercentage), gesorteerd in aflopende volgorde van de ernst van de containerstatus (dat wil zeggen kritiek, waarschuwing, gezond).<br> Als er geen record wordt ontvangen van een containerinstantie, wordt de status van de containerinstantie gerapporteerd als **Onbekend**en heeft deze hogere prioriteit in de sorteervolgorde boven de **status Kritieke** status.<br> De status van elke afzonderlijke containerinstantie wordt berekend aan de hand van de drempelwaarden die in de configuratie zijn opgegeven. Als het gebruik boven de kritieke drempel waarde (90%) is, bevindt de instantie zich in een **kritieke** toestand, als deze lager is dan kritieke drempelwaarde (90%) maar groter dan de waarschuwingsdrempel (80%), dan is de instantie in een **waarschuwingstaat.** Anders is het in **gezonde** staat. |Opeenvolgende monstersforstatetransition<br> Failiflessthanpercentage<br> StateThresholdPercentage<br> WarnIfGreaterthanPercentage| 3<br> 90<br> 90<br> 80 ||
|Systeemwerkbelastingpods gereed |Eenheidsmonitor |Deze monitor rapporteert de status op basis van het percentage pods in gereedstaat in een bepaalde werkbelasting. De status is ingesteld op **Kritiek** als minder dan 100% van de pods in **een gezonde** toestand verkeren |Opeenvolgende monstersforstatetransition<br> Failiflessthanpercentage |2<br> 100 ||
|Kube API-status |Eenheidsmonitor |Deze monitor rapporteert de status van de Kube API-service. Monitor is in kritieke toestand voor het geval Kube API-eindpunt niet beschikbaar is. Voor deze specifieke monitor wordt de status bepaald door een query te maken naar het eindpunt 'knooppunten' voor de kube-api-server. Iets anders dan een OK-antwoordcode verandert de monitor in een **kritieke** status. | Geen configuratie-eigenschappen |||

### <a name="aggregate-monitors"></a>Geaggregeerde monitoren

|**Monitornaam** | **Beschrijving** | **Algoritme** |
|-----------------|-----------------|---------------|
|Knooppunt |Deze monitor is een aggregaat van alle node monitoren. Het komt overeen met de toestand van de onderliggende monitor met de slechtste gezondheidstoestand:<br> Cpu-gebruik knooppunt<br> Geheugengebruik knooppunt<br> Knooppuntstatus | Slechtste van|
|Knooppuntzwembad |Deze monitor rapporteert de gecombineerde status van alle knooppunten in de *agentpool van*de knooppuntgroep . Dit is een driestatusmonitor, waarvan de status is gebaseerd op de slechtste toestand van 80% van de knooppunten in de knooppuntgroep, gesorteerd in aflopende volgorde van de ernst van knooppunttoestanden (dat wil zeggen, Kritiek, Waarschuwing, Gezond).|Percentage |
|Knooppunten (bovenliggende groep knooppunt) |Dit is een geaggregeerde monitor van alle knooppuntpools. De status is gebaseerd op de slechtste status van de onderliggende monitoren (dat wil zeggen, de knooppuntpools die aanwezig zijn in het cluster). |Slechtste van |
|Cluster (bovenliggende knooppunten/<br> Kubernetes-infrastructuur) |Dit is de bovenliggende monitor die overeenkomt met de status van de onderliggende monitor met de slechtste status, dat is kubernetes-infrastructuur en knooppunten. |Slechtste van |
|Kubernetes-infrastructuur |Deze monitor rapporteert de gecombineerde status van de beheerde infrastructuurcomponenten van het cluster. de status ervan wordt berekend als de 'slechtste van' de onderliggende monitor staten, d.w.z. kube-system workloads en API Server status. |Slechtste van|
|Systeemwerkbelasting |Deze monitor rapporteert de status van een kube-systeemwerkbelasting. Deze monitor komt overeen met de status van de onderliggende monitor met de slechtste status, dat wil zeggen de **Pods in gereedstaat** (monitor en de containers in de werkbelasting). |Slechtste van |
|Container |Deze monitor rapporteert de algehele status van een container in een bepaalde werkbelasting. Deze monitor komt overeen met de status van de onderliggende monitor met de slechtste status, dat wil zeggen het **CPU-gebruik** en **geheugengebruiksmonitors.** |Slechtste van |

## <a name="next-steps"></a>Volgende stappen

Bekijk [de monitorclusterstatus](container-insights-health.md) voor meer informatie over het weergeven van de status van uw Kubernetes-cluster.
