---
title: Integratie van Power BI-dashboards met Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u een realtime Power BI-dashboard gebruiken om gegevens uit een Azure Stream Analytics-taak te visualiseren.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2019
ms.openlocfilehash: 8466fbcb4325dc244551a3b84fc20581366b7071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851150"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics en Power BI: een realtime analysedashboard voor het streamen van gegevens

Met Azure Stream Analytics u profiteren van een van de toonaangevende business intelligence-tools, [Microsoft Power BI.](https://powerbi.com/) In dit artikel leert u hoe u hulpprogramma's voor bedrijfsinformatie maakt door Power BI te gebruiken als uitvoer voor uw Azure Stream Analytics-taken. U leert ook hoe u een real-time dashboard maken en gebruiken.

Dit artikel gaat verder vanuit de [realtime handleiding voor fraudedetectie](stream-analytics-real-time-fraud-detection.md) van Stream Analytics. Het bouwt voort op de workflow die in die zelfstudie is gemaakt en voegt een Power BI-uitvoer toe, zodat u frauduleuze telefoongesprekken visualiseren die worden gedetecteerd door een streaming analytics-taak. 

U [een video](https://www.youtube.com/watch?v=SGUpT-a99MA) bekijken die dit scenario illustreert.


## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u het volgende hebt voordat u begint:

* Een Azure-account.
* Een account voor Power BI Pro. U een werkaccount of een schoolaccount gebruiken.
* Een voltooide versie van de [real-time fraude detectie](stream-analytics-real-time-fraud-detection.md) tutorial. De zelfstudie bevat een app die fictieve metagegevens van het telefoongesprek genereert. In de zelfstudie maakt u een gebeurtenishub en verzendt u de gegevens over het streamen van gesprekken naar de gebeurtenishub. U schrijft een query die frauduleuze oproepen detecteert (oproepen van hetzelfde nummer op hetzelfde moment op verschillende locaties). 


## <a name="add-power-bi-output"></a>Power BI-uitvoer toevoegen
In de realtime handleiding voor fraudedetectie wordt de uitvoer verzonden naar Azure Blob-opslag. In deze sectie voegt u een uitvoer toe die informatie naar Power BI verzendt.

1. Open in de Azure-portal de streaminganalytics-taak die u eerder hebt gemaakt. Als u de voorgestelde naam `sa_frauddetection_job_demo`hebt gebruikt, wordt de taak benoemd .

2. Selecteer in het linkermenu **Uitvoer** onder **Taaktopologie**. Selecteer **+ Toevoegen** en kies Vervolgens **Power BI** in het vervolgkeuzemenu.

3. Selecteer **+ Power** > **BI**toevoegen . Vul vervolgens het formulier in met de volgende gegevens en selecteer **Autoriseren**:

   |**Instelling**  |**Voorgestelde waarde**  |
   |---------|---------|
   |Uitvoeralias  |  CallStream-PowerBI  |
   |Naam van de gegevensset  |   sa-dataset  |
   |Tabelnaam |  frauduleuze oproepen  |

   ![Azure Stream Analytics-uitvoer configureren](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Als Power BI een gegevensset en tabel heeft die dezelfde namen hebben als die welke u opgeeft in de taak Stream Analytics, worden de bestaande gegevens overschreven.
   > We raden u aan deze gegevensset en tabel niet expliciet te maken in uw Power BI-account. Ze worden automatisch gemaakt wanneer u uw Stream Analytics-taak start en de taak begint met het pompen van uitvoer naar Power BI. Als uw taakquery geen resultaten oplevert, worden de gegevensset en tabel niet gemaakt.
   >

4. Als u **Autoriseren** hebt geselecteerd, wordt er een pop-upvenster geopend en wordt u gevraagd referenties te verstrekken als verificatie voor uw Power BI-account. Zodra de autorisatie geslaagd is, kunt u de instellingen **Opslaan**.

8. Klik **op Maken**.

De gegevensset wordt gemaakt met de volgende instellingen:

* **defaultRetentionPolicy: BasicFIFO** - Data is FIFO, met een maximum van 200.000 rijen.
* **defaultMode: pushStreaming** - De dataset ondersteunt zowel streaming tegels als traditionele report-based visuals (ook wel push genoemd).

Momenteel u geen gegevenssets maken met andere vlaggen.

Zie de [Power BI REST API-referentie](https://msdn.microsoft.com/library/mt203562.aspx) voor meer informatie over Power BI-gegevenssets.


## <a name="write-the-query"></a>De query schrijven

1. Sluit het **uitvoerblad** en keer terug naar het taakblad.

2. Klik op het vak **Query.** 

3. Voer de volgende query in. Deze query is vergelijkbaar met de zelf-join query die u hebt gemaakt in de fraudedetectie tutorial. Het verschil is dat deze query resultaten verzendt`CallStream-PowerBI`naar de nieuwe uitvoer die u hebt gemaakt ( ). 

    >[!NOTE]
    >Als u de invoer `CallStream` in de zelfstudie voor fraudedetectie niet hebt genoemd, vervangt u uw naam in `CallStream` de **FROM-** en **JOIN-clausules** in de query.

   ```SQL
   /* Our criteria for fraud:
   Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "CallStream-PowerBI"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

   /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
   ON CS1.CallingIMSI = CS2.CallingIMSI

   /* ...and date between CS1 and CS2 is between one and five seconds */
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

   /* Where the switch location is different */
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Klik op **Opslaan**.


## <a name="test-the-query"></a>De query testen

Deze sectie is optioneel, maar wordt aanbevolen. 

1. Als de TelcoStreaming-app momenteel niet wordt uitgevoerd, start u deze door de volgende stappen te volgen:

    * Opdrachtprompt openen.
    * Ga naar de map waar de telcogenerator.exe en gemodificeerde telcodatagen.exe.config bestanden zijn.
    * Voer de volgende opdracht uit:

       `telcodatagen.exe 1000 .2 2`

2. Klik **op** de pagina Query voor de taak `CallStream` Stream Analytics op de puntjes naast de invoer en selecteer **Voorbeeldgegevens uit invoer.**

3. Geef op dat u drie minuten aan gegevens wilt en klik op **OK**. Wacht tot u een melding krijgt dat de gegevens zijn verzameld.

4. Klik **op Testen** en bekijk de resultaten.

## <a name="run-the-job"></a>De taak uitvoeren

1. Controleer of de TelcoStreaming-app actief is.

2. Navigeer naar de **overzichtspagina** voor uw streamanalytics-taak en selecteer **Start**.

    ![De functie Stream Analytics starten](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Je Streaming Analytics-taak gaat op zoek naar frauduleuze oproepen in de binnenkomende stream. De taak maakt ook de gegevensset en tabel in Power BI en begint met het verzenden van gegevens over de frauduleuze oproepen naar hen.


## <a name="create-the-dashboard-in-power-bi"></a>Het dashboard maken in Power BI

1. Ga naar [Powerbi.com](https://powerbi.com) en meld je aan met je werk- of schoolaccount. Als de uitvoerresultaten van de taakquery Stream Analytics worden weergegeven, ziet u dat uw gegevensset al is gemaakt:

    ![Locatie van streaminggegevensset in Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. Klik in uw ** + &nbsp;** werkruimte op Maken .

    ![De knop Maken in de Power BI-werkruimte](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Maak een nieuw dashboard `Fraudulent Calls`en geef het een naam.

    ![Een dashboard maken en een naam geven in De Werkruimte van Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Klik boven aan het venster op **Tegel toevoegen,** selecteer **AANGEPASTE STREAMING-GEGEVENS**en klik op **Volgende**.

    ![Aangepaste streaminggegevenssettegel in Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Selecteer **onder UW DATSETS**uw gegevensset en klik op **Volgende**.

    ![Uw streaminggegevensset in Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Selecteer **onder Visualisatietype**de optie **Kaart**en selecteer vervolgens in de lijst **Velden** **frauduleuze oproepen**.

    ![Visualisatiedetails voor nieuwe tegel](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Klik op **Volgende**.

8. Vul tegeldetails in, zoals een titel en ondertitel.

    ![Titel en ondertitel voor nieuwe tegel](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Klik op **Toepassen**.

    Nu heb je een fraudeteller!

    ![Fraudeteller in Power BI-dashboard](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Volg de stappen opnieuw om een tegel toe te voegen (te beginnen met stap 4). Ga deze keer als volgt te werk:

    * Wanneer u **visualisatietype gebruikt,** selecteert u **Lijndiagram**. 
    * Voeg een as toe en selecteer **windowend**. 
    * Voeg een waarde toe en selecteer **fraudulentcalls**.
    * Selecteer bij **Tijdvenster voor weergave** de laatste 10 minuten.

      ![Tegel maken voor lijndiagram in Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Klik **op Volgende,** voeg een titel en ondertitel toe en klik op **Toepassen**.

     Het Power BI-dashboard geeft u nu twee weergaven van gegevens over frauduleuze oproepen zoals gedetecteerd in de streaminggegevens.

     ![Voltooid Power BI-dashboard met twee tegels voor frauduleuze oproepen](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Meer informatie over Power BI

In deze zelfstudie wordt uitgelegd hoe u slechts een paar soorten visualisaties voor een gegevensset maken. Power BI kan u helpen andere business intelligence-tools voor uw organisatie te maken. Zie de volgende bronnen voor meer ideeën:

* Bekijk de Power [BI-video](https://youtu.be/L-Z_6P56aas?t=1m58s) voor een ander voorbeeld van een Power BI-dashboard.
* Voor meer informatie over het configureren van de taakuitvoer van Streaming Analytics naar Power BI en het gebruik van Power BI-groepen, bekijk het [gedeelte Power BI](stream-analytics-define-outputs.md#power-bi) van het artikel Stream [Analytics-uitvoeruitvoer.](stream-analytics-define-outputs.md) 
* Zie [Dashboards in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)voor informatie over het gebruik van Power BI in het algemeen.


## <a name="learn-about-limitations-and-best-practices"></a>Meer informatie over beperkingen en aanbevolen procedures
Momenteel kan Power BI ongeveer één keer per seconde worden opgeroepen. Streaming visuals ondersteunen pakketten van 15 KB. Buiten dat, streaming visuals mislukken (maar push blijft werken). Vanwege deze beperkingen leent Power BI zich natuurlijk voor gevallen waarin Azure Stream Analytics een aanzienlijke vermindering van de gegevensbelasting doet. We raden u aan een tumbling-venster of hopping-venster te gebruiken om ervoor te zorgen dat gegevenspush maximaal één druk per seconde is en dat uw query binnen de doorvoervereisten valt.

U de volgende vergelijking gebruiken om de waarde te berekenen om uw venster binnen enkele seconden te geven:

![Vergelijking om de waarde te berekenen om venster in seconden te geven](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Bijvoorbeeld:

* U hebt 1.000 apparaten die gegevens verzenden met intervallen van één seconde.
* U gebruikt de Power BI Pro SKU die 1.000.000 rijen per uur ondersteunt.
* U wilt de hoeveelheid gemiddelde gegevens per apparaat publiceren naar Power BI.

Als gevolg hiervan wordt de vergelijking:

![Vergelijking op basis van voorbeeldcriteria](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Gezien deze configuratie u de oorspronkelijke query als volgt wijzigen:

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>Autorisatie verlengen
Als het wachtwoord is gewijzigd sinds uw taak is gemaakt of voor het laatst is geverifieerd, moet u uw Power BI-account opnieuw verifiëren. Als Azure Multi-Factor Authentication is geconfigureerd op uw Azure Active Directory (Azure AD)-tenant, moet u ook de Power BI-autorisatie elke twee weken verlengen. Als u niet verlengt, u symptomen zien, zoals een `Authenticate user error` gebrek aan taakuitvoer of een in de bewerkingslogboeken.

Als een taak wordt gestart nadat het token is verlopen, treedt er een fout op en mislukt de taak. Als u dit probleem wilt oplossen, stopt u de taak die wordt uitgevoerd en gaat u naar uw Power BI-uitvoer. Als u gegevensverlies wilt voorkomen, selecteert u de koppeling **Autorisatie verlengen** en start u de taak opnieuw op vanaf de **laatste gestopte tijd**.

Nadat de autorisatie is vernieuwd met Power BI, wordt er een groene waarschuwing weergegeven in het autorisatiegebied om aan te geven dat het probleem is opgelost.

## <a name="get-help"></a>Help opvragen
Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaginformatie over de querytaal van Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Verwijzing naar API-api voor Azure Stream Analytics-beheer](https://msdn.microsoft.com/library/azure/dn835031.aspx)
