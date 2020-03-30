---
title: Uitzondering bij het uitvoeren van query's van Apache Ambari Hive View in Azure HDInsight
description: Problemen oplossen bij het uitvoeren van Apache Hive-query's via apache Ambari Hive View in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895036"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Uitzondering bij het uitvoeren van query's van Apache Ambari Hive View in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u een Apache Hive-query uitvoert vanuit de Apache Ambari Hive View, ontvangt u met tussenpozen het volgende foutbericht:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Oorzaak

Een gateway-time-out.

De time-outwaarde gateway is 2 minuten. Query's van Ambari Hive `/hive2` View worden via de gateway naar het eindpunt verzonden. Zodra de query is gecompileerd en `queryid`geaccepteerd, retourneert de HiveServer een . Clients houden vervolgens de polling voor de status van de query. Als de HiveServer tijdens dit proces niet binnen 2 minuten een HTTP-antwoord retourneert, gooit de HDI-gateway een time-outfout van 502.3 Gateway naar de beller. De fouten kunnen optreden wanneer de query wordt ingediend voor verwerking (meer waarschijnlijk) en ook in de oproep status krijgen (minder waarschijnlijk). Gebruikers konden een van hen zien.

De http handler thread wordt verondersteld om snel `queryid`te zijn: de voorbereiding van de baan en terug te keren een . Echter, als gevolg van verschillende redenen, alle handler threads kunnen worden druk resulterend in time-outs voor nieuwe query's en de get status calls.

### <a name="responsibilities-of-the-http-handler-thread"></a>Verantwoordelijkheden van de HTTP-handlerthread

Wanneer de client een query indient bij HiveServer, wordt het volgende in de voorgrondthread weergegeven:

* Ontken het verzoek, doe semantische verificatie
* Verkrijgen slot
* Metastore lookup indien nodig
* De query compileren (DDL of DML)
* Een queryplan opstellen
* Autorisatie uitvoeren (Alle toepasselijke rangerbeleidsregels uitvoeren in beveiligde clusters)

## <a name="resolution"></a>Oplossing

Enkele algemene aanbevelingen aan u om de situatie te verbeteren:

* Als u een externe hive-metastore gebruikt, controleert u de DB-statistieken en controleert u of de database niet overbelast is. Overweeg de metastore-databaselaag te schalen.

* Zorg ervoor dat parallelle ops is ingeschakeld (hierdoor kunnen de HTTP-handlerthreads parallel worden uitgevoerd). Als u de waarde wilt verifiëren, start u [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) en navigeert u naar **Hive** > **Configs** > **Advanced** > **Custom hive-site.** De waarde `hive.server2.parallel.ops.in.session` voor `true`moet zijn .

* Controleer of de VM SKU van het cluster niet te klein is voor de belasting. Overweeg om het werk te splitsen tussen meerdere clusters. Zie [Een clustertype kiezen](../hdinsight-capacity-planning.md#choose-a-cluster-type)voor meer informatie .

* Als Ranger op het cluster is geïnstalleerd, controleert u of er te veel Ranger-beleidsregels zijn die voor elke query moeten worden geëvalueerd. Zoek naar duplicaat of onnodig beleid.

* Controleer de **heapgroottewaarde van HiveServer2 van** Ambari. Navigeer naar **Optimalisatie** > van de**instellingen** > van Hive**Configs** > .**Optimization** Zorg ervoor dat de waarde groter is dan 10 GB. Pas aan waar nodig om de prestaties te optimaliseren.

* Zorg ervoor dat de Hive-query goed is afgestemd. Zie [Apache Hive-query's optimaliseren in Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
