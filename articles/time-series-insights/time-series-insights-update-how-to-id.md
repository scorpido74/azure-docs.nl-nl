---
title: Aanbevolen procedures voor het kiezen van een time series-ID in Azure Time Series Insights preview | Microsoft Docs
description: Bekijk aanbevolen procedures wanneer u een tijd reeks-ID kiest in Azure Time Series Insights preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: 48f1fb542f5e28c7b8130d03cd86442390a8ad56
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989943"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Aanbevolen procedures voor het kiezen van een time series-ID

Dit artikel bevat een overzicht van het belang van de tijd reeks-ID voor uw Azure Time Series Insights-voorbeeld omgeving en aanbevolen procedures voor het kiezen van een.

## <a name="choose-a-time-series-id"></a>Een Time Series-id kiezen

Het kiezen van een tijd reeks-ID is hetzelfde als het kiezen van een partitie sleutel voor een Data Base. U moet deze selecteren tijdens het maken van een Time Series Insights preview-omgeving. Het is een *onveranderbare* eigenschap. Dat wil zeggen, nadat u een Time Series Insights preview-omgeving met een tijd reeks-ID hebt gemaakt, kunt u deze niet wijzigen voor die omgeving. 

> [!IMPORTANT]
> De time series-ID is hoofdletter gevoelig.

Het selecteren van de juiste tijd reeks-ID is van cruciaal belang. Hier volgen enkele van de aanbevolen procedures die u kunt volgen:

* Kies een partitie sleutel met veel afzonderlijke waarden (bijvoorbeeld honderden of duizenden). In veel gevallen kan dit de apparaat-ID, sensor-ID of label-ID in uw JSON zijn.
* De time series-ID moet uniek zijn op het leaf-knooppunt niveau van uw [Time Series-model](./time-series-insights-update-tsm.md).
* Als uw gebeurtenis bron een IoT-hub is, zal uw tijd reeks-ID waarschijnlijk *iothub-Connection-apparaat-id*zijn.
* De teken limiet voor de eigenschaps naam van de tijd reeks-ID is 128. De teken limiet voor de eigenschaps waarde van de tijd reeks-ID is 1.024.
* Als een unieke eigenschaps waarde voor de time series-ID ontbreekt, wordt deze behandeld als een null-waarde en volgt dezelfde regel van de uniekheids beperking.
* U kunt ook Maxi maal *drie* sleutel eigenschappen selecteren als uw tijd reeks-id. De combi natie hiervan is een samengestelde sleutel die de tijd reeks-ID vertegenwoordigt.  

  > [!NOTE]
  > Uw drie sleutel eigenschappen moeten teken reeksen zijn.
  > U moet een query uitvoeren op deze samengestelde sleutel in plaats van één eigenschap tegelijk.

In de volgende scenario's wordt het selecteren van meer dan één sleutel eigenschap als uw tijd reeks-ID beschreven.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Voor beeld 1: een time reeks-ID met een unieke sleutel

* U hebt verouderde vloots van activa. Elk heeft een unieke sleutel.
* Eén vloot wordt uniek geïdentificeerd door de property **deviceId**. Voor een andere vloot is de unieke eigenschap **objectId**. Geen van beide vloot bevat de unieke eigenschap van de andere vloot. In dit voor beeld selecteert u twee sleutels, **deviceId** en **objectId**als unieke sleutels.
* We accepteren Null-waarden en het ontbreken van de aanwezigheid van een eigenschap in de nettolading van de gebeurtenis telt als een null-waarde. Dit is ook de juiste manier om het verzenden van gegevens naar twee gebeurtenis bronnen te verwerken, waarbij de gegevens in elke gebeurtenis bron een unieke time series-ID hebben.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Voor beeld 2: een tijd reeks-ID met een samengestelde sleutel

* U wilt dat meerdere eigenschappen uniek zijn binnen dezelfde vloot van activa. 
* U bent een fabrikant van Smart gebouwen en implementeert Sens oren in elke kamer. In elke ruimte hebt u doorgaans dezelfde waarden voor **sensorId**. Voor beelden zijn **sensor1**, **sensor2**en **sensor3**.
* Uw gebouw heeft overlappende vloer-en kamer nummers tussen sites in de eigenschap **flrRm**. Deze getallen hebben waarden als **1a**, **2b**en **3a**.
* U hebt een eigenschap, **locatie**, die waarden bevat zoals **Redmond**, **Barcelona**en **Tokyo**. Als u een uniek karakter wilt maken, wijst u de volgende drie eigenschappen toe als uw tijd reeks-ID-sleutels: **sensorId**, **flrRm**en **locatie**.

Voor beeld van onbewerkte gebeurtenis:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

In de Azure Portal kunt u deze samengestelde sleutel als volgt invoeren: 

`[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]`

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [gegevens modellering](./time-series-insights-update-tsm.md).

* Plan uw [Azure time series Insights preview-omgeving](./time-series-insights-update-plan.md).