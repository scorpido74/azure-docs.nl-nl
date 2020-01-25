---
title: Blob storage gegevens verplaatsen met Azure Storage Explorer - Team Data Science Process
description: Meer informatie over het gebruik van Azure Storage Explorer voor het uploaden en downloaden van gegevens uit Azure Blob-opslag.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720908"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Gegevens verplaatsen naar en van Azure Blob Storage met behulp van Azure Storage Explorer
Azure Storage Explorer is een gratis hulpprogramma van Microsoft waarmee u werkt met Azure Storage-gegevens op Windows, macOS en Linux. In dit onderwerp wordt beschreven hoe u kunt gebruiken om te uploaden en downloaden van gegevens uit Azure blob-opslag. Het hulpprogramma kan worden gedownload vanaf [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Als u virtuele machine die is ingesteld met behulp van de scripts die is geleverd door [Data Science Virtual machines in Azure](virtual-machines.md), en vervolgens Azure Storage Explorer al is geïnstalleerd op de virtuele machine.
> 
> [!NOTE]
> Raadpleeg voor een volledige Inleiding tot Azure blob-opslag, [grondbeginselen van Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) en [Azure Blob-Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Vereisten
Dit document wordt ervan uitgegaan dat u een Azure-abonnement, een storage-account en de bijbehorende opslagsleutel voor dat account hebt. Voordat u gegevens kunt uploaden/downloaden, moet u de account naam en de account sleutel van Azure Storage kennen. 

* Als u een Azure-abonnement instelt, Zie [gratis proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/).
* Zie [over Azure Storage-accounts](../../storage/common/storage-create-storage-account.md)voor instructies over het maken van een opslag account en voor het ophalen van account-en sleutel gegevens. Noteer de toegangssleutel voor uw storage-account, moet u deze sleutel verbinding maken met het account met het hulpprogramma Azure Storage Explorer.
* Het Azure Storage Explorer-hulpprogramma kan worden gedownload vanaf [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Accepteer de standaardwaarden tijdens de installatie.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Azure Storage Explorer gebruiken
De volgende stappen uit het document over het uploaden/downloaden van gegevens met behulp van Azure Storage Explorer. 

1. Start Microsoft Azure Storage Explorer.
2. Om de **aanmelden bij uw account...**  wizard de optie **Azure-Accountinstellingen** pictogram, klikt u vervolgens **een account toevoegen** en geef de referenties. 
![een Azure Storage-account toevoegen](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Als u de wizard **verbinding maken met Azure Storage** wilt weer geven, selecteert u het pictogram **verbinding maken met Azure Storage** . ![klikt u op verbinding maken met Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Voer de toegangs sleutel van uw Azure Storage-account in op de wizard **verbinding maken met Azure Storage** en klik vervolgens op **volgende**. ![Voer de toegangs sleutel van Azure Storage account in](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Voer de naam van opslagaccount in de **accountnaam** vak en selecteer vervolgens **volgende**. ![Externe opslag koppelen](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Het toe te voegen opslag account moet nu worden weer gegeven. Voor het maken van een blob-container in een opslagaccount, met de rechtermuisknop op de **Blobcontainers** knooppunt in het account, selecteer **Blob-Container maken**, en voer een naam.
7. Als u gegevens wilt uploaden naar een container, selecteert u de doel container en klikt u op de knop **uploaden** .
![Opslagaccounts](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klik op de **...**  aan de rechterkant van de **bestanden** Selecteer een of meerdere bestanden voor het uploaden van het bestandssysteem en klikt u op **uploaden** om te beginnen met uploaden van de bestanden.![ Bestanden uploaden](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Voor het downloaden van gegevens, het selecteren van de blob in de bijbehorende container om te downloaden en klikt u op **downloaden**. ![Bestanden downloaden](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

