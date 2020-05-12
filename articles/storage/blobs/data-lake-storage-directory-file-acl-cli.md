---
title: Azure CLI gebruiken voor bestanden & Acl's in Azure Data Lake Storage Gen2
description: Gebruik de Azure CLI voor het beheren van mappen en ACL'S (toegangs beheer lijsten) in opslag accounts met een hiërarchische naam ruimte.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 79a87f02c8730e0b2110e7475de721f11beda568
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120603"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Azure CLI gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2

In dit artikel wordt beschreven hoe u de [Azure-opdracht regel interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) gebruikt voor het maken en beheren van mappen, bestanden en machtigingen in opslag accounts met een hiërarchische naam ruimte. 

Toewijzing van gen1 [naar Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)  |  [Feedback geven](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.
> * Azure CLI-versie `2.5.1` of hoger.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Controleer of u de juiste versie van Azure CLI hebt geïnstalleerd

1. Open de [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)of open een opdracht console toepassing zoals Windows Power shell als u de Azure cli lokaal hebt [geïnstalleerd](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .

2. Controleer of de versie van de Azure CLI die is geïnstalleerd `2.5.1` of hoger is met behulp van de volgende opdracht.

   ```azurecli
    az --version
   ```
   Als uw versie van Azure CLI lager is dan `2.5.1` , installeert u een nieuwere versie. Zie [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-account"></a>Verbinding maken met het account

1. Als u Azure CLI lokaal gebruikt, voert u de aanmeldings opdracht uit.

   ```azurecli
   az login
   ```

   Als de CLI uw standaard browser kan openen, wordt dit gedaan en wordt er een Azure-aanmeldings pagina geladen.

   Als dat niet het geval is, opent u een browser pagina op [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voert u de autorisatie code in die wordt weer gegeven in uw Terminal. Meld u vervolgens aan met uw account referenties in de browser.

   Zie voor meer informatie over verschillende verificatie methoden [toegang verlenen tot BLOB-of wachtrij gegevens met Azure cli](../common/authorize-data-operations-cli.md).

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat als host voor uw statische website gaat.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

> [!NOTE]
> In het voor beeld in dit artikel wordt de autorisatie Azure Active Directory (AD) weer gegeven. Zie [toegang verlenen aan BLOB-of wachtrij gegevens met Azure cli](../common/authorize-data-operations-cli.md)voor meer informatie over verificatie methoden.

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestands systeem fungeert als een container voor uw bestanden. U kunt er een maken met behulp van de `az storage fs create` opdracht. 

In dit voor beeld wordt een bestands systeem gemaakt met de naam `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>Eigenschappen van bestands systeem weer geven

U kunt de eigenschappen van een bestands systeem naar de-console afdrukken met behulp van de `az storage fs show` opdracht.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>Inhoud van het bestands systeem weer geven

De inhoud van een directory weer geven met behulp van de `az storage fs file list` opdracht.

In dit voor beeld wordt de inhoud van een bestands systeem met de naam weer gegeven `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>Een bestands systeem verwijderen

Verwijder een bestands systeem met behulp van de `az storage fs delete` opdracht.

In dit voor beeld wordt een bestands systeem met de naam verwijderd `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Een map maken

Maak een directory verwijzing met behulp van de `az storage fs directory create` opdracht. 

In dit voor beeld wordt een map toegevoegd `my-directory` met de naam van een bestands systeem `my-file-system` dat zich in een account bevindt met de naam `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Mapeigenschappen weer geven

U kunt de eigenschappen van een directory naar de-console afdrukken met behulp van de `az storage fs directory show` opdracht.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam van een map of verplaats deze met behulp van de `az storage fs directory move` opdracht.

In het volgende voor beeld wordt de naam van een map gewijzigd van de naam `my-directory` `my-new-directory` in hetzelfde bestands systeem.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

In dit voor beeld wordt een map verplaatst naar een bestands systeem met de naam `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Een map verwijderen

Een directory verwijderen met behulp van de `az storage fs directory delete` opdracht.

In dit voor beeld wordt een map met de naam verwijderd `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Controleren of er een map bestaat

Bepaal of een specifieke map bestaat in het bestands systeem met behulp van de `az storage fs directory exists` opdracht.

In dit voor beeld wordt onthuld of een map `my-directory` met de naam in het `my-file-system` Bestands systeem bestaat. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Downloaden uit een directory

Down load een bestand vanuit een map met behulp van de `az storage fs file download` opdracht.

In dit voor beeld wordt een bestand gedownload met de naam `upload.txt` van een map met de naam `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

De inhoud van een directory weer geven met behulp van de `az storage fs file list` opdracht.

In dit voor beeld wordt de inhoud weer gegeven van een map met `my-directory` de naam die zich bevindt in het `my-file-system` Bestands systeem van een opslag account met de naam `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Upload een bestand naar een map met behulp van de `az storage fs directory upload` opdracht.

In dit voor beeld wordt een bestand geüpload `upload.txt` met de naam naar een map met de naam `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Bestands eigenschappen weer geven

U kunt de eigenschappen van een bestand naar de-console afdrukken met behulp van de `az storage fs file show` opdracht.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Een bestand een andere naam geven of verplaatsen

Wijzig de naam of verplaats een bestand met behulp van de `az storage fs file move` opdracht.

In dit voor beeld wordt de naam van een bestand gewijzigd van de naam `my-file.txt` in de naam `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Een bestand verwijderen

Verwijder een bestand met behulp van de `az storage fs file delete` opdracht.

In dit voor beeld wordt een bestand met de naam`my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>Machtigingen beheren

U kunt toegangs machtigingen van mappen en bestanden ophalen, instellen en bijwerken.

> [!NOTE]
> Als u Azure Active Directory (Azure AD) gebruikt om opdrachten te autoriseren, moet u ervoor zorgen dat aan uw beveiligingsprincipal de [rol van BLOB-gegevens eigenaar voor opslag](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)is toegewezen. Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van [toegangs beheer in azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Een ACL ophalen

De ACL van een **Directory** ophalen met behulp van de `az storage fs access show` opdracht.

In dit voor beeld wordt de ACL van een Directory opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

De toegangs machtigingen van een **bestand** ophalen met behulp van de `az storage fs access show` opdracht. 

In dit voor beeld wordt de ACL van een bestand opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

In de volgende afbeelding ziet u de uitvoer na het ophalen van de ACL van een directory.

![ACL-uitvoer ophalen](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In dit voor beeld heeft de gebruiker die eigenaar is, de machtigingen lezen, schrijven en uitvoeren. De groep die eigenaar is, heeft alleen lees-en uitvoer machtigingen. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangs beheer lijsten.

### <a name="set-an-acl"></a>Een ACL instellen

Gebruik de `az storage fs access set` opdracht om de ACL van een **Directory**in te stellen. 

In dit voor beeld wordt de ACL ingesteld op een map voor de gebruiker die eigenaar is van de groep of andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In dit voor beeld wordt de *standaard* -ACL ingesteld op een map voor de gebruiker die eigenaar is of de groep die eigenaar is, en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Gebruik de `az storage fs access set` opdracht om de ACL van een **bestand**in te stellen. 

In dit voor beeld wordt de toegangs beheer lijst voor een bestand ingesteld op de gebruiker die eigenaar is van de groep of van andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

In de volgende afbeelding ziet u de uitvoer na het instellen van de ACL van een bestand.

![ACL-uitvoer ophalen](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In dit voor beeld hebben de gebruiker die eigenaar is en de groep die eigenaar is alleen lees-en schrijf machtigingen. Alle andere gebruikers hebben machtigingen voor schrijven en uitvoeren. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangs beheer lijsten.

### <a name="update-an-acl"></a>Een ACL bijwerken

Een andere manier om deze machtiging in te stellen, is met behulp van de `az storage fs access set` opdracht. 

De ACL van een map of bestand bijwerken door de `-permissions` para meter in te stellen op de korte vorm van een ACL.

In dit voor beeld wordt de ACL van een **Directory**bijgewerkt.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In dit voor beeld wordt de ACL van een **bestand**bijgewerkt.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

U kunt ook de gebruiker en groep van een map of bestand eigenaar bijwerken door de `--owner` `group` para meters of in te stellen op de entiteit-id of UPN (User Principal Name) van een gebruiker. 

In dit voor beeld wordt de eigenaar van een map gewijzigd. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In dit voor beeld wordt de eigenaar van een bestand gewijzigd. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>Zie ook

* [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Feedback geven](https://github.com/Azure/azure-cli-extensions/issues)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


