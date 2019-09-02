---
title: Gebruik Azure Data Factory voor het migreren van gegevens van een on-premises Hadoop-cluster naar Azure Storage | Microsoft Docs
description: Gebruik Azure Data Factory voor het migreren van gegevens van een on-premises Hadoop-cluster naar Azure Storage.
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
ms.date: 8/30/2019
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211609"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>Azure Data Factory gebruiken om gegevens van on-premises Hadoop-cluster te migreren naar Azure Storage 

Azure Data Factory biedt een krachtig, robuust en rendabel mechanisme voor het migreren van gegevens op schaal van on-premises HDFS naar Azure Blob Storage of Azure Data Lake Storage Gen2. In principe bevat Azure Data Factory twee benaderingen voor het migreren van gegevens van on-premises HDFS naar Azure. U kunt elk ervan selecteren op basis van uw scenario. 

- ADF DistCp-modus. De ADF-ondersteuning met behulp van [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) om bestanden te kopiëren naar Azure Blob (inclusief [gefaseerde kopie](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)) of Azure data Lake Store, in dat geval kan de stroom van het cluster volledig worden benut in plaats van op de ADF zelf-hostende Integration runtime (IR). Het biedt een betere Kopieer doorvoer, vooral als uw cluster zeer krachtig is. Op basis van uw configuratie in Azure Data Factory maakt Kopieer activiteit automatisch een DistCp-opdracht, wordt deze naar uw Hadoop-cluster verzonden en wordt de Kopieer status gecontroleerd. Met behulp van ADF geïntegreerd met DistCp kan de klant niet alleen gebruikmaken van uw bestaande krachtige cluster om de beste Kopieer doorvoer te bereiken, maar ook het voor deel van de flexibele planning en geïntegreerde bewakings ervaring van ADF. Standaard is de modus ADF DistCp de aanbevolen manier om gegevens van een on-premises Hadoop-cluster naar Azure te migreren.
- Systeem eigen IR-modus. In sommige gevallen kan DistCp niet werken voor uw aanvragen (bijvoorbeeld in de VNET-omgeving biedt DistCp-hulp programma geen ondersteuning voor Express route private peering + Azure Storage VNet-eind punt). U wilt niet alleen uw bestaande Hadoop-cluster gebruiken als de engine voor het migreren van de gegevens omdat hiermee zware belasting van het cluster wordt uitgevoerd. Dit kan van invloed zijn op de prestaties van bestaande ETL-taken. Als dat het geval is, kunt u de systeem eigen mogelijkheid van ADF gebruiken, waarbij u met de engine van ADF Integration runtime (IR) de gegevens van on-premises HDFS naar Azure kunt kopiëren.

Dit artikel bevat de volgende informatie over beide benaderingen voor gegevens technici en ontwikkel aars:
> [!div class="checklist"]
> * Prestaties 
> * Tolerantie kopiëren
> * Netwerkbeveiliging
> * Architectuur van oplossing op hoog niveau 
> * Aanbevolen procedures voor implementatie  

## <a name="performance"></a>Prestaties

In de modus ADF DistCp is de door Voer gelijk aan het gebruik van het DistCp-hulp programma onafhankelijk van de capaciteit van uw bestaande Hadoop-cluster. DistCp (gedistribueerde kopie) is een hulp programma dat wordt gebruikt voor het kopiëren van grote hoeveel heden tussen clusters. MapReduce maakt gebruik van de distributie, het afhandelen en herstellen van fouten en rapportage. Hiermee wordt een lijst met bestanden en mappen in de invoer uitgebreid om taken toe te wijzen, waarmee een partitie wordt gekopieerd van de bestanden die zijn opgegeven in de lijst Bron. Met behulp van ADF geïntegreerd met DistCp kunt u pijp lijnen bouwen om uw netwerk bandbreedte volledig te benutten, evenals opslag-IOPS en band breedte om de door Voer van gegevens verplaatsing voor uw omgeving te maximaliseren.  

In de native IR-modus van ADF maakt het ook parallelle uitvoering op verschillende niveaus mogelijk, waardoor uw netwerk bandbreedte volledig kan worden gebruikt en opslag-IOPS en band breedte om de door Voer van gegevens verplaatsing te maximaliseren door de zelf-hostende IR-computer hand matig te schalen of uit te breiden naar zelf-hostende IR meerdere computers.

- Eén Kopieer activiteit kan profiteren van schaal bare reken resources. Met de zelf-hostende Integration runtime kunt u de machine hand matig schalen of uitschalen naar meerdere machines ([Maxi maal 4 knoop punten](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), en met één Kopieer activiteit wordt de bestands set gepartitioneerd op alle knoop punten. 
- Eén Kopieer activiteit leest van en schrijft naar het gegevens archief met behulp van meerdere threads. 
- De controle stroom van ADF kan meerdere Kopieer activiteiten parallel starten, bijvoorbeeld [voor elke lus](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Meer informatie vindt u in de [hand leiding](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) voor de prestaties van de Kopieer activiteit

## <a name="resilience"></a>Flexibiliteit

In de ADF DistCp-modus kunt u gebruikmaken van verschillende DistCp-opdracht regel parameters (bijvoorbeeld-i, fouten negeren;-bijwerken, gegevens schrijven wanneer het bron bestand en het doel bestand een andere grootte hebben) om verschillende tolerantie niveaus te krijgen.

In de native IR-modus van ADF, binnen één exemplaar van een Kopieer activiteit, heeft ADF een ingebouwd mechanisme voor opnieuw proberen, zodat het een bepaald niveau van tijdelijke fouten in de gegevens archieven of in het onderliggende netwerk kan verwerken. Wanneer binaire kopieën worden gekopieerd van on-premises HDFS naar Blob en van on-premises HDFS naar ADLS Gen2, voert ADF automatisch controle punten in voor een groot gebied. Als er een fout is opgetreden > bij het uitvoeren van een Kopieer activiteit of als er een time-out optreedt, wordt het kopiëren hervat vanaf het laatste fout punt in plaats van vanaf het begin.

## <a name="network-security"></a>Netwerkbeveiliging 

Standaard brengt ADF gegevens over van on-premises HDFS naar Azure Blob Storage of Azure Data Lake Storage Gen2 met behulp van een versleutelde verbinding via het HTTPS-protocol.  HTTPS biedt gegevens versleuteling tijdens de overdracht en voor komt het afluis teren en man-in-the-middle-aanvallen. 

Als u niet wilt dat gegevens worden overgezet via het open bare Internet, kunt u de beveiliging verbeteren door gegevens over een koppeling van een privé-peering over te brengen via Azure Express route. Raadpleeg de onderstaande oplossings architectuur voor meer informatie over hoe dit kan worden bereikt.

## <a name="solution-architecture"></a>Oplossingsarchitectuur

Gegevens migreren via het open bare Internet:

![oplossing-architectuur-openbaar-netwerk](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- In deze architectuur worden gegevens veilig overgedragen met behulp van HTTPS via open bare Internet. 
- De modus ADF DistCp wordt aanbevolen voor gebruik in een open bare netwerk omgeving. Op die manier kunt u niet alleen gebruikmaken van uw bestaande krachtige cluster om de beste Kopieer doorvoer te bereiken, maar ook het voor deel van de flexibele planning en Unified monitoring-ervaring van ADF.
- U moet de zelf-hostende versie van ADF installeren op een Windows-computer achter de bedrijfs firewall voor het verzenden van de DistCp-opdracht naar uw Hadoop-cluster en het controleren van de Kopieer status. Op voor waarde dat deze computer geen gegevens kan verplaatsen (alleen voor controle doeleinden), is de capaciteit van de machine niet van invloed op de door Voer van gegevens verplaatsing.
- De bestaande para meters van de opdracht DistCp worden ondersteund.


Gegevens migreren via een persoonlijke koppeling: 

![oplossing-architectuur-privé-netwerk](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- In deze architectuur wordt de gegevens migratie uitgevoerd via een koppeling voor een persoonlijke peering via een Azure Express-route zodat de gegevens nooit via het open bare Internet worden gepasseerd.
- Het DistCp-hulp programma biedt geen ondersteuning voor Express route private peering + Azure Storage VNet-eind punt, dus u wordt geadviseerd om de automatische ADF-functie te gebruiken via Integration runtime voor het migreren van de gegevens.
- U moet de zelf-hostende Integration runtime van ADF installeren op een Windows-VM in uw virtuele Azure-netwerk om deze architectuur te verzorgen. U kunt uw virtuele machine hand matig schalen of uitschalen naar meerdere Vm's om uw netwerk en opslag-IOPS/band breedte volledig te benutten.
- De aanbevolen configuratie om te beginnen voor elke Azure VM (met ADF zelf-hostende Integration runtime) is Standard_D32s_v3 met 32 vCPU en 128 GB geheugen. U kunt tijdens de gegevens migratie het CPU-en geheugen gebruik van de virtuele machine blijven bewaken om te zien of u de virtuele machine verder moet opschalen voor betere prestaties of de virtuele machine kunt schalen om kosten te besparen.
- U kunt ook uitschalen door Maxi maal 4 VM-knoop punten te koppelen aan één zelf-hostende IR. Een enkele Kopieer taak die wordt uitgevoerd op een zelf-hostende IR, partitioneert automatisch de set bestanden en maakt gebruik van alle VM-knoop punten om de bestanden parallel te kopiëren. Voor maximale Beschik baarheid kunt u het beste beginnen met twee VM-knoop punten om Single Point of Failure te voor komen tijdens de gegevens migratie.
- U kunt zowel de initiële momentopname gegevens migratie als de migratie van de Delta gegevens bereiken met behulp van deze architectuur.


## <a name="implementation-best-practices"></a>Aanbevolen procedures voor implementatie 

### <a name="authentication-and-credential-management"></a>Verificatie en referentie beheer 

- Als u wilt verifiëren op HDFS, kunt u [Windows (Kerberos) of anoniem](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)gebruiken. 
- Meerdere verificatie typen worden ondersteund om verbinding te maken met Azure Blob Storage.  Het gebruik van [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) wordt ten zeerste aanbevolen: gebouwd op basis van een automatisch beheerde ADF-zoek opdracht in azure AD, kunt u pijp lijnen configureren zonder dat er referenties worden opgegeven in de definitie van de gekoppelde service.  U kunt ook verifiëren bij Azure Blob Storage met behulp van de [Service-Principal](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), de [hand tekening voor gedeelde toegang](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)of de sleutel van het [opslag account](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Meerdere verificatie typen worden ook ondersteund om verbinding te maken met Azure Data Lake Storage Gen2.  Het gebruik van [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) wordt sterk aanbevolen, hoewel de sleutel van de [Service-Principal](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) of het [opslag account](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) ook kan worden gebruikt. 
- Wanneer u geen beheerde identiteiten voor Azure-resources gebruikt, wordt [het opslaan van de referenties in azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) ten zeerste aanbevolen, zodat het eenvoudiger is om sleutels te beheren en te draaien zonder de gekoppelde services van ADF aan te passen.  Dit is ook een van de [Aanbevolen procedures voor CI/cd](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Initiële momentopname gegevens migratie 

In de ADF DistCp-modus kunt u één Kopieer activiteit maken om de DistCp-opdracht met verschillende para meters te verzenden om het initiële gedrag van gegevens migratie te bepalen. 

In de native IR-modus van ADF wordt de gegevens partitie vooral aanbevolen bij het migreren van meer dan 10 TB aan gegevens. Als u de gegevens wilt partitioneren, maakt u gebruik van de mapnamen op HDFS, waarna elke taak voor het kopiëren van een ADF per keer één mappen partitie kan kopiëren. U kunt gelijktijdig meerdere ADF-Kopieer taken uitvoeren voor een betere door voer.
Als een van de Kopieer taken mislukt als gevolg van een probleem met het tijdelijk netwerk of het gegevens archief, kunt u de mislukte Kopieer taak opnieuw uitvoeren om deze specifieke partitie opnieuw uit HDFS te laden. Alle andere Kopieer taken voor het laden van andere partities worden niet beïnvloed.

### <a name="delta-data-migration"></a>Delta gegevens migratie 

In de ADF DistCp-modus kunt u gebruikmaken van DistCp-opdracht regel parameters "-bijwerken, gegevens schrijven wanneer het bron bestand en het doel bestand een andere grootte hebben" om de Delta gegevens migratie te verzorgen.

In de systeem eigen IR-modus van ADF is de meest krachtige manier om nieuwe of gewijzigde bestanden van HDFS te identificeren door gebruik te maken van tijdgebonden naamgevings regels. als uw gegevens in HDFS gepartitioneerd zijn met tijd segment informatie in de naam van het bestand of de map (bijvoorbeeld/yyyy/mm/dd/ File. CSV), kan de pijp lijn eenvoudig bepalen welke bestanden/mappen incrementeel moeten worden gekopieerd.
Als uw gegevens in HDFS niet gepartitioneerd zijn, kan ADF nieuwe of gewijzigde bestanden identificeren met hun LastModifiedDate. Zoals u ziet, scant de ADF alle bestanden van HDFS en kopieert alleen het nieuwe en bijgewerkte bestand waarvan het tijdstip waarop de laatste wijziging is aangebracht, groter is dan een bepaalde waarde. Houd er rekening mee dat als u een groot aantal bestanden in HDFS hebt, de eerste scan van het bestand veel tijd kan duren, ongeacht het aantal bestanden dat overeenkomt met de filter voorwaarde. In dit geval wordt u geadviseerd om eerst de gegevens te partitioneren, waarbij u dezelfde partitie gebruikt voor de initiële migratie van moment opnamen, zodat het scannen van bestanden parallel kan plaatsvinden.

### <a name="estimating-price"></a>Prijs schatten 

Bekijk de volgende pijp lijn die is gebouwd voor het migreren van gegevens van HDFS naar Azure Blob Storage: 

![prijs informatie-pijp lijn](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

We nemen het volgende uit: 

- Het totale gegevens volume is 1 PB
- Gegevens migreren met de tweede oplossings architectuur (ADF native IR-modus)
- 1 PB is onderverdeeld in 1000-partities en elke kopie verplaatst één partitie
- Elke Kopieer activiteit wordt geconfigureerd met één zelf-hostende IR die is gekoppeld aan 4 computers en de door Voer van 500 MBps wordt gerealiseerd.
- Gelijktijdigheid van ForEach is ingesteld op 4 en een geaggregeerde door Voer is 2 GBps
- In totaal duurt het 146 uur om de migratie te volt ooien.


Dit is de geschatte prijs op basis van de bovenstaande hypo Thesen: 

![prijs informatie: tabel](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Dit is een voor beeld van een hypothetische prijs.  Uw werkelijke prijs is afhankelijk van de werkelijke door Voer in uw omgeving.
> De prijs voor Azure Windows VM (met zelf-hostende Integration runtime) is niet opgenomen.

### <a name="additional-references"></a>Aanvullende naslaginformatie 
- [HDFS-connector](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob Storage-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Gids voor het afstemmen van de activiteit prestaties kopiëren](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Zelf-hostende Integration Runtime maken en configureren](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Zelf-hostende runtime HA en schaal baarheid](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Beveiligings overwegingen voor gegevens verplaatsing](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Referenties opslaan in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Bestand incrementeel kopiëren op basis van een gepartitioneerde bestands naam](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Nieuwe en gewijzigde bestanden kopiëren op basis van LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Pagina met prijzen voor ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Volgende stappen

- [Bestanden van meerdere containers met Azure Data Factory kopiëren](solution-template-copy-files-multiple-containers.md)