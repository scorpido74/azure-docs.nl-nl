---
title: Wizard Azure Copy Data Factory
description: Meer informatie over Data Factory het gebruik van de wizard Azure kopiëren om gegevens te kopiëren van ondersteunde gegevens bronnen naar Sinks.
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
ms.openlocfilehash: 2dfb5876922fd53c372afe82ecdfa843179fb135
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89439007"
---
# <a name="azure-data-factory-copy-wizard"></a>Wizard Azure Data Factory kopiëren
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. 

De wizard voor het Azure Data Factory kopiëren vereenvoudigt het proces van het opnemen van gegevens. Dit is meestal een eerste stap in een end-to-end gegevens integratie scenario. Wanneer u de wizard kopiëren van Azure Data Factoryt, hoeft u geen JSON-definities te begrijpen voor gekoppelde services, gegevens sets en pijp lijnen. De wizard maakt automatisch een pijp lijn om gegevens van de geselecteerde gegevens bron naar de geselecteerde bestemming te kopiëren. Daarnaast helpt de wizard kopiëren u bij het valideren van de gegevens die op het moment van ontwerpen worden opgenomen. Dit bespaart tijd, vooral wanneer u gegevens voor de eerste keer opneemt vanuit de gegevens bron. Als u de wizard kopiëren wilt starten, klikt u op de tegel **gegevens kopiëren** op de start pagina van uw Data Factory.

![De wizard Kopiëren](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Ontworpen voor big data
Met deze wizard kunt u eenvoudig gegevens van een groot aantal bronnen naar bestemmingen verplaatsen binnen enkele minuten. Nadat u de wizard hebt door lopen, wordt er automatisch een pijp lijn met een Kopieer activiteit voor u gemaakt, samen met afhankelijke Data Factory entiteiten (gekoppelde services en gegevens sets). Er zijn geen extra stappen vereist om de pijp lijn te maken.   

![Gegevensbron selecteren](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Voor stapsgewijze instructies voor het maken van een voorbeeld pijplijn voor het kopiëren van gegevens van een Azure-Blob naar een Azure SQL Database tabel, raadpleegt u de [zelf studie](data-factory-copy-data-wizard-tutorial.md)over het kopiëren van de wizard.
>
>

De wizard is ontworpen met big data van het begin, met ondersteuning voor diverse gegevens en object typen. U kunt Data Factory pijp lijnen ontwerpen waarmee honderden mappen, bestanden of tabellen worden verplaatst. De wizard ondersteunt automatische gegevens weergave, schema-opname en-toewijzing en gegevens filtering.

## <a name="automatic-data-preview"></a>Automatische gegevens voorbeeld
U kunt een voor beeld van een deel van de gegevens uit de geselecteerde gegevens bron weer geven om te controleren of de gegevens die u wilt kopiëren, moeten worden gekopieerd. Als de bron gegevens zich in een tekst bestand bevinden, parseert de wizard kopiëren ook het tekst bestand om de rij-en kolom scheidings tekens en het schema automatisch te leren.

![Pagina Instellingen bestandsindelingen](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schema's vastleggen en toewijzen
Het schema van invoer gegevens komt mogelijk niet overeen met het schema van de uitvoer gegevens in sommige gevallen. In dit scenario moet u kolommen uit het bron schema toewijzen aan kolommen uit het doel schema.

> [!TIP]
> Bij het kopiëren van gegevens uit SQL Server of Azure SQL Database naar Azure Synapse Analytics (voorheen SQL Data Warehouse), als de tabel niet bestaat in het doel archief, Data Factory ondersteuning voor het automatisch maken van tabellen met het schema van de bron. Meer informatie over [het verplaatsen van gegevens van en naar Azure Synapse Analytics met behulp van Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Gebruik een vervolg keuzelijst om een kolom uit het bron schema te selecteren die u wilt toewijzen aan een kolom in het doel schema. De wizard kopiëren probeert het patroon voor de kolom toewijzing te begrijpen. Hiermee wordt hetzelfde patroon op de rest van de kolommen toegepast, zodat u niet elke kolom afzonderlijk hoeft te selecteren om de schema toewijzing te volt ooien. Als u wilt, kunt u deze toewijzingen negeren door de kolommen één voor één toe te wijzen met behulp van de vervolg keuzelijsten. Het patroon wordt nauw keuriger naarmate u meer kolommen toewijst. Met de wizard kopiëren wordt het patroon continu bijgewerkt en uiteindelijk het juiste patroon bereikt voor de kolom toewijzing die u wilt bereiken.     

![Schema toewijzing](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Gegevens filteren
U kunt bron gegevens filteren om alleen de gegevens te selecteren die moeten worden gekopieerd naar de Sink-gegevens opslag. Filteren vermindert het volume van de gegevens die moeten worden gekopieerd naar de Sink-gegevens opslag en verhoogt daarom de door Voer van de Kopieer bewerking. Het biedt een flexibele manier om gegevens in een relationele data base te filteren met behulp van de SQL-query taal of bestanden in een Azure Blob-map door gebruik te maken van [Data Factory-functies en-variabelen](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filteren van gegevens in een Data Base
De volgende scherm afbeelding toont een SQL-query met behulp van de `Text.Format` functie en de `WindowStart` variabele.

![Expressies valideren](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filteren van gegevens in een Azure Blob-map
U kunt variabelen in het mappad gebruiken om gegevens te kopiëren uit een map die wordt bepaald tijdens runtime op basis van [systeem variabelen](data-factory-functions-variables.md#data-factory-system-variables). De ondersteunde variabelen zijn: **{Year}**, **{Month}**, **{Day}**, **{Hour}**, **{Minute}** en **{Custom}**. Bijvoorbeeld: inputfolder/{year}/{month}/{Day}.

Stel dat u een invoer mappen hebt met de volgende indeling:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klik op de knop **Bladeren** voor **bestand of map**, blader naar een van deze mappen (bijvoorbeeld 2016->03->01->02) en klik op **kiezen**. U ziet `2016/03/01/02` in het tekstvak. Vervang nu **2016** door **{Year}**, **03** met **{Month}**, **01** met **{Day}** en **02** met **{Hour}** en druk op de **Tab** -toets. Er moeten vervolg keuzelijsten worden weer gegeven om de indeling voor deze vier variabelen te selecteren:

![Systeem variabelen gebruiken](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Zoals in de volgende scherm afbeelding wordt weer gegeven, kunt u ook een **aangepaste** variabele en [ondersteunde opmaak teken reeksen](https://msdn.microsoft.com/library/8kb3ddd4.aspx)gebruiken. Als u een map met die structuur wilt selecteren, gebruikt u eerst de knop **Bladeren** . Vervang vervolgens een waarde door **{Custom}** en druk op de **Tab** -toets om het tekstvak weer te geven waarin u de notatie teken reeks kunt invoeren.     

![Aangepaste variabele gebruiken](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Plannings opties
U kunt de Kopieer bewerking eenmaal of volgens een schema uitvoeren (elk uur, dagelijks, enzovoort). Beide opties kunnen worden gebruikt voor de breedte van de connectors in omgevingen, waaronder on-premises, Cloud en lokale bureaublad kopieën.

Een eenmalige Kopieer bewerking maakt het verplaatsen van gegevens van een bron naar een bestemming slechts één keer mogelijk. Dit is van toepassing op gegevens van elke grootte en een ondersteunde indeling. Met de geplande kopie kunt u gegevens kopiëren naar een voorgeschreven terugkeer patroon. U kunt uitgebreide instellingen (zoals nieuwe poging, time-out en waarschuwingen) gebruiken om de geplande kopie te configureren.

![Plannings eigenschappen](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Volgende stappen
Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle beschrijving van het gebruik van de wizard voor het kopiëren van Data Factory om een pijp lijn te maken met de Kopieer activiteit.
