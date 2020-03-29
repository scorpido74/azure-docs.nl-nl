---
title: Geavanceerde R-functies schrijven
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Meer informatie over het schrijven van een R-functie voor geavanceerde statistische berekeningen in Azure SQL Database met Machine Learning Services (voorbeeld).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60702449"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Geavanceerde R-functies schrijven in Azure SQL Database met Machine Learning Services (voorbeeld)

In dit artikel wordt beschreven hoe u Wiskundige en hulpprogrammafuncties van R insluit in een SQL-opgeslagen procedure. Geavanceerde statistische functies die ingewikkeld zijn om te implementeren in T-SQL kunnen worden gedaan in R met slechts een enkele regel code.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/) voordat u begint.

- Als u de voorbeeldcode in deze oefeningen wilt uitvoeren, moet u eerst een Azure SQL-database hebben waarbij Machine Learning Services (met R) is ingeschakeld. Na de onboarding voor de openbare preview wordt Machine Learning voor u ingeschakeld voor uw bestaande of nieuwe database. Volg de stappen in [Meld u aan voor de preview-versie](sql-database-machine-learning-services-overview.md#signup).

- Zorg ervoor dat u de nieuwste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) hebt geïnstalleerd. U R-scripts uitvoeren met andere databasebeheer- of querytools, maar in deze quickstart gebruikt u SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Een opgeslagen procedure maken om willekeurige getallen te genereren

Laten we voor de eenvoud `stats` het R-pakket gebruiken dat standaard is geïnstalleerd en geladen met Azure SQL Database met machine learning services (voorbeeld). Het pakket bevat honderden functies voor veelvoorkomende `rnorm` statistische taken, waaronder de functie. Deze functie genereert een bepaald aantal willekeurige getallen met behulp van de normale verdeling, gezien een standaarddeviatie en middelen.

De volgende R-code retourneert bijvoorbeeld 100 nummers op een gemiddelde van 50, met een standaarddeviatie van 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Als u deze lijn Van R `sp_execute_external_script` vanuit T-SQL wilt aanroepen, voert u de Functie R uit en voegt u de R-functie toe aan de parameter R-script, zoals deze:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Wat als u het gemakkelijker wilt maken om een andere set willekeurige getallen te genereren?

Dat is eenvoudig in combinatie met SQL. U definieert een opgeslagen procedure die de argumenten van de gebruiker krijgt en geeft deze argumenten vervolgens door in het R-script als variabelen.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- De eerste regel definieert elk van de SQL-invoerparameters die nodig zijn wanneer de opgeslagen procedure wordt uitgevoerd.

- De lijn `@params` die begint met definieert alle variabelen die door de R-code worden gebruikt en de bijbehorende SQL-gegevenstypen.

- De regels die onmiddellijk volgen, brengen de SQL-parameternamen in kaart met de bijbehorende R-variabelenamen.

Nu u de R-functie in een opgeslagen procedure hebt verpakt, u de functie eenvoudig aanroepen en verschillende waarden doorgeven, zoals deze:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Gebruik R-hulpprogrammafuncties voor het oplossen van problemen

Het `utils` pakket, standaard geïnstalleerd, biedt een verscheidenheid aan hulpprogrammafuncties voor het onderzoeken van de huidige R-omgeving. Deze functies kunnen handig zijn als u discrepanties vindt in de manier waarop uw R-code presteert in SQL en in externe omgevingen. U bijvoorbeeld de `memory.limit()` R-functie gebruiken om geheugen te krijgen voor de huidige R-omgeving.

Omdat `utils` het pakket is geïnstalleerd maar niet standaard `library()` is geladen, moet u de functie eerst gebruiken om het pakket te laden.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Veel gebruikers gebruiken de systeemtimingfuncties graag `system.time` in `proc.time`R, zoals en , om de tijd vast te leggen die door R-processen wordt gebruikt en prestatieproblemen te analyseren.
