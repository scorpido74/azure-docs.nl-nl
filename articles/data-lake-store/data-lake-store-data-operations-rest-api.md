---
title: 'REST API: Bestandssysteembewerkingen op Azure Data Lake Storage Gen1 | Microsoft Documenten'
description: WebHDFS REST API's gebruiken om bestandssysteembewerkingen uit te voeren op Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 351c92f1e1a698893f61004d523ba79ebca253e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878780"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Bestandssysteembewerkingen op Azure Data Lake Storage Gen1 met REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

In dit artikel vindt u informatie over het gebruik van WebHDFS REST API's en Gegevensmeeropslag Gen1 REST API's om bestandssysteembewerkingen uit te voeren op Azure Data Lake Storage Gen1. Zie Accountbeheerbewerkingen op Data Lake Storage Gen1 met REST API voor instructies over het uitvoeren van accountbeheerbewerkingen op Data Lake [Storage Gen1.](data-lake-store-get-started-rest-api.md)

## <a name="prerequisites"></a>Vereisten
* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1-account**. Volg de instructies bij [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure-portal.](data-lake-store-get-started-portal.md)

* **[cURL](https://curl.haxx.se/)**. In dit artikel wordt cURL gebruikt om aan te tonen hoe u REST API-aanroepen voeren tegen een Data Lake Storage Gen1-account.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hoe verifieer ik met Azure Active Directory?
Er zijn twee benaderingen voor verificatie met Azure Active Directory.

* Zie Verificatie van eindgebruikers met Data [Lake Storage Gen1 met .NET SDK](data-lake-store-end-user-authenticate-rest-api.md)voor verificatie van eindgebruikers voor uw toepassing (interactief).
* Zie [Service-to-service-verificatie met Data Lake Storage Gen1 met .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md)voor service-to-service-verificatie voor uw toepassing (niet-interactief).


## <a name="create-folders"></a>Mappen maken
Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht: Vervang ** \<uw winkelnaam>** door de naam van uw Data Lake Storage Gen1-account.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

Vervang \<`REDACTED`\> in de voorgaande opdracht door het verificatietoken dat u eerder hebt opgehaald. Met deze opdracht wordt een map gemaakt met de naam **mytempdir** onder de hoofdmap van uw Data Lake Storage Gen1-account.

Als de bewerking is geslaagd, wordt er een antwoord weergegeven zoals in het volgende codefragment:

    {"boolean":true}

## <a name="list-folders"></a>Lijst met mappen
Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht: Vervang ** \<uw winkelnaam>** door de naam van uw Data Lake Storage Gen1-account.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

Vervang \<`REDACTED`\> in de voorgaande opdracht door het verificatietoken dat u eerder hebt opgehaald.

Als de bewerking is geslaagd, wordt er een antwoord weergegeven zoals in het volgende codefragment:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Gegevens uploaden
Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht: Vervang ** \<uw winkelnaam>** door de naam van uw Data Lake Storage Gen1-account.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

In de voorgaande syntaxis geeft de parameter **-T** de locatie aan van het bestand dat u uploadt.

De uitvoer lijkt op die in het volgende codefragment:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Gegevens lezen
Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File) wordt gedefinieerd.

Het lezen van gegevens van een Data Lake Storage Gen1-account is een proces in twee stappen.

* Eerst dient u een GET-aanvraag in voor het eindpunt `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Met deze oproep wordt de locatie geretourneerd waarnaar u de volgende GET-aanvraag moet verzenden.
* Vervolgens dient u de GET-aanvraag in voor het eindpunt `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Met deze oproept wordt de inhoud van het bestand weergegeven.

Omdat de invoerparameters in de eerste en tweede stap echter gelijk zijn, kunt u de parameter `-L` gebruiken om de eerste aanvraag in te dienen. Optie `-L` combineert in feite twee aanvragen in één en zorgt dat cURL de aanvraag opnieuw uitvoert op de nieuwe locatie. Ten slotte wordt de uitvoer van alle aanroepen van de aanvraag weergegeven, zoals u in het volgende codefragment kunt zien. Vervang ** \<uw winkelnaam>** door de naam van uw Data Lake Storage Gen1-account.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Als het goed is, wordt ongeveer het volgende codefragment weergegeven:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>De naam van een bestand wijzigen
Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory) wordt gedefinieerd.

Als u de naam van een bestand wil wijzigen, gebruikt u de volgende cURL-opdracht. Vervang ** \<uw winkelnaam>** door de naam van uw Data Lake Storage Gen1-account.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Als het goed is, wordt ongeveer het volgende codefragment weergegeven:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Een bestand verwijderen
Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht als u een bestand wilt verwijderen. Vervang ** \<uw winkelnaam>** door de naam van uw Data Lake Storage Gen1-account.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Volgende stappen
* [Accountbeheerbewerkingen op Data Lake Storage Gen1 met REST API](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Zie ook
* [Naslaginformatie over Azure Data Lake Storage Gen1 REST API](https://docs.microsoft.com/rest/api/datalakestore/)
* [Open Source Big Data-toepassingen die compatibel zijn met Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

