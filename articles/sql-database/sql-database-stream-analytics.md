---
title: Gegevens streamen met Stream Analytics-integratie (voorbeeld)
description: Gebruik Azure Stream Analytics om gegevens te streamen naar een Azure SQL-database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820830"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Gegevens streamen met Azure SQL Database Stream Analytics-integratie (voorbeeld)

Gebruikers kunnen nu realtime streaminggegevens rechtstreeks vanuit een SQL-database in de Azure-portal opnemen, verwerken, bekijken en analyseren met [Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md) Deze ervaring maakt een breed scala aan scenario's mogelijk, zoals connected car, bewaking op afstand, fraudedetectie en nog veel meer. In de Azure-portal u een gebeurtenisbron (Gebeurtenishub/IoT-hub) selecteren, binnenkomende realtime gebeurtenissen weergeven en een tabel selecteren om gebeurtenissen op te slaan. U ook query's voor Stream Analytics-query's in de portal schrijven om binnenkomende gebeurtenissen te transformeren en op te slaan in de geselecteerde tabel. Dit nieuwe toegangspunt is een aanvulling op de creatie- en configuratie-ervaringen die al bestaan in Stream Analytics. Deze ervaring vertrekt vanuit de context van uw database, zodat u snel een Stream Analytics-taak instellen en naadloos navigeren tussen de Azure SQL Database- en Stream Analytics-ervaringen.

![Stream Analytics-stroom](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Belangrijkste voordelen

- Minimale contextswitching: u vanuit een SQL-database in de portal beginnen en realtime gegevens in een tabel opnemen zonder over te schakelen naar een andere service. 
- Minder stappen: de context van uw database en tabel wordt gebruikt om een Stream Analytics-taak vooraf te configureren.
- Extra gebruiksgemak met voorbeeldgegevens: voorbeeld van binnenkomende gegevens van de gebeurtenisbron (Gebeurtenishub/IoT-hub) in de context van geselecteerde tabel 


## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om de stappen in dit artikel uit te voeren:

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/)aan. 
- Een SQL-database. Zie Eén [database maken in Azure SQL Database](sql-database-single-database-get-started.md)voor meer informatie.
- Een firewallregel waarmee uw computer verbinding kan maken met de Azure SQL-server. Zie Een [firewallregel op serverniveau maken](sql-database-server-level-firewall-rule.md)voor meer informatie.


## <a name="configure-stream-analytics-integration"></a>Integratie van Stream-analyses configureren

1. Meld u aan bij Azure Portal. 
2. Navigeer naar uw SQL-database waar u uw streaminggegevens wilt opnemen. Selecteer **Stream-analyses (voorbeeld)**. 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Als u wilt beginnen met het opnemen van uw streaminggegevens in deze SQL-database, selecteert u **Maken** en een naam geven aan uw streamingtaak en selecteert u **Volgende: Invoer**. 

    ![Stream Analytics-taak maken](media/sql-database-stream-analytics/create-job.png)

4. Voer de brongegevens van uw gebeurtenissen in en selecteer **Volgende: Uitvoer**.

   - **Invoertype**: Gebeurtenishub/IoT-hub
   - **Invoeralias:** Voer een naam in om uw gebeurtenisbron te identificeren 
   - **Abonnement**: Hetzelfde als SQL Database-abonnement 
   - **Naamruimte gebeurtenishub:** naam voor naamruimte 
   - **Naam gebeurtenishub:** naam van gebeurtenishub binnen geselecteerde naamruimte 
   - **Naam van het beleid voor gebeurtenishub** (Standaard om nieuw te maken): Geef een beleidsnaam 
   - **Event Hub-consumentengroep** (Standaard om nieuw te maken): geef een naam van een consumentengroep  
     - We raden u aan een consumentengroep en een beleid te maken voor elke nieuwe Azure Stream Analytics-taak die u vanaf hier maakt. Consumentengroepen staan slechts vijf gelijktijdige lezers toe, dus het verstrekken van een speciale consumentengroep voor elke taak zal eventuele fouten voorkomen die kunnen voortvloeien uit het overschrijden van die limiet. Met een speciaal beleid u uw sleutel roteren of machtigingen intrekken zonder dat dit gevolgen heeft voor andere bronnen.

    ![Stream Analytics-taak maken](media/sql-database-stream-analytics/create-job-output.png)

5. Selecteer in welke tabel u uw streaminggegevens wilt opnemen. Zodra u klaar bent, selecteert **u Maken**.
   - **Gebruikersnaam**, **Wachtwoord:** Voer uw referenties in voor SQL-serververificatie. Selecteer **Valideren**.
   - **Tabel**: Selecteer **Nieuw maken** of **Bestaand gebruiken**. Laten we in deze stroom **Maken**selecteren. Hiermee wordt een nieuwe tabel gemaakt wanneer u de stream Analytics-taak start.

    ![Stream Analytics-taak maken](media/sql-database-stream-analytics/create.png)

6. Er wordt een querypagina geopend met de volgende details:

   - Uw **invoer** (bron van invoergebeurtenissen) waaruit u gegevens inneemt  
   - Uw **uitvoer** (uitvoertabel) waarmee getransformeerde gegevens worden opgeslagen 
   - Voorbeeld [van SAQL-query](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) met SELECT-instructie. 
   - **Voorvertoning invoer:** toont momentopname van de nieuwste binnenkomende gegevens van de bron van invoergebeurtenissen.  
     - Het serialisatietype in uw gegevens wordt automatisch gedetecteerd (JSON/CSV). U het ook handmatig wijzigen in JSON/CSV/AVRO. 
     - U een voorbeeld bekijken van binnenkomende gegevens in de tabelindeling of de raw-indeling. 
     - Als de weergegeven gegevens niet actueel zijn, selecteert u **Vernieuwen** om de nieuwste gebeurtenissen weer te geven. 
     - Selecteer **Tijdbereik selecteren** om uw query te testen op een specifiek tijdsbereik van binnenkomende gebeurtenissen. 
     - Selecteer **Voorbeeldinvoer uploaden** om uw query te testen door een voorbeeld-JSON/CSV-bestand te uploaden. Zie [Een Azure Stream Analytics-taak testen met voorbeeldgegevens](../stream-analytics/stream-analytics-test-query.md)voor meer informatie over het testen van een SAQL-query. 

    ![testquery](media/sql-database-stream-analytics/test-query.png)


   - **Testresultaten**: **Testquery** selecteren en u de resultaten van uw streamingquery bekijken 

    ![testresultaten](media/sql-database-stream-analytics/test-results.png)

   - **Testresultatenschema**: toont het schema van de resultaten van uw streamingquery na het testen. Zorg ervoor dat het testresultatenschema overeenkomt met uw uitvoerschema. 

    ![testresultatenschema](media/sql-database-stream-analytics/test-results-schema.png)


   - **Uitvoerschema:** Dit bevat schema van de tabel die u in stap 5 hebt geselecteerd (nieuw of bestaand).
     - Nieuw maken: als u deze optie in stap 5 hebt geselecteerd, ziet u het schema nog niet totdat u de streamingtaak start. Selecteer bij het maken van een nieuwe tabel de juiste tabelindex. Zie [Geclusterde en niet-geclusterde indexen beschreven](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)voor meer informatie over tabelindexering.
    - Bestaande gebruiken: als u deze optie in stap 5 hebt geselecteerd, ziet u het schema van de geselecteerde tabel. 
 
7. Nadat u klaar bent met het ontwerpen & het testen van de query, selecteert u **Query opslaan**. Selecteer **De taak Start Stream Analytics** om getransformeerde gegevens in de SQL-tabel in te nemen. Zodra u de volgende velden hebt afgerond, **start u** de taak. 
   - **Begintijd van uitvoer**: Hiermee wordt de tijd van de eerste uitvoer van de taak gedefinieerd.  
     - Nu: De taak start nu en verwerkt nieuwe binnenkomende gegevens.
     - Aangepast: De taak begint nu, maar verwerkt gegevens vanaf een specifiek tijdstip (dat kan in het verleden of in de toekomst zijn). Zie [Een Azure Stream Analytics-taak starten](../stream-analytics/start-job.md)voor meer informatie.
   - **Streaming-eenheden**: Azure Stream Analytics wordt geprijsd op basis van het aantal streaming-eenheden dat nodig is om de gegevens in de service te verwerken. Zie [Azure Stream Analytics-prijzen voor](https://azure.microsoft.com/pricing/details/stream-analytics/)meer informatie. 
   - **Verwerking van uitvoergegevensfout:**  
     - Opnieuw proberen: wanneer er een fout optreedt, probeert Azure Stream Analytics de gebeurtenis voor onbepaalde tijd te schrijven totdat de schrijftijd is geslaagd. Er is geen time-out voor nieuwe pogingen. Uiteindelijk worden alle volgende gebeurtenissen geblokkeerd voor verwerking door de gebeurtenis die opnieuw wordt geprobeerd. Deze optie is het beleid voor het afhandelen van de standaarduitvoerfout. 
     - Drop: Azure Stream Analytics laat elke uitvoergebeurtenis vallen die resulteert in een fout in gegevensconversie. De verwijderde gebeurtenissen kunnen niet worden hersteld voor latere verwerking. Alle tijdelijke fouten (bijvoorbeeld netwerkfouten) worden opnieuw geprobeerd, ongeacht de beleidsconfiguratie voor het afhandelen van uitvoerfouten. 
   - **SQL Database-uitvoerinstellingen:** een optie voor het overnemen van het partitieschema van uw vorige querystap, om volledig parallelle topologie met meerdere schrijvers in de tabel mogelijk te maken. Zie [Azure Stream Analytics-uitvoer naar Azure SQL Database voor](../stream-analytics/stream-analytics-sql-output-perf.md)meer informatie.
   - **Maximum aantal batch:** De aanbevolen bovengrens voor het aantal records dat bij elke bulkwisseltransactie wordt verzonden.  
    Zie [Uitvoerfoutbeleid in Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md)voor meer informatie over het verwerken van uitvoerfouten.  

    ![startjob](media/sql-database-stream-analytics/start-job.png)

8. Zodra u de taak start, ziet u de taak Uitvoeren in de lijst en u de volgende acties uitvoeren: 
   - **Start/stop de taak**: Als de taak wordt uitgevoerd, u de taak stoppen. Als de taak wordt gestopt, u de taak starten. 
   - **Taak bewerken:** u de query bewerken. Als u meer wijzigingen wilt aanbrengen in de taakex, voegt u meer ingangen/uitvoer toe en opent u de taak in Stream Analytics. De optie Bewerken is uitgeschakeld wanneer de taak wordt uitgevoerd. 
   - **Voorbeeld van uitvoertabel**: U een voorbeeld van de tabel bekijken in sql-queryeditor. 
   - **Open in Stream Analytics:** Open de taak in de Stream Analytics-service om de controle weer te geven, details van de taak te debuggen. 


    ![vacatures voor streamanalyses](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)
- [Azure Stream Analytics-oplossingspatronen](../stream-analytics/stream-analytics-solution-patterns.md)
