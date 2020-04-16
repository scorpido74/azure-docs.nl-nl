---
title: Gegevens migreren van Amazon S3 naar Azure Storage
description: Gebruik Azure Data Factory om gegevens van Amazon S3 naar Azure Storage te migreren.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 3f40ad7346219b48a38ade38b2a75ddf71940875
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416420"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Azure Data Factory gebruiken om gegevens van Amazon S3 naar Azure Storage te migreren 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory biedt een performant, robuust en kosteneffectief mechanisme om gegevens op schaal te migreren van Amazon S3 naar Azure Blob Storage of Azure Data Lake Storage Gen2.  In dit artikel vindt u de volgende informatie voor gegevenstechnici en ontwikkelaars: 

> [!div class="checklist"]
> * Prestaties 
> * Veerkracht kopiëren
> * Netwerkbeveiliging
> * Oplossingsarchitectuur op hoog niveau 
> * Best Practices voor de implementatie  

## <a name="performance"></a>Prestaties

ADF biedt een serverloze architectuur die parallellisme op verschillende niveaus mogelijk maakt, waardoor ontwikkelaars pijplijnen kunnen bouwen om uw netwerkbandbreedte volledig te benutten, evenals opslag-IOPS en bandbreedte om de doorvoer van gegevensverkeer voor uw omgeving te maximaliseren. 

Klanten hebben met succes petabytes aan gegevens gemigreerd, bestaande uit honderden miljoenen bestanden van Amazon S3 naar Azure Blob Storage, met een aanhoudende doorvoer van 2 GBps en hoger. 

![prestaties](media/data-migration-guidance-s3-to-azure-storage/performance.png)

De afbeelding hierboven illustreert hoe u grote databewegingssnelheden bereiken door verschillende niveaus van parallellisme:
 
- Eén kopieeractiviteit kan profiteren van schaalbare rekenbronnen: wanneer u Azure Integration Runtime gebruikt, u [maximaal 256 DIU's](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) voor elke kopieeractiviteit op een serverloze manier opgeven; wanneer u zelf gehoste Implementatieruntime gebruikt, u de machine handmatig opschalen of uitschalen naar meerdere machines[(maximaal 4 knooppunten)](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)en wordt de bestandsset met één kopieactiviteit verdeeld over alle knooppunten. 
- Een enkele kopie activiteit leest van en schrijft naar het gegevensarchief met behulp van meerdere threads. 
- ADF-besturingsstroom kan meerdere kopieeractiviteiten parallel starten, bijvoorbeeld met [Voor Elke lus](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Veerkracht

Binnen één exemplaaractiviteitsrun heeft ADF een ingebouwd mechanisme voor nieuwe apparaten, zodat het een bepaald niveau van tijdelijke fouten in de gegevensarchieven of in het onderliggende netwerk kan verwerken. 

Bij het kopiëren van binaire teksten van S3 naar Blob en van S3 naar ADLS Gen2 voert ADF automatisch controlepunten uit.  Als een kopieeractiviteit is mislukt of een time-out heeft opgetreden, wordt de kopie bij een volgende poging hervat vanaf het laatste foutpunt in plaats van vanaf het begin te beginnen. 

## <a name="network-security"></a>Netwerkbeveiliging 

ADF draagt standaard gegevens over van Amazon S3 naar Azure Blob Storage of Azure Data Lake Storage Gen2 via een versleutelde verbinding via het HTTPS-protocol.  HTTPS biedt data-encryptie tijdens het transport en voorkomt afluisteren en man-in-the-middle-aanvallen. 

Als u niet wilt dat gegevens via openbaar internet worden overgedragen, u ook een hogere beveiliging bereiken door gegevens over te dragen via een privé-peeringkoppeling tussen AWS Direct Connect en Azure Express Route.  Raadpleeg hieronder de oplossingsarchitectuur over hoe dit kan worden bereikt. 

## <a name="solution-architecture"></a>Architectuur voor de oplossing

Gegevens migreren via openbaar internet:

![solution-architecture-public-network](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- In deze architectuur worden gegevens veilig overgedragen via HTTPS via openbaar internet. 
- Zowel de bron Amazon S3 als de bestemming Azure Blob Storage of Azure Data Lake Storage Gen2 zijn geconfigureerd om verkeer toe te staan vanaf alle NETWERK-IP-adressen.  Raadpleeg de tweede architectuur hieronder over hoe u de toegang tot het netwerk beperken tot een specifiek IP-bereik. 
- U eenvoudig de hoeveelheid pk's op serverloze manier opschalen om uw netwerk- en opslagbandbreedte volledig te benutten, zodat u de beste doorvoer voor uw omgeving krijgen. 
- Zowel de eerste momentopnamemigratie als deltagegevensmigratie kunnen met deze architectuur worden bereikt. 

Gegevens migreren via een privékoppeling: 

![solution-architecture-private-network](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- In deze architectuur wordt gegevensmigratie uitgevoerd via een privé-peeringkoppeling tussen AWS Direct Connect en Azure Express Route, zodat gegevens nooit via openbaar internet worden doorkruisen.  Het vereist het gebruik van AWS VPC en Azure Virtual netwerk. 
- U moet adf zelf gehoste integratieruntime installeren op een Windows VM binnen uw Virtuele Azure-netwerk om deze architectuur te bereiken.  U uw zelf gehoste IR-VM's handmatig opschalen of uitschalen naar meerdere VM's (maximaal 4 knooppunten) om uw netwerk- en opslag-IOPS/-bandbreedte volledig te gebruiken. 
- Als het aanvaardbaar is om gegevens over HTTPS over te dragen, maar u wilt netwerktoegang tot bron S3 vergrendelen naar een specifiek IP-bereik, u een variatie van deze architectuur aannemen door AWS VPC te verwijderen en privékoppeling te vervangen door HTTPS.  U wilt Azure Virtual en zelfgehoste IR op Azure VM houden, zodat u een statisch openbaar routeerbaar IP hebben voor whitelistingdoeleinden. 
- Zowel de eerste momentopnamegegevensmigratie als deltagegevensmigratie kunnen met deze architectuur worden bereikt. 

## <a name="implementation-best-practices"></a>Best Practices voor de implementatie 

### <a name="authentication-and-credential-management"></a>Verificatie- en referentiebeheer 

- Als u wilt verifiëren voor amazon S3-account, moet u [de toegangssleutel voor het IAM-account](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties)gebruiken. 
- Meerdere verificatietypen worden ondersteund om verbinding te maken met Azure Blob Storage.  Het gebruik van [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) wordt ten zeerste aanbevolen: bovenop een automatisch beheerde ADF-identificatie in Azure AD, u pijplijnen configureren zonder referenties te leveren in de definitie van Linked Service.  U ook verifiëren naar Azure Blob Storage met [serviceprincipal,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [handtekening voor gedeelde toegang](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)of [opslagaccountsleutel](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Er worden ook meerdere verificatietypen ondersteund om verbinding te maken met Azure Data Lake Storage Gen2.  Het gebruik van [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) wordt ten zeerste aanbevolen, hoewel [serviceprincipal](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) of [opslagaccountsleutel](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) ook kan worden gebruikt. 
- Wanneer u beheerde identiteiten niet gebruikt voor Azure-resources, wordt [het opslaan van de referenties in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) ten zeerste aanbevolen om het eenvoudiger te maken om sleutels centraal te beheren en te roteren zonder adf-gekoppelde services te wijzigen.  Dit is ook een van de [best practices voor CI / CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migratie van eerste momentopnamegegevens 

Gegevenspartitie wordt aanbevolen, vooral bij het migreren van meer dan 100 TB aan gegevens.  Als u de gegevens wilt verdelen, maakt u gebruik van de instelling 'voorvoegsel' om de mappen en bestanden in Amazon S3 op naam te filteren en kan elke ADF-kopieertaak één partitie tegelijk kopiëren.  U meerdere ADF-kopieertaken tegelijk uitvoeren voor een betere doorvoer. 

Als een van de kopieertaken mislukt als gevolg van een tijdelijk probleem in het netwerk- of gegevensarchief, u de mislukte kopieertaak opnieuw uitvoeren om die specifieke partitie opnieuw te laden vanuit AWS S3.  Alle andere kopieertaken die andere partities laden, worden niet beïnvloed. 

### <a name="delta-data-migration"></a>Delta-gegevensmigratie 

De meest performante manier om nieuwe of gewijzigde bestanden van AWS S3 te identificeren, is door gebruik te maken van de tijdsverdelingsconventie - wanneer uw gegevens in AWS S3 tijd zijn verdeeld met tijdsegmentinformatie in het bestand of de naam van de map (bijvoorbeeld /yyyy/mm/dd/file.csv), kan uw pijplijn eenvoudig identificeren welke bestanden/mappen stapsgewijs moeten worden gekopieerd. 

Als uw gegevens in AWS S3 geen tijd salmals zijn verdeeld, kan ADF nieuwe of gewijzigde bestanden identificeren aan de basis van de LastModifiedDate.   De manier waarop het werkt is dat ADF alle bestanden van AWS S3 scant en alleen het nieuwe en bijgewerkte bestand kopieert waarvan de laatste gewijzigde tijdstempel groter is dan een bepaalde waarde.  Houd er rekening mee dat als u een groot aantal bestanden in S3 hebt, het scannen van bestanden lang kan duren, ongeacht het aantal bestanden dat overeenkomt met de filterconditie.  In dit geval wordt u aangeraden om de gegevens eerst te verdelen, met dezelfde 'voorvoegsel'-instelling voor de eerste momentopnamemigratie, zodat het scannen van bestanden parallel kan plaatsvinden.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Voor scenario's waarvoor zelfgehoste integratie-runtime op Azure VM vereist is 

Of u nu gegevens migreert via een privékoppeling of een specifiek IP-bereik op de Amazon S3-firewall wilt toestaan, u moet zelf gehoste runtime voor integratie installeren op Azure Windows VM. 

- De aanbevolen configuratie om mee te beginnen voor elke Azure VM is Standard_D32s_v3 met 32 vCPU en 128 GB geheugen.  U het CPU- en geheugengebruik van de IR-VM tijdens de gegevensmigratie blijven controleren om te zien of u de VM verder moet opschalen voor betere prestaties of de VM moet verkleinen om kosten te besparen. 
- U ook uitschalen door maximaal 4 VM-knooppunten te koppelen aan één zelf gehoste IR.  Een enkele kopieertaak die wordt uitgevoerd tegen een zelf gehoste IR, partitiet automatisch de bestandsset en maakt gebruik van alle VM-knooppunten om de bestanden parallel te kopiëren.  Voor hoge beschikbaarheid wordt u aangeraden om te beginnen met 2 VM-knooppunten om één storingspunt tijdens de gegevensmigratie te voorkomen. 

### <a name="rate-limiting"></a>Snelheidsbeperking 

Voer als best practice een performance POC uit met een representatieve voorbeeldgegevensset, zodat u een geschikte partitiegrootte bepalen. 

Begin met één partitie en één kopieeractiviteit met de standaard DIU-instelling.  Verhoog geleidelijk de DIU-instelling totdat u de bandbreedtelimiet van uw netwerk of IOPS/bandbreedtelimiet van de gegevensopslag hebt bereikt, of u hebt de maximaal toegestane 256 DIU bereikt voor één exemplaaractiviteit. 

Verhoog vervolgens geleidelijk het aantal gelijktijdige kopieeractiviteiten totdat u limieten van uw omgeving bereikt. 

Wanneer u throttling-fouten tegenkomt die worden gerapporteerd door ADF-kopieeractiviteit, vermindert u de gelijktijdigheid of diu-instelling in ADF of overweegt u de bandbreedte/IOPS-limieten van de netwerk- en gegevensopslag te verhogen.  

### <a name="estimating-price"></a>Prijs schatten 

> [!NOTE]
> Dit is een hypothetisch prijsvoorbeeld.  Uw werkelijke prijs hangt af van de werkelijke doorvoer in uw omgeving.

Overweeg de volgende pijplijn die is gemaakt voor het migreren van gegevens van S3 naar Azure Blob Storage: 

![pricing-pipeline](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Laten we uitgaan van het volgende: 

- Het totale gegevensvolume is 2 PB 
- Gegevens migreren via HTTPS met behulp van de eerste oplossingsarchitectuur 
- 2 PB is verdeeld in 1 K partities en elke kopie verplaatst een partitie 
- Elk exemplaar is geconfigureerd met DIU=256 en bereikt 1 GBps-doorvoer 
- ForEach gelijktijdigheid is ingesteld op 2 en de totale doorvoer is 2 GBps 
- In totaal duurt het 292 uur om de migratie te voltooien 

Hier is de geschatte prijs op basis van de bovenstaande veronderstellingen: 

![prijstabel](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Aanvullende naslaginformatie 
- [Amazon Simple Storage Service-connector](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob-opslagconnector](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Tuning-handleiding voor activiteitsprestaties kopiëren](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Runtime voor zelfgehoste integratie maken en configureren](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Self-hosted integration runtime HA en schaalbaarheid](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Beveiligingsoverwegingen bij het verplaatsen van gegevens](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Referenties opslaan in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Bestand stapsgewijs kopiëren op basis van tijdpartitiebestandsnaam](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Nieuwe en gewijzigde bestanden kopiëren op basis van LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF-prijspagina](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Template

Hier is de [sjabloon](solution-template-migration-s3-azure.md) om mee te beginnen met het migreren van petabytes aan gegevens bestaande uit honderden miljoenen bestanden van Amazon S3 naar Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Volgende stappen

- [Bestanden uit meerdere containers kopiëren met Azure Data Factory](solution-template-copy-files-multiple-containers.md)
