---
title: Gebruik met één klik opname om gegevens in te nemen in een bestaande Azure Data Explorer-tabel
description: Gegevens eenvoudig innemen (laden) in een bestaande Azure Data Explorer-tabel, met één klik.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: a7fdab66394c132bcd9134669b841d178c559f28
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546214"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-an-existing-table-in-azure-data-explorer"></a>Gebruik met één klik opname om gegevens in te nemen in een bestaande tabel in Azure Data Explorer

Met één klik u snel gegevens inJSON, CSV en andere indelingen in een tabel opnemen. Met de Web-gebruikersinterface van Azure Data Explorer u gegevens opnemen uit opslag, uit een lokaal bestand of uit een container. 

In dit document wordt beschreven dat u met de intuïtieve wizard met één klik JSON-gegevens uit een bestand in een bestaande tabel opnemen. U de tabel vervolgens bewerken en query's uitvoeren met de Web-gebruikersinterface van Azure Data Explorer.

Inname met één klik is vooral handig bij het voor het eerst innemen van gegevens of wanneer het schema van uw gegevens niet bekend voor u is. 

Zie [Inname](ingest-data-one-click.md)met één klik voor een overzicht met één klik en een lijst met vereisten.
Zie Met één klik opname in een nieuwe tabel in een nieuwe tabel in een nieuwe tabel voor informatie over het innemen van gegevens in [een nieuwe tabel.](one-click-ingestion-new-table.md)

## <a name="ingest-new-data"></a>Nieuwe gegevens innemen

1. Klik in het linkermenu van de webgebruikersinterface met de rechtermuisknop op een *database* of *tabel* en selecteer **Nieuwe gegevens opnemen (Voorbeeld).**

    ![Selecteer opname met één klik in de webgebruikersinterface](media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png)   
 
1. In het venster **Nieuwe gegevens innemen (Voorbeeld)** wordt het tabblad **Bron** automatisch geselecteerd.

1. Als het veld **Tabel** niet automatisch wordt gevuld, selecteert u een bestaande tabelnaam in het vervolgkeuzemenu.
    > [!TIP]
    > Als u **Nieuwe gegevens (Voorbeeld) innemen** selecteert in een *tabelrij,* wordt de geselecteerde tabelnaam weergegeven in de **projectdetails**.

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]
    
Selecteer **Schema bewerken** om de configuratie van de tabelkolom weer te geven en te bewerken.

## <a name="edit-the-schema"></a>Het schema bewerken

1. Het dialoogvenster **Kolommen toewijzen** wordt geopend en u brongegevenskolommen toewijzen aan doeltabelkolommen. 
    * Voer in de velden **Bronkolommen** kolomnamen in die u wilt toewijzen aan de **doelkolommen**.
    * Als u een toewijzing wilt verwijderen, selecteert u het pictogram prullenbak.

    ![Venster Kolommen toewijzen](media/one-click-ingestion-existing-table/map-columns.png)

1. Selecteer **Update**.
1. Ga als het tabblad **Schema:**
    1. Selecteer **Compressietype**en selecteer **Vervolgens Ongecomprimeerd** of **GZip**.

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]
        
    1. Als u **JSON**selecteert, moet u ook **JSON-niveaus**selecteren, van 1 tot 10. De niveaus zijn van invloed op de weergave van de tabelkolomgegevens.

    ![Json-niveaus selecteren](media/one-click-ingestion-existing-table/json-levels.png)

    * Als u een andere indeling dan JSON inschakelt, u het selectievakje **Kolomnamen opnemen** inschakelen om de koprij van het bestand te negeren.
        
    ![Kolomnamen opnemen selecteren](media/one-click-ingestion-existing-table/non-json-format.png)

    > [!Note]
    > Tabelindelingen kunnen kolomgegevens opnemen in slechts één kolom in een tabel met Azure Data Explorer. 

    * Nieuwe toewijzingen worden automatisch ingesteld, maar u deze wijzigen om een bestaande kaart te gebruiken. 
    * U **Kolommen toewijzen** selecteren om het venster Kolommen van de **kaart** te openen.

## <a name="copy-and-paste-queries"></a>Query's kopiëren en plakken

1. Selecteer boven het **editorvenster** de **v-knop** om de editor te openen. In de editor u de automatische opdrachten bekijken en kopiëren die zijn gegenereerd vanuit uw invoer. 
1. In de tabel: 
    * Selecteer nieuwe kolomkoppen om een **nieuwe kolom**toe te voegen , **Kolom verwijderen**, **Oplopend sorteren**of **Aflopend sorteren**. In bestaande kolommen is alleen gegevenssortering beschikbaar.

    > [!Note]
    > * U de naam en het gegevenstype voor bestaande tabellen niet bijwerken.
    > * Drop opdrachten zullen alleen de wijzigingen die zijn aangebracht door zijn inname stroom (nieuwe omvang en kolommen). Niets anders zal worden geschrapt.

[![](media/one-click-ingestion-existing-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-existing-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Inname starten

Selecteer **Inname starten** om een tabel en toewijzing te maken en om te beginnen met het innemen van gegevens.

![Inname starten](media/one-click-ingestion-existing-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Gegevensopname voltooid

In het voltooide venster **Gegevensopname** worden alle drie de stappen gemarkeerd met groene vinkjes als de inname van gegevens succesvol is voltooid.
 
![Gegevensopname met één klik voltooid](media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Volgende stappen

* [Querygegevens in Azure Data Explorer Web- gebruikersinterface](/azure/data-explorer/web-query-data)
* [Query's schrijven voor Azure Data Explorer met Kusto QueryLanguage](/azure/data-explorer/write-queries)
