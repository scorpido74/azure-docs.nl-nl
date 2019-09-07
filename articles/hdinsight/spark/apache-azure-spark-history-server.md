---
title: Uitgebreide Spark-geschiedenis server voor het opsporen van fouten in Spark-toepassingen-Azure HDInsight
description: Uitgebreide Spark-geschiedenis server gebruiken voor het opsporen en diagnosticeren van Spark-toepassingen-Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 9ba03f6b1e658e08c3d07d7ccb5e2a99e96fe69c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736457"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Uitgebreide Apache Spark geschiedenis server gebruiken om fouten op te sporen en op te sporen Apache Spark toepassingen

Dit artikel bevat richt lijnen voor het gebruik van uitgebreide Apache Spark geschiedenis server voor het opsporen en diagnosticeren van voltooide en actieve Spark-toepassingen. De uitbrei ding bevat tabblad gegevens en tabblad en het tabblad diagnose. Op het tabblad **gegevens** kunnen gebruikers de invoer-en uitvoer gegevens van de Spark-taak controleren. Op het tabblad **grafiek** kunnen gebruikers de gegevens stroom controleren en de taak grafiek opnieuw afspelen. Op het tabblad **diagnose** kan de gebruiker verwijzen naar **gegevens scheefheid**, **tijd verschil** en **gebruiks analyse**.

## <a name="get-access-to-apache-spark-history-server"></a>Toegang krijgen tot de Apache Spark-geschiedenis server

Apache Spark geschiedenis server is de Web-UI voor voltooide en actieve Spark-toepassingen. 

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>De Web-UI van de Apache Spark geschiedenis server openen vanuit Azure Portal

1. Open in het [Azure Portal](https://portal.azure.com/)het Spark-cluster. Zie voor meer informatie het [overzicht en weer geven van clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Klik vanuit **snelle koppelingen**op **cluster dashboard**en klik vervolgens op **Spark-geschiedenis server**. Wanneer u hierom wordt gevraagd, voert u de beheerders referenties in voor het Spark-cluster. 

    ![Spark-geschiedenis server](./media/apache-azure-spark-history-server/launch-history-server.png "Spark-geschiedenis server")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>De Web-UI van de Spark-geschiedenis server op URL openen
Open de Spark-geschiedenis server door te bladeren naar de volgende URL `<ClusterName>` , vervang door de Spark-cluster naam van de klant.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

De Web-UI van de Spark-geschiedenis server ziet er als volgt uit:

![HDInsight Spark-geschiedenis server](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="data-tab-in-spark-history-server"></a>Het tabblad gegevens in de Spark-geschiedenis server
Selecteer taak-ID en klik vervolgens op **gegevens** in het menu van het hulp programma om de gegevens weergave op te halen.

+ Controleer de **invoer**, **uitvoer**en **tabel bewerkingen** door de tabbladen afzonderlijk te selecteren.

    ![Gegevens tabbladen](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Kopieer alle rijen door te klikken op de knop **kopiëren**.

    ![Gegevens kopiëren](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Sla alle gegevens op als CSV-bestand door te klikken op de knop **CSV**.

    ![Gegevens opslaan](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Zoek door tref woorden **in zoeken in velden in**te voeren, wordt het Zoek resultaat onmiddellijk weer gegeven.

    ![Gegevens zoeken](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Klik op de kolomkop om de tabel te sorteren en klik op het plus teken om een rij uit te vouwen om meer details weer te geven of klik op het minteken om een rij samen te vouwen.

    ![Gegevens tabel](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ U kunt één bestand downloaden door te klikken op knop **gedeelte downloaden** dat aan de rechter kant wordt weer gegeven. vervolgens wordt het geselecteerde bestand gedownload naar lokale, als het bestand niet meer bestaat, wordt er een nieuw tabblad geopend om de fout berichten weer te geven.

    ![Rij gegevens downloaden](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopieer het volledige pad of het relatieve pad door het **volledige pad kopiëren**te selecteren, **relatief pad kopiëren** dat uit het menu downloaden wordt uitgebreid. Voor Azure data Lake Storage-bestanden **opent u in Azure Storage Explorer** Azure Storage Explorer start en gaat u naar de map wanneer u zich aanmeldt.

    ![Pad voor gegevens kopiëren](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Klik op het nummer onder de tabel om te navigeren naar pagina's wanneer te veel rijen op één pagina worden weer gegeven. 

    ![Gegevens pagina](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Beweeg de muis aanwijzer op het vraag teken naast gegevens om de knop Info weer te geven of klik op het vraag teken voor meer informatie.

    ![Meer informatie](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Stuur feedback met problemen door op **feedback geven**te klikken.

    ![feedback over grafieken](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="graph-tab-in-apache-spark-history-server"></a>Tabblad grafiek in Apache Spark geschiedenis server
Selecteer taak-ID en klik vervolgens op **grafiek** in het menu van het hulp programma om de weer gave taak grafiek op te halen.

+ Bekijk overzicht van uw taak door de gegenereerde taak grafiek. 

+ Standaard worden alle taken weer gegeven. deze kunnen worden gefilterd op **taak-id**.

    ![grafiek taak-ID](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Standaard is de **voortgang** geselecteerd, de gebruiker kan de gegevens stroom controleren door in de vervolg keuzelijst met **weer gave**de optie **lezen/geschreven** te selecteren.

    ![grafiek weergave](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Het grafiek knooppunt wordt weer gegeven in een kleur die de heatmap weergeeft.

    ![grafiek heatmap](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Als u de taak wilt afspelen, klikt u op de knop **afspelen** en stopt u op elk moment door te klikken op de knop stoppen. De taak wordt in kleur weer gegeven om een andere status weer te geven tijdens het afspelen:

  + Groen voor geslaagd: De taak is voltooid.
  + Oranje voor opnieuw proberen: Exemplaren van taken die zijn mislukt, hebben geen invloed op het uiteindelijke resultaat van de taak. Deze taken bevatten dubbele of nieuwe instanties die later kunnen slagen.
  + Blauw voor het uitvoeren van: De taak wordt uitgevoerd.
  + Wit voor wachtend of overgeslagen: De taak wacht op uitvoering of het stadium is overgeslagen.
  + Rood voor mislukt: De taak is mislukt.

    ![kleur voorbeeld van grafiek, uitvoeren](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    De overgeslagen fase wordt wit weer gegeven.
    ![voor beeld van grafiek kleur, overs Laan](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![kleur voorbeeld van grafiek is mislukt](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]  
    > Het afspelen van elke taak is toegestaan. Voor een onvolledige taak wordt afspelen niet ondersteund.


+ Met de muis schuift u in-of uitzoomen op de taak grafiek, of klikt u op **passend** maken om deze aan te passen aan het scherm.
 
    ![grafiek passend maken](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Beweeg de muis aanwijzer op het grafiek knooppunt om de knop Info weer te geven wanneer er mislukte taken zijn en klik op fase om de pagina fase te openen.

    ![knop Info voor grafieken](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Op het tabblad taak grafiek worden de knop Info en het pictogram klein weer gegeven als de taken aan de onderstaande voor waarden voldoen:
  + Gegevens scheefheid: gegevens lezen grootte > gemiddelde Lees grootte van gegevens van alle taken in deze fase * 2 en grootte van gelezen gegevens > 10 MB.
  + Tijd scheefheid: uitvoerings tijd > gemiddelde uitvoerings tijd van alle taken in deze fase * 2 en uitvoerings tijd > 2 minuten.

    ![pictogram grafiek scheefheid](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ In het knoop punt taak grafiek wordt de volgende informatie weer gegeven van elke fase:
  + ID.
  + Naam of beschrijving.
  + Totaal taak nummer.
  + Gegevens lezen: de som van de invoer grootte en de grootte van de Lees bewerking in wille keurige volg orde.
  + Gegevens schrijven: de som van de uitvoer grootte en de schrijf grootte in wille keurige volg orde.
  + Uitvoerings tijd: de tijd tussen de begin tijd van de eerste poging en voltooiings tijd van de laatste poging.
  + Aantal rijen: de som van de invoer records, uitvoer records, in wille keurige volg orde records lezen en schrijf records in wille keurige volg orde opslaan.
  + Gang.

    > [!NOTE]  
    > Het knoop punt taak grafiek geeft standaard informatie weer van de laatste poging van elke fase (met uitzonde ring van de uitvoerings tijd fase), maar tijdens het knoop punt afspelen wordt informatie van elke poging weer gegeven.

    > [!NOTE]  
    > Voor gegevens grootte van lezen en schrijven gebruiken we 1MB = 1000 KB = 1000 * 1000 bytes.

+ Stuur feedback met problemen door op **feedback geven**te klikken.

    ![feedback over grafieken](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Het tabblad diagnose in Apache Spark geschiedenis server
Selecteer taak-ID en klik vervolgens op **diagnose** in het menu van het hulp programma om de taak diagnose weer te geven. Het tabblad diagnose bevat **gegevens scheefheid**, **tijds scheefheid**en **gebruiks analyse**.
    
+ Controleer de **gebruiks analyse** van de **gegevens scheefheid**, het **tijds verschil**en de uitvoerder door de tabbladen respectievelijk te selecteren.

    ![Tabbladen voor diagnose](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Gegevens scheef trekken
Klik op het tabblad **gegevens scheef trekken** , de overeenkomstige scheefende taken worden weer gegeven op basis van de opgegeven para meters. 

+ **Para meters opgeven** : in de eerste sectie worden de para meters weer gegeven die worden gebruikt voor het detecteren van gegevens scheefheid. De ingebouwde regel is: De taak gegevens die zijn gelezen, zijn meer dan drie keer gelezen van de gegevens van het gemiddelde van de taak en de gelezen taak gegevens zijn meer dan 10 MB. Als u uw eigen regel wilt definiëren voor gescheefde taken, kunt u de para meters kiezen, de sectie **scheefe fase**en **schuine tekens** wordt dienovereenkomstig vernieuwd.

+ **Scheefe fase** : in het tweede gedeelte worden fasen weer gegeven met gescheefe taken die voldoen aan de bovenstaande criteria. Als er meer dan een scheefe taak in een fase is, wordt in de tabel verscheefde fase alleen de meest afgeschuinde taak weer gegeven (bijvoorbeeld de grootste gegevens voor het hellen van gegevens).

    ![Gegevens scheefheid section2](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Grafiek scheef trekken** : wanneer een rij in de tabel scheefe fase is geselecteerd, worden in het schuine diagram meer details over taak distributies weer gegeven op basis van de lees-en uitvoerings tijd van de gegevens. De gescheefe taken worden rood gemarkeerd en de normale taken worden blauw gemarkeerd. Ten behoeve van de prestaties worden in de grafiek slechts 100 voorbeeld taken weer gegeven. De taak Details worden weer gegeven in het rechterdeel venster.

    ![Gegevens scheefheid section3](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tijd verschil
Op het tabblad **tijd verschil** worden scheefe taken weer gegeven op basis van de uitvoerings tijd van de taak. 

+ **Para meters opgeven** : in de eerste sectie worden de para meters weer gegeven die worden gebruikt voor het detecteren van tijd scheefheid. De standaard criteria voor het detecteren van tijd verschil is: de uitvoerings tijd van de taak is groter dan drie keer van de gemiddelde uitvoerings tijd en de uitvoerings tijd van de taak is langer dan 30 seconden. U kunt de para meters wijzigen op basis van uw behoeften. In de grafiek **scheefe fase** en **scheefheid** worden de bijbehorende fasen en taak gegevens weer gegeven, net als het tabblad **gegevens scheefheid** hierboven.

+ Klik op **tijd scheefheid**en vervolgens gefilterd resultaat wordt weer gegeven in de sectie **scheefgetrokken fase** volgens de para meters die zijn ingesteld in **para meters opgeven**. Klik op een item in de sectie **scheefgetrokken stage** en vervolgens op de bijbehorende grafiek is geschetst in section3, en de taak Details worden weer gegeven in het rechter paneel.

    ![Tijd scheef trekken section2](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Gebruiks analyse
In de gebruiks grafiek van de uitvoerder wordt de werkelijke uitvoerings toewijzing en uitvoerings status van de Spark-taak gevisualiseerd.  

+ Klik op **gebruiks analyse**uitvoeren en vervolgens vier typen bochten over het gebruik van de bewerkings plaats, inclusief **toegewezen uitvoerende**machines, het **uitvoeren van uitvoerende**machines,**niet-actieve uitvoerende**machines en **Maxi maal**bewerkings instanties. Met betrekking tot de toegewezen uitvoerder worden de toegewezen uitvoerder verg root of verkleind met de gebeurtenis ' uitvoerder toegevoegd ' of ' uitvoer verwijderd '. u kunt ook ' gebeurtenis tijdlijn ' op het tabblad taken voor meer vergelijking controleren.

    ![Tabblad uitvoerende uitvoeringen](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Klik op het kleur pictogram om de bijbehorende inhoud in alle concepten te selecteren of de selectie ervan op te heffen.

    ![Grafiek selecteren](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)


## <a name="faq"></a>Veelgestelde vragen

### <a name="1-revert-to-community-version"></a>1. Herstellen naar Community-versie

Voer de volgende stappen uit om de Community-versie terug te zetten:

1. Open cluster in Ambari. Klik op **Spark2** in het linkerdeel venster.
2. Klik op het tabblad **configuratie** .
3. Vouw de groep **aangepaste spark2-standaard instellingen**uit.
4. Klik op **eigenschap toevoegen**, Voeg **Spark. UI. Enhancement. Enabled = False**, opslaan.
5. De eigenschap wordt nu ingesteld op **Onwaar** .
6. Klik op **Opslaan** om de configuratie op te slaan.

    ![functie wordt uitgeschakeld](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Klik op **Spark2** in het linkerdeel venster, onder tabblad **samen vatting** , op **Spark2 geschiedenis server**.

    ![server1 opnieuw starten](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Start de geschiedenis server opnieuw door te klikken op **opnieuw starten** van de **Spark2-geschiedenis server**.

    ![server2 opnieuw starten](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. De Web-UI van de Spark-geschiedenis server vernieuwen, wordt deze teruggezet naar de Community-versie.

### <a name="2-upload-history-server-event"></a>2. Gebeurtenis geschiedenis server uploaden

Als u een geschiedenis server fout uitvoert, volgt u de stappen om de gebeurtenis op te geven:
1. Down load gebeurtenis door te klikken op **downloaden** in de Web-UI van de geschiedenis server.

    ![Download gebeurtenis](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Klik op **ons feedback geven** via het tabblad gegevens/grafiek.

    ![feedback over grafieken](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Geef de titel en de beschrijving van de fout op, Sleep het zip-bestand naar het veld bewerken en klik vervolgens op **nieuw probleem verzenden**.

    ![bestands probleem](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Het jar-bestand voor het hotfix-scenario bijwerken

Als u een upgrade wilt uitvoeren met hotfix, gebruikt u het onderstaande script om Spark-Enhancement. jar * bij te werken.

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

**Gebruik**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**Voor beeld**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar` 

**Het bash-bestand gebruiken van Azure Portal**

1. Start [Azure Portal](https://ms.portal.azure.com)en selecteer uw cluster.
2. Klik op **script acties**en vervolgens op **nieuwe verzenden**. Voltooi het **actie** formulier voor het indienen van het script en klik vervolgens op de knop **maken** .
    
    + **Script type**: Selecteer **aangepast**.
    + **Naam**: Geef een script naam op.
    + **Bash-script-URI**: upload het bash-bestand naar het persoonlijke cluster en Kopieer hier de URL. U kunt ook de beschik bare URI gebruiken.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

   + Controleren op **kop** en **werk nemers**.
   + **Para meters**: Stel de para meters in op het bash-gebruik.

     ![hotfix voor het uploaden van het logboek of de upgrade](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issues"></a>Bekende problemen

1.  Op dit moment werkt dit alleen voor het Spark 2,3-en 2,4-cluster.

2.  Invoer-en uitvoer gegevens met behulp van RDD worden niet weer gegeven op het tabblad gegevens.

## <a name="next-steps"></a>Volgende stappen

* [Resources voor een Apache Spark cluster in HDInsight beheren](apache-spark-resource-manager.md)
* [Apache Spark-instellingen configureren](apache-spark-settings.md)


## <a name="contact-us"></a>Contact opnemen

Als u feedback hebt of als u andere problemen ondervindt bij het gebruik van dit hulp programma, kunt u een[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)e-mail verzenden via ().
