---
title: Taken maken en uitvoeren in uw Azure IoT Central-toepassing | Microsoft Documenten
description: Azure IoT Central-taken maken mogelijkheden voor het beheer van bulkapparaten mogelijk, zoals het bijwerken van eigenschappen of het uitvoeren van een opdracht.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/03/2020
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: c9d5a0daa364b09e45699e898511c28d4b4d92ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157752"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Een taak maken en uitvoeren in uw Azure IoT Central-toepassing

U Microsoft Azure IoT Central gebruiken om uw verbonden apparaten op schaal te beheren met behulp van taken. Met taken u bulkupdates uitvoeren voor apparaateigenschappen en opdrachten uitvoeren. In dit artikel ziet u hoe u aan de slag met vacatures in uw eigen toepassing.

## <a name="create-and-run-a-job"></a>Een taak maken en uitvoeren

In deze sectie ziet u hoe u een taak maakt en uitvoert. Het laat u zien hoe u de lichtdrempel instelt voor een groep logistieke gateway-apparaten.

1. Navigeer vanuit het linkerdeelvenster naar **Taken.**

2. Selecteer **+ Nieuw** om een nieuwe taak te maken:

    ![Nieuwe taak maken](./media/howto-run-a-job/createnewjob.png)

3. Voer een naam en beschrijving in om de taak te identificeren die u maakt.

4. Selecteer de doelgroep waarop u wilt solliciteren. U in de sectie **Samenvatting** zien op hoeveel apparaten uw taakconfiguratie van toepassing is.

5. Kies vervolgens **de eigenschap Cloud,** **Eigenschap** of **Opdracht** als het type taak dat u wilt configureren. Als u een **functieconfiguratie voor eigenschap** wilt instellen, selecteert u een eigenschap en stelt u de nieuwe waarde in. Als u een **opdracht wilt**instellen, kiest u de opdracht die u wilt uitvoeren. Een eigenschapstaak kan meerdere eigenschappen instellen:

    ![Taak configureren](./media/howto-run-a-job/configurejob.png)

6. Nadat u uw taak hebt gemaakt, kiest u **Uitvoeren** of **Opslaan**. De taak wordt nu weergegeven op de hoofdpagina **van Vacatures.** Op deze pagina ziet u uw momenteel lopende taak en de geschiedenis van eerder uitgevoerd of opgeslagen taken. Uw opgeslagen taak kan op elk gewenst moment opnieuw worden geopend om deze te blijven bewerken of uit te voeren:

    ![Job bekijken](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > U 30 dagen geschiedenis weergeven voor uw eerder draaiende taken.

7. Als u een overzicht wilt krijgen van uw taak, selecteert u de taak die u in de lijst wilt weergeven. Dit overzicht bevat de taakgegevens, apparaten en apparaatstatuswaarden. In dit overzicht u ook **Taakgegevens downloaden** selecteren om een CSV-bestand met uw taakgegevens te downloaden, inclusief de apparaten en hun statuswaarden. Deze informatie kan handig zijn voor het oplossen van problemen:

    ![Apparaatstatus weergeven](./media/howto-run-a-job/downloaddetails.png)

### <a name="manage-a-job"></a>Een taak beheren

Als u een van uw hardlooptaken wilt stoppen, opent u deze en selecteert u **Stoppen.** De functiestatus wordt gewijzigd om de taak weer te geven en wordt gestopt. In de sectie **Overzicht** ziet u welke apparaten zijn voltooid, mislukt of nog in behandeling zijn.

Als u een taak wilt uitvoeren die momenteel is gestopt, selecteert u deze en selecteert u **Uitvoeren.** De taakstatus wordt gewijzigd om de taak weer te geven en wordt nu opnieuw uitgevoerd. De **sectie Samenvatting** blijft updaten met de laatste voortgang.

![Taak beheren](./media/howto-run-a-job/managejob.png)

## <a name="copy-a-job"></a>Een taak kopiëren

Als u een van uw bestaande taken wilt kopiëren, selecteert u deze op de pagina **Vacatures** en selecteert u **Kopiëren**. Er wordt een kopie van de taakconfiguratie geopend die u wilt bewerken en **Kopiëren** wordt toegevoegd aan de taaknaam. U de nieuwe taak opslaan of uitvoeren:

![Taak kopiëren](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>De taakstatus weergeven

Nadat een taak is gemaakt, wordt de kolom **Status** bijgewerkt met het laatste statusbericht van de taak. In de volgende tabel worden de mogelijke statuswaarden weergegeven:

| Statusbericht       | Statusbetekenis                                          |
| -------------------- | ------------------------------------------------------- |
| Voltooid            | Deze taak is uitgevoerd op alle apparaten.              |
| Mislukt               | Deze taak is mislukt en niet volledig uitgevoerd op apparaten.  |
| In behandeling              | Deze taak is nog niet begonnen met het uitvoeren op apparaten.         |
| In uitvoering              | Deze taak wordt momenteel uitgevoerd op apparaten.             |
| Gestopt              | Deze taak is handmatig gestopt door een gebruiker.           |

Het statusbericht wordt gevolgd door een overzicht van de apparaten in de taak. In de volgende tabel worden de mogelijke apparaatstatuswaarden weergegeven:

| Statusbericht       | Statusbetekenis                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Geslaagd            | Het aantal apparaten waarop de taak is uitgevoerd.       |
| Mislukt               | Het aantal apparaten waarop de taak niet is uitgevoerd.       |

### <a name="view-the-device-status"></a>De status van het apparaat weergeven

Als u de status van de taak en alle betrokken apparaten wilt weergeven, opent u de taak. Als u een CSV-bestand wilt downloaden met de taakgegevens, inclusief de lijst met apparaten en hun statuswaarden, selecteert u **Taakgegevens downloaden**. Naast elke apparaatnaam ziet u een van de volgende statusberichten:

| Statusbericht       | Statusbetekenis                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Voltooid            | De taak is uitgevoerd op dit apparaat.                                     |
| Mislukt               | De taak is niet uitgevoerd op dit apparaat. Het foutbericht toont meer informatie.  |
| In behandeling              | De taak is nog niet uitgevoerd op dit apparaat.                                   |

> [!NOTE]
> Als een apparaat is verwijderd, u het apparaat niet selecteren. Het wordt weergegeven als verwijderd met de apparaat-ID.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u taken maken in uw Azure IoT Central-toepassing, volgen enkele volgende stappen:

- [Uw apparaten beheren](howto-manage-devices.md)
- [Versie van uw apparaatsjabloon](howto-version-device-template.md)
