---
title: 'Monitoring: Apache Ambari & Azure Monitor logs - Azure HDInsight'
description: Meer informatie over het gebruik van Ambari- en Azure Monitor-logboeken om de clusterstatus en -beschikbaarheid te controleren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060168"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Clusterbeschikbaarheid controleren met Apache Ambari- en Azure Monitor-logboeken

HDInsight-clusters omvatten zowel Apache Ambari, die gezondheidsinformatie in één oogopslag en vooraf gedefinieerde waarschuwingen biedt, evenals Azure Monitor-logboekenintegratie, die querybare statistieken en logboeken biedt, evenals configureerbare waarschuwingen.

In dit document ziet u hoe u deze hulpprogramma's gebruiken om uw cluster te controleren en enkele voorbeelden doorlopen voor het configureren van een Ambari-waarschuwing, het bewaken van de beschikbaarheidssnelheid van knooppunten en het maken van een Azure Monitor-waarschuwing die wordt geactiveerd wanneer een heartbeat niet is ontvangen van een of meer knooppunten over vijf uur.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Dashboard

Het Ambari-dashboard is toegankelijk door de **Ambari-koppeling te** selecteren in het gedeelte **Clusterdashboards** van het HDInsight-overzicht in Azure-portal zoals hieronder weergegeven. U ook toegang krijgen tot `https://CLUSTERNAME.azurehdinsight.net` het cluster waar clusternaam de naam van uw cluster is.

![HDInsight-bronportalweergave](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

U wordt dan gevraagd om een gebruikersnaam en wachtwoord voor het inloggen van het cluster. Voer de referenties in die u hebt gekozen toen u het cluster maakte.

U wordt vervolgens naar het Ambari-dashboard gebracht, dat widgets bevat die een handvol statistieken weergeven om u een snel overzicht te geven van de status van uw HDInsight-cluster. Deze widgets tonen statistieken zoals het aantal live DataNodes (worker nodes) en JournalNodes (zookeeper node), NameNodes (head nodes) uptime, evenals metrics die specifiek zijn voor bepaalde clustertypen, zoals YARN ResourceManager uptime voor Spark- en Hadoop-clusters.

![Apache Ambari gebruik dashboarddisplay](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosts – bekijk de status van het afzonderlijke knooppunt

U ook statusgegevens voor afzonderlijke knooppunten weergeven. Selecteer het tabblad **Hosts** om een lijst met alle knooppunten in uw cluster weer te geven en bekijk basisinformatie over elk knooppunt. De groene controle links van elke knooppuntnaam geeft aan dat alle onderdelen zich op het knooppunt bevinden. Als een component op een knooppunt is uitgeschakeld, ziet u een rode waarschuwingsdriehoek in plaats van de groene controle.

![HDInsight Apache Ambari hosts view HDInsight Apache Ambari hosts view HDInsight Apache Ambari hosts view HDInsight](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

U vervolgens op de **naam** van een knooppunt selecteren om meer gedetailleerde hoststatistieken voor dat specifieke knooppunt weer te geven. Deze weergave toont de status/beschikbaarheid van elk afzonderlijk onderdeel.

![Apache Ambari host weergave met één knooppunt](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari-waarschuwingen

Ambari biedt ook verschillende configureerbare waarschuwingen die melding kunnen geven van bepaalde gebeurtenissen. Wanneer waarschuwingen worden geactiveerd, worden ze in de linkerbovenhoek van Ambari weergegeven in een rode badge met het aantal waarschuwingen. Als u deze badge selecteert, wordt een lijst met huidige waarschuwingen weergegeven.

![Apache Ambari huidige waarschuwingen tellen](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Als u een lijst met waarschuwingsdefinities en hun statussen wilt weergeven, selecteert u het tabblad **Waarschuwingen,** zoals hieronder wordt weergegeven.

![Ambari waarschuwt definities weergave](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari biedt veel vooraf gedefinieerde waarschuwingen met betrekking tot beschikbaarheid, waaronder:

| Naam van waarschuwing                        | Beschrijving   |
|---|---|
| Samenvatting van de status gegevensnode           | Deze waarschuwing op serviceniveau wordt geactiveerd als er ongezonde DataNodes zijn|
| NameNode High Availability Health | Deze waarschuwing op serviceniveau wordt geactiveerd als de Active NameNode of Standby NameNode niet worden uitgevoerd.|
| Percentage JournalNodes beschikbaar    | Deze waarschuwing wordt geactiveerd als het aantal down-JournalNodes in het cluster groter is dan de geconfigureerde kritieke drempelwaarde. Het verzamelt de resultaten van JournalNode procescontroles. |
| Percenten dataknooppunten beschikbaar       | Deze waarschuwing wordt geactiveerd als het aantal down DataNodes in het cluster groter is dan de geconfigureerde kritieke drempelwaarde. Het verzamelt de resultaten van DataNode procescontroles.|

Een volledige lijst van Ambari-waarschuwingen die helpen bij het bewaken van de beschikbaarheid van een cluster is [hier](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)te vinden,

Als u details voor een waarschuwing wilt weergeven of criteria wilt wijzigen, selecteert u de **naam** van de waarschuwing. Neem **het datanode-overzicht als** voorbeeld. U een beschrijving van de waarschuwing zien, evenals de specifieke criteria die een waarschuwing of 'kritieke' waarschuwing en het controle-interval voor de criteria activeren. Als u de configuratie wilt bewerken, selecteert u de knop **Bewerken** in de rechterbovenhoek van het vak Configuratie.

![Configuratie van Apache Ambari-waarschuwing](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Hier u de beschrijving en, nog belangrijker, het controleinterval en de drempelwaarden voor waarschuwingof kritieke waarschuwingen bewerken.

![Ambari-waarschuwingsconfiguraties bewerkingsweergave](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

In dit voorbeeld u ervoor zorgen dat 2 ongezonde DataNodes een kritieke waarschuwing activeren en 1 ongezonde DataNode alleen een waarschuwing activeren. Selecteer **Opslaan** wanneer u klaar bent met bewerken.

### <a name="email-notifications"></a>E-mailmeldingen

U ook optioneel e-mailmeldingen configureren voor Ambari-waarschuwingen. Klik hiervoor op het tabblad **Waarschuwingen** op de knop **Acties** linksboven en vervolgens **Meldingen beheren.**

![Ambari beheren meldingen actie](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Er wordt een dialoogvenster geopend voor het beheren van waarschuwingsmeldingen. Selecteer **+** de onderaan van het dialoogvenster en vul de vereiste velden in om Ambari te voorzien van e-mailservergegevens van waaruit u e-mails verzenden.

> [!TIP]
> Het instellen van Ambari-e-mailmeldingen kan een goede manier zijn om waarschuwingen op één plaats te ontvangen bij het beheren van veel HDInsight-clusters.

## <a name="azure-monitor-logs-integration"></a>Azure Monitor registreert integratie

Azure Monitor-logboeken maken het mogelijk gegevens die door meerdere bronnen worden gegenereerd, zoals HDInsight-clusters, op één plaats te verzamelen en samen te voegen om een uniforme bewakingservaring te bereiken.

Als voorwaarde hebt u een Log Analytics Workspace nodig om de verzamelde gegevens op te slaan. Als u er nog geen hebt gemaakt, u hier instructies volgen: [Een logboekanalysewerkruimte maken.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Integratie van HDInsight Azure Monitor-logboeken inschakelen

Selecteer **Azure Monitor**op de pagina HDInsight-clusterbron in de portal . Selecteer vervolgens de werkruimte Log Analytics **in-** en selecteert u in de vervolgkeuzelijst.

![HDInsight Operations Management Suite](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Querystatistieken en logboekentabellen

Zodra azure monitor-logboekintegratie is ingeschakeld (dit kan enkele minuten duren), navigeert u naar uw **logboekanalysewerkruimtebron** en selecteert u **Logboeken.**

![Logboekanalysewerkruimtelogboeken](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

In logboeken worden een aantal voorbeeldquery's weergegeven, zoals:

| Querynaam                      | Beschrijving                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Beschikbaarheid van computers vandaag    | Het aantal computers dat logboeken verzendt, elk uur in kaart brengen                     |
| Heartbeats weergeven                 | Alle computerheartbeats van het laatste uur weergeven                           |
| Laatste hartslag van elke computer | De laatste hartslag weergeven die door elke computer wordt verzonden                             |
| Niet-beschikbare computers           | Vermeld alle bekende computers die in de afgelopen 5 uur geen heartbeat hebben verzonden |
| Beschikbaarheidspercentage               | De beschikbaarheidssnelheid van elke aangesloten computer berekenen                |

Voer bijvoorbeeld de voorbeeldquery **Beschikbaarheidssnelheid** uit door **Uitvoeren** op die query te selecteren, zoals in de bovenstaande schermafbeelding wordt weergegeven. Hiermee wordt het beschikbaarheidspercentage van elk knooppunt in uw cluster als percentage weergegeven. Als u meerdere HDInsight-clusters hebt ingeschakeld om statistieken naar dezelfde loganalytics-werkruimte te verzenden, ziet u de beschikbaarheidssnelheid voor alle knooppunten in die clusters die worden weergegeven.

![Voorbeeldquery voor logboekanalysewerkruimte registreert voorbeeldquery 'beschikbaarheidssnelheid'](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> De beschikbaarheidssnelheid wordt gemeten over een periode van 24 uur, dus uw cluster moet minstens 24 uur worden uitgevoerd voordat u nauwkeurige beschikbaarheidspercentages ziet.

U deze tabel vastmaken aan een gedeeld dashboard door rechtsboven op **Vasttemaken** te klikken. Als u geen beschrijfbare gedeelde dashboards hebt, u hier zien hoe u er een maken: [Dashboards maken en delen in de Azure-portal.](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)

### <a name="azure-monitor-alerts"></a>Azure Monitor-waarschuwingen

U azure monitorwaarschuwingen ook instellen die worden geactiveerd wanneer de waarde van een statistiek of de resultaten van een query aan bepaalde voorwaarden voldoen. Laten we bijvoorbeeld een waarschuwing maken om een e-mail te verzenden wanneer een of meer knooppunten binnen 5 uur geen heartbeat hebben verzonden (d.w.z. wordt verondersteld niet beschikbaar te zijn).

Voer vanuit **Logboeken**de voorbeeldquery **Voor niet-beschikbare computers** uit door **Uitvoeren** op die query te selecteren, zoals hieronder wordt weergegeven.

![Voorbeeld van logboekanalysewerkruimtelogboeken 'niet-beschikbare computers'](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Als alle knooppunten beschikbaar zijn, moet deze query voorlopig nul resultaten opleveren. Klik **op Nieuwe waarschuwingsregel** om te beginnen met het configureren van uw waarschuwing voor deze query.

![Nieuwe waarschuwingsregel log Analytics-werkruimte](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Er zijn drie componenten voor een waarschuwing: de *resource* waarvoor de regel moet worden gemaakt (de werkruimte Log Analytics in dit geval), de *voorwaarde* om de waarschuwing te activeren en de *actiegroepen* die bepalen wat er gebeurt wanneer de waarschuwing wordt geactiveerd.
Klik op de **voorwaardetitel**, zoals hieronder weergegeven, om de signaallogica te voltooien.

![Portal waarschuwing maken regel voorwaarde](media/hdinsight-cluster-availability/portal-condition-title.png)

Hiermee wordt **signaallogica configureren geopend**.

Stel de sectie **Waarschuwingslogica** als volgt in:

*Gebaseerd op: Aantal resultaten, Voorwaarde: Groter dan, Drempel: 0.*

Aangezien deze query alleen niet-beschikbare knooppunten als resultaat retourneert, moet de waarschuwing worden afgeslagen als het aantal resultaten steeds groter is dan 0.

Stel **in de sectie Geëvalueerd op basis van** sectie de **periode** en **frequentie** in op basis van hoe vaak u wilt controleren op niet-beschikbare knooppunten.

Voor deze waarschuwing wilt u er zeker van zijn dat **Period=Frequency.** Meer informatie over periode, frequentie en andere waarschuwingsparameters vindt u [hier.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)

Selecteer **Gereed** wanneer u klaar bent met het configureren van de signaallogica.

![Waarschuwingsregel configureert signaallogica](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Als u nog geen bestaande actiegroep hebt, klikt u op **Nieuw maken** onder de sectie **Actiegroepen.**

![Waarschuwingsregel maakt nieuwe actiegroep](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Hiermee wordt **actiegroep Toevoegen geopend**. Kies een **groep naam van**een actiegroep , Korte **naam**, **Abonnement**en **Resourcegroep.** Kies onder de sectie **Acties** een **actienaam** en selecteer **E-mail/SMS/Push/Voice** als **actietype.**

> [!NOTE]
> Er zijn verschillende andere acties die een waarschuwing kan activeren naast een e-mail/sms/push/voice, zoals een Azure-functie, LogicApp, Webhook, ITSM en Automation Runbook. [Meer informatie.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Hiermee wordt **E-mail/SMS/Push/Voice geopend.** Kies een **naam** voor de ontvanger, **schakel** het selectievakje **E-mail** in en typ een e-mailadres waarnaar u de waarschuwing wilt verzenden. Selecteer **OK** in **E-mail/SMS/Push/Voice**en vervolgens in **Actiegroep toevoegen** om het configureren van uw actiegroep te voltooien.

![Waarschuwingsregel maakt toevoegen actiegroep](media/hdinsight-cluster-availability/portal-add-action-group.png)

Nadat deze bladen zijn gesloten, ziet u uw actiegroep onder de sectie **Actiegroepen.** Vul ten slotte de sectie **Waarschuwingsdetails in** door een naam en **beschrijving** **van een waarschuwingsregel** te typen en een **ernst te**kiezen. Klik **op Waarschuwingsregel maken** om te voltooien.

![Portal maakt finish van waarschuwingsregel](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> De mogelijkheid om **Ernst** op te geven is een krachtig hulpmiddel dat kan worden gebruikt bij het maken van meerdere waarschuwingen. U bijvoorbeeld één waarschuwing maken om een waarschuwing (Sev 1) te verhogen als een enkel hoofdknooppunt naar beneden gaat en een andere waarschuwing die kritiek (Sev 0) verhoogt in het onwaarschijnlijke geval dat beide hoofdknooppunten naar beneden gaan.

Wanneer aan de voorwaarde voor deze waarschuwing is voldaan, wordt de waarschuwing afgegaan en ontvangt u een e-mail met de waarschuwingsgegevens als volgt:

![Voorbeeld van azure monitor-waarschuwingse-mail](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

U ook alle waarschuwingen bekijken die zijn geactiveerd, gegroepeerd op ernst, door naar **Waarschuwingen** in uw **Logboekanalysewerkruimte**te gaan.

![Waarschuwingen voor logboekanalysewerkruimtes](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Als u op een ernstgroepering selecteert (d.w.z. **Sev 1,** zoals hierboven is aangegeven), worden records weergegeven voor alle waarschuwingen van die ernst die zijn afgevuurd zoals hieronder:

![Log Analytics workspace sev one alert](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Volgende stappen

- [Beschikbaarheid en betrouwbaarheid van Apache Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md)
