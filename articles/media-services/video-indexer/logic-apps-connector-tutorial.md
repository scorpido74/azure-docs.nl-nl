---
title: De Video Indexer-connectors met een logische app en zelf studie voor het automatisch uitvoeren van energie.
description: In deze zelf studie leert u hoe u nieuwe ervaringen en verdiensten maximaliseren-mogelijkheden kunt ontgrendelen Video Indexer connectors met Logic app en Poweral automatiseert.
author: anikaz
manager: johndeu
ms.author: anzaman
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 05/01/2020
ms.openlocfilehash: 932f52aa694c13fd3696d82872135304a4e41bdc
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801124"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Zelf studie: Video Indexer gebruiken met logische app en automatisch aan/uit

Azure Media Services [video indexer v2 rest API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) ondersteunt zowel server-naar-server-als client-naar-server-communicatie en stelt video indexer gebruikers in staat om snel video-en audio inzichten te integreren in hun toepassings logica, waardoor nieuwe ervaringen en verdiensten maximaliseren-mogelijkheden kunnen worden vergrendeld.

We [Logic apps](https://azure.microsoft.com/services/logic-apps/) ondersteunen het [automatiseren](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/) van connectors die compatibel zijn met onze API om de integratie nog eenvoudiger te maken. U kunt de connectors gebruiken om aangepaste werk stromen in te stellen voor het effectief indexeren en extra heren van inzichten van een grote hoeveelheid video-en audio bestanden, zonder dat u maar één regel code hoeft te schrijven. Bovendien biedt het gebruik van connectors voor uw integratie beter inzicht in de status van uw werk stroom en een eenvoudige manier om fouten op te sporen.  

Om u te helpen snel aan de slag te gaan met de Video Indexer-connectors, gaan we een overzicht van een logische app en een geautomatiseerde oplossing voor het automatiseren van de toepassing. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw video automatisch uploaden en indexeren
> * De stroom voor het uploaden van bestanden instellen
> * De JSON-extractie stroom instellen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Als u met wilt beginnen, hebt u ook een Video Indexer-account nodig en hebt u toegang tot de Api's via de API-sleutel. 

U hebt ook een Azure Storage-account nodig. Noteer de toegangs sleutel voor uw opslag account. Maak twee containers: een voor het opslaan van Video's in en één voor het opslaan van inzichten die zijn gegenereerd door Video Indexer in.  

Vervolgens moet u twee afzonderlijke stromen openen op Logic Apps of energie automatisering (afhankelijk van uw gebruik).  

## <a name="upload-and-index-your-video-automatically"></a>Uw video automatisch uploaden en indexeren 

Dit scenario bestaat uit twee verschillende stromen die samen werken. De eerste stroom wordt geactiveerd wanneer een BLOB wordt toegevoegd aan of gewijzigd in een Azure Storage-account. Het nieuwe bestand wordt geüpload naar Video Indexer met een call back-URL om een melding te verzenden nadat de indexerings bewerking is voltooid. De tweede stroom wordt geactiveerd op basis van de call back-URL en slaat de uitgepakte inzichten terug naar een JSON-bestand in Azure Storage. Deze twee richtings methode wordt gebruikt voor het effectief ondersteunen van het asynchroon uploaden en indexeren van grotere bestanden. 

### <a name="set-up-the-file-upload-flow"></a>De stroom voor het uploaden van bestanden instellen 

De eerste stroom wordt geactiveerd wanneer een BLOB wordt toegevoegd aan de Azure Storage container. Zodra de trigger is geactiveerd, wordt er een SAS-URI gemaakt die u kunt gebruiken om de video in Video Indexer te uploaden en te indexeren. Begin met het maken van de volgende stroom. 

![Stroom voor uploaden van bestanden](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Als u de eerste stroom wilt instellen, moet u uw Video Indexer-API-sleutel en Azure Storage referenties opgeven. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Verbindingsnaam en API-sleutel](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

Als u verbinding kunt maken met uw Azure Storage en Video Indexer accounts, gaat u naar de trigger ' wanneer een BLOB wordt toegevoegd of gewijzigd ' en selecteert u de container waarin u uw video bestanden wilt plaatsen. 

![Container](./media/logic-apps-connector-tutorial/container.png)

Ga vervolgens naar de actie SAS URI op pad maken en selecteer een lijst met bestanden van de opties voor dynamische inhoud.  

![SAS-URI op pad](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Vul [uw account locatie en-id](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-use-apis#location) in om het token voor het video indexer-account op te halen.

![Toegangs token voor account ophalen](./media/logic-apps-connector-tutorial/account-access-token.png)

Vul voor "video en index uploaden" de vereiste para meters en video-URL in. Selecteer nieuwe para meter toevoegen en selecteer URL voor terugbellen. 

![Uploaden en indexeren](./media/logic-apps-connector-tutorial/upload-and-index.png)

De call back-URL wordt nu leeg gelaten. U voegt deze pas toe nadat u de tweede stroom voor het maken van de call back-URL hebt voltooid. 

U kunt de standaard waarde voor de andere para meters gebruiken of deze instellen op basis van uw behoeften. 

Klik op Opslaan om de tweede stroom te configureren om de inzichten te extra heren zodra het uploaden en indexeren is voltooid. 

## <a name="set-up-the-json-extraction-flow"></a>De JSON-extractie stroom instellen 

De voltooiing van het uploaden en indexeren van de eerste stroom stuurt een HTTP-aanvraag met de juiste call back-URL om de tweede stroom te activeren. Vervolgens worden de inzichten opgehaald die zijn gegenereerd door Video Indexer. In dit voor beeld wordt de uitvoer van uw indexerings taak opgeslagen in uw Azure Storage.  Het is echter wel aan te raden wat u met de uitvoer kunt doen.  

Maak de tweede stroom gescheiden van de eerste. 

![JSON-extractie stroom](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Als u deze stroom wilt instellen, moet u uw Video Indexer-API-sleutel en Azure Storage referenties opnieuw opgeven. U moet dezelfde para meters bijwerken als voor de eerste stroom. 

Voor uw trigger ziet u een HTTP POST-URL-veld. De URL wordt pas gegenereerd als u de stroom hebt opgeslagen. u hebt de URL echter uiteindelijk nodig. We gaan hier terug. 

Vul [uw account locatie en-id](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-use-apis#location) in om het token voor het video indexer-account op te halen.  

Ga naar de actie video-index ophalen en vul de vereiste para meters in. Voor video-ID plaatst u de volgende expressie: triggerOutputs () [' query's '] [' id '] 

![informatie over de actie video indexer](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Met deze expressie krijgt de verbindings verbinder de video-ID uit de uitvoer van de trigger. In dit geval wordt de uitvoer van de trigger de uitvoer van ' video en index uploaden ' in de eerste trigger. 

Ga naar de actie Blob maken en selecteer het pad naar de map waarin u de inzichten wilt opslaan. Stel de naam in van de blob die u maakt. Voor blob-inhoud, plaatst u de volgende expressie: Body (' Get_Video_Index ') 

![BLOB-actie maken](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Deze expressie neemt de uitvoer van de actie ' video-index ophalen ' van deze stroom. 

Klik op stroom opslaan. 

Zodra de stroom is opgeslagen, wordt er een HTTP POST-URL in de trigger gemaakt. Kopieer de URL van de trigger. 

![URL-trigger opslaan](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Ga nu terug naar de eerste stroom en plak de URL in de actie video en index uploaden voor de call back-URL-para meter. 

Zorg ervoor dat beide stromen worden opgeslagen en dat u klaar bent. 

Probeer de zojuist gemaakte logische app of de oplossing voor het automatiseren van uw eigen kracht uit door een video toe te voegen aan uw Azure blobs-container en een paar minuten later terug te gaan om te zien dat de inzichten worden weer gegeven in de doelmap. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelf studie, kunt u deze logische app of stroom voor het automatiseren van de oplossing blijven gebruiken als u dat nodig hebt. Als u echter niet wilt dat deze wordt uitgevoerd en u niet wilt worden gefactureerd, moet u beide stromen uitschakelen als u gebruikmaakt van energie automatisering. Schakel beide stromen uit als u Logic Apps gebruikt. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie is slechts één Video Indexer-connectors-voor beeld uitgewezen. U kunt de Video Indexer-connectors gebruiken voor elke API-aanroep van Video Indexer. Bijvoorbeeld: u kunt inzichten uploaden en ophalen, de resultaten vertalen, insluit bare widgets ophalen en zelfs uw modellen aanpassen. Daarnaast kunt u ervoor kiezen om deze acties te activeren op basis van verschillende bronnen, zoals updates voor bestands opslagplaatsen of e-mail berichten die worden verzonden. Vervolgens kunt u ervoor kiezen om de resultaten bij te werken naar onze relevante infra structuur of toepassing of een wille keurig aantal actie-items te genereren.  

> [!div class="nextstepaction"]
> [De Video Indexer-API gebruiken](video-indexer-use-apis.md)
