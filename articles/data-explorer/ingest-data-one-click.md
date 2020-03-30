---
title: Gebruik met één klik opname om gegevens in te nemen in Azure Data Explorer
description: Meer informatie over het innemen van (laden) gegevens in Azure Data Explorer met één klik.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444558"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Gebruik met één klik opname om gegevens in te nemen in Azure Data Explorer

In dit artikel ziet u hoe u met één klik inslikken voor het snel inslikken van een nieuwe tabel in JSON- of CSV-indelingen. De gegevens kunnen worden ingenomen uit opslag of een lokaal bestand in een bestaande tabel of een nieuwe tabel. Gebruik de intuïtieve wizard met één klik en uw gegevens worden binnen enkele minuten opgenomen. Vervolgens u de tabel bewerken en query's uitvoeren met de webgebruikersinterface van Azure Data Explorer.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Log hier [the application](https://dataexplorer.azure.com/)in
* Maak [een Azure Data Explorer-cluster en -database](create-cluster-database-portal.md).
* Meld u aan bij de [webgebruikersinterface](https://dataexplorer.azure.com/) en [voeg een verbinding toe aan uw cluster.](/azure/data-explorer/web-query-data#add-clusters)

## <a name="ingest-new-data"></a>Nieuwe gegevens innemen

1. Klik met de rechtermuisknop op de *database* of *tabelrij* in het linkermenu van de webgebruikersinterface en selecteer **Nieuwe gegevens opnemen (Voorbeeld).**

    ![Selecteer opname met één klik in de webgebruikersinterface](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Selecteer in het venster **Nieuwe gegevens innemen (Voorbeeld)** het tabblad **Bron** en vul het **projectdetails in:**

    * Selecteer **voor Tabel**een bestaande tabelnaam in het vervolgkeuzemenu of selecteer Nieuw **maken** om een nieuwe tabel te maken.
    * Kies voor **opnametype** **uit de opslag** of uit het **bestand**.
      * Als u **vanuit de opslag**hebt geselecteerd, selecteert u Koppeling naar **opslag** om de URL toe te voegen. Gebruik [blob sas-URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) voor privéopslagaccounts. 
      * Als u **uit het bestand**hebt geselecteerd, selecteert u **Bladeren** en sleept u het bestand naar het vak.
    * Selecteer **Schema bewerken** om de configuratie van de tabelkolom weer te geven en te bewerken.
 
    ![Gegevens over opnamebron met één klik](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Als u **Nieuwe gegevens (Voorbeeld) innemen** selecteert in een *tabelrij,* wordt de geselecteerde tabelnaam weergegeven in de **projectdetails**.

1. Als u een bestaande tabel hebt geselecteerd, wordt het venster **Kolommen met toewijzen** geopend om brongegevenskolommen naar doeltabelkolommen toe te geven. 
    * Gebruik **Kolom weglaten** om een doelkolom uit de tabel te verwijderen.
    * Gebruik **Nieuwe kolom** om een nieuwe kolom aan de tabel toe te voegen.

    ![Venster Kolommen toewijzen](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Ga als het tabblad **Schema:**

    * Selecteer **Compressietype** in het vervolgkeuzemenu en selecteer **Vervolgens Ongecomprimeerd** of **GZip**.
    * Selecteer **De indeling Gegevens** in het vervolgkeuzemenu en selecteer vervolgens **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**of **PSV**. 
        * Wanneer u de **JSON-indeling** selecteert, moet u ook **JSON-niveaus**selecteren , van 1 tot 10. De niveaus zijn van invloed op de weergave van de tabelkolomgegevens. 
        * Als u een andere indeling dan JSON inschakelt, schakelt u het selectievakje **Kolomnamen opnemen in** om de koprij van het bestand te negeren.
    * **De toewijzingsnaam** wordt automatisch ingesteld, maar kan worden bewerkt.
    * Als u een bestaande tabel hebt geselecteerd, u **Kolommen toewijzen** selecteren om het venster Kolommen **van kaart** te openen.

    ![Met één klik csv-indelingsschema](media/ingest-data-one-click/one-click-csv-format.png)

1. Selecteer boven het **editorvenster** de **v-knop** om de editor te openen. In de editor u de automatische query's bekijken en kopiëren die zijn gegenereerd vanuit uw invoer. 

1. In de tabel: 
    * Klik met de rechtermuisknop op nieuwe kolomkoppen om **gegevenstype**wijzigen , **Kolom**wijzigen , **Kolom verwijderen**, **Oplopend sorteren**of **Aflopend sorteren**. In bestaande kolommen is alleen gegevenssortering beschikbaar. 
    * Dubbelklik op de nieuwe kolomnaam om te bewerken.

1. Selecteer **Inname starten** om een tabel en toewijzing te maken en om te beginnen met het innemen van gegevens.

    ![Json-indelingsschema met één klik](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Querygegevens

1. In het voltooide venster **Gegevensopname** worden alle drie de stappen gemarkeerd met groene vinkjes als de inname van gegevens succesvol is voltooid.
 
    ![Gegevensopname met één klik voltooid](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selecteer de **v-knop** om de query te openen. Kopieer naar de webgebruikersinterface om de query te bewerken.

1. Het menu aan de rechterkant bevat **snelle query's** **en opties voor extra's.** 

    * **Snelle query's** bevatten koppelingen naar de webgebruikersinterface met voorbeeldquery's.
    * **Hulpprogramma's** bevatten een koppeling naar **Opdrachten laten vallen** op de webgebruikersinterface, waarmee u problemen oplossen door de relevante `.drop` opdrachten uit te voeren.

    > [!TIP]
    > U gegevens verliezen `.drop` wanneer u opdrachten gebruikt. Gebruik ze zorgvuldig.

## <a name="next-steps"></a>Volgende stappen

* [Querygegevens in Azure Data Explorer Web- gebruikersinterface](web-query-data.md)
* [Query's schrijven voor Azure Data Explorer met Kusto QueryLanguage](write-queries.md)
