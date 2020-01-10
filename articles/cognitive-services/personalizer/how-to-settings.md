---
title: Personalizer configureren
titleSuffix: Azure Cognitive Services
description: Service configuratie bevat de manier waarop de service beloningen behandelt, hoe vaak de service bekijkt, hoe vaak het model opnieuw wordt getraind en hoeveel gegevens worden opgeslagen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: d20f81bf7db2e098f2bca674c5540bc067577f30
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833910"
---
# <a name="configure-personalizer"></a>Personalizer configureren

Service configuratie bevat de manier waarop de service beloningen behandelt, hoe vaak de service bekijkt, hoe vaak het model opnieuw wordt getraind en hoeveel gegevens worden opgeslagen.

## <a name="create-personalizer-resource"></a>Een persoonlijke resource maken

Maak een persoonlijke resource voor elke feedback-lus.

1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Met de vorige koppeling gaat u naar de pagina **maken** voor de personaler-service.
1. Voer uw service naam in, selecteer een abonnement, locatie, prijs categorie en resource groep.
1. Selecteer de bevestiging en selecteer **maken**.

<a name="configure-service-settings-in-the-azure-portal"></a>

## <a name="configure-service-in-the-azure-portal"></a>De service in de Azure Portal configureren

1. Meld u aan bij de [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Zoek uw persoonlijke resource.
1. Selecteer in de sectie **resource management** de optie **configuratie**.

    Kopieer voordat u de Azure Portal verlaat een van de bron sleutels van de pagina **sleutels** . U hebt dit nodig om de [personaler SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer)te gebruiken.

<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

### <a name="configure-reward-for-the-feedback-loop-based-on-use-case"></a>Beloning voor de feedback-lus op basis van use case configureren

Configureer de service voor het gebruik van beloningen voor uw feedback-lus. Als u de volgende waarden wijzigt, wordt het huidige personalisatie model opnieuw ingesteld en wordt het opnieuw getraind met de laatste 2 dagen aan gegevens:

![De belonings waarden voor de feedback-lus configureren](media/settings/configure-model-reward-settings.png)

|Waarde|Doel|
|--|--|
|Wacht tijd op beloning|Hiermee stelt u de periode in waarin Personaler belonings waarden voor een classificatie oproep verzamelt, vanaf het moment dat de classificatie oproep plaatsvindt. Deze waarde wordt ingesteld door te vragen: ' hoe lang moet Personaler wachten op de aanroepen van beloningen? ' Elke beloning die wordt binnengekomen nadat dit venster wordt geregistreerd, wordt vastgelegd, maar niet voor Learning.|
|Standaard beloning|Als er geen belonings oproep wordt ontvangen door Personaler tijdens het venster belonings wachttijd dat is gekoppeld aan een rang nummer, zal Personaler de standaard beloning toewijzen. In de meeste scenario's is de standaard beloning standaard nul.|
|Belonings aggregatie|Als er meerdere beloningen worden ontvangen voor dezelfde positie-API-aanroep, wordt deze samenvoegings methode gebruikt: **Sum** of **oudste**. De eerste keer kiest de eerste ontvangen Score en wordt de rest verwijderd. Dit is handig als u een unieke beloning wilt hebben tussen mogelijk dubbele aanroepen. |

Nadat u deze waarden hebt gewijzigd, moet u **Opslaan**selecteren.

### <a name="configure-exploration"></a>Verkennen configureren

Personalisatie kan nieuwe patronen ontdekken en de wijzigingen van gebruikers gedrag in de loop van de tijd aanpassen door alternatieven te verkennen. De waarde voor **verkennen** bepaalt welk percentage van de positie aanroepen wordt beantwoord met verkennen.

Als u deze waarde wijzigt, wordt het huidige personalisatie model opnieuw ingesteld en wordt het opnieuw getraind met de laatste 2 dagen aan gegevens.

![De waarde voor verkennen bepaalt welk percentage van de Rangings aanroepen met verkennen wordt beantwoord](media/settings/configure-exploration-setting.png)

Nadat u deze waarde hebt gewijzigd, moet u **Opslaan**selecteren.

### <a name="model-update-frequency"></a>Frequentie van model updates

Het meest recente model, dat is getraind vanuit belonings-API-aanroepen van elke actieve gebeurtenis, wordt niet automatisch gebruikt door de rang orde van Personaler. Met de frequentie van de **model updates** wordt ingesteld hoe vaak het model dat wordt gebruikt door de classificatie oproep up-to-date wordt bijgewerkt.

High model-update frequenties zijn nuttig voor situaties waarin u de wijzigingen in het gedrag van gebruikers nauw keurig wilt bijhouden. Voor beelden zijn onder andere sites die worden uitgevoerd op Live nieuws, virale inhoud of Live-product biedingen. In deze scenario's kunt u een frequentie van 15 minuten gebruiken. Voor de meeste gebruiks voorbeelden is een lagere update frequentie van kracht. Update frequenties van één minuut zijn handig bij het opsporen van fouten in de code van een toepassing met behulp van Personaler, het uitvoeren van demo's of het interactief testen van machine learning aspecten.

![Met de model update frequentie stelt u in hoe vaak een nieuw Personalr model opnieuw wordt getraind.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Nadat u deze waarde hebt gewijzigd, moet u **Opslaan**selecteren.

### <a name="data-retention"></a>Gegevens bewaren

**Gegevens retentie periode** instellen hoeveel dagen persoonlijke gegevens logboeken worden bewaard. Eerdere gegevens logboeken zijn vereist voor het uitvoeren van [offline-evaluaties](concepts-offline-evaluation.md), die worden gebruikt om de effectiviteit van personaler te meten en het trainings beleid te optimaliseren.

Nadat u deze waarde hebt gewijzigd, moet u **Opslaan**selecteren.

## <a name="export-the-personalizer-model"></a>Het Personalr model exporteren

Bekijk in het gedeelte resource beheer voor **model-en leer instellingen**het maken van het model en de datum van de laatste update en exporteer het huidige model. U kunt de Azure Portal of de Personaler-Api's gebruiken voor het exporteren van een model bestand voor archiverings doeleinden.

![Huidig Personal model exporteren](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Gegevens voor uw leer proces wissen

1. Selecteer in de Azure Portal voor uw Personaler-resource op de pagina **model-en leer instellingen** de optie **gegevens wissen**.
1. Schakel alle selectie vakjes 3 in om alle gegevens te wissen en de learning-lus opnieuw in te stellen op de oorspronkelijke staat.

    ![In Azure Portal gegevens wissen uit de resource personaliseren.](./media/settings/clear-data-from-personalizer-resource.png)

    |Waarde|Doel|
    |--|--|
    |Vastgelegde persoonlijke instellingen en belonings gegevens.|Deze logboek registratie gegevens worden gebruikt in offline-evaluaties. Wis de gegevens als u uw resource opnieuw instelt.|
    |Stel het persoonlijke model opnieuw in.|Dit model wordt bij elke retraining gewijzigd. Deze frequentie van training is opgegeven bij **Upload model frequentie** op de **configuratie** pagina. |
    |Stel het trainings beleid in op standaard.|Als u het trainings beleid hebt gewijzigd als onderdeel van een offline-evaluatie, wordt dit opnieuw ingesteld op het oorspronkelijke leer beleid.|

1. Selecteer **geselecteerde gegevens wissen** om het wissen te starten. De status wordt gerapporteerd in azure-meldingen in de rechter navigatie.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het beheren van een leer beleid](how-to-learning-policy.md)
