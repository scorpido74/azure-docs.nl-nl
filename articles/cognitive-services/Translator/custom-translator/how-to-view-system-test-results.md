---
title: Systeemtestresultaten en -implementatie weergeven - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Wanneer uw training succesvol is, bekijkt u systeemtests om uw trainingsresultaten te analyseren. Als u tevreden bent met de trainingsresultaten, plaatst u een implementatieaanvraag voor het getrainde model.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f5e1a21f24fdd2458d4803d6ed1675455fa5037d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595599"
---
# <a name="view-system-test-results"></a>Testresultaten voor systeem weergeven

Wanneer uw training succesvol is, bekijkt u systeemtests om uw trainingsresultaten te analyseren. Als u tevreden bent met de trainingsresultaten, plaatst u een implementatieaanvraag voor het getrainde model.

## <a name="system-test-results-page"></a>Pagina Systeemtestresultaten

Selecteer een project, selecteer vervolgens het tabblad modellen van dat project, zoek het model dat u wilt gebruiken en selecteer uiteindelijk het testtabblad.

Op het testtabblad ziet u het:

1.  **Systeemtestresultaten:** Het resultaat van het testproces in de trainingen. Het testproces produceert de BLEU-score.

    **Aantal zinnen:** Hoeveel parallelle zinnen werden gebruikt in de testset.

     **BLEU Score:** BLEU score gegenereerd voor een model na de voltooiing van de training.

    **Status:** Geeft aan of het testproces is voltooid of in uitvoering is.

    ![Systeemtestresultaten](media/how-to/how-to-system-test-results.png)

2.  Klik op de systeemtestresultaten en dat brengt u naar de pagina met resultaatdetails. Deze pagina toont de machinevertaling van zinnen die deel uitmaakten van de testgegevensset.

3.  De tabel op de pagina met details van het testresultaat bevat twee kolommen - één voor elke taal in het paar. In de kolom voor de brontaal ziet u de te vertalen zin. De kolom voor de doeltaal bevat twee zinnen in elke rij.

    **Ref:** Deze zin is de referentievertaling van de bronzin zoals vermeld in de testgegevensset.

    **MT:** Deze zin is de automatische vertaling van de bronzin die door het model wordt gedaan dat na de opleiding werd gebouwd werd uitgevoerd.

    ![Systeemtestresultaten vergelijken](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Downloadtest

Klik op de koppeling Vertalingen downloaden om een zip-bestand te downloaden. De rits bevat de machinevertalingen van bronzinnen in de testgegevensset.

![Downloadtest](media/how-to/how-to-system-test-download.png)

Dit gedownloade zip-archief bevat drie bestanden.

1.  **custom.mt.txt:** Dit bestand bevat machinevertalingen van brontaalzinnen in de doeltaal die wordt uitgevoerd door het model dat is getraind met de gegevens van de gebruiker.

2.  **ref.txt:** Dit bestand bevat door de gebruiker verstrekte vertalingen van brontaalzinnen in de doeltaal.

3.  **source.txt:** Dit bestand bevat zinnen in de brontaal.

    ![Gedownloade systeemtestresultaten](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Een model implementeren

Een implementatie aanvragen:

1.  Selecteer een project en ga naar het tabblad Modellen.

2. Voor een met succes getraind model wordt de knop 'Implementeren' weergegeven, als deze niet is geïmplementeerd.

    ![Model implementeren](media/how-to/how-to-deploy-model.png)

3.  Klik op Implementeren.
4.  Selecteer **Geïmplementeerd** voor de regio(s) waar u wilt dat uw model wordt geïmplementeerd en klik op Opslaan. U **Geïmplementeerd** selecteren voor meerdere regio's.

    ![Model implementeren](media/how-to/how-to-deploy-model-regions.png)

5.  U de status van uw model bekijken in de kolom Status.

>[!Note]
>Custom Translator ondersteunt 10 geïmplementeerde modellen in een werkruimte op elk moment.

## <a name="update-deployment-settings"></a>Implementatie-instellingen bijwerken

Implementatie-instellingen bijwerken:

1.  Selecteer een project en ga naar het tabblad **Modellen.**

2. Voor een met succes geïmplementeerd model wordt een knop **Bijwerken** weergegeven.

    ![Model implementeren](media/how-to/how-to-update-undeploy-model.png)

3.  Selecteer **Update**.
4.  Selecteer **Geïmplementeerd** of **Niet geïmplementeerd** voor de regio(s) waar u wilt dat uw model wordt geïmplementeerd of niet geïmplementeerd, en klik vervolgens op **Opslaan**.

    ![Model implementeren](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Als u **Niet-geïmplementeerd** voor alle regio's selecteert, wordt het model niet vanuit alle regio's geïmplementeerd en in een niet-geïmplementeerde status geplaatst. Het is nu niet beschikbaar voor gebruik.

## <a name="next-steps"></a>Volgende stappen

- Gebruik uw geïmplementeerde aangepaste vertaalmodel via [Microsoft Translator Text API V3.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)
- Meer informatie over [het beheren van instellingen](how-to-manage-settings.md) om uw werkruimte te delen, het beheren van abonnementssleutel.
- Meer [informatie over het migreren](how-to-migrate.md) van uw werkruimte en project vanuit Microsoft Translator [Hub](https://hub.microsofttranslator.com)
