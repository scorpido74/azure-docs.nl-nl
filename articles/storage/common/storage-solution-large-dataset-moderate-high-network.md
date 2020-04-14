---
title: Azure-opties voor gegevensoverdracht voor grote gegevenssets, matige tot hoge netwerkbandbreedte| Microsoft Documenten
description: Lees hoe u een Azure-oplossing voor gegevensoverdracht kiezen wanneer u een matige tot hoge netwerkbandbreedte in uw omgeving hebt en u van plan bent grote gegevenssets over te zetten.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: cfe450972acda3affbea6cbe3d262bc4b1956dc9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262856"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Gegevensoverdracht voor grote gegevenssets met gemiddelde tot grote netwerkbandbreedte
 
Dit artikel geeft een overzicht van de oplossingen voor gegevensoverdracht wanneer u een matige tot hoge netwerkbandbreedte in uw omgeving hebt en u van plan bent grote gegevenssets over te zetten. Het artikel beschrijft ook de aanbevolen opties voor gegevensoverdracht en de respectievelijke sleutelcapaciteitsmatrix voor dit scenario.

Als u een overzicht wilt begrijpen van alle beschikbare opties voor gegevensoverdracht, gaat u naar [Een Azure-oplossing voor gegevensoverdracht kiezen.](storage-choose-data-transfer-solution.md)

## <a name="scenario-description"></a>Scenariobeschrijving

Grote datasets verwijzen naar gegevensformaten in de volgorde van tbs's op PC's. Matige tot hoge netwerkbandbreedte verwijst naar 100 Mbps tot 10 Gbps.

## <a name="recommended-options"></a>Aanbevolen opties

De opties die in dit scenario worden aanbevolen, zijn afhankelijk van de vraag of u een matige netwerkbandbreedte of een hoge netwerkbandbreedte hebt.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Matige netwerkbandbreedte (100 Mbps - 1 Gbps)

Met een matige netwerkbandbreedte moet u de tijd voor gegevensoverdracht via het netwerk projecteren.

Gebruik de volgende tabel om de tijd te schatten en op basis daarvan te kiezen tussen een offline overdracht of via de netwerkoverdracht. De tabel toont de verwachte tijd voor netwerkgegevensoverdracht, voor verschillende beschikbare netwerkbandbreedtes (uitgaande van 90% gebruik).  

![Netwerkoverdracht of offlineoverdracht](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Als de netwerkoverdracht naar verwachting te traag is, moet u een fysiek apparaat gebruiken. De aanbevolen opties in dit geval zijn de offline transferapparaten uit azure data box-familie of Azure Import/Export met uw eigen schijven.

    - **Azure Data Box-familie voor offline overdrachten** : gebruik apparaten van door Microsoft geleverde Data Box-apparaten om grote hoeveelheden gegevens naar Azure te verplaatsen wanneer u beperkt bent door tijd, beschikbaarheid van het netwerk of kosten. On-premises gegevens kopiëren met behulp van tools zoals Robocopy. Afhankelijk van de gegevensgrootte die is bedoeld voor overdracht, u kiezen uit Data Box Disk, Data Box of Data Box Heavy.
    - **Azure Import/Export** : gebruik azure import/exportservice door uw eigen schijven te verzenden om grote hoeveelheden gegevens veilig te importeren naar Azure Blob-opslag en Azure-bestanden. Deze service kan ook worden gebruikt om gegevens van Azure Blob-opslag over te zetten naar schijven en te verzenden naar uw on-premises sites.

- Als de netwerkoverdracht naar verwachting redelijk is, u een van de volgende hulpprogramma's gebruiken die zijn beschreven in [Hoge netwerkbandbreedte.](#high-network-bandwidth)


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Hoge netwerkbandbreedte (1 Gbps - 100 Gbps)

Als de beschikbare netwerkbandbreedte hoog is, gebruikt u een van de volgende hulpprogramma's.

- **AzCopy** - Gebruik dit opdrachtregelgereedschap om eenvoudig gegevens van en naar Azure Blobs-, Bestanden- en Tabelopslag te kopiëren met optimale prestaties. AzCopy ondersteunt gelijktijdigheid en parallellisme en de mogelijkheid om kopieerbewerkingen te hervatten wanneer deze worden onderbroken.
- **Azure Storage REST API's/SDKs** – Bij het bouwen van een toepassing u de toepassing ontwikkelen tegen Azure Storage REST API's en de Azure SDK's gebruiken die in meerdere talen worden aangeboden.
- **Azure Data Box-familie voor online overdrachten** – Data Box Edge en Data Box Gateway zijn online netwerkapparaten die gegevens van en naar Azure kunnen verplaatsen. Gebruik het fysieke apparaat Data Box Edge wanneer er een gelijktijdige behoefte is aan continue opname en voorverwerking van de gegevens voordat ze worden geüpload. Data Box Gateway is een virtuele versie van het apparaat met dezelfde mogelijkheden voor gegevensoverdracht. In elk geval wordt de gegevensoverdracht beheerd door het apparaat.
- **Azure Data Factory** – Data Factory moet worden gebruikt om een overdrachtsbewerking uit te schalen en als er behoefte is aan mogelijkheden voor orchestration en enterprise grade monitoring. Gebruik Data Factory om regelmatig bestanden over te zetten tussen verschillende Azure-services, on-premises of een combinatie van beide. met Data Factory u gegevensgestuurde workflows (pijplijnen genoemd) maken en plannen die gegevens van verschillende gegevensopslag opnemen en gegevensverplaatsing en gegevenstransformatie automatiseren.

## <a name="comparison-of-key-capabilities"></a>Vergelijking van de belangrijkste mogelijkheden

In de volgende tabellen worden de verschillen in sleutelmogelijkheden voor de aanbevolen opties samengevat.

### <a name="moderate-network-bandwidth"></a>Matige netwerkbandbreedte

Als u offline gegevensoverdracht gebruikt, gebruikt u de volgende tabel om inzicht te krijgen in de verschillen in sleutelmogelijkheden.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/Export                       |
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


Als u online gegevensoverdracht gebruikt, gebruikt u de tabel in de volgende sectie voor een hoge netwerkbandbreedte.

### <a name="high-network-bandwidth"></a>Hoge netwerkbandbreedte

|                                     |    Tools AzCopy, <br>Azure PowerShell, <br>Azure CLI             |    Azure Storage REST API's, SDKs                   |    Data Box Gateway of Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Gegevenstype                  |    Azure Blobs, Azure Files, Azure-tabellen    |    Azure Blobs, Azure Files, Azure-tabellen    |    Azure Blobs, Azure-bestanden                           |   Ondersteunt meer dan 70-gegevensconnectoren voor gegevensarchieven en -indelingen    |
|    Vormfactor                |    Opdrachtregelprogramma's                        |    Programmatische interface                    |    Microsoft levert een virtuele <br>of fysiek apparaat     |    Service in Azure-portal                                            |
|    Initiële eenmalige installatie     |    Gemakkelijk               |    Gemiddeld                       |    Gemakkelijk (<30 minuten) tot matig (1-2 uur)            |    Uitgebreide                                                          |
|    Gegevens voorverwerking              |    Nee                                        |    Nee                                        |    Ja (met randgegevens)                               |    Ja                                                                |
|    Overdracht van andere wolken       |    Nee                                        |    Nee                                        |    Nee                                                    |    Ja                                                                |
|    Gebruikerstype                        |    IT Pro of dev                                       |    Dev                                       |    IT pro                                                |    IT pro                                                             |
|    Prijzen                          |    Gratis, data uitgangskosten van toepassing         |    Gratis, data uitgangskosten van toepassing         |    [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Prijzen](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het overbrengen van gegevens met Importeren/exporteren](/azure/storage/common/storage-import-export-data-to-blobs).
- Begrijpen hoe u

    - [Gegevens overbrengen met gegevensvakschijf](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Gegevens overbrengen met gegevensvak](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Gegevens overbrengen met AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Begrijpen hoe u:
    - [Gegevens overbrengen met Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Gegevens transformeren met Data Box Edge voordat u naar Azure verzendt.](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute)
- [Meer informatie over het overbrengen van gegevens met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Gebruik de REST API's om gegevens over te dragen

    - [In .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [In Java](https://docs.microsoft.com/java/api/overview/azure/storage)
