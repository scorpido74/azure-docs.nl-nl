---
title: Blob-opslaggegevens verplaatsen met Azure Storage Explorer - Team Data Science-proces
description: Meer informatie over het gebruik van Azure Storage Explorer om gegevens te uploaden en te downloaden vanuit Azure blob-opslag.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bfc63c6f5aca92fb7fda9e3ecf63ce4c332b12ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720908"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Gegevens verplaatsen van en naar Azure Blob Storage met Azure Storage Explorer
Azure Storage Explorer is een gratis tool van Microsoft waarmee u werken met Azure Storage-gegevens op Windows, macOS en Linux. In dit onderwerp wordt beschreven hoe u deze gebruiken om gegevens uit Azure blob-opslag te uploaden en te downloaden. De tool kan worden gedownload van [Microsoft Azure Storage Explorer.](https://storageexplorer.com/)

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Als u VM gebruikt die is ingesteld met de scripts die worden geleverd door [Virtual-machines voor Gegevenswetenschap in Azure,](virtual-machines.md)is Azure Storage Explorer al op de VM geïnstalleerd.
> 
> [!NOTE]
> Raadpleeg [Azure Blob Basics](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) en Azure Blob [Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)voor een volledige inleiding tot Azure blob-opslag.   
> 
> 

## <a name="prerequisites"></a>Vereisten
In dit document wordt ervan uitgegaan dat u een Azure-abonnement, een opslagaccount en de bijbehorende opslagsleutel voor dat account hebt. Voordat u gegevens uploadt/downloadt, moet u de naam en de accountsleutel van Azure Storage kennen. 

* Zie Gratis proefperiode van één maand voor het instellen van een [Azure-abonnement.](https://azure.microsoft.com/pricing/free-trial/)
* Zie [Over Azure Storage-accounts](../../storage/common/storage-create-storage-account.md)voor instructies over het maken van een opslagaccount en voor het verkrijgen van account- en sleutelgegevens. Noteer de toegangssleutel voor uw opslagaccount, omdat u deze sleutel nodig hebt om verbinding te maken met het account met het Azure Storage Explorer-hulpprogramma.
* Het hulpprogramma Azure Storage Explorer kan worden gedownload van [Microsoft Azure Storage Explorer.](https://storageexplorer.com/) Accepteer de standaardinstellingen tijdens de installatie.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Azure Storage Explorer gebruiken
In de volgende stappen wordt uitgelegd hoe u gegevens uploadt/downloadt met Azure Storage Explorer. 

1. Microsoft Azure Storage Explorer starten.
2. Als u de wizard **Aanmelden bij uw account wilt weergeven...** selecteert u het pictogram **Azure-accountinstellingen,** **voegt u een account toe** en voert u referenties in. 
![Een Azure Storage-account toevoegen](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Als u de wizard **Verbinding maken met Azure Storage** wilt weergeven, selecteert u het pictogram Verbinding maken met **Azure-opslag.** ![Klik op 'Verbinding maken met Azure Storage'](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Voer de toegangssleutel in van uw Azure Storage-account in de wizard **Verbinding maken met Azure Storage** en vervolgens **Volgende**. ![Toegangssleutel invoeren vanuit Azure Storage-account](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Voer de naam van het opslagaccount in het vak **Accountnaam** in en selecteer **Volgende**. ![Externe opslag toevoegen](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Het toegevoegde opslagaccount moet nu worden weergegeven. Als u een blobcontainer in een opslagaccount wilt maken, klikt u met de rechtermuisknop op het **blobcontainerknooppunt** in dat account, selecteert u **Blob-container maken**en voert u een naam in.
7. Als u gegevens wilt uploaden naar een container, selecteert u de doelcontainer en klikt u op de knop **Uploaden.**
![Opslagaccounts](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klik op de **...** rechts van het vak **Bestanden,** selecteer een of meerdere bestanden die u wilt uploaden vanuit het bestandssysteem en klik op **Uploaden** om te beginnen met het uploaden van de bestanden. ![Bestanden uploaden](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Als u gegevens wilt downloaden, selecteert u de blob in de bijbehorende container om te downloaden en klikt u op **Downloaden**. ![Bestanden downloaden](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

