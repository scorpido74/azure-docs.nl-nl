---
title: Taken maken en uitvoeren in uw Azure IoT Central-toepassing | Microsoft Docs
description: Met Azure IoT Central-taken kunnen beheer mogelijkheden voor bulk apparaten worden toegepast, zoals het bijwerken van eigenschappen of het uitvoeren van een opdracht.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797833"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Een taak maken en uitvoeren in uw Azure IoT Central-toepassing

U kunt Microsoft Azure IoT Central gebruiken om uw verbonden apparaten op schaal te beheren via taken. Met taken kunt u bulksgewijs updates uitvoeren op de apparaateigenschappen en de opdrachten uit te voeren. In dit artikel wordt beschreven hoe u aan de slag gaat met het gebruik van taken in uw eigen toepassing.

## <a name="create-and-run-a-job"></a>Een taak maken en uitvoeren

In deze sectie wordt beschreven hoe u een taak maakt en uitvoert in de vorm van het instellen van de licht drempelwaarde voor een groep logistiek-gateway apparaten.

1. Selecteer **taken**in het linkerdeel venster.

2. Selecteer **+ Nieuw**.

   ![Scherm opname van selecties voor het maken van een taak.](./media/howto-run-a-job/create-new-job.png)

3. Voer een naam en beschrijving in om de taak te identificeren die u wilt maken.

4. Selecteer de doel apparaten groep waarop u de taak wilt Toep assen. In de sectie **samen vatting** kunt u zien hoeveel apparaten de taak configuratie van toepassing heeft.

5. Kies **Cloud eigenschap**, **eigenschap**of **opdracht** als het type taak dat moet worden geconfigureerd. 

   Als u een configuratie van een **Eigenschappen** taak wilt instellen, selecteert u een eigenschap en stelt u de nieuwe waarde in. Als u de configuratie van een **opdracht** taak wilt instellen, kiest u de opdracht die u wilt uitvoeren. Een eigenschaps taak kan meerdere eigenschappen instellen.

   ![Scherm afbeelding met selecties voor het maken van een eigenschaps taak met de naam instellen licht drempel.](./media/howto-run-a-job/configure-job.png)

6. Selecteer **uitvoeren** of **Opslaan**. De taak wordt nu weer gegeven op de pagina met hoofd **taken** . Op deze pagina ziet u de taak die momenteel wordt uitgevoerd en de geschiedenis van eerder uitgevoerde of opgeslagen taken. U kunt de opgeslagen taak op elk gewenst moment opnieuw openen om deze te bewerken of om deze uit te voeren.

   ![Scherm opname waarin de naam, de status en de beschrijving van een gemaakte taak worden weer gegeven.](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > U kunt de geschiedenis van 30 dagen voor uw eerder uitgevoerde taken bekijken.

7. Selecteer de opgeslagen taak en voer deze uit door de knop **uitvoeren** te selecteren. 

   Het dialoog venster **uw taak uitvoeren?** wordt weer gegeven. Bevestig door de knop **taak uitvoeren** te selecteren. 

   ![Scherm opname van het dialoog venster waarin wordt bevestigd dat u een taak wilt uitvoeren.](./media/howto-run-a-job/run-job.png)

8. Een taak doorloopt de fasen van in behandeling, actief en voltooid. De details van de taak uitvoering bevatten resultaat waarden, Details over de duur en een raster van een lijst met apparaten. 

   In dit overzicht kunt u ook het **resultaten logboek** selecteren om een CSV-bestand van uw taak details te downloaden, met inbegrip van de apparaten en hun status waarden. Deze informatie kan nuttig zijn bij het oplossen van problemen.

   ![Scherm opname met de Apparaatstatus.](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Taken beheren

Als u een actieve taak wilt stoppen, opent u deze en selecteert u **stoppen**. De taak status wordt gewijzigd om aan te geven dat de taak is gestopt. In de sectie **samen vatting** ziet u welke apparaten zijn voltooid, zijn mislukt of nog in behandeling zijn.

![Scherm afbeelding waarin een actieve taak en de knop voor het stoppen van een taak worden weer gegeven.](./media/howto-run-a-job/manage-job.png)

Nadat een taak is gestopt, kunt u **door gaan** met het uitvoeren van de taak hervatten selecteren. De taak status wordt gewijzigd om aan te geven dat de taak nu opnieuw wordt uitgevoerd. De sectie **samen vatting** blijft bijgewerkt met de laatste voortgang.

![Scherm afbeelding met een gestopte taak en de knop voor het voortzetten van een taak.](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Een taak kopiëren

Als u een van uw bestaande taken wilt kopiëren, selecteert u deze op de pagina **taken** en selecteert u **taak Details**. De pagina **taak Details** wordt weer gegeven. 

![Scherm opname van de pagina voor taak Details.](./media/howto-run-a-job/job-details.png)

Selecteer **Kopiëren**.

![Scherm opname van de knop kopiëren.](./media/howto-run-a-job/job-details-copy.png)

Een kopie van de taak configuratie wordt geopend, zodat u deze kunt bewerken en **kopiëren** wordt toegevoegd aan de taak naam. U kunt de nieuwe taak opslaan of uitvoeren.

![Scherm afbeelding met een kopie van de taak configuratie.](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Taakstatus weergeven

Nadat een taak is gemaakt, wordt de kolom **status** bijgewerkt met het laatste status bericht voor de taak. De volgende tabel bevat de mogelijke taak status waarden:

| Statusbericht       | Status betekenis                                          |
| -------------------- | ------------------------------------------------------- |
| Voltooid            | Deze taak is uitgevoerd op alle apparaten.              |
| Mislukt               | Deze taak is mislukt en niet volledig uitgevoerd op apparaten.  |
| In behandeling              | Deze taak is nog niet gestart op apparaten.         |
| Wordt uitgevoerd              | Deze taak wordt momenteel uitgevoerd op apparaten.             |
| Gestopt              | Een gebruiker heeft deze taak hand matig gestopt.           |

Het status bericht wordt gevolgd door een overzicht van de apparaten in de taak. De volgende tabel geeft een lijst van mogelijke status waarden van apparaten:

| Statusbericht       | Status betekenis                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Geslaagd            | Het aantal apparaten waarop de taak is uitgevoerd.       |
| Mislukt               | Het aantal apparaten waarop de taak niet kan worden uitgevoerd.       |

Als u de status van de taak en alle betrokken apparaten wilt weer geven, opent u de taak. Naast de naam van elk apparaat ziet u een van de volgende status berichten:

| Statusbericht       | Status betekenis                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Voltooid            | De taak is uitgevoerd op dit apparaat.                                     |
| Mislukt               | De taak kan niet worden uitgevoerd op dit apparaat. In het fout bericht wordt meer informatie weer gegeven.  |
| In behandeling              | De taak is nog niet uitgevoerd op dit apparaat.                                   |

Als u een CSV-bestand wilt downloaden dat de taak Details bevat en de lijst met apparaten en hun status waarden, selecteert u **downloaden**.

## <a name="filter-the-device-list"></a>De apparatenlijst filteren

U kunt de apparaten lijst op de pagina met **taak Details** filteren door het filter pictogram te selecteren. U kunt filteren op de **apparaat-id** of het **status** veld.

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Scherm afbeelding met selecties voor het filteren van een lijst met apparaten.":::

## <a name="customize-columns-in-the-device-list"></a>Kolommen aanpassen in de lijst met apparaten

U kunt extra kolommen kiezen om weer te geven in de lijst met apparaten door het pictogram kolom opties te selecteren.

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Scherm afbeelding met het pictogram voor kolom opties.":::

In een dialoog venster kunt u de kolommen kiezen die u wilt weer geven in de lijst met apparaten. Selecteer de kolommen die u wilt weer geven, selecteer de pijl-rechts en selecteer vervolgens **OK**. Als u alle beschik bare kolommen wilt selecteren, **selecteert u alles selecteren**.

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Scherm afbeelding van het dialoog venster voor het selecteren van kolommen die moeten worden weer gegeven.":::

De geselecteerde kolommen worden weer gegeven in de lijst met apparaten.

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Scherm opname van de geselecteerde kolommen in de lijst met apparaten.":::

Geselecteerde kolommen blijven behouden in een gebruikers sessie of tussen gebruikers sessies die toegang hebben tot de toepassing.

## <a name="rerun-jobs"></a>Taken opnieuw uitvoeren

U kunt een taak met mislukte apparaten opnieuw uitvoeren. Selecteer **opnieuw uitvoeren bij mislukt**.

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Scherm afbeelding met de knop voor het opnieuw uitvoeren van een taak op mislukte apparaten.":::

Voer een taak naam en beschrijving in en selecteer vervolgens **taak opnieuw uitvoeren**. Er wordt een nieuwe taak verzonden om de actie opnieuw op mislukte apparaten uit te voeren.

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Scherm opname van het dialoog venster voor het opnieuw uitvoeren van defecte apparaten.":::

> [!NOTE]
> U kunt niet meer dan vijf taken tegelijk uitvoeren vanuit een Azure IoT Central-toepassing.
>
> Wanneer een taak is voltooid en u een apparaat verwijdert dat zich in de apparaten lijst van de taak bevindt, wordt de invoer van het apparaat weer gegeven als verwijderd in de naam van het apparaat. De koppeling Details is niet beschikbaar voor het verwijderde apparaat.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u taken kunt maken in uw Azure IoT Central-toepassing, kunt u het volgende doen:

- [Uw apparaten beheren](howto-manage-devices.md)
- [De sjabloon van uw apparaat versie](howto-version-device-template.md)
