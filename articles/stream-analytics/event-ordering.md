---
title: Beleid voor het bestellen van gebeurtenissen configureren voor Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u zelfs bestelinstellingen configureren in Stream Analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461191"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Beleid voor het bestellen van gebeurtenissen configureren voor Azure Stream Analytics

In dit artikel wordt beschreven hoe u het beleid voor late aankomst en out-of-order-gebeurtenis instellen en gebruiken in Azure Stream Analytics. Deze beleidsregels worden alleen toegepast wanneer u de clausule [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) in uw query gebruikt.

## <a name="event-time-and-arrival-time"></a>Evenementtijd en aankomsttijd

Uw Stream Analytics-taak kan gebeurtenissen verwerken op basis van *de gebeurtenistijd* of *de aankomsttijd.* **Gebeurtenis-/toepassingstijd** is de tijdstempel die aanwezig is in gebeurtenispayload (wanneer de gebeurtenis is gegenereerd). **Aankomsttijd** is de tijdstempel waarop de gebeurtenis is ontvangen bij de invoerbron (Event Hubs/IoT Hub/Blob-opslag). 

Stream Analytics verwerkt standaard gebeurtenissen op *aankomsttijd,* maar u ervoor kiezen om gebeurtenissen te verwerken op *gebeurtenistijd* met behulp van [TIMESTAMP BY-component](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) in uw query. Late aankomst en out-of-order beleid zijn alleen van toepassing als u gebeurtenissen verwerkt op gebeurtenistijd. Overweeg het gebruik van vereisten voor latentie en juistheid voor uw scenario bij het configureren van deze instellingen. 

## <a name="what-is-late-arrival-policy"></a>What is late arrival policy? (Wat is beleid voor late aankomst?)

Soms komen evenementen te laat om verschillende redenen. Een gebeurtenis die bijvoorbeeld 40 seconden te laat aankomt, heeft een gebeurtenistijd = 00:10:00 en aankomsttijd = 00:10:40. Als u het beleid voor late aankomst instelt op 15 seconden, wordt elke gebeurtenis die later dan 15 seconden binnenkomt, verwijderd (niet verwerkt door Stream Analytics) of wordt de gebeurtenistijd aangepast. In het bovenstaande voorbeeld, als de gebeurtenis 40 seconden te laat is aangekomen (meer dan het beleid is ingesteld), wordt de gebeurtenistijd aangepast aan het maximum van het beleid voor late aankomst 00:10:25 (aankomsttijd - beleidswaarde voor late aankomst). Standaard beleid voor late aankomst is 5 seconden.

## <a name="what-is-out-of-order-policy"></a>Wat is buiten het orde beleid? 

Evenement kan ook buiten de orde komen. Nadat de gebeurtenistijd is aangepast op basis van het beleid voor late aankomst, u er ook voor kiezen om gebeurtenissen die niet in orde zijn automatisch te laten vallen of aan te passen. Als u dit beleid instelt op 8 seconden, worden alle gebeurtenissen die niet in orde zijn, maar binnen het venster van 8 seconden, opnieuw geordend op gebeurtenistijd. Gebeurtenissen die later aankomen, worden verwijderd of aangepast aan de maximale out-of-order beleidswaarde. Standaard beleid buiten de orde is 0 seconden. 

## <a name="adjust-or-drop-events"></a>Gebeurtenissen aanpassen of neerzetten

Als gebeurtenissen te laat of out-of-order aankomen op basis van het beleid dat u hebt geconfigureerd, u dergelijke gebeurtenissen laten vallen (niet verwerkt door Stream Analytics) of hun gebeurtenistijd laten aanpassen.

Laten we een voorbeeld van dit beleid in actie zien.
<br> **Beleid voor late aankomst:** 15 seconden
<br> **Out-of-order beleid:** 8 seconden

| Gebeurtenis nr. | Tijdstip van gebeurtenis | Aankomsttijd | System.Timestamp | Uitleg |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Evenement kwam te laat en buiten tolerantie niveau. Dus gebeurtenis tijd wordt aangepast aan maximale late aankomst tolerantie.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Evenement kwam te laat, maar binnen tolerantie niveau. De gebeurtenistijd wordt dus niet aangepast.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Evenement kwam op tijd. Geen aanpassing nodig.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Evenement kwam out-of-order, maar binnen de tolerantie van 8 seconden. De gebeurtenistijd wordt dus niet aangepast. Voor analysedoeleinden wordt deze gebeurtenis beschouwd als voorafgaand aan gebeurtenisnummer 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Evenement kwam out-of-order en buiten tolerantie van 8 seconden. De gebeurtenistijd wordt dus aangepast aan het maximum aan tolerantie buiten de orde. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Kunnen deze instellingen de uitvoer van mijn taak vertragen? 

Ja. Standaard is het out-of-order-beleid ingesteld op nul (00 minuten en 00 seconden). Als u de standaardwaarde wijzigt, wordt de eerste uitvoer van uw taak vertraagd door deze waarde (of meer). 

Als een van de partities van uw ingangen geen gebeurtenissen ontvangt, moet u verwachten dat uw uitvoer wordt vertraagd door de beleidswaarde voor late aankomst. Lees de sectie Fout van invoerpartitie hieronder voor meer informatie. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Ik zie LateInputEvents-berichten in mijn activiteitenlogboek

Deze berichten worden getoond om u te informeren dat gebeurtenissen te laat zijn aangekomen en worden verwijderd of aangepast aan uw configuratie. U deze berichten negeren als u het beleid voor late aankomst op de juiste manier hebt geconfigureerd. 

Voorbeeld van dit bericht is: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Ik zie InputPartitionNotProgressing in mijn activiteitenlogboek

Uw invoerbron (Event Hub/IoT Hub) heeft waarschijnlijk meerdere partities. Azure Stream Analytics produceert uitvoer voor tijdstempel t1 pas nadat alle partities die worden gecombineerd ten minste op tijd t1 zijn. Neem bijvoorbeeld aan dat de query wordt gelezen uit een gebeurtenishubpartitie met twee partities. Een van de partities, P1, heeft gebeurtenissen tot tijd t1. De andere partitie, P2, heeft gebeurtenissen tot de tijd t1 + x. Output wordt vervolgens geproduceerd tot de tijd t1. Maar als er een expliciete partitie door PartitionId clausule, zowel de partities onafhankelijk. 

Wanneer meerdere partities van dezelfde invoerstroom worden gecombineerd, is de tolerantie voor late aankomst de maximale tijd die elke partitie wacht op nieuwe gegevens. Als er één partitie in uw gebeurtenishub staat of als IoT Hub geen ingangen ontvangt, wordt de tijdlijn voor die partitie pas verlengd als de drempel voor de late aankomsttolerantie is bereikt. Dit vertraagt uw output door de drempel voor late aankomsttolerantie. In dergelijke gevallen ziet u mogelijk het volgende bericht:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Dit bericht om u te informeren dat ten minste één partitie in uw invoer leeg is en uw uitvoer zal vertragen door de late aankomstdrempel. Om dit te overwinnen, wordt u aanbevolen:  
1. Zorg ervoor dat alle partities van uw Event Hub/IoT Hub invoer ontvangen. 
2. Gebruik partitie per partitie-id-component in uw query. 

## <a name="next-steps"></a>Volgende stappen
* [Overwegingen over tijdsverwerking](stream-analytics-time-handling.md)
* [Statistieken beschikbaar in Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
