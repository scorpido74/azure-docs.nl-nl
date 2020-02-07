---
title: 'Bewaking: Apache Ambari & Azure Monitor-logboeken-Azure HDInsight'
description: Informatie over het gebruik van Ambari-en Azure Monitor-logboeken voor het bewaken van de cluster status en beschik baarheid.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060168"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>De beschik baarheid van clusters bewaken met Apache Ambari en Azure Monitor logs

HDInsight-clusters bevatten zowel Apache Ambari, dat status informatie bevat in één oogopslag en vooraf gedefinieerde waarschuwingen, evenals Azure Monitor logboek integratie, waarmee gegevens over metrische gegevens en logboeken kunnen worden verzameld, evenals Configureer bare waarschuwingen.

In dit document ziet u hoe u deze hulpprogram ma's kunt gebruiken om uw cluster te bewaken en vindt u enkele voor beelden voor het configureren van een Ambari-waarschuwing, het bewaken van de beschik baarheid van knoop punten en het maken van een Azure Monitor waarschuwing die wordt geactiveerd wanneer er geen heartbeat is ontvangen van een of meer knoop punten over vijf uur.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Dashboard

U kunt het Ambari-dash board openen door de koppeling **Ambari Home** te selecteren in het gedeelte **cluster dashboards** van het overzicht van HDInsight in azure Portal, zoals hieronder wordt weer gegeven. U kunt deze ook openen door te navigeren naar `https://CLUSTERNAME.azurehdinsight.net` in een browser waarbij CLUSTERNAME de naam van uw cluster is.

![Weer gave HDInsight-resource Portal](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Vervolgens wordt u gevraagd om een gebruikers naam en wacht woord voor het aanmelden bij een cluster. Voer de referenties in die u hebt gekozen tijdens het maken van het cluster.

Vervolgens gaat u naar het Ambari-dash board, dat widgets bevat die een aantal metrische gegevens bevatten om u een kort overzicht te geven van de status van uw HDInsight-cluster. In deze widgets worden metrische gegevens weer gegeven, zoals het aantal live DataNodes (werk knooppunten) en het beschik journalnodes (Zookeeper-knoop punt), NameNodes (hoofd knooppunten), evenals de metrische gegevens die specifiek zijn voor bepaalde cluster typen, zoals de uptime van de Resource Manager voor Spark-en Hadoop-clusters.

![Apache Ambari-weer gave dash board gebruiken](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosts: de status van afzonderlijke knoop punten weer geven

U kunt ook status informatie voor afzonderlijke knoop punten weer geven. Selecteer het tabblad **hosts** om een lijst met alle knoop punten in uw cluster weer te geven en Bekijk basis informatie over elk knoop punt. De groene controle links van elke knooppunt naam geeft aan dat alle onderdelen op het knoop punt zijn ingesteld. Als een onderdeel op een knoop punt niet beschikbaar is, ziet u een rode waarschuwings driehoek in plaats van de groene controle.

![Weer gave HDInsight Apache Ambari-hosts](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

U kunt vervolgens de **naam** van een knoop punt selecteren om meer gedetailleerde metrische gegevens van de host voor dat specifieke knoop punt weer te geven. In deze weer gave wordt de status/Beschik baarheid van elk afzonderlijk onderdeel weer gegeven.

![Apache Ambari host weer gave met één knoop punt](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari-waarschuwingen

Ambari biedt ook verschillende Configureer bare waarschuwingen waarmee u bepaalde gebeurtenissen kunt melden. Wanneer waarschuwingen worden geactiveerd, worden ze weer gegeven in de linkerbovenhoek van Ambari in een rode badge met het aantal waarschuwingen. Als u deze badge selecteert, wordt er een lijst met huidige waarschuwingen weer gegeven.

![Aantal huidige waarschuwingen Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Als u een lijst met waarschuwings definities en hun statussen wilt weer geven, selecteert u het tabblad **waarschuwingen** , zoals hieronder wordt weer gegeven.

![Weer gave Ambari-waarschuwings definities](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari biedt veel vooraf gedefinieerde waarschuwingen met betrekking tot Beschik baarheid, waaronder:

| Naam van waarschuwing                        | Beschrijving   |
|---|---|
| DataNode status overzicht           | Deze waarschuwing op service niveau wordt geactiveerd als er sprake is van een slechte DataNodes|
| Status van hoge Beschik baarheid van NameNode | Deze waarschuwing op service niveau wordt geactiveerd als de actieve NameNode of stand-NameNode niet actief is.|
| Percentage beschik journalnodes beschikbaar    | Deze waarschuwing wordt geactiveerd als het aantal beschik journalnodes in het cluster hoger is dan de geconfigureerde kritieke drempel waarde. Hiermee worden de resultaten van JournalNode-proces controles geaggregeerd. |
| Percentage DataNodes beschikbaar       | Deze waarschuwing wordt geactiveerd als het aantal DataNodes in het cluster hoger is dan de geconfigureerde kritieke drempel waarde. Hiermee worden de resultaten van DataNode-proces controles geaggregeerd.|

[Hier](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)vindt u een volledige lijst met Ambari-waarschuwingen die u helpen de beschik baarheid van een cluster te bewaken.

Als u Details voor een waarschuwing wilt weer geven of criteria wilt wijzigen, selecteert u de **naam** van de waarschuwing. Maak een voor beeld van **DataNode-status samenvatting** . U ziet een beschrijving van de waarschuwing en de specifieke criteria die een waarschuwing of kritieke waarschuwing activeren en het controle-interval voor de criteria. Als u de configuratie wilt bewerken, selecteert u de knop **bewerken** in de rechter bovenhoek van het configuratie venster.

![Configuratie van Apache Ambari-waarschuwingen](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Hier kunt u de beschrijving bewerken en, belang rijker, het controle-interval en de drempel waarden voor waarschuwing of kritieke waarschuwingen.

![Ambari waarschuwings configuraties weer gave bewerken](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

In dit voor beeld zou u 2 een slechte DataNodes activeren en een kritieke waarschuwing geven en 1 slecht DataNode alleen een waarschuwing activeren. Selecteer **Opslaan** wanneer u klaar bent met het bewerken van.

### <a name="email-notifications"></a>E-mailmeldingen

U kunt desgewenst ook e-mail meldingen configureren voor Ambari-waarschuwingen. Als u dit wilt doen, klikt u op het tabblad **waarschuwingen** op de knop **acties** in de linkerbovenhoek en vervolgens op **meldingen beheren.**

![Actie Ambari beheren](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Er wordt een dialoog venster geopend voor het beheren van waarschuwings meldingen. Selecteer de **+** aan de onderkant van het dialoog venster en vul de vereiste velden in om Ambari te bieden met de details van de e-mail server waaruit e-mail berichten worden verzonden.

> [!TIP]
> Het instellen van Ambari-e-mail meldingen kan een goede manier zijn om waarschuwingen op één plek te ontvangen bij het beheren van veel HDInsight-clusters.

## <a name="azure-monitor-logs-integration"></a>Integratie van Azure Monitor-logboeken

Azure Monitor logboeken maken het mogelijk om gegevens die zijn gegenereerd door meerdere resources, zoals HDInsight-clusters, op één plek te verzamelen en samen te voegen voor een uniforme bewakings ervaring.

Als vereiste hebt u een Log Analytics-werk ruimte nodig om de verzamelde gegevens op te slaan. Als u er nog geen hebt gemaakt, kunt u hier de instructies volgen: [Maak een log Analytics-werk ruimte](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Integratie van HDInsight Azure Monitor-logboeken inschakelen

Selecteer op de pagina HDInsight-cluster resource in de Portal de optie **Azure monitor**. Selecteer vervolgens **inschakelen** en selecteer uw log Analytics-werk ruimte in de vervolg keuzelijst.

![HDInsight Operations Management Suite](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Query's uitvoeren op metrische gegevens en logboeken van tabellen

Als de integratie van Azure Monitor logboek is ingeschakeld (dit kan enkele minuten duren), navigeert u naar uw **log Analytics werkruimte** resource en selecteert u **Logboeken**.

![Log Analytics werkruimte logboeken](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

In Logboeken worden een aantal voorbeeld query's weer geven, zoals:

| Query naam                      | Beschrijving                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Computers Beschik baarheid vandaag    | Grafiek het aantal computers dat Logboeken verzendt, elk uur                     |
| Heartbeats weer geven                 | Alle Heartbeats van de computer in het afgelopen uur weer geven                           |
| Laatste heartbeat van elke computer | De laatste heartbeat weer geven die door elke computer is verzonden                             |
| Niet-beschik bare computers           | Alle bekende computers weer geven die de afgelopen vijf uur geen heartbeat hebben verzonden |
| Beschikbaarheids tempo               | De beschik baarheid van elke verbonden computer berekenen                |

Voer bijvoorbeeld de voorbeeld query **beschikbaarheids frequentie** uit door **uitvoeren** op die query te selecteren, zoals wordt weer gegeven in de bovenstaande scherm afbeelding. Hiermee wordt de beschikbaarheids percentage van elk knoop punt in het cluster weer gegeven als een percentage. Als u meerdere HDInsight-clusters hebt ingeschakeld voor het verzenden van metrische gegevens naar dezelfde Log Analytics-werk ruimte, ziet u de beschik baarheid van alle knoop punten in die clusters weer gegeven.

![Voorbeeld query beschik baarheids frequentie van Log Analytics werkruimte logboeken](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> Het beschikbaarheids tarief wordt gemeten over een periode van 24 uur, zodat uw cluster ten minste 24 uur moet worden uitgevoerd voordat er nauw keurige beschikbaarheids tarieven worden weer geven.

U kunt deze tabel vastmaken aan een gedeeld dash board door in de rechter bovenhoek op **vastmaken** te klikken. Als u geen Beschrijf bare gedeelde Dash boards hebt, kunt u deze hier maken: [Dash boards maken en delen in de Azure Portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Azure Monitor waarschuwingen

U kunt ook Azure Monitor waarschuwingen instellen die worden geactiveerd wanneer de waarde van een metriek of de resultaten van een query voldoen aan bepaalde voor waarden. U kunt bijvoorbeeld een waarschuwing maken om een e-mail bericht te verzenden wanneer een of meer knoop punten niet langer dan 5 uur een heartbeat hebben verzonden (dat wil zeggen dat deze niet beschikbaar is).

Voer vanuit **Logboeken**de voorbeeld query niet- **beschik bare computers** uit door **uitvoeren** op die query te selecteren, zoals hieronder wordt weer gegeven.

![Voor beeld van Log Analytics werkruimte logboeken ' niet-beschik bare computers '](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Als alle knoop punten beschikbaar zijn, moet deze query voor Taan nul resultaten retour neren. Klik op **nieuwe waarschuwings regel** om te beginnen met het configureren van de waarschuwing voor deze query.

![Nieuwe waarschuwings regel Log Analytics werk ruimte](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Er zijn drie onderdelen voor een waarschuwing: de *resource* waarvoor u de regel (de log Analytics-werk ruimte in dit geval) wilt maken, de *voor waarde* voor het activeren van de waarschuwing en de *actie groepen* die bepalen wat er gebeurt wanneer de waarschuwing wordt geactiveerd.
Klik op de titel van de **voor waarde**, zoals hieronder wordt weer gegeven, om het configureren van de signaal logica te volt ooien.

![Status voor regel voor maken van portal waarschuwing](media/hdinsight-cluster-availability/portal-condition-title.png)

Hiermee wordt de **logica**voor het configureren van signalen geopend.

Stel de sectie **waarschuwings logica** als volgt in:

*Op basis van: aantal resultaten, voor waarde: groter dan, drempel waarde: 0.*

Omdat deze query alleen niet-beschik bare knoop punten retourneert als resultaat als het aantal resultaten ooit groter is dan 0, moet de waarschuwing worden geactiveerd.

Stel in het gedeelte **geëvalueerd op basis van** de **periode** en **frequentie** in op basis van hoe vaak u wilt controleren op niet-beschik bare knoop punten.

Voor het doel van deze waarschuwing moet u de frequentie van de **periode instellen.** Meer informatie over periode, frequentie en andere waarschuwings parameters vindt u [hier](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Selecteer **gereed** wanneer u klaar bent met het configureren van de signaal logica.

![Waarschuwings regel configureert signaal logica](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Als u nog geen actie groep hebt, klikt u op **nieuwe maken** onder de sectie **actie groepen** .

![Waarschuwings regel maakt nieuwe actie groep](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Hiermee wordt de **actie groep toevoegen**geopend. Kies een **naam voor de actie groep**, de **korte naam**, het **abonnement**en de **resource groep.** Kies in de sectie **acties** een **actie naam** en selecteer **e-mail/SMS/push/Voice** als **actie type.**

> [!NOTE]
> Er zijn verschillende andere acties die een waarschuwing kan activeren naast een E-mail/SMS/push/Voice, zoals een Azure-functie, LogicApp, webhook, ITSM en Automation-Runbook. [Meer informatie.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Hiermee wordt **e-mail/SMS/push/Voice**geopend. Kies een **naam** voor de ontvanger, **Schakel** het vak **e-mail** in en typ een e-mail adres waarnaar de waarschuwing moet worden verzonden. Selecteer **OK** in **e-mail/SMS/push/Voice**en klik vervolgens in **actie groep toevoegen** om het configureren van uw actie groep te volt ooien.

![Waarschuwings regel maakt actie groep toevoegen](media/hdinsight-cluster-availability/portal-add-action-group.png)

Nadat deze Blades zijn gesloten, ziet u dat uw actie groep wordt weer gegeven onder de sectie **actie groepen** . Vul tot slot de **sectie waarschuwings Details** in door een naam en **Beschrijving** voor de **waarschuwings regel** te typen en een **Ernst**te kiezen. Klik op **waarschuwings regel maken** om te volt ooien.

![Portal maakt een eind datum voor de waarschuwings regel](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> De mogelijkheid om **Ernst** op te geven is een krachtig hulp programma dat kan worden gebruikt bij het maken van meerdere waarschuwingen. U kunt bijvoorbeeld één waarschuwing maken om een waarschuwing te genereren (Ernst 1) als één hoofd knooppunt wordt weer gegeven en een andere waarschuwing die kritiek (Ernst 0) veroorzaakt in het onwaarschijnlijke geval dat beide hoofd knooppunten omlaag gaan.

Wanneer aan de voor waarde voor deze waarschuwing wordt voldaan, wordt de waarschuwing weer gegeven en ontvangt u een e-mail bericht met de details van de waarschuwing, zoals:

![Voor beeld van een e-mail bericht Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

U kunt ook alle waarschuwingen weer geven die zijn geactiveerd, gegroepeerd op Ernst, door naar **waarschuwingen** in uw log Analytics- **werk ruimte**te gaan.

![Waarschuwingen voor Log Analytics werk ruimte](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Als u een Ernst groep selecteert (dat wil zeggen **Ernst 1,** zoals hierboven is gemarkeerd), worden de records weer gegeven voor alle waarschuwingen van de ernst die als hieronder zijn geactiveerd:

![Eén waarschuwing Log Analytics werk ruimte Ernst](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Volgende stappen

- [Beschik baarheid en betrouw baarheid van Apache Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md)
