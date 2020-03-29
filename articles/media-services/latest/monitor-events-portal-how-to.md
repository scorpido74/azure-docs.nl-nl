---
title: Azure Media Services-gebeurtenissen met gebeurtenisraster met portal bewaken
description: In dit artikel ziet u hoe u zich abonneren op gebeurtenisraster om Azure Media Services-gebeurtenissen te controleren.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, uitzenden, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 18503e64dc6f38daab61599153cd0e0fb6fadb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509220"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Media Services-gebeurtenissen maken en bewaken met Event Grid met de Azure-portal

Azure Event Grid is een gebeurtenisservice voor de cloud. Deze service maakt gebruik [van gebeurtenisabonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten door te sturen naar abonnees. Gebeurtenissen in Media Services bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U een Gebeurtenis Media Services identificeren omdat de eigenschap eventType begint met 'Microsoft.Media'. Zie [Programmaschema's voor mediaservices-gebeurtenissen](media-services-event-schemas.md)voor meer informatie .

In dit artikel gebruikt u de Azure-portal om u te abonneren op gebeurtenissen voor uw Azure Media Services-account. Vervolgens activeert u gebeurtenissen om het resultaat te bekijken. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. In het artikel sturen we gebeurtenissen naar een web-app die de berichten verzamelt en weergeeft.

Wanneer u klaar bent, ziet u dat de gebeurtenisgegevens naar de web-app zijn verzonden.

## <a name="prerequisites"></a>Vereisten 

* Een actief Azure-abonnement hebben.
* Maak een nieuw Media Services-account zoals beschreven in deze [snelstart](create-account-cli-quickstart.md).

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich abonneert op de gebeurtenissen voor het Media Services-account, maken we het eindpunt voor het gebeurtenisbericht. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. In dit artikel implementeert u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) die de gebeurtenisberichten weergeeft. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

Als u overschakelt naar de site 'Azure Event Grid Viewer', ziet u dat deze nog geen gebeurtenissen heeft.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Abonneer u op evenementen van Media Services

U abonneert u op een onderwerp om Event Grid te laten weten welke gebeurtenissen u wilt traceren en waar de gebeurtenissen naartoe moeten worden gestuurd.

1. Selecteer in de portal uw Media Services-account en selecteer **Gebeurtenissen**.
1. Gebruik een webhook voor het eindpunt als u gebeurtenissen naar uw viewer-app wilt verzenden. 

   ![Webhook selecteren](./media/monitor-events-portal/select-web-hook.png)

1. Het gebeurtenisabonnement is vooraf gevuld met waarden voor uw Media Services-account. 
1. Selecteer 'Webhaak' voor het **eindpunttype**.
1. In dit onderwerp laten we het **Abonneren op alle gebeurtenistypen** gecontroleerd. U het echter uitvinken en filteren op specifieke gebeurtenistypen. 
1. Klik op de koppeling **Een eindpunt selecteren.**

    Voor het webhookeindpunt geeft u de URL van uw web-app op en voegt u `api/updates` toe aan de URL van de startpagina. 

1. Druk op **Selectie bevestigen**.
1. Kies **Maken**.
1. Geef het abonnement een beschrijvende naam.

   ![Logboeken selecteren](./media/monitor-events-portal/create-subscription.png)

1. Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. 

    Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. Het eindpunt moet `validationResponse` worden `validationCode`ingesteld op . Zie [Beveiliging en verificatie van gebeurtenisraster](../../event-grid/security-authentication.md)voor meer informatie. U de web-app-code bekijken om te zien hoe het abonnement wordt gevalideerd.

Laten we nu gebeurtenissen activeren om te zien hoe gebeurtenisraster het bericht naar uw eindpunt distribueert.

## <a name="send-an-event-to-your-endpoint"></a>Een gebeurtenis verzenden naar het eindpunt

U gebeurtenissen voor het Media Services-account activeren door een coderingstaak uit te voeren. U [deze quickstart](stream-files-dotnet-quickstart.md) volgen om een bestand te coderen en gebeurtenissen te verzenden. Als u zich hebt geabonneerd op alle evenementen, ziet u een scherm dat vergelijkbaar is met het volgende:

> [!TIP]
> Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Vernieuw de pagina niet als u alle gebeurtenissen wilt bekijken.

![Een abonnementgebeurtenis weergeven](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Volgende stappen

[Uploaden, coderen en streamen](stream-files-tutorial-with-api.md)
