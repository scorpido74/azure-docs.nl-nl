---
title: Taken maken en uitvoeren in uw Azure IoT Central-toepassing | Microsoft Docs
description: Met Azure IoT Central-taken kunnen beheer mogelijkheden voor bulk apparaten worden toegepast, zoals het bijwerken van eigenschappen of het uitvoeren van een opdracht.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 09/10/2020
ms.topic: how-to
ms.openlocfilehash: 82a370bcda2d762abb36e6e43ab755c45348e4c5
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90019952"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Een taak maken en uitvoeren in uw Azure IoT Central-toepassing

U kunt Azure IoT Central gebruiken om uw verbonden apparaten op schaal te beheren via taken. Met taken kunt u bulksgewijs updates uitvoeren op Eigenschappen van apparaten en Clouds en voert u opdrachten uit. In dit artikel wordt beschreven hoe u aan de slag gaat met het gebruik van taken in uw eigen toepassing.

## <a name="create-and-run-a-job"></a>Een taak maken en uitvoeren

In het volgende voor beeld ziet u hoe u een taak maakt en uitvoert om de lichte drempel waarde voor een groep logistiek-gateway apparaten in te stellen. U gebruikt de wizard taak om taken te maken en uit te voeren. U kunt een taak opslaan om deze later uit te voeren.

1. Selecteer **taken**in het linkerdeel venster.

1. Selecteer **+ nieuwe taak**.

1. Voer op de pagina **uw taak configureren** een naam en een beschrijving in om de taak te identificeren die u wilt maken.

1. Selecteer de doel apparaten groep waarop u de taak wilt Toep assen. U kunt zien hoeveel apparaten uw taak configuratie van toepassing is onder de selectie van uw **apparaatgroep** .

1. Kies **Cloud eigenschap**, **eigenschap**of **opdracht** als het **taak type**:

    Als u een configuratie van een **Eigenschappen** taak wilt instellen, selecteert u een eigenschap en stelt u de nieuwe waarde in. Als u de configuratie van een **opdracht** taak wilt instellen, kiest u de opdracht die u wilt uitvoeren. Een eigenschaps taak kan meerdere eigenschappen instellen.

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="Scherm opname van selecties voor het maken van een eigenschaps taak met de naam lichte drempel waarde instellen":::

    Selecteer **opslaan en sluiten** om de taak toe te voegen aan de lijst met opgeslagen taken op de pagina **taken** . U kunt later terugkeren naar een taak in de lijst met opgeslagen taken.

    Selecteer **volgende** om over te scha kelen naar de pagina **controleren** . Op de pagina * * controle * * * * worden de taak configuratie details weer gegeven. Selecteer **uitvoeren** om de taak te verzenden.

    :::image type="content" source="media/howto-run-a-job/job-wizard-review.png" alt-text="Scherm opname van wizard voor taak pagina controleren":::

1. Een taak loopt over *in behandeling*, *uitgevoerd*en *voltooide* fasen. De details van de taak uitvoering bevatten resultaat waarden, Details over de duur en een raster van een lijst met apparaten.

    Wanneer de taak is voltooid, kunt u het **resultaten logboek** selecteren om een CSV-bestand van uw taak details te downloaden, met inbegrip van de apparaten en hun status waarden. Deze informatie kan nuttig zijn bij het oplossen van problemen.

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="Scherm afbeelding met de apparaatstatus":::

1. De taak wordt nu weer gegeven in de **afgelopen 30 dagen** lijst op de pagina **taken** . Op deze pagina worden de momenteel actieve taken en de geschiedenis van eerder uitgevoerde of opgeslagen taken weer gegeven.

    > [!NOTE]
    > U kunt de geschiedenis van 30 dagen voor uw eerder uitgevoerde taken bekijken.

## <a name="manage-jobs"></a>Taken beheren

Als u een actieve taak wilt stoppen, opent u deze en selecteert u **stoppen**. De taak status wordt gewijzigd om aan te geven dat de taak is gestopt. In de sectie **samen vatting** ziet u welke apparaten zijn voltooid, zijn mislukt of nog in behandeling zijn.

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="Scherm afbeelding waarin een actieve taak en de knop voor het stoppen van een taak worden weer gegeven":::

Wanneer een taak is gestopt, kunt u **door gaan** met het uitvoeren van de taak hervatten selecteren. De taak status wordt gewijzigd om aan te geven dat de taak nu opnieuw wordt uitgevoerd. De sectie **samen vatting** blijft bijgewerkt met de laatste voortgang.

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="Scherm afbeelding met een gestopte taak en de knop voor het voortzetten van een taak":::

## <a name="copy-a-job"></a>Een taak kopiëren

Als u een bestaande taak wilt kopiëren, selecteert u een uitgevoerde taak. Selecteer **kopiëren** op de pagina met taak resultaten of pagina Details van taken:

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="Scherm opname van de knop kopiëren":::

Een kopie van de taak configuratie wordt geopend, zodat u deze kunt bewerken en **kopiëren** wordt toegevoegd aan de taak naam.

## <a name="view-job-status"></a>Taakstatus weergeven

Nadat een taak is gemaakt, wordt de kolom **status** bijgewerkt met het laatste taak status bericht. De volgende tabel bevat de mogelijke *taak status* waarden:

| Statusbericht       | Status betekenis                                          |
| -------------------- | ------------------------------------------------------- |
| Voltooid            | Deze taak is uitgevoerd op alle apparaten.              |
| Mislukt               | Deze taak is mislukt en niet volledig uitgevoerd op apparaten.  |
| In behandeling              | Deze taak is nog niet gestart op apparaten.         |
| In uitvoering              | Deze taak wordt momenteel uitgevoerd op apparaten.             |
| Gestopt              | Een gebruiker heeft deze taak hand matig gestopt.           |

Het status bericht wordt gevolgd door een overzicht van de apparaten in de taak. De volgende tabel geeft een lijst van mogelijke status waarden van *apparaten* :

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

Als u een CSV-bestand wilt downloaden dat de taak Details bevat en de lijst met apparaten en hun status waarden, selecteert u **resultaten logboek**.

## <a name="filter-the-device-list"></a>De apparatenlijst filteren

U kunt de apparaten lijst op de pagina met **taak Details** filteren door het filter pictogram te selecteren. U kunt filteren op de **apparaat-id** of **status** veld:

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Scherm afbeelding met selecties voor het filteren van een lijst met apparaten.":::

## <a name="customize-columns-in-the-device-list"></a>Kolommen aanpassen in de lijst met apparaten

U kunt kolommen toevoegen aan de lijst met apparaten door het pictogram kolom opties te selecteren:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Scherm afbeelding met het pictogram voor kolom opties.":::

In het dialoog venster **kolom opties** kunt u de kolommen van de lijst met apparaten kiezen. Selecteer de kolommen die u wilt weer geven, selecteer de pijl-rechts en selecteer vervolgens **OK**. Als u alle beschik bare kolommen wilt selecteren, kiest u **alle selecteren**. De geselecteerde kolommen worden weer gegeven in de lijst met apparaten.

De geselecteerde kolommen blijven behouden in een gebruikers sessie of tussen gebruikers sessies die toegang hebben tot de toepassing.

## <a name="rerun-jobs"></a>Taken opnieuw uitvoeren

U kunt een taak met mislukte apparaten opnieuw uitvoeren. Selecteer **opnieuw uitvoeren bij mislukt**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Scherm afbeelding met de knop voor het opnieuw uitvoeren van een taak op mislukte apparaten.":::

Voer een taak naam en beschrijving in en selecteer vervolgens **taak opnieuw uitvoeren**. Er wordt een nieuwe taak verzonden om de actie opnieuw op mislukte apparaten uit te voeren.

> [!NOTE]
> U kunt niet meer dan vijf taken tegelijk uitvoeren vanuit een Azure IoT Central-toepassing.
>
> Wanneer een taak is voltooid en u een apparaat verwijdert dat zich in de apparaten lijst van de taak bevindt, wordt de invoer van het apparaat weer gegeven als verwijderd in de naam van het apparaat. De koppeling Details is niet beschikbaar voor het verwijderde apparaat.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u taken kunt maken in uw Azure IoT Central-toepassing, kunt u het volgende doen:

- [Uw apparaten beheren](howto-manage-devices.md)
- [De sjabloon van uw apparaat versie](howto-version-device-template.md)
