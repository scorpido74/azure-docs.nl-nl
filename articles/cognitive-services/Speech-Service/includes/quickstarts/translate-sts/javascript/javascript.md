---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: abd34ddf39bd8e64740d3edd4a2bf4f44851c07e
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035918"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een resource voor de Azure-spraakservice maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md)

## <a name="create-a-new-website-folder"></a>Een nieuwe websitemap maken

Maak een nieuwe, lege map. Als u de voorbeeldtoepassing op een webserver wilt hosten, moet u ervoor zorgen dat de webserver toegang heeft tot de map.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>De Speech SDK voor JavaScript in die map uitpakken

Download de Speech SDK als een [ZIP-pakket](https://aka.ms/csspeech/jsbrowserpackage) en pak dit uit in de zojuist gemaakte map. Er worden twee bestanden uitgepakt, `microsoft.cognitiveservices.speech.sdk.bundle.js` en `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Het laatste bestand is optioneel en is handig voor foutopsporing in de SDK-code.

## <a name="create-an-indexhtml-page"></a>Een index.html-pagina maken

Maak een nieuw bestand in de map met de naam `index.html` en open dit bestand met een teksteditor.

1. Maak de volgende HTML-basis:

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-sts.html)]

## <a name="create-the-token-source-optional"></a>De tokenbron maken (optioneel)

Als u de webpagina wilt hosten op een webserver, kunt u desgewenst een tokenbron opgeven voor uw voorbeeldtoepassing.
Op die manier verlaat uw abonnementssleutel nooit uw server en is het gebruikers toegestaan gebruik te maken van spraakmogelijkheden zonder zelf een autorisatiecode in te hoeven voeren.

Maak een nieuw bestand met de naam `token.php`. In dit voorbeeld gaan we ervan uit dat uw webserver de PHP-scripttaal ondersteunt als cURL is ingeschakeld. Voer de volgende code in:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Autorisatietokens hebben slechts een beperkte levensduur.
> In dit eenvoudige voorbeeld wordt niet weergegeven hoe autorisatietokens automatisch worden vernieuwd. Als een gebruiker kunt u de pagina handmatig opnieuw laden of op F5 drukken om te vernieuwen.

## <a name="build-and-run-the-sample-locally"></a>Het voorbeeld bouwen en lokaal uitvoeren

Om de app te starten, dubbelklikt u op het bestand index.html of opent u index.html met uw favoriete webbrowser. Er wordt een eenvoudige grafische gebruikersinterface weergegeven waarin u uw abonnementssleutel en [regio](../../../../regions.md) kunt invoeren en waarin teksttranscriptie voor ingevoerde spraak wordt uitgevoerd.

## <a name="build-and-run-the-sample-via-a-web-server"></a>De voorbeeldtoepassing bouwen en uitvoeren via een webserver

Als u uw app wilt starten, opent u uw favoriete webbrowser en laat u deze wijzen naar de openbare URL waarop u de map host, geeft u uw [regio](../../../../regions.md) op en activeert u teksttranscriptie van de ingevoerde spraak. Indien dit is geconfigureerd, wordt er een token uit uw tokenbron opgehaald.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]