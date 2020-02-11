---
title: De klassieke Azure CLI gebruiken met Azure Storage | Microsoft Docs
description: Informatie over het gebruik van de klassieke Azure-opdracht regel interface (CLI) met Azure Storage om opslag accounts te maken en te beheren en te werken met Azure-blobs en-bestanden.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 6554385a879b054153dcb808c3dff4b60c136458
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120853"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>De klassieke Azure CLI gebruiken met Azure Storage

## <a name="overview"></a>Overzicht

De klassieke Azure-CLI biedt een aantal open-source-en platformoverschrijdende opdrachten voor het werken met het Azure-platform. Het biedt veel van de functionaliteit die wordt aangetroffen in de [Azure Portal](https://portal.azure.com) , evenals uitgebreide functionaliteit voor gegevens toegang.

In deze hand leiding wordt uitgelegd hoe u de klassieke versie van [Azure](../../cli-install-nodejs.md) kunt gebruiken om een aantal ontwikkel-en beheer taken uit te voeren met Azure Storage. U wordt aangeraden om de meest recente klassieke CLI te downloaden en te installeren of te upgraden voordat u deze hand leiding gebruikt.

In deze hand leiding wordt ervan uitgegaan dat u bekend bent met de basis concepten van Azure Storage. De hand leiding bevat een aantal scripts om het gebruik van de klassieke CLI met Azure Storage te demonstreren. Zorg ervoor dat u de script variabelen op basis van uw configuratie bijwerkt voordat u elk script uitvoert.

> [!NOTE]
> De hand leiding bevat de klassieke Azure CLI-opdracht en script voorbeelden voor klassieke opslag accounts. Zie [de Azure CLI voor Mac, Linux en Windows gebruiken met Azure resource management](../../virtual-machines/azure-cli-arm-commands.md#storage-objects) voor klassieke Azure cli-opdrachten voor Resource Manager-opslag accounts.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Ga binnen vijf minuten aan de slag met Azure Storage en de klassieke Azure-CLI
Deze hand leiding maakt gebruik van Ubuntu voor voor beelden, maar andere besturingssysteem platforms moeten op dezelfde manier worden uitgevoerd.

**Nieuw in Azure:** Een Microsoft Azure-abonnement en een Microsoft-account die aan het abonnement zijn gekoppeld, ophalen. Voor informatie over opties voor Azure Purchase, Zie [gratis proef versie](https://azure.microsoft.com/pricing/free-trial/), [aankoop opties](https://azure.microsoft.com/pricing/purchase-options/)en [leden aanbiedingen](https://azure.microsoft.com/pricing/member-offers/) (voor leden van MSDN, Microsoft Partner Network en BizSpark en andere micro soft-Program ma's).

Zie [beheerders rollen toewijzen in azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) voor meer informatie over Azure-abonnementen.

**Nadat u een Microsoft Azure-abonnement en-account hebt gemaakt:**

1. Down load en installeer de klassieke Azure-CLI volgens de instructies in [de klassieke Azure-cli installeren](../../cli-install-nodejs.md).
2. Zodra de klassieke CLI is geïnstalleerd, kunt u de Azure-opdracht van de opdracht regel interface (bash, Terminal, opdracht prompt) gebruiken om toegang te krijgen tot de klassieke CLI-opdrachten. Typ de _Azure_ -opdracht en de volgende uitvoer wordt weer geven.

    ![Azure-opdracht uitvoer](./media/storage-azure-cli/azure_command.png)   
3. In de opdracht regel interface typt u `azure storage` om alle Azure Storage-opdrachten weer te geven en een eerste indruk te krijgen van de functionaliteiten die de klassieke CLI biedt. U kunt de opdracht naam met de para meter **-h** typen (bijvoorbeeld `azure storage share create -h`) om de details van de opdracht syntaxis weer te geven.
4. Nu krijgt u een eenvoudig script dat eenvoudige klassieke CLI-opdrachten weergeeft om toegang te krijgen tot Azure Storage. Met het script wordt u eerst gevraagd om twee variabelen in te stellen voor uw opslag account en de sleutel. Vervolgens wordt door het script een nieuwe container gemaakt in dit nieuwe opslag account en wordt een bestaand installatie kopie bestand (BLOB) naar die container geüpload. Nadat het script alle blobs in die container heeft vermeld, wordt het afbeeldings bestand gedownload naar de doelmap die zich op de lokale computer bevindt.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. Open de gewenste tekst editor (vim bijvoorbeeld) op de lokale computer. Typ het bovenstaande script in de tekst editor.
6. Nu moet u de script variabelen bijwerken op basis van uw configuratie-instellingen.

   * **< storage_account_name >** Gebruik de opgegeven naam in het script of voer een nieuwe naam in voor uw opslag account. **Belang rijk:** De naam van het opslag account moet uniek zijn in Azure. Het moet ook kleine letters zijn.
   * **< storage_account_key >** De toegangs sleutel van uw opslag account.
   * **< container_name >** Gebruik de opgegeven naam in het script of voer een nieuwe naam in voor de container.
   * **< image_to_upload >** Geef een pad op naar een afbeelding op de lokale computer, zoals: ' ~/images/HelloWorld.png '.
   * **< destination_folder >** Voer een pad naar een lokale map in voor het opslaan van bestanden die zijn gedownload van Azure Storage, zoals: "~/downloadImages".
7. Nadat u de benodigde variabelen in vim hebt bijgewerkt, drukt u op toetsen combinaties `ESC`, `:``wq!` om het script op te slaan.
8. Als u dit script wilt uitvoeren, typt u de naam van het script bestand in de bash-console. Nadat dit script is uitgevoerd, moet u een lokale doelmap hebben die het gedownloade afbeeldings bestand bevat. In de volgende scherm afbeelding ziet u een voor beeld van een uitvoer:

Nadat het script is uitgevoerd, moet u een lokale doelmap hebben die het gedownloade afbeeldings bestand bevat.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Opslag accounts beheren met de klassieke Azure-CLI
### <a name="connect-to-your-azure-subscription"></a>Verbinding maken met uw Azure-abonnement
Hoewel de meeste opslag opdrachten werken zonder een Azure-abonnement, wordt u aangeraden verbinding te maken met uw abonnement vanuit de klassieke CLI.

### <a name="create-a-new-storage-account"></a>Een nieuw opslagaccount maken
Als u Azure Storage wilt gebruiken, hebt u een opslag account nodig. U kunt een nieuw Azure-opslag account maken nadat u uw computer hebt geconfigureerd om verbinding te maken met uw abonnement.

```azurecli
azure storage account create <account_name>
```

De naam van uw opslag account moet tussen 3 en 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Een standaard Azure-opslag account instellen in omgevings variabelen
U kunt meerdere opslag accounts in uw abonnement hebben. U kunt er een selecteren en instellen in de omgevings variabelen voor alle opslag opdrachten in dezelfde sessie. Hierdoor kunt u de klassieke CLI-opslag opdrachten uitvoeren zonder het opslag account en de sleutel expliciet op te geven.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Een andere manier om een standaard-opslag account in te stellen, is het gebruik van connection string. Haal eerst de connection string op via de opdracht:

```azurecli
azure storage account connectionstring show <account_name>
```

Kopieer vervolgens de uitvoer connection string en stel deze in op omgevings variabele:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Blobs maken en beheren
Azure Blob-opslag is een service voor het opslaan van grote hoeveel heden ongestructureerde gegevens, zoals tekst of binaire gegevens, die overal ter wereld toegankelijk zijn via HTTP of HTTPS. In deze sectie wordt ervan uitgegaan dat u al bekend bent met de concepten van Azure Blob-opslag. Zie voor meer informatie aan de [slag met Azure Blob Storage met behulp](../blobs/storage-dotnet-how-to-use-blobs.md) van de concepten van .net en [BLOB-service](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Een container maken
Elke Blob in azure Storage moet zich in een container bevinden. U kunt een persoonlijke container maken met behulp van de `azure storage container create` opdracht:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Er zijn drie niveaus anonieme lees toegang: **uit**, **BLOB**en **container**. Om anonieme toegang tot blobs te voor komen, stelt u de para meter permission in op **off**. De nieuwe container is standaard privé en kan alleen worden geopend door de eigenaar van het account. Als u anonieme open bare Lees toegang wilt toestaan voor BLOB-resources, maar niet voor meta gegevens van containers of naar de lijst met blobs in de container, stelt u de machtigings parameter in op **BLOB**. Als u volledige open bare Lees toegang wilt toestaan voor BLOB-resources, meta gegevens van containers en de lijst met blobs in de container, stelt u de machtigings parameter in op **container**. Zie [Anonieme leestoegang tot containers en blobs beheren](../blobs/storage-manage-access-to-resources.md) voor meer informatie.
>
>

### <a name="upload-a-blob-into-a-container"></a>Een blob uploaden naar een container
Azure Blob Storage ondersteunt blok-blobs en pagina-blobs. Zie voor meer informatie [over blok-blobs, toevoeg-blobs en pagina-blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx).

Als u blobs wilt uploaden naar een container, kunt u de `azure storage blob upload`gebruiken. Met deze opdracht worden standaard de lokale bestanden geüpload naar een blok-blob. Als u het type voor de BLOB wilt opgeven, kunt u de para meter `--blobtype` gebruiken.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Blobs downloaden uit een container
In het volgende voor beeld ziet u hoe u blobs kunt downloaden uit een container.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Blobs kopiëren
U kunt blobs binnen of tussen opslagaccounts en regio's asynchroon kopiëren.

In het volgende voorbeeld ziet u hoe u blobs van één opslagaccount naar een ander kopieert. In dit voor beeld maken we een container waarin blobs openbaar zijn en anoniem toegankelijk zijn.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

In dit voor beeld wordt een asynchrone kopie uitgevoerd. U kunt de status van elke Kopieer bewerking controleren door de bewerking `azure storage blob copy show` uit te voeren.

Houd er rekening mee dat de bron-URL die is geleverd voor de Kopieer bewerking openbaar toegankelijk moet zijn of een SAS (Shared Access Signature)-token moeten bevatten.

### <a name="delete-a-blob"></a>Een blob verwijderen
Als u een BLOB wilt verwijderen, gebruikt u de onderstaande opdracht:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Bestands shares maken en beheren
Azure Files biedt gedeelde opslag voor toepassingen die gebruikmaken van het standaard SMB-protocol. Microsoft Azure virtuele machines en Cloud Services, en on-premises toepassingen, kunnen bestands gegevens delen via gekoppelde shares. U kunt bestands shares en bestands gegevens beheren via de klassieke CLI. Zie [Inleiding tot Azure files](../files/storage-files-introduction.md)voor meer informatie over Azure files.

### <a name="create-a-file-share"></a>Een bestandsshare maken
Een Azure-bestands share is een SMB-bestands share in Azure. Alle mappen en bestanden moeten worden gemaakt in een bestands share. Een account kan een onbeperkt aantal shares bevatten en een share kan een onbeperkt aantal bestanden opslaan, tot aan de capaciteits limieten van het opslag account. In het volgende voor beeld wordt een bestands share met de naam **MyShare**gemaakt.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Een map maken
Een Directory biedt een optionele hiërarchische structuur voor een Azure-bestands share. In het volgende voor beeld wordt een map gemaakt met de naam **myDir** in de bestands share.

```azurecli
azure storage directory create myshare myDir
```

Houd er rekening mee dat mappad meerdere niveaus kunnenbevatten, zoals **a/b**. U moet er echter voor zorgen dat alle bovenliggende directory's bestaan. Voor pad **a/b**moet u bijvoorbeeld eerst Directory **a** maken en vervolgens map **b**maken.

### <a name="upload-a-local-file-to-directory"></a>Een lokaal bestand uploaden naar de map
In het volgende voor beeld wordt een bestand geüpload van **~/temp/samplefile.txt** naar de **myDir** -map. Bewerk het bestandspad zodat dit verwijst naar een geldig bestand op uw lokale computer:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Houd er rekening mee dat een bestand in de share Maxi maal 1 TB groot kan zijn.

### <a name="list-the-files-in-the-share-root-or-directory"></a>De bestanden in de hoofdmap van de share of de map weer geven
U kunt de bestanden en submappen in een share-hoofdmap of een map weer geven met de volgende opdracht:

```azurecli
azure storage file list myshare myDir
```

Houd er rekening mee dat de mapnaam optioneel is voor de bewerking vermelding. Als u dit weglaat, wordt de inhoud van de hoofdmap van de share weer gegeven.

### <a name="copy-files"></a>Bestanden kopiëren
Vanaf versie 0.9.8 van de klassieke CLI kunt u een bestand kopiëren naar een ander bestand, een bestand naar een BLOB of een BLOB naar een bestand. Hieronder ziet u hoe u deze Kopieer bewerkingen kunt uitvoeren met CLI-opdrachten. Een bestand kopiëren naar de nieuwe map:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Een BLOB naar een bestands directory kopiëren:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Volgende stappen

U vindt hier informatie over de klassieke CLI-opdracht van Azure voor het werken met opslag resources:

* [Klassieke Azure CLI-opdrachten in de Resource Manager-modus](../../virtual-machines/azure-cli-arm-commands.md#storage-objects)
* [Klassieke Azure CLI-opdrachten in azure Service Management-modus](../../cli-install-nodejs.md)

Mogelijk wilt u ook de nieuwste versie van de [Azure cli](../storage-azure-cli.md)proberen, voor gebruik met het Resource Manager-implementatie model.
