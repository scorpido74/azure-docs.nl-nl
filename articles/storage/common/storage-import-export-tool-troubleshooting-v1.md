---
title: Problemen oplossen met het azure import/exporttool | Microsoft Documenten
description: Meer informatie over enkele veelvoorkomende problemen die worden gezien bij het gebruik van het Azure Import/Export Tool en hoe u deze afhandelen.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 4eeeb538bcd39eed40a92dd45e7ba7bed25558e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978404"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Problemen met het hulpprogramma Azure Import/Export oplossen
Met het hulpprogramma voor importeren/exporteren van Microsoft Azure worden foutberichten geretourneerd als deze problemen veroorzaakt. In dit onderwerp worden enkele veelvoorkomende problemen weergegeven die gebruikers kunnen tegenkomen.  

## <a name="a-copy-session-fails-what-i-should-do"></a>Een kopieersessie mislukt, wat moet ik doen?  
 Wanneer een kopieersessie mislukt, zijn er twee opties:  

 Als de fout opnieuw kan worden geprobeerd, bijvoorbeeld als het netwerkaandeel voor een korte periode offline was en nu weer online is, u de kopieersessie hervatten. Als de fout niet opnieuw kan worden geprobeerd, bijvoorbeeld als u de verkeerde bronbestandsmap hebt opgegeven in de parameters van de opdrachtregel, moet u de kopieersessie afbreken. Zie [Harde schijven voorbereiden voor een taak importeren](../storage-import-export-tool-preparing-hard-drives-import-v1.md) voor meer informatie over het hervatten en afbreken van kopieersessies.  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Ik kan een kopieersessie niet hervatten of afbreken.  
 Als de kopieersessie de eerste kopieersessie voor een station is, moet in het foutbericht worden vermeld: "De eerste kopiesessie kan niet worden hervat of afgebroken." In dit geval u het oude logboekbestand verwijderen en de opdracht opnieuw uitvoeren.  

 Als een kopieersessie niet de eerste is voor een station, kan deze altijd worden hervat of afgebroken.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Ik ben het dagboekbestand kwijt, kan ik de baan nog steeds maken?  
 Het logboekbestand voor een station bevat de volledige informatie over het kopiëren van gegevens naar dit station, en het is nodig om meer bestanden toe te voegen aan het station en zal worden gebruikt om een importtaak te maken. Als het journaalbestand verloren is gegaan, moet u alle kopieersessies voor het station opnieuw doen.  

## <a name="next-steps"></a>Volgende stappen

* [Het azure import/export-hulpprogramma instellen](../storage-import-export-tool-setup-v1.md)   
* [Harde schijven voorbereiden voor een importtaak](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [De taakstatus controleren met kopielogboekbestanden](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Een importtaak herstellen](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Een exporttaak herstellen](../storage-import-export-tool-repairing-an-export-job-v1.md)
