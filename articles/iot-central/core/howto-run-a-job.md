---
title: Taken maken en uitvoeren in uw Azure IoT Central-toepassing | Microsoft Docs
description: Met Azure IoT Central-taken kunnen beheer functies voor bulk apparaten worden toegepast, zoals het bijwerken van een eigenschap van een apparaat, het instellen of uitvoeren van een opdracht.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 2a18cfabdecf930f45ca10ae25e9be44cf3f1a5c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953146"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Een taak maken en uitvoeren in uw Azure IoT Central-toepassing

U kunt Microsoft Azure IoT Central gebruiken om uw verbonden apparaten op schaal te beheren met behulp van taken. Met taken kunt u bulksgewijs updates uitvoeren op apparaateigenschappen, instellingen en opdrachten. In dit artikel leert u hoe u aan de slag kunt met taken in uw eigen toepassing.

> [!NOTE] 
> Taken voor Azure IoT Edge-apparaten worden momenteel niet ondersteund. 

## <a name="create-and-run-a-job"></a>Een taak maken en uitvoeren

In deze sectie wordt beschreven hoe u een taak maakt en uitvoert. U ziet hoe u de snelheid van de ventilator kunt verhogen voor meerdere gekoelde verkoop automaten.

1. Navigeer naar taken in het navigatie deel venster.

2. Selecteer **+ Nieuw** om een nieuwe taak te maken.

    ![Nieuwe taak maken](./media/howto-run-a-job/createnewjob.png)

3. Voer een naam en beschrijving in om de taak te identificeren die u wilt maken.

4. Selecteer de apparaatset waarop u de taak wilt Toep assen. Nadat u de apparaatset hebt geselecteerd, ziet u de rechter kant met de apparaten in de apparaatset. Als u een beschadigde apparaatset selecteert, worden er geen apparaten weer gegeven en ziet u een bericht dat uw apparaat is ingesteld.

5. Kies vervolgens het type taak dat u wilt definiëren (een instelling, eigenschap of opdracht). Selecteer **+** naast het type taak dat is geselecteerd en voeg uw bewerkingen toe.

    ![Taak configureren](./media/howto-run-a-job/configurejob.png)

6. Kies aan de rechter kant de apparaten waarop u de taak wilt uitvoeren. Als u het selectie vakje boven selecteert, worden alle apparaten geselecteerd in de hele apparaatset. Als u het selectie vakje bij **naam**selecteert, worden alle apparaten op de huidige pagina geselecteerd.

7. Nadat u uw apparaten hebt geselecteerd, kiest u **uitvoeren** of **Opslaan**. De taak wordt nu weer gegeven op de pagina met hoofd **taken** . In deze weer gave ziet u de taak die momenteel wordt uitgevoerd en de geschiedenis van eerder uitgevoerde taken. De taak die wordt uitgevoerd, wordt altijd bovenaan de lijst weer gegeven. Uw opgeslagen taak kan op elk gewenst moment opnieuw worden geopend om door te gaan met bewerken of om te worden uitgevoerd.

    ![Taak weer geven](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > U kunt de geschiedenis van uw eerder uitgevoerde taken gedurende Maxi maal 30 dagen bekijken.

8. Selecteer de taak die u wilt weer geven in de lijst om een overzicht van uw taak te krijgen. Dit overzicht bevat de taak Details, apparaten en status waarden van de apparaten. In dit overzicht kunt u ook **Download taak gegevens** selecteren om een CSV-bestand van uw taak details te downloaden, met inbegrip van de apparaten en hun status waarden. Deze informatie kan nuttig zijn bij het oplossen van problemen.

    ![Apparaatstatus weergeven](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Een actieve taak stoppen

Als u een taak die wordt uitgevoerd wilt stoppen, selecteert u deze en kiest u **stoppen** in het deel venster. De taak status wordt gewijzigd in weer spie gelen de taak wordt gestopt.

   ![Taak stoppen](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Een taak gestopt uitvoeren

Als u een taak wilt uitvoeren die momenteel is gestopt, selecteert u de taak gestopt. Kies **uitvoeren** in het deel venster. De taak status wordt gewijzigd in weer spie gelen de taak wordt nu opnieuw uitgevoerd.

   ![Taak hervat](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Een taak kopiëren

Als u een bestaande taak die u hebt gemaakt, wilt kopiëren, selecteert u deze op de pagina hoofd taken en selecteert u **kopiëren**. Er wordt een nieuwe kopie van de taak configuratie geopend die u kunt bewerken. U kunt de nieuwe taak opslaan of uitvoeren. Als er wijzigingen zijn aangebracht in de geselecteerde apparaatset, worden deze in deze gekopieerde taak weer gegeven zodat u deze kunt bewerken.

   ![Taak kopiëren](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>De taak status weer geven

Nadat een taak is gemaakt, wordt de kolom **status** bijgewerkt met het laatste status bericht van de taak. De volgende tabel bevat de mogelijke status waarden:

| Statusbericht       | Status betekenis                                          |
| -------------------- | ------------------------------------------------------- |
| Voltooid            | Deze taak is uitgevoerd op alle apparaten.              |
| Mislukt               | Deze taak is mislukt en niet volledig uitgevoerd op apparaten.  |
| In behandeling              | De uitvoering van deze taak is nog niet gestart op apparaten.         |
| In uitvoering              | Deze taak wordt momenteel uitgevoerd op apparaten.             |
| Stopped              | Deze taak is hand matig gestopt door een gebruiker.           |

Het status bericht wordt gevolgd door een overzicht van de apparaten in de taak. De volgende tabel geeft een lijst van mogelijke status waarden van apparaten:

| Statusbericht       | Status betekenis                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Geslaagd            | Het aantal apparaten waarop de taak is uitgevoerd.       |
| Mislukt               | Het aantal apparaten waarop de taak niet kan worden uitgevoerd.       |

### <a name="view-the-device-status"></a>De apparaatstatus weer geven

Als u de status van de taak en alle betrokken apparaten wilt weer geven, selecteert u de taak. Als u een CSV-bestand wilt downloaden dat de taak Details bevat, met inbegrip van de lijst met apparaten en hun status waarden, selecteert u **Details van Download taak**. Naast de naam van elk apparaat ziet u een van de volgende status berichten:

| Statusbericht       | Status betekenis                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Voltooid            | De taak is uitgevoerd op dit apparaat.                                     |
| Mislukt               | De taak kan niet worden uitgevoerd op dit apparaat. In het fout bericht wordt meer informatie weer gegeven.  |
| In behandeling              | De taak is nog niet uitgevoerd op dit apparaat.                                   |

> [!NOTE]
> Als een apparaat is verwijderd, kunt u het apparaat niet selecteren en wordt het weer gegeven als verwijderd met de apparaat-ID.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u taken kunt maken in uw Azure IoT Central-toepassing, kunt u het volgende doen:

- [Apparaatsets gebruiken](howto-use-device-sets.md)
- [Uw apparaten beheren](howto-manage-devices.md)
- [De sjabloon van uw apparaat versie](howto-version-device-template.md)
