---
title: De status van Azure import/export-taken weer geven | Microsoft Docs
description: Meer informatie over het weer geven van de status van import/export-taken en de gebruikte stations.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 222c893f06d9adf77f8a8124af18bc03c5d20bdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "72821440"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>De status van de Import/Export-taken in Azure bekijken

Dit artikel bevat informatie over het weer geven van de status van het station en de taak voor Azure import/export-taken. Azure import/export-service wordt gebruikt voor het veilig overdragen van grote hoeveel heden gegevens naar Azure-blobs en Azure Files. De service wordt ook gebruikt voor het exporteren van gegevens uit Azure Blob-opslag.  

## <a name="view-job-and-drive-status"></a>Taak en de status van het station weer geven
U kunt de status van uw import-of export taken volgen vanuit het Azure Portal. Klik op het tabblad **importeren/exporteren** . Er wordt een lijst met taken op de pagina weer gegeven.

![Taak status weer geven](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Taakstatus weergeven

U ziet een van de volgende taak statussen, afhankelijk van waar uw station zich in het proces bevindt.

| Taak status | Beschrijving |
|:--- |:--- |
| Maken | Nadat een taak is gemaakt, wordt de status ingesteld op **maken**. Hoewel de taak de status **bezig met maken** heeft, wordt ervan uitgegaan dat de-import/export-service niet is verzonden naar het Data Center. Een taak kan Maxi maal twee weken in deze status blijven, waarna deze automatisch door de service wordt verwijderd. |
| Verzenden | Nadat u het pakket hebt verzonden, moet u de tracerings gegevens in het Azure Portal bijwerken.  Hiermee wordt de **Verzend** status van de taak hersteld. De taak blijft in de **Verzend** status voor Maxi maal twee weken. 
| Ontvangen | Nadat alle stations in het Data Center zijn ontvangen, wordt de status van de taak ingesteld op **ontvangen**. |
| Geboekt | Als ten minste één station wordt verwerkt, wordt de taak status ingesteld op **overdragen**. Ga naar de status van het [station](#view-drive-status)voor meer informatie. |
| Verpakking | Nadat de verwerking van alle stations is voltooid, wordt de taak in de **verpakkings** status geplaatst totdat de stations naar u worden teruggestuurd. |
| Voltooid | Als de taak is voltooid zonder fouten, wordt de taak ingesteld op **voltooid**nadat alle stations naar u zijn teruggestuurd. De taak wordt automatisch verwijderd na 90 dagen met de status **voltooid** . |
| Gesloten | Als er tijdens de verwerking van de taak fouten optreden, wordt de taak ingesteld op **gesloten**nadat alle stations naar u zijn verzonden. De taak wordt automatisch verwijderd na 90 dagen met de status **gesloten** . |

## <a name="view-drive-status"></a>Status van station weergeven

In de volgende tabel wordt de levens cyclus van een afzonderlijk station beschreven tijdens de overgang naar een import-of export taak. De huidige status van elke schijf in een taak wordt weer gegeven in de Azure Portal.

In de volgende tabel wordt elke status beschreven waarin elke schijf van een taak kan passeren.

| Status van station | Beschrijving |
|:--- |:--- |
| Opgegeven | Voor een import taak wordt de begin status voor een station **opgegeven**wanneer de taak wordt gemaakt op basis van de Azure Portal. Voor een export taak, omdat er geen station is opgegeven wanneer de taak wordt gemaakt, wordt de eerste status van het station **ontvangen**. |
| Ontvangen | De schijf wordt overgezet naar de status **ontvangen** wanneer de import/export-service de stations heeft verwerkt die zijn ontvangen van het verzend bedrijf voor een import taak. Voor een export taak is de eerste status van het station de status **ontvangen** . |
| NeverReceived | Het station wordt verplaatst naar de **NeverReceived** -status wanneer het pakket voor een taak arriveert, maar het-pakket het station niet bevat. Een station verplaatst ook de status als het twee weken geleden is sinds de service de verzend gegevens heeft ontvangen, maar het pakket nog niet is aangekomen op het Data Center. |
| Geboekt | Een station wordt verplaatst naar de **overdrachts** status wanneer de service begint met het overdragen van gegevens van het station naar Azure Storage. |
| Voltooid | Een station wordt verplaatst naar de status **voltooid** wanneer de service alle gegevens zonder fouten heeft overgebracht.
| CompletedMoreInfo | Een station wordt verplaatst naar de **CompletedMoreInfo** -status wanneer de service enkele problemen heeft ondervonden bij het kopiëren van gegevens van of naar het station. De informatie kan fouten, waarschuwingen of informatieve berichten bevatten over het overschrijven van blobs.
| ShippedBack | Een station wordt verplaatst naar de status **ShippedBack** wanneer het is verzonden van het Data Center terug naar het retour adres. |

In deze installatie kopie van de Azure Portal wordt de status van het station van een voorbeeld taak weer gegeven:

![Status van station weer geven](./media/storage-import-export-service/drivestate.png)

In de volgende tabel worden de fout statussen van het station en de acties voor elke status beschreven.

| Status van station | Gebeurtenis | Oplossing/volgende stap |
|:--- |:--- |:--- |
| NeverReceived | Een station dat is gemarkeerd als **NeverReceived** (omdat het niet is ontvangen als onderdeel van de verzen ding van de taak) arriveert bij een andere verzen ding. | Het operations-team verplaatst het station naar **ontvangen**. |
| N.v.t. | Een station dat geen deel uitmaakt van een taak, arriveert bij het Data Center als onderdeel van een andere taak. | Het station is gemarkeerd als een extra station en wordt aan u geretourneerd wanneer de taak die aan het oorspronkelijke pakket is gekoppeld, is voltooid. |

## <a name="time-to-process-job"></a>Taak voor het verwerken van tijd
De hoeveelheid tijd die nodig is voor het verwerken van een import/export-taak is afhankelijk van een aantal factoren, zoals:

-  Verzend tijd
-  Laden in het Data Center
-  Taak type en grootte van de gegevens die worden gekopieerd
-  Aantal schijven in een taak. 

Import/export-service heeft geen SLA, maar de service streeft naar het volt ooien van de kopie in 7 tot 10 dagen nadat de schijven zijn ontvangen. Naast de status die in azure Portal is gepubliceerd, kunnen REST Api's worden gebruikt om de voortgang van de taak bij te houden. De para meter percentage voltooid in de API-aanroep van de [lijst Jobs](/previous-versions/azure/dn529083(v=azure.100)) bevat de voortgang van het percentage kopiëren.


## <a name="next-steps"></a>Volgende stappen

* [Het hulp programma WAImportExport instellen](storage-import-export-tool-how-to.md)
* [Gegevens overdragen met AzCopy-opdracht regel programma](storage-use-azcopy.md)
* [Voor beeld van Azure import-export REST API](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
