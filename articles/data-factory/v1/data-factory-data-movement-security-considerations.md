---
title: Beveiligingsoverwegingen voor gegevensverkeer in Azure Data Factory
description: Meer informatie over het beveiligen van gegevensverkeer in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1f19d258531e5368238cba72c986aede3f4a64ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130839"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - Beveiligingsoverwegingen voor gegevensverplaatsing

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, raadpleegt u [overwegingen voor gegevensverplaatsingsbeveiliging voor Gegevensfabriek](../data-movement-security-considerations.md).

## <a name="introduction"></a>Inleiding
In dit artikel wordt beschreven dat gegevensverkeerservices in Azure Data Factory gebruiken om uw gegevens te beveiligen. Beheerbronnen van Azure Data Factory zijn gebaseerd op azure-beveiligingsinfrastructuur en maken gebruik van alle mogelijke beveiligingsmaatregelen die Azure biedt.

In een Data Factory-oplossing maakt u een of meer gegevens[pijplijnen](data-factory-create-pipelines.md). Een pijplijn is een logische groep activiteiten die samen een taak uitvoeren. Deze pijplijnen bevinden zich in de regio waar de gegevensfabriek is gemaakt. 

Hoewel Data Factory alleen beschikbaar is in **regio's in het Westen**van de VS , **Oost-VS**en **Noord-Europa,** is de dienst voor gegevensverkeer wereldwijd beschikbaar [in verschillende regio 's](data-factory-data-movement-activities.md#global). Data Factory-service zorgt ervoor dat gegevens een geografisch gebied/regio niet verlaten, tenzij u de service expliciet opdraagt een alternatief gebied te gebruiken als de gegevensverplaatsingsservice nog niet naar die regio is geïmplementeerd. 

Azure Data Factory zelf slaat geen gegevens op, behalve voor gekoppelde servicereferenties voor cloudgegevensarchieven, die worden versleuteld met certificaten. Hiermee u gegevensgestuurde werkstromen maken om gegevensverkeer tussen [ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) en verwerking van gegevens te orkestreren met behulp van [compute services](data-factory-compute-linked-services.md) in andere regio's of in een on-premises omgeving. Het stelt u ook in staat om workflows te [controleren en te beheren](data-factory-monitor-manage-pipelines.md) met behulp van zowel programmatische als UI-mechanismen.

Gegevensverplaatsing met Azure Data Factory is **gecertificeerd** voor:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STER](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Als u geïnteresseerd bent in Azure-naleving en hoe Azure zijn eigen infrastructuur beveiligt, gaat u naar het [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

In dit artikel bekijken we beveiligingsoverwegingen in de volgende twee scenario's voor gegevensverplaatsing: 

- **Cloudscenario**- In dit scenario zijn zowel uw bron als bestemming openbaar toegankelijk via internet. Deze omvatten beheerde cloudopslagservices zoals Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-services zoals Salesforce en webprotocollen zoals FTP en OData. Hier vindt u een volledige lijst met ondersteunde [gegevensbronnen.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- **Hybride scenario**- In dit scenario bevindt uw bron of bestemming zich achter een firewall of in een on-premises bedrijfsnetwerk of bevindt het gegevensarchief zich in een privénetwerk/virtueel netwerk (meestal de bron) en is het niet openbaar toegankelijk. Databaseservers die op virtuele machines worden gehost, vallen ook onder dit scenario.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cloudscenario's
### <a name="securing-data-store-credentials"></a>Referenties voor gegevensopslag beveiligen
Azure Data Factory beschermt de referenties van uw gegevensarchief door ze **te versleutelen** met certificaten die door Microsoft **worden beheerd.** Deze certificaten worden om de **twee jaar** geroteerd (inclusief verlenging van het certificaat en migratie van referenties). Deze versleutelde referenties worden veilig opgeslagen in een **Azure Storage die wordt beheerd door Azure Data Factory-beheerservices.** Raadpleeg [azure storagebeveiligingsoverzicht](../../security/fundamentals/storage-overview.md)voor meer informatie over Azure Storage-beveiliging.

### <a name="data-encryption-in-transit"></a>Gegevensversleuteling tijdens het transport
Als de clouddatastore HTTPS of TLS ondersteunt, zijn alle gegevensoverdrachten tussen dataverplaatsingsservices in Data Factory en een clouddatawinkel via veilig kanaal HTTPS of TLS.

> [!NOTE]
> Alle verbindingen met **Azure SQL Database** en Azure SQL Data **Warehouse** vereisen altijd versleuteling (SSL/TLS) terwijl gegevens onderweg zijn van en naar de database. Terwijl u een pijplijn maakt met een JSON-editor, voegt u de **eigenschap versleuteling** toe en stelt u deze in op **true** in de **verbindingstekenreeks**. Wanneer u de [wizard Kopiëren](data-factory-azure-copy-wizard.md)gebruikt, stelt de wizard deze eigenschap standaard in. Voor **Azure Storage**u **HTTPS** gebruiken in de verbindingstekenreeks.

### <a name="data-encryption-at-rest"></a>Versleuteling van inactieve gegevens
Sommige gegevensopslag ondersteunen versleuteling van gegevens in rust. We raden u aan het mechanisme voor gegevensversleuteling in te schakelen voor die gegevensopslag. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent Data Encryption (TDE) in Azure SQL Data Warehouse helpt bij het beschermen tegen de dreiging van schadelijke activiteiten door het uitvoeren van real-time encryptie en decryptie van uw gegevens in rust. Dit gedrag is transparant voor de klant. Zie [Een database beveiligen in SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)voor meer informatie.

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database ondersteunt ook transparante gegevensversleuteling (TDE), die helpt bij het beschermen tegen de dreiging van schadelijke activiteiten door het uitvoeren van real-time encryptie en decryptie van de gegevens zonder wijzigingen in de toepassing. Dit gedrag is transparant voor de klant. Zie [Transparante gegevensversleuteling met Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)voor meer informatie. 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store biedt ook versleuteling voor gegevens die zijn opgeslagen in het account. Wanneer gegevens lake-archief is ingeschakeld, versleutelt het automatisch gegevens voordat ze blijven bestaan en worden gedecodeerd voordat deze worden opgehaald, waardoor deze transparant zijn voor de client die toegang heeft tot de gegevens. Zie [Beveiliging in Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md)voor meer informatie. 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob-opslag en Azure-tabelopslag
Azure Blob Storage en Azure Table storage ondersteunt Storage Service Encryption (SSE), die uw gegevens automatisch versleutelt voordat deze blijven worden opgeslagen en gedecodeerd voordat u deze ophaalt. Zie [Azure Storage Service Encryption for Data at Rest voor](../../storage/common/storage-service-encryption.md)meer informatie.

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 ondersteunt zowel client- als serverversleuteling van gegevens bij Rest. Zie [Gegevens beveiligen met versleuteling voor](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)meer informatie. Momenteel ondersteunt Data Factory Amazon S3 niet in een virtual private cloud (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift ondersteunt clusterversleuteling voor gegevens in rust. Zie [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)voor meer informatie . Momenteel ondersteunt Data Factory Amazon Redshift niet in een VPC. 

#### <a name="salesforce"></a>SalesForce
Salesforce ondersteunt Shield Platform Encryption waarmee versleuteling van alle bestanden, bijlagen en aangepaste velden mogelijk is. Zie [De Verificatiestroom van de webserver oauth begrijpen](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)voor meer informatie.  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybride scenario's (met behulp van Data Management Gateway)
Hybride scenario's vereisen dat De Gateway voor gegevensbeheer wordt geïnstalleerd in een on-premises netwerk of in een virtueel netwerk (Azure) of een virtuele privécloud (Amazon). De gateway moet toegang hebben tot de lokale gegevensarchieven. Zie [Data Management Gateway](data-factory-data-management-gateway.md)voor meer informatie over de gateway. 

![Data Management Gateway-kanalen](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

Het **commandokanaal** maakt communicatie mogelijk tussen dataverplaatsingsservices in Data Factory en Data Management Gateway. De mededeling bevat informatie met betrekking tot de activiteit. Het datakanaal wordt gebruikt voor het overbrengen van gegevens tussen on-premises datastores en clouddatastores.    

### <a name="on-premises-data-store-credentials"></a>On-premises gegevensarchiefreferenties
De referenties voor uw on-premises gegevensopslag worden lokaal opgeslagen (niet in de cloud). Ze kunnen op drie verschillende manieren worden ingesteld. 

- Met behulp van **plain-text** (minder veilig) via HTTPS van Azure Portal / Copy Wizard. De referenties worden in platte tekst doorgegeven aan de on-premises gateway.
- **JavaScript-cryptografiebibliotheek gebruiken vanuit wizard Kopiëren**.
- Met behulp van **click-once based credentials manager app**. De click-once-toepassing wordt uitgevoerd op de on-premises machine die toegang heeft tot de gateway en stelt referenties in voor het gegevensarchief. Deze optie en de volgende zijn de meest veilige opties. De app voor credential manager gebruikt standaard de poort 8050 op de machine met gateway voor veilige communicatie.  
- Gebruik de powerdlet [van New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) PowerShell om referenties te versleutelen. De cmdlet gebruikt het certificaat dat gateway is geconfigureerd om te gebruiken om de referenties te versleutelen. U de versleutelde referenties die door deze cmdlet worden geretourneerd, gebruiken en deze toevoegen aan **encryptedCredential-element** van de **verbindingString** in het JSON-bestand dat u gebruikt met de cmdlet [Nieuw-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) of in het JSON-fragment in de Data Factory Editor in de portal. Deze optie en de click-once applicatie zijn de meest veilige opties. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript-codering op basis van javascript-bibliotheek
U gegevensarchiefreferenties versleutelen met [javascript-cryptografiebibliotheek](https://www.microsoft.com/download/details.aspx?id=52439) vanuit de [wizard Kopiëren.](data-factory-copy-wizard.md) Wanneer u deze optie selecteert, haalt de wizard Kopiëren de openbare sleutel van de gateway op en gebruikt deze om de referenties van het gegevensarchief te versleutelen. De referenties worden gedecodeerd door de gateway machine en beschermd door Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Ondersteunde browsers:** IE8, IE9, IE10, IE11, Microsoft Edge en nieuwste Firefox, Chrome, Opera, Safari browsers. 

#### <a name="click-once-credentials-manager-app"></a>App voor het beheer van referenties per keer
U de op klikken gebaseerde app voor referentiebeheer van Azure-portal/Wizard kopiëren starten bij het ontwerpen van pijplijnen. Deze toepassing zorgt ervoor dat referenties niet worden overgedragen in platte tekst over de draad. Standaard gebruikt het de poort **8050** op de machine met gateway voor veilige communicatie. Indien nodig kan deze poort worden gewijzigd.  
  
![HTTPS-poort voor de gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Momenteel gebruikt Data Management Gateway één **certificaat.** Dit certificaat wordt gemaakt tijdens de gateway-installatie (is van toepassing op Data Management Gateway die is gemaakt na november 2016 en versie 2.4.xxxx.x of hoger). U dit certificaat vervangen door uw eigen SSL/TLS-certificaat. Dit certificaat wordt gebruikt door de toepassing click-once credential manager om veilig verbinding te maken met de gatewaymachine voor het instellen van gegevensarchiefreferenties. Het slaat gegevensarchiefreferenties veilig on-premises op met behulp van de Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) op de machine met gateway. 

> [!NOTE]
> Oudere gateways die vóór november 2016 of versie 2.3.xxxx.x zijn geïnstalleerd, blijven referenties gebruiken die versleuteld en opgeslagen zijn in de cloud. Zelfs als u de gateway naar de nieuwste versie upgradet, worden de referenties niet gemigreerd naar een on-premises machine    
  
| Gateway-versie (tijdens het maken) | Referenties opgeslagen | Standaardversleuteling/beveiliging | 
| --------------------------------- | ------------------ | --------- |  
| < = 2,3.xxxx.x | Op cloud | Versleuteld met behulp van certificaat (anders dan het certificaat dat wordt gebruikt door de App Credential Manager) | 
| > = 2,4.xxxx.x | Ter plaatse | Beveiligd via DPAPI | 
  

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Alle gegevensoverdrachten zijn via secure channel **HTTPS** en **TLS via TCP** om man-in-the-middle-aanvallen te voorkomen tijdens de communicatie met Azure-services.
 
U [ipsec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) of [Express Route](../../expressroute/expressroute-introduction.md) ook gebruiken om het communicatiekanaal tussen uw on-premises netwerk en Azure verder te beveiligen.

Virtueel netwerk is een logische weergave van uw netwerk in de cloud. U een on-premises netwerk verbinden met uw Virtuele Azure-netwerk (VNet) door IPSec VPN (site-to-site) of Express Route (Private Peering) in te stellen     

In de volgende tabel worden de aanbevelingen voor netwerk- en gatewayconfiguratie samengevat op basis van verschillende combinaties van bron- en doellocaties voor hybride gegevensverplaatsing.

| Bron | Doel | Netwerkconfiguratie | Gateway-instelling |
| ------ | ----------- | --------------------- | ------------- | 
| On-premises | Virtuele machines en cloudservices geïmplementeerd in virtuele netwerken | IPSec VPN (point-to-site of site-to-site) | Gateway kan on-premises of op een Virtuele Azure-machine (VM) in VNet worden geïnstalleerd | 
| On-premises | Virtuele machines en cloudservices geïmplementeerd in virtuele netwerken | ExpressRoute (Private Peering) | Gateway kan on-premises of op een Azure VM in VNet worden geïnstalleerd | 
| On-premises | Azure-services met een openbaar eindpunt | ExpressRoute (Openbare peering) | Gateway moet on-premises worden geïnstalleerd | 

De volgende afbeeldingen tonen het gebruik van Data Management Gateway voor het verplaatsen van gegevens tussen een on-premises database en Azure-services met behulp van Express-route en IPSec VPN (met virtueel netwerk):

**Express Route:**
 
![ExpressRoute gebruiken met gateway](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN met gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Firewallconfiguraties en whitelisting IP-adres van gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Firewallvereisten voor on-premises/privénetwerk  
In een onderneming draait een **bedrijfsfirewall** op de centrale router van de organisatie. En, **Windows firewall** draait als een daemon op de lokale machine waarop de gateway is geïnstalleerd. 

In de volgende tabel vindt **u uitgaande poort-** en domeinvereisten voor de **bedrijfsfirewall.**

| Domeinnamen | Uitgaande poorten | Beschrijving |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Vereist door de gateway om verbinding te maken met gegevensverplaatsingsservices in Data Factory |
| `*.core.windows.net` | 443 | Wordt gebruikt door de gateway om verbinding te maken met Azure Storage Account wanneer u de [gefaseerde kopiefunctie](data-factory-copy-activity-performance.md#staged-copy) gebruikt. | 
| `*.frontend.clouddatahub.net` | 443 | Vereist door de gateway om verbinding te maken met de Azure Data Factory-service. | 
| `*.database.windows.net` | 1433   | (OPTIONEEL) nodig wanneer uw bestemming Azure SQL Database/ Azure SQL Data Warehouse is. Gebruik de gefaseerde kopieerfunctie om gegevens naar Azure SQL Database/Azure SQL Data Warehouse te kopiëren zonder de poort 1433 te openen. | 
| `*.azuredatalakestore.net` | 443 | (OPTIONEEL) nodig wanneer uw bestemming Azure Data Lake Store is | 

> [!NOTE] 
> Het kan zijn dat u poorten/ whitelistingdomeinen moet beheren op het niveau van de bedrijfsfirewall, zoals vereist door de respectieve gegevensbronnen. Deze tabel gebruikt alleen Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store als voorbeelden.   

In de volgende tabel vindt **u inkomende poortvereisten** voor de **windows-firewall.**

| Poorten voor inkomend verkeer | Beschrijving | 
| ------------- | ----------- | 
| 8050 (TCP) | Vereist door de toepassing credential manager om veilig referenties in te stellen voor on-premises gegevensarchieven op de gateway. | 

![Vereisten voor gatewaypoorten](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>IP-configuraties/ whitelisting in het gegevensarchief
Voor sommige gegevensopslag in de cloud moet ook een IP-adres op de witte lijst worden gewhitelist. Zorg ervoor dat het IP-adres van de gatewaymachine op de juiste manier op de witte lijst staat/geconfigureerd in firewall.

De volgende cloudgegevensarchieven vereisen whitelisting van ip-adres van de gatewaymachine. Voor sommige van deze gegevensarchieven is het mogelijk dat het IP-adres niet op de witte lijst hoeft te worden gewhitelist. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL-gegevensmagazijn](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Vraag:** Kan de Gateway worden gedeeld in verschillende datafabrieken?
**Antwoord:** We ondersteunen deze functie nog niet. We zijn hier druk mee bezig.

**Vraag:** Wat zijn de poortvereisten voor de gateway to work?
**Antwoord:** Gateway maakt HTTP-gebaseerde verbindingen met open internet. De **uitgaande poorten 443 en 80** moeten worden geopend voor gateway om deze verbinding te maken. Open **Binnenkomende poort 8050** alleen op machineniveau (niet op bedrijfsfirewallniveau) voor de toepassing Credential Manager. Als Azure SQL Database of Azure SQL Data Warehouse wordt gebruikt als bron/bestemming, moet u ook **de poort van 1433** openen. Zie [Firewall-configuraties en de sectie IP-adressen op de witte lijst](#firewall-configurations-and-whitelisting-ip-address-of gateway) voor meer informatie. 

**Vraag:** Wat zijn certificaatvereisten voor Gateway?
**Antwoord:** De huidige gateway vereist een certificaat dat wordt gebruikt door de toepassing credential manager voor het veilig instellen van gegevensarchiefreferenties. Dit certificaat is een zelfondertekend certificaat dat is gemaakt en geconfigureerd door de gateway-instelling. In plaats daarvan u uw eigen TLS/SSL-certificaat gebruiken. Zie de sectie [click-once credential manager application](#click-once-credentials-manager-app) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen
Zie [Activiteitsprestatie- en stemhandleiding kopiëren](data-factory-copy-activity-performance.md)voor informatie over de prestaties van kopieeractiviteiten.

 
