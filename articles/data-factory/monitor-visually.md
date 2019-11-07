---
title: Azure-gegevensfabrieken visueel bewaken
description: Meer informatie over het visueel bewaken van Azure-gegevens fabrieken
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 7b79fd9c87e97e624cce567b57c1c65fefcc151e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684636"
---
# <a name="visually-monitor-azure-data-factories"></a>Azure-gegevensfabrieken visueel bewaken
Azure Data Factory is een service voor gegevens integratie in de Cloud. U kunt het gebruiken om gegevensgestuurde werk stromen te maken in de Cloud voor het organiseren en automatiseren van gegevens verplaatsing en gegevens transformatie. Met behulp van Azure Data Factory kunt u het volgende doen:

- Gegevensgestuurde werkstromen (pijplijnen genoemd) maken en plannen die gegevens uit verschillende gegevensarchieven kunnen opnemen.
- De gegevens verwerken/transformeren met behulp van reken services zoals Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics en Azure Machine Learning.
- Uitvoergegevens publiceren naar gegevensopslaglocaties als Azure SQL Data Warehouse, waar BI-toepassingen (business intelligence) er gebruik van kunnen maken.

In deze Quick Start leert u hoe u Data Factory pijp lijnen visueel kunt bewaken zonder dat u maar één regel code hoeft te schrijven.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="monitor-data-factory-pipelines"></a>Data Factory pijp lijnen bewaken

Bewaak pijp lijn-en activiteit uitvoeringen met een eenvoudige lijst weergave interface. Alle uitvoeringen worden weer gegeven in de lokale tijd zone van de browser. Als u de tijd zone wijzigt, worden alle datum-en tijd velden uitgelijnd op het veld dat u hebt geselecteerd.  

1. Start micro soft Edge of Google Chrome. Op dit moment wordt de Data Factory gebruikers interface alleen ondersteund in deze twee webbrowsers.
2. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
3. Ga naar de Blade voor de gemaakte data factory in het Azure Portal. Selecteer de **Monitor & tegel beheren** om de Data Factory visuele bewakings ervaring te starten.

## <a name="monitor-pipeline-runs"></a>Pijplijnuitvoeringen controleren
In de lijst weergave wordt elke pijplijn uitvoering voor uw Data Factory pijp lijnen weer gegeven. Deze kolom bevat de volgende kolommen:

| **Kolom naam** | **Beschrijving** |
| --- | --- |
| Naam van pijp lijn | Naam van de pijp lijn |
| Acties | Er is één actie beschikbaar om de uitvoering van de activiteit weer te geven |
| Start uitvoeren | Begin datum en-tijd voor de pijplijn uitvoering (MM/DD/JJJJ, uu: MM: SS AM/PM) |
| Duur | Uitvoerings duur (UU: MM: SS) |
| Geactiveerd door | Hand matige trigger of geplande trigger |
| Status | **Mislukt**, **geslaagd**of **in behandeling** |
| Parameters | Para meters voor de pijp lijn run (naam/waarde-paren) |
| Fout | Fout bij uitvoering van pijp lijn (indien van toepassing) |
| Run-ID | ID van de pijplijn uitvoering |

![Lijst weergave voor het bewaken van pijplijn uitvoeringen](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Uitvoering van activiteiten controleren
In de lijst weergave worden de uitvoeringen van activiteiten weer gegeven die overeenkomen met elke pijplijn uitvoering. Als u de uitvoering van de activiteit voor elke pijplijn uitvoering wilt weer geven, selecteert u het pictogram **activiteit wordt uitgevoerd** onder de kolom **acties** . De lijst weergave bevat de volgende kolommen:

| **Kolom naam** | **Beschrijving** |
| --- | --- |
| Naam van activiteit | De naam van de activiteit in de pijp lijn |
| Type activiteit | Type activiteit, zoals **copy**, **HDInsightSpark**of **HDInsightHive** |
| Start uitvoeren | Begin datum en-tijd voor de uitvoering van de activiteit (MM/DD/JJJJ, uu: MM: SS AM/PM) |
| Duur | Uitvoerings duur (UU: MM: SS) |
| Status | **Mislukt**, **geslaagd**of **in behandeling** |
| Invoer | JSON-matrix waarmee de activiteiten invoer wordt beschreven |
| Uitvoer | JSON-matrix waarmee de uitvoer van de activiteit wordt beschreven |
| Fout | Fout bij uitvoeren van activiteit (indien van toepassing) |

![Lijst weergave voor uitvoeringen van bewakings activiteiten](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> U moet de knop **vernieuwen** bovenaan selecteren om de lijst met pijp lijn-en activiteit uitvoeringen te vernieuwen. Automatisch vernieuwen wordt momenteel niet ondersteund.

![Knop Vernieuwen](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>data factory selecteren die u wilt bewaken
Beweeg de muis aanwijzer over het pictogram **Data Factory** linksboven. Selecteer het pijl pictogram om een lijst weer te geven met Azure-abonnementen en-gegevens fabrieken die u kunt bewaken.

![Selecteer de data factory](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>De lijst weergave configureren

### <a name="apply-rich-ordering-and-filtering"></a>Uitgebreide ordening en filters toep assen

Order pijplijn wordt in DESC/ASC uitgevoerd op basis van de start tijd van de uitvoering. Filter pijplijn wordt uitgevoerd met behulp van de volgende kolommen:

| **Kolom naam** | **Beschrijving** |
| --- | --- |
| Naam van pijp lijn | Naam van de pijplijn. Opties bevatten snelle filters voor de **afgelopen 24 uur**, de **afgelopen week**en de **afgelopen 30 dagen**. Of selecteer een aangepaste datum en tijd. |
| Start uitvoeren | Begin datum en-tijd voor de pijplijn uitvoering. |
| Uitvoerings status | Filter wordt uitgevoerd op status: **geslaagd**, **mislukt**of wordt **uitgevoerd.** |

![Opties voor filteren](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Kolommen toevoegen of verwijderen
Klik met de rechter muisknop op de koptekst van de lijst weergave en kies kolommen die u wilt weer geven in de lijst weergave.

![Opties voor kolommen](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Kolom breedten aanpassen
Verg root of verklein de kolom breedten in de lijst weergave door met de muis aanwijzer over de kolomkop te bewegen.

## <a name="promote-user-properties-to-monitor"></a>Gebruikers eigenschappen promo veren om te bewaken

U kunt elke pijplijn activiteit eigenschap promo veren als een gebruikers eigenschap, zodat deze een entiteit wordt die u kunt bewaken. U kunt bijvoorbeeld de **bron** -en **doel** eigenschappen van de Kopieer activiteit in uw pijp lijn promo veren als gebruikers eigenschappen. U kunt ook **automatisch genereren** selecteren om de eigenschappen van de **bron** -en **doel** gebruiker te genereren voor een Kopieer activiteit.

![Gebruikers eigenschappen maken](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> U kunt Maxi maal vijf eigenschappen van pijplijn activiteit verhogen als gebruikers eigenschappen.

Nadat u de gebruikers eigenschappen hebt gemaakt, kunt u deze bewaken in de controle lijst weergaven. Als de bron voor de Kopieer activiteit een tabel naam is, kunt u de naam van de bron tabel bewaken als een kolom in de lijst weergave voor uitvoeringen van activiteit.

![Lijst met uitgevoerde activiteiten zonder gebruikers eigenschappen](media/monitor-visually/monitor-user-properties-image2.png)

![Kolommen voor gebruikers eigenschappen toevoegen aan de lijst met uitvoeringen van activiteit](media/monitor-visually/monitor-user-properties-image3.png)

![Lijst met uitgevoerde activiteiten met kolommen voor gebruikers eigenschappen](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Activiteiten in een pijp lijn opnieuw uitvoeren

U kunt nu activiteiten in een pijp lijn opnieuw uitvoeren. Selecteer **uitvoering van activiteit weer geven**en selecteer vervolgens de activiteit in de pijp lijn van welk punt u de pijp lijn opnieuw wilt uitvoeren.

![Uitvoeringen van activiteit bekijken](media/monitor-visually/rerun-activities-image1.png)

![Een uitvoering van een activiteit selecteren](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Geschiedenis van opnieuw uitvoeren weer geven

U kunt de geschiedenis van opnieuw uitvoeren weer geven voor alle pijplijn uitvoeringen in de lijst weergave.

![Geschiedenis weergeven](media/monitor-visually/rerun-history-image1.png)

U kunt ook de uitvoerings geschiedenis weer geven voor een bepaalde pijplijn uitvoering.

![Geschiedenis voor een pijplijn uitvoering weer geven](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt-weer gaven

Gebruik Gantt-weer gaven om uw pijp lijnen en activiteiten uitvoeringen snel te visualiseren. U kunt de Gantt-weer gave per pijp lijn of groep bekijken op aantekeningen/Tags die u hebt gemaakt in uw pijp lijnen.

![Voor beeld van een Gantt-diagram](media/monitor-visually/gantt1.png)

![Aantekeningen in Gantt-diagrammen](media/monitor-visually/gantt2.png)

De lengte van de balk informeert de duur van de pijp lijn. U kunt ook de balk selecteren om meer details weer te geven.

![Duur Gantt-diagram](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Rond leidingen
Selecteer het **informatie** pictogram linksonder. Selecteer vervolgens rond **leidingen** voor stapsgewijze instructies voor het bewaken van de uitvoering van uw pijp lijn en activiteit.

![Rond leidingen](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Feedback
Selecteer het pictogram **feedback** om ons feedback te geven over de verschillende functies of eventuele problemen die u mogelijk ondervindt.

![Feedback](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Waarschuwingen

U kunt waarschuwingen activeren over ondersteunde metrische gegevens in Data Factory. Selecteer > waarschuwingen **bewaken** **& metrische gegevens** op de pagina Data Factory bewaking om aan de slag te gaan.

![Pagina Data Factory-monitor](media/monitor-visually/alerts01.png)

Bekijk de volgende video voor een inleiding en demonstratie van zeven minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Waarschuwingen maken

1.  Selecteer **nieuwe waarschuwings regel** om een nieuwe waarschuwing te maken.

    ![Knop nieuwe waarschuwings regel](media/monitor-visually/alerts02.png)

1.  Geef de naam van de regel op en selecteer de ernst van de waarschuwing.

    ![Vakken voor naam en ernst van de regel](media/monitor-visually/alerts03.png)

1.  Selecteer de waarschuwings criteria.

    ![Vak voor doel criteria](media/monitor-visually/alerts04.png)

    ![Lijst met criteria](media/monitor-visually/alerts05.png)

1.  De waarschuwings logica configureren. U kunt een waarschuwing maken voor de geselecteerde metrische gegevens voor alle pijp lijnen en bijbehorende activiteiten. U kunt ook een bepaald activiteitstype, de naam van de activiteit, de naam van de pijp lijn of het type fout selecteren.

    ![Opties voor het configureren van waarschuwings logica](media/monitor-visually/alerts06.png)

1.  E-mail-, SMS-, push-en voicemail meldingen configureren voor de waarschuwing. Een actie groep maken of een bestaande kiezen, voor de waarschuwings meldingen.

    ![Opties voor het configureren van meldingen](media/monitor-visually/alerts07.png)

    ![Opties voor het toevoegen van een melding](media/monitor-visually/alerts08.png)

1.  De waarschuwings regel maken.

    ![Opties voor het maken van een waarschuwings regel](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het bewaken en beheren van pijp lijnen het [programmatische artikel pijp lijnen bewaken en beheren](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) .
