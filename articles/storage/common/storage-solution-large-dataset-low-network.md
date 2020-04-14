---
title: Azure-opties voor gegevensoverdracht voor grote gegevenssets met een lage of geen netwerkbandbreedte| Microsoft Documenten
description: Lees hoe u een Azure-oplossing voor gegevensoverdracht kiezen wanneer u zich beperkt hebt tot geen netwerkbandbreedte in uw omgeving en u van plan bent grote gegevenssets over te zetten.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: be1f74dcccc654dbdd0a743d1da2da89071045f1
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253132"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Gegevensoverdracht voor grote gegevenssets met weinig of geen netwerkbandbreedte
 
Dit artikel geeft een overzicht van de oplossingen voor gegevensoverdracht wanneer u zich beperkt hebt tot geen netwerkbandbreedte in uw omgeving en u van plan bent grote gegevenssets over te dragen. Het artikel beschrijft ook de aanbevolen opties voor gegevensoverdracht en de respectievelijke sleutelcapaciteitsmatrix voor dit scenario.

Als u een overzicht wilt begrijpen van alle beschikbare opties voor gegevensoverdracht, gaat u naar [Een Azure-oplossing voor gegevensoverdracht kiezen.](storage-choose-data-transfer-solution.md)

## <a name="offline-transfer-or-network-transfer"></a>Offline overdracht of netwerkoverdracht

Grote gegevenssets impliceren dat u weinig tb's tot weinig PCB's aan gegevens hebt. U hebt zich beperkt tot geen netwerkbandbreedte, uw netwerk is traag of onbetrouwbaar. Daarnaast:

- U bent beperkt door de kosten van netwerkoverdracht van uw Internet Service Providers (ISP's).
- Beveiligings- of organisatiebeleid staat geen uitgaande verbindingen toe bij het omgaan met gevoelige gegevens.

Gebruik in alle bovenstaande exemplaren een fysiek apparaat om een eenmalige bulkgegevensoverdracht uit te voeren. Kies uit Data Box Disk, Data Box, Data Box Heavy-apparaten die door Microsoft worden geleverd of Importeren/exporteren met uw eigen schijven.

Als u wilt controleren of een fysiek apparaat de juiste optie is, gebruikt u de volgende tabel. Het toont de verwachte tijd voor netwerkgegevensoverdracht, voor verschillende beschikbare bandbreedtes (uitgaande van 90% gebruik). Als de netwerkoverdracht naar verwachting te traag is, moet u een fysiek apparaat gebruiken.  

![Netwerkoverdracht of offlineoverdracht](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Aanbevolen opties

De opties die in dit scenario beschikbaar zijn, zijn apparaten voor offline overdracht van Azure Data Box of Azure Import/Export.

- **Azure Data Box-familie voor offline overdrachten** : gebruik apparaten van door Microsoft geleverde Data Box-apparaten om grote hoeveelheden gegevens naar Azure te verplaatsen wanneer u beperkt bent door tijd, beschikbaarheid van het netwerk of kosten. On-premises gegevens kopiëren met behulp van tools zoals Robocopy. Afhankelijk van de gegevensgrootte die is bedoeld voor overdracht, u kiezen uit Data Box Disk, Data Box of Data Box Heavy.
- **Azure Import/Export** : gebruik azure import/exportservice door uw eigen schijven te verzenden om grote hoeveelheden gegevens veilig te importeren naar Azure Blob-opslag en Azure-bestanden. Deze service kan ook worden gebruikt om gegevens van Azure Blob-opslag over te zetten naar schijven en te verzenden naar uw on-premises sites.

## <a name="comparison-of-key-capabilities"></a>Vergelijking van de belangrijkste mogelijkheden

In de volgende tabel worden de verschillen in sleutelmogelijkheden samengevat.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Gegevensgrootte                        |    Tot 35 tbs                 |    Tot 80 tbs per apparaat                       |    Tot 800 TB per apparaat               |    Variabele                            |
|    Gegevenstype                        |    Azure-blobs                  |    Azure-blobs<br>Azure Files                    |    Azure-blobs<br>Azure Files            |    Azure-blobs<br>Azure Files          |
|    Vormfactor                      |    5 SSD's per bestelling             |    1 X 50-lbs. apparaat van desktopformaat per bestelling    |    1 X ~500-lbs. groot apparaat per bestelling    |    Maximaal 10 HDD's/SSD's per bestelling        |
|    Initiële insteltijd               |    Laag <br>(15 minuten)            |    Laag tot matig <br> (<30 minuten)               |    Gemiddeld<br>(1-2 uur)               |    Matig tot moeilijk<br>(variabel) |
|    Gegevens verzenden naar Azure               |    Ja                          |    Ja                                           |    Ja                                   |    Ja                                 |
|    Gegevens vanuit Azure exporteren           |    Nee                           |    Nee                                            |    Nee                                    |    Ja                                 |
|    Versleuteling                       |    AES 128-bits                  |    AES 256-bits                                   |    AES 256-bits                           |    AES 128-bits                         |
|    Hardware                         |     Microsoft geleverd          |    Microsoft geleverd                            |    Microsoft geleverd                    |    Geleverde klant                   |
|    Netwerkinterface                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Partnerintegratie              |    Sommige                         |    [Hoge](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Hoge](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Sommige                                |
|    Verzenden                         |    Microsoft beheerd            |    Microsoft beheerd                             |    Microsoft beheerd                     |    Klant beheerd                    |
| Gebruiken wanneer gegevens worden verplaatst         |Binnen een handelsgrens|Binnen een handelsgrens|Binnen een handelsgrens|Over geografische grenzen heen, bijvoorbeeld tussen de VS en de EU|
|    Prijzen                          |    [Prijzen](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prijzen](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Volgende stappen

- Begrijpen hoe u

    - [Gegevens overbrengen met gegevensvakschijf](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Gegevens overbrengen met gegevensvak](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Gegevens overdragen met Importeren/exporteren](/azure/storage/common/storage-import-export-data-to-blobs).
