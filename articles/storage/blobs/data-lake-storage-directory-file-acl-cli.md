---
title: Azure CLI gebruiken voor bestanden & ACL's in Azure Data Lake Storage Gen2 (voorbeeld)
description: Gebruik de Azure CLI om mappen en lijsten met bestands- en adresmaptoegangsbeheer (ACL) te beheren in opslagaccounts met een hiërarchische naamruimte.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486131"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Azure CLI gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2 (voorbeeld)

In dit artikel ziet u hoe u de [Azure Command-Line Interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) gebruiken om mappen, bestanden en machtigingen te maken en te beheren in opslagaccounts met een hiërarchische naamruimte. 

> [!IMPORTANT]
> De `storage-preview` extensie die in dit artikel wordt weergegeven, is momenteel in openbare preview.

[Voorbeeld](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1 naar Gen2-toewijzing](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [Geef feedback](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslagaccount met hiërarchische naamruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.
> * Azure CLI-versie `2.0.67` of hoger.

## <a name="install-the-storage-cli-extension"></a>De CLI-extensie voor opslag installeren

1. Open de [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)of open de Azure CLI lokaal als u de Azure CLI hebt [geïnstalleerd,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) open een opdrachtconsoletoepassing zoals Windows PowerShell.

2. Controleer of de geïnstalleerde versie van `2.0.67` Azure CLI of hoger is met de volgende opdracht.

   ```azurecli
    az --version
   ```
   Als uw versie van Azure `2.0.67`CLI lager is dan , installeer dan een latere versie. Zie [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Installeer de `storage-preview`-extensie.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

1. Als u Azure CLI lokaal gebruikt, voert u de aanmeldingsopdracht uit.

   ```azurecli
   az login
   ```

   Als de CLI uw standaardbrowser kan openen, wordt dit gedaan en wordt een Azure-aanmeldingspagina geladen.

   Anders opent u een [https://aka.ms/devicelogin](https://aka.ms/devicelogin) browserpagina op en voert u de autorisatiecode in die in uw terminal wordt weergegeven. Meld u vervolgens aan met uw accountgegevens in de browser.

   Raadpleeg Aanmelden met Azure CLI voor meer informatie over verschillende verificatiemethoden.

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op een abonnement op het opslagaccount dat uw statische website host.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Vervang `<subscription-id>` de tijdelijke aanduidingswaarde door de id van uw abonnement.

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestandssysteem fungeert als een container voor uw bestanden. U er een `az storage container create` maken met behulp van de opdracht. 

In dit voorbeeld wordt `my-file-system`een bestandssysteem met de naam .

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Een map maken

Maak een adreslijstverwijzing `az storage blob directory create` met de opdracht. 

In dit voorbeeld `my-directory` wordt een map `my-file-system` toegevoegd met de `mystorageaccount`naam aan een bestandssysteem met de naam die zich bevindt in een account met de naam .

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Mapeigenschappen weergeven

U de eigenschappen van een map `az storage blob show` afdrukken op de console met behulp van de opdracht.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>De naam van een map wijzigen of verplaatsen

Wijzig de naam of verplaats `az storage blob directory move` een map met de opdracht.

In dit voorbeeld wordt de `my-directory` naam `my-new-directory`van een map van de naam naar de naam gewijzigd.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Een map verwijderen

Een map verwijderen `az storage blob directory delete` met de opdracht.

In dit voorbeeld wordt `my-directory`een map met de naam . 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Controleren of er een map bestaat

Bepaal met de `az storage blob directory exist` opdracht of er een specifieke map in het bestandssysteem bestaat.

In dit voorbeeld wordt `my-directory` duidelijk of `my-file-system` er een map met de naam in het bestandssysteem bestaat. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Downloaden uit een map

Download een bestand uit een `az storage blob directory download` map met de opdracht.

In dit voorbeeld `upload.txt` wordt een `my-directory`bestand met de naam uit een map met de naam gedownload. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

In dit voorbeeld wordt een hele map gedownload.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

Vermeld de inhoud van een `az storage blob directory list` map met behulp van de opdracht.

In dit voorbeeld worden de `my-directory` inhoud weergegeven `my-file-system` van een map met `mystorageaccount`de naam die zich bevindt in het bestandssysteem van een opslagaccount met de naam . 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Upload een bestand naar een `az storage blob directory upload` map met de opdracht.

In dit voorbeeld wordt `upload.txt` een bestand `my-directory`geüpload dat is vernoemd naar een map met de naam . 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

In dit voorbeeld wordt een hele map geüpload.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Bestandseigenschappen weergeven

U de eigenschappen van een bestand `az storage blob show` afdrukken op de console met behulp van de opdracht.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>De naam van een bestand wijzigen of verplaatsen

Wijzig de naam of verplaats `az storage blob move` een bestand met de opdracht.

In dit voorbeeld wordt de `my-file.txt` naam `my-file-renamed.txt`van een bestand van de naam naar de naam gewijzigd.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Een bestand verwijderen

Een bestand verwijderen `az storage blob delete` met de opdracht.

In dit voorbeeld wordt een bestand met de naam`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Machtigingen beheren

U toegangsmachtigingen voor mappen en bestanden ontvangen, instellen en bijwerken.

> [!NOTE]
> Als u Azure Active Directory (Azure AD) gebruikt om opdrachten te autoriseren, controleert u of uw beveiligingsprincipal de [rol Opslagblob-gegevenseigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)heeft toegewezen . Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie over hoe ACL-machtigingen worden toegepast en de effecten van het wijzigen ervan.

### <a name="get-directory-and-file-permissions"></a>Directory- en bestandsmachtigingen downloaden

Haal de ACL **directory** van een `az storage blob directory access show` map met behulp van de opdracht.

In dit voorbeeld wordt de ACL van een map en wordt de ACL op de console afgedrukt.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Download de toegangsmachtigingen **file** van een `az storage blob access show` bestand met behulp van de opdracht. 

In dit voorbeeld wordt de ACL van een bestand opgevraagd en wordt de ACL op de console afgedrukt.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

De volgende afbeelding toont de uitvoer na het verkrijgen van de ACL van een directory.

![ACL-uitvoer krijgen](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In dit voorbeeld heeft de eigenaar van de gebruiker machtigingen gelezen, geschreven en uitgevoerd. De eigenaargroep heeft alleen lees- en uitvoermachtigingen. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangscontrolelijsten.

### <a name="set-directory-and-file-permissions"></a>Adreslijst- en bestandsmachtigingen instellen

Gebruik `az storage blob directory access set` de opdracht om de ACL van een **map**in te stellen. 

In dit voorbeeld wordt de ACL ingesteld op een map voor de eigenaar, de eigenaar van de groep of andere gebruikers en wordt de ACL op de console afgedrukt.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

In dit voorbeeld wordt de *standaardACL* ingesteld op een map voor de eigenaar, de eigenaar van de groep of andere gebruikers en wordt de ACL op de console afgedrukt.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Gebruik `az storage blob access set` de opdracht om de acl van een **bestand**in te stellen. 

In dit voorbeeld wordt de ACL ingesteld op een bestand voor de eigenaar, de eigenaar van de groep of andere gebruikers en wordt de ACL op de console afgedrukt.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
De volgende afbeelding toont de uitvoer na het instellen van de ACL van een bestand.

![ACL-uitvoer krijgen](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In dit voorbeeld hebben de eigenaarsgebruiker en de eigenaarsgroep alleen lees- en schrijfmachtigingen. Alle andere gebruikers hebben schrijf- en uitvoermachtigingen. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangscontrolelijsten.

### <a name="update-directory-and-file-permissions"></a>Map- en bestandsmachtigingen bijwerken

Een andere manier om deze `az storage blob directory access update` toestemming `az storage blob access update` in te stellen is het gebruik van de of opdracht. 

Werk de ACL van een map `-permissions` of bestand bij door de parameter in te stellen op de korte vorm van een ACL.

In dit voorbeeld wordt de ACL van een **map bijgewerkt.**

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

In dit voorbeeld wordt de ACL van een **bestand bijgewerkt.**

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

U de eigenaar van een map of bestand `--owner` ook `group` bijwerken door de or-parameters in te stellen op de entiteits-id of gebruikersnaam (UPN) van een gebruiker. 

In dit voorbeeld wordt de eigenaar van een map gewijzigd. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

In dit voorbeeld wordt de eigenaar van een bestand gewijzigd. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Metagegevens van gebruikers beheren

U door de gebruiker gedefinieerde metagegevens `az storage blob directory metadata update` toevoegen aan een bestand of map met behulp van de opdracht met een of meer naamwaardeparen.

In dit voorbeeld worden door de `my-directory` gebruiker gedefinieerde metagegevens toegevoegd voor een map met de naam directory.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

In dit voorbeeld worden alle door `my-directory`de gebruiker gedefinieerde metagegevens voor map met de naam .

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Zie ook

* [Monster](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 naar Gen2 mapping](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Feedback geven](https://github.com/Azure/azure-cli-extensions/issues)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Broncode](https://github.com/Azure/azure-cli-extensions/tree/master/src)

