---
title: 'Zelfstudie: On-premises gegevens migreren naar Azure Storage met AzCopy| Microsoft Documenten'
description: In deze zelfstudie gebruikt u AzCopy om gegevens te migreren of gegevens te kopiëren naar of vanuit een blob, tabel en bestandsinhoud. Eenvoudig gegevens migreren vanuit uw lokale opslag naar Azure Storage.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: f7155053072b3533503765dc6f4fbf185d21f0d4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74327521"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Zelfstudie: On-premises gegevens migreren naar cloudopslag met AzCopy

AzCopy is een opdrachtregelprogramma voor het kopiëren van gegevens naar of van Azure Blob Storage, Azure Files en Azure Table Storage met behulp van eenvoudige opdrachten. De opdrachten zijn ontworpen voor optimale prestaties. Met AzCopy kunt u gegevens kopiëren tussen een bestandssysteem en een opslagaccount of tussen opslagaccounts. AzCopy kan worden gebruikt om gegevens te kopiëren van lokale (on-premises) gegevens naar een opslagaccount.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een opslagaccount maken. 
> * AzCopy gebruiken voor het uploaden van al uw gegevens.
> * De gegevens wijzigen voor testdoeleinden.
> * Een geplande taak of Cron-taak maken om te zoeken naar nieuwe bestanden om te uploaden.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Download de nieuwste versie van AzCopy om deze zelfstudie te voltooien. Zie [Aan de slag met AzCopy](storage-use-azcopy-v10.md).

Als u Windows gebruikt, hebt u [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) nodig omdat deze opdracht in deze zelfstudie wordt gebruikt om een taak te plannen. Linux-gebruikers maken in plaats hiervan gebruik van de opdracht crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Een container maken

De eerste stap is het maken van een container, omdat blobs altijd moeten worden geüpload in een container. Containers worden gebruikt als methode voor het ordenen van groepen blobs, zoals u bestanden zou ordenen op de computer of in mappen.

Volg deze stappen voor het maken van een container:

1. Selecteer de knop **Opslagaccounts** op de hoofdpagina en selecteer de opslagaccount die u hebt gemaakt.
2. Selecteer **Blobs** onder **Services** en selecteer vervolgens **Container**.

   ![Een container maken](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Containernamen moeten beginnen met een letter of cijfer. Ze mogen alleen letters, cijfers en het koppelteken (-) bevatten. Zie [Naming and referencing containers, blobs, and metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Containers, blobs en metagegevens een naam geven en hiernaar verwijzen) voor meer regels voor de naamgeving van blobs en containers.

## <a name="download-azcopy"></a>AzCopy downloaden

Download het uitvoerbaar bestand AzCopy V10.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (teer)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Plaats het AzCopy-bestand overal op uw computer. Voeg de locatie van het bestand toe aan de variabele van het systeempad, zodat u vanuit elke map op uw computer naar dit uitvoerbare bestand verwijzen.

## <a name="authenticate-with-azure-ad"></a>Verifiëren met Azure AD

Wijs eerst de rol [Opslagblobgegevensbijdrager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) toe aan uw identiteit. Zie [Toegang verlenen tot Azure blob- en wachtrijgegevens met RBAC in de Azure-portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Open vervolgens een opdrachtprompt, typ de volgende opdracht en druk op ENTER.

```azcopy
azcopy login
```

Met deze opdracht retourneert een verificatiecode en de URL van een website. Open de website, geef de code op en kies de knop **Volgende.**

![Een container maken](media/storage-use-azcopy-v10/azcopy-login.png)

Er verschijnt een aanmeldingsvenster. Meld u in dat venster aan bij uw Azure-account met uw Azure-accountreferenties. Nadat u zich hebt aangemeld, u het browservenster sluiten en AzCopy gaan gebruiken.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Upload de inhoud van een map in Blob-opslag

U kunt AzCopy gebruiken om alle bestanden in een map te uploaden naar Blob-opslag in [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) of [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux). Als u alle blobs in een map wilt uploaden, voert u de volgende AzCopy-opdracht uit:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Vervang `<local-folder-path>` de tijdelijke aanduiding door het pad naar een `C:\myFolder` `/mnt/myFolder`map die bestanden bevat (bijvoorbeeld: of ).

* Vervang de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Vervang `<container-name>` de tijdelijke aanduiding door de naam van de container die u hebt gemaakt.

Als u de inhoud van de opgegeven map opnieuw wilt `--recursive` uploaden naar Blob-opslag, geeft u de optie op. Wanneer u AzCopy met deze optie uitvoert, worden ook alle submappen en hun bestanden geüpload.

## <a name="upload-modified-files-to-blob-storage"></a>Gewijzigde bestanden uploaden naar Blob-opslag

U kunt AzCopy gebruiken om bestanden te uploaden op basis van de datum/tijd waarop deze het laatst zijn gewijzigd. 

Als u dit wilt uitproberen, wijzigt u bestanden of maakt u nieuwe bestanden in uw bronmap voor testdoeleinden. Gebruik vervolgens de `sync` opdracht AzCopy.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Vervang `<local-folder-path>` de tijdelijke aanduiding door het pad naar een `C:\myFolder` `/mnt/myFolder`map die bestanden bevat (bijvoorbeeld: of .

* Vervang de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Vervang `<container-name>` de tijdelijke aanduiding door de naam van de container die u hebt gemaakt.

Zie [Bestanden](storage-use-azcopy-blobs.md#synchronize-files)synchroniseren `sync` voor meer informatie over de opdracht.

## <a name="create-a-scheduled-task"></a>Een geplande taak maken

U kunt een geplande taak of Cron-taak maken die een AzCopy-opdrachtscript uitvoert. Door het script worden nieuwe on-premises gegevens met een bepaald tijdsinterval herkend en geüpload naar de cloudopslag.

Kopieer de AzCopy-opdracht naar een teksteditor. Werk de parameterwaarden van de AzCopy-opdracht bij met de juiste waarden. Sla het bestand op als `script.sh` (Linux) of `script.bat` (Windows) voor AzCopy. 

In deze voorbeelden wordt ervan `myFolder`uitgegaan dat de `mystorageaccount` naam van `mycontainer`uw map is, dat de naam van uw opslagaccount is en dat uw containernaam is .

> [!NOTE]
> Het Linux-voorbeeld voegt een SAS-token toe. Je moet er een in je commando geven. De huidige versie van AzCopy V10 ondersteunt geen Azure AD-autorisatie in cron-taken.

# <a name="linux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true

# <a name="windows"></a>[Windows](#tab/windows)

    azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

---

In deze zelfstudie wordt [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) gebruikt om een geplande taak in Windows te maken. De opdracht [Crontab](http://crontab.org/) wordt gebruikt voor het maken van een Cron-taak in Linux.

 Met **Schtasks** kan een beheerder geplande taken op een lokale of externe computer maken, verwijderen, opvragen, wijzigen, uitvoeren en beëindigen. Met **Cron** kunnen Linux- en Unix-gebruikers op een opgegeven datum en tijd opdrachten of scripts uitvoeren met behulp van [Cron-expressies](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linux"></a>[Linux](#tab/linux)

Voor het maken van een Cron-taak in Linux, voert u de volgende opdracht op een terminal uit:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Wanneer de Cron-expressie `*/5 * * * *` wordt opgegeven in de opdracht, geeft dit aan dat het shellscript `script.sh` om de vijf minuten moet worden uitgevoerd. U kunt het script plannen voor uitvoering op een bepaald dagelijks, maandelijks of jaarlijks tijdstip. Zie [Cron-expressies](https://en.wikipedia.org/wiki/Cron#CRON_expression) voor meer informatie over het instellen van de datum en tijd voor de taakuitvoering.

# <a name="windows"></a>[Windows](#tab/windows)

Als u een geplande taak in Windows wilt maken, voert u de volgende opdracht uit via de opdrachtprompt of in PowerShell:

In dit voorbeeld wordt ervan uitgegaan dat uw script zich in de hoofdstation van uw computer bevindt, maar dat uw script overal kan zijn waar u maar wilt.

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

* [Gegevens verplaatsen van en naar Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

Zie voor meer informatie over AzCopy een van deze artikelen:

* [Aan de slag met AzCopy](storage-use-azcopy-v10.md)

* [Gegevens overbrengen met AzCopy- en blob-opslag](storage-use-azcopy-blobs.md)

* [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

* [Gegevens overbrengen met AzCopy- en Amazon S3-buckets](storage-use-azcopy-s3.md)
 
* [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)
