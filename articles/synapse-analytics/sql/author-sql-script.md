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
ms.openlocfilehash: 9d130c2a2db9ccead7180b6248398a84fcb34c3f
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441235"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>SQL-script gebruiken in azure Synapse Studio (preview-versie)

Azure Synapse Studio (preview) biedt een SQL script web-interface waarmee u SQL-query's kunt schrijven. U kunt verbinding maken met SQL-groep (preview) of SQL op aanvraag (preview). 

## <a name="begin-authoring-in-sql-script"></a>Beginnen met ontwerpen in SQL-script 

Er zijn verschillende manieren om de ontwerp ervaring in SQL-script te starten. U kunt een nieuw SQL-script maken via een van de volgende methoden.

1. Selecteer in het menu ontwikkelen het pictogram **' + '** en kies **SQL-script**.

![nieuw SQL-script](media/author-sql-script/newsqlscript.png)

2. Kies in het menu **acties** de optie **Nieuw SQL-script**.
> [!div class="mx-imgBorder"]
> ![nieuwe SQL-script 2-acties](media/author-sql-script/newsqlscript2actions.png)

U kunt ook: 

3. Kies **importeren** in het menu **acties** onder SQL-scripts ontwikkelen. Selecteer een bestaand SQL-script uit uw lokale opslag.
![nieuwe SQL script 3-acties](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Een SQL-script maken

1. Kies een naam voor uw SQL-script door de knop **Eigenschappen** te selecteren en de standaard naam te vervangen die aan het SQL-script is toegewezen. 
![nieuwe SQL-script naam wijzigen](media/author-sql-script/newsqlscriptrename.png)

2. Kies de specifieke SQL-groep of de SQL op aanvraag in de vervolg keuzelijst **verbinding maken met** . Of kies, indien nodig, de data base uit **Data Base gebruiken**. 
![nieuwe SQL-groep kiezen](media/author-sql-script/newsqlchoosepool.png)

3. Begin met het ontwerpen van uw SQL-script met behulp van de IntelliSense-functie.
![nieuwe SQL IntelliSense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Uw SQL-script uitvoeren

Selecteer de knop uitvoeren om het SQL-script **uit** te voeren. De resultaten worden standaard weer gegeven in een tabel.

![tabel met nieuwe SQL-script resultaten](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Uw resultaten exporteren

U kunt de resultaten exporteren naar uw lokale opslag in verschillende indelingen (waaronder CSV, Excel, JSON en XML) door ' resultaten exporteren ' te selecteren en de uitbrei ding te kiezen.

U kunt de resultaten van het SQL-script ook visualiseren in een grafiek door de knop **grafiek** te selecteren. Selecteer de kolom grafiek type en **categorie**. U kunt de grafiek exporteren naar een afbeelding door **Opslaan als afbeelding**te selecteren. 

![grafiek met nieuwe SQL-script resultaten](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Gegevens uit een Parquet-bestand verkennen

U kunt Parquet-bestanden in een opslag account verkennen met behulp van SQL script om de inhoud van het bestand te bekijken.

![nieuw script sqlod Parquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-tabellen, externe tabellen, weer gaven

Als u het menu **acties** onder gegevens selecteert, kunt u verschillende acties selecteren, zoals:

- Nieuw SQL-script
- BOVENSTE 1000 rijen selecteren
- CREËREN
- NEERZETten en maken 
 
Verken de beschik bare penbeweging door met de rechter muisknop te klikken op de knoop punten van SQL-groep en SQL op aanvraag.
 
![nieuwe script database](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)voor meer informatie over het ontwerpen van een SQL-script.
