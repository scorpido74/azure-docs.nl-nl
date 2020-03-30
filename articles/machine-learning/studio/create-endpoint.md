---
title: Eindpunten voor webservices maken
titleSuffix: ML Studio (classic) - Azure
description: Webserviceeindpunten maken in Azure Machine Learning Studio (klassiek). Elk eindpunt in de webservice wordt onafhankelijk aangepakt, beperkt en beheerd.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 980ed1e54de30ec8a2dc0c1fdac6546d31f48a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218204"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Eindpunten maken voor geïmplementeerde Azure Machine Learning Studio (klassieke) webservices

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> In dit onderwerp worden technieken beschreven die van toepassing zijn op een **Classic** Machine Learning-webservice.

Na de implementatie van een webservice wordt een standaardeindpunt voor de service gemaakt. Het standaardeindpunt kan worden aangeroepen met de API-sleutel. U meer eindpunten toevoegen met hun eigen sleutels via de Web Services-portal.
Elk eindpunt in de webservice wordt onafhankelijk aangepakt, beperkt en beheerd. Elk eindpunt is een unieke URL met een autorisatiesleutel die u aan uw klanten distribueren.

## <a name="add-endpoints-to-a-web-service"></a>Eindpunten toevoegen aan een webservice

U een eindpunt toevoegen aan een webservice via de Azure Machine Learning Web Services-portal. Zodra het eindpunt is gemaakt, u het gebruiken via synchrone API's, batch-API's en excel-werkbladen.

> [!NOTE]
> Als u extra eindpunten aan de webservice hebt toegevoegd, u het standaardeindpunt niet verwijderen.

1. Klik in Machine Learning Studio (klassiek) in de linkernavigatiekolom op Webservices.
2. Klik onder aan het dashboard van de webservice op **Eindpunten beheren**. De Azure Machine Learning Web Services-portal wordt geopend op de eindpuntenpagina voor de webservice.
3. Klik op **Nieuw**.
4. Typ een naam en beschrijving voor het nieuwe eindpunt. Eindpuntnamen moeten 24 tekens of minder lang zijn en moeten bestaan uit kleine alfabetten of getallen. Selecteer het logboekniveau en of voorbeeldgegevens zijn ingeschakeld. Zie [Logboekregistratie inschakelen voor Machine Learning-webservices voor](web-services-logging.md)meer informatie over logboekregistratie.

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a>Een webservice schalen door extra eindpunten toe te voegen

Standaard is elke gepubliceerde webservice geconfigureerd om 20 gelijktijdige aanvragen te ondersteunen en kan het oplopen tot 200 gelijktijdige aanvragen. Azure Machine Learning Studio (klassiek) optimaliseert automatisch de instelling om de beste prestaties voor uw webservice te bieden en de portalwaarde wordt genegeerd.

Als u van plan bent de API aan te roepen met een hogere belasting dan een maximumwaarde voor gelijktijdige oproepen van 200 ondersteunt, moet u meerdere eindpunten maken op dezelfde webservice. U vervolgens willekeurig uw lading verdelen over alle van hen.

Het schalen van een webservice is een veelvoorkomende taak. Enkele redenen om te schalen zijn om meer dan 200 gelijktijdige aanvragen te ondersteunen, de beschikbaarheid via meerdere eindpunten te verhogen of afzonderlijke eindpunten voor de webservice te bieden. U de schaal vergroten door extra eindpunten toe te voegen voor dezelfde webservice via de [Azure Machine Learning Web Service-portal.](https://services.azureml.net/)

Houd er rekening mee dat het gebruik van een hoog aantal gelijktijdigheid schadelijk kan zijn als u de API niet aanroept met een navenant hoog tarief. Mogelijk ziet u sporadische time-outs en/of pieken in de latentie als u een relatief lage belasting op een API plaatst die is geconfigureerd voor hoge belasting.

De synchrone API's worden meestal gebruikt in situaties waarin een lage latentie gewenst is. Latentie impliceert hier de tijd die nodig is voor de API om een aanvraag te voltooien, en houdt geen rekening met eventuele netwerkvertragingen. Stel dat u een API hebt met een latentie van 50 ms. Om de beschikbare capaciteit volledig te verbruiken met gaspedaalniveau Hoog en Max Concurrent Calls = 20, moet u deze API 20 * 1000 / 50 = 400 keer per seconde aanroepen. Door dit verder uit te breiden, u met een Max Concurrent Calls van 200 de API 4000 keer per seconde aanroepen, uitgaande van een latentie van 50 ms.

## <a name="next-steps"></a>Volgende stappen

[Een Azure Machine Learning-webservice gebruiken.](consume-web-services.md)
