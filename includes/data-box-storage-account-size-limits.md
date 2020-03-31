---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75468884"
---
Hier volgen de limieten voor de grootte van de gegevens die worden gekopieerd naar een opslagaccount. Zorg ervoor dat de gegevens die u uploadt aan deze limieten voldoen. Zie [Schaalbaarheid en prestatiedoelen voor blob-opslag en](../articles/storage/blobs/scalability-targets.md) [Azure-bestanden](../articles/storage/files/storage-files-scale-targets.md)voor de meest actuele informatie over deze limieten.

| Grootte van gegevens die zijn gekopieerd naar Azure-opslagaccount                      | Standaardlimiet          |
|---------------------------------------------------------------------|------------------------|
| Blob- en paginablob blokkeren                                            | 2 PB voor de VS en Europa.<br>500 TB voor alle andere regio's, waaronder het Verenigd Koninkrijk.  <br> Dit omvat gegevens uit alle bronnen, waaronder Data Box.|
| Azure Files                                                          | 5 TB per aandeel.<br> Alle mappen onder *StorageAccount_AzureFiles* moeten deze limiet volgen.       |
