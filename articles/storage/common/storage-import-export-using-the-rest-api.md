---
title: De Azure import/export-service gebruiken REST API | Microsoft Docs
description: Meer informatie over het zoeken naar bronnen voor het gebruik van de Azure import/export-service REST API, met inbegrip van de procedures en het referentie materiaal.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978863"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>De REST-API van de Azure-service Import/Export gebruiken

De Microsoft Azure Import/Export-service stelt een REST API in staat om programmatisch beheer van import/export-taken mogelijk te maken. U kunt de REST API gebruiken om alle import/export-bewerkingen uit te voeren die u met de [Azure Portal](https://portal.azure.com/)kunt uitvoeren. Daarnaast kunt u de REST API gebruiken om bepaalde granulaire bewerkingen uit te voeren, zoals het uitvoeren van een query op het voltooiings percentage van een taak, die momenteel niet beschikbaar is in de Azure Portal.

Zie [de Microsoft Azure import/export-service gebruiken om gegevens over te dragen naar Blob Storage](../storage-import-export-service.md) voor een overzicht van de import/export-service en een zelf studie waarin wordt getoond hoe u de portal kunt gebruiken om import-en export taken te maken en te beheren.

## <a name="service-endpoints"></a>Service-eindpunten

De Azure import/export-service is een resource provider voor Azure Resource Manager en biedt een set REST-Api's op het volgende HTTPS-eind punt voor het beheren van import/export-taken:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Versiebeheer

Aanvragen voor de import/export-service moeten de para meter `api-version` opgeven en de waarde ervan instellen op `2016-11-01`.

## <a name="importexport-service-operations"></a>Import/export-service bewerkingen

[Een importtaak maken](../storage-import-export-creating-an-import-job.md)

[Een exporttaak maken](../storage-import-export-creating-an-export-job.md)

[Statusinformatie van een taak ophalen](storage-import-export-retrieving-state-info-for-a-job.md)

[Taken opsommen](../storage-import-export-enumerating-jobs.md)

[Taken annuleren en verwijderen](storage-import-export-cancelling-and-deleting-jobs.md)

[Back-ups maken van station manifesten](../storage-import-export-backing-up-drive-manifests.md)

[Diagnoses en het herstel van fouten voor Import/Export-taken](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Volgende stappen

* [Opslag voor importeren/exporteren](/rest/api/storageimportexport)
