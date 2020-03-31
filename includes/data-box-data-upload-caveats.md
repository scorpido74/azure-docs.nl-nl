---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73799776"
---
- Kopieer geen bestanden rechtstreeks naar een van de vooraf gemaakte aandelen. U moet een map maken onder de share en vervolgens bestanden naar die map kopiëren.
- Een map onder de *StorageAccount_BlockBlob* en *StorageAccount_PageBlob* is een container. Containers worden bijvoorbeeld gemaakt als *StorageAccount_BlockBlob/container* en *StorageAccount_PageBlob/container.*
- Elke map die rechtstreeks onder *StorageAccount_AzureFiles* wordt gemaakt, wordt vertaald naar een Azure File Share.
- Als u een bestaand Azure-object (zoals een blob of een bestand) in de cloud hebt met dezelfde naam als het object dat wordt gekopieerd, overschrijft Data Box het bestand in de cloud.
- Elk bestand dat in *StorageAccount_BlockBlob* en *StorageAccount_PageBlob* aandelen is geschreven, wordt geüpload als respectievelijk een blokblob en paginablob.
- Azure blob-opslag biedt geen ondersteuning voor mappen. Als u een map maakt onder de *map StorageAccount_BlockBlob,* worden virtuele mappen gemaakt in de naam van de blob. Voor Azure Files wordt de werkelijke directorystructuur gehandhaafd.
- Lege maphiërarchie (zonder bestanden) die is gemaakt onder *StorageAccount_BlockBlob* en *StorageAccount_PageBlob* mappen wordt niet geüpload.
- Als er fouten zijn bij het uploaden van gegevens naar Azure, wordt er een foutlogboek gemaakt in het doelopslagaccount. Het pad naar dit foutlogboek is beschikbaar wanneer de upload is voltooid en u het logboek bekijken om corrigerende maatregelen te nemen. Verwijder geen gegevens uit de bron zonder de geüploade gegevens te verifiëren.
- Bestandsmetagegevens en NTFS-machtigingen worden niet bewaard wanneer de gegevens worden geüpload naar Azure-bestanden. Het kenmerk *Laatst gewijzigd van* de bestanden wordt bijvoorbeeld niet bewaard wanneer de gegevens worden gekopieerd.
