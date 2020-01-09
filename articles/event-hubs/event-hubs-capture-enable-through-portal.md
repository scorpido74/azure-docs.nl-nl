---
title: Streaming-gebeurtenissen Event Hubs vastleggen met behulp van Azure Portal
description: In dit artikel wordt beschreven hoe u het vastleggen van streaming-gebeurtenissen via Azure Event Hubs kunt inschakelen met behulp van Azure Portal.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: shvija
ms.openlocfilehash: 267f7c47d101406463378ad12c295ace80aecb7f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437242"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Vastleggen van streaming-gebeurtenissen via Azure Event Hubs inschakelen

Met Azure [Event hubs Capture][capture-overview] kunt u automatisch de streaminggegevens in Event hubs leveren aan een [Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) of een [Azure data Lake Store](https://azure.microsoft.com/services/data-lake-store/) account van uw keuze.

Wanneer u de gebeurtenishub maakt, kunt u Capture configureren met behulp van de [Azure-portal](https://portal.azure.com). U kunt de gegevens vastleggen in een [Blob Storage](https://azure.microsoft.com/services/storage/blobs/)-container van Azure of in een [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)-account.

Zie [Event hubs Capture-overzicht][capture-overview]voor meer informatie.

## <a name="capture-data-to-an-azure-storage-account"></a>Gegevens vastleggen in een Azure Storage-account  

Wanneer u een Event Hub maakt, kunt u Capture inschakelen door op het scherm **Event Hub maken** in de portal te klikken op de knop **Aan**. Vervolgens geeft u een opslagaccount en een container op door in de lijst **Capture-provider** te klikken op **Azure Storage**. Omdat Event Hubs Capture gebruikmaakt van service-naar-serviceverificatie met opslag, hoeft u geen verbindingsreeks voor opslag op te geven. De objectkiezer selecteert automatisch de resource-URI voor uw opslagaccount. Als u Azure Resource Manager gebruikt, moet u deze URI expliciet als een tekenreeks opgeven.

Het standaardtijdvenster is 5 minuten. De minimale waarde is 1, de maximale 15. Het venster **Grootte** heeft een bereik van 10-500 MB.

![Tijdvenster voor vastleggen][1]

> [!NOTE]
> U kunt het verzenden van lege bestanden in- of uitschakelen wanneer er geen gebeurtenissen optreden tijdens de periode voor vastleggen. 

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Gegevens vastleggen in een Azure Data Lake Store-account

Als u gegevens wilt vastleggen in Azure Data Lake Store, maakt u een Data Lake Store-account en een Event Hub:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Een Azure Data Lake Store-account en -mappen maken


1. Maak een Data Lake Store-account volgens de instructies in [Aan de slag met Azure Data Lake Store met Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md).
2. Volg de instructies in de sectie [Machtigingen toewijzen aan Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) om een map te maken binnen het Data Lake Store-account waarin u de gegevens uit Event Hubs wilt vastleggen en machtigingen toe te wijzen aan de Event Hubs, zodat deze gegevens naar uw Data Lake Store-account kan schrijven.  


### <a name="create-an-event-hub"></a>Een Event Hub maken

1. De Event Hub moet deel uitmaken van het Azure-abonnement waarin de Azure Data Lake Store-account is opgenomen dat u zojuist hebt gemaakt. Maak de Event Hub door te klikken op de knop **Aan**, onder **Vastleggen** op de pagina **Event Hub maken** van de portal. 
2. Selecteer **Azure Data Lake Store** in de lijst **Capture-provider** op de pagina **Event Hub maken** van de portal.
3. Geef bij **Data Lake Store selecteren** het Data Lake Store-account op dat u eerder hebt gemaakt en voer vervolgens in het veld **Data Lake-pad** het pad in naar de gegevensmap die u hebt gemaakt.

    ![Data Lake Storage-account selecteren][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Capture toevoegen of configureren op een bestaande Event Hub

U kunt Capture configureren op bestaande Event Hubs in Event Hubs-naamruimten. Om Capture in te schakelen op een bestaande Event Hub of de Capture-instellingen te wijzigen, klikt u op de naamruimte om het overzichtsscherm te laden en vervolgens klikt u op de Event Hub waarvan u de Capture-instelling wilt inschakelen of wijzigen. Ten slotte klikt u op de optie **Capture** links in de geopende pagina en wijzigt u de instellingen, zoals weergegeven in de volgende afbeelding:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Azure Blob Storage configureren][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![Azure Data Lake Storage configureren][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Volgende stappen

- Lees de [Event hubs Capture-overzicht][capture-overview]voor meer informatie over het vastleggen van Event hubs.
- U kunt ook Event Hubs Capture configureren met behulp van Azure Resource Manager-sjablonen. Zie voor meer informatie [Capture inschakelen met behulp van een Azure Resource Manager-sjabloon](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Informatie over het maken van een Azure Event Grid-abonnement met een Event Hubs-naamruimte als bron](store-captured-data-data-warehouse.md)
- [Aan de slag met Azure Data Lake Store met Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
