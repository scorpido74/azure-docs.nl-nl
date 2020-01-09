---
title: Azure CLI gebruiken voor bestanden & Acl's in Azure Data Lake Storage Gen2 (preview-versie)
description: Gebruik de Azure CLI voor het beheren van mappen en ACL'S (toegangs beheer lijsten) in opslag accounts met een hiërarchische naam ruimte.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 596f8334b647daf6fe3a15521f7caeecb0c0e303
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462599"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Azure CLI gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2 (preview-versie)

In dit artikel wordt beschreven hoe u de [Azure-opdracht regel interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) gebruikt voor het maken en beheren van mappen, bestanden en machtigingen in opslag accounts met een hiërarchische naam ruimte. 

> [!IMPORTANT]
> De uitbrei ding `storage-preview` die in dit artikel wordt aanbevolen, is momenteel beschikbaar als open bare preview.

Voor [beeld](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [gen1 naar Gen2-toewijzing](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [feedback geven](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.
> * Azure CLI-versie `2.0.67` of hoger.

## <a name="install-the-storage-cli-extension"></a>De opslag-CLI-extensie installeren

1. Open de [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)of open een opdracht console toepassing zoals Windows Power shell als u de Azure cli lokaal hebt [geïnstalleerd](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .

2. Controleer of de geïnstalleerde versie van de Azure CLI `2.0.67` of hoger is met behulp van de volgende opdracht.

   ```azurecli
    az --version
   ```
   Als uw versie van Azure CLI lager is dan `2.0.67`, installeert u een nieuwere versie. Zie [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Installeer de `storage-preview`-extensie.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

1. Als u Azure CLI lokaal gebruikt, voert u de aanmeldings opdracht uit.

   ```azurecli
   az login
   ```

   Als de CLI uw standaard browser kan openen, wordt dit gedaan en wordt er een Azure-aanmeldings pagina geladen.

   Als dat niet het geval is, opent u een browser pagina op [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voert u de autorisatie code in die wordt weer gegeven in uw Terminal. Meld u vervolgens aan met uw account referenties in de browser.

   Zie aanmelden met Azure CLI voor meer informatie over de verschillende verificatie methoden.

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat als host voor uw statische website gaat.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Vervang de waarde van de tijdelijke `<subscription-id>` door de ID van uw abonnement.

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestands systeem fungeert als een container voor uw bestanden. U kunt er een maken met behulp van de `az storage container create` opdracht. 

In dit voor beeld wordt een bestands systeem gemaakt met de naam `my-file-system`.

```azurecli
az storage container create --name my-file-system
```

## <a name="create-a-directory"></a>Een map maken

Maak een directory verwijzing met behulp van de opdracht `az storage blob directory create`. 

In dit voor beeld wordt een map met de naam `my-directory` toegevoegd aan een bestands systeem met de naam `my-file-system` dat zich bevindt in een account met de naam `mystorageaccount`.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Mapeigenschappen weer geven

U kunt de eigenschappen van een directory afdrukken naar de-console met behulp van de opdracht `az storage blob show`.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam van een map of verplaats deze met behulp van de `az storage blob directory move` opdracht.

In dit voor beeld wordt de naam van een map gewijzigd van de naam `my-directory` naar de naam `my-new-directory`.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een directory met de opdracht `az storage blob directory delete`.

In dit voor beeld wordt een map met de naam `my-directory`verwijderd. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Controleren of er een map bestaat

Bepaal of een specifieke map bestaat in het bestands systeem met behulp van de opdracht `az storage blob directory exist`.

In dit voor beeld wordt onthuld of een map met de naam `my-directory` bestaat in het `my-file-system` bestands systeem. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Downloaden uit een directory

Down load een bestand vanuit een map met behulp van de opdracht `az storage blob directory download`.

In dit voor beeld wordt een bestand met de naam `upload.txt` gedownload uit een map met de naam `my-directory`. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

In dit voor beeld wordt een volledige map gedownload.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

De inhoud van een directory weer geven met behulp van de opdracht `az storage blob directory list`.

In dit voor beeld wordt de inhoud weer gegeven van een map met de naam `my-directory` die zich bevindt in het `my-file-system` bestands systeem van een opslag account met de naam `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Upload een bestand naar een map met behulp van de opdracht `az storage blob directory upload`.

In dit voor beeld wordt een bestand met de naam `upload.txt` geüpload naar een map met de naam `my-directory`. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

In dit voor beeld wordt een volledige directory geüpload.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Bestands eigenschappen weer geven

U kunt de eigenschappen van een bestand afdrukken naar de-console met behulp van de opdracht `az storage blob show`.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Een bestand een andere naam geven of verplaatsen

Wijzig de naam of verplaats een bestand met behulp van de opdracht `az storage blob move`.

In dit voor beeld wordt de naam van een bestand gewijzigd van de naam `my-file.txt` naar de naam `my-file-renamed.txt`.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Een bestand verwijderen

Verwijder een bestand met behulp van de opdracht `az storage blob delete`.

In dit voor beeld wordt een bestand met de naam `my-file.txt` verwijderd

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Machtigingen beheren

U kunt toegangs machtigingen van mappen en bestanden ophalen, instellen en bijwerken.

### <a name="get-directory-and-file-permissions"></a>Map-en bestands machtigingen ophalen

De ACL van een **Directory** ophalen met behulp van de opdracht `az storage blob directory access show`.

In dit voor beeld wordt de ACL van een Directory opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

De toegangs machtigingen van een **bestand** ophalen met behulp van de opdracht `az storage blob access show`. 

In dit voor beeld wordt de ACL van een bestand opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

In de volgende afbeelding ziet u de uitvoer na het ophalen van de ACL van een directory.

![ACL-uitvoer ophalen](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In dit voor beeld heeft de gebruiker die eigenaar is, de machtigingen lezen, schrijven en uitvoeren. De groep die eigenaar is, heeft alleen lees-en uitvoer machtigingen. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangs beheer lijsten.

### <a name="set-directory-and-file-permissions"></a>Map-en bestands machtigingen instellen

Gebruik de `az storage blob directory access set` opdracht om de ACL van een **Directory**in te stellen. 

In dit voor beeld wordt de ACL ingesteld op een map voor de gebruiker die eigenaar is van de groep of andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Gebruik de opdracht `az storage blob access set` om de ACL van een **bestand**in te stellen. 

In dit voor beeld wordt de toegangs beheer lijst voor een bestand ingesteld op de gebruiker die eigenaar is van de groep of van andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
In de volgende afbeelding ziet u de uitvoer na het instellen van de ACL van een bestand.

![ACL-uitvoer ophalen](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In dit voor beeld hebben de gebruiker die eigenaar is en de groep die eigenaar is alleen lees-en schrijf machtigingen. Alle andere gebruikers hebben machtigingen voor schrijven en uitvoeren. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangs beheer lijsten.

### <a name="update-directory-and-file-permissions"></a>Map-en bestands machtigingen bijwerken

Een andere manier om deze machtiging in te stellen, is door de `az storage blob directory access update`-of `az storage blob access update` opdracht te gebruiken. 

Werk de ACL van een map of bestand bij door de para meter `-permissions` in te stellen op de korte vorm van een ACL.

In dit voor beeld wordt de ACL van een **Directory**bijgewerkt.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

In dit voor beeld wordt de ACL van een **bestand**bijgewerkt.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

U kunt ook de gebruiker en groep van een map of bestand die eigenaar is, bijwerken door de `--owner`-of `group`-para meters in te stellen op de entiteit-ID of UPN (User Principal Name) van een gebruiker. 

In dit voor beeld wordt de eigenaar van een map gewijzigd. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

In dit voor beeld wordt de eigenaar van een bestand gewijzigd. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Door de gebruiker gedefinieerde meta gegevens beheren

U kunt door de gebruiker gedefinieerde meta gegevens toevoegen aan een bestand of map met behulp van de `az storage blob directory metadata update` opdracht met een of meer naam/waarde-paren.

In dit voor beeld worden door de gebruiker gedefinieerde meta gegevens toegevoegd voor een map met de naam `my-directory` Directory.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

In dit voor beeld worden alle door de gebruiker gedefinieerde meta gegevens weer gegeven voor de map met de naam `my-directory`.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Zie ook

* [Voorbeeld](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Feedback geven](https://github.com/Azure/azure-cli-extensions/issues)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Broncode](https://github.com/Azure/azure-cli-extensions/tree/master/src)

