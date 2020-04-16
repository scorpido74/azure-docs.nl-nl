---
title: Gebruik de uitgebreide Spark-geschiedenisserver om apps te debuggen - Apache Spark in Azure Synapse
description: Gebruik de uitgebreide Spark-geschiedenisserver om Spark-toepassingen in Azure Synapse Analytics te opsporen en te diagnosticeren.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429211"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Gebruik uitgebreide Apache Spark-geschiedenisserver om Apache Spark-toepassingen te debuggen en te diagnosticeren

In dit artikel vindt u richtlijnen voor het gebruik van de uitgebreide Apache Spark-geschiedenisserver om voltooide en voltooide en uitvoerende Spark-toepassingen te debuggen en te diagnosticeren.

De extensie bevat een tabblad gegevens, het tabblad grafiek en het tabblad diagnose. Gebruik het tabblad **Gegevens** om de invoer- en uitvoergegevens van de taak Spark te controleren. Op het tabblad **Grafiek** ziet u de gegevensstroom en de herhaling van de taakgrafiek. Op **het** tabblad Diagnose ziet u **Gegevensscheefheid,** **Tijdscheefheid**en **Analyse van het gebruik van de executor**.

## <a name="access-the-apache-spark-history-server"></a>Toegang tot de Apache Spark-geschiedenisserver

Apache Spark-geschiedenisserver is de webgebruikersinterface voor voltooide en uitvoerende Spark-toepassingen. U de webinterface van de Apache Spark-geschiedenisserver openen vanuit de Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Open de webgebruikersinterface van sparkhistoryserver vanuit knooppunt Apache spark-toepassingen

1. Open [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Klik **op Monitor**en selecteer Vervolgens Apache **Spark-toepassingen**.

    ![Klik op monitor en selecteer spark-toepassing.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Selecteer een toepassing en open **vervolgens De query Logboek** en klik erop.

    ![Venster logboekquery openen.](./media/apache-spark-history-server/open-application-window.png)

4. Selecteer **Spark-geschiedenisserver**en de webgebruikersinterface van sparkgeschiedenisserver wordt weergegeven.

    ![Open spark history server.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>De webgebruikersinterface van sparkhistoryserver openen vanuit het gegevensknooppunt

1. Selecteer **spark-geschiedenisserver** in de cel voor taakuitvoering of in het statuspaneel onder aan het notitieblok van het notitieblok van het notitieblok. Selecteer **Sessiegegevens**.

   ![Spark-geschiedenisserver starten](./media/apache-spark-history-server/launch-history-server2.png "Spark-geschiedenisserver starten")

2. Selecteer **Spark-geschiedenisserver** in het deelvenster Uitschuifing.

   ![Spark-geschiedenisserver starten](./media/apache-spark-history-server/launch-history-server.png "Spark-geschiedenisserver starten")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Het tabblad Gegevens in de Spark-geschiedenisserver verkennen

Selecteer de taak-id voor de taak die u wilt weergeven. Selecteer vervolgens **Gegevens** in het gereedschapsmenu om de gegevensweergave te krijgen. In deze sectie ziet u hoe u verschillende taken uitvoert op het tabblad Gegevens.

* Controleer de **invoer,** uitvoer **en** **tabelbewerkingen** door de tabbladen afzonderlijk te selecteren.

    ![Gegevens voor tabbladen van Spark-toepassingen](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Alle rijen kopiëren door **Kopiëren te**selecteren.

    ![Gegevens voor de kopie van spark-toepassingen](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Sla alle gegevens op als CSV-bestand door **csv**te selecteren .

    ![Gegevens voor spark-toepassing opslaan](./media/apache-spark-history-server/apache-spark-data-save.png)

* Zoeken door trefwoorden in te voeren in veld **Zoeken**. De zoekresultaten worden onmiddellijk weergegeven.

    ![Gegevens voor zoeken naar spark-toepassingen](./media/apache-spark-history-server/apache-spark-data-search.png)

* Selecteer de kolomkop om de tabel te sorteren, selecteer het plusteken om een rij uit te vouwen om meer details weer te geven of selecteer het minteken om een rij samen te vouwen.

    ![Gegevens voor de spark-toepassingstabel](./media/apache-spark-history-server/apache-spark-data-table.png)

* Download één bestand door **Gedeeltelijke download te**selecteren. Het geselecteerde bestand wordt gedownload naar lokaal. Als het bestand niet meer bestaat, verschijnt er een nieuw tabblad met een foutbericht.

    ![Rij Gegevens voor de downloadrij van Spark-toepassingen](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Als u een volledig pad of relatief pad wilt kopiëren, selecteert u de opties **Volledig pad kopiëren** of Relatief pad **kopiëren** dat wordt uitgebreid in het vervolgkeuzemenu. Voor Azure Data Lake Storage-bestanden start **Openen in Azure Storage Explorer** Azure Storage Explorer en zoekt u de map wanneer u bent aangemeld.

    ![Gegevens voor het kopieerpad van Spark-toepassingen](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Selecteer paginanummers onder de tabel om door pagina's te navigeren wanneer er te veel rijen zijn om op één pagina weer te geven.

    ![Pagina Gegevens voor spark-toepassing](./media/apache-spark-history-server/apache-spark-data-page.png)

* Plaats de plaats op het vraagteken naast **Gegevens** om de knopinfo weer te geven of selecteer het vraagteken om meer informatie te krijgen.

    ![Gegevens voor Spark-toepassing meer info](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Stuur feedback met problemen door **Ons feedback te geven.**

    ![Spark grafiek geven ons feedback weer](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Tabblad Grafiek in de geschiedenisserver apache spark

Selecteer de taak-id voor de taak die u wilt weergeven. Selecteer vervolgens **Grafiek** in het gereedschapsmenu om de weergave van de taakgrafiek te krijgen.

### <a name="overview"></a>Overzicht

U een overzicht van uw taak zien in de gegenereerde taakgrafiek. Standaard toont de grafiek alle taken. U deze weergave filteren op **taak-id.**

![Functie-id spark-toepassing en taakgrafiek](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Weergave

Standaard is het **weergavescherm Voortgang** geselecteerd. U de gegevensstroom controleren door **Lezen** of **Schrijven te** selecteren in de vervolgkeuzelijst **Weergave.**

![Spark-toepassing en weergave van taakgrafieken](./media/apache-spark-history-server/sparkui-graph-display.png)

Het grafiekknooppunt geeft de kleuren weer die worden weergegeven in de heatmap-legenda.

![Spark-toepassings- en taakgrafiekheatmap](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Afspelen

Als u de taak wilt afspelen, selecteert u **Afspelen**. U op elk gewenst moment **Stoppen** selecteren om te stoppen. De taakkleuren geven verschillende statussen weer bij het afspelen:

|Kleur|Betekenis|
|-|-|
|Groen|Geslaagd: De taak is voltooid.|
|Orange|Opnieuw geprobeerd: taken die zijn mislukt, maar geen invloed hebben op het eindresultaat van de taak. Deze taken hadden dubbele of opnieuw proberen exemplaren die later kunnen slagen.|
|Blauw|Actief: de taak wordt uitgevoerd.|
|Wit|Wachten of overgeslagen: de taak wacht om uit te voeren of het podium is overgeslagen.|
|Rood|Mislukt: de taak is mislukt.|

In de volgende afbeelding ziet u de statuskleuren Groen, Oranje en Blauw.

![Voorbeeld van spark-toepassing en taakgrafiek, uitgevoerd](./media/apache-spark-history-server/sparkui-graph-color-running.png)

In de volgende afbeelding worden de statuskleuren Groen en Wit weergegeven.

![Voorbeeld van spark-toepassing en functiegrafiek, overslaan](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

In de volgende afbeelding ziet u rode en groene statuskleuren.

![Voorbeeld van spark-toepassing en functiegrafiek, mislukt](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Afspelen voor elke taak is toegestaan. Voor onvolledige taken wordt afspelen niet ondersteund.

### <a name="zoom"></a>Zoom

Gebruik de muisrol om in en uit te zoomen op de taakgrafiek of selecteer **Zoomen om** het op het scherm te laten passen.

![Spark-toepassing en vacaturegrafiekzoom om te passen](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Knopinfo

Plaats de plaats in het grafiekknooppunt om de knopinfo te zien wanneer er mislukte taken zijn en selecteer een fase om de werkpagina te openen.

![Knopinfo spark-toepassing en taakgrafiek](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Op het tabblad taakgrafiek hebben fasen een knopinfo en een klein pictogram weergegeven als ze taken hebben die aan de volgende voorwaarden voldoen:

|Voorwaarde|Beschrijving|
|-|-|
|Gegevensscheeftrekking|gegevensleesgrootte > gemiddelde leesgrootte van alle taken in deze fase * 2 en de grootte van het lezen van gegevens > 10 MB|
|Tijdscheefheid|uitvoeringstijd > gemiddelde uitvoeringstijd van alle taken in deze fase * 2 en uitvoeringstijd > 2 minuten|
   
![Pictogram Van toepassing en taakgrafiek scheeftrekken](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Beschrijving van het grafiekknooppunt

In het taakdiagramknooppunt worden de volgende gegevens van elke fase weergegeven:

  * Id.
  * Naam of beschrijving.
  * Totaal taaknummer.
  * Gegevens lezen: de som van de input grootte en shuffle leesgrootte.
  * Gegevens schrijven: de som van de uitvoer grootte en shuffle schrijft grootte.
  * Uitvoeringstijd: de tijd tussen begintijd van de eerste poging en voltooiingstijd van de laatste poging.
  * Aantal rij's: de som van invoerrecords, uitvoerrecords, leesrecords voor shuffle en shuffle-schrijfrecords.
  * Vooruitgang.

    > [!NOTE]  
    > Standaard geeft het taakdiagramknooppunt informatie weer van de laatste poging van elke fase (met uitzondering van de uitvoeringstijd van de fase). Tijdens het afspelen geeft het grafiekknooppunt echter informatie over elke poging weer.
    >  
    > De gegevensgrootte van lezen en schrijven is 1MB = 1000 KB = 1000 * 1000 Bytes.

### <a name="provide-feedback"></a>Feedback geven

Stuur feedback met problemen door **Ons feedback te geven.**

![Feedback over spark-toepassingen en taakgrafieken](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Het tabblad Diagnose in de geschiedenisserver van Apache Spark verkennen

Als u het tabblad Diagnose wilt openen, selecteert u een taak-id. Selecteer vervolgens **Diagnosis** in het gereedschapsmenu om de weergave taakdiagnose te krijgen. Het tabblad diagnose bevat **Gegevensscheefheid,** **Tijdscheefheid**en **Analyse van het gebruik van de executor**.

Controleer de **gegevensscheefheid,** **tijdscheefheid**en **analyse van het gebruik van de executor** door respectievelijk de tabbladen te selecteren.

![SparkUI diagnose gegevens scheeftrekken tabblad weer](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Gegevens scheeftrekken

Wanneer u het tabblad **Gegevensscheefheid** selecteert, worden de bijbehorende scheve taken weergegeven op basis van de opgegeven parameters.

* **Parameters opgeven** - In de eerste sectie worden de parameters weergegeven die worden gebruikt om gegevensscheefheid te detecteren. De standaardregel is: Taakgegevens lezen is groter dan drie keer van de gemiddelde gelezen taakgegevens en de gelezen taakgegevens meer dan 10 MB. Als u uw eigen regel voor scheve taken wilt definiëren, u uw parameters kiezen, worden de secties **Scheefwerkgebied** en **Verdraaiingsmarge** dienovereenkomstig vernieuwd.

* **Scheefwerk :** in het tweede deel worden fasen weergegeven die voldoen aan taken die voldoen aan de hierboven vermelde criteria. Als er meer dan één scheve taak in een fase is, wordt in de scheve fasetabel alleen de meest scheve taak weergegeven (bijvoorbeeld de grootste gegevens voor gegevensscheeftrekken).

    ![tabblad sparkui-diagnosegegevens scheeftrekken](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Grafiek scheeftrekken** : wanneer een rij in de fasetabel scheeftrekken is geselecteerd, worden in de grafiek met scheeftrekken meer taakverdelingsdetails weergegeven op basis van de lees- en uitvoeringstijd van gegevens. De scheve taken zijn rood gemarkeerd en de normale taken zijn blauw gemarkeerd. De grafiek geeft maximaal 100 voorbeeldtaken weer en de taakdetails worden weergegeven in het rechter onderste deelvenster.

    ![sparkui scheeftrekkinggrafiek voor fase 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tijd scheeftrekken

Op het tabblad **Tijdsverschil** worden scheve taken weergegeven op basis van de uitvoeringstijd van de taak.

* **Parameters opgeven** - In de eerste sectie worden de parameters weergegeven die worden gebruikt om Tijdscheefheid te detecteren. De standaardcriteria voor het detecteren van tijdsverschil zijn: de uitvoeringstijd van de taak is groter dan drie keer de gemiddelde uitvoeringstijd en de uitvoeringstijd van de taak is groter dan 30 seconden. U de parameters wijzigen op basis van uw behoeften. In **het overzichtswerkgebied scheef** en de grafiek **Schuintrekken** worden de bijbehorende fasen en takengegevens weergegeven, net als het tabblad **Gegevensscheefheid** hierboven.

* Selecteer **Tijdscheefheid**en het gefilterde resultaat wordt weergegeven in de sectie **Scheef werkgebied** volgens de parameters die zijn ingesteld in de sectie **Parameters opgeven**. Selecteer één item in de sectie **Scheefwerkgebied,** vervolgens wordt het bijbehorende diagram opgesteld in sectie3 en worden de taakdetails in het rechter onderste deelvenster weergegeven.

    ![sparkui diagnose tijd scheeftrekken sectie](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analyse van het gebruik van de executeur

De executorgebruiksgrafiek visualiseert de toewijzings- en uitvoeringsstatus van de Spark-taakuitvoerder.  

1. Selecteer **Executor Usage Analysis**, vervolgens vier typen curven over executor gebruik worden opgesteld, met inbegrip van Toegewezen **executors**, **Lopende Executors**, **Idle Executors,** en **Max Executor Instances**. Met betrekking tot toegewezen uitvoerders, elke "Executor toegevoegd" of "Executor verwijderd" gebeurtenis verhoogt of verlaagt de toegewezen uitvoerders. U 'Tijdlijn van gebeurtenissen' controleren op het tabblad 'Vacatures' voor meer vergelijking.

   ![tabblad sparkui-diagnoseuitvoerders](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Selecteer het kleurenpictogram om de bijbehorende inhoud in alle concepten te selecteren of de selectie uit te doen.

    ![sparkui diagnoses select chart](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Bekende problemen

Invoer-/uitvoergegevens met Behulp van Robuuste gedistribueerde gegevenssets (RdD's) worden niet weergegeven op het tabblad Gegevens.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET voor Apache Spark-documentatie](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

