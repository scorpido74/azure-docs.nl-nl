---
title: Gebruik een opname met één klik om gegevens op te nemen in azure Data Explorer
description: Meer informatie over het opnemen (laden) van gegevens in azure Data Explorer met één klik op opname.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 98598a28e14dfd8175cbb019ff1b001c65503580
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73644598"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Gebruik een opname met één klik om gegevens op te nemen in azure Data Explorer

In dit artikel wordt beschreven hoe u met één klik kunt opnemen in een nieuwe tabel in JSON-of CSV-indelingen van opslag in azure Data Explorer. Zodra de gegevens zijn opgenomen, kunt u de tabel bewerken en query's uitvoeren met de webgebruikersinterface.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* Meld u aan bij [de toepassing](https://dataexplorer.azure.com/).
* [Een Azure Data Explorer-cluster en-data base](create-cluster-database-portal.md) maken
* Aanmelden bij de [webgebruikersinterface](https://dataexplorer.azure.com/) en [een verbinding met uw cluster toevoegen](/azure/data-explorer/web-query-data#add-clusters)
* Bron van de gegevens in Azure Storage.

## <a name="ingest-new-data"></a>Nieuwe gegevens opnemen

1. Klik met de rechter muisknop op de *Data Base* *of tabelrij* in het linkermenu van de Web-UI en selecteer **nieuwe gegevens opnemen (preview-versie)**

    ![Selecteer een opname in de Web-UI](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. In het venster **nieuwe gegevens opnemen (preview)** op het tabblad **bron** voltooit u de **Project Details**:

    * **Tabel**: Selecteer bestaande tabel naam in de vervolg keuzelijst of selecteer **nieuwe maken** om een nieuwe tabel te maken.
    * Selecteer **opname type** > **van opslag** of **van bestand**.
        * Als u **uit de opslag ruimte**hebt gekozen, voert u **koppeling naar opslag** in om de URL toe te voegen aan de opslag. Gebruik de [SAS-URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) van de BLOB voor privé opslag accounts. 
        * Als u **in het bestand**hebt geselecteerd, selecteert u **Bladeren** en sleept u het bestand naar het vak.
    * Selecteer **schema bewerken** om de configuratie van de tabel kolom te bekijken en te bewerken.
 
    ![Eén Klik op opname bron Details](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Als u **nieuwe gegevens opnemen (preview)** *op een tabelrij* selecteert, wordt de naam van de geselecteerde tabel weer gegeven in de **Project Details**.

1. Als u een bestaande tabel hebt geselecteerd, wordt het venster **overzichts kolommen** geopend om de kolommen met bron gegevens toe te wijzen aan doel tabel kolommen. 
    * Gebruik de kolom overs **Laan** om een doel kolom uit de tabel te verwijderen. 
    * **Nieuwe** kolom gebruiken om een nieuwe kolom toe te voegen aan uw tabel. 

    ![Venster kolommen toewijzen](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Op het tabblad **schema** :

    * Selecteer **compressie type** in de vervolg keuzelijst > niet- **gecomprimeerd** of **gzip**.
    * Selecteer **gegevens indeling** in vervolg keuzelijst > **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**of **PSV**. 
        * Wanneer u **JSON** -indeling selecteert, selecteert u **JSON-niveaus**: 1-10. De niveaus zijn van invloed op de weer gave van tabel kolom gegevens. 
        * Als u een andere indeling dan JSON selecteert: selectie vakje selecteren **kolom namen** om rij met koppen van het bestand te negeren.    
    * De **toewijzings naam** wordt automatisch ingesteld, maar kan worden bewerkt.
    * Als u een bestaande tabel hebt geselecteerd, kunt u de knop **kolommen toewijzen** selecteren om het venster **kolommen toewijzen** te openen.

    ![Eén Klik op opname CSV-indeling schema. png](media/ingest-data-one-click/one-click-csv-format.png)

1. Selecteer in de **Editor**de optie **V** aan de rechter kant om de editor te openen. In de editor kunt u de automatische query's weer geven en kopiëren die zijn gegenereerd op basis van uw invoer. 

1.  In tabel: 
    * Klik met de rechter muisknop op nieuwe kolom koppen voor het wijzigen van het **gegevens type**, de **kolom naam wijzigen**, **kolom verwijderen**, **Oplopend sorteren**of **Aflopend sorteren**. In bestaande kolommen is alleen het sorteren van gegevens beschikbaar. 
    * Dubbel klik op de naam van de nieuwe kolom die u wilt bewerken.

1. Selecteer **opname starten** om tabel, toewijzing maken en gegevens opname te maken.

    ![schema met één klik voor de JSON-indeling van opname](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Querygegevens

1. In het venster **gegevens opname voltooid** worden alle drie de stappen gemarkeerd met een groen vinkje als de gegevens opname is voltooid. 
 
    ![Eén Klik op gegevens opname is voltooid](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selecteer **V** om query te openen. Kopiëren naar de Web-UI om de query te bewerken.

1. Het menu aan de rechter kant bevat **snelle query's** en **hulpprogram ma's**. 

    * **Snelle query's** bevatten koppelingen naar de Web-UI met voorbeeld query's.
    * **Hulpprogram ma's** bevatten koppelingen naar de Web-UI met **opdrachten voor neerzetten** waarmee u problemen kunt oplossen door de relevante `.drop` opdracht uit te voeren.

    > [!TIP]
    > Gegevens kunnen verloren gaan met `.drop` opdrachten. Gebruik deze zorgvuldig.

## <a name="next-steps"></a>Volgende stappen

* [Query's uitvoeren op gegevens in azure Data Explorer web-UI](web-query-data.md)
* [Query's schrijven voor Azure Data Explorer met behulp van de Kusto-query taal](write-queries.md)
