---
title: Beslissen wanneer Azure Blobs, Azure Files of Azure Disks moeten worden gebruikt
description: Meer informatie over de verschillende manieren om gegevens op te slaan en te openen in Azure om u te helpen beslissen welke technologie u wilt gebruiken.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71671050"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Beslissen wanneer Azure Blobs, Azure Files of Azure Disks moeten worden gebruikt

Microsoft Azure biedt verschillende functies in Azure Storage voor het opslaan en openen van uw gegevens in de cloud. Dit artikel heeft betrekking op Azure-bestanden, blobs en schijven en is ontworpen om u te helpen kiezen tussen deze functies.

## <a name="scenarios"></a>Scenario's

In de volgende tabel worden bestanden, blobs en schijven vergeleken en worden voorbeeldscenario's weergegeven die geschikt zijn voor elk.

| Functie | Beschrijving | Wanneer gebruikt u dit? |
|--------------|-------------|-------------|
| **Azure-bestanden** | Biedt een SMB-interface, clientbibliotheken en een [REST-interface](/rest/api/storageservices/file-service-rest-api) waarmee toegang vanaf elke locatie tot opgeslagen bestanden mogelijk is. | U wilt een toepassing "optillen en verplaatsen" naar de cloud die al gebruikmaakt van de API's van het native bestandssysteem om gegevens te delen tussen de toepassing die in Azure wordt uitgevoerd.<br/><br/>U wilt ontwikkelings- en foutopsporingstools opslaan die vanaf veel virtuele machines moeten worden geopend. |
| **Azure-blobs** | Biedt clientbibliotheken en een [REST-interface](/rest/api/storageservices/blob-service-rest-api) waarmee ongestructureerde gegevens op grote schaal kunnen worden opgeslagen en geopend in blokblobs.<br/><br/>Ondersteunt ook [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) voor oplossingen voor big data-analyse van bedrijven. | U wilt dat uw toepassing streaming- en willekeurige toegangsscenario's ondersteunt.<br/><br/>U wilt overal toegang hebben tot toepassingsgegevens.<br/><br/>U wilt een enterprise data lake bouwen op Azure en big data analytics uitvoeren. |
| **Azure-schijven** | Biedt clientbibliotheken en een [REST-interface](/rest/api/compute/manageddisks/disks/disks-rest-api) waarmee gegevens voortdurend kunnen worden opgeslagen en geopend vanaf een aangesloten virtuele harde schijf. | U wilt toepassingen die gebruikmaken van native bestandssysteemAPI's om gegevens te lezen en te schrijven naar permanente schijven, opheffen en verschuiven.<br/><br/>U wilt gegevens opslaan die niet van buiten de virtuele machine moeten worden geopend waarop de schijf is gekoppeld. |


## <a name="next-steps"></a>Volgende stappen

Bij het nemen van beslissingen over hoe uw gegevens worden opgeslagen en geopend, moet u ook rekening houden met de kosten die ermee gemoeid zijn. Zie [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie .
  
Sommige SMB-functies zijn niet van toepassing op de cloud. Zie [Functies die niet worden ondersteund door de Azure File-service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)voor meer informatie.
 
Zie ons artikel Wat is Azure [Blob-opslag voor](../blobs/storage-blobs-overview.md)meer informatie over Azure Blobs.

Zie onze [inleiding tot beheerde schijven](../../virtual-machines/windows/managed-disks-overview.md)voor meer informatie over schijfopslag.

Zie ons artikel [Planning for an Azure Files- voor](../files/storage-files-planning.md)meer informatie over Azure Files.