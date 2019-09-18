---
title: De beschik baarheid van clusters bewaken met Ambari-en Azure Monitor-logboeken
description: Informatie over het gebruik van Ambari-en Azure Monitor-logboeken voor het bewaken van de cluster status en beschik baarheid.
keywords: bewaking, ambari, monitor, log Analytics, waarschuwing, Beschik baarheid, status
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 153c28dc8a06968dc9dd3cfda021496672a094d5
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076556"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>De beschik baarheid van clusters bewaken met Ambari-en Azure Monitor-logboeken

HDInsight-clusters bevatten zowel Apache Ambari, dat status informatie bevat in één oogopslag en vooraf gedefinieerde waarschuwingen, evenals Azure Monitor logboek integratie, waarmee gegevens over metrische gegevens en logboeken kunnen worden verzameld, evenals Configureer bare waarschuwingen.

In dit document ziet u hoe u deze hulpprogram ma's kunt gebruiken om uw cluster te bewaken en worden enkele voor beelden gegeven voor het configureren van een Ambari-waarschuwing, het bewaken van de beschik baarheid van knoop punten en het maken van een Azure Monitor waarschuwing die wordt geactiveerd wanneer er geen heartbeat is ontvangen van een of meer knoop punten over vijf uur.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Dashboard

U kunt het Ambari-dash board openen door te klikken op de koppeling **Ambari Home** in het gedeelte **cluster Dash boards** van de Blade HDInsight Overview in azure Portal, zoals hieronder wordt weer gegeven. U kunt deze ook openen door de volgende URL in te voeren in een browser [https://\<clustername\>. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Weer gave HDInsight-resource Portal](media/hdinsight-cluster-availability/portal-oms-overview1.png)

Vervolgens wordt u gevraagd om een gebruikers naam en wacht woord voor het aanmelden bij een cluster. Voer de referenties in die u hebt gekozen tijdens het maken van het cluster.

Vervolgens gaat u naar het Ambari-dash board, dat widgets bevat die een aantal metrische gegevens bevatten om u een kort overzicht te geven van de status van uw HDInsight-cluster. In deze widgets worden metrische gegevens weer gegeven, zoals het aantal live DataNodes (werk knooppunten) en het beschik journalnodes (Zookeeper-knoop punt), NameNodes (hoofd knooppunten), evenals de metrische gegevens die specifiek zijn voor bepaalde cluster typen, zoals de uptime van de Resource Manager voor Spark-en Hadoop-clusters.

![Apache Ambari-weer gave dash board gebruiken](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosts: de status van afzonderlijke knoop punten weer geven

U kunt ook status informatie voor afzonderlijke knoop punten weer geven. Klik op het tabblad **hosts** om een lijst met alle knoop punten in uw cluster weer te geven en Bekijk basis informatie over elk knoop punt. De groene controle links van elke knooppunt naam geeft aan dat alle onderdelen op het knoop punt zijn ingesteld. Als een onderdeel op een knoop punt niet beschikbaar is, ziet u een rode waarschuwings driehoek in plaats van de groene controle.

![Weer gave HDInsight Apache Ambari-hosts](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

U kunt vervolgens op de **naam** van een knoop punt klikken om gedetailleerdere metrische gegevens van de host voor dat specifieke knoop punt weer te geven. In deze weer gave wordt de status/Beschik baarheid van elk afzonderlijk onderdeel weer gegeven.

![Apache Ambari host weer gave met één knoop punt](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari-waarschuwingen

Ambari biedt ook verschillende Configureer bare waarschuwingen waarmee u bepaalde gebeurtenissen kunt melden. Wanneer waarschuwingen worden geactiveerd, worden ze weer gegeven in de linkerbovenhoek van Ambari in een rode badge met het aantal waarschuwingen. Als u op deze badge klikt, wordt een lijst met huidige waarschuwingen weer gegeven.

![Aantal huidige waarschuwingen Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Als u een lijst met waarschuwings definities en hun statussen wilt weer geven, klikt u op het tabblad **waarschuwingen** , zoals hieronder wordt weer gegeven.

![Weer gave Ambari-waarschuwings definities](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari biedt veel vooraf gedefinieerde waarschuwingen met betrekking tot Beschik baarheid, waaronder:

| Naam van de waarschuwing                        | Description                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode status overzicht           | Deze waarschuwing op service niveau wordt geactiveerd als er sprake is van een slechte DataNodes                                                                                                                |
| Status van hoge Beschik baarheid van NameNode | Deze waarschuwing op service niveau wordt geactiveerd als de actieve NameNode of stand-NameNode niet actief zijn.                                                                              |
| Percentage beschik journalnodes beschikbaar    | Deze waarschuwing wordt geactiveerd als het aantal beschik journalnodes in het cluster hoger is dan de geconfigureerde kritieke drempel waarde. Hiermee worden de resultaten van JournalNode-proces controles geaggregeerd. |
| Percentage DataNodes beschikbaar       | Deze waarschuwing wordt geactiveerd als het aantal DataNodes in het cluster hoger is dan de geconfigureerde kritieke drempel waarde. Hiermee worden de resultaten van DataNode-proces controles geaggregeerd.       |

[Hier](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)vindt u een volledige lijst met Ambari-waarschuwingen die u helpen de beschik baarheid van een cluster te bewaken.

Als u Details voor een waarschuwing wilt weer geven of criteria wilt wijzigen, klikt u op de **naam** van de waarschuwing. Maak een voor beeld van **DataNode-status samenvatting** . U ziet een beschrijving van de waarschuwing en de specifieke criteria die een waarschuwing of kritieke waarschuwing activeren en het controle-interval voor de criteria. Als u de configuratie wilt bewerken, klikt u op de knop **bewerken** in de rechter bovenhoek van het configuratie venster.

![Configuratie van Apache Ambari-waarschuwingen](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Hier kunt u de beschrijving bewerken en, belang rijker, het controle-interval en de drempel waarden voor waarschuwing of kritieke waarschuwingen.

![Bewerkings weergave waarschuwing configuratie Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

In dit voor beeld zou u 2 een slechte DataNodes activeren en een kritieke waarschuwing geven en 1 slecht DataNode alleen een waarschuwing activeren. Klik op **Opslaan** wanneer u klaar bent met het bewerken van.

### <a name="email-notifications"></a>E-mailwaarschuwingen

U kunt desgewenst ook e-mail meldingen configureren voor Ambari-waarschuwingen. Als u dit wilt doen, klikt u op het tabblad **waarschuwingen** op de knop **acties** in de linkerbovenhoek en vervolgens op **meldingen beheren.**

![Actie Ambari beheren](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Er wordt een dialoog venster geopend voor het beheren van waarschuwings meldingen. Klik op **+** de onderkant van het dialoog venster en vul de vereiste velden in om Ambari te bieden met de details van de e-mail server waaruit e-mails worden verzonden.

> [!TIP]
> Het instellen van Ambari-e-mail meldingen kan een goede manier zijn om waarschuwingen op één plek te ontvangen bij het beheren van veel HDInsight-clusters.

## <a name="azure-monitor-logs-integration"></a>Integratie van Azure Monitor-logboeken

Azure Monitor logboeken maakt het mogelijk om gegevens die zijn gegenereerd door meerdere resources, zoals HDInsight-clusters, te verzamelen en samen te voegen op één plek om een uniforme bewakings ervaring te creëren.

Als vereiste hebt u een Log Analytics-werk ruimte nodig om de verzamelde gegevens op te slaan. Als u er nog geen hebt gemaakt, kunt u hier de instructies volgen: [Maak een log Analytics-werk ruimte](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Integratie van HDInsight Azure Monitor-logboeken inschakelen

Klik op de Blade van de HDInsight-cluster resource in de portal op het tabblad **Operations Management Suite** . Klik vervolgens op **inschakelen** en selecteer uw log Analytics-werk ruimte in de vervolg keuzelijst.

![Blade HDInsight Operations Management Suite](media/hdinsight-cluster-availability/hdi-portal-oms-enable.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Query's uitvoeren op metrische gegevens en logboeken tabellen in de Blade logboeken

Als de integratie van Azure Monitor logboek is ingeschakeld (dit kan enkele minuten duren), navigeert u naar uw **log Analytics werkruimte** resource en klikt u op de Blade **Logboeken**

![Blade Log Analytics werkruimte logboeken](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

De Blade **logs** bevat een aantal voorbeeld query's, zoals:

| Query naam                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Computers Beschik baarheid vandaag    | Grafiek het aantal computers dat Logboeken verzendt, elk uur                     |
| Heartbeats weer geven                 | Alle Heartbeats van de computer in het afgelopen uur weer geven                           |
| Laatste heartbeat van elke computer | De laatste heartbeat weer geven die door elke computer is verzonden                             |
| Niet-beschik bare computers           | Alle bekende computers weer geven die de afgelopen vijf uur geen heartbeat hebben verzonden |
| Beschikbaarheids tempo               | De beschik baarheid van elke verbonden computer berekenen                |

Voer bijvoorbeeld de voorbeeld query **beschikbaarheids frequentie** uit door te klikken op **uitvoeren** op die query, zoals wordt weer gegeven in de bovenstaande scherm afbeelding. Hiermee wordt de beschikbaarheids percentage van elk knoop punt in het cluster weer gegeven als een percentage. Als u meerdere HDInsight-clusters hebt ingeschakeld voor het verzenden van metrische gegevens naar dezelfde Log Analytics-werk ruimte, ziet u de beschik baarheid van alle knoop punten in die clusters weer gegeven.

![Voorbeeld query beschik baarheids frequentie van de Blade Log Analytics-werk ruimte](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Het beschikbaarheids tarief wordt gemeten over een periode van 24 uur, zodat uw cluster ten minste 24 uur moet worden uitgevoerd voordat er nauw keurige beschikbaarheids tarieven worden weer geven.

U kunt deze tabel vastmaken aan een gedeeld dash board door in de rechter bovenhoek op **vastmaken** te klikken. Als u nog geen Beschrijf bare gedeelde Dash boards hebt, kunt u hier zien hoe u er een maakt: [Dash boards maken en delen in de Azure Portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Azure Monitor waarschuwingen

U kunt ook Azure Monitor waarschuwingen instellen die worden geactiveerd wanneer de waarde van een metriek of de resultaten van een query voldoen aan bepaalde voor waarden. U kunt bijvoorbeeld een waarschuwing maken om een e-mail bericht te verzenden wanneer een of meer knoop punten niet langer dan 5 uur een heartbeat hebben verzonden (dat wil zeggen dat deze niet beschikbaar is).

Voer op de Blade **Logboeken** de voorbeeld query **niet-beschik bare computers** uit door te klikken op **uitvoeren** op die query, zoals hieronder wordt weer gegeven.

![Voor beeld van Blade ' niet-beschik bare computers ' in Log Analytics werkruimte logboeken](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Als alle knoop punten beschikbaar zijn, moet deze query 0 resultaten voor nu retour neren. Klik op **nieuwe waarschuwings regel** om te beginnen met het configureren van de waarschuwing voor deze query.

![Nieuwe waarschuwings regel Log Analytics werk ruimte](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Er zijn drie onderdelen voor een waarschuwing: de *resource* waarvoor u de regel (de log Analytics-werk ruimte in dit geval) wilt maken, de *voor waarde* voor het activeren van de waarschuwing en de *actie groepen* die bepalen wat er gebeurt wanneer de waarschuwing wordt geactiveerd.

Klik op de titel van de **voor waarde**, zoals hieronder wordt weer gegeven, om het configureren van de signaal logica te volt ooien.

![Status voor regel voor maken van portal waarschuwing](media/hdinsight-cluster-availability/portal-condition-title.png)

Hiermee opent u de Blade **signaal logica configureren** .

Stel de sectie **waarschuwings logica** als volgt in:

*Op basis van: Aantal resultaten, voor waarde: Groter dan, drempel waarde: 0,3.*

Omdat deze query alleen niet-beschik bare knoop punten retourneert als resultaat als het aantal resultaten ooit groter is dan 0, moet de waarschuwing worden geactiveerd.

Stel in het gedeelte **geëvalueerd op basis van** de **periode** en **frequentie** in op basis van hoe vaak u wilt controleren op niet-beschik bare knoop punten.

Houd er rekening mee dat voor het doel van deze waarschuwing de frequentie moet worden bepaald **.** Meer informatie over periode, frequentie en andere waarschuwings parameters vindt u [hier](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Klik op **gereed** wanneer u klaar bent met het configureren van de signaal logica.

![Waarschuwings regel signaal logica configureren](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Als u nog geen actie groep hebt, klikt u op **nieuwe maken** onder de sectie **actie groepen** .

![Waarschuwings regel nieuwe actie groep maken](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Hiermee opent u de Blade **actie groep toevoegen** . Kies een **naam voor de actie groep**, de **korte naam**, het **abonnement**en de **resource groep.** Kies in de sectie **acties** een **actie naam** en selecteer **e-mail/SMS/push/Voice** als **actie type.**

> [!NOTE]
> Er zijn verschillende andere acties die een waarschuwing kan activeren naast een E-mail/SMS/push/Voice, zoals een Azure-functie, LogicApp, webhook, ITSM en Automation-Runbook. [Meer informatie.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Hiermee opent u de Blade **e-mail/SMS/push/Voice** . Kies een **naam** voor de ontvanger, **Schakel** het vak **e-mail** in en typ een e-mail adres waarnaar de waarschuwing moet worden verzonden. Klik op **OK** op de Blade **e-mail/SMS/push/Voice** en klik vervolgens op de Blade **actie groep toevoegen** om het configureren van uw actie groep te volt ooien.

![Actie groep voor toevoegen van waarschuwings regel maken](media/hdinsight-cluster-availability/portal-add-action-group.png)

Nadat deze Blades zijn gesloten, ziet u dat uw actie groep wordt weer gegeven onder de sectie **actie groepen** . Vul tot slot de **sectie waarschuwings Details** in door een naam en **Beschrijving** voor de **waarschuwings regel** te typen en een **Ernst**te kiezen.
Klik op **waarschuwings regel maken** om te volt ooien.

![De waarschuwings regel voor het maken van de portal is voltooid](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> De mogelijkheid om **Ernst** op te geven is een krachtig hulp programma dat kan worden gebruikt bij het maken van meerdere waarschuwingen. U kunt bijvoorbeeld één waarschuwing maken om een waarschuwing te genereren (Ernst 1) als één hoofd knooppunt wordt weer gegeven en een andere waarschuwing die kritiek (Ernst 0) veroorzaakt in het onwaarschijnlijke geval dat beide hoofd knooppunten omlaag gaan.

Wanneer aan de voor waarde voor deze waarschuwing wordt voldaan, wordt de waarschuwing geactiveerd en ontvangt u een e-mail bericht met de details van de waarschuwing, zoals:

![Voor beeld van een e-mail bericht Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

U kunt ook alle waarschuwingen weer geven die zijn geactiveerd, gegroepeerd op Ernst, door naar de Blade **waarschuwingen** in uw **log Analytics-werk ruimte**te gaan.

![Waarschuwingen voor Log Analytics werk ruimte](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Door te klikken op een Ernst groepering (bijvoorbeeld **Ernst 1,** zoals hierboven gemarkeerd) worden records weer gegeven voor alle waarschuwingen van de ernst die als hieronder zijn geactiveerd:

![Log Analytics werk ruimte Ernst 1 waarschuwingen](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Volgende stappen
- [Beschik baarheid en betrouw baarheid van Apache Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md)
