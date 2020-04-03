---
title: Azure Data Lake Storage Gen2 PowerShell voor bestanden & ACL's (voorbeeld)
description: PowerShell-cmdlets gebruiken om mappen en lijsten met bestands- en adresmaptoegangsbeheer (ACL) te beheren in opslagaccounts waarop hiërarchische naamruimte (HNS) is ingeschakeld.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 9be3b2c9b2624d4cd758081703373a433861e4a7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585314"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>PowerShell gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2 (voorbeeld)

In dit artikel ziet u hoe u PowerShell gebruiken voor het maken en beheren van mappen, bestanden en machtigingen in opslagaccounts waarop hiërarchische naamruimte (HNS) is ingeschakeld. 

> [!IMPORTANT]
> De PowerShell-module die in dit artikel wordt weergegeven, bevindt zich momenteel in een openbare preview.

[Gen1 naar Gen2 mapping](#gen1-gen2-map) | [Geef feedback](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslagaccount met hiërarchische naamruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.
> * .NET Framework is 4.7.2 of hoger geïnstalleerd. Zie [.NET Framework downloaden](https://dotnet.microsoft.com/download/dotnet-framework).
> * PowerShell-versie `5.1` of hoger.

## <a name="install-powershell-modules"></a>PowerShell-modules installeren

1. Controleer of de geïnstalleerde versie van `5.1` PowerShell of hoger is met de volgende opdracht.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Zie [Bestaande Windows PowerShell upgraden](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) als u uw versie van PowerShell wilt upgraden
    
2. Installeer de nieuwste **PowershellGet-module.** Sluit vervolgens de PowerShell-console en open deze opnieuw.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force 
   ```

3. Installeer de voorbeeldmodule **voor Az.Storage.**

   ```powershell
   Install-Module az.storage -RequiredVersion 1.13.3-preview -Repository PSGallery -AllowClobber -AllowPrerelease -Force 
   ```

   Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) voor meer informatie over het installeren van PowerShell-modules

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Open een Windows PowerShell-opdrachtvenster en meld u `Connect-AzAccount` aan bij uw Azure-abonnement met de opdracht en volg de aanwijzingen op het scherm.

```powershell
Connect-AzAccount
```

Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op een abonnement op het opslagaccount waarin u mappen wilt maken en beheren. Vervang in dit `<subscription-id>` voorbeeld de tijdelijke waarde door de id van uw abonnement.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Kies vervolgens hoe u wilt dat uw opdrachten toestemming krijgen voor het opslagaccount. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Optie 1: Autorisatie verkrijgen met Azure Active Directory (AD)

Met deze aanpak zorgt het systeem ervoor dat uw gebruikersaccount over de juiste RBAC-toewijzingen (Role-based access control) en ACL-machtigingen beschikt. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Optie 2: Autorisatie verkrijgen met behulp van de opslagaccountsleutel

Met deze aanpak controleert het systeem geen RBAC- of ACL-machtigingen.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestandssysteem fungeert als een container voor uw bestanden. U er een `New-AzDatalakeGen2FileSystem` maken met behulp van de cmdlet. 

In dit voorbeeld wordt `my-file-system`een bestandssysteem met de naam .

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Een map maken

Maak een mapverwijzing `New-AzDataLakeGen2Item` met de cmdlet. 

In dit voorbeeld `my-directory` wordt een map toegevoegd met de naam aan een bestandssysteem.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

In dit voorbeeld wordt dezelfde map toegevoegd, maar worden ook de waarden voor machtigingen, umask, eigenschapswaarden en metagegevens ingesteld. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mapeigenschappen weergeven

In dit voorbeeld wordt `Get-AzDataLakeGen2Item` een map met behulp van de cmdlet en worden eigenschapswaarden afgedrukt op de console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Metadata
$dir.Properties
```

## <a name="rename-or-move-a-directory"></a>De naam van een map wijzigen of verplaatsen

Wijzig de naam of verplaats `Move-AzDataLakeGen2Item` een map met de cmdlet of verplaats deze.

In dit voorbeeld wordt de `my-directory` naam `my-new-directory`van een map van de naam naar de naam gewijzigd.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Gebruik `-Force` de parameter als u zonder aanwijzingen wilt overschrijven.

In dit voorbeeld `my-directory` wordt een map `my-directory-2` `my-subdirectory`met de naam verplaatst naar een submap met de naam . In dit voorbeeld wordt ook een umask op de submap gebruikt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een map `Remove-AzDataLakeGen2Item` met behulp van de cmdlet.

In dit voorbeeld wordt `my-directory`een map met de naam . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

U `-Force` de parameter gebruiken om het bestand te verwijderen zonder een prompt.

## <a name="download-from-a-directory"></a>Downloaden uit een map

Download een bestand uit een `Get-AzDataLakeGen2ItemContent` map met de cmdlet.

In dit voorbeeld `upload.txt` wordt een `my-directory`bestand met de naam uit een map met de naam gedownload. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

Vermeld de inhoud van een `Get-AzDataLakeGen2ChildItem` map met behulp van de cmdlet. U de `-OutputUserPrincipalName` optionele parameter gebruiken om de naam (in plaats van de object-ID) van gebruikers te krijgen.

In dit voorbeeld wordt de `my-directory`inhoud van een map met de naam .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

In dit voorbeeld worden geen `ACL` `Permissions`waarden `Group`voor `Owner` de eigenschappen , en eigenschappen, als - teruggegeven. Gebruik de `-FetchProperty` parameter om deze waarden te verkrijgen. 

In het volgende voorbeeld worden de inhoud van `-FetchProperty` dezelfde map weergegeven, `ACL` `Permissions`maar `Group`wordt `Owner` de parameter ook gebruikt om waarden voor de eigenschappen , , en eigenschappen terug te geven. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Als u de inhoud van een `-Path` bestandssysteem wilt weergeven, laat u de parameter weg uit de opdracht.

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Upload een bestand naar een `New-AzDataLakeGen2Item` map met de cmdlet.

In dit voorbeeld wordt `upload.txt` een bestand `my-directory`geüpload dat is vernoemd naar een map met de naam . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

In dit voorbeeld wordt hetzelfde bestand geüpload, maar worden de waarden voor machtigingen, umask, eigenschapswaarden en metagegevens van het doelbestand ingesteld. In dit voorbeeld worden deze waarden ook afgedrukt op de console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Bestandseigenschappen weergeven

In dit voorbeeld wordt `Get-AzDataLakeGen2Item` een bestand met behulp van de cmdlet en worden eigenschapswaarden afgedrukt op de console.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Metadata
$file.Properties
```

## <a name="delete-a-file"></a>Een bestand verwijderen

Een bestand verwijderen `Remove-AzDataLakeGen2Item` met de cmdlet.

In dit voorbeeld wordt `upload.txt`een bestand met de naam verwijderd. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

U `-Force` de parameter gebruiken om het bestand te verwijderen zonder een prompt.

## <a name="manage-access-permissions"></a>Toegangsmachtigingen beheren

U toegangsmachtigingen voor bestandssystemen, mappen en bestanden ontvangen, instellen en bijwerken.

> [!NOTE]
> Als u Azure Active Directory (Azure AD) gebruikt om opdrachten te autoriseren, controleert u of uw beveiligingsprincipal de [rol Opslagblob-gegevenseigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)heeft toegewezen . Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie over hoe ACL-machtigingen worden toegepast en de effecten van het wijzigen ervan.

### <a name="get-permissions"></a>Machtigingen aanvragen

Haal de ACL van een map `Get-AzDataLakeGen2Item`of bestand met behulp van de cmdlet.


In dit voorbeeld wordt de ACL van een **bestandssysteem** en wordt de ACL op de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

In dit voorbeeld wordt de ACL van een **map**en wordt de ACL afgedrukt op de console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

In dit voorbeeld wordt de ACL van een **bestand opgevraagd** en wordt de ACL op de console afgedrukt.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

De volgende afbeelding toont de uitvoer na het verkrijgen van de ACL van een directory.

![ACL-uitvoer krijgen](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

In dit voorbeeld heeft de eigenaar van de gebruiker machtigingen gelezen, geschreven en uitgevoerd. De eigenaargroep heeft alleen lees- en uitvoermachtigingen. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangscontrolelijsten.

### <a name="set-or-update-permissions"></a>Machtigingen instellen of bijwerken

Gebruik `set-AzDataLakeGen2ItemAclObject` de cmdlet om een ACL te maken voor de eigenaar, eigenaar van de groep of andere gebruikers. Gebruik vervolgens `Update-AzDataLakeGen2Item` de cmdlet om de ACL te plegen.

In dit voorbeeld wordt de ACL ingesteld op een **bestandssysteem** voor de eigenaar, de eigenaar van de groep of andere gebruikers en wordt de ACL op de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$filesystem.ACL
```

In dit voorbeeld wordt de ACL ingesteld op een **map** voor de eigenaar, de eigenaar van de groep of andere gebruikers en wordt de ACL op de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
In dit voorbeeld wordt de ACL ingesteld op een **bestand** voor de eigenaar, de eigenaar van de groep of andere gebruikers en wordt de ACL op de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

De volgende afbeelding toont de uitvoer na het instellen van de ACL van een bestand.

![ACL-uitvoer krijgen](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

In dit voorbeeld hebben de eigenaarsgebruiker en de eigenaarsgroep alleen lees- en schrijfmachtigingen. Alle andere gebruikers hebben schrijf- en uitvoermachtigingen. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangscontrolelijsten.


### <a name="set-permissions-on-all-items-in-a-file-system"></a>Machtigingen instellen voor alle items in een bestandssysteem

U `Get-AzDataLakeGen2Item` de `-Recurse` parameter en `Update-AzDataLakeGen2Item` de parameter samen met de cmdlet gebruiken om de ACL van alle mappen en bestanden in een bestandssysteem in te stellen. 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -FetchProperty | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 naar Gen2 Mapping

In de volgende tabel ziet u hoe de cmdlets die worden gebruikt voor Data Lake Storage Gen1, worden toegewezen aan de cmdlets voor Data Lake Storage Gen2.

|Data Lake Storage Gen1-cmdlet| Data Lake Storage Gen2-cmdlet| Opmerkingen |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-azdatalakegen2ChildItem|Standaard worden in de cmdlet Get-AzDataLakeLakeGen2ChildItem alleen de onderliggende items op het eerste niveau weergegeven. De parameter -Recurse geeft een lijst van onderliggende items die opnieuw worden weergegeven. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzdatalakeStoreItemOwner<br>Toestemming voor get-azdatalakestoreitem|Get-AzDataLakeGen2Item|De uitvoeritems van de cmdlet Get-AzDataLakeGen2Item hebben deze eigenschappen: Acl, Eigenaar, Groep, Toestemming.|
|Get-azdatalakestoreitemcontent|Get-azdatalakegen2FileContent|De cmdlet Get-AzDataLakeLakeGen2FileContent download bestandsinhoud naar lokaal bestand.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|Nieuw-AzDatalakeStoreItem|Nieuw-AzDataLakeGen2Item|Deze cmdlet uploadt de nieuwe bestandsinhoud uit een lokaal bestand.|
|Verwijderen-AzDatalakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-azdatalakeStoreItemOwner<br>Set-azdatalakeStoreItem-toestemming<br>Set-AzDataLakeStoreItemAcl|Update-azdatalakegen2Item|De cmdlet Update-AzDataLakeGen2Item werkt slechts één item bij en niet recursief. Als u recursief wilt bijwerken, vermeldt u items met behulp van de cmdlet Get-AzDataLakeStoreChildItem en vervolgens naar de cmdlet Update-AzDataLakeGen2Item.|
|Test-azdatalakestoreitem|Get-AzDataLakeGen2Item|De cmdlet Get-AzDataLakeGen2Item meldt een fout als het item niet bestaat.|



## <a name="see-also"></a>Zie ook

* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Azure PowerShell gebruiken met Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* [Storage PowerShell-cmdlets](/powershell/module/az.storage).

