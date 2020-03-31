---
title: Gebeurtenishubs - Streaminggebeurtenissen vastleggen met Azure-portal
description: In dit artikel wordt beschreven hoe u het vastleggen van streaming-gebeurtenissen via Azure Event Hubs kunt inschakelen met behulp van Azure Portal.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 8a6d9456b00e5520e6f4fbb9ccb77b0260731ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187410"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Vastleggen van streaming-gebeurtenissen via Azure Event Hubs inschakelen

Azure [Event Hubs Capture][capture-overview] stelt u in staat om de streaminggegevens in Gebeurtenishubs automatisch te leveren aan een Azure [Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) of [Azure Data Lake Storage Gen1 of Gen 2-account](https://azure.microsoft.com/services/data-lake-store/) naar keuze.

Wanneer u de gebeurtenishub maakt, kunt u Capture configureren met behulp van de [Azure-portal](https://portal.azure.com). U de gegevens vastleggen op een Azure [Blob-opslagcontainer](https://azure.microsoft.com/services/storage/blobs/) of naar een [Azure Data Lake Storage Gen 1- of Gen 2-account.](https://azure.microsoft.com/services/data-lake-store/)

Zie voor meer informatie het [overzicht van Event Hubs Capture][capture-overview].

## <a name="capture-data-to-azure-storage"></a>Gegevens vastleggen in Azure Storage

Wanneer u een Event Hub maakt, kunt u Capture inschakelen door op het scherm **Event Hub maken** in de portal te klikken op de knop **Aan**. Vervolgens geeft u een opslagaccount en een container op door in de lijst **Capture-provider** te klikken op **Azure Storage**. Omdat Event Hubs Capture gebruikmaakt van service-naar-serviceverificatie met opslag, hoeft u geen verbindingsreeks voor opslag op te geven. De objectkiezer selecteert automatisch de resource-URI voor uw opslagaccount. Als u Azure Resource Manager gebruikt, moet u deze URI expliciet als een tekenreeks opgeven.

Het standaardtijdvenster is 5 minuten. De minimale waarde is 1, de maximale 15. Het venster **Grootte** heeft een bereik van 10-500 MB.

![Tijdvenster voor vastleggen][1]

> [!NOTE]
> U kunt het verzenden van lege bestanden in- of uitschakelen wanneer er geen gebeurtenissen optreden tijdens de periode voor vastleggen. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Gegevens vastleggen op Azure Data Lake Storage Gen 2 

1. Volg [Een artikel voor een opslagaccount maken](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) om een Azure Storage-account te maken. Stel **hiërarchische naamruimte** **in op ingeschakeld** op het tabblad **Geavanceerd** om er een Azure Data Lake Storage Gen 2-account van te maken.
2. Ga bij het maken van een gebeurtenishub de volgende stappen uit: 

    1. Selecteer **Aan** voor **Vastleggen**. 
    2. Selecteer **Azure Storage** als opnameprovider. De azure **data lake store-optie** die u voor de **Capture-provider** ziet, is voor de Gen 1 van Azure Data Lake Storage. Als u een Gen 2 van Azure Data Lake Storage wilt gebruiken, selecteert u **Azure Storage**.
    2. Selecteer de knop **Container selecteren.** 

        ![Vastleggen op Data Lake Storage Gen 2 inschakelen](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Selecteer het **Azure Data Lake Storage Gen 2-account** in de lijst. 

    ![Gegevensmeeropslag gen 2 selecteren](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Selecteer de **container** (bestandssysteem in Data Lake Storage Gen 2).

    ![Bestandssysteem selecteren in de opslag](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Selecteer op de pagina **Gebeurtenishub maken** de optie **Maken**. 

    ![Knop Maken selecteren](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > De container die u maakt in een Azure Data Lake Storage Gen 2 met behulp van deze gebruikersinterface (UI) wordt weergegeven onder **Bestandssystemen** in **Storage Explorer.** Ook wordt het bestandssysteem dat u maakt in een Data Lake Storage Gen 2-account weergegeven als een container in deze gebruikersinterface. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Gegevens vastleggen op Azure Data Lake Storage Gen 1 

Als u gegevens wilt vastleggen in Azure Data Lake Storage Gen 1, maakt u een Data Lake Storage Gen 1-account en een gebeurtenishub:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Een Azure Data Lake Storage Gen 1-account en -mappen maken

1. Maak een Data Lake Storage-account aan volgens de instructies in [Aan de slag met Azure Data Lake Storage Gen 1 met behulp van de Azure-portal.](../data-lake-store/data-lake-store-get-started-portal.md)
2. Volg de instructies in de sectie [Machtigingen toewijzen aan gebeurtenishubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) om een map te maken in het Data Lake Storage Gen 1-account waarin u de gegevens uit gebeurtenishubs wilt vastleggen en machtigingen wilt toewijzen aan gebeurtenishubs, zodat deze gegevens kunnen schrijven naar uw Data Lake Storage Gen 1-account.  


### <a name="create-an-event-hub"></a>Een Event Hub maken

1. De gebeurtenishub moet zich in hetzelfde Azure-abonnement bevinden als het Azure Data Lake Storage Gen 1-account dat u hebt gemaakt. Maak de Event Hub door te klikken op de knop **Aan**, onder **Vastleggen** op de pagina **Event Hub maken** van de portal. 
2. Selecteer **Azure Data Lake Store** in de lijst **Capture-provider** op de pagina **Event Hub maken** van de portal.
3. Geef in **Select Store** naast de vervolgkeuzelijst Data **Lake Store** het Data Lake Storage Gen 1-account op dat u eerder hebt gemaakt en voer in het veld Data **Lake Path** het pad in naar de gegevensmap die u hebt gemaakt.

    ![Data Lake Storage-account selecteren][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Capture toevoegen of configureren op een bestaande Event Hub

U kunt Capture configureren op bestaande Event Hubs in Event Hubs-naamruimten. Om Capture in te schakelen op een bestaande Event Hub of de Capture-instellingen te wijzigen, klikt u op de naamruimte om het overzichtsscherm te laden en vervolgens klikt u op de Event Hub waarvan u de Capture-instelling wilt inschakelen of wijzigen. Ten slotte klikt u op de optie **Capture** links in de geopende pagina en wijzigt u de instellingen, zoals weergegeven in de volgende afbeelding:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Azure Blob Storage configureren][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Azure Data Lake Storage Gen 2 configureren](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Azure Data Lake Storage configureren][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over Event Hubs Capture vindt u in het [overzicht van Event Hubs Capture][capture-overview].
- U kunt ook Event Hubs Capture configureren met behulp van Azure Resource Manager-sjablonen. Zie voor meer informatie [Capture inschakelen met behulp van een Azure Resource Manager-sjabloon](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Informatie over het maken van een Azure Event Grid-abonnement met een Event Hubs-naamruimte als bron](store-captured-data-data-warehouse.md)
- [Aan de slag met Azure Data Lake Store met de Azure-portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
