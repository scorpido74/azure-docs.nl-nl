---
title: Aanbevolen procedures voor het kiezen van een Time Series ID - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over aanbevolen procedures bij het kiezen van een Tijdreeks-id in Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083524"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Aanbevolen procedures voor het kiezen van een Time Series ID

In dit artikel wordt het belang van de Time Series ID voor uw Azure Time Series Insights Preview-omgeving en aanbevolen procedures voor het kiezen van een id samengevat.

## <a name="choose-a-time-series-id"></a>Een Time Series-id kiezen

Het selecteren van een geschikte Time Series ID is van cruciaal belang. Het kiezen van een Time Series ID is als het kiezen van een partitiesleutel voor een database. Dit is vereist wanneer u een Time Series Insights Preview-omgeving maakt. 

> [!IMPORTANT]
> Time Series-iD's zijn:
> * Een *hoofdlettergevoelige* eigenschap: letter- en tekenbehuizingen worden gebruikt in zoekopdrachten, vergelijkingen, updates en bij het partitioneren.
> * Een *onveranderlijke* eigenschap: eenmaal gemaakt kan deze niet meer worden gewijzigd.

> [!TIP]
> Als uw gebeurtenisbron een IoT-hub is, is uw Time Series ID waarschijnlijk ***iothub-connection-device-id.***

De belangrijkste best practices om te volgen zijn:

* Kies een partitiesleutel met veel verschillende waarden (bijvoorbeeld honderden of duizenden). In veel gevallen kan dit de apparaat-ID, sensor-ID of tag-ID in uw JSON zijn.
* De Time Series ID moet uniek zijn op het bladknooppuntniveau van uw [Time Series Model.](./time-series-insights-update-tsm.md)
* De tekenlimiet voor de eigenschappentekenreeks van de Time Series ID is 128. Voor de eigenschapswaarde van de Time Series ID is de tekenlimiet 1.024.
* Als een unieke eigenschapswaarde voor de Time Series ID ontbreekt, wordt deze behandeld als een null-waarde en volgt deze dezelfde regel van de uniciteitsbeperking.
* U ook maximaal *drie* belangrijke eigenschappen selecteren als uw Tijdreeks-ID. Hun combinatie zal een samengestelde sleutel zijn die de Time Series ID vertegenwoordigt.  
  > [!NOTE]
  > Uw drie belangrijke eigenschappen moeten tekenreeksen zijn.
  > U zou moeten vragen tegen deze samengestelde sleutel in plaats van een eigenschap tegelijk.

## <a name="select-more-than-one-key-property"></a>Meer dan één belangrijke eigenschap selecteren

In de volgende scenario's wordt beschreven het selecteren van meer dan één belangrijke eigenschap als uw tijdreeks-id.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Voorbeeld 1: Time Series ID met een unieke sleutel

* Je hebt oude vloten van activa. Elk heeft een unieke sleutel.
* Een vloot is uniek geïdentificeerd door de eigenschap **deviceId**. Voor een andere vloot is de unieke eigenschap **objectId.** Geen van beide vloot bevat de unieke eigendom van de andere vloot. In dit voorbeeld selecteert u twee toetsen, **deviceId** en **objectId,** als unieke toetsen.
* We accepteren null-waarden en het ontbreken van de aanwezigheid van een eigenschap in de gebeurtenispayload telt als een null-waarde. Dit is ook de juiste manier om het verzenden van gegevens naar twee gebeurtenisbronnen te verwerken waar de gegevens in elke gebeurtenisbron een unieke Time Series ID hebben.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Voorbeeld 2: Tijdreeks-ID met een samengestelde sleutel

* U vereist dat meerdere eigenschappen uniek zijn binnen dezelfde vloot van activa. 
* Je bent een fabrikant van slimme gebouwen en zet sensoren in elke kamer in. In elke kamer hebt u meestal dezelfde waarden voor **sensorId.** Voorbeelden zijn **sensor1,** **sensor2**en **sensor3**.
* Uw gebouw heeft overlappende vloer- en kamernummers op verschillende locaties in de **accommodatie.** Deze getallen hebben waarden zoals **1a**, **2b**en **3a**.
* Je hebt een woning, **locatie,** die waarden bevat zoals **Redmond,** **Barcelona**en **Tokio.** Om uniciteit te creëren, wijst u de volgende drie eigenschappen aan als uw Time Series ID-toetsen: **sensorId,** **flrRm**en **locatie**.

Voorbeeld van een raw-gebeurtenis:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

In de Azure-portal u vervolgens als volgt de samengestelde sleutel invoeren: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Volgende stappen

* Lees meer over [gegevensmodellering](./time-series-insights-update-tsm.md).

* Plan uw [Azure Time Series Insights Preview-omgeving](./time-series-insights-update-plan.md).