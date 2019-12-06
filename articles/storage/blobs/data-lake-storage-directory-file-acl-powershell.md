---
title: Power shell gebruiken voor bestanden & Acl's in Azure Data Lake Storage Gen2 (preview-versie)
description: Gebruik Power shell-cmdlets voor het beheren van mappen en ACL'S (toegangs beheer lijsten) in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 5eac6b112b46d1b2c80321bdeeee7f4e1fc5f4ac
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873910"
---
# <a name="use-powershell-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Power shell gebruiken voor bestanden & Acl's in Azure Data Lake Storage Gen2 (preview-versie)

In dit artikel leest u hoe u Power shell gebruikt voor het maken en beheren van mappen, bestanden en machtigingen in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld. 

> [!IMPORTANT]
> De Power shell-module die in dit artikel wordt aanbevolen, is momenteel beschikbaar als open bare preview.

[Gen1 naar Gen2 toewijzing](#gen1-gen2-map) | [feedback geven](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.
> * .NET Framework is 4.7.2 of hoger geïnstalleerd. Zie [.NET Framework downloaden](https://dotnet.microsoft.com/download/dotnet-framework).
> * Power shell-versie `5.1` of hoger.

## <a name="install-powershell-modules"></a>Power shell-modules installeren

1. Controleer of de geïnstalleerde versie van Power shell `5.1` of hoger is met behulp van de volgende opdracht. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Zie [bestaande Windows Power shell upgraden](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) voor informatie over het bijwerken van uw versie van Power shell
    
2. Installeer de meest recente **PowershellGet** -module. Sluit vervolgens de Power shell-console en open deze opnieuw.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Installeer **AZ. Storage** preview-module.

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) voor meer informatie over het installeren van Power shell-modules.

## <a name="connect-to-the-account"></a>Verbinding maken met het account

1. Open een Windows Power shell-opdracht venster.

2. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

   ```powershell
   Connect-AzAccount
   ```

3. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account waarin u directory's wilt maken en beheren.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

   Vervang de waarde van de tijdelijke `<subscription-id>` door de ID van uw abonnement.

4. Haal het opslag account op.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   ```

   * Vervang de waarde van de tijdelijke aanduiding `<resource-group-name>` door de naam van uw resource groep.

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

5. De context van het opslag account ophalen.

   ```powershell
   $ctx = $storageAccount.Context
   ```

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestands systeem fungeert als een container voor uw bestanden. U kunt er een maken met behulp van de cmdlet `New-AzDatalakeGen2FileSystem`. 

In dit voor beeld wordt een bestands systeem gemaakt met de naam `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Een map maken

Maak een directory-verwijzing met behulp van de cmdlet `New-AzDataLakeGen2Item`. 

In dit voor beeld wordt een map met de naam `my-directory` toegevoegd aan een bestands systeem.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

In dit voor beeld wordt dezelfde map toegevoegd, maar worden ook de machtigingen, umask, eigenschaps waarden en meta gegevens waarden ingesteld. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mapeigenschappen weer geven

In dit voor beeld wordt een Directory opgehaald met behulp van de cmdlet `Get-AzDataLakeGen2Item` en worden de eigenschaps waarden vervolgens naar de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam van een map of verplaats deze met behulp van de cmdlet `Move-AzDataLakeGen2Item`.

In dit voor beeld wordt de naam van een map gewijzigd van de naam `my-directory` naar de naam `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

In dit voor beeld wordt een map met de naam `my-directory` verplaatst naar een submap van `my-directory-2` met de naam `my-subdirectory`. In dit voor beeld wordt ook een umask toegepast op de submap.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Een map verwijderen

Een directory verwijderen met de cmdlet `Remove-AzDataLakeGen2Item`.

In dit voor beeld wordt een map met de naam `my-directory`verwijderd. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

U kunt de para meter `-Force` gebruiken om het bestand zonder prompt te verwijderen.

## <a name="download-from-a-directory"></a>Downloaden uit een directory

Down load een bestand vanuit een directory met behulp van de cmdlet `Get-AzDataLakeGen2ItemContent`.

In dit voor beeld wordt een bestand met de naam `upload.txt` gedownload uit een map met de naam `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

De inhoud van een directory weer geven met behulp van de cmdlet `Get-AzDataLakeGen2ChildItem`.

In dit voor beeld wordt de inhoud weer gegeven van een map met de naam `my-directory`. 

Als u de inhoud van een bestands systeem wilt weer geven, laat u de para meter `-Path` weg van de opdracht.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

In dit voor beeld wordt de inhoud weer gegeven van een map met de naam `my-directory` en zijn Acl's opgenomen in de lijst. De para meter `-Recurse` wordt ook gebruikt om de inhoud van alle submappen weer te geven.

Als u de inhoud van een bestands systeem wilt weer geven, laat u de para meter `-Path` weg van de opdracht.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Upload een bestand naar een map met behulp van de cmdlet `New-AzDataLakeGen2Item`.

In dit voor beeld wordt een bestand met de naam `upload.txt` geüpload naar een map met de naam `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

In dit voor beeld wordt hetzelfde bestand geüpload, maar worden vervolgens de machtigingen, umask, eigenschaps waarden en meta gegevens waarden van het doel bestand ingesteld. In dit voor beeld worden deze waarden ook afgedrukt naar de-console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Bestands eigenschappen weer geven

In dit voor beeld wordt een bestand opgehaald met behulp van de cmdlet `Get-AzDataLakeGen2Item` en worden vervolgens eigenschappen waarden naar de console afgedrukt.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>Een bestand verwijderen

Een bestand verwijderen met behulp van de cmdlet `Remove-AzDataLakeGen2Item`.

In dit voor beeld wordt een bestand met de naam `upload.txt`verwijderd. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

U kunt de para meter `-Force` gebruiken om het bestand zonder prompt te verwijderen.

## <a name="manage-access-permissions"></a>Toegangs machtigingen beheren

U kunt toegangs machtigingen van mappen en bestanden ophalen, instellen en bijwerken.

### <a name="get-directory-and-file-permissions"></a>Map-en bestands machtigingen ophalen

De ACL van een map of bestand ophalen met behulp van de cmdlet `Get-AzDataLakeGen2Item`.

In dit voor beeld wordt de ACL van een **Directory**opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

In dit voor beeld wordt de ACL van een **bestand** opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

In de volgende afbeelding ziet u de uitvoer na het ophalen van de ACL van een directory.

![ACL-uitvoer ophalen](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

In dit voor beeld heeft de gebruiker die eigenaar is, de machtigingen lezen, schrijven en uitvoeren. De groep die eigenaar is, heeft alleen lees-en uitvoer machtigingen. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangs beheer lijsten.

### <a name="set-directory-and-file-permissions"></a>Map-en bestands machtigingen instellen

Gebruik de cmdlet `New-AzDataLakeGen2ItemAclObject` om een ACL te maken voor de gebruiker die eigenaar is, de groep die eigenaar is of andere gebruikers. Gebruik vervolgens de cmdlet `Update-AzDataLakeGen2Item` om de ACL door te voeren.

In dit voor beeld wordt de ACL ingesteld op een **map** voor de gebruiker die eigenaar is van de groep of andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
In dit voor beeld wordt de toegangs beheer lijst voor een **bestand** ingesteld op de gebruiker die eigenaar is van de groep of van andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

In de volgende afbeelding ziet u de uitvoer na het instellen van de ACL van een bestand.

![ACL-uitvoer ophalen](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

In dit voor beeld hebben de gebruiker die eigenaar is en de groep die eigenaar is alleen lees-en schrijf machtigingen. Alle andere gebruikers hebben machtigingen voor schrijven en uitvoeren. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangs beheer lijsten.

### <a name="update-directory-and-file-permissions"></a>Map-en bestands machtigingen bijwerken

Gebruik de cmdlet `Get-AzDataLakeGen2Item` om de ACL van een map of bestand op te halen. Gebruik vervolgens de cmdlet `New-AzDataLakeGen2ItemAclObject` om een nieuwe ACL-vermelding te maken. Gebruik de cmdlet `Update-AzDataLakeGen2Item` om de nieuwe toegangs beheer lijst toe te passen.

In dit voor beeld krijgt de gebruiker de machtiging schrijven en uitvoeren voor een directory.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```
In dit voor beeld krijgt de gebruiker de machtiging schrijven en uitvoeren voor een bestand.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $acl
```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Machtigingen instellen voor alle items in een bestands systeem

U kunt de `Get-AzDataLakeGen2Item` en de para meter `-Recurse` samen met de cmdlet `Update-AzDataLakeGen2Item` recursief gebruiken om de toegangs beheer lijst van alle mappen en bestanden in een bestands systeem in te stellen. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Toewijzing van gen1 naar Gen2

In de volgende tabel ziet u hoe de cmdlets die worden gebruikt voor Data Lake Storage Gen1 worden toegewezen aan de cmdlets voor Data Lake Storage Gen2.

|Data Lake Storage Gen1-cmdlet| Data Lake Storage Gen2-cmdlet| Opmerkingen |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Standaard worden met de cmdlet Get-AzDataLakeGen2ChildItem alleen de onderliggende items op het eerste niveau weer gegeven. Met de para meter-recursief worden onderliggende items recursief weer gegeven. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|De uitvoer items van de cmdlet Get-AzDataLakeGen2Item hebben eigenschappen deze eigenschappen: ACL, eigenaar, groep, machtiging.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|De cmdlet Get-AzDataLakeGen2FileContent laadt bestand inhoud naar een lokaal bestand.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Met deze cmdlet wordt de nieuwe bestands inhoud van een lokaal bestand geüpload.|
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Met de cmdlet Update-AzDataLakeGen2Item wordt één enkel item bijgewerkt en niet recursief. Als u recursief wilt bijwerken, kunt u items weer geven met behulp van de cmdlet Get-AzDataLakeStoreChildItem en vervolgens pijp lijn naar de cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Met de cmdlet Get-AzDataLakeGen2Item wordt een fout gerapporteerd als het item niet bestaat.|



## <a name="see-also"></a>Zie ook

* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Azure PowerShell gebruiken met Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* [Opslag-Power shell-cmdlets](/powershell/module/az.storage).

