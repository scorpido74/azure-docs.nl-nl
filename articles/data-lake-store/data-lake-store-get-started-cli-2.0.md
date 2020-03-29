---
title: Gebruik de Azure CLI om aan de slag te gaan met Azure Data Lake Storage Gen1 | Microsoft Documenten
description: Gebruik de Azure CLI om een Data Lake Storage Gen1-account te maken en basisbewerkingen uit te voeren
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 9431cc7fa12b86371ce6b2325aca8e13d264442e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60885341"
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli"></a>Aan de slag met Azure Data Lake Store met Azure CLI

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Powershell](data-lake-store-get-started-powershell.md)
> * [Azure-CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

Lees hoe u de Azure CLI gebruiken om een Azure Data Lake Storage Gen1-account te maken en basisbewerkingen uit te voeren, zoals mappen maken, gegevensbestanden uploaden en downloaden, uw account verwijderen, enz. Zie Overzicht van Data Lake Storage Gen1 voor meer informatie over Data Lake Storage [Gen1.](data-lake-store-overview.md)

Azure CLI is de nieuwe opdrachtregel van Azure voor het beheren van Azure-resources. Deze kan worden gebruikt in Mac OS, Linux en Windows. Zie [Overzicht van Azure CLI](https://docs.microsoft.com/cli/azure)voor meer informatie. U ook de [Azure Data Lake Storage Gen1 CLI-verwijzing](https://docs.microsoft.com/cli/azure/dls) bekijken voor een volledige lijst met opdrachten en syntaxis.


## <a name="prerequisites"></a>Vereisten
Voordat u dit artikel gaat lezen, moet u beschikken over het volgende:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI** - Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) voor instructies.

## <a name="authentication"></a>Authentication

In dit artikel wordt gebruik gemaakt van een eenvoudigere verificatiebenadering met Data Lake Storage Gen1, waarbij u zich aanmeldt als eindgebruiker. Het toegangsniveau tot het Data Lake Storage Gen1-account en bestandssysteem wordt vervolgens bepaald door het toegangsniveau van de ingelogde gebruiker. Er zijn echter ook andere benaderingen om te verifiëren met Data Lake Storage Gen1, die **verificatie van eindgebruikers** of **service-to-service-verificatie**zijn. Zie [Verificatie door eindgebruikers](data-lake-store-end-user-authenticate-using-active-directory.md) of [Service-to-serviceverificatie](data-lake-store-authenticate-using-active-directory.md) voor instructies en meer informatie over verificatie.


## <a name="log-in-to-your-azure-subscription"></a>Aanmelden bij uw Azure-abonnement

1. Meld u aan bij uw Azure-abonnement.

    ```azurecli
    az login
    ```

    U ontvangt een code die u in de volgende stap nodig hebt. Open de pagina https://aka.ms/devicelogin met een webbrowser en voer de code in om te verifiëren. U wordt gevraagd om u aan te melden met uw referenties.

2. Wanneer u bent aangemeld, wordt er een venster weergegeven met alle Azure-abonnementen die aan uw account zijn gekoppeld. Gebruik de volgende opdracht als u een specifiek abonnement wilt gebruiken.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Een Azure Data Lake Storage Gen1-account maken

1. Maak een nieuwe resourcegroep. Geef in de volgende opdracht de parameterwaarden op die u wilt gebruiken. Als de locatienaam spaties bevat, moet deze tussen dubbele aanhalingstekens worden geplaatst. Bijvoorbeeld 'US - oost 2'. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Maak het Data Lake Storage Gen1-account.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Mappen maken in een Data Lake Storage Gen1-account

U mappen maken onder uw Azure Data Lake Storage Gen1-account om gegevens te beheren en op te slaan. Gebruik de volgende opdracht om een map te maken met de naam **mynewfolder** aan de basis van het Data Lake Storage Gen1-account.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> Met de parameter `--folder` geeft u aan dat u een map wilt maken. Als deze parameter niet aanwezig is, maakt de opdracht een leeg bestand genaamd mynewfolder aan de basis van het Data Lake Storage Gen1-account.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Gegevens uploaden naar een Data Lake Storage Gen1-account

U gegevens rechtstreeks op basisniveau of naar een map die u in het account hebt gemaakt, uploaden naar Data Lake Storage Gen1. De codefragmenten hieronder laten zien hoe u voorbeeldgegevens uploadt naar de map (**mynewfolder**) die u in de voorgaande sectie hebt gemaakt.

Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken. Download het bestand en sla het in een lokale map op uw computer op, bijvoorbeeld C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Als bestemming moet u het volledige pad inclusief de bestandsnaam opgeven.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Bestanden weergeven in een Data Lake Storage Gen1-account

Gebruik de volgende opdracht om de bestanden in een Data Lake Storage Gen1-account weer te geven.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

De uitvoer ziet er ongeveer als volgt uit:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>De naam van gegevens uit een Data Lake Storage Gen1-account wijzigen, downloaden en verwijderen 

* **Als u de naam van een bestand wilt wijzigen,** gebruikt u de volgende opdracht:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Als u een bestand wilt downloaden,** gebruikt u de volgende opdracht. Zorg ervoor dat het doelpad dat u opgeeft al bestaat.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Met de opdracht wordt de doelmap gemaakt als deze niet bestaat.
    > 
    >

* **Als u een bestand wilt verwijderen,** gebruikt u de volgende opdracht:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Als u de map **mynewfolder** en het bestand **vehicle1_09142014_copy.csv** met één opdracht wilt verwijderen, gebruikt u de parameter --recurse

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Werken met machtigingen en ACL's voor een Data Lake Storage Gen1-account

In deze sectie vindt u meer informatie over het beheren van ACL's en machtigingen met behulp van azure cli. Zie [Toegangsbeheer in Azure Data Lake Storage Gen1](data-lake-store-access-control.md)voor een gedetailleerde discussie over de manier waarop ACL's worden geïmplementeerd in Azure Data Lake Storage Gen1.

* Gebruik de volgende opdracht als u **de eigenaar van een bestand/map wilt bijwerken**:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* Gebruik de volgende opdracht als u **de machtigingen voor een bestand/map wilt bijwerken**:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* Gebruik de volgende opdracht als u **de ACL's voor een gegeven pad wilt ophalen**:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    De uitvoer moet er ongeveer als volgt uitzien:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* Gebruik de volgende opdracht als u **een item voor een ACL wilt instellen**:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* Gebruik de volgende opdracht als u **een item voor een ACL wilt verwijderen**:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* Gebruik de volgende opdracht als u **een complete standaard-ACL wilt verwijderen**:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* Gebruik de volgende opdracht als u **een complete niet-standaard-ACL wilt verwijderen**:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account verwijderen
Gebruik de volgende opdracht om een Data Lake Storage Gen1-account te verwijderen.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Wanneer dit wordt gevraagd, typt u **Y** om het account te verwijderen.

## <a name="next-steps"></a>Volgende stappen
* [Azure Data Lake Storage Gen1 gebruiken voor vereisten voor big data](data-lake-store-data-scenarios.md) 
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
