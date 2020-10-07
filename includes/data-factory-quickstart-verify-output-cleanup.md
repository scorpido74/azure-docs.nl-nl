---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: c0fcdf1cf69c55f63288138bc7377a78295cb2be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "86544282"
---
## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

De uitvoermap wordt automatisch door de pijplijn gemaakt in de blobcontainer adftutorial. Vervolgens wordt het bestand emp.txt gekopieerd van de invoermap naar de uitvoermap. 

1. Selecteer in de Azure-portal op de pagina met de **adftutorial**-container de optie **Vernieuwen** om de uitvoermap weer te geven. 
    
    ![Vernieuwen](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)

2. Selecteer **Uitvoer** in de lijst met mappen. 

3. Controleer of het bestand **emp.txt** naar de uitvoermap is gekopieerd. 

    ![Vernieuwen](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Resources opschonen

De resources die u hebt gemaakt in de Quick Start kunt u op twee manieren opschonen. U kunt de [Azure-resourcegroep](../articles/azure-resource-manager/management/overview.md) verwijderen, met alle resources uit de resourcegroep. Als u de andere resources intact wilt houden, verwijdert u alleen de data factory die u in deze zelfstudie hebt gemaakt.

Als u een resourcegroep verwijdert, worden alle resources die deze bevat, met inbegrip van de data factory's, ook verwijderd. Voer de volgende opdracht uit om de gehele resourcegroep te verwijderen: 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> Het verwijderen van een resourcegroep kan enige tijd duren. Even geduld met het proces

Als u alleen de data factory wilt verwijderen en niet de gehele resourcegroep, moet u de volgende opdracht uitvoeren: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```