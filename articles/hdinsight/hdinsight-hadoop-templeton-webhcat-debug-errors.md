---
title: WebHCat-fouten op HDInsight begrijpen en oplossen - Azure
description: Meer informatie over veelvoorkomende fouten die door WebHCat op HDInsight worden geretourneerd en hoe u deze oplossen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 011ef4f192bbae12be7d2464d5b0526f584821a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638847"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Fouten die vanuit WebHCat zijn ontvangen, begrijpen en oplossen op HDInsight

Meer informatie over fouten die zijn ontvangen bij het gebruik van WebHCat met HDInsight en hoe u deze oplossen. WebHCat wordt intern gebruikt door client-side tools zoals Azure PowerShell en de Data Lake Tools voor Visual Studio.

## <a name="what-is-webhcat"></a>Wat is WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) is een REST API voor [HCatalog,](https://cwiki.apache.org/confluence/display/Hive/HCatalog)een tabel en opslagbeheerlaag voor Apache Hadoop. WebHCat is standaard ingeschakeld op HDInsight-clusters en wordt door verschillende hulpprogramma's gebruikt om taken in te dienen, de status van de taak te krijgen, enzovoort, zonder in te loggen op het cluster.

## <a name="modifying-configuration"></a>Configuratie wijzigen

Verschillende van de fouten in dit document treden op omdat een geconfigureerd maximum is overschreden. Wanneer in de resolutiestap wordt vermeld dat u een waarde wijzigen, gebruikt u Apache Ambari (web- of REST API) om de waarde te wijzigen. Zie [HDInsight beheren met Apache Ambari voor](hdinsight-hadoop-manage-ambari.md) meer informatie

### <a name="default-configuration"></a>Standaardconfiguratie

Als de volgende standaardwaarden worden overschreden, kan dit de prestaties van WebHCat verslechteren of fouten veroorzaken:

| Instelling | Wat het doet | Standaardwaarde |
| --- | --- | --- |
| [garen.scheduler.capacity.maximumtoepassingen][maximum-applications] |Het maximum aantal taken dat gelijktijdig actief kan zijn (in behandeling of uitgevoerd) |10.000 |
| [templeton.exec.max-procs][max-procs] |Het maximum aantal aanvragen dat gelijktijdig kan worden ingediend |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |Het aantal dagen dat de functiegeschiedenis wordt behouden |7 dagen |

## <a name="too-many-requests"></a>Te veel aanvragen

**HTTP-statuscode**: 429

| Oorzaak | Oplossing |
| --- | --- |
| U hebt de maximale gelijktijdige aanvragen die door WebHCat per minuut worden geserveerd overschreden (standaard 20) |Verlaag uw werklast om ervoor te zorgen dat u niet meer dan het maximum `templeton.exec.max-procs`aantal gelijktijdige aanvragen indient of de limiet voor gelijktijdige aanvragen verhoogt door het wijzigen van . Zie [Configuratie wijzigen voor](#modifying-configuration) meer informatie |

## <a name="server-unavailable"></a>Server niet beschikbaar

**HTTP-statuscode**: 503

| Oorzaak | Oplossing |
| --- | --- |
| Deze statuscode treedt meestal op tijdens failover tussen de primaire en secundaire HeadNode voor het cluster |Wacht twee minuten en probeer de bewerking opnieuw |

## <a name="bad-request-content-could-not-find-job"></a>Slecht verzoek Inhoud: Kon geen baan vinden

**HTTP-statuscode**: 400

| Oorzaak | Oplossing |
| --- | --- |
| Werkdetails zijn opgeschoond door de jobgeschiedenisreiniger |De standaardbewaartermijn voor taakgeschiedenis is 7 dagen. De standaardbewaarperiode kan worden `mapreduce.jobhistory.max-age-ms`gewijzigd door het wijzigen van . Zie [Configuratie wijzigen voor](#modifying-configuration) meer informatie |
| Job is gedood als gevolg van een failover |Jobsubmission opnieuw proberen voor maximaal twee minuten |
| Er is een ongeldig taak-id gebruikt |Controleren of de taak-id correct is |

## <a name="bad-gateway"></a>Slechte gateway

**HTTP-statuscode**: 502

| Oorzaak | Oplossing |
| --- | --- |
| Interne afvalinzameling vindt plaats binnen het WebHCat-proces |Wachten tot het ophalen van afval is voltooid of start de WebHCat-service opnieuw |
| Time-out te wachten op een reactie van de ResourceManager-service. Deze fout kan optreden wanneer het aantal actieve toepassingen het geconfigureerde maximum wordt (standaard 10.000) |Wacht tot momenteel lopende taken de gelijktijdige taaklimiet `yarn.scheduler.capacity.maximum-applications`hebben voltooid of verhoogd door . Zie de sectie [Configuratie wijzigen](#modifying-configuration) voor meer informatie. |
| Proberen om alle taken op te `Fields` halen via de GET / [jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) call terwijl is ingesteld op`*` |Haal niet *alle* taakgegevens op. In `jobid` plaats daarvan gebruiken om details op te halen voor taken die alleen groter zijn dan bepaalde taak-ID. Of, gebruik niet`Fields` |
| De WebHCat-service is uitgeschakeld tijdens HeadNode failover |Wacht twee minuten en probeer de bewerking opnieuw |
| Er zijn meer dan 500 lopende vacatures ingediend via WebHCat |Wacht tot de lopende taken zijn voltooid voordat u meer taken indient |

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
