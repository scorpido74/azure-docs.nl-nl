---
title: Delta-kopie uit een database met behulp van een controletabel
description: Meer informatie over het gebruik van een oplossingssjabloon om nieuwe of bijgewerkte rijen alleen vanuit een database met Azure Data Factory stapsgewijs te kopiëren.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 3c077e2c04cae94d2e1a2a84ccd7d09c7a0829b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439738"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Delta-kopie uit een database met een controletabel

In dit artikel wordt een sjabloon beschreven die beschikbaar is om nieuwe of bijgewerkte rijen uit een databasetabel stapsgewijs te laden naar Azure met behulp van een externe controletabel die een hoogwatermerkwaarde opslaat.

Deze sjabloon vereist dat het schema van de brondatabase een tijdstempelkolom of een verhogingssleutel bevat om nieuwe of bijgewerkte rijen te identificeren.

>[!NOTE]
> Als u een tijdstempelkolom in uw brondatabase hebt om nieuwe of bijgewerkte rijen te identificeren, maar u geen externe controletabel wilt maken die u wilt gebruiken voor delta-kopie, u in plaats daarvan het [hulpprogramma gegevenskopiëren](copy-data-tool.md) van Azure Data Factory gebruiken om een pijplijn te krijgen. Dat hulpprogramma gebruikt een door trigger geplande tijd als variabele om nieuwe rijen uit de brondatabase te lezen.

## <a name="about-this-solution-template"></a>Over deze oplossingssjabloon

Deze sjabloon haalt eerst de oude watermerkwaarde op en vergelijkt deze met de huidige watermerkwaarde. Daarna worden alleen de wijzigingen uit de brondatabase kopieën gemaakt op basis van een vergelijking tussen de twee watermerkwaarden. Ten slotte slaat het de nieuwe hoogwatermerkwaarde op in een externe controletabel voor het laden van deltagegevens de volgende keer.

De sjabloon bevat vier activiteiten:
- **Lookup** haalt de oude hoogwatermerkwaarde op, die is opgeslagen in een externe controletabel.
- Een andere **opzoekactiviteit** haalt de huidige hoogwatermerkwaarde op uit de brondatabase.
- **Kopieer** alleen wijzigingen van de brondatabase naar het doelarchief. De query die de wijzigingen in de brondatabase identificeert, is vergelijkbaar met 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > 'last high-watermark' en TIMESTAMP_Column <= 'current high-watermark''.
- **SqlServerStoredProcedure** schrijft de huidige hoogwatermerkwaarde naar een externe controletabel voor delta-kopie de volgende keer.

De sjabloon definieert de volgende parameters:
- *Data_Source_Table_Name* is de tabel in de brondatabase waarvan u gegevens wilt laden.
- *Data_Source_WaterMarkColumn* is de naam van de kolom in de brontabel die wordt gebruikt om nieuwe of bijgewerkte rijen te identificeren. Het type van deze kolom is meestal *datumtijd,* *INT*of iets dergelijks.
- *Data_Destination_Container* is het hoofdpad van de plaats waar naar de gegevens wordt gekopieerd in uw bestemmingsarchief.
- *Data_Destination_Directory* is het mappad onder de hoofdmap van de plaats waar naar de gegevens wordt gekopieerd in uw bestemmingsarchief.
- *Data_Destination_Table_Name* is de plaats waar de gegevens worden gekopieerd naar in uw doelarchief (van toepassing wanneer "Azure Synapse Analytics (voorheen SQL DW)" is geselecteerd als Gegevensbestemming).
- *Data_Destination_Folder_Path* is de plaats waar naar de gegevens wordt gekopieerd in uw bestemmingsarchief (van toepassing wanneer 'Bestandssysteem' of 'Azure Data Lake Storage Gen1' is geselecteerd als gegevensbestemming).
- *Control_Table_Table_Name* is de externe controletabel die de hoogwatermerkwaarde opslaat.
- *Control_Table_Column_Name* is de kolom in de externe controletabel die de waarde van het hoogwatermerk opslaat.

## <a name="how-to-use-this-solution-template"></a>Deze oplossingssjabloon gebruiken

1. Verken de brontabel die u wilt laden en definieer de hoogwatermerkkolom die kan worden gebruikt om nieuwe of bijgewerkte rijen te identificeren. Het type van deze kolom kan *datumtijd,* *INT*of iets dergelijks zijn. De waarde van deze kolom neemt toe naarmate nieuwe rijen worden toegevoegd. In de volgende voorbeeldbrontabel (data_source_table) kunnen we de kolom *LastModifytime* gebruiken als de kolom hoogwatermerk.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Maak een besturingselementtabel in SQL Server of Azure SQL Database om de hoogwatermerkwaarde voor deltagegevensladen op te slaan. In het volgende voorbeeld is de naam van de controletabel *watermarktbaar.* In deze tabel is *WatermarkValue* de kolom die de waarde van het hoogwatermerk opslaat en het type *datumtijd*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Maak een opgeslagen procedure in dezelfde SQL Server- of Azure SQL Database-instantie die u hebt gebruikt om de beheertabel te maken. De opgeslagen procedure wordt gebruikt om de nieuwe hoogwatermerkwaarde naar de externe controletabel te schrijven voor het laden van deltagegevens de volgende keer.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Ga naar de **Delta-kopie van databasesjabloon.** Maak een **nieuwe** verbinding met de brondatabase waarvan u gegevens wilt kopiëren.

    ![Een nieuwe verbinding met de brontabel maken](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Maak een **nieuwe** verbinding met het doelgegevensarchief waarnaar u de gegevens wilt kopiëren.

    ![Een nieuwe verbinding maken met de doeltabel](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Maak een **nieuwe** verbinding met de externe controletabel en de opgeslagen procedure die u in stap 2 en 3 hebt gemaakt.

    ![Een nieuwe verbinding maken met het gegevensarchief van de controletabel](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selecteer **Deze sjabloon gebruiken**.
    
8. U ziet de beschikbare pijplijn, zoals weergegeven in het volgende voorbeeld:
  
    ![De pijplijn bekijken](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selecteer **Opgeslagen procedure**. Kies voor **de naam van de opgeslagen procedure** **[dbo].[ update_watermark]**. Selecteer **Parameter Importeren**en selecteer Dynamische inhoud **toevoegen**.  

    ![De opgeslagen procedureactiviteit instellen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Schrijf de inhoud ** \@{activiteit('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** en selecteer **Voltooien**.  

    ![De inhoud voor de parameters van de opgeslagen procedure schrijven](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Selecteer **Foutopsporing,** voer de **parameters**in en selecteer **Voltooien**.

    ![Selecteer **Foutopsporing**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Resultaten die vergelijkbaar zijn met het volgende voorbeeld worden weergegeven:

    ![Bekijk het resultaat](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. U nieuwe rijen maken in uw brontabel. Hier vindt u voorbeeldSQL-taal om nieuwe rijen te maken:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Als u de pijplijn opnieuw wilt uitvoeren, selecteert u **Foutopsporing,** voert u de **parameters**in en selecteert u **Voltooien**.

    U zult zien dat alleen nieuwe rijen naar de bestemming zijn gekopieerd.

15. (Optioneel:) Als u Azure Synapse Analytics (voorheen SQL DW) als gegevensbestemming selecteert, moet u ook een verbinding met Azure Blob-opslag bieden voor fasering, wat vereist is door SQL Data Warehouse Polybase. De sjabloon genereert een containerpad voor u. Controleer na het uitvoeren van de pijplijn of de container is gemaakt in blobopslag.
    
    ![Polybase configureren](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Volgende stappen

- [Bulkkopie uit een database met behulp van een controletabel met Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Bestanden uit meerdere containers kopiëren met Azure Data Factory](solution-template-copy-files-multiple-containers.md)
