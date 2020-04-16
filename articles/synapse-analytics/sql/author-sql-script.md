---
title: SQL-scripts in Azure Synapse Studio (voorbeeld)
description: Introductie Azure Synapse Studio (preview) SQL-scripts
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431070"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>SQL-script gebruiken in Azure Synapse Studio (voorbeeld)

Azure Synapse Studio (preview) biedt een SQL script-webinterface voor u om SQL-query's te maken. U verbinding maken met SQL Pool (preview) of SQL on-demand (preview). 

## <a name="begin-authoring-in-sql-script"></a>Beginnen met ontwerpen in SQL-script 

Er zijn verschillende manieren om de ontwerpervaring in SQL-script te starten. U een nieuw SQL-script maken via een van de volgende methoden.

1. Selecteer het pictogram '+' en kies SQL-script.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Kies in het menu Acties onder SQL-scripts ontwikkelen Kies 'Nieuw SQL-script' in het menu 'Acties' onder SQL-scripts ontwikkelen. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

of 

3. Kies 'Importeren' in het menu 'Acties' onder SQL-scripts ontwikkelen en selecteer een bestaand SQL-script uit uw lokale opslag.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Uw SQL-script maken

1. Kies een naam voor uw SQL-script door de knop Eigenschap te selecteren en de standaardnaam te vervangen die is toegewezen aan het SQL-script.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Kies de specifieke SQL-groep of de SQL on-demand in het vervolgkeuzemenu 'Verbinden met' . Of kies indien nodig de database uit "Database gebruiken".

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Begin met het ontwerpen van uw SQL-script met de intellisense-functie.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Uw SQL-script uitvoeren

Selecteer de knop Uitvoeren om uw SQL-script uit te voeren. De resultaten worden standaard weergegeven in een tabel.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Uw resultaten exporteren

U de resultaten exporteren naar uw lokale opslag in verschillende indelingen (waaronder CSV, Excel, JSON, XML) door 'Resultaten exporteren' te selecteren en de extensie te kiezen.

U ook de SQL-scriptresultaten in een grafiek visualiseren door de knop 'Grafiek' te selecteren. Selecteer het 'Grafiektype' en 'Kolom Categorie'. U de grafiek exporteren naar een afbeelding door 'Opslaan als afbeelding' te selecteren. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Bekijk gegevens uit een parketbestand.

U Parketbestanden in een opslagaccount verkennen met SQL-script om een voorbeeld van de bestandsinhoud te bekijken. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-tabellen, externe tabellen, weergaven

Door het menu 'Acties' onder gegevens te selecteren, u verschillende acties selecteren, zoals: Nieuw SQL-script, Selecteer TOP 1000-rijen, CREATE, DROP en CREATE. Verken het beschikbare gebaar door met de rechtermuisknop op de knooppunten sql-pool en SQL on-demand te klikken.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)voor meer informatie over het ontwerpen van een SQL-script.