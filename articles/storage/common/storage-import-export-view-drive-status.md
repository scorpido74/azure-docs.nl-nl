---
title: Status van Azure Import/Export-taken weergeven | Microsoft Documenten
description: Meer informatie over het weergeven van de status van import/exporttaken en de gebruikte stations.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 222c893f06d9adf77f8a8124af18bc03c5d20bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72821440"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>De status van de Import/Export-taken in Azure bekijken

In dit artikel vindt u informatie over het weergeven van de schijf- en taakstatus voor Azure Import/Export-taken. Azure Import/Export-service wordt gebruikt om grote hoeveelheden gegevens veilig over te zetten naar Azure Blobs en Azure Files. De service wordt ook gebruikt om gegevens uit Azure Blob-opslag te exporteren.  

## <a name="view-job-and-drive-status"></a>Functie- en rijstatus weergeven
U de status van uw import- of exporttaken bijhouden vanuit de Azure-portal. Klik op het tabblad **Importeren/exporteren.** Er verschijnt een lijst met vacatures op de pagina.

![Taakstatus weergeven](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Taakstatus weergeven

U ziet een van de volgende taakstatussen, afhankelijk van waar uw schijf zich bevindt.

| Functiestatus | Beschrijving |
|:--- |:--- |
| Maken | Nadat een taak is gemaakt, wordt de status ingesteld op **Maken**. Terwijl de taak zich in de status **Maken** bevindt, gaat de service Importeren/exporteren ervan uit dat de stations niet naar het datacenter zijn verzonden. Een taak kan maximaal twee weken in deze status blijven, waarna deze automatisch door de service wordt verwijderd. |
| Verzenden | Nadat u uw pakket hebt verzonden, moet u de trackinggegevens in de Azure-portal bijwerken.  Dit verandert de baan in **verzendstatus.** De taak blijft in de **scheepvaart** staat voor maximaal twee weken. 
| Ontvangen | Nadat alle stations in het datacenter zijn ontvangen, is de taakstatus ingesteld op **Ontvangen**. |
| Overbrengen | Zodra ten minste één station is begonnen met de verwerking, is de taakstatus ingesteld op **overdracht .** Ga voor meer informatie naar [Drive States](#view-drive-status). |
| Verpakking | Nadat alle stations de verwerking hebben voltooid, wordt de taak in **de verpakkingsstatus** geplaatst totdat de schijven naar u worden teruggestuurd. |
| Voltooid | Nadat alle stations naar u worden teruggestuurd, als de taak zonder fouten is voltooid, is de taak ingesteld op **Voltooid**. De taak wordt automatisch verwijderd na 90 dagen in de **status Voltooid.** |
| Gesloten | Nadat alle schijven naar u worden teruggestuurd, als er fouten zijn gemaakt tijdens de verwerking van de taak, is de taak ingesteld op **Gesloten**. De taak wordt automatisch verwijderd na 90 dagen in de **status Gesloten.** |

## <a name="view-drive-status"></a>Status van station weergeven

De onderstaande tabel beschrijft de levenscyclus van een individuele schijf terwijl deze overgaat via een import- of exporttaak. De huidige status van elk station in een taak wordt gezien in de Azure-portal.

In de volgende tabel wordt elke status beschreven die elk station in een taak kan passeren.

| Stationsstatus | Beschrijving |
|:--- |:--- |
| Opgegeven | Wanneer de taak wordt gemaakt vanuit de Azure-portal, wordt voor een importtaak de oorspronkelijke status voor een station **opgegeven.** Voor een exporttaak, omdat er geen station is opgegeven wanneer de taak wordt gemaakt, wordt de oorspronkelijke status van station **ontvangen**. |
| Ontvangen | De schijf wordt overgezet naar de status **Ontvangen** wanneer de service Importeren/exporteren de schijven heeft verwerkt die van de verzendmaatschappij zijn ontvangen voor een importtaak. Voor een exporttaak is de oorspronkelijke status van de schijf de **status Ontvangen.** |
| Nooit ontvangen | Het station wordt verplaatst naar de **status NeverReceived** wanneer het pakket voor een taak binnenkomt, maar het pakket bevat het station niet. Een station beweegt zich ook in deze status als het twee weken geleden is dat de service de verzendinformatie heeft ontvangen, maar het pakket is nog niet aangekomen bij het datacenter. |
| Overbrengen | Een station wordt verplaatst naar de **status Overdracht** wanneer de service gegevens begint over te dragen van het station naar Azure Storage. |
| Voltooid | Een station wordt verplaatst naar de **status Voltooid** wanneer de service alle gegevens zonder fouten heeft overgedragen.
| VoltooidMeerInfo | Een station wordt verplaatst naar de status **CompletedMoreInfo** wanneer de service problemen heeft ondervonden tijdens het kopiëren van gegevens van of naar het station. De informatie kan fouten, waarschuwingen of informatieve berichten bevatten over het overschrijven van blobs.
| VerzondenTerug | Een station wordt verplaatst naar de **status ShippedBack** wanneer het is verzonden vanuit het datacenter terug naar het retouradres. |

Deze afbeelding van de Azure-portal geeft de status van een voorbeeldtaak weer:

![Drive-status weergeven](./media/storage-import-export-service/drivestate.png)

In de volgende tabel worden de statussen van schijffouten en de acties beschreven die voor elke status zijn uitgevoerd.

| Stationsstatus | Gebeurtenis | Resolutie / Volgende stap |
|:--- |:--- |:--- |
| Nooit ontvangen | Een station dat is gemarkeerd als **NeverReceived** (omdat het niet is ontvangen als onderdeel van de zending van de taak) komt in een andere zending. | Het operationele team verplaatst de schijf naar **Ontvangen**. |
| N.v.t. | Een schijf die geen deel uitmaakt van een taak komt in het datacenter als onderdeel van een andere taak. | Het station is gemarkeerd als een extra station en wordt aan u geretourneerd wanneer de taak die is gekoppeld aan het oorspronkelijke pakket is voltooid. |

## <a name="time-to-process-job"></a>Tijd om taak te verwerken
De hoeveelheid tijd die nodig is om een import/exporttaak te verwerken, is afhankelijk van een aantal factoren, zoals:

-  Verzendtijd
-  Laden in het datacenter
-  Taaktype en grootte van de gegevens die worden gekopieerd
-  Aantal schijven in een taak. 

Import/Export-service heeft geen SLA, maar de service streeft ernaar om de kopie in 7 tot 10 dagen nadat de schijven zijn ontvangen, in te vullen. Naast de status die op Azure Portal is geplaatst, kunnen REST-API's worden gebruikt om de voortgang van de taak bij te houden. De parameter percentage voltooid in de API-aanroep [listtaken](/previous-versions/azure/dn529083(v=azure.100)) geeft het percentage copy progress.


## <a name="next-steps"></a>Volgende stappen

* [Het gereedschap WAImportExport instellen](storage-import-export-tool-how-to.md)
* [Gegevens overbrengen met AzCopy-opdrachtregelhulpprogramma](storage-use-azcopy.md)
* [Voorbeeld van Azure Import Export REST API-voorbeeld](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
