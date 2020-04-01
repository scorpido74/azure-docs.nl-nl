---
title: Handmatig niet door HTTP geactiveerde Azure-functies uitvoeren
description: Een HTTP-aanvraag gebruiken om niet door HTTP geactiveerde Azure-functies uit te voeren
author: craigshoemaker
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 4ce7b8590e4718585fe841921466e049dc204928
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75769129"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Handmatig een niet door HTTP geactiveerde functie uitvoeren

In dit artikel wordt gedemonstreerd hoe u een niet door HTTP geactiveerde functie handmatig kunt uitvoeren via een speciaal opgemaakte HTTP-aanvraag.

In sommige scenario's moet u wellicht een Azure-functie 'op aanvraag' uitvoeren die indirect wordt geactiveerd.  Voorbeelden van indirecte activeringen zijn [geplande functies](./functions-create-scheduled-function.md) of functies die worden uitgevoerd als gevolg van [de actie van een andere resource](./functions-create-storage-blob-triggered-function.md). 

In het volgende voorbeeld wordt [Postman](https://www.getpostman.com/) gebruikt, maar u kunt ook [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) of een soortgelijk hulpprogramma gebruiken HTTP-aanvragen te verzenden.

## <a name="define-the-request-location"></a>Aanvraaglocatie definiëren

Als u een niet-HTTP-geactiveerde functie wilt uitvoeren, hebt u een manier nodig om een verzoek naar Azure te verzenden om de functie uit te voeren. De URL om deze aanvraag te doen, heeft een specifieke vorm.

![De aanvraaglocatie definiëren: hostnaam + mappad + functienaam](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Hostnaam:** De openbare locatie van de functie-app die is opgebouwd uit de naam van de functie-app plus *azurewebsites.net* of uw aangepaste domein.
- **Mappad:** Als u via een HTTP-verzoek toegang wilt krijgen tot niet-HTTP-geactiveerde functies, moet u het verzoek verzenden via de *beheerder/functies*van de mappen.
- **Functienaam:** De naam van de functie die u wilt uitvoeren.

Gebruik de aanvraaglocatie in Postman in combinatie met de hoofdsleutel van de functie in de aanvraag voor Azure om de functie uit te voeren.

> [!NOTE]
> Als de functie lokaal wordt uitgevoerd, is de hoofdsleutel van de functie niet vereist. U kunt de [functie rechtstreeks aanroepen](#call-the-function) en de `x-functions-key`-header weglaten.

## <a name="get-the-functions-master-key"></a>De hoofdsleutel van de functie ophalen

Ga naar de functie in de Azure-portal en klik op **Beheren** en zoek de sectie **Hostsleutels**. Klik in de rij *_master* op de knop **Kopiëren** om de hoofdsleutel naar het klembord te kopiëren.

![Hoofdsleutel vanaf scherm Functiebeheer kopiëren](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Nadat u de hoofdsleutel hebt gekopieerd, klikt u op de functienaam om terug te gaan naar het venster met het codebestand. Klik vervolgens op het tabblad **Logboeken.** Je ziet berichten van de functie die hier is geregistreerd wanneer je de functie handmatig uitvoert vanuit Postman.

> [!CAUTION]  
> Vanwege verhoogde machtigingen in de functie-app die door de hoofdsleutel zijn verleend, dient u deze sleutel niet te delen met derden of in een toepassing te distribueren.

## <a name="call-the-function"></a>Functie aanroepen

Open Postman en volg deze stappen:

1. Voer de **aanvraaglocatie in het URL-tekstvak in**.
2. Zorg ervoor dat de HTTP-methode is ingesteld op **POST**.
3. **Klik** op het tabblad **Headers**.
4. Voer **x-functions-key** in als de eerste **sleutel** en plak de hoofdsleutel (vanaf het klembord) in het vak **waarde**.
5. Voer **Content-Type** in als de tweede **sleutel** en voer **application/json** in als de **waarde**.

    ![Instellingen voor de Postman-headers](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Klik** op het tabblad **Hoofdtekst**.
7. Voer **{ "input": "test" }** in als de hoofdtekst voor de aanvraag.

    ![Instellingen voor de Postman-hoofdtekst](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Klik op **Verzenden**.

    ![Een aanvraag verzenden met Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Postman rapporteert vervolgens de status **202 - Geaccepteerd**.

Ga vervolgens terug naar uw functie in de Azure-portal. Ga naar het venster *Logboeken* en u ziet berichten verschijnen die afkomstig zijn van de handmatige aanroep naar de functie.

![Resultaten in het logboek van de functie na handmatige aanroep](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Volgende stappen

- [Strategieën voor het testen van uw code in Azure Functions](./functions-test-a-function.md)
- [Lokaal opsporen van fouten in Azure-functies voor Event Grid Trigger](./functions-debug-event-grid-trigger-local.md)
