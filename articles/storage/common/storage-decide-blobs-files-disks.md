---
title: Bepalen wanneer u Azure-blobs, Azure Files of Azure-schijven wilt gebruiken
description: Meer informatie over de verschillende manieren om gegevens op te slaan en te openen in azure om u te helpen bepalen welke technologie u moet gebruiken.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671050"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Bepalen wanneer u Azure-blobs, Azure Files of Azure-schijven wilt gebruiken

Microsoft Azure biedt verschillende functies in Azure Storage voor het opslaan en openen van uw gegevens in de Cloud. In dit artikel worden Azure Files, blobs en schijven beschreven, en is ontworpen om u te helpen bij het kiezen tussen deze functies.

## <a name="scenarios"></a>Scenario's

In de volgende tabel worden de bestanden, blobs en schijven vergeleken en worden voorbeeld scenario's weer gegeven die geschikt zijn voor elk.

| Functie | Description | Wanneer gebruikt u dit? |
|--------------|-------------|-------------|
| **Azure Files** | Biedt een SMB-interface,-client bibliotheken en een [rest-interface](/rest/api/storageservices/file-service-rest-api) waarmee u overal toegang kunt krijgen tot opgeslagen bestanden. | U wilt een toepassing "lift en verschuiving" verplaatsen naar de cloud die al gebruikmaakt van de systeem eigen Api's van het systeem voor het delen van gegevens tussen IT en andere toepassingen die worden uitgevoerd in Azure.<br/><br/>U wilt hulpprogram ma's voor ontwikkeling en fout opsporing opslaan die toegankelijk moeten zijn vanaf een groot aantal virtuele machines. |
| **Azure Blobs** | Biedt client bibliotheken en een [rest-interface](/rest/api/storageservices/blob-service-rest-api) waarmee ongestructureerde gegevens kunnen worden opgeslagen en geopend op een enorme schaal in blok-blobs.<br/><br/>Biedt ook ondersteuning voor [Azure data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) voor oplossingen voor enter prise Big Data Analytics. | U wilt dat uw toepassing streaming en wille keurige toegangs scenario's ondersteunt.<br/><br/>U wilt vanaf elke locatie toegang hebben tot toepassings gegevens.<br/><br/>U wilt een Enter prise data Lake bouwen op Azure en big data Analytics uitvoeren. |
| **Azure Disks** | Biedt client bibliotheken en een [rest-interface](/rest/api/compute/manageddisks/disks/disks-rest-api) waarmee gegevens permanent kunnen worden opgeslagen en geopend vanaf een gekoppelde virtuele harde schijf. | U wilt toepassingen die gebruikmaken van systeem eigen Api's van het bestands systeem voor het lezen en schrijven van gegevens naar permanente schijven, opheffen en verplaatsen.<br/><br/>U wilt gegevens opslaan die niet nodig zijn voor toegang tot de virtuele machine waarop de schijf is aangesloten. |


## <a name="next-steps"></a>Volgende stappen

Bij het nemen van beslissingen over hoe uw gegevens worden opgeslagen en geopend, moet u ook rekening houden met de kosten. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie.
  
Sommige SMB-functies zijn niet van toepassing op de Cloud. Zie [functies die niet worden ondersteund door de Azure File-Service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)voor meer informatie.
 
Zie ons artikel, [Wat is Azure Blob-opslag?](../blobs/storage-blobs-overview.md)voor meer informatie over Azure-blobs.

Zie voor meer informatie over Disk Storage onze [Inleiding tot Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

Zie voor meer informatie over Azure Files ons artikel, [planning voor een implementatie van Azure files](../files/storage-files-planning.md).