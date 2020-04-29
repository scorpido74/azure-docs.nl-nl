---
title: Geavanceerde R-functies schrijven
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Meer informatie over het schrijven van een R-functie voor geavanceerde statistische berekeningen in Azure SQL Database met behulp van Machine Learning Services (preview).
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
ROBOTS: NOINDEX
ms.openlocfilehash: ba78267b1c6dc8f0e1bd25bb8ecdb1d8d344d03e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453111"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)

In dit artikel wordt beschreven hoe u R wiskundige en hulpprogramma functies insluit in een door SQL opgeslagen procedure. Geavanceerde statistische functies die gecompliceerd zijn om te worden geïmplementeerd in T-SQL, kunnen worden uitgevoerd in R met slechts één regel code.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/) voordat u begint.

- Als u de voorbeeld code in deze oefeningen wilt uitvoeren, moet u eerst [Azure SQL database hebben met Machine Learning Services (met R)](sql-database-machine-learning-services-overview.md) ingeschakeld.

- Zorg ervoor dat u de nieuwste versie van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) hebt geïnstalleerd. U kunt R-scripts uitvoeren met andere database beheer-of query hulpprogramma's, maar in deze Snelstartgids gebruikt u SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Een opgeslagen procedure maken voor het genereren van wille keurige getallen

Voor eenvoud gebruiken we het R `stats` -pakket dat standaard is geïnstalleerd en geladen met Azure SQL database met behulp van Machine Learning Services (preview). Het pakket bevat honderden functies voor algemene statistische taken, waaronder de `rnorm` functie. Deze functie genereert een opgegeven aantal wille keurige getallen met behulp van de normale distributie, op basis van een standaard afwijking en een gemiddelde.

De volgende R-code retourneert bijvoorbeeld 100 getallen met een gemiddelde van 50, op basis van een standaard afwijking van 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Als u deze regel van R vanuit T-SQL wilt aanroepen, voert `sp_execute_external_script` u de r-functie uit in de para meter r-script en voegt u deze toe als volgt:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Wat moet u doen als u eenvoudiger een andere set wille keurige getallen wilt genereren?

Dat is eenvoudig in combi natie met SQL. U definieert een opgeslagen procedure die de argumenten van de gebruiker ophaalt en geeft die argumenten vervolgens door in het R-script als variabelen.

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

- De eerste regel definieert elk van de para meters voor SQL-invoer die vereist zijn wanneer de opgeslagen procedure wordt uitgevoerd.

- De regel die begint `@params` met definieert alle variabelen die worden gebruikt door de R-code en de bijbehorende SQL-gegevens typen.

- De regels die onmiddellijk volgen, wijzen de namen van SQL-para meters toe aan de corresponderende R-variabele namen.

Nu u de R-functie in een opgeslagen procedure hebt ingepakt, kunt u de functie eenvoudig aanroepen en in verschillende waarden door geven, bijvoorbeeld:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>R-hulpprogramma functies gebruiken voor het oplossen van problemen

Het `utils` pakket dat standaard wordt geïnstalleerd, biedt diverse hulp functies voor het onderzoeken van de huidige R-omgeving. Deze functies kunnen nuttig zijn als u verschillen zoekt in de manier waarop uw R-code wordt uitgevoerd in SQL en buiten omgevingen. U kunt bijvoorbeeld de functie R `memory.limit()` gebruiken om geheugen voor de huidige R-omgeving op te halen.

Omdat het `utils` pakket is geïnstalleerd maar niet standaard is geladen, moet u de `library()` functie gebruiken om deze eerst te laden.

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
> Veel gebruikers gebruiken de systeem timing functies in R, zoals `system.time` en `proc.time`, voor het vastleggen van de tijd die wordt gebruikt door R-processen en het analyseren van prestatie problemen.
