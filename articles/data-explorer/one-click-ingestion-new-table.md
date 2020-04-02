---
title: Gebruik met één klik opname om gegevens in te nemen in een nieuwe Azure Data Explorer-tabel
description: Gegevens eenvoudig innemen (laden) in een nieuwe Azure Data Explorer-tabel, met één klik.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 1e337a46d1d8ddda1b07bde6f12d4c1f7feda42f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549220"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-a-new-table-in-azure-data-explorer"></a>Gebruik met één klik opname om gegevens in te nemen in een nieuwe tabel in Azure Data Explorer

Met één klik u snel gegevens inJSON, CSV en andere indelingen in een tabel opnemen. Met de Web-gebruikersinterface van Azure Data Explorer u gegevens opnemen uit opslag, uit een lokaal bestand of uit een container. 

In dit document wordt beschreven dat u met de intuïtieve wizard met één klik CSV-gegevens uit een container in een nieuwe tabel opnemen. U de tabel vervolgens bewerken en query's uitvoeren met de Web-gebruikersinterface van Azure Data Explorer. U ook continu innemen instellen om automatisch gegevens in de tabel op te nemen wanneer de brongegevens worden bijgewerkt.

Inname met één klik is vooral handig bij het voor het eerst innemen van gegevens of wanneer het schema van uw gegevens niet bekend voor u is. 

Zie [Inname](ingest-data-one-click.md)met één klik voor een overzicht met één klik en een lijst met vereisten.
Zie Inname met één klik in een bestaande tabel voor informatie over het innemen van gegevens in een bestaande tabel in [een bestaande tabel.](one-click-ingestion-existing-table.md)

## <a name="ingest-new-data"></a>Nieuwe gegevens innemen

1. Klik in het linkermenu van de webgebruikersinterface met de rechtermuisknop op een *database* en selecteer **Nieuwe gegevens opnemen (Voorbeeld).**

    ![Selecteer opname met één klik in de webgebruikersinterface](media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png)   
 
1. In het venster **Nieuwe gegevens innemen (Voorbeeld)** wordt het tabblad **Bron** automatisch geselecteerd. 

1. Selecteer **Nieuwe tabel maken** en voer een naam in voor de nieuwe tabel. U alfanumerieke, koppeltekens en underscores gebruiken. Speciale tekens worden niet ondersteund.

![Een nieuwe tabel maken](media/one-click-ingestion-new-table/create-new-table.png) 

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]

Selecteer **Schema bewerken** om de configuratie van de tabelkolom weer te geven en te bewerken. Het systeem selecteert willekeurig een van de blobs en het schema wordt gegenereerd op basis van die blob. Door te kijken naar de naam van de bron, de dienst automatisch identificeert of het wordt gecomprimeerd of niet.

## <a name="edit-the-schema"></a>Het schema bewerken

1. Ga als het tabblad **Schema:**

    1. Selecteer **Gegevensindeling:**

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]

    1. Als u **JSON**selecteert, moet u ook **JSON-niveaus**selecteren, van 1 tot 10. De niveaus zijn van invloed op de weergave van de tabelkolomgegevens. 

    ![Json-niveaus selecteren](media/one-click-ingestion-new-table/json-levels.png)

    * Als u een andere indeling dan JSON inschakelt, u het selectievakje **Kolomnamen opnemen** inschakelen om de koprij van het bestand te negeren.

        ![Kolomnamen opnemen selecteren](media/one-click-ingestion-new-table/non-json-format.png)
        
1. Voer in het veld **Naam van kaarten** een toewijzingsnaam in. U alfanumerieke tekens en underscores gebruiken. Spaties, speciale tekens en koppeltekens worden niet ondersteund.
    
    ![Naam van tabeltoewijzing](media/one-click-ingestion-new-table/table-mapping.png)

## <a name="copy-and-paste-queries"></a>Query's kopiëren en plakken

1. Selecteer boven het **editorvenster** de **v-knop** om de editor te openen. In de editor u de automatische query's bekijken en kopiëren die zijn gegenereerd vanuit uw invoer. 
1. In de tabel: 
    * Dubbelklik op de nieuwe kolomnaam om te bewerken.
    * Selecteer nieuwe kolomkoppen en doe een van de volgende handelingen:
    
|Actie         |Beschrijving                                  |
|-----------------|-------------------------------------------|
|Gegevenstype wijzigen |Het gegevenstype wijzigen van de gegevensdie automatisch door de service is geselecteerd in een van de andere [ondersteunde gegevenstypen](#edit-the-schema)|
|Naam van kolom wijzigen    |De kolomnaam wijzigen |
|Nieuwe kolom       |Een nieuwe kolom toevoegen|
|Kolom verwijderen    |De geselecteerde kolom verwijderen|
|Oplopend sorteren   |De tabel sorteren op de geselecteerde kolom in opgaande volgorde (alleen bestaande kolommen)|
|Aflopend sorteren  |De tabel sorteren op de geselecteerde kolom in aflopende volgorde (alleen bestaande kolommen) |

> [!Note]
> Voor tabelindelingindelingen kan elke kolom in één kolom in Azure Data Explorer worden opgenomen.
> U nieuwe kolommen maken op verschillende JSON-niveaus.

[![](media/one-click-ingestion-new-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-new-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Inname starten

Selecteer **Inname starten** om een tabel en toewijzing te maken en om te beginnen met het innemen van gegevens.
![Inname starten](media/one-click-ingestion-new-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Gegevensopname voltooid

In het voltooide venster **Gegevensopname** worden alle drie de stappen gemarkeerd met groene vinkjes als de inname van gegevens succesvol is voltooid.
 
![Gegevensopname met één klik voltooid](media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

**Continue inname**

Continue inname stelt u in staat om een gebeurtenisraster te maken dat naar de broncontainer luistert. Elke nieuwe blob die voldoet aan de criteria van de vooraf gedefinieerde parameters (voorvoegsel, achtervoegsel, enzovoort) wordt automatisch opgenomen in de doeltabel.

> [!Note]
> Continue inname is alleen relevant bij het innemen van een container.

1. Selecteer **Continue opname** om de Azure-portal te openen. De pagina gegevensverbinding wordt geopend met de gebeurtenisrastergegevensconnector geopend en met bron- en doelparameters die al zijn ingevoerd (broncontainer, tabellen en toewijzingen).

1. Selecteer **Maken** om een gegevensverbinding te maken die luistert naar eventuele wijzigingen in die containercontainer. 

## <a name="next-steps"></a>Volgende stappen

* [Querygegevens in Azure Data Explorer Web- gebruikersinterface](/azure/data-explorer/web-query-data)
* [Query's schrijven voor Azure Data Explorer met Kusto QueryLanguage](/azure/data-explorer/write-queries)
