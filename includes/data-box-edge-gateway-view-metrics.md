---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176486"
---
U ook de statistieken bekijken om de prestaties van het apparaat te controleren en in sommige gevallen voor het oplossen van apparaatproblemen.

Neem de volgende stappen in de Azure-portal om een grafiek te maken voor geselecteerde apparaatstatistieken.

1. Ga voor uw resource in de Azure-portal naar **Monitoring > Metrics** en selecteer Metrische **toevoegen**.

    ![Waarde toevoegen](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. De bron wordt automatisch ingevuld.  

    ![Huidige resource](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Als u een andere resource wilt opgeven, selecteert u de resource. Selecteer **bij Een resourceblad selecteren** het abonnement, de resourcegroep, het resourcetype en de specifieke resource waarvoor u de statistieken wilt weergeven en selecteer **Toepassen**.

    ![Een andere bron kiezen](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. Selecteer in de vervolgkeuzelijst een statistiek om uw apparaat te controleren. De statistieken kunnen **capaciteitsstatistieken** of **transactiestatistieken**zijn. De capaciteitsstatistieken zijn gerelateerd aan de capaciteit van het apparaat. De transactiestatistieken zijn gerelateerd aan de lees- en schrijfbewerkingen naar Azure Storage.

    |Metrische capaciteitsgegevens                     |Beschrijving  |
    |-------------------------------------|-------------|
    |**Beschikbare capaciteit**               | Verwijst naar de grootte van de gegevens die naar het apparaat kunnen worden geschreven. Met andere woorden, dit is de capaciteit die beschikbaar kan worden gesteld op het apparaat. <br></br>U de apparaatcapaciteit vrijmaken door de lokale kopie te verwijderen van bestanden met een kopie op zowel het apparaat als de cloud.        |
    |**Totale capaciteit**                   | Verwijst naar de totale bytes op het apparaat om gegevens naar te schrijven. Dit wordt ook wel de totale grootte van de lokale cache genoemd. <br></br> U nu de capaciteit van een bestaand virtueel apparaat vergroten door een gegevensschijf toe te voegen. Voeg een gegevensschijf toe via het hypervisorbeheer voor de VM en start uw VM opnieuw op. De lokale opslaggroep van het Gateway-apparaat wordt uitgebreid met de nieuw toegevoegde gegevensschijf. <br></br>Ga voor meer informatie naar [Een harde schijf toevoegen voor de virtuele hyper-v-machine.](https://www.youtube.com/watch?v=EWdqUw9tTe4) |
    
    |Metrische gegevens voor transacties              | Beschrijving         |
    |-------------------------------------|---------|
    |**Cloudbytes geüpload (apparaat)**    | Som van alle bytes die zijn geüpload over alle aandelen op uw apparaat        |
    |**Cloudbytes geüpload (delen)**     | Bytes geüpload per aandeel. Dit kan zijn: <br></br> Avg, dat is de (Som van alle bytes geüpload per aandeel / Aantal aandelen),  <br></br>Max, dat is het maximum aantal bytes geüpload van een aandeel <br></br>Min, dat is het minimum aantal bytes geüpload van een aandeel      |
    |**Cloud downloaddoorvoer (delen)**| Bytes gedownload per aandeel. Dit kan zijn: <br></br> Avg, dat is de (Som van alle bytes gelezen of gedownload naar een aandeel / Aantal aandelen) <br></br> Max, dat is het maximum aantal bytes gedownload van een aandeel<br></br> en Min, dat is het minimum aantal bytes gedownload van een aandeel  |
    |**Cloudleesdoorvoer**            | Som van alle bytes die vanuit de cloud worden gelezen over alle aandelen op uw apparaat     |
    |**Doorvoer van cloudupload**          | Som van alle bytes die naar de cloud zijn geschreven over alle aandelen op uw apparaat     |
    |**Doorvoer van cloudupload (delen)**  | Som van alle bytes geschreven naar de cloud van een aandeel / # van de aandelen is gemiddeld, max, en min per aandeel      |
    |**Doorvoer lezen (netwerk)**           | Inclusief de netwerkdoorvoer van het systeem voor alle bytes die uit de cloud worden gelezen. Deze weergave kan gegevens bevatten die niet beperkt zijn tot aandelen. <br></br>Splitsen toont het verkeer over alle netwerkadapters op het apparaat. Dit geldt ook voor adapters die niet zijn aangesloten of ingeschakeld.      |
    |**Doorvoer schrijven (netwerk)**       | Inclusief de netwerkdoorvoer van het systeem voor alle bytes die naar de cloud zijn geschreven. Deze weergave kan gegevens bevatten die niet beperkt zijn tot aandelen. <br></br>Splitsen toont het verkeer over alle netwerkadapters op het apparaat. Dit geldt ook voor adapters die niet zijn aangesloten of ingeschakeld.          |
    |**Edge compute - geheugengebruik**      | Deze statistiek is niet van toepassing op Data Box Gateway en dus niet ingevuld.          |
    |**Edge compute - percentage CPU**    | Deze statistiek is niet van toepassing op Data Box Gateway en dus niet ingevuld.         |

4. Wanneer een statistiek is geselecteerd uit de vervolgkeuzelijst, kan ook aggregatie worden gedefinieerd. Aggregatie verwijst naar de werkelijke waarde die over een bepaalde tijdspanne is geaggregeerd. De geaggregeerde waarden kunnen gemiddeld, minimaal of de maximale waarde zijn. Selecteer de aggregatie in Avg, Max of Min.

    ![Grafiek weergeven](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Als de statistiek die u hebt geselecteerd meerdere exemplaren heeft, is de splitsingsoptie beschikbaar. Selecteer **Splitsen toepassen** en selecteer vervolgens de waarde waarmee u de uitsplitsing wilt zien.

    ![Splitsen toepassen](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Als u de uitsplitsing nu slechts voor een paar exemplaren wilt zien, u de gegevens filteren. Als u in dit geval bijvoorbeeld de netwerkdoorvoer alleen wilt zien voor de twee verbonden netwerkinterfaces op uw apparaat, u deze interfaces filteren. Selecteer **Filter toevoegen** en geef de naam van de netwerkinterface op voor het filteren.

    ![Filter toevoegen](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. U de grafiek ook vastmaken aan het dashboard voor eenvoudige toegang.

    ![Vastmaken aan dashboard](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Als u grafiekgegevens wilt exporteren naar een Excel-spreadsheet of een koppeling wilt maken naar de grafiek die u delen, selecteert u de optie delen op de opdrachtbalk.

    ![Gegevens exporteren](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)