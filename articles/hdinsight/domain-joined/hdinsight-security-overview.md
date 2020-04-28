---
title: Overzicht van ENTER prise Security in azure HDInsight
description: Meer informatie over de verschillende methoden om ondernemings beveiliging in azure HDInsight te garanderen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 492c248b9a68b39b37984b978944b4f7ef34d756
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190109"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Overzicht van ENTER prise Security in azure HDInsight

Azure HDInsight biedt een aantal methoden voor het beantwoorden van uw Enter prise-beveiligings behoeften. De meeste van deze oplossingen zijn standaard niet geactiveerd. Met deze flexibiliteit kunt u de beveiligings functies kiezen die voor u het belangrijkst zijn. En helpt u te voor komen dat u betaalt voor functies die u niet nodig hebt. Deze flexibiliteit betekent ook dat het uw verantwoordelijkheid is om ervoor te zorgen dat de juiste oplossingen zijn ingeschakeld voor uw installatie en omgeving.

In dit artikel wordt gekeken naar beveiligings oplossingen door beveiligings oplossingen te delen in vier traditionele veiligheids stijlen: perimeter beveiliging, verificatie, autorisatie en versleuteling.

In dit artikel wordt ook de **Azure HDInsight-Enterprise Security Package (ESP)** geïntroduceerd, die op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en op rollen gebaseerd toegangs beheer voor HDInsight-clusters voorziet.

## <a name="enterprise-security-pillars"></a>Enter prise Security-pijlers

Een van de manieren om bedrijfs beveiliging te bekijken, verdeelt beveiligings oplossingen in vier hoofd groepen op basis van het type besturings element. Deze groepen worden ook wel Security-pijlers genoemd en zijn de volgende typen: perimeter beveiliging, authenticatie, autorisatie en versleuteling.

### <a name="perimeter-security"></a>Perimeter beveiliging

De beveiliging van een perimeter netwerk in HDInsight wordt bereikt via [virtuele netwerken](../hdinsight-plan-virtual-network-deployment.md). Een Enter prise-beheerder kan een cluster maken in een virtueel netwerk (VNET) en netwerk beveiligings groepen (NSG) gebruiken om de toegang tot het virtuele netwerk te beperken. Alleen de toegestane IP-adressen in de regels voor binnenkomende NSG kunnen communiceren met het HDInsight-cluster. Deze configuratie biedt een perimeter beveiliging.

Alle clusters die zijn geïmplementeerd in een VNET, hebben ook een persoonlijk eind punt. Het eind punt wordt omgezet in een privé-IP-adres in het VNET voor privé-HTTP-toegang tot de cluster gateways.

### <a name="authentication"></a>Verificatie

[Enterprise Security Package](apache-domain-joined-architecture.md) van HDInsight voorziet in op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en toegangs beheer op basis van rollen. De Active Directory integratie wordt bereikt door het gebruik van [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Met deze mogelijkheden kunt u een HDInsight-cluster maken dat is gekoppeld aan een Active Directory domein. Configureer vervolgens een lijst met werk nemers van de onderneming die bij het cluster kan worden geverifieerd.

Met deze instelling kunnen werk nemers van een onderneming zich met hun domein referenties aanmelden bij de cluster knooppunten. Ze kunnen ook hun domein referenties gebruiken om te verifiëren met andere goedgekeurde eind punten. Zoals Apache Ambari views, ODBC, JDBC, Power shell en REST Api's voor interactie met het cluster.

### <a name="authorization"></a>Autorisatie

Een best practice de meeste ondernemingen volgen, zorgt ervoor dat niet elke werk nemer volledige toegang tot alle bedrijfs resources heeft. De beheerder kan ook op rollen gebaseerd toegangs beheer beleid definiëren voor de cluster resources. Deze actie is alleen beschikbaar in de ESP-clusters.

De Hadoop-beheerder kan op rollen gebaseerd toegangs beheer (RBAC) configureren. De configuraties Secure Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)en [Kafka](apache-domain-joined-run-kafka.md) met Apache Range-invoeg toepassingen. Met het configureren van het RBAC-beleid kunt u machtigingen koppelen aan een rol in de organisatie. Deze laag van abstractie maakt het gemakkelijker om ervoor te zorgen dat personen alleen de machtigingen hebben die nodig zijn om hun werk verantwoordelijkheden uit te voeren. Daarnaast kunt u met zwerver de gegevens toegang van werk nemers en alle wijzigingen die worden uitgevoerd voor toegangs beheer beleid controleren.

De beheerder kan bijvoorbeeld [Apache Ranger](https://ranger.apache.org/) configureren om toegangscontrolebeleid in te stellen voor Hive. Deze functie zorgt voor filteren op rijniveau en op kolom niveau (gegevens maskering). En worden de gevoelige gegevens van niet-geautoriseerde gebruikers gefilterd.

### <a name="auditing"></a>Controleren

Het controleren van de toegang tot cluster bronnen is nodig voor het bijhouden van onbevoegde of onbedoelde toegang tot de resources. Het is even belang rijk dat u de cluster bronnen beveiligt tegen onbevoegde toegang.

De beheerder kan alle toegang tot de bronnen en gegevens van het HDInsight-cluster weer geven en rapporteren. De beheerder kan wijzigingen in het toegangs beheer beleid bekijken en rapporteren.

Om toegang te krijgen tot Apache zwerver en Ambari-controle logboeken en SSH-toegangs logboeken, [schakelt u Azure monitor in](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing). En de tabellen weer geven die controle records bieden.

### <a name="encryption"></a>Versleuteling

Het beveiligen van gegevens is belang rijk voor het voldoen aan de beveiligings-en nalevings vereisten van de organisatie. Naast het beperken van de toegang tot gegevens van onbevoegde werk nemers, moet u deze versleutelen.

Azure Storage en Data Lake Storage Gen1/Gen2, ondersteunen transparante versleuteling aan de server zijde [van gegevens](../../storage/common/storage-service-encryption.md) in rust. Beveiligde HDInsight-clusters werken naadloos met server versleuteling van gegevens in rust.

### <a name="compliance"></a>Naleving

Nalevings aanbiedingen voor Azure zijn gebaseerd op verschillende soorten garanties, waaronder formele certificeringen. Ook verklaringen, validaties en autorisaties. Evaluaties die door onafhankelijke audit bedrijven van derden worden geproduceerd. Contractuele wijzigingen, zelf evaluaties en klant guidance-documenten die door micro soft worden geproduceerd. Zie het [vertrouwens centrum van micro soft](https://www.microsoft.com/trust-center) en het [overzicht van Microsoft Azure naleving](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)voor informatie over HDInsight-compatibiliteit.

## <a name="shared-responsibility-model"></a>Model met gedeelde verantwoordelijkheid

De volgende afbeelding bevat een overzicht van de belangrijkste systeem beveiligings gebieden en de beveiligings oplossingen die voor u beschikbaar zijn. Het markeert ook welke beveiligings gebieden uw verantwoordelijkheid zijn als een klant. En welke gebieden zijn de verantwoordelijkheid van HDInsight als de service provider.

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
|  | [Beperking van uitgaand verkeer](../hdinsight-restrict-outbound-traffic.md) configureren met firewall | Klant |
| Gevirtualiseerde infra structuur | N.v.t. | HDInsight (Cloud provider) |
| Fysieke infrastructuur beveiliging | N.v.t. | HDInsight (Cloud provider) |

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters plannen met ESP](apache-domain-joined-architecture.md)
* [HDInsight-clusters configureren met ESP](apache-domain-joined-configure.md)
* [HDInsight-clusters beheren met ESP](apache-domain-joined-manage.md)
