---
title: Beveiligingsoverwegingen
description: Beschrijft de basisbeveiligingsinfrastructuur die gegevensverplaatsingsservices in Azure Data Factory gebruiken om uw gegevens te beveiligen.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: bb3f22223bd64c06cfa4a5f6ffabe7b128dff1d5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416474"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Beveiligingsoverwegingen voor gegevensverkeer in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
>
> * [Versie 1](v1/data-factory-data-movement-security-considerations.md)
> * [Huidige versie](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven dat gegevensverkeerservices in Azure Data Factory gebruiken om uw gegevens te beveiligen. Data Factory-beheerbronnen zijn gebaseerd op azure-beveiligingsinfrastructuur en maken gebruik van alle mogelijke beveiligingsmaatregelen die Azure biedt.

In een Data Factory-oplossing maakt u een of meer gegevens[pijplijnen](concepts-pipelines-activities.md). Een pijplijn is een logische groep activiteiten die samen een taak uitvoeren. Deze pijplijnen bevinden zich in de regio waar de gegevensfabriek is gemaakt. 

Hoewel Data Factory slechts in weinig regio's beschikbaar is, is de service voor gegevensverkeer [wereldwijd beschikbaar](concepts-integration-runtime.md#integration-runtime-location) om naleving van gegevens, efficiëntie en lagere netwerkkosten te garanderen. 

Azure Data Factory slaat geen gegevens op, behalve gekoppelde servicereferenties voor cloudgegevensarchieven, die worden versleuteld met behulp van certificaten. Met Data Factory maakt u gegevensgestuurde werkstromen om gegevensverkeer tussen [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)en verwerking van gegevens te orkestreren met behulp van [compute services](compute-linked-services.md) in andere regio's of in een on-premises omgeving. U ook werkstromen bewaken en beheren met behulp van SDK's en Azure Monitor.

Data Factory is gecertificeerd voor:

| **[CSA STAR-certificering](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Als u geïnteresseerd bent in Azure-naleving en hoe Azure de eigen infrastructuur beveiligt, gaat u naar het [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). Voor de laatste lijst met alle https://aka.ms/AzureComplianceAzure Compliance-aanbiedingen check - .

In dit artikel bekijken we beveiligingsoverwegingen in de volgende twee scenario's voor gegevensverplaatsing: 

- **Cloudscenario:** In dit scenario zijn zowel uw bron als uw bestemming openbaar toegankelijk via internet. Deze omvatten beheerde cloudopslagservices zoals Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-services zoals Salesforce en webprotocollen zoals FTP en OData. Zoek een volledige lijst met ondersteunde gegevensbronnen in [ondersteunde gegevensarchieven en -indelingen.](copy-activity-overview.md#supported-data-stores-and-formats)
- **Hybride scenario:** In dit scenario bevindt uw bron of uw bestemming zich achter een firewall of in een on-premises bedrijfsnetwerk. Of het gegevensarchief bevindt zich in een privénetwerk of virtueel netwerk (meestal de bron) en is niet openbaar toegankelijk. Databaseservers die op virtuele machines worden gehost, vallen ook onder dit scenario.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cloudscenario's

### <a name="securing-data-store-credentials"></a>Referenties voor gegevensopslag beveiligen

- **Versleutelde referenties opslaan in een beheerde azure datafactory.** Data Factory helpt bij het beschermen van uw gegevensarchiefreferenties door ze te versleutelen met certificaten die door Microsoft worden beheerd. Deze certificaten worden om de twee jaar geroteerd (inclusief certificaatverlenging en de migratie van referenties). Zie [Azure Storage-beveiligingsoverzicht](../security/fundamentals/storage-overview.md)voor meer informatie over azure storage-beveiliging.
- **Referenties opslaan in Azure Key Vault**. U de referenties van het gegevensarchief ook opslaan in [Azure Key Vault.](https://azure.microsoft.com/services/key-vault/) Data Factory haalt de referentie op tijdens het uitvoeren van een activiteit. Zie [Store-referenties in Azure Key Vault](store-credentials-in-key-vault.md)voor meer informatie.

### <a name="data-encryption-in-transit"></a>Gegevensversleuteling tijdens het transport
Als de clouddatastore HTTPS of TLS ondersteunt, zijn alle gegevensoverdrachten tussen gegevensverplaatsingsservices in Data Factory en een clouddatawinkel via veilig kanaal HTTPS of TLS.

> [!NOTE]
> Alle verbindingen met Azure SQL Database en Azure SQL Data Warehouse vereisen versleuteling (SSL/TLS) terwijl gegevens onderweg zijn van en naar de database. Wanneer u een pijplijn maakt met JSON, voegt u de eigenschap versleuteling toe en stelt u deze in op **true** in de verbindingstekenreeks. Voor Azure Storage u **HTTPS** gebruiken in de verbindingstekenreeks.

> [!NOTE]
> Als u versleuteling tijdens het transport wilt inschakelen terwijl gegevens van Oracle worden verplaatst, volgt u een van de onderstaande opties:
> 1. Ga in oracle-server naar Oracle Advanced Security (OAS) en configureer de versleutelingsinstellingen, die Triple-DES Encryption (3DES) en Advanced Encryption Standard (AES) ondersteunen, raadpleeg [hier](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) voor meer informatie. ADF onderhandelt automatisch over de versleutelingsmethode om de codering smeu te gebruiken die u in OAS configureert bij het tot stand brengen van een verbinding met Oracle.
> 2. In ADF u EncryptionMethod=1 toevoegen aan de verbindingstekenreeks (in de gekoppelde service). Dit gebruikt SSL/TLS als versleutelingsmethode. Om dit te gebruiken, moet u niet-SSL-versleutelingsinstellingen uitschakelen in OAS aan de kant van de Oracle-server om versleutelingsconflicten te voorkomen.

> [!NOTE]
> TLS-versie gebruikt is 1.2.

### <a name="data-encryption-at-rest"></a>Versleuteling van inactieve gegevens
Sommige gegevensopslag ondersteunen versleuteling van gegevens in rust. We raden u aan het gegevensversleutelingsmechanisme voor die gegevensopslag in te schakelen. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent Data Encryption (TDE) in Azure SQL Data Warehouse helpt beschermen tegen de dreiging van schadelijke activiteiten door het uitvoeren van real-time encryptie en decryptie van uw gegevens in rust. Dit gedrag is transparant voor de klant. Zie [Een database beveiligen in SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)voor meer informatie.

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database ondersteunt ook transparante gegevensversleuteling (TDE), die helpt beschermen tegen de dreiging van schadelijke activiteiten door het uitvoeren van real-time encryptie en decryptie van de gegevens, zonder dat wijzigingen in de toepassing. Dit gedrag is transparant voor de klant. Zie [Transparante gegevensversleuteling voor SQL Database en Data Warehouse voor](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)meer informatie.

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store biedt ook versleuteling voor gegevens die in het account zijn opgeslagen. Wanneer gegevens lake store is ingeschakeld, versleutelt het automatisch gegevens voordat ze blijven bestaan en worden gedecodeerd voordat deze worden opgehaald, waardoor deze transparant zijn voor de client die toegang heeft tot de gegevens. Zie [Beveiliging in Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md)voor meer informatie. 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob-opslag en Azure Table-opslag
Azure Blob-opslag en Azure Table Storage Storage Service Encryption (SSE), die uw gegevens automatisch versleutelt voordat deze worden opgeslagen en gedecodeerd voordat u deze ophaalt. Zie [Azure Storage Service Encryption for Data at Rest voor](../storage/common/storage-service-encryption.md)meer informatie.

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 ondersteunt zowel client- als serverversleuteling van gegevens in rust. Zie [Gegevens beveiligen met versleuteling voor](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)meer informatie.

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift ondersteunt clusterversleuteling voor gegevens in rust. Zie [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)voor meer informatie . 

#### <a name="salesforce"></a>SalesForce
Salesforce ondersteunt Shield Platform Encryption waarmee versleuteling van alle bestanden, bijlagen en aangepaste velden mogelijk is. Zie [De Verificatiestroom van de webserver oauth begrijpen](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)voor meer informatie.  

## <a name="hybrid-scenarios"></a>Hybride scenario's
Hybride scenario's vereisen dat zelf gehoste runtime voor integratie wordt geïnstalleerd in een on-premises netwerk, in een virtueel netwerk (Azure) of in een virtuele privécloud (Amazon). De self-hosted integratie runtime moet toegang hebben tot de lokale datastores. Zie [Runtime voor zelfgehoste](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)integratie maken en configureren voor meer informatie over runtime voor zelfgehoste integratie. 

![self-hosted integration runtime-kanalen](media/data-movement-security-considerations/data-management-gateway-channels.png)

Het commandokanaal maakt communicatie mogelijk tussen databewegingsservices in Data Factory en zelfgehoste inloop van integratie. De mededeling bevat informatie met betrekking tot de activiteit. Het datakanaal wordt gebruikt voor het overbrengen van gegevens tussen on-premises datastores en clouddatastores.    

### <a name="on-premises-data-store-credentials"></a>On-premises gegevensarchiefreferenties
De referenties kunnen worden opgeslagen in de gegevensfabriek of worden [verwezen door gegevensfabriek](store-credentials-in-key-vault.md) tijdens de runtime van Azure Key Vault. Als referenties worden opgeslagen in de gegevensfabriek, wordt deze altijd versleuteld opgeslagen op de zelfgehoste runtime voor integratie. 
 
- **Referenties lokaal opslaan**. Als u de **cmdlet Set-AzDataFactoryV2LinkedService** met de verbindingstekenreeksen en referenties inline in de JSON direct gebruikt, wordt de gekoppelde service versleuteld en opgeslagen op zelfgehoste runtime voor integratie.  In dit geval stromen de referenties door azure backend service, die uiterst veilig is, naar de zelf gehoste integratiemachine waar deze uiteindelijk wordt versleuteld en opgeslagen. De self-hosted integratieruntime maakt gebruik van Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) om de gevoelige gegevens en referentiegegevens te versleutelen.

- **Referenties opslaan in Azure Key Vault**. U de referenties van het gegevensarchief ook opslaan in [Azure Key Vault.](https://azure.microsoft.com/services/key-vault/) Data Factory haalt de referentie op tijdens het uitvoeren van een activiteit. Zie [Store-referenties in Azure Key Vault](store-credentials-in-key-vault.md)voor meer informatie.

- **Sla referenties lokaal op zonder de referenties via Azure backend te laten stromen naar de zelf gehoste runtime voor integratie.** Als u referenties lokaal wilt versleutelen en opslaan op de runtime voor zelfgehoste integratie zonder dat u de referenties door de backend van de gegevensfabriek hoeft te leiden, volgt u de stappen in [Referenties versleutelen voor on-premises gegevensopslag in Azure Data Factory.](encrypt-credentials-self-hosted-integration-runtime.md) Alle connectors ondersteunen deze optie. De self-hosted integratieruntime maakt gebruik van Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) om de gevoelige gegevens en referentiegegevens te versleutelen. 

   Gebruik de cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** om gekoppelde servicereferenties en gevoelige gegevens in de gekoppelde service te versleutelen. U vervolgens de JSON-geretourneerde gegevens gebruiken (met het element **EncryptedCredential** in de verbindingstekenreeks) om een gekoppelde service te maken met behulp van de cmdlet **Set-AzDataFactoryV2LinkedService.**  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Poorten die worden gebruikt bij het versleutelen van gekoppelde service op zelf gehoste runtime voor integratie
PowerShell gebruikt standaard poort 8060 op de machine met zelf gehoste runtime voor integratie voor veilige communicatie. Indien nodig kan deze poort worden gewijzigd.  

![HTTPS-poort voor de gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Alle gegevensoverdrachten zijn via secure channel HTTPS en TLS via TCP om man-in-the-middle-aanvallen te voorkomen tijdens de communicatie met Azure-services.

U [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) of [Azure ExpressRoute](../expressroute/expressroute-introduction.md) ook gebruiken om het communicatiekanaal tussen uw on-premises netwerk en Azure verder te beveiligen.

Azure Virtual Network is een logische weergave van uw netwerk in de cloud. U een on-premises netwerk verbinden met uw virtuele netwerk door IPSec VPN (site-to-site) of ExpressRoute (private peering) in te stellen.    

In de volgende tabel worden de aanbevelingen voor runtime-configuratie van netwerk en zelf gehoste integratie samengevat op basis van verschillende combinaties van bron- en bestemmingslocaties voor hybride gegevensverkeer.

| Bron      | Doel                              | Netwerkconfiguratie                    | Installatie van integratieruntime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| On-premises | Virtuele machines en cloudservices geïmplementeerd in virtuele netwerken | IPSec VPN (point-to-site of site-to-site) | De self-hosted integration runtime moet worden geïnstalleerd op een virtuele Azure-machine in het virtuele netwerk.  |
| On-premises | Virtuele machines en cloudservices geïmplementeerd in virtuele netwerken | ExpressRoute (privé-peering)           | De self-hosted integration runtime moet worden geïnstalleerd op een virtuele Azure-machine in het virtuele netwerk.  |
| On-premises | Azure-services met een openbaar eindpunt | ExpressRoute (Microsoft-peering)            | De self-hosted integration runtime kan on-premises of op een Virtuele Azure-machine worden geïnstalleerd. |

De volgende afbeeldingen tonen het gebruik van zelf gehoste invoerruntime voor het verplaatsen van gegevens tussen een on-premises database en Azure-services met behulp van ExpressRoute en IPSec VPN (met Azure Virtual Network):

**ExpressRoute**

![ExpressRoute gebruiken met gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN met gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a>Firewallconfiguraties en het instellen van lijstvoor IP-adressen toestaan

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Firewallvereisten voor on-premises/privénetwerk    
In een onderneming draait een bedrijfsfirewall op de centrale router van de organisatie. Windows Firewall draait als een daemon op de lokale machine waarin de zelf gehoste integratie runtime is geïnstalleerd. 

In de volgende tabel vindt u uitgaande poort- en domeinvereisten voor bedrijfsfirewalls:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> Mogelijk moet u poorten beheren of lijst toestaan instellen voor domeinen op bedrijfsfirewallniveau, zoals vereist door de desbetreffende gegevensbronnen. In deze tabel worden alleen Azure SQL Database, Azure SQL Data Warehouse en Azure Data Lake Store als voorbeelden gebruikt.   

In de volgende tabel worden inkomende poortvereisten voor Windows Firewall aangemaakt:

| Poorten voor inkomend verkeer | Beschrijving                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Vereist door de PowerShell-versleutelingscmdlet zoals beschreven in [Versleutelingsreferenties voor on-premises gegevensopslag in Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md)en door de toepassing credential manager om veilig referenties in te stellen voor on-premises gegevensopslag op de zelf gehoste runtime voor integratie. |

![Vereisten voor gatewaypoorten](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>IP-configuraties en lijst instellen in gegevensarchieven toestaan
Sommige gegevensopslag in de cloud vereisen ook dat u het IP-adres van de machine toegang tot de winkel toestaat. Zorg ervoor dat het IP-adres van de zelf gehoste runtime-machine voor integratie op de juiste manier is toegestaan of geconfigureerd in de firewall.

De volgende cloudgegevensarchieven vereisen dat u het IP-adres van de zelf gehoste runtime-machine voor integratie toestaat. Voor sommige van deze gegevensarchieven is standaard geen lijst met toegestane gegevens vereist. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL-gegevensmagazijn](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Kan de self-hosted integratieruntime worden gedeeld in verschillende datafabrieken?**

Ja. [Hier](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/) vindt u meer informatie.

**Wat zijn de poortvereisten voor de zelfgehoste runtime voor integratie om te werken?**

De self-hosted integratie runtime maakt HTTP-gebaseerde verbindingen om toegang te krijgen tot het internet. De uitgaande poorten 443 moeten worden geopend voor de zelf gehoste runtime voor integratie om deze verbinding te maken. Open binnenkomende poort 8060 alleen op machineniveau (niet op bedrijfsfirewallniveau) voor de toepassing van credential manager. Als Azure SQL Database of Azure SQL Data Warehouse wordt gebruikt als bron of bestemming, moet u poort 1433 ook openen. Zie voor meer informatie de [firewallconfiguraties en sta lijstinstellingen voor ip-adressen](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) toe. 


## <a name="next-steps"></a>Volgende stappen
Zie [Prestatie- en stemhandleiding voor activiteit kopiëren](copy-activity-performance.md)kopiëren voor informatie over de prestaties van Azure Data Factory Copy Activity.

 
