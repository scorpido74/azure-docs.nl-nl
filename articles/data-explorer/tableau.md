---
title: Azure Data Explorer ODBC-connector gebruiken om tableau-gegevens te visualiseren
description: In dit artikel leert u hoe u een Open Database Connectivity (ODBC)-verbinding met Data Explorer Azure kunt gebruiken om gegevens te visualiseren met tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562442"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Gegevens visualiseren vanuit Azure Data Explorer in tableau

 [Tableau](https://www.tableau.com/) is een Visual Analytics-platform voor Business Intelligence. Als u verbinding wilt maken met Azure Data Explorer van tableau en gegevens uit een voor beeld van een cluster wilt halen, gebruikt u het stuur programma SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om dit artikel te volt ooien:

* [Maak verbinding met azure Data Explorer met ODBC](connect-odbc.md) met behulp van het SQL Server ODBC-stuur programma om verbinding te maken met Azure Data Explorer vanuit tableau. 

* Tableau Desktop, full of [proef](https://www.tableau.com/products/desktop/download) versie.

* Een cluster dat de StormEvents-voorbeeld gegevens bevat. Zie [een azure Data Explorer-cluster en-data base maken](create-cluster-database-portal.md) en [voorbeeld gegevens opnemen in azure Data Explorer](ingest-sample-data.md)voor meer informatie.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Gegevens visualiseren in tableau 

Zodra u klaar bent met het configureren van ODBC, kunt u voorbeeld gegevens in tableau plaatsen.

1. Selecteer in tableau bureau blad in het linkermenu **andere data bases (ODBC)** .

    ![Verbinding maken met ODBC](media/tableau/connect-odbc.png)

1. Selecteer bij **DSN**de gegevens bron die u hebt gemaakt voor ODBC en selecteer vervolgens **Aanmelden**.

    ![ODBC-aanmelding](media/tableau/odbc-sign-in.png)

1. Selecteer voor de **Data Base**de Data Base op uw voor beeld-cluster, zoals *TestDatabase*. Selecteer voor **schema** *dbo*en selecteer voor **tabel**de voorbeeld tabel *StormEvents* .

    ![Data Base en tabel selecteren](media/tableau/select-database-table.png)

1. Tableau toont nu het schema voor de voorbeeld gegevens. Selecteer **Nu bijwerken** om de gegevens naar tableau te brengen.

    ![Gegevens bijwerken](media/tableau/update-data.png)

    Wanneer de gegevens worden geïmporteerd, worden in tableau rijen met gegevens weer gegeven die lijken op de volgende afbeelding.

    ![Resultatenset](media/tableau/result-set.png)

1. Nu kunt u visualisaties in tableau maken op basis van de gegevens die u hebt verzameld van Azure Data Explorer. Zie [tableau Learning](https://www.tableau.com/learn)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Query's schrijven voor Azure Data Explorer](write-queries.md)