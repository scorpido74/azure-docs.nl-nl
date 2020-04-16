---
title: Azure Data Factory visueel bewaken
description: Meer informatie over het visueel bewaken van Azure-gegevensfabrieken
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 40b1b8d040c4b3ea76372920f88551fba35c5f26
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419439"
---
# <a name="visually-monitor-azure-data-factory"></a>Azure Data Factory visueel bewaken

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Zodra u een pijplijn hebt gemaakt en gepubliceerd in Azure Data Factory, u deze koppelen aan een trigger of handmatig een ad-hocrun starten. U al uw pijplijnuitvoeringen native controleren in de gebruikerservaring van Azure Data Factory. Als u de bewakingservaring wilt openen, selecteert u de tegel **Monitor & Beheren** in het gegevensfabrieksblad van de [Azure-portal.](https://portal.azure.com/) Als u al in de ADF-UX zit, klikt u op het pictogram **Monitor** op de linkerzijbalk.

Alle gegevensfabrieksuitvoeringen worden weergegeven in de lokale tijdzone van de browser. Als u de tijdzone wijzigt, worden alle datum-/tijdvelden uitgelijnd op de zone die u hebt geselecteerd.

## <a name="monitor-pipeline-runs"></a>Pijplijnuitvoeringen controleren

De standaardbewakingsweergave is een lijst met pijplijnuitvoeringen in de geselecteerde periode. De volgende kolommen worden weergegeven:

| **Kolomnaam** | **Beschrijving** |
| --- | --- |
| Naam van pijplijn | Naam van de pijplijn |
| Acties | Pictogrammen waarmee u activiteitsgegevens bekijken, de pijplijn annuleren of opnieuw uitvoeren |
| Start uitvoeren | Begindatum en -tijd voor de pijplijnuitvoering (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duur | Duur uitvoeren (HH:MM:SS) |
| Geactiveerd door | De naam van de trigger waarmee de pijplijn is gestart |
| Status | **Mislukt**, **Geslaagd**, **In uitvoering,** **Geannuleerd**of In **de wachtrij** |
| Aantekeningen | Filterbare tags die zijn gekoppeld aan een pijplijn  |
| Parameters | Parameters voor de pijplijnrun (naam/waardeparen) |
| Fout | Als de pijplijn is mislukt, is de fout met uitvoeren |
| Uitvoerings-id | ID van de pijplijnrun |

![Lijstweergave voor het bewaken van pijplijnuitvoeringen](media/monitor-visually/pipeline-runs.png)

U moet handmatig de knop **Vernieuwen** selecteren om de lijst met pijplijn- en activiteitsuitvoeringen te vernieuwen. Autorefresh wordt momenteel niet ondersteund.

![Knop Vernieuwen](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Uitvoering van activiteiten controleren

Als u activiteitsuitvoeringen voor elke pijplijnrun wilt weergeven, selecteert u het pictogram **Activiteitsactiviteiten weergeven** onder de kolom **Acties.** In de lijstweergave worden activiteitsruns weergegeven die overeenkomen met elke pijplijnuitvoering.

| **Kolomnaam** | **Beschrijving** |
| --- | --- |
| Activiteitsnaam | Naam van de activiteit in de pijplijn |
| Type activiteit | Type activiteit, zoals **Kopiëren,** **UitvoerenGegevensstroom**of **AzureMLExecutePipeline** |
| Acties | Pictogrammen waarmee u JSON-invoerinformatie, JSON-uitvoerinformatie of gedetailleerde activiteitsspecifieke monitoringervaringen zien | 
| Start uitvoeren | Begindatum en -tijd voor de activiteitsrun (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duur | Duur uitvoeren (HH:MM:SS) |
| Status | **Mislukt,** **geslaagd**, **in uitvoering**of **geannuleerd** |
| Runtime van integratie | Op welke Integratieruntime de activiteit is uitgevoerd |
| Gebruikerseigenschappen | Door de gebruiker gedefinieerde eigenschappen van de activiteit |
| Fout | Als de activiteit is mislukt, is de fout met uitvoeren |
| Uitvoerings-id | ID van de activiteitsrun |

![Lijstweergave voor het opvoeren van bewakingsactiviteiten](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Gebruikerseigenschappen promoten om te controleren

Promoot een eigenschap van pijplijnactiviteit als eigenschap van een gebruiker, zodat deze een entiteit wordt die u controleert. U bijvoorbeeld de **eigenschappen Bron** en **Bestemming** van de kopieeractiviteit in uw pijplijn promoten als gebruikerseigenschappen. Selecteer **Automatisch genereren** om de gebruikerseigenschappen **Bron** en **Bestemming** voor een kopieeractiviteit te genereren.

![Gebruikerseigenschappen maken](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> U maximaal vijf eigenschappen van pijplijnactiviteit promoten als gebruikerseigenschappen.

Nadat u de gebruikerseigenschappen hebt gemaakt, u deze controleren in de weergavevan de controlelijst. Als de bron voor de kopieeractiviteit een tabelnaam is, u de naam van de brontabel controleren als een kolom in de lijstweergave voor activiteitsuitvoeringen.

![Lijst met activiteitsuitvoeringen zonder gebruikerseigenschappen](media/monitor-visually/monitor-user-properties-image2.png)

![Kolommen voor gebruikerseigenschappen toevoegen aan de lijst activiteitsuitvoeringen](media/monitor-visually/monitor-user-properties-image3.png)

![Lijst met activiteitsuitvoeringen met kolommen voor gebruikerseigenschappen](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>De lijstweergave configureren

### <a name="order-and-filter"></a>Bestellen en filteren

Schakel in of pijplijnuitvoeringen aflopend of oplopend zijn op basis van de begintijd van de uitvoering. Filterpijplijn wordt uitgevoerd met de volgende kolommen:

| **Kolomnaam** | **Beschrijving** |
| --- | --- |
| Naam van pijplijn | Filter op de naam van de pijplijn. |
| Start uitvoeren |  Bepaal het tijdsbereik van de weergegeven pijplijnuitvoeringen. Opties omvatten snelle filters voor **de afgelopen 24 uur,** **vorige week**en **laatste 30 dagen** of om een aangepaste datum en tijd te selecteren. |
| Status uitvoeren | Filter wordt uitgevoerd op status: **Geslaagd**, **Mislukt**, **Wachtrij**, **Geannuleerd**of **In uitvoering**. |
| Aantekeningen | Filteren op tags die op elke pijplijn zijn toegepast |
| Wordt uitgevoerd | Filteren of u opnieuw gelopen pijplijnen wilt zien |

![Opties voor filteren](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Kolommen toevoegen of verwijderen
Klik met de rechtermuisknop op de koptekst van de lijstweergave en kies kolommen die u in de lijstweergave wilt weergeven.

![Opties voor kolommen](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Kolombreedtes aanpassen
De kolombreedtes in de lijstweergave vergroten en verkleinen door over de kolomkop te zweven.

## <a name="rerun-activities-inside-a-pipeline"></a>Activiteiten binnen een pijplijn opnieuw uitvoeren

U activiteiten in een pijplijn opnieuw uitvoeren. Selecteer **Activiteitsuitvoeringen weergeven**en selecteer vervolgens de activiteit in de pijplijn vanaf welk punt u de pijplijn opnieuw wilt uitvoeren.

![Uitvoeringen van activiteit bekijken](media/monitor-visually/rerun-activities-image1.png)

![Een activiteitsrun selecteren](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Opnieuw uitvoeren van mislukte activiteit

Als een activiteit mislukt, een keer uitvalt of wordt geannuleerd, u de pijplijn uit die mislukte activiteit opnieuw uitvoeren door **Opnieuw uit mislukte activiteit te**selecteren.

![Mislukte activiteit opnieuw uitvoeren](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Geschiedenis voor opnieuw uitvoeren weergeven

U de herhalingsgeschiedenis voor alle pijplijnuitvoeringen in de lijstweergave bekijken.

![Geschiedenis weergeven](media/monitor-visually/rerun-history-image1.png)

U ook de loopgeschiedenis voor een bepaalde pijplijnrun weergeven.

![Geschiedenis weergeven voor een pijplijnrun](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt-weergaven

Gebruik Gantt-weergaven om uw pijplijnen en activiteitensnel te visualiseren.

![Voorbeeld van een Gantt-diagram](media/monitor-visually/gantt1.png)

U de Gantt-weergave per pijplijn of groep bekijken door aantekeningen/tags die u op uw pijplijnen hebt gemaakt.

![Gantt-diagramannotaties](media/monitor-visually/gantt2.png)

De lengte van de balk geeft de duur van de pijplijn aan. U de balk ook selecteren om meer details te zien.

![Gantt-diagramduur](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Rondleidingen
Selecteer het pictogram **Informatie** linksonder. Selecteer vervolgens **Rondleidingen** om stapsgewijze instructies te krijgen over het bewaken van uw pijplijn en activiteiten.

![Rondleidingen](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Waarschuwingen

U waarschuwingen inzamelen voor ondersteunde statistieken in Data Factory. Selecteer > **Monitorwaarschuwingen & Statistieken** op de bewakingspagina gegevensfabriek om aan de slag te gaan. **Monitor**

![Pagina Monitor gegevensfabriek](media/monitor-visually/alerts01.png)

Voor een zeven minuten durende introductie en demonstratie van deze functie, bekijk de volgende video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Waarschuwingen maken

1.  Selecteer **Nieuwe waarschuwingsregel** om een nieuwe waarschuwing te maken.

    ![Knop Nieuwe waarschuwingsregel](media/monitor-visually/alerts02.png)

1.  Geef de regelnaam op en selecteer de waarschuwingsernst.

    ![Vakken voor regelnaam en ernst](media/monitor-visually/alerts03.png)

1.  Selecteer de waarschuwingscriteria.

    ![Vak voor doelcriteria](media/monitor-visually/alerts04.png)

    ![Lijst van criteria](media/monitor-visually/alerts05.png)

1.  Configureer de waarschuwingslogica. U een waarschuwing maken voor de geselecteerde statistiek voor alle pijplijnen en bijbehorende activiteiten. U ook een bepaald activiteitstype, activiteitsnaam, pijplijnnaam of fouttype selecteren.

    ![Opties voor het configureren van waarschuwingslogica](media/monitor-visually/alerts06.png)

1.  Configureer e-mail-, sms-, push- en spraakmeldingen voor de waarschuwing. Maak een actiegroep of kies een bestaande groep voor de waarschuwingsmeldingen.

    ![Opties voor het configureren van meldingen](media/monitor-visually/alerts07.png)

    ![Opties voor het toevoegen van een melding](media/monitor-visually/alerts08.png)

1.  Maak de waarschuwingsregel.

    ![Opties voor het maken van een waarschuwingsregel](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Volgende stappen

Zie het artikel Pijplijnen controleren en beheren voor meer informatie over het bewaken en beheren van [pijplijnen.](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)
