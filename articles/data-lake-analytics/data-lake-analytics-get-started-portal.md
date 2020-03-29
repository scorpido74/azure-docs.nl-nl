---
title: "& Azure Data Lake Analytics voor query's maken - Azure-portal"
description: Gebruik het Azure-portal om Azure Data Lake Analytics-accounts te maken en vervolgens U-SQL-taken te verzenden.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 46da3750e4d0ac78c5fd9df91ae37670e541302d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315761"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Aan de slag met Azure Data Lake Analytics met het Azure-portal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

In dit artikel vind u informatie over het gebruik van Azure Portal voor het maken van Azure Data Lake Analytics-accounts, het definiëren van taken in [U-SQL](data-lake-analytics-u-sql-get-started.md) en het verzenden van taken naar de Data Lake Analytics-service.

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u een **Azure-abonnement** hebben. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Een Data Lake Analytics-account maken

U maakt nu tegelijkertijd een Data Lake Analytics en een Azure Data Lake Storage Gen1-account.  Deze stap is eenvoudig en duurt maar ongeveer 60 seconden om te voltooien.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op Een >  resource**Data + Analytics** > **Data Lake Analytics** **maken.**
3. Selecteer waarden voor de volgende items:
   * **Naam**: geef uw Data Lake Analytics-account een naam (alleen kleine letters en cijfers zijn toegestaan).
   * **Abonnement**: kies het Azure-abonnement dat u gebruikt voor het Analytics-account.
   * **Resourcegroep**. Selecteer een bestaande Azure-resourcegroep of maak een nieuwe.
   * **Locatie**. Selecteer een Azure-datacenter voor het Data Lake Analytics-account.
   * **Data Lake Storage Gen1**: Volg de instructie om een nieuw Data Lake Storage Gen1-account aan te maken of selecteer een bestaand account. 
4. U kunt ervoor kiezen om een prijscategorie te selecteren voor uw Data Lake Analytics-account.
5. Klik **op Maken**. 


## <a name="your-first-u-sql-script"></a>Uw eerste U-SQL-script

De volgende tekst is een zeer eenvoudig U-SQL-script. Het enige wat het doet is een kleine gegevensset in het script definiëren en vervolgens `/data.csv`die gegevensset naar het standaard Data Lake Storage Gen1-account schrijven als een bestand genaamd .

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>Een U-SQL-taak verzenden

1. Selecteer **New Job** vanuit het Data Lake Analytics-account.
2. Plak de tekst van het bovenstaande U-SQL-script. Geef een naam op voor de taak. 
3. Selecteer **Submit** om de taak te starten.   
4. Controleer de **status** van de taak en wacht totdat de taak de status **Succeeded** heeft.
5. Selecteer het tabblad **Gegevens** en selecteer vervolgens het tabblad `data.csv` **Uitvoer.** Selecteer het uitvoerbestand met de naam en bekijk de uitvoergegevens.

## <a name="see-also"></a>Zie ook

* Zie [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) om aan de slag te gaan met het ontwikkelen van U-SQL-toepassingen.
* Zie [Aan de slag met de Azure Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md) om U-SQL te leren.
* Zie [Azure Data Lake Analytics beheren met Azure-portal](data-lake-analytics-manage-use-portal.md)voor beheertaken.
