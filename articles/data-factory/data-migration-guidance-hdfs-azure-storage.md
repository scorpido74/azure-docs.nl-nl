---
title: Gegevens migreren van een on-premises Hadoop-cluster naar Azure Storage
description: Meer informatie over het gebruik van Azure Data Factory om gegevens van het on-premises Hadoop-cluster te migreren naar Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: afccbdbbfd5b8ddeefa621448d6170d937b518f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931454"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Azure Data Factory gebruiken om gegevens van een on-premises Hadoop-cluster te migreren naar Azure Storage 

Azure Data Factory biedt een performant, robuust en kosteneffectief mechanisme voor het migreren van gegevens op schaal van on-premises HDFS naar Azure Blob-opslag of Azure Data Lake Storage Gen2. 

Data Factory biedt twee basisbenaderingen voor het migreren van gegevens van on-premises HDFS naar Azure. U de aanpak selecteren op basis van uw scenario. 

- **Data Factory DistCp-modus** (aanbevolen): in Data Factory u [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (gedistribueerde kopie) gebruiken om bestanden te kopiëren naar Azure Blob-opslag (inclusief [gefaseerde kopie)](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)of Azure Data Lake Store Gen2. Gebruik Data Factory geïntegreerd met DistCp om te profiteren van een bestaand krachtig cluster om de beste kopieerdoorvoer te bereiken. U profiteert ook van flexibele planning en een uniforme monitoringervaring van Data Factory. Afhankelijk van de configuratie van uw gegevensfabriek wordt in kopieeractiviteit automatisch een distcp-opdracht samengesteld, worden de gegevens naar uw Hadoop-cluster verzonden en wordt de kopieerstatus bewaakt. We raden de DistCp-modus voor gegevensfabriek aan voor het migreren van gegevens van een on-premises Hadoop-cluster naar Azure.
- **Data Factory native integration runtime mode:** DistCp is niet in alle scenario's een optie. In een Azure Virtual Networks-omgeving biedt het hulpprogramma DistCp bijvoorbeeld geen ondersteuning voor Azure ExpressRoute private peering met een azure storage-eindpunt voor virtueel netwerk. Bovendien wilt u in sommige gevallen uw bestaande Hadoop-cluster niet gebruiken als een engine voor het migreren van gegevens, zodat u geen zware lasten op uw cluster plaatst, wat de prestaties van bestaande ETL-taken kan beïnvloeden. In plaats daarvan u de native mogelijkheid van de runtime van de Integratiegegevensfabriek gebruiken als de engine die gegevens kopieert van on-premises HDFS naar Azure.

In dit artikel vindt u de volgende informatie over beide benaderingen:
> [!div class="checklist"]
> * Prestaties 
> * Veerkracht kopiëren
> * Netwerkbeveiliging
> * Oplossingsarchitectuur op hoog niveau 
> * Best Practices voor de implementatie  

## <a name="performance"></a>Prestaties

In de DistCp-modus van gegevensfabriek is de doorvoer hetzelfde als wanneer u de distCp-tool onafhankelijk gebruikt. Data Factory DistCp-modus maximaliseert de capaciteit van uw bestaande Hadoop-cluster. U DistCp gebruiken voor groot intercluster- of intraclusterkopiëren. 

DistCp gebruikt MapReduce om de distributie, foutafhandeling en -herstel en rapportage uit te voeren. Het breidt een lijst van bestanden en mappen uit in invoer voor taaktoewijzing. Elke taak kopieert een bestandspartitie die is opgegeven in de bronlijst. U Data Factory die is geïntegreerd met DistCp gebruiken om pijplijnen te bouwen om uw netwerkbandbreedte, opslag-IOPS en bandbreedte volledig te gebruiken om de doorvoer van gegevensverplaatsingen voor uw omgeving te maximaliseren.  

Data Factory native integration runtime mode maakt ook parallellisme op verschillende niveaus mogelijk. U parallellisme gebruiken om uw netwerkbandbreedte, opslag-IOPS en bandbreedte volledig te gebruiken om de doorvoer van gegevensverplaatsingen te maximaliseren:

- Eén kopieeractiviteit kan profiteren van schaalbare rekenbronnen. Met een self-hosted integratie runtime u de machine handmatig opschalen of uitschalen naar meerdere machines[(maximaal vier knooppunten).](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability) Met één kopieactiviteit wordt de bestandsset over alle knooppunten verdeeld. 
- Een enkele kopie activiteit leest van en schrijft naar het gegevensarchief met behulp van meerdere threads. 
- Data Factory controlestroom kan meerdere kopieeractiviteiten parallel starten. U bijvoorbeeld een [voor elke lus](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)gebruiken. 

Zie de [prestatiegids voor kopieeractiviteit](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)voor meer informatie .

## <a name="resilience"></a>Veerkracht

In de DistCp-modus gegevensfabriek u verschillende DistCp-opdrachtregelparameters gebruiken (Bijvoorbeeld `-i`fouten negeren of `-update`gegevens schrijven wanneer bronbestand en doelbestand in grootte verschillen) voor verschillende niveaus van veerkracht.

In de native integration runtime-modus van Data Factory heeft Data Factory in één exemplaaractiviteit een ingebouwd mechanisme voor nieuwe try. Het kan een bepaald niveau van tijdelijke fouten in de gegevensopslag of in het onderliggende netwerk behandelen. 

Bij het kopiëren van binaire gegevens van on-premises HDFS naar Blob-opslag en van on-premises HDFS naar Data Lake Store Gen2 voert Data Factory automatisch voor een groot deel controlepunten uit. Als een kopieeractiviteit mislukt of een keer uitvalt, bij een volgende poging (zorg ervoor dat het aantal opnieuw proberen > 1 is), wordt de kopie hervat vanaf het laatste foutpunt in plaats van bij het begin te beginnen.

## <a name="network-security"></a>Netwerkbeveiliging 

Gegevensfabriek brengt gegevens standaard over van on-premises HDFS naar Blob-opslag of Azure Data Lake Storage Gen2 met behulp van een versleutelde verbinding via het HTTPS-protocol. HTTPS biedt data-encryptie tijdens het transport en voorkomt afluisteren en man-in-the-middle-aanvallen. 

Als u niet wilt dat gegevens via het openbare internet worden overgedragen, u voor een hogere beveiliging gegevens overbrengen via een privé-peering-koppeling via ExpressRoute. 

## <a name="solution-architecture"></a>Architectuur voor de oplossing

Deze afbeelding toont migrerende gegevens via het openbare internet:

![Diagram met de oplossingsarchitectuur voor het migreren van gegevens via een openbaar netwerk](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- In deze architectuur worden gegevens veilig overgedragen door HTTPS via het openbare internet te gebruiken. 
- We raden u aan de DistCp-modus van DeGegevensfabriek te gebruiken in een openbare netwerkomgeving. U profiteren van een krachtig bestaand cluster om de beste kopieerdoorvoer te bereiken. U profiteert ook van flexibele planning en uniforme monitoringervaring van Data Factory.
- Voor deze architectuur moet u de runtime voor zelf gehoste integratievan Data Factory installeren op een Windows-machine achter een bedrijfsfirewall om de opdracht DistCp in te dienen bij uw Hadoop-cluster en de kopieerstatus te controleren. Omdat de machine niet de motor is die gegevens verplaatst (alleen voor besturingsdoeleinden), heeft de capaciteit van de machine geen invloed op de doorvoer van gegevensverplaatsing.
- Bestaande parameters van de opdracht DistCp worden ondersteund.

Deze afbeelding toont migrerende gegevens via een privékoppeling: 

![Diagram met de oplossingsarchitectuur voor het migreren van gegevens via een privénetwerk](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- In deze architectuur worden gegevens gemigreerd via een private peering-koppeling via Azure ExpressRoute. Gegevens komen nooit via het openbare internet.
- Het hulpprogramma DistCp biedt geen ondersteuning voor privépeering van ExpressRoute met een virtueel netwerkeindpunt voor Azure Storage. We raden u aan de native mogelijkheid van Data Factory te gebruiken via de inwerkingstijd van de integratie om de gegevens te migreren.
- Voor deze architectuur moet u de runtime voor zelf gehoste integratie van Data Factory installeren op een Windows-VM in uw virtuele Azure-netwerk. U uw VM handmatig opschalen of naar meerdere VM's schalen om uw netwerk- en opslag-IOPS of bandbreedte volledig te gebruiken.
- De aanbevolen configuratie om mee te beginnen voor elke Azure VM (met de zelf gehoste runtime van De Data Factory) is Standard_D32s_v3 met een 32 vCPU en 128 GB geheugen. U het CPU- en geheugengebruik van de VM tijdens gegevensmigratie controleren om te zien of u de VM moet opschalen voor betere prestaties of de VM moet verkleinen om de kosten te verlagen.
- U ook uitschalen door maximaal vier VM-knooppunten te koppelen aan één zelf gehoste runtime voor integratie. Een enkele kopieertaak die wordt uitgevoerd tegen een zelf gehoste nawerking van integratie, partities automatisch de bestandsset en maakt gebruik van alle VM-knooppunten om de bestanden parallel te kopiëren. Voor hoge beschikbaarheid raden we u aan te beginnen met twee VM-knooppunten om een scenario met één punt van fout te voorkomen tijdens gegevensmigratie.
- Wanneer u deze architectuur gebruikt, zijn de eerste momentopnamegegevensmigratie en deltagegevensmigratie voor u beschikbaar.

## <a name="implementation-best-practices"></a>Best Practices voor de implementatie

We raden u aan deze aanbevolen procedures te volgen wanneer u uw gegevensmigratie implementeert.

### <a name="authentication-and-credential-management"></a>Verificatie- en referentiebeheer 

- Als u wilt verifiëren bij HDFS, u [Windows (Kerberos) of Anoniem](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)gebruiken. 
- Er worden meerdere verificatietypen ondersteund voor verbinding maken met Azure Blob-opslag.  We raden ten zeerste aan [beheerde identiteiten te gebruiken voor Azure-bronnen.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) Dankzij beheerde identiteiten u pijplijnen configureren zonder referenties te leveren in de definitie van de gekoppelde service. U ook verifiëren naar Blob-opslag met behulp van een [serviceprincipal,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)een [handtekening voor gedeelde toegang](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)of een [opslagaccountsleutel](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Er worden ook meerdere verificatietypen ondersteund voor het maken van verbinding met Data Lake Storage Gen2.  We raden ten zeerste aan [beheerde identiteiten te gebruiken voor Azure-resources,](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)maar u ook een [serviceprincipal](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) of een [opslagaccountsleutel](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)gebruiken. 
- Wanneer u beheerde identiteiten niet gebruikt voor Azure-bronnen, raden we u ten zeerste aan [de referenties in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) op te slaan om het eenvoudiger te maken om sleutels centraal te beheren en te roteren zonder gekoppelde services van Data Factory te wijzigen. Dit is ook een [best practice voor CI / CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migratie van eerste momentopnamegegevens 

In de DistCp-modus gegevensfabriek u één kopieeractiviteit maken om de opdracht DistCp in te dienen en verschillende parameters gebruiken om het initiële gedrag van gegevensmigratie te beheren. 

In de runtime-modus voor native integratie van Data Factory raden we gegevenspartitie aan, vooral wanneer u meer dan 10 TB aan gegevens migreert. Als u de gegevens wilt partitioneren, gebruikt u de mapnamen op HDFS. Vervolgens kan elke kopietaak van Gegevensfabriek één mappartitie tegelijk kopiëren. U meerdere gegevensfabriekstaken tegelijk uitvoeren voor een betere doorvoer.

Als een van de kopieertaken mislukt als gevolg van tijdelijke problemen in het netwerk- of gegevensarchief, u de mislukte kopieertaak opnieuw uitvoeren om die specifieke partitie van HDFS opnieuw te laden. Andere kopieertaken die andere partities laden, worden niet beïnvloed.

### <a name="delta-data-migration"></a>Delta-gegevensmigratie 

In de DistCp-modus van gegevensfabriek u de `-update`parameter DistCp-opdrachtregel gebruiken, gegevens schrijven wanneer bronbestand en doelbestand in grootte verschillen, voor deltagegevensmigratie.

In de native integratiemodus van Data Factory is de meest performante manier om nieuwe of gewijzigde bestanden van HDFS te identificeren, met behulp van een naamgevingsconventie met tijdsverdeling. Wanneer uw gegevens in HDFS tijdsgebonden zijn met tijdsegmentgegevens in de bestands- of mapnaam (bijvoorbeeld */yyyy/mm/dd/file.csv),* kan uw pijplijn eenvoudig identificeren welke bestanden en mappen stapsgewijs moeten worden gekopieerd.

Als uw gegevens in HDFS niet worden verdeeld over de tijd, kan Data Factory nieuwe of gewijzigde bestanden identificeren met behulp van de waarde **LastModifiedDate.** Data Factory scant alle bestanden van HDFS en kopieert alleen nieuwe en bijgewerkte bestanden met een laatst gewijzigde tijdstempel die groter is dan een ingestelde waarde. 

Als u een groot aantal bestanden in HDFS hebt, kan het scannen van bestanden lang duren, ongeacht het aantal bestanden dat overeenkomt met de filtervoorwaarde. In dit scenario raden we u aan de gegevens eerst te verdelen met dezelfde partitie die u hebt gebruikt voor de eerste momentopnamemigratie. Vervolgens kan het scannen van bestanden parallel plaatsvinden.

### <a name="estimate-price"></a>Geschatte prijs 

Overweeg de volgende pijplijn voor het migreren van gegevens van HDFS naar Azure Blob-opslag: 

![Diagram met de prijspijplijn](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Laten we uitgaan van de volgende informatie: 

- Het totale gegevensvolume is 1 PB.
- U migreert gegevens met behulp van de runtime-modus Voor native integratie van Data Factory.
- 1 PB is verdeeld in 1.000 partities en elke kopie verplaatst één partitie.
- Elke kopieeractiviteit is geconfigureerd met één zelf gehoste runtime voor integratie die is gekoppeld aan vier machines en die een doorvoersnelheid van 500 MBps bereikt.
- ForEach gelijktijdigheid is ingesteld op **4** en de totale doorvoer is 2 GBps.
- In totaal duurt het 146 uur om de migratie te voltooien.

Hier is de geschatte prijs op basis van onze veronderstellingen: 

![Tabel met prijsberekeningen](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Dit is een hypothetisch prijsvoorbeeld. Uw werkelijke prijs hangt af van de werkelijke doorvoer in uw omgeving.
> De prijs voor een Azure Windows VM (met zelf gehoste runtime voor integratie is geïnstalleerd) is niet inbegrepen.

### <a name="additional-references"></a>Aanvullende naslaginformatie

- [HDFS-connector](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob-opslagconnector](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Tuning-handleiding voor activiteitsprestaties kopiëren](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Zelf-hostende Integration Runtime maken en configureren](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Self-hosted integratie runtime hoge beschikbaarheid en schaalbaarheid](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Beveiligingsoverwegingen bij het verplaatsen van gegevens](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Referenties opslaan in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Een bestand stapsgewijs kopiëren op basis van een tijdsverdelingsbestandsnaam](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Nieuwe en gewijzigde bestanden kopiëren op basis van LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Prijspagina Gegevensfabriek](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Volgende stappen

- [Bestanden uit meerdere containers kopiëren met Azure Data Factory](solution-template-copy-files-multiple-containers.md)