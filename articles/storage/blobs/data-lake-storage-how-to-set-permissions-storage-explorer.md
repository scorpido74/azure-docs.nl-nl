---
title: 'Azure Storage Explorer: Manage access in Azure Data Lake Storage Gen2'
description: In deze instructie leert u hoe u machtigingen instelt met Azure Storage Explorer voor bestanden en mappen in uw voor Azure Data Lake Storage Gen2 geschikte opslagaccount.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b34103e521def678acce17e3292e04fca95b5e6e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327970"
---
# <a name="use-azure-storage-explorer-to-manage-access-in-azure-data-lake-storage-gen2"></a>Use Azure Storage Explorer to manage access in Azure Data Lake Storage Gen2

Bestanden die zijn opgeslagen in Azure Data Lake Storage Gen2 bieden ondersteuning voor gedetailleerde machtigingen en ACL-beheer (ACL: Access Control List, toegangsbeheerlijst). Dankzij de combinatie van gedetailleerde machtigingen en ACL-beheer kunt u de toegang tot uw gegevens op een zeer gedetailleerd niveau beheren.

In dit artikel leert u hoe u Azure Storage Explorer kunt gebruiken om:

> [!div class="checklist"]
> * machtigingen op bestandsniveau in te stellen
> * machtigingen op mapniveau in te stellen
> * gebruikers of groepen toe te voegen aan een toegangsbeheerlijst

## <a name="prerequisites"></a>Vereisten

Voor de beste weergave van het proces vragen wij u om onze [snelstart voor Azure Storage Explorer](data-lake-storage-Explorer.md) te voltooien. This ensures your storage account will be in the most appropriate state (container created and data uploaded to it).

## <a name="managing-access"></a>Toegang beheren

You can set permissions at the root of your container. To do so, you must be logged into Azure Storage Explorer with your individual account with rights to do so (as opposed to with a connection string). Right-click your container and select **Manage Permissions**, bringing up the **Manage Permission** dialog box.

![Microsoft Azure Storage Explorer - Toegang tot mappen beheren](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Via het dialoogvenster **Machtiging beheren** kunt u machtigingen beheren voor de eigenaar en de groep eigenaren. Ook kunt u nieuwe gebruikers en groepen toevoegen aan de toegangsbeheerlijst voor wie u vervolgens machtigingen kunt beheren.

Selecteer het veld **Gebruiker of groep toevoegen** om een nieuwe gebruiker of groep toe te voegen aan de toegangsbeheerlijst.

Voer de bijbehorende AAD-vermelding (Azure Active Directory) in die u aan de lijst wilt toevoegen en selecteer vervolgens **Toevoegen**.

De gebruiker of groep wordt nu weergegeven in het veld **Gebruikers en groepen:** , zodat u kunt beginnen met het beheren van hun machtigingen.

> [!NOTE]
> Het is een best practice en het wordt aanbevolen om een beveiligingsgroep in AAD te maken en machtigingen te onderhouden voor de groep in plaats van afzonderlijke gebruikers. Zie [Best practices voor Data Lake Storage Gen2](data-lake-storage-best-practices.md) voor meer informatie over deze aanbeveling en tevens andere best practices.

Er zijn twee machtigingscategorieën die u kunt toewijzen: Toegangs-ACL’s en Standaard-ACL’s.

* **Access**: Access ACLs control access to an object. Bestanden en mappen hebben beide Toegangs-ACL's.

* **Default**: A template of ACLs associated with a directory that determines the access ACLs for any child items that are created under that directory. Bestanden hebben geen Standaard-ACL's.

Within both of these categories, there are three permissions you can then assign on files or directories: **Read**, **Write**, and **Execute**.

>[!NOTE]
> Wanneer u hier selecties maakt, worden machtigingen voor een reeds bestaand item in de map niet ingesteld. U moet naar elk afzonderlijk item gaan en de machtigingen handmatig instellen als het bestand al bestaat.

U kunt machtigingen voor afzonderlijke mappen beheren, evenals de afzonderlijke bestanden waarmee voor u een gedetailleerd toegangsbeheer mogelijk wordt. Het proces voor het beheren van machtigingen voor zowel mappen als bestanden is hetzelfde als hierboven beschreven. Klik met de rechtermuisknop op het bestand of map waarvoor u machtigingen wilt beheren en volg hetzelfde proces.

## <a name="next-steps"></a>Volgende stappen

In deze instructies hebt u geleerd hoe u machtigingen kunt instellen voor bestanden en mappen met behulp van **Azure Storage Explorer**. Voor meer informatie over ACL's, met inbegrip van Standaard-ACL's, Toegangs-ACL's, hun gedrag en de bijbehorende machtigingen gaat u verder met ons conceptuele artikel over het onderwerp.

> [!div class="nextstepaction"]
> [Toegangsbeheer in Data Lake Storage Gen2](data-lake-storage-access-control.md)
