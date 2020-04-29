---
title: SQL-scripts in azure Synapse Studio (preview-versie)
description: Kennis Making met Azure Synapse Studio (preview) SQL-scripts
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431070"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>SQL-script gebruiken in azure Synapse Studio (preview-versie)

Azure Synapse Studio (preview) biedt een SQL script web-interface waarmee u SQL-query's kunt schrijven. U kunt verbinding maken met SQL-groep (preview) of SQL op aanvraag (preview). 

## <a name="begin-authoring-in-sql-script"></a>Beginnen met ontwerpen in SQL-script 

Er zijn verschillende manieren om de ontwerp ervaring in SQL-script te starten. U kunt een nieuw SQL-script maken via een van de volgende methoden.

1. Selecteer het pictogram ' + ' en kies SQL-script.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Kies in het menu acties onder SQL-scripts ontwikkelen de optie Nieuw SQL-script in het menu acties onder SQL-scripts ontwikkelen. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

of 

3. Kies importeren in het menu acties onder SQL-scripts ontwikkelen en selecteer een bestaand SQL-script uit uw lokale opslag.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Een SQL-script maken

1. Kies een naam voor het SQL-script door de knop eigenschap te selecteren en de standaard naam die is toegewezen aan het SQL-script te vervangen.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Kies de specifieke SQL-groep of de SQL on-demand in de vervolg keuzelijst verbinding maken met. Of kies, indien nodig, de data base in Data Base gebruiken.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Begin met het ontwerpen van uw SQL-script met behulp van de IntelliSense-functie.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Uw SQL-script uitvoeren

Selecteer de knop uitvoeren om het SQL-script uit te voeren. De resultaten worden standaard weer gegeven in een tabel.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Uw resultaten exporteren

U kunt de resultaten exporteren naar uw lokale opslag in verschillende indelingen (waaronder CSV, Excel, JSON en XML) door ' resultaten exporteren ' te selecteren en de uitbrei ding te kiezen.

U kunt de resultaten van het SQL-script ook visualiseren in een grafiek door de knop ' diagram ' te selecteren. Selecteer de kolom grafiek type en categorie. U kunt de grafiek naar een afbeelding exporteren door ' opslaan als afbeelding ' te selecteren. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Gegevens uit een Parquet-bestand verkennen.

U kunt Parquet-bestanden in een opslag account verkennen met behulp van SQL script om de inhoud van het bestand te bekijken. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-tabellen, externe tabellen, weer gaven

Als u het menu acties onder gegevens selecteert, kunt u verschillende acties selecteren, zoals ' nieuw SQL-script ', ' Best 1000 rijen selecteren ', ' maken ', ' verwijderen en maken '. Verken de beschik bare penbeweging door met de rechter muisknop te klikken op de knoop punten van SQL-groep en SQL op aanvraag.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)voor meer informatie over het ontwerpen van een SQL-script.