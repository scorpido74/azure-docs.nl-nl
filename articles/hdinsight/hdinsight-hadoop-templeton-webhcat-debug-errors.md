---
title: WebHCat-fouten in HDInsight-Azure begrijpen en oplossen
description: Meer informatie over veelvoorkomende fouten die worden geretourneerd door WebHCat in HDInsight en hoe u deze kunt oplossen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 5c103482771b829730d009d65283a54ec1d8eb8a
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555017"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Fouten die zijn ontvangen van WebHCat in HDInsight begrijpen en oplossen

Meer informatie over fouten die zijn ontvangen bij het gebruik van WebHCat met HDInsight en hoe u deze kunt oplossen. WebHCat wordt intern gebruikt door Program ma's aan de client zijde zoals Azure PowerShell en de Data Lake-Hulpprogram Ma's voor Visual Studio.

## <a name="what-is-webhcat"></a>Wat is WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) is een rest API voor [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), een tabel en een opslag beheer-laag voor Apache Hadoop. WebHCat is standaard ingeschakeld op HDInsight-clusters en wordt door diverse hulpprogram ma's gebruikt om taken te verzenden, de taak status op te halen, enzovoort zonder dat u zich hoeft aan te melden bij het cluster.

## <a name="modifying-configuration"></a>Configuratie wijzigen

> [!IMPORTANT]  
> Verschillende van de fouten die in dit document worden vermeld, treden op omdat een geconfigureerd maximum is overschreden. Wanneer de oplossings stap vermeldt dat u een waarde kunt wijzigen, moet u een van de volgende opties gebruiken om de wijziging uit te voeren:

* Voor **Windows** -clusters: gebruik een script actie om de waarde te configureren tijdens het maken van het cluster. Zie [script acties ontwikkelen](hdinsight-hadoop-script-actions-linux.md)voor meer informatie.

* Voor **Linux** -clusters: gebruik Apache Ambari (web of rest API) om de waarde te wijzigen. Zie [HDInsight beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md) voor meer informatie.


### <a name="default-configuration"></a>Standaard configuratie

Als de volgende standaard waarden worden overschreden, kan dit de prestaties van de WebHCat verminderen of fouten veroorzaken:

| Instelling | Wat het doet | Standaardwaarde |
| --- | --- | --- |
| [garens. scheduler. capacity. maximum-toepassingen][maximum-applications] |Het maximum aantal taken dat gelijktijdig actief kan zijn (in behandeling of uitvoering) |10.000 |
| [Templeton. exec. Max-procs][max-procs] |Het maximum aantal aanvragen dat gelijktijdig kan worden verwerkt |20 |
| [MapReduce. jobhistory. max-age-MS][max-age-ms] |Het aantal dagen dat de taak geschiedenis wordt bewaard |7 dagen |

## <a name="too-many-requests"></a>Te veel aanvragen

**HTTP-status code**: 429

| Oorzaak | Resolutie |
| --- | --- |
| U hebt het maximum aantal gelijktijdige aanvragen dat wordt aangeboden door WebHCat per minuut overschreden (standaard 20) |Verminder uw werk belasting om ervoor te zorgen dat u niet meer dan het maximum aantal gelijktijdige aanvragen indient of de limiet voor gelijktijdige aanvragen verhoogt door `templeton.exec.max-procs`te wijzigen. Zie Modify [Configuration (configuratie wijzigen](#modifying-configuration) ) voor meer informatie. |

## <a name="server-unavailable"></a>De server is niet beschikbaar

**HTTP-status code**: 503

| Oorzaak | Resolutie |
| --- | --- |
| Deze status code treedt meestal op tijdens een failover tussen de primaire en secundaire hoofd knooppunt voor het cluster |Wacht twee minuten en voer de bewerking opnieuw uit |

## <a name="bad-request-content-could-not-find-job"></a>Inhoud van ongeldige aanvraag: de taak is niet gevonden

**HTTP-status code**: 400

| Oorzaak | Resolutie |
| --- | --- |
| De taak Details zijn opgeschoond door de taak geschiedenis opschoning |De standaard Bewaar periode voor de taak geschiedenis is 7 dagen. De standaard Bewaar periode kan worden gewijzigd door `mapreduce.jobhistory.max-age-ms`aan te passen. Zie Modify [Configuration (configuratie wijzigen](#modifying-configuration) ) voor meer informatie. |
| De taak is beëindigd vanwege een failover |Taak opnieuw verzenden gedurende Maxi maal twee minuten |
| Er is een ongeldige taak-ID gebruikt |Controleren of de taak-ID juist is |

## <a name="bad-gateway"></a>Ongeldige gateway

**HTTP-status code**: 502

| Oorzaak | Resolutie |
| --- | --- |
| Interne garbagecollection wordt uitgevoerd in het WebHCat-proces |Wacht tot de garbagecollection is voltooid of start de WebHCat-service opnieuw |
| Time-out tijdens het wachten op een reactie van de Resource Manager-service. Deze fout kan optreden wanneer het aantal actieve toepassingen het geconfigureerde maximum overschrijdt (standaard 10.000) |Wacht totdat de taken die momenteel worden uitgevoerd, zijn voltooid of verg root de limiet van de gelijktijdige taken door `yarn.scheduler.capacity.maximum-applications`te wijzigen. Zie de sectie [configuratie wijzigen](#modifying-configuration) voor meer informatie. |
| Er wordt geprobeerd om alle taken op te halen via de aanroep [Get/Jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) terwijl `Fields` is ingesteld op `*` |Niet *alle* taak Details ophalen. Gebruik in plaats daarvan `jobid` om details op te halen voor taken die groter zijn dan een bepaalde taak-ID. Of gebruik `Fields` niet |
| De WebHCat-service is niet beschikbaar tijdens de hoofd knooppunt-failover |Wacht twee minuten en voer de bewerking opnieuw uit |
| Er zijn meer dan 500 taken die moeten worden verzonden via WebHCat |Wachten tot de momenteel wachtende taken zijn voltooid voordat u meer taken hebt verzonden |

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
