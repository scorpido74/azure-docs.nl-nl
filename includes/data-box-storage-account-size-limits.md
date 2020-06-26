---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378610"
---
Dit zijn de limieten voor de grootte van de gegevens die worden gekopieerd naar het opslag account. Zorg ervoor dat de gegevens die u uploadt, voldoen aan deze limieten. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](../articles/storage/blobs/scalability-targets.md) en [Azure files schaalbaarheids-en prestatie doelen](../articles/storage/files/storage-files-scale-targets.md)voor de meest actuele informatie over deze limieten.

| Grootte van gegevens die zijn gekopieerd naar een Azure-opslag account                      | Standaardlimiet          |
|---------------------------------------------------------------------|------------------------|
| BLOB en pagina-BLOB blok keren                                            | De maximum limiet is hetzelfde als de [opslag limiet die is gedefinieerd voor het Azure-abonnement](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) en bevat gegevens uit alle bronnen, inclusief data box.|
| Azure Files                                                          | De maximale grootte van standaard bestands shares is 5 TB <br> De maximale grootte van Premium-bestands shares is 100TiB per share.<br> Alle mappen onder *StorageAccount_AzureFiles* moeten deze limiet volgen.       |
