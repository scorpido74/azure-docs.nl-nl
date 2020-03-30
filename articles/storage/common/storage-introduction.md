---
title: Inleiding tot Azure Storage - cloudopslag in Azure | Microsoft Docs
description: Azure Storage is Microsoft's oplossing voor opslag in de cloud. Azure Storage biedt opslag voor gegevensobjecten die maximaal beschikbaar, veilig, duurzaam, in hoge mate schaalbaar en redundant is.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5bab70b6b023a4e6510e32368d407a38388cde2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256066"
---
# <a name="introduction-to-azure-storage"></a>Kennismaking met Azure Storage

Azure Storage is Microsoft's cloudoplossing bedoeld voor scenario's voor gegevensopslag. Azure Storage biedt een in hoge mate schaalbare opslag voor gegevensobjecten, een bestandssysteemservice voor de cloud, een berichtenarchief voor betrouwbaar gebruik van berichten en een NoSQL-archief. Azure Storage is:

- **Duurzaam en maximaal beschikbaar.** Redundantie zorgt ervoor dat uw gegevens veilig zijn in geval van tijdelijke hardwarefouten. U kunt er ook voor kiezen gegevens te repliceren in datacenters of geografische regio's voor extra beveiliging tegen lokale rampen of natuurrampen. Op deze manier gerepliceerde gegevens blijven maximaal beschikbaar in het geval van een stroomstoring.
- **Veilige.** Alle gegevens die naar Azure Storage worden geschreven, worden versleuteld door de service. Azure Storage biedt u gedetailleerde controle over wie toegang tot uw gegevens heeft.
- **Schaalbare.** Azure Storage is in hoge mate schaalbaar om te voldoen aan de gegevensopslag- en prestatiebehoeften van de huidige toepassingen.
- **Beheerd.** Microsoft Azure zorgt voor het onderhoud van hardware, updates en het oplossen van ernstige problemen.
- **Toegankelijk.** Gegevens in Azure Storage zijn overal ter wereld toegankelijk via HTTP of HTTPS. Microsoft biedt clientbibliotheken voor Azure Storage in verschillende talen, waaronder .NET, Java, Node.js, Python, PHP, Ruby, Go en anderen, evenals een volwassen REST API. Azure Storage ondersteunt scripts in Azure PowerShell of Azure CLI. En Azure Portal en Azure Storage Explorer bieden handige visuele oplossingen voor het werken met uw gegevens.  

## <a name="azure-storage-services"></a>Azure Storage-services

Azure Storage omvat deze gegevensservices:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): een in hoge mate schaalbaar objectarchief voor tekst en binaire gegevens.
- [Azure Files](../files/storage-files-introduction.md): beheerde bestandsshares voor implementaties in de cloud of on-premises.
- [Azure Queues](../queues/storage-queues-introduction.md): een berichtenarchief voor betrouwbare uitwisseling van berichten tussen toepassingsonderdelen.
- [Azure Tables](../tables/table-storage-overview.md): een NoSQL-archief voor schemaloze opslag van gestructureerde gegevens.

Elke service kan worden geopend via een opslagaccount. Zie [Een opslagaccount maken](storage-account-create.md) om aan de slag te gaan.

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

Op dit moment worden verificatie op basis van Active Directory en toegangsbeheerlijsten (ACL's) niet ondersteund, maar ondersteuning hiervan wordt in de toekomst beschikbaar. De opslagaccountreferenties worden gebruikt voor verificatie voor toegang tot de bestandsshare. Dit betekent dat iedereen met de gekoppelde share volledige lees-/schrijftoegang tot de share heeft.

Raadpleeg de [Inleiding tot Azure Files](../files/storage-files-introduction.md) voor meer informatie over Azure Files.

## <a name="queue-storage"></a>Queue Storage

De Azure Queue-service wordt gebruikt voor het opslaan en ophalen van berichten. Berichten in de wachtrij kunnen maximaal 64 kB groot zijn, en een wachtrij kan miljoenen berichten bevatten. Wachtrijen worden meestal gebruikt voor het opslaan van lijsten met berichten die asynchroon moeten worden verwerkt.

Stel dat u uw klanten in de gelegenheid wilt stellen om afbeeldingen te uploaden en dat u voor elke afbeelding miniaturen wilt maken. U kunt uw klant dan laten wachten totdat u tijdens het uploaden van de afbeeldingen de miniaturen hebt gemaakt. Een alternatief is het inzetten van een wachtrij. Wanneer de klant klaar is met het uploaden, schrijft u een bericht naar de wachtrij. Vervolgens gebruikt u Azure Function om het bericht op te halen uit de wachtrij en de miniaturen te maken. Al deze verwerkingsstappen kunnen afzonderlijk worden geschaald, waardoor u meer controle hebt bij het afstemmen van de procedure op uw specifieke scenario.

Raadpleeg de [Inleiding tot Azure Queues](../queues/storage-queues-introduction.md) voor meer informatie over Azure Queues.

## <a name="table-storage"></a>Table Storage

Azure Table Storage maakt nu deel uit van Cosmos DB. Voor documentatie over Azure Table Storage raadpleegt u [Overzicht van Azure Table Storage](../tables/table-storage-overview.md). Naast de bestaande Azure Table Storage-service is er een nieuwe Azure Cosmos DB tabel-API die voor doorvoer geoptimaliseerde tabellen, wereldwijde distributie en automatische secundaire indexen biedt. Bekijk [Azure Cosmos DB: tabel-API](https://aka.ms/premiumtables) voor meer informatie en om de nieuwe premium versie uit te proberen.

Zie [Overzicht van Azure Table Storage](../tables/table-storage-overview.md) voor meer informatie over Table Storage.

## <a name="disk-storage"></a>File Storage

Een door Azure beheerde schijf is een virtuele harde schijf (VHD). Je het zien als een fysieke schijf in een on-premises server, maar gevirtualiseerd. Azure-beheerde schijven worden opgeslagen als paginablobs, een willekeurig IO-opslagobject in Azure. We noemen een beheerde schijf 'beheerd' omdat het een abstractie is over paginablobs, blobcontainers en Azure-opslagaccounts. Bij beheerde schijven hoeft u alleen de schijf in te richten en zorgt Azure voor de rest.

Zie Inleiding tot door [Azure beheerde schijven voor](../../virtual-machines/windows/managed-disks-overview.md)meer informatie over beheerde schijven.

## <a name="types-of-storage-accounts"></a>Typen opslagaccounts

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Zie [Overzicht van Azure-opslagaccounts](storage-account-overview.md) voor meer informatie over de typen opslagaccounts.

## <a name="securing-access-to-storage-accounts"></a>Toegang tot opslagaccounts beveiligen

Elk verzoek om Azure Storage moet zijn geautoriseerd. Azure Storage ondersteunt de volgende autorisatiemethoden:

- **Azure Active Directory (Azure AD)-integratie voor blob- en wachtrijgegevens.** Azure Storage ondersteunt verificatie en autorisatie met Azure AD voor de Blob- en Queue-services via rbac (Role-based access control). Het toestaan van aanvragen met Azure AD wordt aanbevolen voor superieure beveiliging en gebruiksgemak. Zie [Toegang tot Azure-blobs en wachtrijen autoriseren met Azure Active Directory](storage-auth-aad.md)voor meer informatie.
- **Azure AD-autorisatie via SMB voor Azure-bestanden (voorbeeld).** Azure Files ondersteunt autorisatie op basis van identiteit via SMB (Server Message Block) via Azure Active Directory Domain Services. Uw virtuele Windows-machines (VM's) met domeinlid Windows hebben toegang tot Azure-bestandsshares met Azure AD-referenties. Zie [Overzicht van Azure Active Directory-autorisatie via SMB voor Azure-bestanden (voorbeeld) voor](../files/storage-files-active-directory-overview.md)meer informatie.
- **Autorisatie met gedeelde sleutel.** De Azure Storage Blob- en Queue- en Tabelservices en Azure Files-ondersteuningsautorisatie met Shared Key.A-client met gedeelde sleutelautorisatie geeft een koptekst door met elk verzoek dat is ondertekend met de toegangssleutel voor opslagaccount. Zie [Autoriseren met gedeelde sleutel voor](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)meer informatie.
- **Autorisatie met behulp van gedeelde toegangshandtekeningen (SAS).** Een gedeelde toegangshandtekening (SAS) is een tekenreeks met een beveiligingstoken die kan worden toegevoegd aan de URI voor een opslagbron. Het beveiligingstoken bevat beperkingen zoals machtigingen en het toegangsinterval. Zie Voor meer informatie Shared [Access Signatures (SAS)](storage-sas-overview.md)gebruiken.
- **Anonieme toegang tot containers en blobs.** Een container en de blobs kunnen openbaar beschikbaar zijn. Wanneer u opgeeft dat een container of blob openbaar is, kan iedereen deze anoniem lezen. er is geen verificatie vereist. Zie [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Anonieme leestoegang tot containers en blobs beheren) voor meer informatie.

## <a name="encryption"></a>Versleuteling

Er zijn twee basistypen versleuteling beschikbaar voor de Storage-services. Raadpleeg de Engelstalige [Azure Storage-beveiligingshandleiding](../blobs/security-recommendations.md) voor meer informatie over beveiliging en versleuteling.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Azure Storage-versleuteling beschermt en beschermt uw gegevens om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Azure Storage versleutelt automatisch alle gegevens voordat ze worden uitgevoerd naar het opslagaccount en decodeert deze voordat deze worden opgehaald. De encryptie, decryptie en key management processen zijn volledig transparant voor gebruikers. Klanten kunnen er ook voor kiezen om hun eigen sleutels te beheren met Azure Key Vault. Zie [Azure Storage-versleuteling voor gegevens in rust](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Clientversleuteling

De Azure Storage-clientbibliotheken bieden methoden voor het versleutelen van gegevens uit de clientbibliotheek voordat deze over de draad worden verzonden en het antwoord worden gedecodeerd. Gegevens die versleuteld zijn via client-side encryptie, worden ook in rust versleuteld door Azure Storage. Zie [Client-side encryptie met .NET voor Azure Storage voor](storage-client-side-encryption.md)meer informatie over versleuteling aan de clientzijde.

## <a name="redundancy"></a>Redundantie

Om ervoor te zorgen dat uw gegevens duurzaam zijn, slaat Azure Storage meerdere kopieën van uw gegevens op. Als u uw opslagaccount gaat instellen, selecteert u een type replicatie.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="transferring-data-to-and-from-azure-storage"></a>Gegevens overbrengen van en naar Azure Storage

Er zijn verschillende mogelijkheden om gegevens te verplaatsen van of naar Azure Storage. Welke optie u kiest, is afhankelijk van de grootte van de gegevensset en de bandbreedte van het netwerk. Zie [Een Azure-oplossing kiezen voor gegevensoverdracht](storage-choose-data-transfer-solution.md) voor meer informatie.

## <a name="pricing"></a>Prijzen

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/) voor gedetailleerde informatie over prijzen voor Azure Storage.

## <a name="storage-apis-libraries-and-tools"></a>Storage-API's, -bibliotheken en -hulpprogramma's

Azure Storage-resources zijn toegankelijk voor elke taal waarvoor HTTP/HTTPS-aanvragen mogelijk zijn. Daarnaast biedt Azure Storage programmeringsbibliotheken voor verschillende veelgebruikte talen. Deze bibliotheken vereenvoudigen veel aspecten van het werken met Azure Storage door bewerkingen zoals synchrone en asynchrone aanroepafhandeling, batchverwerking van bewerkingen, uitzonderingsbeheer, automatische nieuwe pogingen, werking, enzovoort, voor hun rekening te nemen. Bibliotheken zijn momenteel beschikbaar voor de volgende talen en platformen (deze lijst wordt in de toekomst uitgebreid):

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

Zie [Een opslagaccount maken](storage-account-create.md) om aan de slag te gaan met Azure Storage.
