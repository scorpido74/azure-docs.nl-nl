---
title: Problemen met het Azure-hulp programma voor importeren/exporteren oplossen | Microsoft Docs
description: Meer informatie over een aantal veelvoorkomende problemen bij het gebruik van het Azure-hulp programma voor importeren/exporteren, en hoe u deze kunt afhandelen.
author: twooley
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: bc9d338579385001d33669ed06ff71e590571502
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85514152"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Problemen met het hulpprogramma Azure Import/Export oplossen
Het hulp programma Microsoft Azure Import/Export retourneert fout berichten als er problemen optreden. In dit onderwerp vindt u enkele veelvoorkomende problemen die gebruikers kunnen uitvoeren in.  

## <a name="a-copy-session-fails-what-i-should-do"></a>Een Kopieer sessie mislukt, wat moet ik doen?  
 Wanneer een Kopieer sessie mislukt, zijn er twee opties:  

 Als de fout opnieuw kan worden uitgevoerd, bijvoorbeeld als de netwerk share gedurende een korte periode offline was en nu weer online is, kunt u de Kopieer sessie hervatten. Als de fout niet kan worden herhaald, bijvoorbeeld als u de verkeerde directory van het bron bestand hebt opgegeven in de opdracht regel parameters, moet u de Kopieer sessie afbreken. Zie [harde schijven voorbereiden voor een import taak](../storage-import-export-tool-preparing-hard-drives-import-v1.md) voor meer informatie over het hervatten en afbreken van Kopieer sessies.  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Ik kan een Kopieer sessie niet hervatten of afbreken.  
 Als de Kopieer sessie de eerste kopie sessie voor een station is, wordt het volgende fout bericht weer gegeven: "de eerste Kopieer sessie kan niet worden hervat of worden afgebroken." In dit geval kunt u het oude logboek bestand verwijderen en de opdracht opnieuw uitvoeren.  

 Als een Kopieer sessie niet de eerste is voor een station, kan deze altijd worden hervat of afgebroken.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Ik ben het logboek bestand verloren. kan ik nog steeds de taak maken?  
 Het logboek bestand voor een station bevat de volledige informatie over het kopiëren van gegevens naar dit station en het is nodig om meer bestanden toe te voegen aan het station en wordt gebruikt om een import taak te maken. Als het logboek bestand verloren is gegaan, moet u alle kopie sessies voor het station opnieuw uitvoeren.  

## <a name="next-steps"></a>Volgende stappen

* [Het Azure-hulp programma voor importeren/exporteren instellen](../storage-import-export-tool-setup-v1.md)   
* [Harde schijven voorbereiden voor een importtaak](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [De taakstatus controleren met kopielogboekbestanden](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Een importtaak herstellen](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Een exporttaak herstellen](../storage-import-export-tool-repairing-an-export-job-v1.md)
