---
title: Gegevens migreren van een on-premises Netezza-server naar Azure
description: Gebruik Azure Data Factory om gegevens van een on-premises Netezza-server naar Azure te migreren.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: a0263880262da95f4d26ee8388da464e9a59efca
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416445"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Azure Data Factory gebruiken om gegevens van een on-premises Netezza-server naar Azure te migreren 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory biedt een performant, robuust en kosteneffectief mechanisme om gegevens op schaal te migreren van een on-premises Netezza-server naar uw Azure-opslagaccount of Azure SQL Data Warehouse-database. 

In dit artikel vindt u de volgende informatie voor gegevenstechnici en ontwikkelaars:

> [!div class="checklist"]
> * Prestaties 
> * Veerkracht kopiëren
> * Netwerkbeveiliging
> * Oplossingsarchitectuur op hoog niveau 
> * Best Practices voor de implementatie  

## <a name="performance"></a>Prestaties

Azure Data Factory biedt een serverloze architectuur die parallellisme op verschillende niveaus mogelijk maakt. Als u een ontwikkelaar bent, betekent dit dat u pijplijnen bouwen om zowel netwerk- als databasebandbreedte volledig te gebruiken om de doorvoer van gegevensverplaatsingen voor uw omgeving te maximaliseren.

![Prestatiediagram](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Het voorgaande diagram kan als volgt worden geïnterpreteerd:

- Eén kopieeractiviteit kan profiteren van schaalbare rekenbronnen. Wanneer u Azure Integration Runtime gebruikt, u [maximaal 256 DIU's](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) voor elke kopieeractiviteit op een serverloze manier opgeven. Met een self-hosted integration runtime (self-hosted IR) u de machine handmatig opschalen of uitschalen naar meerdere machines[(maximaal vier knooppunten)](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)en één kopieeractiviteit verdeelt de partitie over alle knooppunten. 

- Een enkele kopie activiteit leest van en schrijft naar het gegevensarchief met behulp van meerdere threads. 

- De controlestroom van Azure Data Factory kan meerdere kopieeractiviteiten parallel starten. Het kan ze bijvoorbeeld starten met een [voor elke lus.](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity) 

Zie [Handleiding activiteitsprestaties en schaalbaarheid kopiëren voor](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)meer informatie.

## <a name="resilience"></a>Veerkracht

Binnen één exemplaaractiviteitsrun heeft Azure Data Factory een ingebouwd mechanisme voor nieuwe apparaten, waarmee het een bepaald niveau van tijdelijke fouten in de gegevensopslag of in het onderliggende netwerk kan verwerken.

Wanneer u gegevens kopieert tussen bron- en sinkgegevensopslag, u met de kopieeractiviteit van Azure Data Factory op twee manieren in compatibele rijen omgaan. U de kopieeractiviteit afbreken en niet nalaten of de rest van de gegevens blijven kopiëren door de incompatibele gegevensrijen over te slaan. Als u de oorzaak van de fout wilt achterhalen, u bovendien de incompatibele rijen in Azure Blob-opslag of Azure Data Lake Store registreren, de gegevens op de gegevensbron herstellen en de kopieeractiviteit opnieuw proberen.

## <a name="network-security"></a>Netwerkbeveiliging 

Azure Data Factory brengt gegevens standaard over van de on-premises Netezza-server naar een Azure-opslagaccount of Azure SQL Data Warehouse-database met behulp van een versleutelde verbinding via Hypertext Transfer Protocol Secure (HTTPS). HTTPS biedt data-encryptie tijdens het transport en voorkomt afluisteren en man-in-the-middle-aanvallen.

Als u niet wilt dat gegevens via het openbare internet worden overgedragen, u ook helpen bij het bereiken van een hogere beveiliging door gegevens over te dragen via een privé-peeringkoppeling via Azure Express Route. 

De volgende sectie bespreekt hoe u een hogere beveiliging bereiken.

## <a name="solution-architecture"></a>Architectuur voor de oplossing

In deze sectie worden twee manieren besproken om uw gegevens te migreren.

### <a name="migrate-data-over-the-public-internet"></a>Gegevens migreren via het openbare internet

![Gegevens migreren via het openbare internet](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Het voorgaande diagram kan als volgt worden geïnterpreteerd:

- In deze architectuur draagt u gegevens veilig over door HTTPS via het openbare internet te gebruiken.

- Om deze architectuur te bereiken, moet u de runtime van Azure Data Factory-integratie (zelf gehost) installeren op een Windows-machine achter een bedrijfsfirewall. Zorg ervoor dat deze integratieruntime rechtstreeks toegang heeft tot de Netezza-server. Als u uw netwerk- en gegevensopslagbandbreedte volledig wilt gebruiken om gegevens te kopiëren, u uw machine handmatig opschalen of opschalen naar meerdere machines.

- Met deze architectuur u zowel de eerste momentopnamegegevens als deltagegevens migreren.

### <a name="migrate-data-over-a-private-network"></a>Gegevens migreren via een privénetwerk 

![Gegevens migreren via een privénetwerk](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Het voorgaande diagram kan als volgt worden geïnterpreteerd:

- In deze architectuur migreert u gegevens via een privé-peeringkoppeling via Azure Express Route en worden gegevens nooit via het openbare internet overgemaakt. 

- Om deze architectuur te bereiken, moet u de runtime van Azure Data Factory-integratie (zelf gehost) installeren op een Virtuele Windows-machine (VM) in uw virtuele Azure-netwerk. Als u uw netwerk- en gegevensopslagbandbreedte volledig wilt gebruiken om gegevens te kopiëren, u uw VM handmatig opschalen of uitschalen naar meerdere VM's.

- Met deze architectuur u zowel de eerste momentopnamegegevens als deltagegevens migreren.

## <a name="implement-best-practices"></a>Best practices implementeren 

### <a name="manage-authentication-and-credentials"></a>Verificatie en referenties beheren 

- Als u wilt verifiëren bij Netezza, u [ODBC-verificatie gebruiken via verbindingstekenreeks.](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties) 

- Ga als een verifiëren in Azure Blob-opslag: 

   - We raden ten zeerste aan [beheerde identiteiten te gebruiken voor Azure-bronnen.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) Dankzij beheerde identiteiten u pijplijnen configureren zonder dat u referenties hoeft te leveren in de definitie van Linked Service.  

   - U ook verifiëren naar Azure Blob-opslag met behulp van [serviceprincipal,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)een [handtekening voor gedeelde toegang](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)of een [opslagaccountsleutel](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 

- Ga als een verificatie naar Azure Data Lake Storage Gen2: 

   - We raden ten zeerste aan [beheerde identiteiten te gebruiken voor Azure-bronnen.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)
   
   - U ook [serviceprincipal](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) of een [opslagaccountsleutel](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)gebruiken. 

- Verificatie naar Azure SQL Data Warehouse:

   - We raden ten zeerste aan [beheerde identiteiten te gebruiken voor Azure-bronnen.](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)
   
   - U ook [serviceprincipal-](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) of [SQL-verificatie](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)gebruiken.

- Wanneer u beheerde identiteiten niet gebruikt voor Azure-bronnen, raden we u ten zeerste aan [de referenties in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) op te slaan om het eenvoudiger te maken om sleutels centraal te beheren en te roteren zonder dat azure data factory-gekoppelde services hoeven te worden gewijzigd. Dit is ook een van de [best practices voor CI / CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Eerste momentopnamegegevens migreren 

Voor kleine tabellen (dat wil zeggen tabellen met een volume van minder dan 100 GB of die binnen twee uur naar Azure kunnen worden gemigreerd), u elke taaktaakbelastinggegevens per tabel laten kopiëren. Voor een grotere doorvoer u meerdere Azure Data Factory-kopieertaken uitvoeren om afzonderlijke tabellen tegelijkertijd te laden. 

Binnen elke kopieertaak u parallelle query's uitvoeren en gegevens kopiëren op partities, en u ook een bepaald niveau van parallellisme bereiken door de [ `parallelCopies` eigenschapsinstelling](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) te gebruiken met een van de volgende opties voor gegevenspartitie:

- Om een grotere efficiëntie te bereiken, raden we u aan om vanuit een dataslice te gaan.  Zorg ervoor dat de `parallelCopies` waarde in de instelling lager is dan het totale aantal gegevenssegmentpartities in uw tabel op de Netezza-server.  

- Als het volume van elke dataslicepartitie nog steeds groot is (bijvoorbeeld 10 GB of meer), raden we u aan om over te schakelen naar een partitie met dynamisch bereik. Deze optie geeft u meer flexibiliteit om het aantal partities en het volume van elke partitie te definiëren op partitiekolom, bovengrens en ondergrens.

Voor grotere tabellen (dat wil zeggen tabellen met een volume van 100 GB of meer of die niet binnen twee uur naar Azure *kunnen* worden gemigreerd), raden we u aan de gegevens te partitioneren op basis van aangepaste query en vervolgens elke copy-job één partitie tegelijk te laten kopiëren. Voor een betere doorvoer u meerdere Azure Data Factory-kopieertaken tegelijk uitvoeren. Voor elk exemplaartaakdoel van het laden van één partitie op aangepaste query, u de doorvoer verhogen door parallellisme in te schakelen via gegevenssegment of dynamisch bereik. 

Als een kopieertaak mislukt vanwege een probleem met een tijdelijke netwerk- of gegevensarchief, u de mislukte kopieertaak opnieuw uitvoeren om die specifieke partitie uit de tabel opnieuw te laden. Andere kopieertaken die andere partities laden, worden niet beïnvloed.

Wanneer u gegevens laadt in een Azure SQL Data Warehouse-database, raden we u aan PolyBase in te schakelen in de kopieertaak met Azure Blob-opslag als faseringsfase.

### <a name="migrate-delta-data"></a>Deltagegevens migreren 

Als u de nieuwe of bijgewerkte rijen uit uw tabel wilt identificeren, gebruikt u een tijdstempelkolom of een verhogingssleutel in het schema. U vervolgens de laatste waarde opslaan als een hoog watermerk in een externe tabel en deze vervolgens gebruiken om de deltagegevens te filteren wanneer u de volgende keer gegevens laadt. 

Elke tabel kan een andere watermerkkolom gebruiken om de nieuwe of bijgewerkte rijen te identificeren. We raden u aan een externe controletabel te maken. In de tabel vertegenwoordigt elke rij één tabel op de Netezza-server met de specifieke naam van de watermerkkolom en de hoge watermerkwaarde. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Een zelf gehoste runtime voor integratie configureren

Als u gegevens migreert van de Netezza-server naar Azure, of de server nu on-premises achter uw bedrijfsfirewall of binnen een virtuele netwerkomgeving staat, moet u een zelf gehoste IR installeren op een Windows-machine of VM, de engine die wordt gebruikt om gegevens te verplaatsen. Terwijl u de zelfgehoste IR installeert, raden we u de volgende aanpak aan:

- Voor elke Windows-machine of -vm begint u met een configuratie van 32 vCPU- en 128 GB geheugen. U de CPU en het geheugengebruik van de IR-machine tijdens de gegevensmigratie blijven volgen om te zien of u de machine verder moet opschalen voor betere prestaties of de machine moet verkleinen om kosten te besparen.

- U ook uitschalen door maximaal vier knooppunten te koppelen aan één zelf gehoste IR. Een enkele kopieertaak die wordt uitgevoerd tegen een zelf gehoste IR, past automatisch alle VM-knooppunten toe om de gegevens parallel te kopiëren. Voor hoge beschikbaarheid begint u met vier VM-knooppunten om één storingspunt tijdens de gegevensmigratie te voorkomen.

### <a name="limit-your-partitions"></a>Uw partities beperken

Voer als best practice een performance proof of concept (POC) uit met een representatieve voorbeeldgegevensset, zodat u voor elke kopieeractiviteit een geschikte partitiegrootte bepalen. We raden u aan elke partitie binnen twee uur op Azure te laden.  

Als u een tabel wilt kopiëren, begint u met één exemplaaractiviteit met één, zelf gehoste IR-machine. Verhoog de `parallelCopies` instelling geleidelijk op basis van het aantal gegevenssegmentpartities in uw tabel. Bekijk of de hele tabel binnen twee uur op Azure kan worden geladen, afhankelijk van de doorvoer die voortvloeit uit de kopieertaak. 

Als het niet binnen twee uur op Azure kan worden geladen en de capaciteit van het zelf gehoste IR-knooppunt en het gegevensarchief niet volledig worden gebruikt, neemt u het aantal gelijktijdige kopieeractiviteiten geleidelijk toe totdat u de limiet van uw netwerk of de bandbreedtelimiet van de gegevensopslag hebt bereikt. 

Houd het CPU- en geheugengebruik op de zelf gehoste IR-machine in de gaten en wees klaar om de machine op te schalen of uit te schalen naar meerdere machines wanneer u ziet dat de CPU en het geheugen volledig worden gebruikt. 

Wanneer u beperkingsfouten tegenkomt, zoals gerapporteerd door de kopieeractiviteit van `parallelCopies` Azure Data Factory, vermindert u de gelijktijdigheid of instelling in Azure Data Factory of overweegt u de bandbreedte- of I/O-bewerkingen per seconde (IOPS) van de netwerk- en gegevensopslag te verhogen. 


### <a name="estimate-your-pricing"></a>Uw prijzen schatten 

Overweeg de volgende pijplijn, die is gemaakt om gegevens van de on-premises Netezza-server te migreren naar een Azure SQL Data Warehouse-database:

![De prijspijplijn](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Laten we aannemen dat de volgende uitspraken waar zijn: 

- Het totale datavolume is 50 terabyte (TB). 

- We migreren gegevens met behulp van architectuur met de eerste oplossing (de Netezza-server is on-premises, achter de firewall).

- Het 50-TB volume is verdeeld in 500 partities en elke kopieeractiviteit verplaatst één partitie.

- Elke kopieeractiviteit is geconfigureerd met één zelf gehoste IR tegen vier machines en bereikt een doorvoer van 20 megabyte per seconde (MBps). (Binnen kopieeractiviteit `parallelCopies` is ingesteld op 4 en elke thread om gegevens uit de tabel te laden, bereikt een doorvoervan 5 MBps.)

- De ForEach-gelijktijdigheid is ingesteld op 3 en de totale doorvoer is 60 MBps.

- In totaal duurt het 243 uur om de migratie te voltooien.

Op basis van de voorgaande veronderstellingen, hier is de geschatte prijs: 

![De prijstabel](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> De prijzen in de voorgaande tabel zijn hypothetisch. Uw werkelijke prijs hangt af van de werkelijke doorvoer in uw omgeving. De prijs voor de Windows-machine (met de zelf gehoste IR geïnstalleerd) is niet inbegrepen. 

### <a name="additional-references"></a>Aanvullende naslaginformatie

Zie de volgende artikelen en handleidingen voor meer informatie:

- [Gegevens migreren van een on-premises relationele datawarehousedatabase naar Azure met Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza-connector](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC-connector](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob-opslagconnector](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL Data Warehouse-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Tuning-handleiding voor activiteitsprestaties kopiëren](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Zelf-hostende Integration Runtime maken en configureren](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Self-hosted integration runtime HA en schaalbaarheid](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Beveiligingsoverwegingen bij het verplaatsen van gegevens](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Referenties opslaan in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Gegevens stapsgewijs uit één tabel kopiëren](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Gegevens stapsgewijs kopiëren uit meerdere tabellen](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Prijspagina Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Volgende stappen

- [Bestanden uit meerdere containers kopiëren met Azure Data Factory](solution-template-copy-files-multiple-containers.md)
