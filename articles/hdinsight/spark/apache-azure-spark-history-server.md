---
title: Gebruik de uitgebreide functies in de Apache Spark History Server om apps te debuggen - Azure HDInsight
description: Gebruik de uitgebreide functies in de Apache Spark History Server om Spark-toepassingen te debuggen en te diagnosticeren - Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548931"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Gebruik de uitgebreide functies van de Apache Spark History Server om Spark-toepassingen te debuggen en te diagnosticeren

In dit artikel ziet u hoe u de uitgebreide functies van de Apache Spark History Server gebruiken om voltooide of voltooide of voltooide Spark-toepassingen te debuggen en te diagnosticeren. De extensie bevat een tabblad **Gegevens,** een tabblad **Grafiek** en een tabblad **Diagnose.** Op het tabblad **Gegevens** u de invoer- en uitvoergegevens van de spark-taak controleren. Op het tabblad **Grafiek** u de gegevensstroom controleren en de taakgrafiek opnieuw afspelen. Op het tabblad **Diagnose** u verwijzen naar de functies **Gegevensscheefheid,** **Tijdscheeftrekken**en **Analyse van de uitvoeranalyse.**

## <a name="get-access-to-the-spark-history-server"></a>Toegang krijgen tot de Spark History Server

De Spark History Server is de web-gebruikersinterface voor voltooide en uitvoerende Spark-toepassingen. U het openen vanuit de Azure-portal of vanuit een URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>De webgebruikersinterface van sparkhistoryserver openen vanuit de Azure-portal

1. Open het Spark-cluster vanuit de [Azure-portal.](https://portal.azure.com/) Zie [Clusters weergeven en weergeven](../hdinsight-administer-use-portal-linux.md#showClusters)voor meer informatie.
2. Selecteer **spark-geschiedenisserver**in **clusterdashboards**. Voer de beheerdersreferenties voor het Spark-cluster in wanneer u daarom wordt gevraagd.

    ![Start de Spark History Server vanuit de Azure-portal.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark-geschiedenisserver")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>De webgebruikersinterface van de Spark History Server openen op URL

Open de Spark History `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`Server door te bladeren naar , waar **CLUSTERNAME** de naam van uw Spark-cluster is.

De webgebruikersinterface van Spark History Server lijkt mogelijk op deze afbeelding:

![De pagina Spark History Server.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Het tabblad Gegevens gebruiken op de Spark-geschiedenisserver

Selecteer de taak-id en selecteer **Gegevens** in het gereedschapsmenu om de gegevensweergave weer te geven.

+ Controleer **invoer,** uitvoer en **tabelbewerkingen** door de afzonderlijke tabbladen te selecteren. **Outputs**

    ![Gegevenstabbladen op de pagina Gegevens voor vonktoepassing.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Kopieer alle rijen door de knop **Kopiëren te** selecteren.

    ![Kopieer gegevens op de pagina Spark-toepassing.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Sla alle gegevens op als een . CSV-bestand door de **csv-knop te** selecteren.

    ![Gegevens opslaan als een . CSV-bestand vanaf de pagina Gegevens voor vonktoepassing.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Zoek in de gegevens door trefwoorden in te voeren in het veld **Zoeken.** De zoekresultaten worden onmiddellijk weergegeven.

    ![Zoek gegevens op de pagina Gegevens voor vonktoepassing.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Selecteer de kolomkop om de tabel te sorteren. Selecteer het plusteken om een rij uit te vouwen om meer details weer te geven. Selecteer het minteken om een rij samen te vouwen.

    ![De gegevenstabel op de pagina Gegevens voor vonktoepassing.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Download één bestand door de knop **Gedeeltelijke download** aan de rechterkant te selecteren. Het geselecteerde bestand wordt lokaal gedownload. Als het bestand niet meer bestaat, wordt er een nieuw tabblad geopend om de foutberichten weer te geven.

    ![De rij gegevens downloaden op de pagina Gegevens voor vonktoepassing.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopieer een volledig pad of een relatief pad door de optie **Volledig pad kopiëren** of Relatief pad **kopiëren** te selecteren, die wordt uitgevouwen in het downloadmenu. Selecteer Voor Azure Data Lake Storage-bestanden **openen in Azure Storage Explorer** om Azure Storage Explorer te starten en de map te zoeken na aanmelding.

    ![Opties voor volledig pad kopiëren en relatieve paden kopiëren op de pagina Gegevens voor vonktoepassing.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Als er te veel rijen op één pagina worden weergegeven, selecteert u de paginanummers onder aan de tabel om te navigeren.

    ![Paginanummers op de pagina Gegevens voor vonktoepassing.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Plaats de plaats voor meer informatie over of selecteer het vraagteken naast **Gegevens voor Spark-toepassing** om de knopinfo weer te geven.

    ![Meer informatie vindt u op de pagina Gegevens voor vonktoepassing.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Als u feedback wilt sturen over problemen, selecteert **u Ons feedback geven**.

    ![Geef feedback van de pagina Gegevens voor vonktoepassingen.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Het tabblad Grafiek gebruiken op de Spark-geschiedenisserver

+ Selecteer de taak-id en selecteer **Grafiek** in het gereedschapsmenu om de taakgrafiek weer te geven. Standaard wordt in de grafiek alle taken weergegeven. Filter de resultaten met het vervolgkeuzemenu **Taak-ID.**

    ![Het vervolgkeuzemenu Job-ID op de pagina & JobGraph.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **Voortgang** is standaard geselecteerd. Controleer de gegevensstroom door **Lezen** of **Schrijven te** selecteren in het vervolgkeuzemenu **Weergave.**

    ![Controleer de gegevensstroom op de pagina & Vacaturegrafiek.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ De achtergrondkleur van elke taak komt overeen met een heatmap.

   ![De heatmap op de pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Kleur |Beschrijving |
    |---|---|
    |Groen|De taak is voltooid.|
    |Orange|De taak is mislukt, maar dit heeft geen invloed op het uiteindelijke resultaat van de taak. Deze taken hebben dubbele of opnieuw proberen exemplaren die later kunnen slagen.|
    |Blauw|De taak wordt uitgevoerd.|
    |Wit|De taak wacht om uit te voeren of het podium is overgeslagen.|
    |Rood|De taak is mislukt.|

     ![Een taak uitvoeren op de pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     De overgeslagen fasen worden in het wit weergegeven.
    ![Een overgeslagen taak op de pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Een mislukte taak op de pagina toepassing & Taakgrafiek.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > Afspelen is beschikbaar voor voltooide taken. Selecteer de knop **Afspelen** om de taak af te spelen. Stop de taak op elk gewenst moment door de stopknop te selecteren. Wanneer een taak wordt afgespeeld, geeft elke taak de status weer op kleur. Afspelen wordt niet ondersteund voor onvolledige taken.

+ Schuif om in of uit te zoomen op de taakgrafiek of selecteer **Zoomen om** het op het scherm te laten passen.

    ![Selecteer Zoomen om te passen op de pagina toepassing & Functiegrafiek.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Wanneer taken mislukken, houdt u de plaats boven het grafiekknooppunt om de knopinfo te zien en selecteert u de fase om deze op een nieuwe pagina te openen.

    ![Bekijk de knopinfo op de pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Op de pagina & Vacaturegrafiek worden in de fasen knopinfo en kleine pictogrammen weergegeven als de taken aan deze voorwaarden voldoen:
  + Gegevensscheefheid: gegevensleesgrootte > gemiddelde leesgrootte van alle taken in deze fase * 2 *en* de grootte van het gegevenslezen > 10 MB.
  + Tijdsverschil: uitvoeringstijd > gemiddelde uitvoeringstijd van alle taken in deze fase * 2 *en* uitvoeringstijd > 2 minuten.

    ![Het pictogram van de scheve taak op de pagina & Functiegrafiek.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ In het knooppunt van de taakgrafiek worden de volgende informatie over elke fase weergegeven:
  + Id
  + Naam of beschrijving
  + Totaal taaknummer
  + Gelezen gegevens: de som van de invoergrootte en de leesgrootte van shuffle
  + Gegevens schrijven: de som van de uitvoer grootte en shuffle schrijfgrootte
  + Uitvoeringstijd: de tijd tussen begintijd van de eerste poging en voltooiingstijd van de laatste poging
  + Aantal rij's: de som van invoerrecords, uitvoerrecords, leesrecords shuffle en shuffle-schrijfrecords
  + Voortgang

    > [!NOTE]  
    > Standaard geeft het taakgrafiekknooppunt informatie weer van de laatste poging van elke fase (met uitzondering van de uitvoeringstijd van de fase). Maar tijdens het afspelen wordt in het knooppunt van de taakgrafiek informatie weergegeven over elke poging.

    > [!NOTE]  
    > Voor gegevenslezen en gegevensschrijfformaten gebruiken we 1MB = 1000 KB = 1000 * 1000 bytes.

+ Stuur feedback over problemen door **Ons feedback te geven.**

    ![De feedbackoptie op de pagina & Vacaturegrafiek.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Het tabblad Diagnose gebruiken op de Spark History Server

Selecteer de taak-id en selecteer **Vervolgens Diagnose** in het gereedschapsmenu om de taakdiagnoseweergave weer te geven. Het tabblad **Diagnose** bevat **Gegevensscheefheid,** **Tijdscheefheid**en **Analyse van het gebruik van de uitvoerder**.

+ Gegevens **scheeftrekken,** **Tijdscheefheid**en **analyse van het gebruik van de executor** controleren door respectievelijk de tabbladen te selecteren.

    ![Het tabblad Gegevensscheefheid op het tabblad Diagnose.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Gegevens scheeftrekken

Selecteer het tabblad **Gegevensscheefheid.** De bijbehorende scheve taken worden weergegeven op basis van de opgegeven parameters.

#### <a name="specify-parameters"></a>Parameters opgeven

In de sectie **Parameters opgeven** worden de parameters weergegeven die worden gebruikt om gegevensscheefheid te detecteren. De standaardregel is: de gelezen taakgegevens zijn groter dan drie keer de gemiddelde gelezen taakgegevens en de gelezen taakgegevens meer dan 10 MB. Als u uw eigen regel voor scheve taken wilt definiëren, u uw parameters kiezen. De secties **Scheefwerkwerk en** **Grafiek schuin trekken** worden dienovereenkomstig bijgewerkt.

#### <a name="skewed-stage"></a>Scheef staand stadium

In de sectie **Scheef werkgebied** worden fasen weergegeven met scheve taken die voldoen aan de opgegeven criteria. Als er meer dan één scheve taak in een fase is, wordt in de sectie **Scheefwerkwerkwerk** alleen de meest scheve taak weergegeven (dat wil zeggen de grootste gegevens voor gegevensscheeftrekken).

![Grotere weergave van het tabblad Gegevensscheeftrekken op het tabblad Diagnose.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Grafiek scheeftrekken

Wanneer u een rij selecteert in de tabel **Werkgebied scheeftrekken,** worden **in het grafiek overzicht** meer taakverdelingsdetails weergegeven op basis van de lees- en uitvoeringstijd van gegevens. De scheve taken zijn rood gemarkeerd en de normale taken zijn blauw gemarkeerd. Voor de overweging van de prestaties wordt in de grafiek maximaal 100 voorbeeldtaken weergegeven. De taakdetails worden weergegeven in het deelvenster rechtsonder.

![De grafiek Schuintrekken voor fase 10 in de Spark-gebruikersinterface.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tijd scheeftrekken

Op het tabblad **Tijdsverschil** worden scheve taken weergegeven op basis van de uitvoeringstijd van de taak.

#### <a name="specify-parameters"></a>Parameters opgeven

In de sectie **Parameters opgeven** worden de parameters weergegeven die worden gebruikt om tijdsscheeftrekking te detecteren. De standaardregel is: de uitvoeringstijd van taken is groter dan drie keer de gemiddelde uitvoeringstijd en de uitvoeringstijd van de taak is groter dan 30 seconden. U de parameters wijzigen op basis van uw behoeften. In **het overzichtswerkgebied en** de grafiek **Scheeftrekken** worden de bijbehorende fasen en takengegevens weergegeven, net als op het tabblad **Gegevensscheeftrekken.**

Wanneer u **Tijdscheefheid**selecteert, wordt het gefilterde resultaat weergegeven in de sectie **Scheef werkgebied,** afhankelijk van de parameters die zijn ingesteld in de sectie **Parameters opgeven.** Wanneer u één item selecteert in de sectie **Scheefwerkgebied,** wordt het bijbehorende diagram opgesteld in het derde gedeelte en worden de taakdetails weergegeven in het deelvenster rechtsonder.

![Het tabblad Tijdscheeftrekken op het tabblad Diagnose.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Grafieken voor analyse van gebruiksanalyse van de executor

In **de grafiek Executorgebruik** wordt de werkelijke executortoewijzing en -status van de taak weergegeven.  

Wanneer u **ExecutorUsage Analysis**selecteert, worden vier verschillende curven over executorgebruik opgesteld: **Toegewezen uitvoerders,** **Uitvoerders uitvoeren,** **inactieve executors**en **Max Executor-instanties**. Elke **executeur die is toegevoegd** of **executor verwijderd** gebeurtenis zal verhogen of verlagen van de toegewezen uitvoerders. U **Gebeurtenistijdlijn** controleren op het tabblad **Vacatures** voor meer vergelijkingen.

![Het tabblad Analyse van de uitvoeranalyse op het tabblad Diagnose.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Selecteer het kleurenpictogram om de bijbehorende inhoud in alle concepten te selecteren of de selectie uit te doen.

 ![Selecteer de grafiek op het tabblad Analyse van de uitvoeranalyse.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Veelgestelde vragen

### <a name="how-do-i-revert-to-the-community-version"></a>Hoe keer ik terug naar de communityversie?

Als u wilt terugkeren naar de communityversie, moet u de volgende stappen uitvoeren.

1. Open het cluster in Ambari.
1. Navigeer naar **Spark2** > **Configs**.
1. Selecteer **Aangepaste spark2-standaardinstellingen**.
1. Selecteer **Eigenschap toevoegen ...**.
1. Voeg **spark.ui.enhancement.enabled=false toe**en sla deze op.
1. Het pand wordt nu **op onwaar.**
1. Selecteer **Opslaan** om de configuratie op te slaan.

    ![Schakel een functie uit in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Selecteer **Spark2** in het linkerdeelvenster. Selecteer vervolgens op het tabblad **Overzicht** de optie **Spark2 History Server**.

    ![De overzichtsweergave in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Als u de Spark History Server opnieuw wilt starten, selecteert u de knop **Gestart** rechts van **Spark2 History Server**en selecteert u Opnieuw **starten** in het vervolgkeuzemenu.

    ![Start de Spark History Server opnieuw in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Vernieuw de webgebruikersinterface van sparkhistoryserver. Het zal terugkeren naar de community-versie.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Hoe upload ik een Spark History Server-gebeurtenis om dit als probleem te melden?

Als u een fout in spark history server tegenkomt, voert u de volgende stappen uit om de gebeurtenis te melden.

1. Download de gebeurtenis door **Downloaden** te selecteren in de webgebruikersinterface van sparkhistoryserver.

    ![Download de gebeurtenis in de gebruikersinterface van de Spark History Server.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Selecteer **Geef ons feedback** op de pagina **Spark-sollicitatie& Functiegrafiek.**

    ![Feedback geven op de pagina & Vacaturegrafiek](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Geef de titel en een beschrijving van de fout op. Sleep vervolgens het zip-bestand naar het bewerkingsveld en selecteer **Nieuw probleem verzenden**.

    ![Upload en verzend een nieuw probleem.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Hoe kan ik een .jar-bestand upgraden in een hotfix-scenario?

Als u wilt upgraden met een hotfix, gebruikt `spark-enhancement.jar*`u het volgende script, dat wordt geüupgradet.

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Gebruik

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Voorbeeld

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Het bash-bestand gebruiken vanuit de Azure-portal

1. Start de [Azure-portal](https://ms.portal.azure.com)en selecteer vervolgens uw cluster.
2. Voltooi een [scriptactie](../hdinsight-hadoop-customize-cluster-linux.md) met de volgende parameters.

    |Eigenschap |Waarde |
    |---|---|
    |Scripttype|- Aangepast|
    |Name|UpgradeJar|
    |Bash script URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Knooppunttype(s)|Hoofd, Arbeider|
    |Parameters|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Scriptactie azure portal verzenden](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Bekende problemen

+ Momenteel werkt de Spark History Server alleen voor Spark 2.3 en 2.4.

+ Invoer- en uitvoergegevens die RDD gebruiken, worden niet weergegeven op het tabblad **Gegevens.**

## <a name="next-steps"></a>Volgende stappen

+ [Resources beheren voor een Apache Spark-cluster op HDInsight](apache-spark-resource-manager.md)
+ [Apache Spark-instellingen configureren](apache-spark-settings.md)

## <a name="feedback"></a>Feedback

Als u feedback hebt of problemen tegenkomt bij het gebruik[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)van deze tool, stuur dan een e-mail naar ( ).
