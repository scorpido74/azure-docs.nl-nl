---
title: Azure Monitor voor containers status monitors configuratie | Microsoft Docs
description: In dit artikel vindt u informatie over de gedetailleerde configuratie van de status monitors in Azure Monitor voor containers.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/12/2019
ms.author: magoedte
ms.openlocfilehash: b782581318751830ec47b9fecb056fecefb353eb
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134955"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Configuratie handleiding voor containers Health Monitor Azure Monitor

Monitors zijn het primaire element voor het meten van de status en het detecteren van fouten in Azure Monitor voor containers. In dit artikel wordt uitgelegd hoe de status wordt gemeten en welke elementen het status model vormen om de status van uw Kubernetes-cluster te controleren en rapporteren met de [status functie](container-insights-health.md).

## <a name="monitors"></a>Monitors

Een monitor meet de status van een bepaald aspect van een beheerd object. Monitors hebben beide twee of drie statussen. Een monitor bevindt zich in slechts één van de mogelijke statussen op een bepaald moment. Wanneer een monitor wordt geladen door de container agent, wordt deze geïnitialiseerd met de status in orde. De status wordt alleen gewijzigd als de opgegeven voor waarden voor een andere status worden gedetecteerd.

De algemene status van een bepaald object wordt bepaald aan de hand van de status van elk van de monitors. Deze hiërarchie wordt geïllustreerd in het deel venster status hiërarchie in Azure Monitor voor containers. Het beleid voor de manier waarop de status wordt getotaliseerd, maakt deel uit van de configuratie van de geaggregeerde monitors.

## <a name="types-of-monitors"></a>Typen monitors

|Bewaken | Beschrijving | 
|--------|-------------|
| Unit-monitor |Een unit-monitor meet een aspect van een resource of toepassing. Dit kan een prestatie meter item controleren om de prestaties van de resource of de beschik baarheid te bepalen. |
|Aggregaatmonitor | Aggregatie bewaakt meerdere monitors om één status geaggregeerde status te bieden. Eenheids monitors worden doorgaans geconfigureerd onder een bepaalde aggregaatmonitor. Een knooppunt aggregatie kan bijvoorbeeld de status van het CPU-gebruik van het knoop punt, het geheugen gebruik en de knooppunt status samen vouwen.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Totaliserings beleid voor totaliseren van status controleren

Elke aggregaatmonitor definieert een totaliserings beleid voor statussen. Dit is de logica die wordt gebruikt om de status van de aggregaatmonitor te bepalen op basis van de status van de monitors daaronder. De volgende beleids regels voor het samen vouwen van de status van een aggregaatmonitor:

#### <a name="worst-state-policy"></a>Slechtste status beleid

De status van de aggregaatmonitor komt overeen met de status van de onderliggende monitor met de slechtste status. Dit is het meest voorkomende beleid dat wordt gebruikt door geaggregeerde monitors.

![Voor beeld van de slechtste status van Total monitor Rollup](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Percentage beleid

Het bron object komt overeen met de slechtste status van één lid van een opgegeven percentage doel objecten in de beste status. Dit beleid wordt gebruikt wanneer een bepaald percentage van doel objecten in orde moet zijn voordat het doel object in orde wordt beschouwd. Het percentage beleid sorteert de monitors in aflopende volg orde van de ernst van de status en de status van de aggregaatmonitor wordt berekend als de slechtste status N% (N wordt bepaald door de configuratie parameter *StateThresholdPercentage*).

Stel dat er vijf container exemplaren van een container installatie kopie zijn en dat hun individuele status **kritiek**, **waarschuwing**, **in orde** **, in orde, in** **orde**is.  De status van de monitor voor het CPU-gebruik van de container is **kritiek**, aangezien de slechtste status van 90% van de containers **kritiek** is wanneer deze in aflopende volg orde van ernst wordt gesorteerd.

## <a name="understand-the-monitoring-configuration"></a>Informatie over de bewakings configuratie

Azure Monitor voor containers bevat een aantal scenario's voor sleutel bewaking die als volgt zijn geconfigureerd.

### <a name="unit-monitors"></a>Unit-monitors

|**Monitor naam** | Monitor type | **Beschrijving** | **Parameter** | **Waarde** |
|-----------------|--------------|-----------------|---------------|-----------|
|Geheugen gebruik van knoop punt |Unit-monitor |Deze monitor evalueert elke minuut het geheugen gebruik van een knoop punt, met behulp van de gerapporteerde gegevens in cadvisor. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|CPU-gebruik van knoop punt |Unit-monitor |Met deze monitor wordt het CPU-gebruik van het knoop punt elke minuut gecontroleerd met behulp van de gerapporteerde gegevens in cadvisor. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Knooppunt status |Unit-monitor |Deze monitor controleert de knooppunt voorwaarden die door Kubernetes worden gerapporteerd.<br> Momenteel zijn de volgende knooppunt omstandigheden ingeschakeld: schijf belasting, geheugen belasting, PID-druk, onvoldoende schijf, netwerk niet beschikbaar, status gereed voor het knoop punt.<br> Als er *onvoldoende schijf ruimte* of *netwerk beschikbaar* **is, wordt de**status van de monitor gewijzigd in **kritiek** .<br> Als andere voor waarden gelijk zijn aan **waar**, anders dan de status **gereed** , verandert de monitor in een **waarschuwings** status. | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|Geheugen gebruik door containers |Unit-monitor |Deze monitor rapporteert de gecombineerde integriteits status van het geheugen gebruik (RSS) van de exemplaren van de container.<br> Er wordt een eenvoudige vergelijking uitgevoerd die elk voor beeld vergelijkt met één drempel en wordt opgegeven door de configuratie parameter **ConsecutiveSamplesForStateTransition**.<br> De status wordt berekend als de slechtste status van 90% van de container-exemplaren (StateThresholdPercentage), gesorteerd in aflopende volg orde van ernst van de status van de container (dat wil zeggen, kritiek, waarschuwing, in orde).<br> Als er geen record wordt ontvangen van een container exemplaar, wordt de status van de container instantie gerapporteerd als **onbekend**en heeft deze een hogere prioriteit in de sorteer volgorde ten opzichte van de **kritieke** status.<br> De status van elk afzonderlijk container exemplaar wordt berekend met behulp van de drempel waarden die zijn opgegeven in de configuratie. Als het gebruik de kritieke drempel waarde (90%) overschrijdt, heeft de instantie een **kritieke** status als deze lager is dan de kritieke drempel waarde (90%) maar groter dan waarschuwings drempelwaarde (80%), de instantie heeft een **waarschuwings** status. Anders is de status in **orde** . |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|CPU-gebruik van container |Unit-monitor |Deze monitor rapporteert de gecombineerde integriteits status van het CPU-gebruik van de exemplaren van de container.<br> Er wordt een eenvoudige vergelijking uitgevoerd die elk voor beeld vergelijkt met één drempel en wordt opgegeven door de configuratie parameter **ConsecutiveSamplesForStateTransition**.<br> De status wordt berekend als de slechtste status van 90% van de container-exemplaren (StateThresholdPercentage), gesorteerd in aflopende volg orde van ernst van de status van de container (dat wil zeggen, kritiek, waarschuwing, in orde).<br> Als er geen record wordt ontvangen van een container exemplaar, wordt de status van de container instantie gerapporteerd als **onbekend**en heeft deze een hogere prioriteit in de sorteer volgorde ten opzichte van de **kritieke** status.<br> De status van elk afzonderlijk container exemplaar wordt berekend met behulp van de drempel waarden die zijn opgegeven in de configuratie. Als het gebruik de kritieke drempel waarde (90%) overschrijdt, heeft de instantie een **kritieke** status als deze lager is dan de kritieke drempel waarde (90%) maar groter dan waarschuwings drempelwaarde (80%), de instantie heeft een **waarschuwings** status. Anders is de status in **orde** . |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Systeem werkbelasting peul gereed |Unit-monitor |Met deze monitor wordt de status gerapporteerd op basis van het percentage van de in de status gereed van een bepaalde werk belasting. De status is ingesteld op **kritiek** als minder dan 100% van de peulen de status in **orde** heeft |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Status van uitvoeren-API |Unit-monitor |Deze monitor rapporteert de status van de uitvoeren API-service. De monitor bevindt zich in een kritieke status in het geval uitvoeren API-eind punt niet beschikbaar is. Voor deze monitor wordt de status bepaald door een query uit te voeren op het eind punt knoop punten voor de uitvoeren-API-server. Anders dan bij een antwoord code van OK wordt de monitor gewijzigd in een **kritieke** status. | Geen configuratie-eigenschappen |||

### <a name="aggregate-monitors"></a>Aggregaatmonitor

|**Monitor naam** | **Beschrijving** | **Algoritme** |
|-----------------|-----------------|---------------|
|Knooppunt |Deze monitor is een aggregatie van alle knooppunt monitors. Dit komt overeen met de status van de onderliggende monitor met de slechtste status:<br> CPU-gebruik van knoop punt<br> Geheugen gebruik van knoop punt<br> Knooppunt status | Slechtste van|
|Knooppunt groep |Deze monitor rapporteert de gecombineerde integriteits status van alle knoop punten in de *agent pool*van de knooppunt groep. Dit is een monitor met drie statussen waarvan de status is gebaseerd op de slechtste staat van 80% van de knoop punten in de knooppunt groep, gesorteerd in aflopende volg orde van ernst van de status van knoop punten (dat wil zeggen, kritiek, waarschuwing, in orde).|Percentage |
|Knoop punten (bovenliggend knoop punt groep) |Dit is een geaggregeerde monitor van alle knooppunt groepen. De status is gebaseerd op de slechtste status van de onderliggende monitors (dat wil zeggen, de knooppunt Pools die aanwezig zijn in het cluster). |Slechtste van |
|Cluster (bovenliggend knoop punt/<br> Kubernetes-infra structuur) |Dit is de bovenliggende monitor die overeenkomt met de status van de onderliggende monitor met de slechtste status, dat wil zeggen kubernetes-infra structuur en-knoop punten. |Slechtste van |
|Kubernetes-infra structuur |Deze monitor rapporteert de gecombineerde integriteits status van de onderdelen van de beheerde infra structuur van het cluster. de status wordt berekend als ' slechtste ' van de onderliggende monitor statussen, d.w.z. uitvoeren-systeem werk belastingen en API-server status. |Slechtste van|
|Systeem belasting |Deze monitor rapporteert de status van een uitvoeren-systeem werk belasting. Deze monitor komt overeen met de status van de onderliggende monitor met de slechtste status, het verschil **in de status monitor Ready** en de containers in de werk belasting). |Slechtste van |
|Container |Deze monitor rapporteert de algehele status van een container in een bepaalde werk belasting. Deze monitor komt overeen met de status van de onderliggende monitor met de slechtste status, dat wil zeggen het **CPU-gebruik** en de monitors voor **geheugen gebruik** . |Slechtste van |

## <a name="next-steps"></a>Volgende stappen

Bekijk de status van het [monitor cluster](container-insights-health.md) voor meer informatie over het weer geven van de integriteits status van uw Kubernetes-cluster.