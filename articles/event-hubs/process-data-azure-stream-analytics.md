---
title: Gegevens van Gebeurtenishubs Azure verwerken met Stream Analytics | Microsoft Documenten
description: In dit artikel ziet u hoe u gegevens verwerken vanuit uw Azure-gebeurtenishub met behulp van een Azure Stream Analytics-taak.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991950"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Gegevens verwerken vanuit uw gebeurtenishub met Azure Stream Analytics 
Met de Azure Stream Analytics-service u eenvoudig streaminggegevens innemen, verwerken en analyseren vanuit Azure Event Hubs, zodat krachtige inzichten realtime acties kunnen uitvoeren. Met deze integratie u snel een hot-path analytics-pijplijn maken. U de Azure-portal gebruiken om binnenkomende gegevens te visualiseren en een Stream Analytics-query te schrijven. Zodra uw query klaar is, u deze in slechts een paar klikken in productie nemen. 

## <a name="key-benefits"></a>Belangrijkste voordelen
Dit zijn de belangrijkste voordelen van Azure Event Hubs en Azure Stream Analytics-integratie: 
- **Voorbeeld van gegevens** : u een voorbeeld bekijken van binnenkomende gegevens van een gebeurtenishub in de Azure-portal.
- **Test uw query** : bereid een transformatiequery voor en test deze rechtstreeks in de Azure-portal. Zie Documentatie [van querytaal streamen](/stream-analytics-query/built-in-functions-azure-stream-analytics) voor de syntaxis van querytaal voor de syntaxis van querytaal.
- **Uw query implementeren in productie:** u de query in productie implementeren door een Azure Stream Analytics-taak te maken en te starten.

## <a name="end-to-end-flow"></a>End-to-end flow

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
1. Navigeer naar de **naamruimte van** uw gebeurtenishubs en navigeer vervolgens naar de **gebeurtenishub**met de binnenkomende gegevens. 
1. Selecteer **Procesgegevens** op de pagina met gebeurtenishubs.  

    ![Tegel gegevens verwerken](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Selecteer **Verkennen** op de **tegel Realtime-inzichten inschakelen in de tegel Gebeurtenissen.** 

    ![StreamAnalytics selecteren](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. U ziet een querypagina met waarden die al zijn ingesteld voor de volgende velden:
    1. Uw **gebeurtenishub** als invoer voor de query.
    1. Voorbeeld **van SQL-query** met SELECT-instructie. 
    1. Een **uitvoeralias** die moet worden verwezen naar de testresultaten van uw query. 

        ![Query-editor](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Wanneer u deze functie voor de eerste keer gebruikt, vraagt deze pagina om uw toestemming om een consumentengroep te maken en een beleid voor uw gebeurtenishub om een voorbeeld van binnenkomende gegevens te bekijken.
1. Selecteer **Maken** in het **voorbeeldvenster Invoer** zoals weergegeven in de voorgaande afbeelding. 
1. U ziet meteen een momentopname van de nieuwste binnenkomende gegevens op dit tabblad.
    - Het serialisatietype in uw gegevens wordt automatisch gedetecteerd (JSON/CSV). U het ook handmatig wijzigen in JSON/CSV/AVRO.
    - U een voorbeeld bekijken van binnenkomende gegevens in de tabelindeling of de raw-indeling. 
    - Als de weergegeven gegevens niet actueel zijn, selecteert u **Vernieuwen** om de nieuwste gebeurtenissen weer te geven. 

        Hier is een voorbeeld van gegevens ![in de **tabelindeling:** resultaten in de tabelindeling](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Hier is een voorbeeld van gegevens in het **ruwe formaat:** 

        ![Resultaten in de raw-indeling](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Selecteer **Testquery** om de momentopname van de testresultaten van uw query te bekijken op het tabblad **Testresultaten.** U ook de resultaten downloaden.

    ![Queryresultaten testen](./media/process-data-azure-stream-analytics/test-results.png)
1. Schrijf uw eigen query om de gegevens te transformeren. Zie [Naslaganalyse-naslaggids voor querytaal](/stream-analytics-query/stream-analytics-query-language-reference).
1. Zodra u de query hebt getest en u deze wilt verplaatsen naar productie, selecteert u **Query implementeren**. Als u de query wilt implementeren, maakt u een Azure Stream Analytics-taak waarin u een uitvoer voor uw taak instellen en de taak starten. Als u een Stream Analytics-taak wilt maken, geeft u een naam voor de taak op en selecteert **u Maken**.

      ![Een Azure Stream Analytics-taak maken](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  We raden u aan een consumentengroep en een beleid te maken voor elke nieuwe Azure Stream Analytics-taak die u maakt op de pagina Gebeurtenishubs. Consumentengroepen staan slechts vijf gelijktijdige lezers toe, dus het verstrekken van een speciale consumentengroep voor elke taak zal eventuele fouten voorkomen die kunnen voortvloeien uit het overschrijden van die limiet. Met een speciaal beleid u uw sleutel roteren of machtigingen intrekken zonder dat dit gevolgen heeft voor andere bronnen. 
1. Je Stream Analytics-taak wordt nu gemaakt waarbij je query dezelfde is die je hebt getest en invoer je gebeurtenishub is. 

9.  Als u de pijplijn wilt voltooien, stelt u de **uitvoer** van de query in en selecteert u **Start** om de taak te starten.

    > [!NOTE]
    > Vergeet de uitvoeralias niet te vervangen door de uitvoernaam die u in Azure Stream Analytics hebt gemaakt.

      ![Uitvoer instellen en de taak starten](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Bekende beperkingen
Tijdens het testen van uw query nemen de testresultaten ongeveer 6 seconden in beslag. We werken aan het verbeteren van de prestaties van testen. Wanneer Azure Stream Analytics echter in productie wordt geïmplementeerd, heeft het subsecondelatentie.

## <a name="streaming-units"></a>Streaming-eenheden
Uw Azure Stream Analytics-taak is standaard ingesteld op drie streaming-eenheden (SU's). Als u deze instelling wilt aanpassen, selecteert u **Schalen** in het linkermenu op de **taakpagina Stream Analytics** in de Azure-portal. Zie [Streamingeenheden begrijpen en aanpassen voor](../stream-analytics/stream-analytics-streaming-unit-consumption.md)meer informatie over streaming-eenheden.

![Streaming-eenheden schalen](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Volgende stappen
Zie [Querytaal streamen analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics) voor meer informatie over query's van Stream Analytics
