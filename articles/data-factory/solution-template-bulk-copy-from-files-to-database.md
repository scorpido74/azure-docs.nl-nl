---
title: Bulkgewijs kopiëren van bestanden naar database
description: Meer informatie over het gebruik van een oplossingssjabloon om gegevens in bulk te kopiëren van Azure Data Lake Storage Gen2 naar Azure Synapse Analytics / Azure SQL Database.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: ae250c7d15801789ad22955845cfa535ed91f2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75921143"
---
# <a name="bulk-copy-from-files-to-database"></a>Bulkgewijs kopiëren van bestanden naar database

In dit artikel wordt een oplossingssjabloon beschreven die u gebruiken om gegevens in bulk te kopiëren van Azure Data Lake Storage Gen2 naar Azure Synapse Analytics / Azure SQL Database.

## <a name="about-this-solution-template"></a>Over deze oplossingssjabloon

Met deze sjabloon worden bestanden opgehaald uit de Bron van Azure Data Lake Storage Gen2. Vervolgens wordt het over elk bestand in de bron heen gehesen en wordt het bestand naar het doelgegevensarchief gekopieerd. 

Momenteel ondersteunt deze sjabloon alleen het kopiëren van gegevens in **de indeling DelimitedText.** Bestanden in andere gegevensindelingen kunnen ook worden opgehaald uit het brongegevensarchief, maar kunnen niet worden gekopieerd naar het doelgegevensarchief.  

De sjabloon bevat drie activiteiten:
- **Metagegevensactiviteit** ophalen haalt bestanden op uit Azure Data Lake Storage Gen2 en geeft deze door aan de volgende *ForEach-activiteit.*
- **ForEach** activity krijgt bestanden van de activiteit *Metagegevens ophalen* en verlegt elk bestand naar de *activiteit Kopiëren.*
- **Kopieer** activiteit bevindt zich in *ForEach* activiteit om elk bestand te kopiëren van het brongegevensarchief naar het doelgegevensarchief.

De sjabloon definieert de volgende twee parameters:
- *SourceContainer* is het hoofdcontainerpad waar de gegevens van worden gekopieerd in uw Azure Data Lake Storage Gen2. 
- *SourceDirectory* is het mappad onder de hoofdcontainer waar de gegevens van worden gekopieerd in uw Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Deze oplossingssjabloon gebruiken

1. Ga naar de **sjabloon Bulk kopiëren van bestanden naar database.** Maak een **nieuwe** verbinding met de bron-Gen2-winkel. Houd er rekening mee dat "GetMetadataDataset" en "SourceDataset" verwijzingen zijn naar dezelfde verbinding van uw bronbestandsarchief.

    ![Een nieuwe verbinding maken met het brongegevensarchief](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Maak een **nieuwe** verbinding met het sink-gegevensarchief waarnaar u gegevens kopieert.

    ![Een nieuwe verbinding maken met het sink-gegevensarchief](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Selecteer **Deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. U ziet een pijplijn die is gemaakt zoals in het volgende voorbeeld wordt weergegeven:

    ![De pijplijn bekijken](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Als u **Azure Synapse Analytics (voorheen SQL DW)** als gegevensbestemming in **stap 2** hierboven hebt gekozen, moet u een verbinding met Azure Blob-opslag invoeren voor fasering, zoals vereist door SQL Data Warehouse Polybase. Zoals de volgende schermafbeelding wordt weergegeven, genereert de sjabloon automatisch een *opslagpad* voor uw Blob-opslag. Controleer of de container is gemaakt nadat de pijplijn is uitgevoerd.
        
    ![Polybase-instelling](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Selecteer **Foutopsporing,** voer de **parameters**in en selecteer **Voltooien**.

    ![Klik op **Foutopsporing**](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Wanneer de pijplijnrun is voltooid, ziet u resultaten die vergelijkbaar zijn met het volgende voorbeeld:

    ![Bekijk het resultaat](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Data Factory](introduction.md)