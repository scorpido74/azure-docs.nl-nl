---
title: Gebruik een opname met één klik om gegevens op te nemen in azure Data Explorer
description: Meer informatie over het opnemen (laden) van gegevens in azure Data Explorer met één klik op opname.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 0d7c8b2661ee3361b3a485b8cae4eef3a8225120
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975241"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Gebruik een opname met één klik om gegevens op te nemen in azure Data Explorer

Dit artikel laat u zien hoe u met één klik kunt klikken voor een snelle opname van een nieuwe tabel in de JSON-of CSV-indeling. De gegevens kunnen worden opgenomen van opslag of een lokaal bestand naar een bestaande tabel of een nieuwe tabel. Gebruik de intuïtieve wizard met één klik en uw gegevens worden binnen een paar minuten opgenomen. Vervolgens kunt u de tabel bewerken en query's uitvoeren met behulp van de Web-UI van Azure Data Explorer.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* Meld u aan bij [de toepassing](https://dataexplorer.azure.com/).
* Maak [een Azure Data Explorer-cluster en-data base](create-cluster-database-portal.md).
* Meld u aan bij de [webgebruikersinterface](https://dataexplorer.azure.com/) en [Voeg een verbinding met uw cluster toe](/azure/data-explorer/web-query-data#add-clusters).
* Een gegevens bron maken in Azure Storage.

## <a name="ingest-new-data"></a>Nieuwe gegevens opnemen

1. Klik met de rechter muisknop op de *Data Base* of *tabelrij* in het linkermenu van de Web-UI en selecteer **nieuwe gegevens opnemen (preview-versie)** .

    ![Selecteer in de webgebruikersinterface een opname met één klik.](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Selecteer in het venster **nieuwe gegevens opnemen (preview)** het tabblad **bron** en voltooi de **Project Details**:

    * Selecteer voor **tabel**een bestaande tabel naam in de vervolg keuzelijst of selecteer **nieuwe maken** om een nieuwe tabel te maken.
    * Voor **opname type**selecteert u **uit opslag** of **van bestand**.
        * Als u **uit de opslag ruimte**hebt gekozen, selecteert u **koppelen aan opslag** om de URL toe te voegen. Gebruik de [SAS-URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) van de BLOB voor privé opslag accounts. 
            * Als u **in het bestand**hebt geselecteerd, selecteert u **Bladeren** en sleept u het bestand naar het vak.
    * Selecteer **schema bewerken** om de configuratie van de tabel kolom te bekijken en te bewerken.
 
    ![Details van de bron voor opname met één klik.](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Als u **nieuwe gegevens opnemen (preview)** *op een tabelrij* selecteert, wordt de naam van de geselecteerde tabel weer gegeven in de **Project Details**.

1. Als u een bestaande tabel hebt geselecteerd, wordt het venster **overzichts kolommen** geopend om de kolommen met bron gegevens toe te wijzen aan doel tabel kolommen. 
    * Gebruik de kolom overs **Laan** om een doel kolom uit de tabel te verwijderen. 
    * **Nieuwe** kolom gebruiken om een nieuwe kolom toe te voegen aan de tabel. 

    ![Venster kolommen toewijzen.](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Op het tabblad **schema** :

    * Selecteer **compressie type** in de vervolg keuzelijst en selecteer vervolgens niet- **gecomprimeerd** of **gzip**.
    * Selecteer **gegevens indeling** in de vervolg keuzelijst en selecteer vervolgens **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**of **PSV**. 
        * Wanneer u de **JSON** -indeling selecteert, moet u ook **JSON-niveaus**selecteren, van 1 tot 10. De niveaus zijn van invloed op de weer gave van tabel kolom gegevens. 
        * Als u een andere indeling dan JSON selecteert, schakelt u het selectie vakje **kolom namen insluiten** in om de rij kopregel van het bestand te negeren.
    * De **toewijzings naam** wordt automatisch ingesteld, maar kan worden bewerkt.
    * Als u een bestaande tabel hebt geselecteerd, kunt u **kolom toewijzen** selecteren om het venster **kolommen toewijzen** te openen.

    ![Schema met één klik op opname CSV-indeling.](media/ingest-data-one-click/one-click-csv-format.png)

1. Selecteer de knop **v** in het deel venster **Editor** om de editor te openen. In de editor kunt u de automatische query's weer geven en kopiëren die zijn gegenereerd op basis van uw invoer. 

1.  In tabel: 
    * Klik met de rechter muisknop op nieuwe kolom koppen voor het wijzigen van het **gegevens type**, de **kolom naam wijzigen**, **kolom verwijderen**, **Oplopend sorteren**of **Aflopend sorteren**. In bestaande kolommen is alleen het sorteren van gegevens beschikbaar. 
    * Dubbel klik op de naam van de nieuwe kolom die u wilt bewerken.

1. Selecteer **opname starten** om een tabel en toewijzing te maken en gegevens opname te starten.

    ![Eén Klik op opname schema voor JSON-indeling.](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Querygegevens

1. In het venster **gegevens opname voltooid** worden alle drie de stappen gemarkeerd met een groen vinkje als de gegevens opname is voltooid.
 
    ![Gegevens opname met één klik is voltooid.](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selecteer de knop **v** om de query te openen. Kopieer de Web-UI om de query te bewerken.

1. Het menu aan de rechter kant bevat opties voor **snelle query's** en **hulpprogram ma's** . 

    * **Snelle query's** bevatten koppelingen naar de Web-UI met voorbeeld query's.
    * **Hulpprogram ma's** bevat een koppeling naar **opdrachten neerzetten** in de Web-UI, waarmee u problemen kunt oplossen door de relevante `.drop`-opdrachten uit te voeren.

    > [!TIP]
    > Gegevens kunnen verloren gaan door gebruik te maken van `.drop`-opdrachten. Gebruik deze zorgvuldig.

## <a name="next-steps"></a>Volgende stappen

* [Query's uitvoeren op gegevens in azure Data Explorer web-UI](web-query-data.md)
* [Query's schrijven voor Azure Data Explorer met behulp van de Kusto-query taal](write-queries.md)
