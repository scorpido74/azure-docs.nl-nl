---
title: Rest-API voor Azure Import/Export -service gebruiken | Microsoft Documenten
description: Meer informatie over bronnen voor het gebruik van de REST-API voor Azure Import/Export-service, inclusief zowel het btw- als het referentiemateriaal.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978863"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>De REST-API van de Azure-service Import/Export gebruiken

De Microsoft Azure Import/Export-service stelt een REST-API bloot om programmatische controle van import-/exporttaken mogelijk te maken. U de REST API gebruiken om alle import-/exportbewerkingen uit te voeren die u uitvoeren met de [Azure-portal.](https://portal.azure.com/) Bovendien u de REST-API gebruiken om bepaalde granulaire bewerkingen uit te voeren, zoals het opvragen van het percentage voltooiing van een taak, die momenteel niet beschikbaar is in de Azure-portal.

Zie [De service Microsoft Azure Importeren/exporteren gebruiken om gegevens over te dragen aan Blob-opslag](../storage-import-export-service.md) voor een overzicht van de import/exportservice en een zelfstudie die laat zien hoe u de portal gebruiken om import- en exporttaken te maken en te beheren.

## <a name="service-endpoints"></a>Service-eindpunten

De Azure Import/Export-service is een resourceprovider voor Azure Resource Manager en biedt een set REST-API's op het volgende HTTPS-eindpunt voor het beheren van import-/exporttaken:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Versiebeheer

Aanvragen voor de import/exportservice `api-version` moeten de parameter `2016-11-01`opgeven en de waarde ervan instellen op .

## <a name="importexport-service-operations"></a>Servicevoor importeren/exporteren

[Een importtaak maken](../storage-import-export-creating-an-import-job.md)

[Een exporttaak maken](../storage-import-export-creating-an-export-job.md)

[Statusinformatie van een taak ophalen](storage-import-export-retrieving-state-info-for-a-job.md)

[Taken opsommen](../storage-import-export-enumerating-jobs.md)

[Taken annuleren en verwijderen](storage-import-export-cancelling-and-deleting-jobs.md)

[Back-updrivemanifesten](../storage-import-export-backing-up-drive-manifests.md)

[Diagnoses en het herstel van fouten voor Import/Export-taken](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Volgende stappen

* [Rest opslag-/exportwaarde](/rest/api/storageimportexport)
