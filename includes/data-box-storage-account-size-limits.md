---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468884"
---
Dit zijn de limieten voor de grootte van de gegevens die worden gekopieerd naar het opslag account. Zorg ervoor dat de gegevens die u uploadt, voldoen aan deze limieten. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](../articles/storage/blobs/scalability-targets.md) en [Azure files schaalbaarheids-en prestatie doelen](../articles/storage/files/storage-files-scale-targets.md)voor de meest actuele informatie over deze limieten.

| Grootte van gegevens die zijn gekopieerd naar een Azure-opslag account                      | Standaardlimiet          |
|---------------------------------------------------------------------|------------------------|
| BLOB en pagina-BLOB blok keren                                            | 2 PB voor Verenigde Staten en Europa.<br>500 TB voor alle andere regio's, inclusief het Verenigd Konink rijk.  <br> Dit omvat gegevens uit alle bronnen met inbegrip van Data Box.|
| Azure Files                                                          | 5 TB per share.<br> Alle mappen onder *StorageAccount_AzureFiles* moeten deze limiet volgen.       |
