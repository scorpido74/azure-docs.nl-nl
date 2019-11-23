---
title: 'Tutorial: Migrate on-premises data to Azure Storage with AzCopy| Microsoft Docs'
description: In deze zelfstudie gebruikt u AzCopy om gegevens te migreren of gegevens te kopiëren naar of vanuit een blob, tabel en bestandsinhoud. Eenvoudig gegevens migreren vanuit uw lokale opslag naar Azure Storage.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: f7155053072b3533503765dc6f4fbf185d21f0d4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327521"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Tutorial: Migrate on-premises data to cloud storage with AzCopy

AzCopy is een opdrachtregelprogramma voor het kopiëren van gegevens naar of van Azure Blob Storage, Azure Files en Azure Table Storage met behulp van eenvoudige opdrachten. De opdrachten zijn ontworpen voor optimale prestaties. Met AzCopy kunt u gegevens kopiëren tussen een bestandssysteem en een opslagaccount of tussen opslagaccounts. AzCopy kan worden gebruikt om gegevens te kopiëren van lokale (on-premises) gegevens naar een opslagaccount.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een opslagaccount maken. 
> * AzCopy gebruiken voor het uploaden van al uw gegevens.
> * De gegevens wijzigen voor testdoeleinden.
> * Een geplande taak of Cron-taak maken om te zoeken naar nieuwe bestanden om te uploaden.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

To complete this tutorial, download the latest version of AzCopy. See [Get started with AzCopy](storage-use-azcopy-v10.md).

Als u Windows gebruikt, hebt u [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) nodig omdat deze opdracht in deze zelfstudie wordt gebruikt om een taak te plannen. Linux-gebruikers maken in plaats hiervan gebruik van de opdracht crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Een container maken

De eerste stap is het maken van een container, omdat blobs altijd moeten worden geüpload in een container. Containers worden gebruikt als methode voor het ordenen van groepen blobs, zoals u bestanden zou ordenen op de computer of in mappen.

Volg deze stappen voor het maken van een container:

1. Selecteer de knop **Opslagaccounts** op de hoofdpagina en selecteer de opslagaccount die u hebt gemaakt.
2. Selecteer **Blobs** onder **Services** en selecteer vervolgens **Container**.

   ![Een container maken](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Containernamen moeten beginnen met een letter of cijfer. Ze mogen alleen letters, cijfers en het koppelteken (-) bevatten. Zie [Naming and referencing containers, blobs, and metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Containers, blobs en metagegevens een naam geven en hiernaar verwijzen) voor meer regels voor de naamgeving van blobs en containers.

## <a name="download-azcopy"></a>Download AzCopy

Download the AzCopy V10 executable file.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Place the AzCopy file anywhere on your computer. Add the location of the file to your system path variable so that you can refer to this executable file from any folder on your computer.

## <a name="authenticate-with-azure-ad"></a>Verifiëren met Azure AD

First, assign the [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) role to your identity. See [Grant access to Azure blob and queue data with RBAC in the Azure portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Then, open a command prompt, type the following command, and press the ENTER key.

```azcopy
azcopy login
```

This command returns an authentication code and the URL of a website. Open the website, provide the code, and then choose the **Next** button.

![Een container maken](media/storage-use-azcopy-v10/azcopy-login.png)

A sign-in window will appear. In that window, sign into your Azure account by using your Azure account credentials. After you've successfully signed in, you can close the browser window and begin using AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Upload de inhoud van een map in Blob-opslag

U kunt AzCopy gebruiken om alle bestanden in een map te uploaden naar Blob-opslag in [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) of [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux). Als u alle blobs in een map wilt uploaden, voert u de volgende AzCopy-opdracht uit:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Replace the `<local-folder-path>` placeholder with the path to a folder that contains files (For example: `C:\myFolder` or `/mnt/myFolder`).

* Vervang de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Replace the `<container-name>` placeholder with the name of the container that you created.

To upload the contents of the specified directory to Blob storage recursively, specify the `--recursive` option. When you run AzCopy with this option, all subfolders and their files are uploaded as well.

## <a name="upload-modified-files-to-blob-storage"></a>Gewijzigde bestanden uploaden naar Blob-opslag

You can use AzCopy to upload files based on their last-modified time. 

Als u dit wilt uitproberen, wijzigt u bestanden of maakt u nieuwe bestanden in uw bronmap voor testdoeleinden. Then, use the AzCopy `sync` command.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Replace the `<local-folder-path>` placeholder with the path to a folder that contains files (For example: `C:\myFolder` or `/mnt/myFolder`.

* Vervang de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Replace the `<container-name>` placeholder with the name of the container that you created.

To learn more about the `sync` command, see [Synchronize files](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Een geplande taak maken

U kunt een geplande taak of Cron-taak maken die een AzCopy-opdrachtscript uitvoert. Door het script worden nieuwe on-premises gegevens met een bepaald tijdsinterval herkend en geüpload naar de cloudopslag.

Kopieer de AzCopy-opdracht naar een teksteditor. Werk de parameterwaarden van de AzCopy-opdracht bij met de juiste waarden. Sla het bestand op als `script.sh` (Linux) of `script.bat` (Windows) voor AzCopy. 

These examples assume that your folder is named `myFolder`, your storage account name is `mystorageaccount` and your container name is `mycontainer`.

> [!NOTE]
> The Linux example appends a SAS token. You'll need to provide one in your command. The current version of AzCopy V10 doesn't support Azure AD authorization in cron jobs.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

---

In deze zelfstudie wordt [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) gebruikt om een geplande taak in Windows te maken. De opdracht [Crontab](http://crontab.org/) wordt gebruikt voor het maken van een Cron-taak in Linux.

 Met **Schtasks** kan een beheerder geplande taken op een lokale of externe computer maken, verwijderen, opvragen, wijzigen, uitvoeren en beëindigen. Met **Cron** kunnen Linux- en Unix-gebruikers op een opgegeven datum en tijd opdrachten of scripts uitvoeren met behulp van [Cron-expressies](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Voor het maken van een Cron-taak in Linux, voert u de volgende opdracht op een terminal uit:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Wanneer de Cron-expressie `*/5 * * * *` wordt opgegeven in de opdracht, geeft dit aan dat het shellscript `script.sh` om de vijf minuten moet worden uitgevoerd. U kunt het script plannen voor uitvoering op een bepaald dagelijks, maandelijks of jaarlijks tijdstip. Zie [Cron-expressies](https://en.wikipedia.org/wiki/Cron#CRON_expression) voor meer informatie over het instellen van de datum en tijd voor de taakuitvoering.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Als u een geplande taak in Windows wilt maken, voert u de volgende opdracht uit via de opdrachtprompt of in PowerShell:

This example assumes that your script is located in the root drive of your computer, but your script can be anywhere that you want.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

De opdracht gebruikt:
- De parameter `/SC` om een planning van een minuut op te geven.
- De parameter `/MO` om een interval van vijf minuten op te geven.
- De parameter `/TN` om de naam van de taak op te geven.
- De parameter `/TR` om het pad naar het bestand `script.bat` op te geven.

Zie [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes) voor meer informatie over het maken van een geplande taak in Windows.

---

Als u wilt valideren of de geplande taak/Cron-taak correct wordt uitgevoerd, maakt u nieuwe bestanden in de map `myFolder`. Wacht vijf minuten om te bevestigen dat de nieuwe bestanden zijn geüpload naar uw opslagaccount. Ga naar de logboekmap om de uitvoerlogboeken van de geplande taak of Cron-taak weer te geven.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over manieren om on-premises gegevens te verplaatsen naar Azure Storage en vice versa, volgt u deze koppeling:

* [Gegevens verplaatsen naar en uit Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

For more information about AzCopy, see any of these articles:

* [Get started with AzCopy](storage-use-azcopy-v10.md)

* [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md)

* [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md)

* [Transfer data with AzCopy and Amazon S3 buckets](storage-use-azcopy-s3.md)
 
* [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md)
