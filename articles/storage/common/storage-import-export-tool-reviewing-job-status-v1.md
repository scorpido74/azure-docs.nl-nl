---
title: De status van azure importeren/exporteren controleren - v1 | Microsoft Documenten
description: Meer informatie over het gebruik van de logboekbestanden die zijn gemaakt toen de import- of exporttaak is uitgevoerd om de status van de taak Importeren/exporteren weer te geven.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 33e6ecca0eb4ca05b1f6bf53cdb3bd4ac8c8d8a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978439"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>De status van Azure Import/Export controleren met logboekbestanden kopiëren
Wanneer de Microsoft Azure Import/Export-service stations verwerkt die zijn gekoppeld aan een import- of exporttaak, worden er copy-logbestanden geschreven naar het opslagaccount waarnaar of van waaruit u blobs importeert of exporteert. Het logboekbestand bevat een gedetailleerde status van elk bestand dat is geïmporteerd of geëxporteerd. De URL van elk logboekbestand wordt geretourneerd wanneer u de status van een voltooide taak opvraagt. zie [Job zoeken](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) voor meer informatie.  

## <a name="example-urls"></a>Voorbeeld-URL's

Hieronder volgen bijvoorbeeld URL's voor copy logbestanden voor een importtaak met twee stations:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Zie [Logboekbestandsindeling importeren/exporteren](../storage-import-export-file-format-log.md) voor de indeling van kopieerlogboeken en de volledige lijst met statuscodes.  

## <a name="next-steps"></a>Volgende stappen

 * [Het azure-import/exporthulpprogramma instellen](storage-import-export-tool-setup-v1.md)   
 * [Harde schijven voorbereiden voor een importtaak](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Een importtaak herstellen](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Een exporttaak herstellen](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Problemen met het hulpprogramma Azure Import/Export oplossen](storage-import-export-tool-troubleshooting-v1.md)
