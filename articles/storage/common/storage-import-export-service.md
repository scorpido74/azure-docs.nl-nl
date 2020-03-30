---
title: Azure Importeren/exporteren gebruiken om gegevens van en naar Azure Storage over te dragen | Microsoft Documenten
description: Meer informatie over het maken van import- en exporttaken in de Azure-portal voor het overbrengen van gegevens van en naar Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/15/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eee0fc2797fbe0666a6b848fde574c7807f47cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282440"
---
# <a name="what-is-azure-importexport-service"></a>Wat is azure import/exportservice?

Azure Import/Export-service wordt gebruikt om grote hoeveelheden gegevens veilig te importeren naar Azure Blob-opslag en Azure Files door schijfstations naar een Azure-datacenter te verzenden. Deze service kan ook worden gebruikt om gegevens van Azure Blob-opslag over te zetten naar schijven en te verzenden naar uw on-premises sites. Gegevens van een of meer schijven kunnen worden geïmporteerd naar Azure Blob-opslag of Azure Files.

Lever uw eigen schijven en breng gegevens over met de Azure Import/Export-service. U ook schijven gebruiken die door Microsoft worden geleverd.

Als u gegevens wilt overdragen met behulp van door Microsoft geleverde schijven, u [Azure Data Box Disk](../../databox/data-box-disk-overview.md) gebruiken om gegevens in Azure te importeren. Microsoft verzendt tot 5 versleutelde solid-state disk drives (SSD's) met een totale capaciteit van 40 TB per bestelling naar uw datacenter via een regionale luchtvaartmaatschappij. U snel schijven configureren, gegevens kopiëren naar schijven via een USB 3.0-verbinding en de schijven terugsturen naar Azure. Ga voor meer informatie naar [het overzicht van Azure Data Box Disk](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Use Cases voor Azure Import/Export

Overweeg om azure import/export-service te gebruiken wanneer het uploaden of downloaden van gegevens via het netwerk te traag is of het verkrijgen van extra netwerkbandbreedte is onbetaalbaar. Gebruik deze service in de volgende scenario's:

* **Gegevensmigratie naar de cloud:** verplaats grote hoeveelheden gegevens snel en kosteneffectief naar Azure.
* **Contentdistributie**: Stuur snel gegevens naar sites van uw klanten.
* **Back-up:** maak back-ups van uw on-premises gegevens om op te slaan in Azure Storage.
* **Gegevensherstel:** herstel grote hoeveelheden gegevens die in de opslag zijn opgeslagen en laat deze op uw locatie worden afgeleverd.

## <a name="importexport-components"></a>Onderdelen importeren/exporteren

De import/exportservice gebruikt de volgende componenten:

* **Import/Exportservice:** deze service die beschikbaar is in Azure-portal helpt de gebruiker bij het maken en bijhouden van taken voor het importeren van gegevens (uploaden) en exporteren (downloaden).  

* **GEREEDSCHAP WAImportExport**: Dit is een opdrachtregelgereedschap dat het volgende doet:
  * Bereidt uw schijven voor die worden verzonden voor import.
  * Vergemakkelijkt het kopiëren van uw gegevens naar het station.
  * Hiermee versleutelt u de gegevens op het station met AES 128-bits BitLocker. U een externe sleutelbeschermer gebruiken om uw BitLocker-sleutel te beschermen.
  * Hiermee genereert u de drive-logboekbestanden die worden gebruikt tijdens het maken van invoer.
  * Helpt bij het identificeren van het aantal stations dat nodig is voor exporttaken.

> [!NOTE]
> De WAImportExport tool is beschikbaar in twee versies, versie 1 en 2. Wij raden u aan:
>
> * Versie 1 voor importeren/exporteren naar Azure Blob-opslag.
> * Versie 2 voor het importeren van gegevens in Azure-bestanden.
>
> Het hulpprogramma WAImportExport is alleen compatibel met een 64-bits Windows-besturingssysteem. Ga voor specifieke ondersteunde BE-versies naar [Azure Import/Export-vereisten](storage-import-export-requirements.md#supported-operating-systems).

* **Schijven:** u Solid-state drives (SSD's) of harde schijven (HDD's) verzenden naar het Azure-datacenter. Wanneer u een importtaak maakt, verzendt u schijven met uw gegevens. Wanneer u een exporttaak maakt, verzendt u lege schijven naar het Azure-datacenter. Ga voor specifieke schijftypen naar [ondersteunde schijftypen](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Hoe werkt Import/Export?

Azure Import/Export-service maakt gegevensoverdracht naar Azure Blobs en Azure Files mogelijk door taken te maken. Gebruik Azure portal of Azure Resource Manager REST API om taken te maken. Elke taak is gekoppeld aan één opslagaccount.

De taken kunnen import- of exporttaken zijn. Met een importtaak u gegevens importeren in Azure Blobs- of Azure-bestanden, terwijl de exporttaak het mogelijk maakt gegevens te exporteren vanuit Azure Blobs. Voor een importtaak verzendt u schijven met uw gegevens. Wanneer u een exporttaak maakt, verzendt u lege schijven naar een Azure-datacenter. In elk geval u maximaal 10 schijven per taak verzenden.

### <a name="inside-an-import-job"></a>Binnen een importtaak

Op een hoog niveau omvat een importtaak de volgende stappen:

1. Bepaal de gegevens die moeten worden geïmporteerd, het aantal schijven dat u nodig hebt, de locatie van de doelblob voor uw gegevens in Azure-opslag.
2. Gebruik het gereedschap WAImportExport om gegevens naar schijven te kopiëren. Versleutel de schijven met BitLocker.
3. Maak een importtaak in uw doelopslagaccount in Azure-portal. Upload de stationslogboekbestanden.
4. Geef het retouradres en het rekeningnummer van de vervoerder op voor het verzenden van de schijven naar u.
5. Verzend de schijven naar het verzendadres dat tijdens het maken van een taak wordt opgegeven.
6. Werk het volgnummer voor de levering bij in de taakgegevens van importeren en dien de importtaak in.
7. De stations worden ontvangen en verwerkt in het Azure-datacenter.
8. De stations worden verzonden met uw provideraccount naar het retouradres in de importtaak.

> [!NOTE]
> Voor lokale (binnen datacenterland/regio) zendingen u een binnenlandse luchtvaartrekening delen.
>
> Voor zendingen in het buitenland (buiten datacenterland/regio) u een internationale luchtvaartrekening delen.

 ![Figuur 1:Taakstroom importeren](./media/storage-import-export-service/importjob.png)

Ga voor stapsgewijze instructies over het importeren van gegevens naar:

* [Gegevens importeren in Azure Blobs](storage-import-export-data-to-blobs.md)
* [Gegevens importeren in Azure-bestanden](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Binnen een exporttaak

> [!IMPORTANT]
> De service ondersteunt alleen de export van Azure Blobs. Exporteren van Azure-bestanden wordt niet ondersteund.

Op een hoog niveau omvat een exporttaak de volgende stappen:

1. Bepaal de gegevens die moeten worden geëxporteerd, het aantal stations dat u nodig hebt, bronblobs of containerpaden van uw gegevens in Blob-opslag.
2. Maak een exporttaak in uw bronopslagaccount in Azure-portal.
3. Geef bronblobs of containerpaden op voor de gegevens die moeten worden geëxporteerd.
4. Geef het retouradres en het rekeningnummer van de vervoerder op voor het verzenden van de schijven naar u.
5. Verzend de schijven naar het verzendadres dat tijdens het maken van een taak wordt opgegeven.
6. Werk het volgnummer voor de levering bij in de taakgegevens exporteren en dien de exporttaak in.
7. De stations worden ontvangen en verwerkt in het Azure-datacenter.
8. De schijven zijn versleuteld met BitLocker en de sleutels zijn beschikbaar via de Azure-portal.  
9. De stations worden verzonden met uw provideraccount naar het retouradres in de importtaak.

> [!NOTE]
> Voor lokale (binnen datacenterland/regio) zendingen u een binnenlandse luchtvaartrekening delen.
>
> Voor zendingen in het buitenland (buiten datacenterland/regio) u een internationale luchtvaartrekening delen.
  
 ![Figuur 2:Taakstroom exporteren](./media/storage-import-export-service/exportjob.png)

Ga voor stapsgewijze instructies voor het exporteren van gegevens naar [Gegevens exporteren vanuit Azure Blobs.](storage-import-export-data-from-blobs.md)

## <a name="region-availability"></a>Beschikbaarheid in regio’s

De Azure Import/Export-service ondersteunt het kopiëren van gegevens van en naar alle Azure-opslagaccounts. U schijven verzenden naar een van de vermelde locaties. Als uw opslagaccount zich op een Azure-locatie bevindt die hier niet is opgegeven, wordt een alternatieve verzendlocatie opgegeven wanneer u de taak maakt.

### <a name="supported-shipping-locations"></a>Ondersteunde verzendlocaties

|Land/regio  |Land/regio  |Land/regio  |Land/regio  |
|---------|---------|---------|---------|
|VS - oost    | Europa - noord        | India - centraal        |US Gov - Iowa         |
|VS - west     |Europa -west         | India - zuid        | US DoD East        |
|VS - oost 2    | Azië - oost        |  India - west        | US DoD Central        |
|VS - west 2     | Azië - zuidoost        | Canada - midden        | China East         |
|VS - centraal     | Australië - oost        | Canada - oost        | China - noord        |
|VS - noord-centraal     |  Australië - zuidoost       | Brazilië - zuid        | Verenigd Koninkrijk Zuid        |
|VS - zuid-centraal     | Japan - west        |Korea - centraal         | Duitsland - centraal        |
|VS - west-centraal     |  Japan - oost       | VS (overheid) - Virginia        | Duitsland - noordoost        |

## <a name="security-considerations"></a>Beveiligingsoverwegingen

De gegevens op het station worden versleuteld met AES 128-bits BitLocker Drive Encryption. Deze versleuteling beschermt uw gegevens tijdens het transport.

Voor importtaken worden stations op twee manieren versleuteld.  

* Geef de optie op wanneer u het bestand *dataset.csv* gebruikt tijdens het uitvoeren van het gereedschap WAImportExport tijdens de voorbereiding van het station.

* BitLocker-versleuteling handmatig inschakelen op het station. Geef de versleutelingssleutel op in de *driveset.csv* wanneer u de opdrachtregel van het gereedschap WAImportExport uitvoert tijdens de voorbereiding van het station. De BitLocker-versleutelingssleutel kan verder worden beschermd met behulp van een externe sleutelbeschermer (ook bekend als de door Microsoft beheerde sleutel) of een door de klant beheerde sleutel. Zie voor meer informatie hoe [u een door de klant beheerde sleutel gebruikt om uw BitLocker-sleutel te beschermen.](storage-import-export-encryption-key-portal.md)

Voor het exporteren van taken, nadat uw gegevens zijn gekopieerd naar de stations, de dienst versleutelt het station met behulp van BitLocker voordat u het terugnaar u. De versleutelingssleutel wordt u via de Azure-portal geleverd. Het station moet worden ontgrendeld met behulp van de WAImporExport tool met behulp van de sleutel.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Prijzen

**Kosten voor rijafhandeling**

Er zijn kosten voor het afhandelen van stations voor elk station dat wordt verwerkt als onderdeel van uw import- of exporttaak. Bekijk de details over de [Azure Import/Export Pricing](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Verzendkosten**

Wanneer u schijven naar Azure verzendt, betaalt u de verzendkosten aan de vervoerder. Wanneer Microsoft de schijven aan u retourneert, worden de verzendkosten in rekening gebracht op het carrieraccount dat u hebt opgegeven op het moment van het maken van een baan.

**Transactiekosten**

[Standaardopslagtransactiekosten](https://azure.microsoft.com/pricing/details/storage/) zijn van toepassing tijdens het importeren en exporteren van gegevens. Er zijn ook standaardkosten voor uitgangen van toepassing, samen met kosten voor opslagtransacties wanneer gegevens worden geëxporteerd vanuit Azure Storage. Zie [Kosten voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/)voor meer informatie over uitgaande kosten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de service Importeren/exporteren om:

* [Gegevens importeren in Azure Blobs](storage-import-export-data-to-blobs.md)
* [Gegevens exporteren vanuit Azure Blobs](storage-import-export-data-from-blobs.md)
* [Gegevens importeren in Azure-bestanden](storage-import-export-data-to-files.md)
