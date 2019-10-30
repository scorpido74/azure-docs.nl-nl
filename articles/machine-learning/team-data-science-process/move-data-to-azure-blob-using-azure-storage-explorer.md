---
title: Blob Storage-gegevens verplaatsen met Azure Storage Explorer-team data Science process
description: Meer informatie over het gebruik van Azure Storage Explorer voor het uploaden en downloaden van gegevens uit Azure Blob-opslag.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 880b81c07aeed7359dfe35e1361a20ecb11e27dd
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053960"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Gegevens verplaatsen van en naar Azure Blob Storage met behulp van Azure Storage Explorer
Azure Storage Explorer is een gratis hulp programma van micro soft waarmee u met Azure Storage gegevens kunt werken in Windows, macOS en Linux. In dit onderwerp wordt beschreven hoe u dit kunt gebruiken om gegevens uit Azure Blob-opslag te uploaden en te downloaden. Het hulp programma kan worden gedownload van [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Als u een VM gebruikt die is ingesteld met de scripts van [Data Science virtual machines in azure](virtual-machines.md), is Azure Storage Explorer al geïnstalleerd op de VM.
> 
> [!NOTE]
> Raadpleeg Azure [BLOB-basis beginselen](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) en [Azure Blob-service](https://msdn.microsoft.com/library/azure/dd179376.aspx)voor een volledige inleiding tot Azure Blob-opslag.   
> 
> 

## <a name="prerequisites"></a>Vereisten
In dit document wordt ervan uitgegaan dat u een Azure-abonnement, een opslag account en de bijbehorende opslag sleutel voor dat account hebt. Voordat u gegevens kunt uploaden/downloaden, moet u de naam en de account sleutel van uw Azure Storage-account kennen. 

* Zie [een gratis proef versie van één maand](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie over het instellen van een Azure-abonnement.
* Zie [over Azure Storage-accounts](../../storage/common/storage-create-storage-account.md)voor instructies over het maken van een opslag account en voor het ophalen van account-en sleutel gegevens. Maak een notitie van de toegangs sleutel voor uw opslag account, omdat u deze sleutel nodig hebt om verbinding te maken met het account met het Azure Storage Explorer hulp programma.
* Het hulp programma Azure Storage Explorer kan worden gedownload van [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Accepteer de standaard instellingen tijdens de installatie.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Azure Storage Explorer gebruiken
In de volgende stappen wordt beschreven hoe u gegevens kunt uploaden/downloaden met behulp van Azure Storage Explorer. 

1. Start Microsoft Azure Storage Explorer.
2. Als u de wizard **Aanmelden bij uw account...** wilt openen, selecteert u het pictogram **Azure-account instellingen** , **voegt u een account toe** en voert u de referenties in. ![een Azure Storage-account toevoegen](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Als u de wizard **verbinding maken met Azure Storage** wilt openen, selecteert u het pictogram **verbinding maken met Azure-opslag** . ![op ' verbinding maken met Azure Storage '](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Voer de toegangs sleutel van uw Azure-opslag account in op de wizard **verbinding maken met Azure Storage** en vervolgens op **volgende**. ![Voer de toegangs sleutel in vanuit het Azure Storage-account](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Voer de naam van het opslag account in het vak **account naam** in en selecteer **volgende**. externe opslag ![koppelen](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Het toegevoegde opslag account moet nu worden weer gegeven. Als u een BLOB-container in een opslag account wilt maken, klikt u met de rechter muisknop op het knoop punt **BLOB containers** in dat account, selecteert u **BLOB-container maken**en voert u een naam in.
7. Als u gegevens wilt uploaden naar een container, selecteert u de doel container en klikt u op de knop **uploaden** .![Storage-accounts](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klik op de **...** rechts van het vak **bestanden** , selecteer een of meer bestanden die u wilt uploaden uit het bestands systeem en klik op **uploaden** om te beginnen met het uploaden van de bestanden.![bestanden uploaden](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Als u gegevens wilt downloaden, selecteert u de BLOB in de bijbehorende container om te downloaden en klikt u op **downloaden**. bestanden ![downloaden](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

