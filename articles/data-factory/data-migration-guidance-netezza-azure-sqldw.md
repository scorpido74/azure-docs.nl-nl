---
title: Azure Data Factory gebruiken om gegevens van on-premises Netezza-server naar Azure te migreren | Microsoft Docs
description: Gebruik Azure Data Factory om gegevens van een on-premises Netezza-server naar Azure te migreren.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259559"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>Azure Data Factory gebruiken om gegevens van on-premises Netezza-server naar Azure te migreren 

Azure Data Factory biedt een krachtig, robuust en rendabel mechanisme voor het migreren van gegevens op schaal van on-premises Netezza-server naar Azure Storage of Azure SQL Data Warehouse. In dit artikel vindt u de volgende informatie voor gegevens technici en ontwikkel aars:

> [!div class="checklist"]
> * Prestaties 
> * Tolerantie kopiëren
> * Netwerkbeveiliging
> * Architectuur van oplossing op hoog niveau 
> * Aanbevolen procedures voor implementatie  

## <a name="performance"></a>Prestaties

Azure Data Factory biedt een serverloze architectuur die parallellisme op verschillende niveaus toestaat, waarmee ontwikkel aars pijp lijnen kunnen bouwen om uw netwerk bandbreedte en database bandbreedte optimaal te benutten om de door Voer van gegevens verplaatsing voor uw variabelen.

![prestaties](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- Eén Kopieer activiteit kan profiteren van schaal bare reken bronnen: wanneer u Azure Integration Runtime gebruikt, kunt u [Maxi maal 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) voor elke Kopieer activiteit op serverloze wijze opgeven. Wanneer u zelf-hostende Integration Runtime gebruikt, kunt u de machine hand matig opschalen of uitschalen naar meerdere machines ([Maxi maal 4 knoop punten](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)). met één Kopieer activiteit wordt de partitie verdeeld over alle knoop punten. 
- Eén Kopieer activiteit leest van en schrijft naar het gegevens archief met behulp van meerdere threads. 
- Met Azure Data Factory controle stroom kunnen meerdere Kopieer activiteiten parallel worden gestart, bijvoorbeeld [voor elke lus](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Meer informatie vindt u in de [hand leiding](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) voor de prestaties van de Kopieer activiteit

## <a name="resilience"></a>Flexibiliteit

Binnen één exemplaar van de Kopieer activiteit heeft Azure Data Factory een ingebouwd mechanisme voor opnieuw proberen, zodat het een bepaald niveau van tijdelijke fouten in de gegevens archieven of in het onderliggende netwerk kan verwerken.

Azure Data Factory Copy-activiteit biedt u twee manieren om incompatibele rijen af te handelen bij het kopiëren van gegevens tussen de bron-en Sink-gegevens opslag. U kunt de Kopieer activiteit afbreken en mislukken wanneer incompatibele gegevens worden aangetroffen of door gaan met het kopiëren van niet-compatibele gegevens rijen. Daarnaast kunt u de niet-compatibele rijen in Azure Blob-opslag of Azure Data Lake Store registreren om de oorzaak van de fout te achterhalen, de gegevens op de gegevens bron te herstellen en de Kopieer activiteit opnieuw uit te voeren.

## <a name="network-security"></a>Netwerkbeveiliging 

Azure Data Factory brengt standaard gegevens over van on-premises Netezza-server naar Azure Storage of Azure SQL Data Warehouse met behulp van een versleutelde verbinding via het HTTPS-protocol. Het biedt gegevens versleuteling tijdens de overdracht en voor komt het afluis teren en man-in-the-middle-aanvallen.

Als u niet wilt dat gegevens worden overgezet via het open bare Internet, kunt u de beveiliging verbeteren door gegevens over een koppeling van een privé-peering over te brengen via Azure Express route. Raadpleeg de onderstaande oplossings architectuur voor meer informatie over hoe dit kan worden bereikt.

## <a name="solution-architecture"></a>Oplossingsarchitectuur

Gegevens migreren via het open bare Internet:

![oplossing-architectuur-openbaar-netwerk](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- In deze architectuur worden gegevens veilig overgedragen met behulp van HTTPS via open bare Internet.
- U moet Azure Data Factory zelf-hostende Integration runtime installeren op een Windows-computer achter de bedrijfs firewall om deze architectuur te realiseren. Zorg ervoor dat uw Azure Data Factory zelf-hostende Integration runtime op een Windows-computer rechtstreeks toegang kan krijgen tot uw Netezza-server. U kunt uw machine hand matig schalen of uitschalen naar meerdere machines om uw netwerk-en gegevens opslag bandbreedte optimaal te benutten om gegevens te kopiëren.
- U kunt zowel de initiële momentopname gegevens migratie als de migratie van de Delta gegevens bereiken met behulp van deze architectuur.

Gegevens migreren via een persoonlijke koppeling: 

![oplossing-architectuur-privé-netwerk](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- In deze architectuur wordt de gegevens migratie uitgevoerd via een koppeling voor een persoonlijke peering via een Azure Express-route zodat de gegevens nooit via het open bare Internet worden gepasseerd. 
- U moet Azure Data Factory zelf-hostende Integration runtime installeren op een Windows-VM in uw virtuele Azure-netwerk om deze architectuur te krijgen. U kunt uw Vm's hand matig opschalen of uitschalen naar meerdere Vm's om uw netwerk-en gegevens opslag bandbreedte optimaal te benutten om gegevens te kopiëren.
- U kunt zowel de initiële momentopname gegevens migratie als de migratie van de Delta gegevens bereiken met behulp van deze architectuur.

## <a name="implementation-best-practices"></a>Aanbevolen procedures voor implementatie 

### <a name="authentication-and-credential-management"></a>Verificatie en referentie beheer 

- U kunt [ODBC-verificatie via Connection String](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)gebruiken om te verifiëren bij Netezza. 
- Meerdere verificatie typen worden ondersteund om verbinding te maken met Azure Blob Storage.  Het gebruik van [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) wordt ten zeerste aanbevolen: gebouwd op basis van een automatisch beheerde Azure Data Factory identificeren in azure AD, kunt u pijp lijnen configureren zonder referenties op te geven in de definitie van de gekoppelde service.  U kunt ook verifiëren bij Azure Blob Storage met behulp van de [Service-Principal](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), de [hand tekening voor gedeelde toegang](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)of de sleutel van het [opslag account](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Meerdere verificatie typen worden ook ondersteund om verbinding te maken met Azure Data Lake Storage Gen2.  Het gebruik van [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) wordt sterk aanbevolen, hoewel de sleutel van de [Service-Principal](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) of het [opslag account](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) ook kan worden gebruikt. 
- Meerdere verificatie typen worden ook ondersteund om verbinding te maken met Azure SQL Data Warehouse. Het gebruik van [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity) wordt sterk aanbevolen, hoewel [Service-Principal](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) of [SQL-verificatie](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication) ook kan worden gebruikt.
- Wanneer u geen beheerde identiteiten voor Azure-resources gebruikt, wordt [het opslaan van de referenties in azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) ten zeerste aanbevolen, zodat het eenvoudiger is om sleutels te beheren en te draaien zonder Azure Data Factory gekoppelde services te wijzigen.  Dit is ook een van de [Aanbevolen procedures voor CI/cd](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Initiële momentopname gegevens migratie 

Voor kleine tabellen wanneer de volume grootte kleiner is dan 100 GB of die binnen twee uur kan worden gemigreerd naar Azure, kunt u elke taak voor het laden van gegevens per tabel maken. U kunt meerdere Azure Data Factory Kopieer taken uitvoeren om verschillende tabellen gelijktijdig te laden voor een betere door voer. 

Binnen elke Kopieer taak kunt u ook een zekere mate van parallellisme bereiken door de [instelling parallelCopies](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) te gebruiken met de optie gegevens partitie om parallelle query's uit te voeren en gegevens op partities te kopiëren. Er zijn twee opties voor de gegevens partitie die hieronder worden gekozen.
- U wordt geadviseerd om te beginnen met gegevens segment omdat het efficiënter is.  Zorg ervoor dat het aantal parallelle instellingen in parallelCopies de waarde lager is dan het totale aantal gegevens segment partities in de tabel op Netezza-server.  
- Als de grootte van het volume voor elke gegevens segment partitie nog steeds groot is (bijvoorbeeld groter dan 10 GB), wordt u aangeraden om over te scha kelen naar een dynamische bereik partitie, waar u meer flexibiliteit hebt om het aantal partities en de grootte van het volume voor elke partitie te definiëren per partitie kolom, bovengrens en ondergrens.

Voor de grote tabellen wanneer de grootte van het volume groter is dan 100 GB of niet binnen twee uur kan worden gemigreerd naar Azure, wordt u aangeraden de gegevens te partitioneren op basis van een aangepaste query en vervolgens elke Kopieer taak één partitie per keer te kopiëren. U kunt gelijktijdig meerdere Azure Data Factory Kopieer taken uitvoeren voor een betere door voer. Houd er rekening mee dat voor elk Kopieer taak doel één partitie per aangepaste query moet worden geladen, u de parallelle factor via gegevens segment of dynamisch bereik nog steeds kunt gebruiken om de door voer te verg Roten. 

Als een van de Kopieer taken mislukt als gevolg van een probleem met de tijdelijke netwerk-of gegevens opslag, kunt u de mislukte Kopieer taak opnieuw uitvoeren om deze specifieke partitie opnieuw uit de tabel te laden. Alle andere Kopieer taken voor het laden van andere partities worden niet beïnvloed.

Bij het laden van gegevens in Azure SQL Data Warehouse, wordt poly base aanbevolen in een Kopieer taak te worden ingeschakeld met een Azure Blob-opslag als fase ring.

### <a name="delta-data-migration"></a>Delta gegevens migratie 

De manier om de nieuwe of bijgewerkte rijen van de tabel te identificeren, is het gebruik van een time stamp-kolom of een incrementele sleutel binnen het schema. vervolgens slaat u de laatste waarde als hoge watermerk op in een externe tabel, die kan worden gebruikt om de Delta gegevens te filteren voor de volgende keer dat gegevens worden geladen. 

Verschillende tabellen kunnen een andere watermerk kolom gebruiken om de nieuwe of bijgewerkte rijen te identificeren. U kunt er ook Voorst Ellen om een tabel voor externe controle te maken waarbij elke rij een tabel op de Netezza-server vertegenwoordigt met de specifieke naam van de watermerk kolom en de bovengrens waarde. 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Zelf-hostende Integration runtime-configuratie op Azure VM of computer

Wanneer u gegevens migreert van Netezza-server naar Azure, is er geen Netezza-server aanwezig achter uw Corporation-firewall of binnen een VNET-omgeving. u moet zelf-hostende Integration runtime op Windows-computer of-VM installeren. Dit is de engine die u wilt verplaatsen gegevens.

- De aanbevolen configuratie om te beginnen voor elke machine of VM is met 32 vCPU en 128 GB geheugen. U kunt het CPU-en geheugen gebruik van de IR-machine tijdens de gegevens migratie blijven bewaken om te zien of u de machine verder moet opschalen voor betere prestaties of de machine omlaag kunt schalen om kosten te besparen.
- U kunt ook uitschalen door Maxi maal 4 knoop punten te koppelen aan één zelf-hostende IR. Eén Kopieer taak die wordt uitgevoerd op een zelf-hostende IR, maakt automatisch gebruik van alle VM-knoop punten om de gegevens parallel te kopiëren. Voor maximale Beschik baarheid kunt u het beste beginnen met 2 VM-knoop punten om Single Point of Failure te voor komen tijdens de gegevens migratie.

### <a name="rate-limiting"></a>Frequentie beperking

Als best practice, voert u een prestatie test uit met een representatieve voor beeld-gegevensset, zodat u de juiste partitie grootte kunt bepalen voor elke Kopieer activiteit. We raden u aan om elke partitie binnen twee uur naar Azure te laden.  

Begin met één Kopieer activiteit met één zelf-hostende IR-computer om een tabel te kopiëren. Verhoog de parallelCopies-instelling geleidelijk op basis van het aantal gegevens segment partities in de tabel en controleer of de volledige tabel binnen 2 uur kan worden geladen in azure, afhankelijk van de door Voer die u ziet in de Kopieer taak. 

Als deze niet kan worden bereikt en de capaciteit van het zelf-hostende IR-knoop punt en het gegevens archief niet volledig worden gebruikt, verhoogt u het aantal gelijktijdige Kopieer activiteiten geleidelijk totdat u de limieten bereikt van uw netwerk-of bandbreedte limiet voor de gegevens opslag. 

Blijf de CPU/het geheugen gebruik op een zelf-hostende IR-computer bewaken en bereid u voor om de machine te schalen of uit te breiden naar meerdere machines wanneer u ziet dat de CPU/het geheugen volledig wordt gebruikt. 

Wanneer u vertragings fouten ondervindt die worden gerapporteerd door Azure Data Factory Kopieer activiteit, verlaagt u de gelijktijdigheids-of parallelCopies instelling in Azure Data Factory of overweegt u de limieten voor band breedte/IOPS van het netwerk en de gegevens opslag te verhogen. 


### <a name="estimating-price"></a>Prijs schatten 

Bekijk de volgende pijp lijn die is gebouwd voor het migreren van gegevens van een on-premises Netezza-server naar Azure SQL Data Warehouse:

![prijs informatie-pijp lijn](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

We nemen het volgende uit: 

- Het totale gegevens volume is 50 TB. 
- Gegevens migreren met de eerste oplossings architectuur (Netezza server is on-premises achter de firewall)
- 50 TB is onderverdeeld in 500-partities en elke Kopieer activiteit verplaatst één partitie.
- Elke Kopieer activiteit wordt geconfigureerd met één zelf-hostende IR op vier computers en de door Voer van 20 MBps wordt gerealiseerd. (In de Kopieer activiteit wordt parallelCopies ingesteld op 4 en elke thread voor het laden van gegevens uit de tabel heeft een maximale door Voer van 5 MBps)
- Gelijktijdigheid van ForEach is ingesteld op 3 en een geaggregeerde door Voer is 60 MBps.
- In totaal duurt het 243 uur om de migratie te volt ooien.

Dit is de geschatte prijs op basis van de bovenstaande hypo Thesen: 

![prijs informatie: tabel](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Dit is een voor beeld van een hypothetische prijs. Uw werkelijke prijs is afhankelijk van de werkelijke door Voer in uw omgeving. De prijs voor de Windows-machine (met zelf-hostende Integration runtime) is niet opgenomen. 

### <a name="additional-references"></a>Aanvullende naslaginformatie 
- [Gegevens migratie van on-premises relationele data warehouse naar Azure met Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Netezza-connector](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC-Connector](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob Storage-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL Data Warehouse-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Gids voor het afstemmen van de activiteit prestaties kopiëren](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Zelf-hostende Integration Runtime maken en configureren](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Zelf-hostende runtime HA en schaal baarheid](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Beveiligings overwegingen voor gegevens verplaatsing](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Referenties opslaan in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Gegevens stapsgewijs uit een tabel kopiëren](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Gegevens stapsgewijs uit meerdere tabellen kopiëren](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Pagina met Azure Data Factory prijzen](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Volgende stappen

- [Bestanden van meerdere containers met Azure Data Factory kopiëren](solution-template-copy-files-multiple-containers.md)