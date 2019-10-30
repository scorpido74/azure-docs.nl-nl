---
title: Overzicht van ENTER prise Security in azure HDInsight
description: Meer informatie over de verschillende methoden om ondernemings beveiliging in azure HDInsight te garanderen.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: overview
ms.date: 09/23/2019
ms.openlocfilehash: 7fadb17476c2a071de767573994bb1120b476cdf
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053741"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Overzicht van ENTER prise Security in azure HDInsight

Azure HDInsight biedt een aantal methoden voor het beantwoorden van uw Enter prise-beveiligings behoeften. De meeste van deze oplossingen zijn standaard niet geactiveerd. Met deze flexibiliteit kunt u de beveiligings functies kiezen die voor u het belangrijkst zijn, en kunt u voor komen dat u betaalt voor functies die u niet nodig hebt. Dit betekent ook dat het uw verantwoordelijkheid is om ervoor te zorgen dat de juiste oplossingen zijn ingeschakeld voor uw installatie en omgeving.

In dit artikel wordt gekeken naar beveiligings oplossingen door beveiligings oplossingen te delen langs de regels van vier traditionele veiligheids stijlen: perimeter beveiliging, verificatie, autorisatie en versleuteling.

In dit artikel wordt ook de **Azure HDInsight-Enterprise Security Package (ESP)** geïntroduceerd, die op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en op rollen gebaseerd toegangs beheer voor HDInsight-clusters voorziet.

## <a name="enterprise-security-pillars"></a>Enter prise Security-pijlers

Een van de manieren om bedrijfs beveiliging te bekijken, verdeelt beveiligings oplossingen in vier hoofd groepen op basis van het type besturings element. Deze groepen worden ook wel Security-pijlers genoemd. Dit zijn de volgende: perimeter beveiliging, authenticatie, autorisatie en versleuteling.

### <a name="perimeter-security"></a>Perimeter beveiliging

De beveiliging van een perimeter netwerk in HDInsight wordt bereikt via [virtuele netwerken](../hdinsight-plan-virtual-network-deployment.md). Een Enter prise-beheerder kan een cluster maken in een virtueel netwerk (VNET) en netwerk beveiligings groepen (NSG) gebruiken om de toegang tot het virtuele netwerk te beperken. Alleen de toegestane IP-adressen in de regels voor binnenkomende NSG kunnen communiceren met het HDInsight-cluster. Deze configuratie biedt een perimeter beveiliging.

Alle clusters die zijn geïmplementeerd in een VNET, hebben ook een persoonlijk eind punt dat wordt omgezet in een privé-IP-adres in het VNET voor privé-HTTP-toegang tot de cluster gateways.

### <a name="authentication"></a>Verificatie

De [Enterprise Security Package](apache-domain-joined-architecture.md) van HDInsight biedt op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en toegangs beheer op basis van rollen. De Active Directory integratie wordt bereikt door het gebruik van [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Met deze mogelijkheden kunt u een HDInsight-cluster maken dat is gekoppeld aan een beheerd Active Directory domein. U kunt vervolgens een lijst met werk nemers configureren van de onderneming die zich kan verifiëren en aanmelden bij het cluster.

Met deze instelling kunnen werk nemers van een onderneming zich met hun domein referenties aanmelden bij de cluster knooppunten. Ze kunnen ook hun domein referenties gebruiken om te verifiëren met andere goedgekeurde eind punten, zoals Apache Ambari views, ODBC, JDBC, Power shell en REST Api's om met het cluster te communiceren. 

### <a name="authorization"></a>Autorisatie

Een best practice dat de meeste ondernemingen volgen, zorgt ervoor dat niet elke werk nemer toegang heeft tot alle bedrijfs resources. De beheerder kan ook op rollen gebaseerd toegangs beheer beleid definiëren voor de cluster resources. Dit is alleen beschikbaar in de ESP-clusters.

De Hadoop-beheerder kan op rollen gebaseerd toegangs beheer (RBAC) configureren voor het beveiligen van Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md) en [Kafka](apache-domain-joined-run-kafka.md) met behulp van deze invoeg toepassingen in Apache zwerver. Met het configureren van het RBAC-beleid kunt u machtigingen koppelen aan een rol in de organisatie. Deze laag van abstractie maakt het gemakkelijker om ervoor te zorgen dat personen alleen de machtigingen hebben die nodig zijn om hun werk verantwoordelijkheden uit te voeren. Daarnaast kunt u met zwerver de gegevens toegang van werk nemers en alle wijzigingen die worden uitgevoerd voor toegangs beheer beleid controleren.

De beheerder kan bijvoorbeeld [Apache Ranger](https://ranger.apache.org/) configureren om toegangscontrolebeleid in te stellen voor Hive. Deze functionaliteit zorgt ervoor dat filtering op rijniveau en op kolom niveau (gegevens maskering) wordt toegepast en de gevoelige gegevens van niet-geautoriseerde gebruikers worden gefilterd.

### <a name="auditing"></a>Controle

Controle van alle toegang tot de cluster bronnen en de gegevens is nodig voor het bijhouden van ongeoorloofde of onbedoelde toegang tot de resources. Het is net zo belang rijk als het beveiligen van de HDInsight-cluster resources van niet-geautoriseerde gebruikers en het beveiligen van de gegevens.

De beheerder kan alle toegang tot de bronnen en gegevens van het HDInsight-cluster weer geven en rapporteren. De beheerder kan ook alle wijzigingen bekijken en rapporteren voor het toegangs beheer beleid dat is gemaakt in Apache zwerver ondersteunde eind punten. 

Om toegang te krijgen tot Apache zwerver en Ambari-audit logboeken en de logboeken voor SSH-toegang, [schakelt u Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) in en bekijkt u de tabellen die controle records bieden.

### <a name="encryption"></a>Versleuteling

Het beveiligen van gegevens is belang rijk voor het voldoen aan de beveiligings-en nalevings vereisten van de organisatie. Naast het beperken van de toegang tot gegevens van onbevoegde werk nemers, moet u deze versleutelen.

Zowel gegevens archieven voor HDInsight-clusters, Azure Blob-opslag als Azure Data Lake Storage Gen1-Gen2, ondersteunen transparante server-side [versleuteling van gegevens](../../storage/common/storage-service-encryption.md) in rust. Beveiligde HDInsight-clusters werken probleemloos met deze mogelijkheid voor het versleutelen van gegevens in de rest van de server.

### <a name="compliance"></a>Naleving

Nalevings aanbiedingen voor Azure zijn gebaseerd op verschillende soorten garanties, waaronder formele certificeringen, attesten, validaties, autorisaties en beoordelingen die worden geproduceerd door onafhankelijke controle bedrijven van derden, evenals contractuele wijzigingen; zelf beoordelingen en documentatie voor klant richtlijnen die door micro soft zijn gemaakt. Zie het [vertrouwens centrum van micro soft](https://www.microsoft.com/trust-center) en het [overzicht van Microsoft Azure naleving](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)voor informatie over HDInsight-compatibiliteit.

## <a name="shared-responsibility-model"></a>Model van gedeelde verantwoordelijkheid

De volgende afbeelding bevat een overzicht van de belangrijkste systeem beveiligings gebieden en de beveiligings oplossingen die voor u beschikbaar zijn. Er wordt ook uitgelegd welke beveiligings gebieden uw verantwoordelijkheid zijn als klant en welke gebieden de verantwoordelijkheid zijn van HDInsight als de service provider.

![Diagram gedeelde HDInsight-verantwoordelijkheden](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

De volgende tabel bevat koppelingen naar bronnen voor elk type beveiligings oplossing.

| Beveiligings gebied | Beschik bare oplossingen | Verantwoordelijke partij |
|---|---|---|
| Beveiliging van gegevens toegang | [Toegangs beheer lijsten configureren acl's](../../storage/blobs/data-lake-storage-access-control.md) voor Azure data Lake Storage gen1 en Gen2  | Klant |
|  | Schakel de eigenschap [beveiligde overdracht vereist](../../storage/common/storage-require-secure-transfer.md) in voor opslag accounts. | Klant |
|  | [Azure Storage firewalls](../../storage/common/storage-network-security.md) en virtuele netwerken configureren | Klant |
|  | [Azure Virtual Network-Service-eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) configureren voor Cosmos DB en [Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Klant |
|  | Zorg ervoor dat [TLS-versleuteling](../../storage/common/storage-security-tls.md) is ingeschakeld voor gegevens die onderweg zijn. | Klant |
|  | Door de [klant beheerde sleutels](../../storage/common/storage-encryption-keys-portal.md) voor Azure Storage versleuteling configureren | Klant |
| Beveiliging van toepassingen en middleware | Integreren met AAD-DS en [verificatie configureren](apache-domain-joined-configure-using-azure-adds.md) | Klant |
|  | Een [Apache zwerver-autorisatie](apache-domain-joined-run-hive.md) beleid configureren | Klant |
|  | [Azure monitor-logboeken](../hdinsight-hadoop-oms-log-analytics-tutorial.md) gebruiken | Klant |
| Beveiliging van het besturings systeem | Clusters met de meest recente beveiligde basis installatie kopie maken | Klant |
|  | [Patching van het besturings systeem](../hdinsight-os-patching.md) controleren op regel matige intervallen | Klant |
| Netwerkbeveiliging | Een [virtueel netwerk](../hdinsight-plan-virtual-network-deployment.md) configureren |
|  | [Regels voor binnenkomende netwerk beveiligings groepen (NSG)](../hdinsight-plan-virtual-network-deployment.md#networktraffic) configureren | Klant |
|  | [Beperking van uitgaand verkeer](../hdinsight-restrict-outbound-traffic.md) configureren met Firewall (preview-versie) | Klant |
| Gevirtualiseerde infra structuur | N/A | HDInsight (Cloud provider) |
| Fysieke infrastructuur beveiliging | N/A | HDInsight (Cloud provider) |

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters plannen met ESP](apache-domain-joined-architecture.md)
* [HDInsight-clusters configureren met ESP](apache-domain-joined-configure.md)
* [HDInsight-clusters beheren met ESP](apache-domain-joined-manage.md)
