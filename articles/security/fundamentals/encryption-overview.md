---
title: Overzicht van Azure-versleuteling | Microsoft Documenten
description: Meer informatie over verschillende versleutelingsopties in Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: mbaldwin
ms.openlocfilehash: ce78ade4df3c5bcea9e4e44750c430065cbfc5b0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454642"
---
# <a name="azure-encryption-overview"></a>Overzicht van Azure-versleuteling

In dit artikel vindt u een overzicht van hoe versleuteling wordt gebruikt in Microsoft Azure. Het omvat de belangrijkste gebieden van versleuteling, waaronder versleuteling in rust, versleuteling tijdens de vlucht en sleutelbeheer met Azure Key Vault. Elke sectie bevat links naar meer gedetailleerde informatie.

## <a name="encryption-of-data-at-rest"></a>Versleuteling van gegevens in rust

Gegevens in rust bevat informatie die zich bevindt in permanente opslag op fysieke media, in elk digitaal formaat. De media kunnen bestanden bevatten op magnetische of optische media, gearchiveerde gegevens en back-ups van gegevens. Microsoft Azure biedt een verscheidenheid aan oplossingen voor gegevensopslag om aan verschillende behoeften te voldoen, waaronder bestands-, schijf-, blob- en tabelopslag. Microsoft biedt ook versleuteling om [Azure SQL Database,](../../sql-database/sql-database-technical-overview.md) [Azure Cosmos DB](../../data-factory/introduction.md)en Azure Data Lake te beschermen.

Gegevensversleuteling in rust is beschikbaar voor services in de software as a service (SaaS), platform as a service (PaaS) en cloudmodellen (Infrastructure as a Service). In dit artikel worden bronnen samengevat en beschikbaar gemaakt waarmee u de Azure-versleutelingsopties gebruiken.

Zie [Azure Data Encryption-at-Rest](encryption-atrest.md)voor een meer gedetailleerde discussie over de manier waarop gegevens in rust worden versleuteld in Azure.

## <a name="azure-encryption-models"></a>Azure-versleutelingsmodellen

Azure ondersteunt verschillende versleutelingsmodellen, waaronder server-side encryptie die servicebeheerde sleutels, door de klant beheerde sleutels in Key Vault of door de klant beheerde sleutels op door de klant beheerde hardware gebruikt. Met versleuteling aan de clientzijde u sleutels on-premises of op een andere veilige locatie beheren en opslaan.

### <a name="client-side-encryption"></a>Clientversleuteling

Versleuteling aan de clientzijde wordt buiten Azure uitgevoerd. Het omvat:

- Gegevens versleuteld door een toepassing die wordt uitgevoerd in het datacenter van de klant of door een servicetoepassing.
- Gegevens die al zijn versleuteld wanneer deze door Azure worden ontvangen.

Met client-side encryptie hebben cloudserviceproviders geen toegang tot de versleutelingssleutels en kunnen ze deze gegevens niet decoderen. U behoudt de volledige controle over de sleutels.

### <a name="server-side-encryption"></a>Versleuteling aan de serverzijde

De drie server-side encryptie modellen bieden verschillende sleutelbeheer kenmerken, die u kiezen op basis van uw eisen:

- **Service-managed sleutels**: Biedt een combinatie van controle en gemak met lage overhead.

- **Door de klant beheerde sleutels:** geeft u controle over de sleutels, inclusief ByOK-ondersteuning (Bring Your Own Keys) of stelt u in staat om nieuwe te genereren.

- **Servicebeheerde sleutels in klantgestuurde hardware:** hiermee u sleutels beheren in uw eigen opslagplaats, buiten de controle van Microsoft. Dit kenmerk heet Host Your Own Key (HYOK). De configuratie is echter complex en de meeste Azure-services ondersteunen dit model niet.

### <a name="azure-disk-encryption"></a>Azure-schijfversleuteling

U virtuele machines van Windows en Linux beschermen met behulp van [Azure-schijfversleuteling](/azure/security/fundamentals/azure-disk-encryption-vms-vmss), die windows [BitLocker-technologie](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) en Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) gebruikt om zowel schijven van besturingssystemen als gegevensschijven te beschermen met volledige volumeversleuteling.

Versleutelingssleutels en -geheimen worden beveiligd in uw [Azure Key Vault-abonnement.](../../key-vault/general/overview.md) Door de Azure Backup-service te gebruiken, u een back-up maken en versleutelde virtuele machines (VM's) herstellen die gebruikmaken van kek-configuratie (Key Encryption Key).

### <a name="azure-storage-service-encryption"></a>Azure Storage Service-versleuteling

Gegevens in rust in Azure Blob-opslag en Azure-bestandsshares kunnen worden versleuteld in scenario's aan de serverzijde en client.

[Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) kan gegevens automatisch versleutelen voordat deze worden opgeslagen en degegevens worden automatisch decodeert wanneer u deze ophaalt. Het proces is volledig transparant voor gebruikers. Storage Service Encryption maakt gebruik van 256-bits [Advanced Encryption Standard (AES) encryptie,](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)dat is een van de sterkste blok ciphers beschikbaar. AES verwerkt versleuteling, decryptie en sleutelbeheer transparant.

### <a name="client-side-encryption-of-azure-blobs"></a>Versleuteling aan de clientzijde van Azure-blobs

U client-side encryptie van Azure blobs op verschillende manieren uitvoeren.

U het Azure Storage Client Library for .NET NuGet-pakket gebruiken om gegevens binnen uw clienttoepassingen te versleutelen voordat deze naar uw Azure-opslag worden geüpload.

Zie [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage)voor meer informatie over en download het Azure Storage Client Library voor .NET NuGet-pakket.

Wanneer u client-side encryptie met Key Vault gebruikt, worden uw gegevens versleuteld met behulp van een eenmalige symmetrische Content Encryption Key (CEK) die wordt gegenereerd door de Azure Storage client SDK. De CEK wordt versleuteld met behulp van een Key Encryption Key (KEK), die een symmetrische sleutel of een asymmetrische sleutelpaar kan zijn. U het lokaal beheren of opslaan in Key Vault. De versleutelde gegevens worden vervolgens geüpload naar Azure Storage.

Zie [Zelfstudie: Blobs versleutelen en decoderen in Azure Storage met Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)voor meer informatie over versleuteling aan de clientzijde en aan de slag met instructies voor how-to.

Ten slotte u de Azure Storage Client Library voor Java ook gebruiken om client-side encryptie uit te voeren voordat u gegevens uploadt naar Azure Storage, en om de gegevens te decoderen wanneer u deze downloadt naar de client. Deze bibliotheek ondersteunt ook integratie met [Key Vault](https://azure.microsoft.com/services/key-vault/) voor beheer van opslagaccountsleutels.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Versleuteling van gegevens in rust met Azure SQL Database

[Azure SQL Database](../../sql-database/sql-database-technical-overview.md) is een relationele databaseservice voor algemene doeleinden in Azure die structuren zoals relationele gegevens, JSON, ruimtelijk en XML ondersteunt. SQL Database ondersteunt zowel server-side encryptie via de Transparent Data Encryption (TDE) functie en client-side encryptie via de Always Encrypted functie.

#### <a name="transparent-data-encryption"></a>Transparante gegevensversleuteling

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) wordt gebruikt om [SQL Server,](https://www.microsoft.com/sql-server/sql-server-2016) [Azure SQL Database](../../sql-database/sql-database-technical-overview.md)en Azure SQL Data [Warehouse-gegevensbestanden](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) in realtime te versleutelen met behulp van een Database Encryption Key (DEK), die wordt opgeslagen in de databasebootrecord voor beschikbaarheid tijdens herstel.

TDE beschermt gegevens en logbestanden met behulp van AES- en Triple Data Encryption Standard -versleutelingsalgoritmen (Triple Data Encryption Standard). Versleuteling van het databasebestand wordt uitgevoerd op paginaniveau. De pagina's in een versleutelde database worden versleuteld voordat ze naar de schijf worden geschreven en worden gedecodeerd wanneer ze in het geheugen worden gelezen. TDE is nu standaard ingeschakeld op nieuw gemaakte Azure SQL-databases.

#### <a name="always-encrypted-feature"></a>Always Encrypted-functie

Met de [functie Altijd versleuteld](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) in Azure SQL u gegevens binnen clienttoepassingen versleutelen voordat u deze opslaat in Azure SQL Database. U ook delegatie van on-premises databasebeheer naar derden inschakelen en de scheiding bewaren tussen degenen die eigenaar zijn van en de gegevens kunnen bekijken en degenen die deze beheren, maar er geen toegang toe zouden moeten hebben.

#### <a name="cell-level-or-column-level-encryption"></a>Versleuteling op celniveau of kolomniveau

Met Azure SQL Database u symmetrische versleuteling toepassen op een kolom met gegevens met Behulp van Transact-SQL. Deze benadering wordt [celversleuteling of kolomversleuteling (CLE)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)genoemd, omdat u deze gebruiken om specifieke kolommen of zelfs specifieke gegevenscellen met verschillende versleutelingssleutels te versleutelen. Hierdoor u meer granulaire versleutelingsmogelijkheden bieden dan TDE, dat gegevens in pagina's versleutelt.

CLE heeft ingebouwde functies die u gebruiken om gegevens te versleutelen met behulp van symmetrische of asymmetrische sleutels, de openbare sleutel van een certificaat of een wachtwoordzin met behulp van 3DES.

### <a name="cosmos-db-database-encryption"></a>Cosmos DB-databaseversleuteling

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) is de wereldwijd gedistribueerde database met meerdere modellen van Microsoft. Gebruikersgegevens die zijn opgeslagen in Cosmos DB in niet-vluchtige opslag (solid-state drives) worden standaard versleuteld. Er zijn geen besturingselementen om het in of uit te schakelen. Encryptie in rust wordt geïmplementeerd met behulp van een aantal beveiligingstechnologieën, waaronder beveiligde sleutelopslagsystemen, versleutelde netwerken en cryptografische API's. Versleutelingssleutels worden beheerd door Microsoft en worden gedraaid volgens interne richtlijnen van Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>At-rest encryptie in Data Lake

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) is een bedrijfsbrede opslagplaats van elk type gegevens dat op één plaats wordt verzameld voordat een formele definitie van vereisten of schema wordt gedefinieerd. Data Lake Store ondersteunt 'standaard' transparante versleuteling van gegevens in rust, die is ingesteld tijdens het aanmaken van uw account. Azure Data Lake Store beheert standaard de sleutels voor u, maar u hebt de optie om ze zelf te beheren.

Drie soorten sleutels worden gebruikt bij het versleutelen en ontsleutelen van gegevens: de Master Encryption Key (MEK), Data Encryption Key (DEK) en Block Encryption Key (BEK). De MEK wordt gebruikt om de DEK, die is opgeslagen op persistente media, te versleutelen en de BEK is afgeleid van de DEK en het gegevensblok. Als u uw eigen sleutels beheert, u de MEK roteren.

## <a name="encryption-of-data-in-transit"></a>Versleuteling van gegevens tijdens het transport

Azure biedt veel mechanismen om gegevens privé te houden terwijl deze van de ene locatie naar de andere worden verplaatst.

### <a name="tlsssl-encryption-in-azure"></a>TLS/SSL-versleuteling in Azure

Microsoft gebruikt het [TLS-protocol (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security) om gegevens te beschermen wanneer deze worden gebruikt tussen de cloudservices en klanten. Microsoft-datacenters onderhandelen over een TLS-verbinding met clientsystemen die verbinding maken met Azure-services. TLS biedt sterke verificatie, berichtprivacy en integriteit (detectie van berichtenvervalsing, interceptie en vervalsing), interoperabiliteit, flexibiliteit van algoritmen en gebruiksgemak.

[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) beschermt verbindingen tussen de clientsystemen van klanten en Microsoft-cloudservices door unieke sleutels. Verbindingen maken ook gebruik van RSA-gebaseerde 2,048-bits encryptiesleutellengtes. Deze combinatie maakt het moeilijk voor iemand om gegevens die onderweg zijn te onderscheppen en te openen.

### <a name="azure-storage-transactions"></a>Azure Storage-transacties

Wanneer u interactie hebt met Azure Storage via de Azure-portal, vinden alle transacties plaats via HTTPS. U de Storage REST API via HTTPS ook gebruiken om te communiceren met Azure Storage. U het gebruik van HTTPS afdwingen wanneer u de REST API's belt om toegang te krijgen tot objecten in opslagaccounts door de beveiligde overdracht in te schakelen die vereist is voor het opslagaccount.

Shared Access Signatures[(SAS),](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)die kan worden gebruikt om toegang tot Azure Storage-objecten te delegeren, bevatten een optie om op te geven dat alleen het HTTPS-protocol kan worden gebruikt wanneer u Gedeelde toegangshandtekeningen gebruikt. Deze aanpak zorgt ervoor dat iedereen die links met SAS-tokens verzendt, het juiste protocol gebruikt.

[SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), dat werd gebruikt om toegang te krijgen tot Azure Files-shares, ondersteunt versleuteling en is beschikbaar in Windows Server 2012 R2, Windows 8, Windows 8.1 en Windows 10. Het biedt toegang tot verschillende regio's en zelfs toegang op het bureaublad.

Versleuteling aan de clientzijde versleutelt de gegevens voordat deze naar uw Azure Storage-instantie worden verzonden, zodat deze worden versleuteld terwijl deze over het netwerk worden verzonden.

### <a name="smb-encryption-over-azure-virtual-networks"></a>SMB-versleuteling via virtuele Azure-netwerken 

Door [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) te gebruiken in VM's waarop Windows Server 2012 of hoger wordt uitgevoerd, u gegevensoverdrachten beveiligen door gegevens te versleutelen tijdens het transport via Azure Virtual Networks. Door gegevens te versleutelen, helpt u u te beschermen tegen sabotage- en afluisteraanvallen. Beheerders kunnen SMB-versleuteling inschakelen voor de hele server, of alleen specifieke shares.

Nadat SMB-versleuteling is ingeschakeld voor een aandeel of server, hebben alleen SMB 3.0-clients toegang tot de versleutelde shares.

## <a name="in-transit-encryption-in-vms"></a>Versleuteling in transit in VM's

Gegevens die onderweg zijn naar, van en tussen VM's waarop Windows wordt uitgevoerd, worden op verschillende manieren versleuteld, afhankelijk van de aard van de verbinding.

### <a name="rdp-sessions"></a>RDP-sessies

U verbinding maken en u aanmelden bij een VM met behulp van het [Extern bureaublad-protocol (RDP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) vanaf een Windows-clientcomputer of vanaf een Mac waarop een RDP-client is geïnstalleerd. Gegevens die via het netwerk worden vervoerd in RDP-sessies kunnen worden beschermd door TLS.

U ook Extern bureaublad gebruiken om verbinding te maken met een Linux-vm in Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Veilige toegang tot Linux VM's met SSH

Voor extern beheer u [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) gebruiken om verbinding te maken met Linux-VM's die in Azure worden uitgevoerd. SSH is een versleuteld verbindingsprotocol dat veilige aanmeldingen via onbeveiligde verbindingen mogelijk maakt. Het is het standaard verbindingsprotocol voor Linux VM's dat wordt gehost in Azure. Door SSH-sleutels te gebruiken voor verificatie, hoeft u zich niet meer aan te melden. SSH gebruikt een publiek/private sleutelpaar (asymmetrische versleuteling) voor authenticatie.

## <a name="azure-vpn-encryption"></a>Azure VPN-versleuteling

U verbinding maken met Azure via een virtueel privénetwerk dat een beveiligde tunnel maakt om de privacy van de gegevens die via het netwerk worden verzonden, te beschermen.

### <a name="azure-vpn-gateways"></a>Azure VPN Gateways

U een [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) gebruiken om versleuteld verkeer tussen uw virtuele netwerk en uw on-premises locatie te verzenden via een openbare verbinding of om verkeer tussen virtuele netwerken te verzenden.

Vpn's van site-to-site gebruiken [IPsec](https://en.wikipedia.org/wiki/IPsec) voor transportversleuteling. Azure VPN-gateways maken gebruik van een reeks standaardvoorstellen. U Azure VPN-gateways configureren om een aangepast IPsec/IKE-beleid te gebruiken met specifieke cryptografische algoritmen en belangrijke sterke punten, in plaats van de standaardbeleidssets van Azure.

### <a name="point-to-site-vpns"></a>Punt-naar-site-VPN's

Point-to-site VPN's bieden afzonderlijke clientcomputers toegang tot een virtueel Azure-netwerk. [Het Secure Socket Tunneling Protocol (SSTP)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) wordt gebruikt om de VPN-tunnel te maken. Het kan firewalls doorkruisen (de tunnel wordt weergegeven als een HTTPS-verbinding). U uw eigen interne openbare sleutelinfrastructuur (PKI) rootcertificaatautoriteit (CA) gebruiken voor point-to-site-connectiviteit.

U een point-to-site VPN-verbinding configureren met behulp van de Azure-portal met certificaatverificatie of PowerShell.

Zie voor meer informatie over point-to-site VPN-verbindingen met virtuele Azure-netwerken:

[Een point-to-site-verbinding met een virtueel netwerk configureren met behulp van certificeringsverificatie: Azure-portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Een point-to-site-verbinding met een virtueel netwerk configureren met behulp van certificaatverificatie: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Site-to-site VPN's 

U een site-to-site VPN-gatewayverbinding gebruiken om uw on-premises netwerk te verbinden met een Virtueel Azure-netwerk via een IPsec/IKE (IKEv1 of IKEv2) VPN-tunnel. Dit type verbinding vereist een on-premises VPN-apparaat met een extern gericht openbaar IP-adres dat eraan is toegewezen.

U een site-to-site VPN-verbinding configureren met behulp van de Azure-portal, PowerShell of Azure CLI.

Zie voor meer informatie:

[Een site-to-site-verbinding maken in de Azure-portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Een site-to-site-verbinding maken in PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Een virtueel netwerk maken met een site-to-site VPN-verbinding met CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Versleuteling tijdens het transport in Data Lake

Gegevens tijdens overdracht (ook wel gegevens in beweging genoemd) worden ook altijd versleuteld in Data Lake Store. Naast het versleutelen van gegevens voordat ze worden opgeslagen in permanente media, worden de gegevens ook altijd beveiligd tijdens het transport met behulp van HTTPS. HTTPS is het enige protocol dat wordt ondersteund voor de Data Lake Store REST-interfaces.

Zie [Versleuteling van gegevens in Data Lake](../../data-lake-store/data-lake-store-encryption.md)voor meer informatie over versleuteling van gegevens die onderweg zijn in Data Lake.

## <a name="key-management-with-key-vault"></a>Sleutelbeheer met Key Vault

Zonder de juiste bescherming en het beheer van de sleutels, encryptie is nutteloos gemaakt. Key Vault is de door Microsoft aanbevolen oplossing voor het beheren en beheren van toegang tot encryptiesleutels die door cloudservices worden gebruikt. Machtigingen voor toegang tot sleutels kunnen worden toegewezen aan services of aan gebruikers via Azure Active Directory-accounts.

Key Vault ontlast organisaties van de noodzaak om hardwarebeveiligingsmodules (HSM's) en key management software te configureren, patchen en onderhouden. Wanneer u Key Vault gebruikt, behoudt u de controle. Microsoft ziet nooit uw sleutels en toepassingen hebben er geen directe toegang toe. U ook sleutels importeren of genereren in HSM's.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure-beveiliging](get-started-overview.md)
- [Overzicht van Azure-netwerkbeveiliging](network-overview.md)
- [Overzicht van azure-databasebeveiliging](database-security-overview.md)
- [Overzicht van de beveiliging van Azure-virtuele machines](virtual-machines-overview.md)
- [Gegevensversleuteling in rust](encryption-atrest.md)
- [Best practices voor gegevensbeveiliging en -versleuteling](data-encryption-best-practices.md)
