---
title: Opmerkingen bij de release van Azure HDInsight
description: Laatste release notes voor Azure HDInsight. Ontvang ontwikkeltips en details voor Hadoop, Spark, R Server, Hive en meer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e5a96d2eb67937ce4eeaa1999d8168e7f5d3d926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130195"
---
# <a name="release-notes"></a>Releaseopmerkingen

In dit artikel vindt u informatie over de **meest recente** Azure HDInsight-releaseupdates. Zie [HDInsight Release Notes Archive](hdinsight-release-notes-archive.md)voor informatie over eerdere releases.

## <a name="summary"></a>Samenvatting

Azure HDInsight is een van de meest populaire services onder zakelijke klanten voor open-source analyses op Azure.

## <a name="release-date-01092020"></a>Releasedatum: 01/09/2020

Deze release geldt zowel voor HDInsight 3.6 als 4.0. HDInsight release is beschikbaar gesteld voor alle regio's gedurende meerdere dagen. De releasedatum geeft hier de eerste releasedatum van de regio aan. Als je onderstaande wijzigingen niet ziet, wacht je tot de release binnen enkele dagen in je regio wordt uitgevoerd.

> [!IMPORTANT]  
> Linux is het enige besturingssysteem dat wordt gebruikt in HDInsight-versie 3.4 of hoger. Zie [HDInsight-versieartikel](hdinsight-component-versioning.md)voor meer informatie.

## <a name="new-features"></a>Nieuwe functies
### <a name="tls-12-enforcement"></a>TLS 1.2 afdwingen
Tls (Transport Layer Security) en Secure Sockets Layer (SSL) zijn cryptografische protocollen die communicatiebeveiliging bieden via een computernetwerk. Meer informatie over [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight gebruikt TLS 1.2 op openbare HTTPs-eindpunten, maar TLS 1.1 wordt nog steeds ondersteund voor achterwaartse compatibiliteit. 

Met deze release kunnen klanten alleen kiezen voor TLS 1.2 voor alle verbindingen via het eindpunt van het openbare cluster. Om dit te ondersteunen, wordt de nieuwe eigenschap **minSupportedTlsVersion** geïntroduceerd en kan worden opgegeven tijdens het maken van het cluster. Als de eigenschap niet is ingesteld, ondersteunt het cluster nog steeds TLS 1.0, 1.1 en 1.2, wat hetzelfde is als het gedrag van vandaag. Klanten kunnen de waarde voor deze eigenschap instellen op '1,2', wat betekent dat het cluster alleen TLS 1.2 en hoger ondersteunt. Zie [Een virtueel netwerk plannen voor](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security)meer informatie - Transport Layer Security .

### <a name="bring-your-own-key-for-disk-encryption"></a>Neem uw eigen sleutel mee voor schijfversleuteling
Alle beheerde schijven in HDInsight zijn beveiligd met Azure Storage Service Encryption (SSE). Gegevens op deze schijven worden standaard versleuteld door door Microsoft beheerde sleutels. Vanaf deze release u byok (Bring Your Own Key) voor schijfversleuteling gebruiken met Azure Key Vault. BYOK-versleuteling is een configuratie in één stap tijdens het maken van het cluster zonder extra kosten. Registreer HDInsight als een beheerde identiteit met Azure Key Vault en voeg de versleutelingssleutel toe wanneer u uw cluster maakt. Zie [Door de klant beheerde sleutelschijfversleuteling](https://docs.microsoft.com/azure/hdinsight/disk-encryption)voor meer informatie.

## <a name="deprecation"></a>Afschaffing
Geen afschrijvingen voor deze release. Zie [Aankomende wijzigingen](#upcoming-changes)om u klaar te maken voor komende afschrijvingen.

## <a name="behavior-changes"></a>Gedragswijzigingen
Geen gedragswijzigingen voor deze release. Zie [Aankomende wijzigingen](#upcoming-changes)om u klaar te maken voor komende wijzigingen.

## <a name="upcoming-changes"></a>Aankomende wijzigingen
De volgende wijzigingen zullen plaatsvinden in de komende releases. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Een minimale 4-core VM is vereist voor Head Node 
Voor Head Node is minimaal 4-core VM vereist om de hoge beschikbaarheid en betrouwbaarheid van HDInsight-clusters te garanderen. Vanaf 6 april 2020 kunnen klanten alleen 4-core of boven VM kiezen als Head Node voor de nieuwe HDInsight clusters. Bestaande clusters blijven zoals verwacht. 

### <a name="esp-spark-cluster-node-size-change"></a>Grootte van ESP Spark-clusterknooppunt wijzigen 
In de komende release wordt de minimaal toegestane knooppuntgrootte voor esp spark-cluster gewijzigd in Standard_D13_V2. Vm's uit de A-serie kunnen ESP-clusterproblemen veroorzaken vanwege de relatief lage CPU- en geheugencapaciteit. Vm's uit de A-serie worden afgeschaft voor het maken van nieuwe ESP-clusters.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Overstappen op azure-set's voor virtuele machineschalen
HDInsight gebruikt nu virtuele Azure-machines om het cluster in te richten. In de komende release gebruikt HDInsight in plaats daarvan Azure-sets voor virtuele machineschalen. Meer informatie over azure-set's voor virtuele machines.

### <a name="hbase-20-to-21"></a>HBase 2.0 tot 2.1
In de komende HDInsight 4.0-versie wordt de HBase-versie geüpgraded van versie 2.0 naar 2.1.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouwbaarheid van het cluster en de prestaties verbeteren. 

## <a name="component-version-change"></a>Wijziging van de componentversie
Geen componentversie wijziging voor deze release. U de huidige componentversies voor HDInsight 4.0 ad HDInsight 3.6 hier vinden.

## <a name="known-issues"></a>Bekende problemen

Vanaf 18 maart 2020 hebben sommige Azure HDInsight-klanten in West-Europa of Noord-Europa foutmeldingen ontvangen bij het maken of schalen van HDInsight-clusters in deze regio's. Fouten in verband met dit probleem zijn onder andere:

- Interne serverfout is opgetreden tijdens het verwerken van de aanvraag. Probeer het verzoek opnieuw of neem contact op met de ondersteuning.
- Ten minste één bewerking voor de implementatie van resources is mislukt. Vermeld implementatiebewerkingen voor meer informatie. Zie https://aka.ms/DeployOperations voor gebruiksgegevens
- User SubscriptionId\<'\>Subscription ID ' heeft geen\<cores meer om de clusternaam van de bron> te maken. Vereist: \<\>X , Beschikbaar: 0.

Ingenieurs zijn zich bewust van dit probleem en zijn actief onderzoeken.

Maak voor extra hulp een [ondersteuningsverzoek](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
