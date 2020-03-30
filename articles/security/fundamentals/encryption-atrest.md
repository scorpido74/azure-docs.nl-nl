---
title: Microsoft Azure-gegevensversleuteling-at-rest | Microsoft Documenten
description: In dit artikel vindt u een overzicht van microsoft Azure-gegevensversleuteling in rust, de algemene mogelijkheden en algemene overwegingen.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: barclayn
ms.openlocfilehash: d8aa643dcf9734ac983c9c4c0d53bda24ce4688d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125074"
---
# <a name="azure-data-encryption-at-rest"></a>Azure-gegevensversleuteling-at-rest

Microsoft Azure bevat hulpprogramma's om gegevens te beveiligen op basis van de beveiligings- en nalevingsbehoeften van uw bedrijf. Deze paper richt zich op:

- Hoe gegevens in rust worden beschermd in Microsoft Azure
- Bespreekt de verschillende onderdelen die deelnemen aan de implementatie van gegevensbescherming,
- Beoordelingen voors en tegens van de verschillende key management bescherming benaderingen.

Versleuteling in rust is een veelvoorkomende beveiligingsvereiste. In Azure kunnen organisaties gegevens in rust versleutelen zonder het risico of de kosten van een aangepaste oplossing voor sleutelbeheer. Organisaties hebben de mogelijkheid om Azure encryptie in rust volledig te laten beheren. Daarnaast hebben organisaties verschillende opties om encryptie- of encryptiesleutels nauwkeurig te beheren.

## <a name="what-is-encryption-at-rest"></a>Wat is encryptie in rust?

Encryptie at Rest is de codering (encryptie) van gegevens wanneer deze worden gehandhaafd. De encryptie bij rust ontwerpen in Azure gebruiken symmetrische encryptie te versleutelen en te decoderen grote hoeveelheden gegevens snel volgens een eenvoudig conceptueel model:

- Een symmetrische encryptiesleutel wordt gebruikt om gegevens te versleutelen terwijl deze naar opslag wordt geschreven.
- Dezelfde encryptiesleutel wordt gebruikt om die gegevens te decoderen omdat deze worden gereadied voor gebruik in het geheugen.
- Gegevens kunnen worden verdeeld en voor elke partitie kunnen verschillende sleutels worden gebruikt.
- Sleutels moeten worden opgeslagen op een veilige locatie met identiteitsgebaseerdtoegangscontrole en controlebeleid. Gegevensversleutelingssleutels worden vaak versleuteld met een sleutelversleutelingssleutel in Azure Key Vault om de toegang verder te beperken.

In de praktijk vereisen belangrijke beheer- en controlescenario's, evenals schaal- en beschikbaarheidsgaranties, extra constructies. Microsoft Azure Encryption at Rest-concepten en -componenten worden hieronder beschreven.

## <a name="the-purpose-of-encryption-at-rest"></a>Het doel van encryptie in rust

Versleuteling in rust biedt gegevensbescherming voor opgeslagen gegevens (in rust). Aanvallen op gegevens in rust omvatten pogingen om fysieke toegang te verkrijgen tot de hardware waarop de gegevens worden opgeslagen, en vervolgens de opgenomen gegevens in gevaar te brengen. Bij een dergelijke aanval kan de harde schijf van een server verkeerd zijn behandeld tijdens onderhoud, waardoor een aanvaller de harde schijf kan verwijderen. Later zou de aanvaller zet de harde schijf in een computer onder hun controle om te proberen toegang te krijgen tot de gegevens.

Versleuteling in rust is ontworpen om te voorkomen dat de aanvaller toegang krijgt tot de onversleutelde gegevens door ervoor te zorgen dat de gegevens worden versleuteld wanneer ze op de schijf zijn. Als een aanvaller een harde schijf met versleutelde gegevens, maar niet de encryptiesleutels verkrijgt, moet de aanvaller de versleuteling verslaan om de gegevens te lezen. Deze aanval is veel complexer en resource verbruikendan toegang tot onversleutelde gegevens op een harde schijf. Om deze reden, encryptie in rust is sterk aanbevolen en is een hoge prioriteit vereiste voor veel organisaties.

Versleuteling in rust kan ook vereist zijn door de behoefte van een organisatie aan gegevensbeheer en compliance-inspanningen. Industrie- en overheidsvoorschriften zoals HIPAA, PCI en FedRAMP bevatten specifieke waarborgen met betrekking tot gegevensbescherming en versleutelingsvereisten. Encryptie in rust is een verplichte maatregel die nodig is voor de naleving van een aantal van deze voorschriften. Zie [Federal Information Processing Standard (FIPS) Publication 140-2 voor](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2)meer informatie over de benadering van FIPS 140-2 van Microsoft. 

Naast het voldoen aan naleving seinen en wettelijke vereisten, biedt versleuteling in rust bescherming-diepgaande bescherming. Microsoft Azure biedt een compatibel platform voor services, toepassingen en gegevens. Het biedt ook uitgebreide faciliteit en fysieke beveiliging, controle over gegevenstoegangscontrole en controle. Het is echter belangrijk om extra "overlappende" beveiligingsmaatregelen te bieden in het geval een van de andere beveiligingsmaatregelen mislukt en encryptie in rust biedt een dergelijke beveiligingsmaatregel.

Microsoft zet zich in voor versleuteling bij rustopties in cloudservices en geeft klanten controle over versleutelingssleutels en logboeken van sleutelgebruik. Daarnaast werkt Microsoft aan het standaard versleutelen van alle klantgegevens in rust.

## <a name="azure-encryption-at-rest-components"></a>Azure-versleuteling bij restcomponenten

Zoals eerder beschreven, het doel van encryptie in rust is dat gegevens die wordt aangehouden op de schijf wordt versleuteld met een geheime encryptiesleutel. Om dat doel te bereiken moet het maken, opslaan, toegangscontrole en beheer van de versleutelingssleutels worden verstrekt om dat doel te bereiken. Hoewel details kunnen variëren, kunnen implementaties voor Versleuteling van Azure-services bij Rust worden beschreven in termen die worden geïllustreerd in het volgende diagram.

![Onderdelen](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

De opslaglocatie van de encryptiesleutels en toegangsbeheer tot deze sleutels staat centraal in een coderingsmodel in rust. De sleutels moeten hoog beveiligd zijn, maar beheersbaar zijn door bepaalde gebruikers en beschikbaar zijn voor specifieke services. Voor Azure-services is Azure Key Vault de aanbevolen oplossing voor sleutelopslag en biedt het een gemeenschappelijke beheerervaring voor alle services. Sleutels worden opgeslagen en beheerd in sleutelkluizen en toegang tot een sleutelkluis kan worden gegeven aan gebruikers of services. Azure Key Vault ondersteunt het maken van sleutels door klanten of het importeren van klantsleutels voor gebruik in door de klant beheerde coderingssleutelscenario's.

### <a name="azure-active-directory"></a>Azure Active Directory

Machtigingen voor het gebruik van de sleutels die zijn opgeslagen in Azure Key Vault, voor het beheren of openen van versleuteling voor versleuteling en decryptie, kunnen worden gegeven aan Azure Active Directory-accounts.

### <a name="key-hierarchy"></a>Sleutelhiërarchie

Meer dan één versleutelingssleutel wordt gebruikt in een encryptie-at-rest-implementatie. Het opslaan van een versleutelingssleutel in Azure Key Vault zorgt voor veilige toegang tot sleutels en centraal beheer van sleutels. Echter, service lokale toegang tot encryptiesleutels is efficiënter voor bulk encryptie en decryptie dan interactie met Key Vault voor elke gegevensoperatie, waardoor voor sterkere encryptie en betere prestaties. Het beperken van het gebruik van een enkele encryptiesleutel vermindert het risico dat de sleutel wordt aangetast en de kosten van herversleuteling wanneer een sleutel moet worden vervangen. Azure-versleutelingen bij rustmodellen maken gebruik van een sleutelhiërarchie die bestaat uit de volgende typen sleutels om aan al deze behoeften te voldoen:

- **Data Encryption Key (DEK)** – Een symmetrische AES256-sleutel die wordt gebruikt om een partitie of blok gegevens te versleutelen.  Eén bron kan veel partities en veel gegevensversleutelingssleutels bevatten. Het versleutelen van elk blok gegevens met een andere sleutel maakt crypto-analyseaanvallen moeilijker. Toegang tot DEKs is nodig door de resourceprovider of toepassingsinstantie die een specifiek blok versleutelt en ontsleutelt. Wanneer een DEK wordt vervangen door een nieuwe sleutel, moeten alleen de gegevens in het bijbehorende blok opnieuw worden versleuteld met de nieuwe sleutel.
- **Key Encryption Key (KEK)** – Een encryptiesleutel die wordt gebruikt om de gegevensversleutelingssleutels te versleutelen. Met het gebruik van een sleutelversleutelingssleutel die Key Vault nooit verlaat, kunnen de gegevensversleutelingssleutels zelf worden versleuteld en gecontroleerd. De entiteit die toegang heeft tot het KEK kan anders zijn dan de entiteit die de DEK nodig heeft. Een entiteit kan toegang tot de DEK bemiddelen om de toegang van elke DEK tot een specifieke partitie te beperken. Aangezien de KEK nodig is om de DEKs te decoderen, is de KEK in feite een enkel punt waarmee DEKs effectief kunnen worden verwijderd door verwijdering van de KEK.

De gegevensversleutelingssleutels, versleuteld met de sleutelversleutelingssleutels, worden afzonderlijk opgeslagen en alleen een entiteit met toegang tot de sleutelversleutelingssleutel kan deze gegevensversleutelingssleutels decoderen. Verschillende modellen van sleutelopslag worden ondersteund. We zullen elk model later in het volgende gedeelte in meer detail bespreken.

## <a name="data-encryption-models"></a>Gegevensversleutelingsmodellen

Een goed begrip van de verschillende versleutelingsmodellen en hun voor- en nadelen is essentieel om te begrijpen hoe de verschillende resourceproviders in Azure versleuteling in Rest implementeren. Deze definities worden gedeeld tussen alle resourceproviders in Azure om gemeenschappelijke taal en taxonomie te garanderen.

Er zijn drie scenario's voor server-side encryptie:

- Versleuteling aan de serverzijde met servicebeheerde sleutels
  - Azure Resource Providers voeren de versleutelings- en decryptiebewerkingen uit
  - Microsoft beheert de sleutels
  - Volledige cloudfunctionaliteit

- Versleuteling aan de serverzijde met door de klant beheerde sleutels in Azure Key Vault
  - Azure Resource Providers voeren de versleutelings- en decryptiebewerkingen uit
  - Sleutels voor klantcontroles via Azure Key Vault
  - Volledige cloudfunctionaliteit

- Server-side encryptie met behulp van door de klant beheerde sleutels op door de klant gecontroleerde hardware
  - Azure Resource Providers voeren de versleutelings- en decryptiebewerkingen uit
  - Sleutels van klantcontroles op door de klant gecontroleerde hardware
  - Volledige cloudfunctionaliteit

Voor versleuteling aan de clientzijde u rekening houden met het volgende:

- Azure-services kunnen gedecodeerde gegevens niet zien
- Klanten beheren en slaan sleutels on-premises (of in andere beveiligde winkels). Sleutels zijn niet beschikbaar voor Azure-services
- Minder cloudfunctionaliteit

De ondersteunde versleutelingsmodellen in Azure splitsen zich op in twee hoofdgroepen: 'Clientencryption' en 'Server-side Encryption' zoals eerder vermeld. Onafhankelijk van het gebruikte versleutelingsmodel, raden Azure-services altijd het gebruik van een veilig transport zoals TLS of HTTPS aan. Daarom moet versleuteling in het vervoer worden aangepakt door het transportprotocol en mag het geen belangrijke factor zijn bij het bepalen welke encryptie in rustmodel moet worden gebruikt.

### <a name="client-encryption-model"></a>Clientversleutelingsmodel

Clientversleutelingsmodel verwijst naar versleuteling die buiten de ResourceProvider of Azure wordt uitgevoerd door de service- of aanroepende toepassing. De versleuteling kan worden uitgevoerd door de servicetoepassing in Azure of door een toepassing die wordt uitgevoerd in het datacenter van de klant. In beide gevallen ontvangt de Azure Resource Provider bij het gebruik van dit versleutelingsmodel een versleutelde blob met gegevens zonder de mogelijkheid om de gegevens op enigerlei wijze te decoderen of toegang te hebben tot de versleutelingssleutels. In dit model wordt het sleutelbeheer uitgevoerd door de aanroepende service/toepassing en is het ondoorzichtig voor de Azure-service.

![Client](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Versleutelingsmodel aan de serverzijde

Versleutelingsmodellen aan de serverzijde verwijzen naar versleuteling die wordt uitgevoerd door de Azure-service. In dat model voert de Resource Provider de versleutelings- en decoderende bewerkingen uit. Azure Storage kan bijvoorbeeld gegevens ontvangen in platte tekstbewerkingen en de versleuteling en decryptie intern uitvoeren. De Resourceprovider kan versleutelingssleutels gebruiken die worden beheerd door Microsoft of door de klant, afhankelijk van de opgegeven configuratie.

![server](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Beheermodellen aan de serverzijde

Elk van de server-side encryptie bij rust modellen impliceert onderscheidende kenmerken van key management. Dit omvat waar en hoe encryptiesleutels worden gemaakt en opgeslagen, evenals de toegangsmodellen en de sleutelrotatieprocedures.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Versleuteling aan de serverzijde met servicebeheerde sleutels

Voor veel klanten is de essentiële eis om ervoor te zorgen dat de gegevens worden versleuteld wanneer deze in rust zijn. Server-side encryptie met behulp van service-managed Keys maakt dit model mogelijk door klanten in staat te stellen de specifieke bron (Storage Account, SQL DB, enz.) te markeren voor versleuteling en alle belangrijke beheeraspecten over te laten, zoals sleuteluitgifte, rotatie en back-up naar Microsoft . De meeste Azure Services die versleuteling in rust ondersteunen, ondersteunen doorgaans dit model van het ontladen van het beheer van de versleutelingssleutels naar Azure. De Azure-resourceprovider maakt de sleutels, plaatst ze in beveiligde opslag en haalt ze op wanneer dat nodig is. Dit betekent dat de service volledige toegang heeft tot de sleutels en dat de service volledige controle heeft over het beheer van de referentielevenscyclus.

![Beheerd](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

Server-side encryptie met behulp van service-managed keys dus snel adressen de noodzaak om encryptie in rust met lage overhead aan de klant. Wanneer een klant beschikbaar is, opent hij doorgaans de Azure-portal voor de doelabonnements- en resourceprovider en controleert een vak met de vermelding dat de gegevens moeten worden versleuteld. In sommige Resource Managers server-side encryptie met service-managed sleutels is standaard ingeschakeld.

Server-side encryptie met door Microsoft beheerde sleutels impliceert wel dat de service volledige toegang heeft tot het opslaan en beheren van de sleutels. Hoewel sommige klanten de sleutels willen beheren omdat ze het gevoel hebben dat ze meer beveiliging krijgen, moeten de kosten en het risico die gepaard gaan met een aangepaste sleutelopslagoplossing worden overwogen bij de evaluatie van dit model. In veel gevallen kan een organisatie bepalen dat resourcebeperkingen of risico's van een on-premises oplossing groter kunnen zijn dan het risico van cloudbeheer van de versleuteling bij rustsleutels.  Dit model is echter mogelijk niet voldoende voor organisaties die vereisten hebben om de creatie of levenscyclus van de versleutelingssleutels te beheren of om verschillende personeelsleden de versleutelingssleutels van een service te laten beheren dan organisaties die de service beheren (dat wil zeggen segregatie van het sleutelbeheer van het algemene managementmodel voor de service).

##### <a name="key-access"></a>Toegang tot sleutels

Wanneer server-side encryptie met servicebeheerde sleutels wordt gebruikt, worden de sleutelcreatie, -opslag en servicetoegang allemaal beheerd door de service. Doorgaans slaan de fundamentele Azure-bronproviders de gegevensversleutelingssleutels op in een winkel die dicht bij de gegevens staat en snel beschikbaar en toegankelijk is, terwijl de sleutelcoderingssleutels worden opgeslagen in een beveiligde interne winkel.

**Voordelen**

- Eenvoudige installatie
- Microsoft beheert sleutelrotatie, back-up en redundantie
- De klant heeft niet de kosten in verband met de implementatie of het risico van een aangepaste sleutelbeheerschema.

**Nadelen**

- Geen klantcontrole over de coderingssleutels (sleutelspecificatie, levenscyclus, intrekking, enz.)
- Geen mogelijkheid om sleutelbeheer te scheiden van het algemene managementmodel voor de service

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Versleuteling aan de serverzijde met door de klant beheerde sleutels in Azure Key Vault

Voor scenario's waarbij het vereiste is om de gegevens in rust te versleutelen en de versleutelingssleutels te beheren, kunnen klanten server-side encryptie gebruiken met behulp van door de klant beheerde sleutels in Key Vault. Sommige services slaan mogelijk alleen de hoofdsleutelversleutelingssleutel op in Azure Key Vault en slaan de versleutelde gegevensversleutelingssleutel op een interne locatie dichter bij de gegevens. In dat scenario kunnen klanten hun eigen sleutels meenemen naar Key Vault (BYOK – Bring Your Own Key), of nieuwe genereren en gebruiken om de gewenste bronnen te versleutelen. Terwijl de Resource Provider voert de encryptie en decryptie operaties, het maakt gebruik van de geconfigureerde sleutel encryptiesleutel als de hoofdsleutel voor alle encryptie bewerkingen.

Verlies van sleutelversleutelingssleutels betekent verlies van gegevens. Om deze reden mogen sleutels niet worden verwijderd. Toetsen moeten worden geback-upt wanneer gemaakt of gedraaid. [Soft-Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) moet zijn ingeschakeld op elke kluis die sleutelversleutelingssleutels opslaat. In plaats van een sleutel te verwijderen, wordt de ingestelde set ingeschakeld voor false of stelt u de vervaldatum in.

##### <a name="key-access"></a>Sleuteltoegang

Het server-side encryptiemodel met door de klant beheerde sleutels in Azure Key Vault omvat de service die toegang krijgt tot de sleutels om te versleutelen en te decoderen als dat nodig is. Versleuteling in rustsleutels worden toegankelijk gemaakt voor een service via een toegangscontrolebeleid. Dit beleid geeft de serviceidentiteit toegang tot de sleutel. Een Azure-service die namens een gekoppeld abonnement wordt uitgevoerd, kan worden geconfigureerd met een identiteit in dat abonnement. De service kan Azure Active Directory-verificatie uitvoeren en een verificatietoken ontvangen dat zichzelf identificeert als die service die optreedt namens het abonnement. Dat token kan vervolgens worden gepresenteerd aan Key Vault om een sleutel te verkrijgen waartoe het toegang heeft gekregen.

Voor bewerkingen met behulp van versleutelingssleutels kan een service-identiteit toegang krijgen tot een van de volgende bewerkingen: decoderen, versleutelen, uitpakkenKey, wrapKey, verifiëren, ondertekenen, ophalen, aanbieden, bijwerken, maken, importeren, verwijderen, back-upen en herstellen.

Als u een sleutel wilt verkrijgen voor het versleutelen of ontsleutelen van gegevens in rust, wordt de service-identiteit uitgevoerd die de serviceinstantie Van Resource Manager zal uitvoeren zoals moet Ontsleutel (om de sleutel voor decryptie te krijgen) en WrapKey (om een sleutel in sleutelkluis in te voegen bij het maken van een nieuwe sleutel).

>[!NOTE]
>Zie de beveiligde pagina van uw sleutelkluis in de [Azure Key Vault-documentatie](../../key-vault/key-vault-secure-your-key-vault.md)voor meer informatie over key vault-autorisatie.

**Voordelen**

- Volledige controle over de gebruikte sleutels - encryptiesleutels worden beheerd in de Key Vault van de klant onder controle van de klant.
- Mogelijkheid om meerdere services te versleutelen naar één master
- Kan sleutelbeheer scheiden van het algemene managementmodel voor de service
- Kan service en sleutellocatie in verschillende regio's definiëren

**Nadelen**

- Klant heeft volledige verantwoordelijkheid voor key access management
- Klant is volledig verantwoordelijk voor key lifecycle management
- Aanvullende & configuratieoverhead

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Server-side encryptie met behulp van door de klant beheerde sleutels in klantgestuurde hardware

Sommige Azure-services maken het HYOK-sleutelbeheermodel (Host Your Own Key) inschakelen. Deze beheermodus is handig in scenario's waarin het nodig is om de gegevens in rust te versleutelen en de sleutels te beheren in een eigen opslagplaats buiten de controle van Microsoft. In dit model moet de service de sleutel ophalen van een externe site. Prestatie- en beschikbaarheidsgaranties worden beïnvloed en de configuratie is complexer. Bovendien, omdat de service toegang heeft tot de DEK tijdens de versleutelings- en decryptiebewerkingen, zijn de algemene beveiligingsgaranties van dit model vergelijkbaar met wanneer de sleutels worden beheerd door de klant in Azure Key Vault.  Als gevolg hiervan is dit model niet geschikt voor de meeste organisaties, tenzij ze specifieke vereisten voor sleutelbeheer hebben. Vanwege deze beperkingen ondersteunen de meeste Azure Services geen serverversleuteling met behulp van serverbeheersleutels in door de klant beheerde hardware.

##### <a name="key-access"></a>Sleuteltoegang

Wanneer server-side encryptie met behulp van service-managed sleutels in klantgestuurde hardware wordt gebruikt, worden de sleutels onderhouden op een systeem geconfigureerd door de klant. Azure-services die dit model ondersteunen, bieden een manier om een veilige verbinding tot stand te brengen met een door de klant geleverde sleutelwinkel.

**Voordelen**

- Volledige controle over de gebruikte hoofdsleutel - encryptiesleutels worden beheerd door een klant die opslaat
- Mogelijkheid om meerdere services te versleutelen naar één master
- Kan sleutelbeheer scheiden van het algemene managementmodel voor de service
- Kan service en sleutellocatie in verschillende regio's definiëren

**Nadelen**

- Volledige verantwoordelijkheid voor sleutelopslag, beveiliging, prestaties en beschikbaarheid
- Volledige verantwoordelijkheid voor key access management
- Volledige verantwoordelijkheid voor sleutellevenscyclusbeheer
- Aanzienlijke installatie-, configuratie- en doorlopende onderhoudskosten
- Verhoogde afhankelijkheid van de beschikbaarheid van het netwerk tussen het klantendatacenter en Azure-datacenters.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Versleuteling in rust in Microsoft-cloudservices

Microsoft Cloud-services worden gebruikt in alle drie de cloudmodellen: IaaS, PaaS, SaaS. Hieronder vindt u voorbeelden van hoe ze passen op elk model:

- Softwareservices, software als server of SaaS genoemd, die door de cloud worden geleverd, zoals Office 365.
- Platformservices waarmee klanten gebruikmaken van de cloud in hun applicaties, waarbij ze de cloud gebruiken voor zaken als opslag, analytics en servicebusfunctionaliteit.
- Infrastructuurservices of Infrastructure as a Service (IaaS) waarin de klant besturingssystemen en toepassingen implementeert die in de cloud worden gehost en mogelijk gebruik maken van andere cloudservices.

### <a name="encryption-at-rest-for-saas-customers"></a>Encryptie in rust voor SaaS-klanten

Klanten van Software as a Service (SaaS) hebben doorgaans versleuteling in rust ingeschakeld of beschikbaar in elke service. Office 365 heeft verschillende opties voor klanten om versleuteling in rust te verifiëren of in te schakelen. Zie [Versleuteling in Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption)voor informatie over Office 365-services.

### <a name="encryption-at-rest-for-paas-customers"></a>Encryptie in rust voor PaaS-klanten

De gegevens van de klant van Platform as a Service (PaaS) bevinden zich meestal in een opslagservice zoals Blob Storage, maar kunnen ook in de cache worden opgeslagen of opgeslagen in de toepassingsuitvoeringsomgeving, zoals een virtuele machine. Als u de beschikbare opties voor versleuteling in rust wilt zien, bekijkt u de onderstaande tabel voor de opslag- en toepassingsplatforms die u gebruikt.

### <a name="encryption-at-rest-for-iaas-customers"></a>Encryptie in rust voor IaaS-klanten

IaaS-klanten (Infrastructure as a Service) kunnen verschillende diensten en toepassingen in gebruik hebben. IaaS-services kunnen versleuteling in rust inschakelen in hun door Azure gehoste virtuele machines en VHD's met Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Versleutelde opslag

Net als PaaS kunnen IaaS-oplossingen gebruikmaken van andere Azure-services die gegevens die in rust zijn versleuteld opslaan. In deze gevallen u de ondersteuning voor Versleuteling bij rust inschakelen, zoals geleverd door elke verbruikte Azure-service. De onderstaande tabel somt de belangrijkste opslag-, services- en toepassingsplatforms op en het ondersteunde model van Encryption at Rest. 

#### <a name="encrypted-compute"></a>Versleutelde rekenkracht

Alle beheerde schijven, momentopnamen en afbeeldingen worden versleuteld met behulp van Storage Service Encryption met behulp van een servicebeheersleutel. Een completere Encryption at Rest-oplossing zorgt ervoor dat de gegevens nooit onversleuteld worden weergegeven. Tijdens het verwerken van de gegevens op een virtuele machine kunnen gegevens worden weergegeven in het Windows-paginabestand of het Linux-swapbestand, een crashdump of een toepassingslogboek. Om ervoor te zorgen dat deze gegevens in rust worden versleuteld, kunnen IaaS-toepassingen Azure Disk Encryption gebruiken op een azure IaaS virtuele machine (Windows of Linux) en virtuele schijf.

#### <a name="custom-encryption-at-rest"></a>Aangepaste versleuteling in rust

Het wordt aanbevolen dat IaaS-toepassingen waar mogelijk gebruik maken van Azure Disk Encryption and Encryption at Rest-opties die worden geleverd door alle verbruikte Azure-services. In sommige gevallen, zoals onregelmatige versleutelingsvereisten of niet-Azure-gebaseerde opslag, moet een ontwikkelaar van een IaaS-toepassing mogelijk zelf versleuteling implementeren. Ontwikkelaars van IaaS-oplossingen kunnen beter integreren met Azure-beheer en klantverwachtingen door gebruik te maken van bepaalde Azure-componenten. Met name ontwikkelaars moeten de Azure Key Vault-service gebruiken om veilige sleutelopslag te bieden en hun klanten consistente sleutelbeheeropties te bieden met die van de meeste Azure-platformservices. Daarnaast moeten aangepaste oplossingen Azure-Managed Service Identities gebruiken om serviceaccounts toegang te geven tot versleutelingssleutels. Zie hun respectieve SDK's voor informatie over ontwikkelaars over Azure Key Vault en Managed Service Identities.

## <a name="azure-resource-providers-encryption-model-support"></a>Ondersteuning voor versleutelingsmodel azure-bronproviders

Microsoft Azure Services ondersteunen elk een of meer van de versleuteling bij restmodellen. Voor sommige services zijn echter mogelijk geen versleutelingsmodellen van toepassing. Voor services die door klanten beheerde sleutelscenario's ondersteunen, kunnen ze alleen een subset van de sleuteltypen ondersteunen die Azure Key Vault ondersteunt voor sleutelversleutelingssleutels. Bovendien kunnen services ondersteuning voor deze scenario's en sleuteltypen in verschillende schema's vrijgeven. In deze sectie wordt de ondersteuning voor versleuteling in rust beschreven op het moment van schrijven voor elk van de belangrijkste Azure-gegevensopslagservices.

### <a name="azure-disk-encryption"></a>Azure-schijfversleuteling

Elke klant die IaaS-functies (Azure Infrastructure as a Service) gebruikt, kan in rust versleuteling voor zijn IaaS-vm's en schijven bereiken via Azure Disk Encryption. Zie de [azure diskencryptiedocumentatie](../azure-security-disk-encryption-overview.md)voor meer informatie over Azure Disk-versleuteling.

#### <a name="azure-storage"></a>Azure Storage

Alle Azure Storage Services (Blob storage, Queue storage, Table storage en Azure Files) ondersteunen server-side encryptie in rust; sommige services ondersteunen bovendien door de klant beheerde sleutels en versleuteling aan de clientzijde. 

- Serverzijde: Alle Azure Storage Services maken standaard server-side encryptie mogelijk met behulp van servicebeheersleutels, die transparant is voor de toepassing. Zie [Azure Storage Service Encryption for Data at Rest voor](../../storage/common/storage-service-encryption.md)meer informatie. Azure Blob-opslag en Azure Files ondersteunen ook RSA 2048-bits door klanten beheerde sleutels in Azure Key Vault. Zie [Storage Service-versleuteling met door de klant beheerde sleutels in Azure Key Vault](../../storage/common/storage-encryption-keys-portal.md)voor meer informatie.
- Clientzijde: Azure Blobs, Tabellen en Wachtrijen ondersteunen versleuteling aan clientzijde. Bij het gebruik van client-side encryptie, klanten versleutelen de gegevens en uploaden de gegevens als een versleutelde blob. Key management wordt gedaan door de klant. Zie [Client-Side Encryption en Azure Key Vault voor Microsoft Azure Storage voor](../../storage/common/storage-client-side-encryption.md)meer informatie.

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database ondersteunt momenteel versleuteling in rust voor door Microsoft beheerde servicezijde en versleutelingsscenario's aan de clientzijde.

Ondersteuning voor serverversleuteling wordt momenteel geleverd via de SQL-functie genaamd Transparent Data Encryption. Zodra een Azure SQL Database-klant TDE-sleutel inschakelt, worden ze automatisch gemaakt en beheerd. Versleuteling in rust kan worden ingeschakeld op database- en serverniveau. Vanaf juni 2017 is [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) standaard ingeschakeld in nieuw gemaakte databases. Azure SQL Database ondersteunt RSA 2048-bits door klanten beheerde sleutels in Azure Key Vault. Zie [Transparante gegevensversleuteling met Bring Your Own Key-ondersteuning voor Azure SQL Database en Data Warehouse voor](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current)meer informatie.

Client-side encryptie van Azure SQL Database-gegevens wordt ondersteund via de [functie Altijd versleuteld.](https://msdn.microsoft.com/library/mt163865.aspx) Always Encrypted maakt gebruik van een sleutel die is gemaakt en opgeslagen door de client. Klanten kunnen de hoofdsleutel opslaan in een Windows-certificaatarchief, Azure Key Vault of een lokale hardwarebeveiligingsmodule. Met SQL Server Management Studio kiezen SQL-gebruikers welke sleutel ze willen gebruiken om welke kolom te versleutelen.

#### <a name="encryption-model-and-key-management-table"></a>Coderingsmodel en sleutelbeheertabel

|                                  |                    | **Versleutelingsmodel en sleutelbeheer** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Serverzijde met servicebeheerde sleutel**     | **Serverzijde met door de klant beheerde sleutel**             | **Clientzijde met clientbeheerde sleutel**      |
| **AI en Machine Learning**      |                    |                    |                    |
| Azure Cognitive Search           | Ja                | Ja                | -                  |
| Azure Machine Learning           | Ja                | Ja                | -                  |
| Azure Machine Learning Studio    | Ja                | Preview, RSA 2048-bit | -               |
| Power BI                         | Ja                | Preview, RSA 2048-bit | -                  |
| **Analytics**                    |                    |                    |                    |
| Azure Stream Analytics           | Ja                | -                  | -                  |
| Event Hubs                       | Ja                | Ja, alle RSA Lengtes. | -                  |
| Functions                        | Ja                | Ja, alle RSA Lengtes. | -                  |
| Azure Analysis Services          | Ja                | -                  | -                  |
| Azure Data Catalog               | Ja                | -                  | -                  |
| Apache Kafka op Azure HDInsight  | Ja                | Alle RSA lengtes.   | -                  |
| Inzichten in Azure-monitortoepassingen | Ja                | Ja                | -                  |
| Azure Monitor Log Analytics | Ja                | Ja                | -                  |
| Azure Data Explorer              | Ja                | Ja                | -                  |
| Azure Data Factory               | Ja                | Ja                | -                  |
| Azure Data Lake Store            | Ja                | Ja, RSA 2048-bit  | -                  |
| **Containers**                   |                    |                    |                    |
| Azure Kubernetes Service         | Ja                | Ja                | -                  |
| Container Instances              | Ja                | Ja                | -                  |
| Container Registry               | Ja                | Ja                | -                  |
| **Compute**                      |                    |                    |                    |
| Virtuele machines                 | Ja                | Ja, RSA 2048-bit  | -                  |
| Virtuele machineschaalset        | Ja                | Ja, RSA 2048-bit  | -                  |
| SAP HANA                         | Ja                | Ja, RSA 2048-bit  | -                  |
| App Service                      | Ja                | Ja                | -                  |
| Automation                       | Ja                | Ja                | -                  |
| Azure-portal                     | Ja                | Ja                | -                  |
| Logic Apps                       | Ja                | Ja                | -                  |
| Azure Managed Applications       | Ja                | Ja                | -                  |
| Service Bus                      | Ja                | Ja                | -                  |
| Site Recovery                    | Ja                | Ja                | -                  |
| **Databases**                    |                    |                    |                    |
| SQL Server op Virtual Machines   | Ja                | Ja, RSA 2048-bit  | Ja                |
| Azure SQL Database               | Ja                | Ja, RSA 2048-bit  | Ja                |
| Azure SQL-database voor MariaDB   | Ja                | -                  | -                  |
| Azure SQL-database voor MySQL     | Ja                | Ja                | -                  |
| Azure SQL-database voor PostgreSQL | Ja               | Ja                | -                  |
| Azure Synapse Analytics          | Ja                | Ja, RSA 2048-bit  | -                  |
| SQL Server Stretch Database      | Ja                | Ja, RSA 2048-bit  | Ja                |
| Table Storage                    | Ja                | Ja                | Ja                |
| Azure Cosmos DB                  | Ja                | Ja                | -                  |
| Azure Databricks                 | Ja                | Ja                | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps                     | Ja                | -                  | Ja                |
| Azure-opslagplaatsen                      | Ja                | -                  | Ja                |
| **Identiteit**                     |                    |                    |                    |
| Azure Active Directory           | Ja                | -                  | -                  |
| Azure Active Directory Domain Services | Ja          | Ja, RSA 2048-bit  | -                  |
| **Integratie**                  |                    |                    |                    |
| Service Bus                      | Ja                | Ja                | Ja                |
| Event Grid                       | Ja                | -                  | -                  |
| API Management                   | Ja                | -                  | -                  |
| **IoT-services**                 |                    |                    |                    |
| IoT Hub                          | Ja                | Ja                | Ja                |
| **Beheer en governance**    |                    |                    |                    |
| Azure Site Recovery              | Ja                | -                  | -                  |
| **Media**                        |                    |                    |                    |
| Media Services                   | Ja                | -                  | Ja                |
| **Opslag**                      |                    |                    |                    |
| Blob Storage                     | Ja                | Ja, RSA 2048-bit  | Ja                |
| Disk Storage                     | Ja                | Ja                | -                  |
| Beheerde schijfopslag             | Ja                | Ja                | -                  |
| File Storage                     | Ja                | Ja, RSA 2048-bit  | -                  |
| Queue Storage                    | Ja                | Ja                | Ja                |
| Avere vFXT                       | Ja                | -                  | -                  |
| Azure NetApp Files               | Ja                | -                  | -                  |
| Archive Storage                  | Ja                | Ja, RSA 2048-bit  | -                  |
| StorSimple                       | Ja                | Ja, RSA 2048-bit  | Ja                |
| Azure Backup                     | Ja                | Ja                | Ja                |
| Data Box                         | Ja                | -                  | Ja                |
| Data Box Edge                    | Ja                | Ja                | -                  |

## <a name="conclusion"></a>Conclusie

Bescherming van klantgegevens die zijn opgeslagen in Azure Services is van het grootste belang voor Microsoft. Alle azure-gehoste services zijn toegewijd aan het bieden van opties voor versleuteling bij rust. Fundamentele services zoals Azure Storage, Azure SQL Database en key analytics en intelligence services bieden al opties voor Versleuteling bij rust. Sommige van deze services ondersteunen zowel door de klant gecontroleerde sleutels en client-side encryptie, evenals service-managed sleutels en encryptie. Microsoft Azure-services verbeteren de beschikbaarheid van versleuteling bij rust in grote lijnen en er zijn nieuwe opties gepland voor preview en algemene beschikbaarheid in de komende maanden.
