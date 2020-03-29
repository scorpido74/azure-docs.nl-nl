---
title: Wizard Kopiëren van gegevensfabriek Azure
description: Meer informatie over het gebruik van de wizard Azure Copy van Gegevensfabriek om gegevens uit ondersteunde gegevensbronnen te kopiëren naar putten.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fef9059700e2bd94029c40bb819870a7174e0812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930120"
---
# <a name="azure-data-factory-copy-wizard"></a>Wizard Kopiëren van Azure-gegevensfabriek
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. 

De wizard Azure Data Factory Copy vereenvoudigt het proces van het innemen van gegevens, wat meestal een eerste stap is in een end-to-end scenario voor gegevensintegratie. Wanneer u de wizard Kopiëren van Azure Data Factory doorloopt, hoeft u geen JSON-definities voor gekoppelde services, gegevenssets en pijplijnen te begrijpen. De wizard maakt automatisch een pijplijn om gegevens van de geselecteerde gegevensbron naar de geselecteerde bestemming te kopiëren. Bovendien helpt de wizard Kopiëren u bij het valideren van de gegevens die worden ingenomen op het moment van schrijven. Dit bespaart tijd, vooral wanneer u gegevens voor het eerst inneemt van de gegevensbron. Als u de wizard Kopiëren wilt starten, klikt u op de tegel **Gegevens kopiëren** op de startpagina van uw gegevensfabriek.

![De wizard Kopiëren](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Ontworpen voor big data
Met deze wizard u eenvoudig gegevens van een breed scala aan bronnen naar bestemmingen in enkele minuten verplaatsen. Nadat u de wizard hebt doorlopen, wordt er automatisch een pijplijn met een kopieeractiviteit voor u gemaakt, samen met afhankelijke entiteiten van Data Factory (gekoppelde services en gegevenssets). Er zijn geen extra stappen nodig om de pijplijn te maken.   

![Gegevensbron selecteren](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Zie de [zelfstudie wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)voor stapsgewijze instructies om een voorbeeldpijplijn te maken om gegevens van een Azure-blob naar een Azure SQL-databasetabel te kopiëren.
>
>

De wizard is vanaf het begin ontworpen met big data in gedachten, met ondersteuning voor diverse gegevens- en objecttypen. U Data Factory-pijplijnen maken die honderden mappen, bestanden of tabellen verplaatsen. De wizard ondersteunt automatische gegevensvoorbeeld, schema-vastleggen en toewijzen en gegevensfiltering.

## <a name="automatic-data-preview"></a>Automatische gegevensvoorbeeld
U een voorbeeld van een deel van de gegevens bekijken uit de geselecteerde gegevensbron om te valideren of de gegevens zijn wat u wilt kopiëren. Als de brongegevens zich in een tekstbestand bevindt, wordt het tekstbestand bovendien door de wizard Kopiëren ontlijnd om de scheidingstekens en het schema van de kolom automatisch te leren.

![Pagina Instellingen bestandsindelingen](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schema vastleggen en toewijzen
Het schema van invoergegevens komt in sommige gevallen mogelijk niet overeen met het schema van uitvoergegevens. In dit scenario moet u kolommen van het bronschema toewijzen aan kolommen uit het doelschema.

> [!TIP]
> Wanneer u gegevens uit SQL Server of Azure SQL Database kopieert naar Azure SQL Data Warehouse, als de tabel niet bestaat in het doelarchief, ondersteunt Data Factory het maken van automatische tabel met behulp van het schema van de bron. Meer informatie van [Gegevens verplaatsen van en naar Azure SQL Data Warehouse met Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Gebruik een vervolgkeuzelijst om een kolom uit het bronschema te selecteren om een kolom in het doelschema toe te voegen. De wizard Kopiëren probeert inzicht te krijgen in uw patroon voor kolomtoewijzing. Het past hetzelfde patroon toe op de rest van de kolommen, zodat u niet elk van de kolommen afzonderlijk hoeft te selecteren om de schematoewijzing te voltooien. Als u dat liever hebt, u deze toewijzingen overschrijven door de vervolgkeuzelijsten te gebruiken om de kolommen één voor één in kaart te brengen. Het patroon wordt nauwkeuriger naarmate u meer kolommen in kaart brengt. De wizard Kopiëren werkt het patroon voortdurend bij en bereikt uiteindelijk het juiste patroon voor de kolomtoewijzing die u wilt bereiken.     

![Schematoewijzing](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Gegevens filteren
U brongegevens filteren om alleen de gegevens te selecteren die naar het opslagarchief voor gootsteengegevens moeten worden gekopieerd. Filteren vermindert het volume van de gegevens die naar het gootsteengegevensarchief moeten worden gekopieerd en verbetert daarom de doorvoer van de kopieerbewerking. Het biedt een flexibele manier om gegevens in een relationele database te filteren met behulp van de SQL-querytaal of bestanden in een Azure blob-map met behulp van [gegevensfabrieksfuncties en -variabelen.](data-factory-functions-variables.md)   

### <a name="filtering-of-data-in-a-database"></a>Gegevens filteren in een database
In de volgende schermafbeelding wordt `Text.Format` een `WindowStart` SQL-query weergegeven met de functie en variabele.

![Expressies valideren](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Gegevens filteren in een Azure blob-map
U variabelen in het mappad gebruiken om gegevens te kopiëren uit een map die wordt bepaald tijdens runtime op basis van [systeemvariabelen.](data-factory-functions-variables.md#data-factory-system-variables) De ondersteunde variabelen zijn: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}**, en **{custom}**. Bijvoorbeeld: invoermap/{year}/{month}/{day}.

Stel dat u invoermappen in de volgende indeling hebt:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klik **op de** knop Bladeren voor **Bestand of map,** blader naar een van deze mappen (bijvoorbeeld 2016->03->01->02) en klik op **Kiezen**. Je zou `2016/03/01/02` het in het tekstvak moeten zien. Vervang **2016** nu door **{jaar}**, **03** met **{maand}**, **01** met **{day}** en **02** met **{hour}** en druk op de **Tab-toets.** U ziet vervolgkeuzelijsten om de indeling voor deze vier variabelen te selecteren:

![Systeemvariabelen gebruiken](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Zoals in de volgende schermafbeelding wordt weergegeven, u ook een **aangepaste** variabele en eventuele [ondersteunde indelingstekenreeksen](https://msdn.microsoft.com/library/8kb3ddd4.aspx)gebruiken. Als u een map met die structuur wilt selecteren, gebruikt u eerst de knop **Bladeren.** Vervang vervolgens een waarde door **{custom}** en druk op de **Tab-toets** om het tekstvak te zien waar u de opmaaktekenreeks typen.     

![Aangepaste variabele gebruiken](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Planningsopties
U de kopieerbewerking één keer of volgens een schema uitvoeren (per uur, per dag, enzovoort). Beide opties kunnen worden gebruikt voor de breedte van de connectors in verschillende omgevingen, waaronder on-premises, cloud en lokale desktopcopy.

Een eenmalige kopieerbewerking maakt gegevensverkeer van een bron naar een bestemming slechts één keer mogelijk. Het is van toepassing op gegevens van elke grootte en elke ondersteunde indeling. Met de geplande kopie u gegevens over een voorgeschreven herhaling kopiëren. U uitgebreide instellingen (zoals opnieuw proberen, time-out en waarschuwingen) gebruiken om de geplande kopie te configureren.

![Eigenschappen plannen](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Volgende stappen
Zie [Zelfstudie: Een pijplijn maken met de wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)kopiëren voor een snelle doorloop van het gebruik van de wizard Gegevensfabriekkopiëren om een pijplijn met Kopieeractiviteit te maken.
