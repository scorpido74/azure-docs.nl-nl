---
title: Taken maken en uitvoeren in uw Azure IoT Central-toepassing | Microsoft Docs
description: Azure IoT Central-taken bieden mogelijkheden voor bulk beheer, zoals het bijwerken van eigenschappen of het uitvoeren van een opdracht.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: dec9abc38bc0354ef3d22994a7988bfb006f5769
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609703"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Een taak maken en uitvoeren in uw Azure IoT Central-toepassing

U kunt Microsoft Azure IoT Central gebruiken om uw verbonden apparaten op schaal te beheren met behulp van taken. Met taken kunt u bulksgewijs updates uitvoeren op de apparaateigenschappen en de opdrachten uit te voeren. In dit artikel leest u hoe u aan de slag kunt met taken in uw eigen toepassing.

## <a name="create-and-run-a-job"></a>Een taak maken en uitvoeren

In deze sectie wordt beschreven hoe u een taak maakt en uitvoert. U ziet hoe u de lichte drempel waarde instelt voor een groep logistiek gateway apparaten.

1. Navigeer naar **taken** in het linkerdeel venster.

2. Selecteer **+ Nieuw** om een nieuwe taak te maken:

    ![Nieuwe taak maken](./media/howto-run-a-job/create-new-job.png)

3. Voer een naam en beschrijving in om de taak te identificeren die u wilt maken.

4. Selecteer de doel apparaatgroep waarop de taak moet worden toegepast. In de sectie **samen vatting** kunt u zien hoeveel apparaten de taak configuratie van toepassing heeft.

5. Kies vervolgens ofwel een **Cloud eigenschap**, **eigenschap**of **opdracht** als het type taak dat moet worden geconfigureerd. Als u een configuratie van een **Eigenschappen** taak wilt instellen, selecteert u een eigenschap en stelt u de nieuwe waarde in. Als u een **opdracht**wilt instellen, kiest u de opdracht die moet worden uitgevoerd. Een eigenschaps taak kan meerdere eigenschappen instellen:

    ![Taak configureren](./media/howto-run-a-job/configure-job.png)

6. Nadat u uw taak hebt gemaakt, kiest u **uitvoeren** of **Opslaan**. De taak wordt nu weer gegeven op de pagina met hoofd **taken** . Op deze pagina ziet u de taak die momenteel wordt uitgevoerd en de geschiedenis van eerder uitgevoerde of opgeslagen taken. U kunt de opgeslagen taak op elk gewenst moment opnieuw openen om deze te bewerken of om deze uit te voeren:

    ![Taak weer geven](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > U kunt de geschiedenis van 30 dagen voor uw eerder uitgevoerde taken bekijken.

7. Selecteer de taak die u wilt weer geven in de lijst om een overzicht van uw taak te krijgen. Dit overzicht bevat de taak Details, apparaten en status waarden van de apparaten. In dit overzicht kunt u ook **Download taak gegevens** selecteren om een CSV-bestand van uw taak details te downloaden, inclusief de apparaten en hun status waarden. Deze informatie kan nuttig zijn bij het oplossen van problemen:

    ![Apparaatstatus weergeven](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Taken beheren

Als u een van de actieve taken wilt stoppen, opent u deze en selecteert u **stoppen**. De taak status wordt gewijzigd in weer spie gelen de taak wordt gestopt. In de sectie **samen vatting** ziet u welke apparaten zijn voltooid, mislukt of nog in behandeling zijn.

Als u een taak wilt uitvoeren die momenteel is gestopt, selecteert u deze en selecteert u vervolgens **uitvoeren**. De taak status wordt gewijzigd in weer spie gelen de taak wordt nu opnieuw uitgevoerd. De sectie **samen vatting** blijft bijgewerkt met de laatste voortgang.

![Taak beheren](./media/howto-run-a-job/manage-job.png)

## <a name="copy-a-job"></a>Een taak kopiëren

Als u een van uw bestaande taken wilt kopiëren, selecteert u deze op de pagina **taken** en selecteert u **kopiëren**. Een kopie van de taak configuratie wordt geopend, zodat u deze kunt bewerken en **kopiëren** wordt toegevoegd aan de taak naam. U kunt de nieuwe taak opslaan of uitvoeren:

![Taak kopiëren](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Taakstatus weergeven

Nadat een taak is gemaakt, wordt de kolom **status** bijgewerkt met het laatste status bericht van de taak. De volgende tabel bevat de mogelijke status waarden:

| Statusbericht       | Status betekenis                                          |
| -------------------- | ------------------------------------------------------- |
| Voltooid            | Deze taak is uitgevoerd op alle apparaten.              |
| Mislukt               | Deze taak is mislukt en niet volledig uitgevoerd op apparaten.  |
| In behandeling              | De uitvoering van deze taak is nog niet gestart op apparaten.         |
| In uitvoering              | Deze taak wordt momenteel uitgevoerd op apparaten.             |
| Gestopt              | Deze taak is hand matig gestopt door een gebruiker.           |

Het status bericht wordt gevolgd door een overzicht van de apparaten in de taak. De volgende tabel geeft een lijst van mogelijke status waarden van apparaten:

| Statusbericht       | Status betekenis                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Geslaagd            | Het aantal apparaten waarop de taak is uitgevoerd.       |
| Mislukt               | Het aantal apparaten waarop de taak niet kan worden uitgevoerd.       |

### <a name="view-the-device-status-values"></a>De status waarden van het apparaat weer geven

Als u de status van de taak en alle betrokken apparaten wilt weer geven, opent u de taak. Naast de naam van elk apparaat ziet u een van de volgende status berichten:

| Statusbericht       | Status betekenis                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Voltooid            | De taak die op dit apparaat wordt uitgevoerd.                                     |
| Mislukt               | De taak kan niet worden uitgevoerd op dit apparaat. In het fout bericht wordt meer informatie weer gegeven.  |
| In behandeling              | De taak is nog niet uitgevoerd op dit apparaat.                                   |

Als u een CSV-bestand wilt downloaden dat de taak Details bevat en de lijst met apparaten en hun status waarden, selecteert u **downloaden**.

### <a name="filter-the-list-of-devices"></a>De lijst met apparaten filteren

U kunt de apparaten lijst op de pagina met taak Details filteren door het filter pictogram te selecteren. U kunt filteren op de **apparaat-id** of **status** velden:

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="De apparatenlijst filteren":::

### <a name="customize-columns-in-the-device-list"></a>Kolommen aanpassen in de lijst met apparaten

U kunt extra kolommen kiezen om weer te geven in de lijst met apparaten door het pictogram kolom opties te selecteren:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Kolom opties":::

U ziet een dialoog venster waarin u kunt kiezen welke kolommen u wilt weer geven in de lijst met apparaten. Selecteer de kolommen die u wilt weer geven, selecteer het pictogram met de pijl naar rechts en selecteer **OK**. Als u alle beschik bare kolommen wilt selecteren, **selecteert u alles selecteren**:

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Dialoog venster kolom kiezer":::

De geselecteerde kolommen worden weer gegeven in de lijst met apparaten:

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Kolommen selecteren":::

Geselecteerde kolommen blijven behouden in een gebruikers sessie of tussen gebruikers sessies die toegang hebben tot de toepassing.

## <a name="rerun-jobs"></a>Taken opnieuw uitvoeren

U kunt een taak met mislukte apparaten opnieuw uitvoeren. Selecteer **opnieuw uitvoeren**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Een taak opnieuw uitvoeren":::

Voer een taak naam en beschrijving in en selecteer vervolgens **taak opnieuw uitvoeren**. Er wordt een nieuwe taak verzonden om de actie opnieuw op mislukte apparaten uit te voeren:

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Mislukte apparaten opnieuw uitvoeren":::

> [!NOTE]
> U kunt niet meer dan vijf taken tegelijk uitvoeren vanuit een IoT Central-toepassing.

> [!NOTE]
> Wanneer een taak is voltooid en u een apparaat verwijdert dat zich in de apparaten lijst van de taak bevindt, wordt de invoer van het apparaat weer gegeven zoals verwijderd in de koppeling apparaatnaam en apparaatgegevens is niet beschikbaar voor het verwijderde apparaat.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u taken kunt maken in uw Azure IoT Central-toepassing, kunt u het volgende doen:

- [Uw apparaten beheren](howto-manage-devices.md)
- [De sjabloon van uw apparaat versie](howto-version-device-template.md)
