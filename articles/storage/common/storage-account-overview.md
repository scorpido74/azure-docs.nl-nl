---
title: Overzicht van opslagaccounts
titleSuffix: Azure Storage
description: Informatie over opties voor het maken en gebruiken van een Azure Storage-account.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3694a1d04ec25f44cfcf9395bdd148e2fd3c0d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371547"
---
# <a name="storage-account-overview"></a>Overzicht van opslagaccounts

Een Azure-opslagaccount bevat al uw Azure Storage-gegevensobjecten: blobs, bestanden, wachtrijen, tabellen en schijven. Het opslagaccount biedt een unieke naamruimte voor uw Azure Storage-gegevens die overal ter wereld toegankelijk is via HTTP of HTTPS. Gegevens in uw Azure-opslagaccount zijn duurzaam en zeer beschikbaar, veilig en enorm schaalbaar.

Zie [Een opslagaccount maken](storage-account-create.md)voor meer informatie over het maken van een Azure-opslagaccount .

## <a name="types-of-storage-accounts"></a>Typen opslagaccounts

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>V2-accounts voor algemeen gebruik

V2-opslagaccounts voor algemene doeleinden ondersteunen de nieuwste Azure Storage-functies en bevatten alle functionaliteit van v1- en Blob-opslagaccounts voor algemeen gebruik. V2-accounts voor algemene doeleinden bieden de laagste capaciteitsprijzen per gigabyte voor Azure Storage en brancheconcurrerende transactieprijzen. V2-opslagaccounts voor algemene doeleinden ondersteunen deze Azure Storage-services:

- Blobs (alle typen: Blokkeren, toevoegen, pagina)
- Gegevens Lake Gen2
- Bestanden
- Disks
- Wachtrijen
- Tabellen

> [!NOTE]
> Microsoft raadt aan om voor de meeste scenario's een v2-opslagaccount voor algemene doeleinden te gebruiken. U eenvoudig een v1- of Blob-opslagaccount voor algemene doeleinden upgraden naar een v2-account voor algemene doeleinden zonder downtime en zonder de noodzaak om gegevens te kopiëren.
>
> Zie [Upgraden naar een v2-opslagaccount](storage-account-upgrade.md)voor algemene doeleinden voor meer informatie over het upgraden naar een v2-account voor algemene doeleinden.

V2-opslagaccounts voor algemene doeleinden bieden meerdere toegangsniveaus voor het opslaan van gegevens op basis van uw gebruikspatronen. Zie [Toegangslagen voor blokblobgegevens voor](#access-tiers-for-block-blob-data)meer informatie .

### <a name="general-purpose-v1-accounts"></a>V1-accounts voor algemeen gebruik

V1-opslagaccounts voor algemene doeleinden bieden toegang tot alle Azure Storage-services, maar hebben mogelijk niet de nieuwste functies of de laagste prijzen per gigabyte. V1-opslagaccounts voor algemene doeleinden ondersteunen deze Azure Storage-services:

- Blobs (alle typen)
- Bestanden
- Disks
- Wachtrijen
- Tabellen

U moet in de meeste gevallen v2-accounts voor algemene doeleinden gebruiken. U v1-accounts voor algemene doeleinden gebruiken voor deze scenario's:

- Voor uw toepassingen is het klassieke Azure-implementatiemodel vereist. V2-accounts voor algemene doeleinden en Blob-opslagaccounts ondersteunen alleen het Azure Resource Manager-implementatiemodel.

- Uw toepassingen zijn transactieintensief of gebruiken een aanzienlijke georeplicatiebandbreedte, maar vereisen geen grote capaciteit. In dit geval kan de algemene v1 de meest economische keuze zijn.

- U gebruikt een versie van de [REST-API](https://msdn.microsoft.com/library/azure/dd894041.aspx) voor Storage Services die eerder is dan 2014-02-14 of een clientbibliotheek met een versie lager dan 4.x. U uw toepassing niet upgraden.

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage-accounts

Een BlockBlobStorage-account is een gespecialiseerd opslagaccount in de premium prestatielaag voor het opslaan van ongestructureerde objectgegevens als blokblobs of het toevoegen van blobs. In vergelijking met v2- en BlobStorage-accounts voor algemene doeleinden bieden BlockBlobStorage-accounts lage, consistente latentie en hogere transactiesnelheden.

BlockBlobStorage-accounts bieden momenteel geen ondersteuning voor gelaagdheid voor hot-, cool- of archieftoegangslagen. Dit type opslagaccount biedt geen ondersteuning voor paginablobs, tabellen of wachtrijen.

### <a name="filestorage-accounts"></a>FileStorage-accounts

Een FileStorage-account is een gespecialiseerde opslagaccount dat wordt gebruikt om premium bestandsshares op te slaan en te maken. Deze opslagaccountsoort ondersteunt bestanden, maar blokkeert geen blobs, plaatst blobs, paginablobs, tabellen of wachtrijen.

FileStorage-accounts bieden unieke prestatiespecifieke kenmerken, zoals IOPS-barsten. Zie het gedeelte [Opslaglagen voor bestandsshare](../files/storage-files-planning.md#storage-tiers) van de planningshandleiding bestanden voor meer informatie over deze kenmerken.

## <a name="naming-storage-accounts"></a>Opslagaccounts een naam noemen

Neem de volgende regels in acht als u het opslagaccount een naam geeft:

- Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten.
- De naam van uw opslagaccount moet uniek zijn binnen Azure. Een opslagaccount kan niet dezelfde naam hebben als een ander opslagaccount.

## <a name="performance-tiers"></a>Prestatielagen

Afhankelijk van het type opslagaccount dat u maakt, u kiezen tussen standaard- en premium prestatieniveaus.

### <a name="general-purpose-storage-accounts"></a>Opslagaccounts voor algemeen gebruik

Opslagaccounts voor algemene doeleinden kunnen worden geconfigureerd voor een van de volgende prestatieniveaus:

- Een standaardprestatielaag voor het opslaan van blobs, bestanden, tabellen, wachtrijen en Azure-schijven voor virtuele machines. Zie [Schaalbaarheidsdoelen voor standaardopslagaccounts voor](scalability-targets-standard-account.md)meer informatie over schaalbaarheidsdoelen voor standaardopslagaccounts.
- Een premium prestatielaag voor het opslaan van onbeheerde virtuele machineschijven. Microsoft raadt aan beheerde schijven te gebruiken met virtuele Azure-machines in plaats van onbeheerde schijven. Zie [Schaalbaarheidsdoelen voor blob-opslagaccounts voor premium pagina's voor](../blobs/scalability-targets-premium-page-blobs.md)meer informatie over schaalbaarheidsdoelen voor de hoogste prestatielaag.

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage-opslagaccounts

BlockBlobStorage-opslagaccounts bieden een premium prestatielaag voor het opslaan van blokblobs en het toevoegen van blobs. Zie [Schaalbaarheidsdoelen voor premium block blob-opslagaccounts voor](../blobs/scalability-targets-premium-block-blobs.md)meer informatie .

### <a name="filestorage-storage-accounts"></a>Opslagaccounts van FileStorage

FileStorage-opslagaccounts bieden een premium prestatielaag voor Azure-bestandsshares. Zie [schaalbaarheids- en prestatiedoelen voor Azure Files](../files/storage-files-scale-targets.md)voor meer informatie.

## <a name="access-tiers-for-block-blob-data"></a>Toegangslagen voor blokblobgegevens

Azure Storage biedt verschillende opties voor toegang tot blokblobgegevens op basis van gebruikspatronen. Elke toegangslaag in Azure Storage is geoptimaliseerd voor een bepaald patroon van gegevensgebruik. Door de juiste toegangslaag voor uw behoeften te selecteren, u uw blokblobgegevens op de meest kosteneffectieve manier opslaan.

De beschikbare toegangsniveaus zijn:

- De **hot** access-laag. Deze laag is geoptimaliseerd voor frequente toegang tot objecten in het opslagaccount. Toegang tot gegevens in de hot-tier is het meest kosteneffectief, terwijl de opslagkosten hoger zijn. Er worden standaard nieuwe opslagaccounts gemaakt in de hot-laag.
- De **Cool** access-laag. Deze laag is geoptimaliseerd voor het opslaan van grote hoeveelheden gegevens die ten minste 30 dagen zelden worden geopend en opgeslagen. Het opslaan van gegevens in de koele laag is kosteneffectiever, maar toegang tot die gegevens kan duurder zijn dan toegang tot gegevens in de hot-tier.
- De **archieflaag.** Deze laag is alleen beschikbaar voor afzonderlijke blokblobs. De archieflaag is geoptimaliseerd voor gegevens die enkele uren latentie kunnen verdragen en die ten minste 180 dagen in de archieflaag blijven. De archieflaag is de meest kosteneffectieve optie voor het opslaan van gegevens. Toegang tot die gegevens is echter duurder dan toegang tot gegevens in de hete of koele lagen.

Als er een wijziging is in het gebruikspatroon van uw gegevens, u op elk gewenst moment schakelen tussen deze toegangsniveaus. Zie [Azure Blob-opslag: hot, cool en archieftoegangslagen voor](../blobs/storage-blob-storage-tiers.md)meer informatie over toegangslagen.

> [!IMPORTANT]
> Als u de toegangslaag voor een bestaand opslagaccount of blob wijzigt, kunnen er extra kosten in rekening worden gebracht. Zie de [factureringssectie Voor opslagaccount](#storage-account-billing)voor meer informatie .

## <a name="redundancy"></a>Redundantie

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Versleuteling

Alle gegevens in uw opslagaccount worden versleuteld aan de servicezijde. Zie [Azure Storage Service Encryption voor gegevens in rust voor](storage-service-encryption.md)meer informatie over versleuteling.

## <a name="storage-account-endpoints"></a>Eindpunten van opslagaccount

Een opslagaccount biedt een unieke naamruimte in Azure voor uw gegevens. Elk object dat u in Azure Storage opslaat, heeft een adres dat uw unieke accountnaam bevat. De combinatie van de accountnaam en het service-eindpunt voor Azure Storage vormen de eindpunten voor uw opslagaccount.

Als uw opslagaccount voor algemene doeleinden bijvoorbeeld *mystorageaccount*wordt genoemd, zijn de standaardeindpunten voor dat account:

- Blob-opslag:`https://*mystorageaccount*.blob.core.windows.net`
- Tabelopslag:`https://*mystorageaccount*.table.core.windows.net`
- Wachtrijopslag:`https://*mystorageaccount*.queue.core.windows.net`
- Azure-bestanden:`https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> Blob- en blobopslagaccounts blokkeren, geven alleen het eindpunt van de Blob-service bloot.

Bouw de URL voor toegang tot een object in een opslagaccount door de locatie van het object in het opslagaccount toe te schrijven aan het eindpunt. Een blobadres kan bijvoorbeeld de volgende indeling hebben: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

U uw opslagaccount ook configureren om een aangepast domein voor blobs te gebruiken. Zie [Een aangepaste domeinnaam configureren voor uw Azure Storage-account voor](../blobs/storage-custom-domain-name.md)meer informatie.  

## <a name="control-access-to-account-data"></a>Toegang tot accountgegevens beheren

De gegevens in uw account zijn standaard alleen beschikbaar voor u, de eigenaar van het account. U hebt zelf controle over wie toegang heeft tot uw gegevens en welke machtigingen ze hebben.

Elk verzoek tegen uw opslagaccount moet zijn geautoriseerd. Op het niveau van de service moet de aanvraag een geldige *autorisatiekop* bevatten. In het bijzonder bevat deze header alle informatie die nodig is voor de service om de aanvraag te valideren voordat deze wordt uitgevoerd.

U toegang verlenen tot de gegevens in uw opslagaccount met behulp van een van de volgende benaderingen:

- **Azure Active Directory:** Gebruik Azure Active Directory-referenties (Azure AD) om een gebruiker, groep of andere identiteit te verifiëren voor toegang tot blob- en wachtrijgegevens. Als verificatie van een identiteit is geslaagd, retourneert Azure AD een token dat moet worden gebruikt bij het autoriseren van de aanvraag voor Azure Blob-opslag of Wachtrijopslag. Zie [Toegang tot Azure Storage verifiëren met Azure Active Directory](storage-auth-aad.md)voor meer informatie.
- **Machtiging Gedeelde sleutel:** Gebruik de toegangssleutel voor uw opslagaccount om een verbindingstekenreeks te maken die uw toepassing tijdens runtime gebruikt om toegang te krijgen tot Azure Storage. De waarden in de verbindingstekenreeks worden gebruikt om de *kopautorisatie* te construeren die wordt doorgegeven aan Azure Storage. Zie [Azure Storage-verbindingstekenreeksen configureren](storage-configure-connection-string.md)voor meer informatie .
- **Handtekening voor gedeelde toegang:** Gebruik een handtekening voor gedeelde toegang om toegang tot bronnen in uw opslagaccount te delegeren als u geen Azure AD-autorisatie gebruikt. Een gedeelde toegangshandtekening is een token dat alle informatie inkapselt die nodig is om een aanvraag voor Azure Storage op de URL te autoriseren. U de opslagbron, de verleende machtigingen en het interval opgeven waarop de machtigingen geldig zijn als onderdeel van de handtekening met gedeelde toegang. Zie [Gedeelde toegangshandtekeningen (SAS) gebruiken voor](storage-sas-overview.md)meer informatie.

> [!NOTE]
> Het verifiëren van gebruikers of toepassingen met Azure AD-referenties biedt superieure beveiliging en gebruiksgemak ten opzichte van andere autorisatiemiddelen. Hoewel u de autorisatie van Gedeelde sleutels met uw toepassingen blijven gebruiken, omzeilt het gebruik van Azure AD de noodzaak om uw accounttoegangssleutel op te slaan met uw code. U ook shared access signatures (SAS) blijven gebruiken om fijnmazige toegang te verlenen tot bronnen in uw opslagaccount, maar Azure AD biedt vergelijkbare mogelijkheden zonder dat u SAS-tokens hoeft te beheren of zich zorgen hoeft te maken over het intrekken van een gecompromitteerde SAS.
>
> Microsoft raadt aan om Azure AD-autorisatie waar mogelijk te gebruiken voor uw Azure Storage-blob- en queue-toepassingen.

## <a name="copying-data-into-a-storage-account"></a>Gegevens kopiëren naar een opslagaccount

Microsoft biedt hulpprogramma's en bibliotheken voor het importeren van uw gegevens van on-premises opslagapparaten of externe cloudopslagproviders. Welke oplossing u gebruikt, is afhankelijk van de hoeveelheid gegevens die u overdraagt.

Wanneer u een upgrade uitvoert naar een v2-account voor algemene doeleinden vanaf een v1- of Blob-opslagaccount voor algemene doeleinden, worden uw gegevens automatisch gemigreerd. Microsoft raadt dit traject aan voor het upgraden van uw account. Als u echter besluit om gegevens van een v1-account voor algemene doeleinden naar een Blob-opslagaccount te verplaatsen, migreert u uw gegevens handmatig met behulp van de onderstaande hulpprogramma's en bibliotheken.

### <a name="azcopy"></a>AzCopy

AzCopy is een Windows-opdrachtregelprogramma dat is  ontworpen voor het high-performance kopiëren van gegevens van en naar Azure Storage. U AzCopy gebruiken om gegevens te kopiëren naar een Blob-opslagaccount van een bestaand opslagaccount voor algemene doeleinden of om gegevens te uploaden vanaf on-premises opslagapparaten. Zie [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie.

### <a name="data-movement-library"></a>Bibliotheek voor gegevensverplaatsing

De Azure Storage-bibliotheek voor gegevensverplaatsing voor .NET is gebaseerd op het basisframework voor gegevensverplaatsing voor AzCopy. De bibliotheek is ontworpen voor high-performance, betrouwbare en eenvoudige gegevensoverdracht vergelijkbaar met AzCopy. U de bibliotheek voor gegevensverkeer gebruiken om te profiteren van azcopy-functies. Zie [Azure Storage Data Movement Library voor .NET voor](https://github.com/Azure/azure-storage-net-data-movement) meer informatie

### <a name="rest-api-or-client-library"></a>REST-API of clientbibliotheek

U een aangepaste toepassing maken om uw gegevens te migreren van een v1-opslagaccount voor algemene doeleinden naar een Blob-opslagaccount. Gebruik een van de Azure-clientbibliotheken of de REST-API voor Azure-opslagservices. Azure Storage biedt uitgebreide clientbibliotheken voor meerdere talen en platforms, zoals  .NET, Java, C++, Node.JS, PHP, Ruby en Python. De clientbibliotheken bieden geavanceerde mogelijkheden, zoals pogingslogica, logboekregistratie en parallelle uploads. U kunt ook rechtstreeks met de REST API ontwikkelen. Deze kan worden aangeroepen in elke taal waarin HTTP-/HTTPS-verzoeken kunnen worden gemaakt.

Zie [Azure Storage Services REST API-naslaginformatie](https://docs.microsoft.com/rest/api/storageservices/)voor meer informatie over de Azure Storage REST API.

> [!IMPORTANT]
> Blobs die aan de clientzijde zijn versleuteld, bevatten versleutelingsgerelateerde metagegevens die samen met de blob zijn opgeslagen. Als u een blob met versleuteling aan de clientzijde kopieert, zorg er dan voor dat bij het kopiëren de blobmetagegevens behouden blijven, en dan met name de versleutelingsgerelateerde metagegevens. Als u een blob kopieert zonder versleutelingsgerelateerde metagegevens, kan de inhoud van de blob niet meer worden opgehaald. Zie [Azure Storage-versleuteling aan de clientzijde](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie over versleutelingsgerelateerde metagegevens.

## <a name="storage-account-billing"></a>Facturering voor opslagaccounts

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Volgende stappen

- [Een opslagaccount maken](storage-account-create.md)
- [Een blok-blob-opslagaccount maken](../blobs/storage-blob-create-account-block-blob.md)
