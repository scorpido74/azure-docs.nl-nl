---
title: Status van Azure import/export-taak controleren-v1 | Microsoft Docs
description: Meer informatie over het gebruik van de logboek bestanden die zijn gemaakt met de import-of export taak om de status van de taak te bekijken.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: c9093af25e621b3ab9a60d9894f0a875340fda45
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525666"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>De status van een Azure import/export-taak controleren met behulp van Kopieer logboek bestanden
Wanneer de Microsoft Azure Import/Export-service de stations verwerkt die aan een import-of export taak zijn gekoppeld, worden de logboek bestanden gekopieerd naar het opslag account dat u hebt gebruikt voor het importeren of exporteren van blobs. Het logboek bestand bevat gedetailleerde status informatie over elk bestand dat is geïmporteerd of geëxporteerd. De service retourneert de URL voor elk exemplaar van het logboek bestand wanneer u de status van een voltooide taak doorzoekt. Zie [taak ophalen](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get)voor meer informatie.  

## <a name="example-urls"></a>Voor beeld-Url's

Hieronder vindt u voor beelden van Url's voor het kopiëren van logboek bestanden voor een import taak met twee stations:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Zie [import/export-service logboek bestands indeling](../storage-import-export-file-format-log.md) voor de indeling van Kopieer logboeken en de volledige lijst met status codes.  

## <a name="next-steps"></a>Volgende stappen

 * [Het Azure-hulp programma voor importeren/exporteren instellen](storage-import-export-tool-setup-v1.md)   
 * [Harde schijven voorbereiden voor een importtaak](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Een importtaak herstellen](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Een exporttaak herstellen](../storage-import-export-tool-repairing-an-export-job-v1.md)
