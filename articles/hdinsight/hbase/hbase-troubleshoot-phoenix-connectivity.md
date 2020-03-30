---
title: Problemen met Apache Phoenix-connectiviteit in Azure HDInsight
description: Verbindingsproblemen tussen Apache HBase en Apache Phoenix in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887288"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scenario: Problemen met de Apache Phoenix-connectiviteit in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Niet in staat om verbinding te maken met Apache HBase met Apache Phoenix. Redenen kunnen variëren.

## <a name="cause-incorrect-ip"></a>Oorzaak: onjuist IP

Onjuist IP van actief Zookeeper-knooppunt.

### <a name="resolution"></a>Oplossing

Het IP van het actieve Zookeeper-knooppunt kan worden geïdentificeerd vanuit de Ambari-gebruikersinterface door de links naar **HBase** > **Quick Links** > **ZK (Active)** > **Zookeeper Info**te volgen. Corrigeer het IP-adres indien nodig.

---

## <a name="cause-systemcatalog-table-offline"></a>Oorzaak: SYSTEEM. CATALOGUStabel offline

Wanneer u opdrachten `!tables`uitvoert, zoals , ontvangt u een foutbericht dat vergelijkbaar is met:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Wanneer u opdrachten `count 'SYSTEM.CATALOG'`uitvoert, zoals , ontvangt u een foutbericht dat vergelijkbaar is met:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Oplossing

Voer vanuit de Apache Ambari-gebruikersinterface de volgende stappen uit om de HMaster-service opnieuw te starten op alle ZooKeeper-knooppunten:

1. Ga **in** het gedeelte Samenvatting van HBase naar **HBase** > **Active HBase Master**.

1. Start in de sectie **Componenten** de HBase Master-service opnieuw.

1. Herhaal deze stappen voor alle resterende **Standby HBase** Master-services.

Het kan tot vijf minuten duren voordat de HBase Master-service het herstel stabiliseert en voltooit. Nadat `SYSTEM.CATALOG` de tabel weer normaal is, moet het verbindingsprobleem met Apache Phoenix automatisch worden opgelost.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
