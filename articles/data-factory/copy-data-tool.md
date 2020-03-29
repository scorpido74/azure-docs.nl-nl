---
title: Gegevenshulpprogramma Kopiëren Azure Data Factory
description: Bevat informatie over het hulpprogramma Gegevens kopiëren in de gebruikersinterface van Azure Data Factory
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: a6de5c28115d3a451256cc43d26552c269ba245a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927502"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Hulpprogramma voor gegevens kopiëren in Azure Data Factory
De azure data factory copy data tool vereenvoudigt en optimaliseert het proces van het opnemen van gegevens in een data lake, wat meestal een eerste stap is in een end-to-end data-integratiescenario.  Het bespaart tijd, vooral wanneer u Azure Data Factory gebruikt om voor het eerst gegevens uit een gegevensbron in te nemen. Enkele van de voordelen van het gebruik van deze tool zijn:

- Wanneer u het hulpprogramma Voor kopiëren van Azure Data Factory gebruikt, hebt u geen informatie nodig over definities van Gegevensfabriek voor gekoppelde services, gegevenssets, pijplijnen, activiteiten en triggers. 
- De stroom van Copy Data tool is intuïtief voor het laden van gegevens in een data lake. De tool maakt automatisch alle benodigde Data Factory-bronnen om gegevens uit het geselecteerde brongegevensarchief naar het geselecteerde doel-/sink-gegevensarchief te kopiëren. 
- Met het hulpprogramma Gegevens kopiëren u de gegevens valideren die worden ingenomen op het moment van ontwerpen, zodat u eventuele fouten in het begin zelf voorkomen.
- Als u complexe bedrijfslogica moet implementeren om gegevens in een gegevensmeer te laden, u de gegevensfabriekbronnen die zijn gemaakt door het hulpprogramma Gegevens kopiëren nog steeds bewerken met behulp van de auteur per activiteit in de gebruikersinterface van datafabriek. 

In de volgende tabel vindt u informatie over het gebruik van het gereedschap Gegevens kopiëren versus het ontwerpen per activiteit in de gebruikersinterface van gegevensfabriek: 

| Hulpprogramma voor het kopiëren van gegevens | Per activiteit (Activiteit kopiëren) ontwerpen |
| -------------- | -------------------------------------- |
| U wilt eenvoudig een taak voor het laden van gegevens bouwen zonder meer te weten te komen over azure data factory-entiteiten (gekoppelde services, gegevenssets, pijplijnen, enz.) | U wilt complexe en flexibele logica implementeren voor het laden van gegevens in lake. |
| U wilt snel een groot aantal gegevensartefacten in een gegevensmeer laden. | U wilt Kopieeractiviteiten met volgende activiteiten voor het reinigen of verwerken van gegevens ketenen. |

Als u het gereedschap Gegevens kopiëren wilt starten, klikt u op de tegel **Gegevens kopiëren** op de startpagina van uw gegevensfabriek.

![Pagina aan de slag - koppeling naar hulpprogramma Gegevens kopiëren](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuïtieve stroom voor het laden van gegevens in een datalake
Met deze tool u eenvoudig gegevens van een breed scala aan bronnen naar bestemmingen verplaatsen in enkele minuten met een intuïtieve stroom:  

1. Instellingen configureren voor de **bron**.
2. Instellingen voor de **bestemming**configureren . 
3. Configureer **geavanceerde instellingen** voor de kopieerbewerking, zoals kolomtoewijzing, prestatie-instellingen en fouttolerantieinstellingen. 
4. Geef een **planning** op voor de taak voor het laden van gegevens. 
5. Bekijk **een overzicht** van de entiteiten van Data Factory die moeten worden gemaakt. 
6. **Bewerk** de pijplijn om de instellingen voor de kopieeractiviteit zo nodig bij te werken. 

   De tool is ontworpen met big data in het achterhoofd vanaf het begin, met ondersteuning voor diverse gegevens- en objecttypen. U het gebruiken om honderden mappen, bestanden of tabellen te verplaatsen. De tool ondersteunt automatische gegevensvoorbeeld, schema-vastleggen en automatische toewijzing, en gegevensfiltering ook.

![Hulpprogramma voor het kopiëren van gegevens](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatische gegevensvoorbeeld
U een voorbeeld van een voorbeeld van een voorbeeld van een deel van de gegevens uit het geselecteerde brongegevensarchief bekijken, waarmee u de gegevens die worden gekopieerd, valideren. Als de brongegevens zich in een tekstbestand bevindt, wordt het tekstbestand bovendien door het gereedschap Gegevens kopiëren ontlijnt om automatisch de scheidingstekens en het schema van de rij- en kolomscheidingen te detecteren.

![Bestandsinstellingen](./media/copy-data-tool/file-format-settings.png)

Na de detectie:

![Gedetecteerde bestandsinstellingen en voorbeeld](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Schemavastleggen en automatisch toewijzen
Het schema van de gegevensbron is in veel gevallen mogelijk niet hetzelfde als het schema van de bestemming van gegevens. In dit scenario moet u kolommen van het bronschema toewijzen aan kolommen uit het doelschema.

Het gereedschap Gegevens kopiëren controleert en leert uw gedrag wanneer u kolommen in kaart brengt tussen bron- en doelopslag. Nadat u een of enkele kolommen uit het brongegevensarchief hebt gekozen en deze hebt toegewezen aan het doelschema, begint het gereedschap Gegevens kopiëren het patroon te analyseren voor kolomparen die u van beide zijden hebt gekozen. Vervolgens wordt hetzelfde patroon op de rest van de kolommen gebruikt. Daarom ziet u dat alle kolommen zijn toegewezen aan de bestemming op een manier die u wilt net na een aantal klikken.  Als u niet tevreden bent met de keuze van kolomtoewijzing die wordt geleverd door het hulpprogramma Gegevens kopiëren, u deze negeren en doorgaan met het handmatig in kaart brengen van de kolommen. Ondertussen leert en actualiseert de tool Gegevens kopiëren voortdurend het patroon en bereikt uiteindelijk het juiste patroon voor de kolomtoewijzing die u wilt bereiken. 

> [!NOTE]
> Wanneer u gegevens uit SQL Server of Azure SQL Database kopieert naar Azure SQL Data Warehouse, als de tabel niet bestaat in het doelarchief, ondersteunt het hulpprogramma Gegevens kopiëren het automatisch maken van de tabel met behulp van het bronschema. 

## <a name="filter-data"></a>Gegevens filteren
U brongegevens filteren om alleen de gegevens te selecteren die naar het opslagarchief voor gootsteengegevens moeten worden gekopieerd. Filteren vermindert het volume van de gegevens die naar het gootsteengegevensarchief moeten worden gekopieerd en verbetert daarom de doorvoer van de kopieerbewerking. Copy Data tool biedt een flexibele manier om gegevens in een relationele database te filteren met behulp van de SQL-querytaal of bestanden in een Azure blob-map. 

### <a name="filter-data-in-a-database"></a>Gegevens in een database filteren
In de volgende schermafbeelding wordt een SQL-query weergegeven om de gegevens te filteren.

![Gegevens in een database filteren](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Gegevens filteren in een Azure blob-map
U variabelen in het mappad gebruiken om gegevens uit een map te kopiëren. De ondersteunde variabelen zijn: **{year}**, **{month}**, **{day}**, **{hour}**, en **{minute}**. Bijvoorbeeld: invoermap/{year}/{month}/{day}. 

Stel dat u invoermappen in de volgende indeling hebt: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klik **op de** knop Bladeren voor **Bestand of map,** blader naar een van deze mappen (bijvoorbeeld 2016->03->01->02) en klik op **Kiezen**. U ziet 2016/03/01/02 in het tekstvak. 

Vervang **2016** vervolgens door **{jaar}**, **03** met **{maand}**, **01** met **{day}** en **02** met **{hour}** en druk op de **Tab-toets.** U ziet vervolgkeuzelijsten om de indeling voor deze vier variabelen te selecteren:

![Bestand of map filteren](./media/copy-data-tool/filter-file-or-folder.png)

Met het gereedschap Gegevens kopiëren worden parameters gegenereerd met expressies, functies en systeemvariabelen die kunnen worden gebruikt om {year}, {month}, {day}, {hour} en {minute} weer te geven bij het maken van pijplijn.

## <a name="scheduling-options"></a>Planningsopties
U de kopieerbewerking één keer of volgens een schema uitvoeren (per uur, per dag, enzovoort). Deze opties kunnen worden gebruikt voor de connectors in verschillende omgevingen, waaronder on-premises, cloud en lokaal bureaublad. 

Een eenmalige kopieerbewerking maakt gegevensverkeer van een bron naar een bestemming slechts één keer mogelijk. Het is van toepassing op gegevens van elke grootte en elke ondersteunde indeling. Met de geplande kopie u gegevens kopiëren over een herhaling die u opgeeft. U uitgebreide instellingen (zoals opnieuw proberen, time-out en waarschuwingen) gebruiken om de geplande kopie te configureren.

![Planningsopties](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Volgende stappen
Probeer deze zelfstudies die het gereedschap Gegevens kopiëren gebruiken:

- [Snelstart: een gegevensfabriek maken met het gereedschap Gegevens kopiëren](quickstart-create-data-factory-copy-data-tool.md)
- [Zelfstudie: gegevens kopiëren in Azure met het gereedschap Gegevens kopiëren](tutorial-copy-data-tool.md) 
- [Zelfstudie: on-premises gegevens kopiëren naar Azure met het hulpprogramma Gegevens kopiëren](tutorial-hybrid-copy-data-tool.md)
