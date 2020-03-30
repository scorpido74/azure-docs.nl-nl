---
title: OdBC-connector van Azure Data Explorer gebruiken om Tableau-gegevens te visualiseren
description: In dit artikel leert u hoe u een ODBC-verbinding (Open Database Connectivity) gebruiken voor azure data explorer-verbinding om gegevens met Tableau te visualiseren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562442"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Gegevens van Azure Data Explorer visualiseren in Tableau

 [Tableau](https://www.tableau.com/) is een visual analytics platform voor business intelligence. Als u verbinding wilt maken met Azure Data Explorer vanuit Tableau en gegevens uit een voorbeeldcluster wilt inbrengen, gebruikt u het ODBC-stuurprogramma (SQL Server Open Database Connectivity). 

## <a name="prerequisites"></a>Vereisten

Je hebt het volgende nodig om dit artikel te voltooien:

* [Maak verbinding met Azure Data Explorer met ODBC](connect-odbc.md) met het SQL Server ODBC-stuurprogramma om verbinding te maken met Azure Data Explorer vanuit Tableau. 

* Tableau Desktop, volledige of [proefversie.](https://www.tableau.com/products/desktop/download)

* Een cluster met voorbeeldgegevens van StormEvents. Zie [Een Azure Data Explorer-cluster en -database maken en](create-cluster-database-portal.md) [voorbeeldgegevens opnemen in Azure Data Explorer](ingest-sample-data.md)voor meer informatie.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Data visualiseren in Tableau 

Zodra u odbc hebt geconfigureerd, u voorbeeldgegevens naar Tableau brengen.

1. Selecteer in Tableau Desktop in het linkermenu de optie **Andere databases (ODBC)**.

    ![Verbinding maken met ODBC](media/tableau/connect-odbc.png)

1. Selecteer voor **DSN**de gegevensbron die u voor ODBC hebt gemaakt en selecteer **Vervolgens Aanmelden**.

    ![ODBC-aanmelding](media/tableau/odbc-sign-in.png)

1. Selecteer **voor Database**de database in uw voorbeeldcluster, zoals *TestDatabase*. Selecteer **voor Schema** *dbo*en selecteer voor **Tabel**de voorbeeldtabel *StormEvents.*

    ![Database en tabel selecteren](media/tableau/select-database-table.png)

1. Tableau toont nu het schema voor de voorbeeldgegevens. Selecteer **Nu bijwerken** om de gegevens naar Tableau te brengen.

    ![Gegevens bijwerken](media/tableau/update-data.png)

    Wanneer de gegevens worden geïmporteerd, toont Tableau rijen met gegevens die vergelijkbaar zijn met de volgende afbeelding.

    ![Resultaatset](media/tableau/result-set.png)

1. Nu u visualisaties maken in Tableau op basis van de gegevens die u hebt binnengehaald vanuit Azure Data Explorer. Zie [Tableau Learning](https://www.tableau.com/learn)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Query's schrijven voor Azure Data Explorer](write-queries.md)