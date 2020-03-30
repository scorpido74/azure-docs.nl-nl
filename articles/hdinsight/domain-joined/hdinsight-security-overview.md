---
title: Overzicht van bedrijfsbeveiliging in Azure HDInsight
description: Meer informatie over de verschillende methoden om bedrijfsbeveiliging te garanderen in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.date: 03/03/2020
ms.openlocfilehash: 95bfe7d7788133d8548598cb30c8084bf64a977f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78267708"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Overzicht van bedrijfsbeveiliging in Azure HDInsight

Azure HDInsight biedt een aantal methoden om aan uw bedrijfsbeveiligingsbehoeften te voldoen. De meeste van deze oplossingen worden niet standaard geactiveerd. Met deze flexibiliteit u de beveiligingsfuncties kiezen die voor u het belangrijkst zijn en u voorkomen dat u betaalt voor functies die u niet wilt. Dit betekent ook dat het uw verantwoordelijkheid is om ervoor te zorgen dat de juiste oplossingen zijn ingeschakeld voor uw setup en omgeving.

In dit artikel wordt gekeken naar beveiligingsoplossingen door beveiligingsoplossingen te verdelen in de lijn van vier traditionele beveiligingspijlers: perimeterbeveiliging, verificatie, autorisatie en versleuteling.

In dit artikel wordt ook het **Azure HDInsight Enterprise Security Package (ESP)** geïntroduceerd, dat active directory-gebaseerde verificatie, ondersteuning voor meerdere gebruikers en op rollen gebaseerde toegangscontrole voor HDInsight-clusters biedt.

## <a name="enterprise-security-pillars"></a>Pijlers voor bedrijfsbeveiliging

Een manier om te kijken naar enterprise security verdeelt beveiligingsoplossingen in vier hoofdgroepen op basis van het type controle. Deze groepen worden ook wel beveiligingspijlers genoemd en zijn de volgende: perimeterbeveiliging, verificatie, autorisatie en versleuteling.

### <a name="perimeter-security"></a>Perimeterbeveiliging

Perimeterbeveiliging in HDInsight wordt bereikt via [virtuele netwerken.](../hdinsight-plan-virtual-network-deployment.md) Een bedrijfsbeheerder kan een cluster binnen een virtueel netwerk (VNET) maken en netwerkbeveiligingsgroepen (NSG) gebruiken om de toegang tot het virtuele netwerk te beperken. Alleen de toegestane IP-adressen in de binnenkomende NSG-regels kunnen communiceren met het HDInsight-cluster. Deze configuratie biedt perimeterbeveiliging.

Alle clusters die in een VNET worden geïmplementeerd, hebben ook een privéeindpunt dat wordt opgelost voor een privé-IP in het VNET voor privé-HTTP-toegang tot de clustergateways.

### <a name="authentication"></a>Authentication

Het [Enterprise Security Package](apache-domain-joined-architecture.md) van HDInsight biedt Active Directory-gebaseerde verificatie, ondersteuning voor meerdere gebruikers en op rollen gebaseerd toegangsbeheer. De Active Directory-integratie wordt bereikt door het gebruik van [Azure Active Directory Domain Services.](../../active-directory-domain-services/overview.md) Met deze mogelijkheden u een HDInsight-cluster maken dat is gekoppeld aan een beheerd Active Directory-domein. Vervolgens u een lijst configureren met werknemers uit de onderneming die zich kunnen verifiëren en zich kunnen aanmelden bij het cluster.

Met deze instelling kunnen bedrijfsmedewerkers zich aanmelden bij de clusterknooppunten met behulp van hun domeinreferenties. Ze kunnen hun domeinreferenties ook gebruiken om te verifiëren met andere goedgekeurde eindpunten zoals Apache Ambari Views, ODBC, JDBC, PowerShell en REST API's om met het cluster te communiceren.

### <a name="authorization"></a>Autorisatie

Een best practice die de meeste ondernemingen volgen is ervoor te zorgen dat niet elke werknemer toegang heeft tot alle bedrijfsresources. Op dezelfde manier kan de beheerder op rollen gebaseerd toegangsbeheerbeleid voor de clusterbronnen definiëren. Dit is alleen beschikbaar in de ESP-clusters.

De hadoop-beheerder kan op rollen gebaseerde toegangscontrole (RBAC) configureren om Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)en [Kafka](apache-domain-joined-run-kafka.md) te beveiligen met behulp van deze plug-ins in Apache Ranger. Met het configureren van RBAC-beleidsregels u machtigingen koppelen aan een rol in de organisatie. Deze abstractielaag maakt het gemakkelijker om ervoor te zorgen dat mensen alleen de machtigingen hebben die nodig zijn om hun werkverantwoordelijkheden uit te voeren. Ranger stelt u ook in staat om de toegang tot gegevens van werknemers en eventuele wijzigingen in het toegangscontrolebeleid te controleren.

De beheerder kan bijvoorbeeld [Apache Ranger](https://ranger.apache.org/) configureren om toegangscontrolebeleid in te stellen voor Hive. Deze functionaliteit zorgt voor filteren op rij- en kolomniveau (gegevensmaskering) en filtert de gevoelige gegevens van onbevoegde gebruikers.

### <a name="auditing"></a>Controleren

Controle van alle toegang tot de clusterbronnen en de gegevens is noodzakelijk om ongeautoriseerde of onbedoelde toegang tot de bronnen bij te houden. Het is net zo belangrijk als het beschermen van de HDInsight-clusterbronnen tegen onbevoegde gebruikers en het beveiligen van de gegevens.

De beheerder kan alle toegang tot de CLUSTERbronnen en gegevens van HDInsight bekijken en rapporteren. De beheerder kan ook alle wijzigingen in het toegangscontrolebeleid dat is gemaakt in door Apache Ranger ondersteunde eindpunten bekijken en rapporteren.

Als u toegang wilt krijgen tot controlelogboeken van Apache Ranger en Ambari en ssh-toegangslogboeken, [schakelt u Azure Monitor in](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) en bekijkt u de tabellen met controlerecords.

### <a name="encryption"></a>Versleuteling

Het beveiligen van gegevens is belangrijk om te voldoen aan de vereisten voor organisatiebeveiliging en compliance. Samen met het beperken van de toegang tot gegevens van onbevoegde werknemers, moet u deze versleutelen.

Beide gegevensopslagvoor HDInsight-clusters, Azure Blob-opslag en Azure Data Lake Storage Gen1/Gen2 ondersteunen transparante [serverversleuteling van gegevens](../../storage/common/storage-service-encryption.md) in rust. Secure HDInsight clusters werken naadloos met deze mogelijkheid van server-side encryptie van gegevens in rust.

### <a name="compliance"></a>Naleving

Azure-nalevingsaanbiedingen zijn gebaseerd op verschillende soorten garanties, waaronder formele certificeringen, verklaringen, validaties, autorisaties en beoordelingen die zijn geproduceerd door onafhankelijke externe accountantskantoren, contractuele wijzigingen, zelfbeoordelingen en door Microsoft geproduceerde documenten voor klantbegeleiding. Zie het Microsoft Trust [Center](https://www.microsoft.com/trust-center) en het [overzicht van Microsoft Azure-compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)voor nalevingsgegevens van HDInsight.

## <a name="shared-responsibility-model"></a>Model met gedeelde verantwoordelijkheid

De volgende afbeelding geeft een overzicht van de belangrijkste systeembeveiligingsgebieden en de beveiligingsoplossingen die voor u in elk van deze gebieden beschikbaar zijn. Ook wordt benadrukt welke beveiligingsgebieden uw verantwoordelijkheid zijn als klant en welke gebieden verantwoordelijk zijn voor HDInsight als dienstverlener.

![HDInsight gedeelde verantwoordelijkheden diagram](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

In de volgende tabel vindt u koppelingen naar resources voor elk type beveiligingsoplossing.

| Veiligheidszone | Beschikbare oplossingen | Verantwoordelijke partij |
|---|---|---|
| Beveiliging van gegevenstoegang | Toegangscontrolelijsten configureren [met ACL's](../../storage/blobs/data-lake-storage-access-control.md) voor Azure Data Lake Storage Gen1 en Gen2  | Klant |
|  | Schakel de eigenschap ['Vereiste overdracht veilig'](../../storage/common/storage-require-secure-transfer.md) in op opslagaccounts. | Klant |
|  | [Azure Storage-firewalls](../../storage/common/storage-network-security.md) en virtuele netwerken configureren | Klant |
|  | [Eindpunten voor Azure-netwerkservice configureren](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) voor Cosmos DB en [Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Klant |
|  | Zorg ervoor dat [TLS-versleuteling](../../storage/common/storage-security-tls.md) is ingeschakeld voor gegevens die onderweg zijn. | Klant |
|  | Door de klant beheerde sleutels configureren voor Azure [Storage-versleuteling](../../storage/common/storage-encryption-keys-portal.md) | Klant |
| Beveiliging van toepassingen en middleware | Integreren met AAD-DS en [verificatie configureren](apache-domain-joined-configure-using-azure-adds.md) | Klant |
|  | Beleid voor [Apache Ranger-autorisatie](apache-domain-joined-run-hive.md) configureren | Klant |
|  | [Azure Monitor-logboeken gebruiken](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Klant |
| Beveiliging van het besturingssysteem | Clusters maken met de meest recente beveiligde basisafbeelding | Klant |
|  | Zorgen voor [OS-patchen](../hdinsight-os-patching.md) op regelmatige tijdstippen | Klant |
| Netwerkbeveiliging | Een [virtueel netwerk](../hdinsight-plan-virtual-network-deployment.md) configureren |
|  | [NSG-regels (Inbound Network Security Group) configureren](../hdinsight-plan-virtual-network-deployment.md#networktraffic) | Klant |
|  | [Uitgaande verkeersbeperking](../hdinsight-restrict-outbound-traffic.md) configureren met Firewall | Klant |
| Gevirtualiseerde infrastructuur | N.v.t. | HDInsight (Cloud provider) |
| Beveiliging van fysieke infrastructuur | N.v.t. | HDInsight (cloudprovider) |

## <a name="next-steps"></a>Volgende stappen

* [Plan voor HDInsight-clusters met ESP](apache-domain-joined-architecture.md)
* [HDInsight-clusters configureren met ESP](apache-domain-joined-configure.md)
* [HDInsight-clusters beheren met ESP](apache-domain-joined-manage.md)
