---
title: Inleiding tot Azure Storage - cloudopslag in Azure | Microsoft Docs
description: Het belangrijkste Azure Storage-platform is de cloudopslagoplossing van Microsoft. Azure Storage biedt opslag voor gegevensobjecten die maximaal beschikbaar, veilig, duurzaam, in hoge mate schaalbaar en redundant is.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 1cc047ee60cf8287f32a42b878371c5fc9680b7a
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985742"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Inleiding tot de belangrijkste Azure Storage-services

Het Azure Storage-platform is de cloudopslagoplossing van Microsoft voor moderne scenario's voor gegevensopslag. Core-opslagservices bieden een enorm schaalbare objectstore voor gegevensobjecten, schijfopslag voor Virtuele Azure-machines (VM's), een bestandssysteemservice voor de cloud, een berichtenwinkel voor betrouwbare berichten en een NoSQL-winkel. De diensten zijn:

- **Duurzaam en maximaal beschikbaar.** Redundantie zorgt ervoor dat uw gegevens veilig zijn in geval van tijdelijke hardwarefouten. U kunt er ook voor kiezen gegevens te repliceren in datacenters of geografische regio's voor extra beveiliging tegen lokale rampen of natuurrampen. Op deze manier gerepliceerde gegevens blijven maximaal beschikbaar in het geval van een stroomstoring.
- **Veilige.** Alle gegevens die naar een Azure-opslagaccount zijn verzonden, worden versleuteld door de service. Azure Storage biedt u gedetailleerde controle over wie toegang tot uw gegevens heeft.
- **Schaalbare.** Azure Storage is in hoge mate schaalbaar om te voldoen aan de gegevensopslag- en prestatiebehoeften van de huidige toepassingen.
- **Beheerd.** Azure verwerkt hardwareonderhoud, updates en kritieke problemen voor u.
- **Toegankelijk.** Gegevens in Azure Storage zijn overal ter wereld toegankelijk via HTTP of HTTPS. Microsoft biedt clientbibliotheken voor Azure Storage in verschillende talen, waaronder .NET, Java, Node.js, Python, PHP, Ruby, Go en anderen, evenals een volwassen REST API. Azure Storage ondersteunt scripts in Azure PowerShell of Azure CLI. En Azure Portal en Azure Storage Explorer bieden handige visuele oplossingen voor het werken met uw gegevens.  

## <a name="core-storage-services"></a>Kernopslagdiensten

Het Azure Storage-platform bevat de volgende gegevensservices:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): een in hoge mate schaalbaar objectarchief voor tekst en binaire gegevens. Bevat ook ondersteuning voor big data-analyses via Data Lake Storage Gen2.
- [Azure Files](../files/storage-files-introduction.md): beheerde bestandsshares voor implementaties in de cloud of on-premises.
- [Azure Queues](../queues/storage-queues-introduction.md): een berichtenarchief voor betrouwbare uitwisseling van berichten tussen toepassingsonderdelen.
- [Azure Tables](../tables/table-storage-overview.md): een NoSQL-archief voor schemaloze opslag van gestructureerde gegevens.
- [Azure Disks:](../../virtual-machines/windows/managed-disks-overview.md)opslagvolumes op blokniveau voor Azure VM's.

Elke service kan worden geopend via een opslagaccount. Zie [Een opslagaccount maken](storage-account-create.md) om aan de slag te gaan.

## <a name="example-scenarios"></a>Voorbeeldscenario 's

In de volgende tabel worden bestanden, blobs, schijven, wachtrijen en tabellen vergeleken en worden voorbeeldscenario's voor elk weergegeven.

| Functie | Beschrijving | Wanneer gebruikt u dit? |
|--------------|-------------|-------------|
| **Azure-bestanden** |Biedt volledig beheerde cloudbestandsshares die u overal openen via het industriestandaard Server Message Block (SMB)-protocol.<br><br>U Azure-bestandsshares monteren vanuit cloud- of on-premises implementaties van Windows, Linux en macOS. | U wilt een toepassing "optillen en verplaatsen" naar de cloud die al gebruikmaakt van de API's voor native bestandssystemen om gegevens te delen tussen de toepassing die in Azure wordt uitgevoerd.<br/><br/>U wilt on-premises bestandsservers of NAS-apparaten vervangen of aanvullen.<br><br> U wilt ontwikkelings- en foutopsporingstools opslaan die vanaf veel virtuele machines moeten worden geopend. |
| **Azure-blobs** | Hiermee kunnen ongestructureerde gegevens op grote schaal worden opgeslagen en geopend in blokblobs.<br/><br/>Ondersteunt ook [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) voor oplossingen voor big data-analyse van bedrijven. | U wilt dat uw toepassing streaming- en willekeurige toegangsscenario's ondersteunt.<br/><br/>U wilt overal toegang hebben tot toepassingsgegevens.<br/><br/>U wilt een enterprise data lake bouwen op Azure en big data analytics uitvoeren. |
| **Azure-schijven** | Hiermee kunnen gegevens voortdurend worden opgeslagen en geopend vanaf een aangesloten virtuele harde schijf. | U wilt toepassingen die gebruikmaken van native bestandssysteemAPI's gebruiken om gegevens te lezen en te schrijven naar hardnekkige schijven "optillen en verschuiven".<br/><br/>U wilt gegevens opslaan die niet van buiten de virtuele machine moeten worden geopend waarop de schijf is gekoppeld. |
| **Azure-wachtrijen** | Hiermee u asynchrone berichten in de wachtrij tussen toepassingsonderdelen plaatsen. | U wilt toepassingscomponenten loskoppelen en asynchrone berichten gebruiken om tussen deze onderdelen te communiceren.<br><br>Zie [Opslagwachtrijen en servicebuswachtrijen - vergeleken en in contrast vergeleken](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted). |
| **Azure-tabellen** | Hiermee u gestructureerde NoSQL-gegevens opslaan in de cloud, zodat u een sleutel-/attribuutarchief opslaan met een schemaloos ontwerp. | U wilt flexibele gegevenssets opslaan, zoals gebruikersgegevens voor webtoepassingen, adresboeken, apparaatgegevens of andere typen metagegevens die uw service vereist. <br/><br/>Zie [Ontwikkelen met Azure Cosmos DB Table API en Azure Table storage](../../cosmos-db/table-support.md)voor richtlijnen over het gebruik van tabelopslag ten opzichte van de Azure Cosmos DB Table API. |

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage is Microsoft's oplossing voor opslag van objecten in de cloud. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens, zoals tekst of binaire gegevens.

Blob-opslag is ideaal voor:

- Het rechtstreeks aan een browser leveren van afbeeldingen of documenten.
- De opslag van bestanden voor gedistribueerde toegang.
- Streaming van video en audio.
- De opslag van gegevens voor back-up en herstel, herstel na noodgevallen en archivering.
- De opslag van gegevens voor analyse door een on-premises of in Azure gehoste service.

Objecten in Blob-opslag zijn overal ter wereld toegankelijk via HTTP of HTTPS. Gebruikers of clienttoepassingen hebben toegang tot blobs via URL's, de [REST-API van Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) of een Azure Storage-clientbibliotheek. Deze clientbibliotheken zijn beschikbaar voor meerdere talen, waaronder [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) en [Ruby](https://azure.github.io/azure-storage-ruby).

Zie [Inleiding tot blob-opslag](../blobs/storage-blobs-introduction.md) voor meer informatie over blob-opslag.

## <a name="azure-files"></a>Azure Files

Met [Azure Files](../files/storage-files-introduction.md) kunt u zeer netwerkbestandsshares met een hoge beschikbaarheid instellen die toegankelijk zijn via het standaard SMB-protocol (Server Message Block). Dit betekent dat meerdere VM's dezelfde bestanden kunnen delen met zowel lees- als schrijftoegang. U kunt de bestanden ook lezen met behulp van de REST-interface of de opslagclientbibliotheken.

Eén ding dat Azure Files onderscheidt van bestanden in een zakelijke bestandsshare is het feit dat u overal ter wereld toegang hebt tot de bestanden via een URL die verwijst naar het bestand en een SAS-token (Shared Access Signature) bevat. U kunt SAS-tokens genereren. Met deze tokens hebt u gedurende een opgegeven tijdperiode toegang tot een specifiek privéasset.

Bestandsshares kunnen worden gebruikt voor veelvoorkomende scenario's:

- Veel on-premises toepassingen maken gebruik van bestandsshares. Met deze functie kunt u gemakkelijker toepassingen die gegevens delen, migreren naar Azure. Als u de bestandsshare koppelt aan dezelfde stationsletter die wordt gebruikt voor de on-premises toepassing, werkt het gedeelte van de toepassing dat toegang heeft tot de bestandsshare, (vrijwel) ongewijzigd.

- Configuratiebestanden kunnen worden opgeslagen in een bestandsshare en zijn toegankelijk vanaf meerdere VM's. Hulpprogramma's en hulpmiddelen die worden gebruikt door meerdere ontwikkelaars in een groep, kunnen worden opgeslagen in een bestandsshare. Hierdoor kan iedereen ze vinden en maakt iedereen ook gebruik van dezelfde versie.

- Diagnostische logboeken, metrische gegevens en crashdumps zijn slechts drie voorbeelden van gegevens die naar een bestandsshare kunnen worden geschreven, en later verwerkt of geanalyseerd.

Raadpleeg de [Inleiding tot Azure Files](../files/storage-files-introduction.md) voor meer informatie over Azure Files.

Sommige SMB-functies zijn niet van toepassing op de cloud. Zie [Functies die niet worden ondersteund door de Azure File-service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)voor meer informatie.

## <a name="queue-storage"></a>Queue Storage

De Azure Queue-service wordt gebruikt voor het opslaan en ophalen van berichten. Berichten in de wachtrij kunnen maximaal 64 kB groot zijn, en een wachtrij kan miljoenen berichten bevatten. Wachtrijen worden meestal gebruikt voor het opslaan van lijsten met berichten die asynchroon moeten worden verwerkt.

Stel dat u uw klanten in de gelegenheid wilt stellen om afbeeldingen te uploaden en dat u voor elke afbeelding miniaturen wilt maken. U kunt uw klant dan laten wachten totdat u tijdens het uploaden van de afbeeldingen de miniaturen hebt gemaakt. Een alternatief is het inzetten van een wachtrij. Wanneer de klant klaar is met het uploaden, schrijft u een bericht naar de wachtrij. Vervolgens gebruikt u Azure Function om het bericht op te halen uit de wachtrij en de miniaturen te maken. Al deze verwerkingsstappen kunnen afzonderlijk worden geschaald, waardoor u meer controle hebt bij het afstemmen van de procedure op uw specifieke scenario.

Raadpleeg de [Inleiding tot Azure Queues](../queues/storage-queues-introduction.md) voor meer informatie over Azure Queues.

## <a name="table-storage"></a>Table Storage

Azure Table Storage maakt nu deel uit van Cosmos DB. Voor documentatie over Azure Table Storage raadpleegt u [Overzicht van Azure Table Storage](../tables/table-storage-overview.md). Naast de bestaande Azure Table Storage-service is er een nieuwe Azure Cosmos DB tabel-API die voor doorvoer geoptimaliseerde tabellen, wereldwijde distributie en automatische secundaire indexen biedt. Zie [Azure Cosmos DB Table API](https://aka.ms/premiumtables)voor meer informatie en uitproberen van de nieuwe premium-ervaring.

Zie [Overzicht van Azure Table Storage](../tables/table-storage-overview.md) voor meer informatie over Table Storage.

## <a name="disk-storage"></a>File Storage

Een door Azure beheerde schijf is een virtuele harde schijf (VHD). Je het zien als een fysieke schijf in een on-premises server, maar gevirtualiseerd. Door Azure beheerde schijven worden opgeslagen als paginablobs, een willekeurig IO-opslagobject in Azure. We noemen een beheerde schijf 'beheerd' omdat het een abstractie is over paginablobs, blobcontainers en Azure-opslagaccounts. Bij beheerde schijven hoeft u alleen de schijf in te richten en zorgt Azure voor de rest.

Zie Inleiding tot door [Azure beheerde schijven voor](../../virtual-machines/windows/managed-disks-overview.md)meer informatie over beheerde schijven.

## <a name="types-of-storage-accounts"></a>Typen opslagaccounts

Azure Storage biedt verschillende soorten opslagaccounts. Elk type ondersteunt verschillende functies en heeft zijn eigen prijsmodel. Zie [Overzicht van Azure-opslagaccounts](storage-account-overview.md) voor meer informatie over de typen opslagaccounts.

## <a name="secure-access-to-storage-accounts"></a>Beveiligde toegang tot opslagaccounts

Elk verzoek om Azure Storage moet zijn geautoriseerd. Azure Storage ondersteunt de volgende autorisatiemethoden:

- **Azure Active Directory (Azure AD)-integratie voor blob- en wachtrijgegevens.** Azure Storage ondersteunt verificatie en autorisatie met Azure AD voor de Blob- en Queue-services via rbac (Role-based access control). Het toestaan van aanvragen met Azure AD wordt aanbevolen voor superieure beveiliging en gebruiksgemak. Zie [Toegang tot Azure-blobs en wachtrijen autoriseren met Azure Active Directory](storage-auth-aad.md)voor meer informatie.
- **Azure AD-autorisatie via SMB voor Azure-bestanden.** Azure Files ondersteunt autorisatie op basis van identiteit via SMB (Server Message Block) via Azure Active Directory Domain Services (Azure AD DS) of on-premises Active Directory Domain Services (preview). Uw windows-VM's met domeinhebben toegang tot Azure-bestandsshares met Azure AD-referenties. Zie [Overzicht van op Azure Files gebaseerde verificatieondersteuning voor smb-toegang](../files/storage-files-active-directory-overview.md) en planning voor een [Azure-bestandsimplementatie voor](../files/storage-files-planning.md#identity)meer informatie.
- **Autorisatie met gedeelde sleutel.** De ondersteuningsautorisatie voor Azure Storage Blob, Files, Queue en Table Services met Shared Key. Een client met gedeelde sleutelautorisatie passeert een koptekst met elk verzoek dat is ondertekend met de toegangssleutel voor het opslagaccount. Zie [Autoriseren met gedeelde sleutel voor](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)meer informatie.
- **Autorisatie met behulp van gedeelde toegangshandtekeningen (SAS).** Een gedeelde toegangshandtekening (SAS) is een tekenreeks met een beveiligingstoken die kan worden toegevoegd aan de URI voor een opslagbron. Het beveiligingstoken bevat beperkingen zoals machtigingen en het toegangsinterval. Zie [SAS (Shared Access Signatures) gebruiken voor](storage-sas-overview.md)meer informatie.
- **Anonieme toegang tot containers en blobs.** Een container en de blobs kunnen openbaar beschikbaar zijn. Wanneer u opgeeft dat een container of blob openbaar is, kan iedereen deze anoniem lezen. er is geen verificatie vereist. Zie [Anonieme leestoegang tot containers en blobs beheren](../blobs/storage-manage-access-to-resources.md)voor meer informatie.

## <a name="encryption"></a>Versleuteling

Er zijn twee basissoorten encryptie beschikbaar voor de kernopslagdiensten. Raadpleeg de Engelstalige [Azure Storage-beveiligingshandleiding](../blobs/security-recommendations.md) voor meer informatie over beveiliging en versleuteling.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Azure Storage-versleuteling beschermt en beschermt uw gegevens om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Azure Storage versleutelt automatisch alle gegevens voordat ze worden uitgevoerd naar het opslagaccount en decodeert deze voordat deze worden opgehaald. De versleutelings-, decryptie- en sleutelbeheerprocessen zijn transparant voor gebruikers. Klanten kunnen er ook voor kiezen om hun eigen sleutels te beheren met Azure Key Vault. Zie [Azure Storage-versleuteling voor gegevens in rust](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Clientversleuteling

De Azure Storage-clientbibliotheken bieden methoden voor het versleutelen van gegevens uit de clientbibliotheek voordat deze over de draad worden verzonden en het antwoord worden gedecodeerd. Gegevens die versleuteld zijn via client-side encryptie, worden ook in rust versleuteld door Azure Storage. Zie [Client-side encryptie met .NET voor Azure Storage voor](storage-client-side-encryption.md)meer informatie over versleuteling aan de clientzijde.

## <a name="redundancy"></a>Redundantie

Om ervoor te zorgen dat uw gegevens duurzaam zijn, slaat Azure Storage meerdere kopieën van uw gegevens op. Als u uw opslagaccount gaat instellen, selecteert u een type replicatie. Zie [Redundantie in Azure Storage](/storage-redundancy?toc=/azure/storage/blobs/toc.json) voor meer informatie.

## <a name="transfer-data-to-and-from-azure-storage"></a>Gegevens overbrengen van en naar Azure Storage

Er zijn verschillende mogelijkheden om gegevens te verplaatsen van of naar Azure Storage. Welke optie u kiest, is afhankelijk van de grootte van de gegevensset en de bandbreedte van het netwerk. Zie [Een Azure-oplossing kiezen voor gegevensoverdracht](storage-choose-data-transfer-solution.md) voor meer informatie.

## <a name="pricing"></a>Prijzen

Bij het nemen van beslissingen over hoe uw gegevens worden opgeslagen en geopend, moet u ook rekening houden met de kosten die ermee gemoeid zijn. Zie [Azure Storage-prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie .

## <a name="storage-apis-libraries-and-tools"></a>Storage-API's, -bibliotheken en -hulpprogramma's

U hebt toegang tot bronnen in een opslagaccount via elke taal die HTTP/HTTPS-verzoeken kan indienen. Daarnaast bieden de belangrijkste Azure Storage-services programmeerbibliotheken voor verschillende populaire talen. Deze bibliotheken vereenvoudigen veel aspecten van het werken met Azure Storage door bewerkingen zoals synchrone en asynchrone aanroepafhandeling, batchverwerking van bewerkingen, uitzonderingsbeheer, automatische nieuwe pogingen, werking, enzovoort, voor hun rekening te nemen. Bibliotheken zijn momenteel beschikbaar voor de volgende talen en platformen (deze lijst wordt in de toekomst uitgebreid):

### <a name="azure-storage-data-api-and-library-references"></a>Azure Storage-gegevens-API en bibliotheekverwijzingen

- [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [Azure Storage-clientbibliotheek voor .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Azure Storage-clientbibliotheek voor Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Azure Storage-clientbibliotheek voor Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Azure Storage-clientbibliotheek voor Python](https://github.com/Azure/azure-storage-python)
- [Azure Storage-clientbibliotheek voor PHP](https://github.com/Azure/azure-storage-php)
- [Azure Storage-clientbibliotheek voor Ruby](https://github.com/Azure/azure-storage-ruby)
- [Azure Storage-clientbibliotheek voor C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure Storage-beheer-API en bibliotheekverwijzingen

- [REST API van opslagresourceprovider](https://docs.microsoft.com/rest/api/storagerp/)
- [Clientbibliotheek van opslagresourceprovider voor .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [REST API van opslagservicebeheer (klassiek)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure Storage-gegevensverplaatsing-API en bibliotheekverwijzingen

- [REST-API van Storage Import/Export-service](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Clientbibliotheek van opslaggegevensverplaatsing voor .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Hulpprogramma's

- [Azure PowerShell-cmdlets voor Storage](https://docs.microsoft.com/powershell/module/az.storage)
- [Azure CLI-cmdlets voor Storage](https://docs.microsoft.com/cli/azure/storage)
- [AzCopy Command-Line Utility](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.
- [Hulpprogramma's voor Azure Storage-client](../storage-explorers.md)
- [Azure Developer-hulpprogramma's](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Volgende stappen

Zie [Een opslagaccount maken](storage-account-create.md)voor aan de slag met de belangrijkste Azure Storage-services.
