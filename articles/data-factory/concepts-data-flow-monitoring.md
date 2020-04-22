---
title: Gegevensstroom visuele bewaking toewijzen
description: Azure Data Factory-gegevensstromen visueel controleren
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/17/2020
ms.openlocfilehash: 18099e853aa44e4434a14d7ea913f968593021ec
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687902"
---
# <a name="monitor-data-flows"></a>Gegevensstromen controleren

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nadat u uw gegevensstroom hebt voltooid en debuggen, wilt u uw gegevensstroom plannen om volgens een planning in de context van een pijplijn uit te voeren. U de pijplijn plannen vanuit Azure Data Factory met Triggers. U de optie Trigger Now van de Azure Data Factory Pipeline Builder gebruiken om een runsuitvoering uit te voeren om uw gegevensstroom binnen de pijplijncontext te testen.

Wanneer u uw pijplijn uitvoert, u de pijplijn en alle activiteiten in de pijplijn controleren, inclusief de activiteit Gegevensstroom. Klik op het monitorpictogram in het linkerazure datafactory-gebruikersinterfacepaneel. U ziet een scherm vergelijkbaar met de onderstaande. Met de gemarkeerde pictogrammen u inzoomen op de activiteiten in de pijplijn, inclusief de activiteit Gegevensstroom.

![Gegevensstroombewaking](media/data-flow/mon001.png "Bewaking gegevensstroom")

U ziet statistieken op dit niveau, inclusief de looptijden en status. De run-id op het activiteitsniveau is anders dan de run-id op pijplijnniveau. De run-id op het vorige niveau is voor de pijplijn. Als u op de bril klikt, krijgt u diepgaande details over de uitvoering van uw gegevensstroom.

![Gegevensstroombewaking](media/data-flow/mon002.png "Bewaking gegevensstroom")

Wanneer u zich in de grafische knooppuntbewakingsweergave bevindt, ziet u een vereenvoudigde weergaveversie van uw gegevensstroomgrafiek.

![Gegevensstroombewaking](media/data-flow/mon003.png "Bewaking gegevensstroom")

Hier volgt een videooverzicht van het monitoren van de prestaties van uw gegevensstromen vanuit het ADF-bewakingsscherm:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Uitvoeringsplannen voor gegevensstromen weergeven

Wanneer uw gegevensstroom wordt uitgevoerd in Spark, bepaalt Azure Data Factory optimale codepaden op basis van het geheel van uw gegevensstroom. Bovendien kunnen de uitvoeringspaden optreden op verschillende scale-outknooppunten en gegevenspartities. Daarom geeft de bewakingsgrafiek het ontwerp van uw stroom weer, rekening houdend met het uitvoeringspad van uw transformaties. Wanneer u op afzonderlijke knooppunten klikt, ziet u 'groeperingen' die code vertegenwoordigen die samen op het cluster is uitgevoerd. De timings en tellingen die u ziet, vertegenwoordigen deze groepen in tegenstelling tot de afzonderlijke stappen in uw ontwerp.

![Gegevensstroombewaking](media/data-flow/mon004.png "Bewaking gegevensstroom")

* Wanneer u op de open ruimte in het controlevenster klikt, worden in de statistieken in het onderste deelvenster de timing en rijtellingen weergegeven voor elke sink en de transformaties die hebben geleid tot de sinkgegevens voor transformatieregel.

* Wanneer u afzonderlijke transformaties selecteert, ontvangt u extra feedback op het rechterpaneel met partitiestatistieken, kolomtellingen, scheefheid (hoe gelijkmatig worden de gegevens verdeeld over partities) en kurtose (hoe stekek is de gegevens).

* Wanneer u op de gootsteen in de knooppuntweergave klikt, ziet u kolomregel. Er zijn drie verschillende methoden die kolommen worden verzameld in uw gegevensstroom om te landen in de gootsteen. Dit zijn:

  * Berekend: u gebruikt de kolom voor voorwaardelijke verwerking of binnen een expressie in uw gegevensstroom, maar landt deze niet in de gootsteen
  * Afgeleid: de kolom is een nieuwe kolom die u in uw stroom hebt gegenereerd, d.w.z. deze was niet aanwezig in de bron
  * Toegewezen: de kolom is afkomstig van de bron en uw bent het toewijzen aan een gootsteenveld
  * Status van gegevensstroom: de huidige status van uw uitvoering
  * Opstarttijd van het cluster: hoeveelheid tijd om de JIT Spark-compute-omgeving te verkrijgen voor de uitvoering van uw gegevensstroom
  * Aantal transformaties: hoeveel transformatiestappen worden uitgevoerd in uw stroom
  
![Gegevensstroombewaking](media/data-flow/monitornew.png "Data Flow Monitoring Nieuw")  
  
## <a name="monitor-icons"></a>Monitorpictogrammen

Dit pictogram betekent dat de transformatiegegevens al in de cache op het cluster zijn opgeslagen, dus de timings en het uitvoeringspad hebben daar rekening mee gehouden:

![Gegevensstroombewaking](media/data-flow/mon004.png "Bewaking gegevensstroom")

U ziet ook groene cirkelpictogrammen in de transformatie. Ze vertegenwoordigen een telling van het aantal putten waarin gegevens stromen.
