---
title: Gegevens transformeren in de cloud met PowerShell
description: Dit PowerShell-script transformeert gegevens in de cloud door spark-programma uit te voeren op een Azure HDInsight Spark-cluster.
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c09d0532b845472d0ccaac1ad57e3772630bb5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74932053"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell-script - gegevens transformeren in de cloud met Azure Data Factory

Met dit voorbeeld maakt PowerShell-script een pijplijn die gegevens in de cloud transformeert door spark-programma uit te voeren op een Azure HDInsight Spark-cluster. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Azure Storage-account**. Maak een python-script en een invoerbestand en upload ze naar de Azure-opslag. De uitvoer van het Spark-programma wordt opgeslagen in dit opslagaccount. Het Spark-cluster op aanvraag gebruikt hetzelfde opslagaccount als de primaire opslag.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Python-script uploaden naar het Blob-opslagaccount
1. Maak een Python-bestand met de naam **WordCount_Spark.py** met de volgende inhoud: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Vervang ** &lt;storageAccountName&gt; ** door de naam van uw Azure Storage-account. Sla het bestand vervolgens op. 
3. Maak in de Azure Blob-opslag een container met de naam **adftutorial** als deze nog niet bestaat. 
4. Maak een map met de naam **spark**.
5. Maak in de map **spark** een submap met de naam **script**. 
6. Upload het bestand **WordCount_Spark.py** naar de submap **script**. 


### <a name="upload-the-input-file"></a>Invoerbestand uploaden
1. Maak een bestand met de naam **minecraftstory.txt** met wat tekst. In het Spark-programma wordt het aantal woorden in deze tekst geteld. 
2. Maak een submap met de naam `inputfiles` in de `spark` map van de blobcontainer. 
3. Upload de `minecraftstory.txt` naar de submap`inputfiles`. 

## <a name="sample-script"></a>Voorbeeldscript
> [!IMPORTANT]
> Met dit script worden JSON-bestanden gemaakt die entiteiten in Gegevensfabriek (gekoppelde service, gegevensset en pijplijn) definiëren op uw harde schijf in de c:\ Map.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Nadat u het voorbeeldscript hebt uitgevoerd, u de volgende opdracht gebruiken om de resourcegroep en alle bijbehorende resources te verwijderen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Voer de volgende opdracht uit om het gegevensfabriek uit de resourcegroep te verwijderen: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Een gegevensfactory maken. |
| [Set-azdatafactoryv2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Hiermee maakt u een gekoppelde service in de gegevensfabriek. Een gekoppelde service koppelt een gegevensarchief of gegevensbestand aan een gegevensfabriek. |
| [Set-azdatafactoryV2-pijplijn](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Hiermee maakt u een pijplijn in de gegevensfabriek. Een pijplijn bevat een of meer activiteiten die een bepaalde bewerking uitvoeren. In deze pijplijn transformeert een vonkactiviteit gegevens door een programma uit te voeren op een Azure HDInsight Spark-cluster. |
| [Invoke-azdatafactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Hiermee maakt u een run voor de pijplijn. Met andere woorden, loopt de pijplijn. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Hier vindt u meer informatie over de uitvoering van de activiteit (activiteitsuitvoering) in de pijplijn. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Factory PowerShell-scriptvoorbeelden zijn te vinden in de [PowerShell-voorbeelden](../samples-powershell.md)van Azure Data Factory.
