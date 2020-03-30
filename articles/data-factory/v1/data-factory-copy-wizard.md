---
title: Gegevens eenvoudig kopiëren met Wizard Kopiëren - Azure
description: Meer informatie over het gebruik van de wizard Gegevensfabriekkopiëren om gegevens uit ondersteunde gegevensbronnen te kopiëren naar putten.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b872cc30ae66e83274f189138dad6d609e3f536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927058"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Gegevens eenvoudig kopiëren of verplaatsen met de wizard Kopiëren van Azure Data Factory
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [zelfstudie over kopieeractiviteiten](../quickstart-create-data-factory-dot-net.md). 


De wizard Azure Data Factory Copy is bedoeld om het proces van het innemen van gegevens te vergemakkelijken, wat meestal een eerste stap is in een end-to-end scenario voor gegevensintegratie. Wanneer u de wizard Azure Data Factory Copy doorloopt, hoeft u geen JSON-definities te begrijpen voor gekoppelde services, gegevenssets en pijplijnen. Nadat u echter alle stappen in de wizard hebt voltooid, maakt de wizard automatisch een pijplijn om gegevens van de geselecteerde gegevensbron naar de geselecteerde bestemming te kopiëren. Bovendien helpt de wizard Kopiëren u bij het valideren van de gegevens die worden ingenomen op het moment van schrijven, wat een groot deel van uw tijd bespaart, vooral wanneer u gegevens voor het eerst uit de gegevensbron inneemt. Als u de wizard Kopiëren wilt starten, klikt u op de tegel **Gegevens kopiëren** op de startpagina van uw gegevensfabriek.

![De wizard Kopiëren](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Een intuïtieve wizard voor het kopiëren van gegevens
Met deze wizard u eenvoudig gegevens van een breed scala aan bronnen naar bestemmingen in enkele minuten verplaatsen. Nadat u de wizard hebt doorlopen, wordt er automatisch een pijplijn met een kopieeractiviteit voor u gemaakt, samen met afhankelijke entiteiten van Data Factory (gekoppelde services en gegevenssets). Er zijn geen extra stappen nodig om de pijplijn te maken.   

![Gegevensbron selecteren](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Zie [Zelfstudieartikel Wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor stapsgewijze instructies om een voorbeeldpijplijn te maken om gegevens van een Azure-blob naar een Azure SQL Database-tabel te kopiëren. 
> 
> 

De wizard is ontworpen met big data in het achterhoofd vanaf het begin. Het is eenvoudig en efficiënt om datafactory-pijplijnen te maken die honderden mappen, bestanden of tabellen verplaatsen met de wizard Gegevens kopiëren. De wizard ondersteunt de volgende drie functies: Automatische gegevensvoorbeeld, schemavastleggen en toewijzen en filteren van gegevens. 

## <a name="automatic-data-preview"></a>Automatische gegevensvoorbeeld
Met de wizard Kopiëren u een deel van de gegevens uit de geselecteerde gegevensbron bekijken om te valideren of de gegevens de juiste gegevens zijn die u wilt kopiëren. Als de brongegevens zich in een tekstbestand bevindt, onteert de wizard kopiëren bovendien het tekstbestand om rij- en kolomscheidingstekens te leren en automatisch schema. 

![Pagina Instellingen bestandsindelingen](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schema vastleggen en toewijzen
Het schema van invoergegevens komt in sommige gevallen mogelijk niet overeen met het schema van uitvoergegevens. In dit scenario moet u kolommen van het bronschema toewijzen aan kolommen uit het doelschema. 

De wizard Kopiëren brengt kolommen in het bronschema automatisch in het bronschema in het doelschema. U de toewijzingen overschrijven met behulp van de vervolgkeuzelijsten (of) opgeven of een kolom moet worden overgeslagen tijdens het kopiëren van de gegevens.   

![Schematoewijzing](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Gegevens filteren
Met de wizard u brongegevens filteren om alleen de gegevens te selecteren die naar het gegevensarchief bestemming/gootsteen moeten worden gekopieerd. Filteren vermindert het volume van de gegevens die naar het gootsteengegevensarchief moeten worden gekopieerd en verbetert daarom de doorvoer van de kopieerbewerking. Het biedt een flexibele manier om gegevens in een relationele database te filteren met SQL-querytaal (of) bestanden in een Azure blob-map met behulp van [datafactory-functies en variabelen.](data-factory-functions-variables.md)   

### <a name="filtering-of-data-in-a-database"></a>Gegevens filteren in een database
In het voorbeeld gebruikt de `Text.Format` SQL-query de functie en `WindowStart` variabele. 

![Expressies valideren](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Gegevens filteren in een Azure blob-map
U variabelen in het mappad gebruiken om gegevens te kopiëren uit een map die wordt bepaald tijdens runtime op basis van [systeemvariabelen.](data-factory-functions-variables.md#data-factory-system-variables) De ondersteunde variabelen zijn: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}**, en **{custom}**. Voorbeeld: invoermap/{year}/{month}/{day}.

Stel dat u invoermappen in de volgende indeling hebt:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klik **op de** knop Bladeren voor **Bestand of map,** blader naar een van deze mappen (bijvoorbeeld 2016->03->01->02) en klik op **Kiezen**. Je zou `2016/03/01/02` het in het tekstvak moeten zien. Vervang **2016** nu door **{jaar}**, **03** met **{maand}**, **01** met **{day}** en **02** met **{hour}** en druk op Tab. U ziet vervolgkeuzelijsten om de indeling voor deze vier variabelen te selecteren:

![Systeemvariabelen gebruiken](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Zoals in de volgende schermafbeelding wordt weergegeven, u ook een **aangepaste** variabele en eventuele [ondersteunde indelingstekenreeksen](https://msdn.microsoft.com/library/8kb3ddd4.aspx)gebruiken. Als u een map met die structuur wilt selecteren, gebruikt u eerst de knop **Bladeren.** Vervang vervolgens een waarde door **{custom}** en druk op Tab om het tekstvak te zien waar u de opmaaktekenreeks typen.     

![Aangepaste variabele gebruiken](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Ondersteuning voor diverse gegevens- en objecttypen
Met de wizard Kopiëren u op efficiënte wijze honderden mappen, bestanden of tabellen verplaatsen.

![Tabellen selecteren waaruit u gegevens wilt kopiëren](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Planningsopties
U de kopieerbewerking één keer of volgens een schema uitvoeren (per uur, per dag, enzovoort). Beide opties kunnen worden gebruikt voor de breedte van de connectors in on-premises, cloud en lokale desktopkopie.

Een eenmalige kopieerbewerking maakt gegevensverkeer van een bron naar een bestemming slechts één keer mogelijk. Het is van toepassing op gegevens van elke grootte en elke ondersteunde indeling. Met de geplande kopie u gegevens over een voorgeschreven herhaling kopiëren. U uitgebreide instellingen (zoals opnieuw proberen, time-out en waarschuwingen) gebruiken om de geplande kopie te configureren.

![Eigenschappen plannen](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Volgende stappen
Zie [Zelfstudie: Een pijplijn maken met de wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)kopiëren voor een snelle doorloop van het gebruik van de wizard Gegevensfabriekkopiëren om een pijplijn met Kopieeractiviteit te maken.

