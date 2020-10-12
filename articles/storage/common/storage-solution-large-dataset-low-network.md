---
title: Opties voor Azure-gegevens overdracht voor grote gegevens sets met weinig of geen netwerk bandbreedte | Microsoft Docs
description: Meer informatie over het kiezen van een Azure-oplossing voor gegevens overdracht wanneer u beperkt bent tot geen netwerk bandbreedte in uw omgeving en u van plan bent om grote gegevens sets over te dragen.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 6514b149b057d690d37551bd0489c63852addb1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207504"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Gegevensoverdracht voor grote gegevenssets met weinig of geen netwerkbandbreedte
 
In dit artikel vindt u een overzicht van de oplossingen voor gegevens overdracht wanneer u een beperkt aantal netwerk bandbreedte hebt in uw omgeving en u van plan bent om grote gegevens sets over te dragen. In het artikel worden ook de aanbevolen opties voor gegevens overdracht en de bijbehorende matrix voor de belangrijkste functionaliteit voor dit scenario beschreven.

Voor een overzicht van alle beschik bare opties voor gegevens overdracht gaat u naar [een Azure-oplossing voor gegevens overdracht kiezen](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Offline overdracht of netwerk overdracht

Grote gegevens sets impliceren dat u slechts enkele bewaarde gegevens van PBs hebt. U hebt een beperkt aantal netwerk bandbreedte, uw netwerk is traag of is onbetrouwbaar. Ook:

- U wordt beperkt door de kosten van de netwerk overdracht van uw Internet serviceproviders (Isp's).
- Beveiligings-of organisatie beleid staat geen uitgaande verbindingen toe bij het verwerken van gevoelige gegevens.

In alle bovenstaande gevallen gebruikt u een fysiek apparaat om eenmalige bulk gegevens overdracht uit te voeren. Kies uit Data Box Disk, Data Box Data Box Heavy apparaten die door micro soft worden geleverd, of importeer/export met uw eigen schijven.

Gebruik de volgende tabel om te controleren of een fysiek apparaat de juiste optie is. De verwachte tijd voor het verzenden van netwerk gegevens wordt weer gegeven voor verschillende beschik bare band breedten (uitgaande van 90% gebruik). Als de netwerk overdracht te langzaam wordt geprojecteerd, moet u een fysiek apparaat gebruiken.  

![Netwerk overdracht of offline overdracht](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Aanbevolen opties

De beschik bare opties in dit scenario zijn apparaten voor Azure Data Box offline-overdracht of Azure import/export.

- **Azure data Box-serie voor offline overdrachten** : gebruik apparaten van door micro soft geleverde data Box apparaten om grote hoeveel heden gegevens naar Azure te verplaatsen wanneer u beperkt bent door tijd, netwerk beschikbaarheid of kosten. On-premises gegevens kopiëren met behulp van hulpprogram ma's zoals Robocopy. Afhankelijk van de grootte van de gegevens die u wilt overdragen, kunt u kiezen uit Data Box Disk, Data Box of Data Box Heavy.
- **Azure import/export** : gebruik Azure import/export-service door uw eigen schijf stations te verzenden om grote hoeveel heden gegevens veilig te importeren naar Azure Blob-opslag en Azure files. Deze service kan ook worden gebruikt om gegevens over te dragen van Azure Blob-opslag naar schijf stations en te verzenden naar uw on-premises sites.

## <a name="comparison-of-key-capabilities"></a>Vergelijking van de belangrijkste mogelijkheden

De volgende tabel bevat een overzicht van de verschillen in de belangrijkste mogelijkheden.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Gegevens grootte**                    |    Maxi maal 35 TBs                 |    Maxi maal 80 TBs per apparaat                       |    Tot 800 TB per apparaat               |    Variabele                            |
|    **Gegevenstype**                    |    Azure-blobs                  |    Azure-blobs<br>Azure Files                    |    Azure-blobs<br>Azure Files            |    Azure-blobs<br>Azure Files          |
|    **Vorm factor**                  |    5 Ssd's per bestelling             |    1 X 50 kg. apparaat op computer formaat per bestelling    |    1 X ~ 500 kg. groot apparaat per order    |    Maxi maal 10 Hdd's/Ssd's per bestelling        |
|    **Eerste instel tijd**           |    Beperkt <br>(15 minuten)            |    Laag tot gemiddeld <br> (<30 minuten)               |    Matig<br>(1-2 uur)               |    Gemiddeld tot moeilijk<br>variabeletype |
|    **Gegevens verzenden naar Azure**           |    Ja                          |    Ja                                           |    Ja                                   |    Ja                                 |
|    **Gegevens importeren uit Azure**       |    Nee                           |    Nee                                            |    Nee                                    |    Ja                                 |
|    **Versleuteling**                   |    AES 128-bits                  |    AES 256-bits                                   |    AES 256-bits                           |    AES 128-bits                         |
|    **Hardware**                     |     Geleverd door micro soft          |    Geleverd door micro soft                            |    Geleverd door micro soft                    |    Klant verstrekt                   |
|    **Netwerkinterface**            |    USB 3.1/SATA                 |    RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    **Partnerintegratie**          |    Enkele                         |    [Hoog](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Hoog](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Enkele                                |
|    **Verzending**                     |    Door micro soft beheerd            |    Door micro soft beheerd                             |    Door micro soft beheerd                     |    Door de klant beheerd                    |
| **Gebruiken wanneer gegevens worden verplaatst**     |Binnen een Commerce grens|Binnen een Commerce grens|Binnen een Commerce grens|Over geografische grenzen, bijvoorbeeld VS naar EU|
|    **Prijzen**                      |    [Prijzen](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prijzen](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Volgende stappen

- Begrijpen hoe u

    - [Gegevens overdragen met data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Gegevens overdragen met data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Gegevens overdragen met import/export](/azure/storage/common/storage-import-export-data-to-blobs).
