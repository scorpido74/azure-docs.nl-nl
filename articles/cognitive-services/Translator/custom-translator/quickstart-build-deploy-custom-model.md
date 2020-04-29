---
title: 'Snelstart: Een aangepast model bouwen, implementeren en gebruiken - Custom Translator'
titleSuffix: Azure Cognitive Services
description: In deze snelstart doorloopt u stapsgewijs het proces voor het bouwen van een vertaalsysteem met behulp van Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: a22ae3b4b11a4eac7da147cf3ad896bbad435448
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80370210"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Snelstart: Een aangepast model voor vertalen bouwen, implementeren en gebruiken

Dit artikel biedt stapsgewijze instructies voor het bouwen van een vertaalsysteem met Custom Translator.

## <a name="prerequisites"></a>Vereisten

1. Als u de [Custom Translator](https://portal.customtranslator.azure.ai)-portal wilt gebruiken, hebt u een [Microsoft-account](https://signup.live.com) of [Azure AD-account](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) nodig (organisatieaccount gehost in Azure) om u aan te melden.

2. Een abonnement op de Translator Text-API via de Azure-portal. U hebt de Translator Text-API-abonnementssleutel nodig om uw werkruimte te koppelen in Custom Translator. Zie [registreren voor de Translator Text-API](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Wanneer u beide voor gaande hebt, meldt u zich aan bij de [aangepaste Vertaal](https://portal.customtranslator.azure.ai) Portal om werk ruimten, projecten te maken, bestanden te uploaden en modellen te maken/implementeren.

## <a name="create-a-workspace"></a>Een werkruimte maken

Als u voor de eerste keer een gebruiker bent, wordt u gevraagd om akkoord te gaan met de service voorwaarden, om een werk ruimte te maken en uw werk ruimte te koppelen aan het micro soft Translator Text-API-abonnement.

![](media/quickstart/terms-of-service.png)
![](media/quickstart/create-workspace-1.png)
![](media/quickstart/create-workspace-2.png)
![Werk ruimte](media/quickstart/create-workspace-3.png)
maken werk ruimte maken werk ruimte maken werk ruimte maken werk ruimte maken werkruimte maken werk ruimte maken![](media/quickstart/create-workspace-4.png)
![](media/quickstart/create-workspace-5.png)
![](media/quickstart/create-workspace-6.png)

Ga naar de pagina instellingen van de aangepaste Vertaal Portal, waar u uw werk ruimte kunt beheren, maak meer werk ruimten, koppel uw micro soft Translator Text-API-abonnements sleutel aan uw werk ruimten, voeg mede-eigen aars toe en wijzig een abonnements sleutel.

## <a name="create-a-project"></a>Een project maken

Klik op de landingspagina van de Custom Translator-portal op Nieuw project. In het dialoogvenster kunt u de projectnaam, de taalcombinatie en de categorie van uw keuze invullen, naast andere relevante velden. Sla het project vervolgens op. Ga naar [Project maken](how-to-create-project.md) voor meer details.

![Project maken](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Documenten uploaden

Upload vervolgens documentensets voor [trainen](training-and-model.md#training-document-type-for-custom-translator), [afstemmen](training-and-model.md#tuning-document-type-for-custom-translator) en [testen](training-and-model.md#testing-dataset-for-custom-translator). U kunt zowel [parallelle](what-are-parallel-documents.md) documenten als combinatiedocumenten uploaden. U kunt ook een [woordenlijst](what-is-dictionary.md) uploaden.

U kunt documenten uploaden via het tabblad documenten of vanaf de pagina voor een bepaald project.

![Documenten uploaden](media/quickstart/ct-how-to-upload.png)

Kies bij het uploaden van documenten het documenttype (trainen, afstemmen of testen) en de taalcombinatie. Wanneer u parallelle documenten uploadt, moet u ook een documentnaam opgeven. Ga naar [Document uploaden](how-to-upload-document.md) voor meer details.

## <a name="create-a-model"></a>Een model maken

Als alle vereiste documenten zijn geüpload, is de volgende stap het bouwen van het model.

Selecteer het project dat u hebt gemaakt. U ziet alle geüploade documenten die een taalcombinatie delen met dit project. Selecteer de documenten die u wilt opnemen in uw model. U kunt kiezen voor het [trainen](training-and-model.md#training-document-type-for-custom-translator), [afstemmen](training-and-model.md#tuning-document-type-for-custom-translator) en [testen](training-and-model.md#testing-dataset-for-custom-translator) van gegevens of alleen trainingsgegevens selecteren en in Custom Translator automatisch sets voor afstemmen en testen bouwen voor het model.

![Een model maken](media/quickstart/ct-how-to-train.png)

Als u klaar bent met het selecteren van de gewenste documenten, klikt u op de knop Model maken om het model te maken en de training te starten. Op het tabblad Modellen kunt u de status van de training bekijken, en de details voor alle modellen die u hebt getraind.

Ga naar [Een model maken](how-to-train-model.md) voor meer details.

## <a name="analyze-your-model"></a>Het model analyseren

Als de training is voltooid, inspecteert u de resultaten. De BLEU-score is één metrisch gegeven dat de kwaliteit van de vertaling aangeeft. U kunt de vertalingen die zijn gemaakt met het aangepaste model, ook handmatig vergelijken met de vertalingen die worden geboden in de testset, door naar het tabblad Test te gaan en te klikken op Systeemresultaten. Als u een aantal van deze vertalingen handmatig inspecteert, krijgt u een goed idee van de kwaliteit van de vertalingen die worden geproduceerd in uw systeem. Ga naar [Testresultaten voor systeem](how-to-view-system-test-results.md) voor meer details.

## <a name="deploy-a-trained-model"></a>Een getraind model implementeren

Als u er klaar voor bent om het getrainde model te implementeren, klikt u op de knop Implementeren. U kunt één geïmplementeerd model per project hebben, en u kunt de status van de implementatie bekijken in de kolom Status. Ga naar [Modelimplementatie](how-to-view-system-test-results.md#deploy-a-model) voor meer details

![Een getraind model implementeren](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Een geïmplementeerd model gebruiken

U hebt toegang tot geïmplementeerde modellen via de Microsoft Translator [Text-API V3 door de Categorie-id op te geven](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Meer informatie over de Translator Text-API vindt u op de webpagina [API-referentie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference).

## <a name="next-steps"></a>Volgende stappen

- Leer hoe u door de [Custom Translator-werkruimte navigeert en projecten beheert](workspace-and-project.md).
