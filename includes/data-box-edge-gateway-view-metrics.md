---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67176486"
---
U kunt ook de metrische gegevens weer geven om de prestaties van het apparaat te controleren en in sommige gevallen voor het oplossen van problemen met apparaten.

Voer de volgende stappen uit in de Azure Portal om een grafiek te maken voor de geselecteerde metrische gegevens van het apparaat.

1. Ga voor uw resource in het Azure Portal naar **bewaking > metrische gegevens** en selecteer **metrische gegevens toevoegen**.

    ![Waarde toevoegen](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. De resource wordt automatisch ingevuld.  

    ![Huidige resource](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Als u een andere resource wilt opgeven, selecteert u de resource. Selecteer op de Blade **een resource selecteren** het abonnement, de resource groep, het resource type en de specifieke resource waarvoor u de metrische gegevens wilt weer geven en selecteer **Toep assen**.

    ![Een andere resource kiezen](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. Selecteer in de vervolg keuzelijst een metriek om uw apparaat te bewaken. De metrische gegevens kunnen de metrische gegevens over de **capaciteit** of de metrische gegevens over de **trans actie**zijn. De metrische gegevens over de capaciteit zijn gerelateerd aan de capaciteit van het apparaat. De metrische gegevens van de trans actie zijn gerelateerd aan de lees-en schrijf bewerkingen die worden Azure Storage.

    |Metrische capaciteitsgegevens                     |Beschrijving  |
    |-------------------------------------|-------------|
    |**Beschik bare capaciteit**               | Verwijst naar de grootte van de gegevens die naar het apparaat kunnen worden geschreven. Met andere woorden, dit is de capaciteit die beschikbaar kan worden gemaakt op het apparaat. <br></br>U kunt de capaciteit van het apparaat vrijmaken door de lokale kopie van bestanden te verwijderen die zowel op het apparaat als in de Cloud zijn gekopieerd.        |
    |**Totale capaciteit**                   | Verwijst naar het totale aantal bytes op het apparaat om gegevens naar te schrijven. Dit wordt ook wel de totale grootte van de lokale cache genoemd. <br></br> U kunt nu de capaciteit van een bestaand virtueel apparaat verg Roten door een gegevens schijf toe te voegen. Voeg een gegevens schijf toe via het Hyper Visor beheer voor de virtuele machine en start vervolgens de virtuele machine opnieuw op. De lokale opslag groep van het gateway-apparaat wordt uitgebreid met de nieuwe toegevoegde gegevens schijf. <br></br>Ga voor meer informatie naar [een harde schijf toevoegen voor Hyper-V virtuele machine](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Metrische gegevens voor transacties              | Beschrijving         |
    |-------------------------------------|---------|
    |**Geüploade Cloud bytes (apparaat)**    | Som van alle bytes die zijn geüpload over alle shares op uw apparaat        |
    |**Geüploade Cloud bytes (delen)**     | Geüploade bytes per share. Dit kan zijn: <br></br> Gem. Dit is het (de som van alle geüploade bytes per share/aantal shares),  <br></br>Max. Dit is het maximum aantal bytes dat van een share is geüpload <br></br>Min. Dit is het minimum aantal bytes dat van een share is geüpload      |
    |**Door Voer van Cloud downloaden (delen)**| Geladen bytes per share. Dit kan zijn: <br></br> Gem. Dit is de (som van alle bytes die worden gelezen of gedownload naar een share/aantal shares) <br></br> Max. Dit is het maximum aantal bytes dat is gedownload van een share<br></br> en min. Dit is het minimum aantal bytes dat is gedownload van een share  |
    |**Lees doorvoer voor Cloud**            | Som van alle bytes die in de Cloud zijn gelezen over alle shares op uw apparaat     |
    |**Upload doorvoer van Cloud**          | Som van alle bytes die in de Cloud zijn geschreven over alle shares op uw apparaat     |
    |**Upload doorvoer van Cloud (delen)**  | De som van alle bytes die naar de cloud worden geschreven vanuit een share/aantal shares is gemiddeld, Max en min per aandeel      |
    |**Lees doorvoer (netwerk)**           | Bevat de systeem netwerk doorvoer voor alle bytes die in de Cloud zijn gelezen. Deze weer gave kan gegevens bevatten die niet zijn beperkt tot shares. <br></br>Bij het splitsen wordt het verkeer voor alle netwerk adapters op het apparaat weer gegeven. Dit omvat adapters die niet zijn verbonden of ingeschakeld.      |
    |**Schrijf doorvoer (netwerk)**       | Bevat de systeem netwerk doorvoer voor alle bytes die worden geschreven naar de Cloud. Deze weer gave kan gegevens bevatten die niet zijn beperkt tot shares. <br></br>Bij het splitsen wordt het verkeer voor alle netwerk adapters op het apparaat weer gegeven. Dit omvat adapters die niet zijn verbonden of ingeschakeld.          |
    |**Edge Compute-geheugen gebruik**      | Deze metriek is niet van toepassing op Data Box Gateway en is daarom niet ingevuld.          |
    |**Edge Compute-percentage CPU**    | Deze metriek is niet van toepassing op Data Box Gateway en is daarom niet ingevuld.         |

4. Wanneer een metriek is geselecteerd in de vervolg keuzelijst, kan aggregatie ook worden gedefinieerd. Aggregatie verwijst naar de werkelijke waarde die is geaggregeerd over een bepaalde periode. De geaggregeerde waarden kunnen het gemiddelde, het minimum of de maximum waarde zijn. Selecteer de aggregatie van AVG, Max of min.

    ![Grafiek weer geven](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Als de metrische gegevens die u hebt geselecteerd meerdere exemplaren hebben, is de optie splitsen beschikbaar. Selecteer **splitsing Toep assen** en selecteer vervolgens de waarde die u wilt weer geven van de uitsplitsing.

    ![Splitsing Toep assen](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Als u de uitsplitsing nu alleen voor enkele exemplaren wilt zien, kunt u de gegevens filteren. Als u bijvoorbeeld de netwerk doorvoer alleen wilt bekijken voor de twee verbonden netwerk interfaces op uw apparaat, kunt u die interfaces filteren. Selecteer **filter toevoegen** en geef de naam van de netwerk interface op voor filteren.

    ![Filter toevoegen](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. U kunt de grafiek ook aan het dash board vastmaken voor eenvoudige toegang.

    ![Vastmaken aan dashboard](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Als u grafiek gegevens wilt exporteren naar een Excel-spread sheet of een koppeling naar de grafiek wilt ontvangen die u kunt delen, selecteert u de optie delen in de opdracht balk.

    ![Gegevens exporteren](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)