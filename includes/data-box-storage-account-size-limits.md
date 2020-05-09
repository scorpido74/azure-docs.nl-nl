---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 3e1f9c225a57e7d41f85c2a92dac989453057c51
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736974"
---
Dit zijn de limieten voor de grootte van de gegevens die worden gekopieerd naar het opslag account. Zorg ervoor dat de gegevens die u uploadt, voldoen aan deze limieten. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](../articles/storage/blobs/scalability-targets.md) en [Azure files schaalbaarheids-en prestatie doelen](../articles/storage/files/storage-files-scale-targets.md)voor de meest actuele informatie over deze limieten.

| Grootte van gegevens die zijn gekopieerd naar een Azure-opslag account                      | Standaardlimiet          |
|---------------------------------------------------------------------|------------------------|
| BLOB en pagina-BLOB blok keren                                            | 2 PB voor Verenigde Staten en Europa.<br>500 TB voor alle andere regio's, inclusief het Verenigd Konink rijk.  <br> Dit omvat gegevens uit alle bronnen met inbegrip van Data Box.|
| Azure Files                                                          | Maximale grootte van standaard bestands shares 100TiB *, 5 TB, Premium bestands shares 100TiB per share.<br> Alle mappen onder *StorageAccount_AzureFiles* moeten deze limiet volgen.       |
