---
title: Uw eerste functie maken in Azure Portal
description: Leer hoe u uw eerste serverloze Azure-functie kunt maken met behulp van Azure Portal.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: a96d2ede80b4c57e7b85048379a4bfb66cacfd52
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754859"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Uw eerste functie maken in Azure Portal

Met Azure Functions u uw code uitvoeren in een serverloze omgeving zonder eerst een virtuele machine (VM) te hoeven maken of een webtoepassing te publiceren. In dit artikel leert u hoe u Azure-functies gebruikt om een HTTP-geactiveerde functie 'hallo wereld' te maken in de Azure-portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Als je een C#-ontwikkelaar bent, kun je overwegen [om je eerste functie te maken in Visual Studio 2019 in](functions-create-your-first-function-visual-studio.md) plaats van in de portal. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-account.

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functies. Met een functie-app u functies groeperen als een logische eenheid voor eenvoudiger beheer, implementatie, schalen en delen van resources.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Maak vervolgens een functie in de nieuwe functie-app.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Een door HTTP geactiveerde functie maken

1. Vouw de nieuwe functie-app uit, selecteer **+** de knop naast **Functies,** kies **In-portal**en selecteer **Doorgaan**.

    ![Functies snelstart voor het kiezen van een platform.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Kies **WebHook + API**en selecteer Vervolgens **Maken**.

    ![De Quick Start van Azure Functions in Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

   Een functie wordt gemaakt met een taalspecifieke sjabloon voor een door HTTP getriggerde functie.

U kunt de nieuwe functie nu uitvoeren door een HTTP-aanvraag te verzenden.

## <a name="test-the-function"></a>De functie testen

1. Selecteer in uw nieuwe functie **</> URL** van de functie ophalen rechtsboven. 

1. Selecteer in het dialoogvenster **URL van** de functie ophalen de **standaardinstelling (functiesleutel)** in de vervolgkeuzelijst en selecteer **Kopiëren**. 

    ![De functie-URL vanuit Azure Portal kopiëren](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Plak de URL van de functie in de adresbalk van uw browser. Voeg de waarde `&name=<your_name>` van de querytekenreeks toe aan het einde van deze URL en druk op Enter om de aanvraag uit te voeren. 

    Het volgende voorbeeld toont het antwoord in de browser:

    ![Het antwoord van de functie in de browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    De aanvraag-URL bevat een sleutel die standaard is vereist, en waarmee u via HTTP toegang hebt tot de functie.

1. Wanneer uw functie wordt uitgevoerd, wordt traceringsinformatie naar de logboeken geschreven. Als u de traceringsuitvoer van de vorige uitvoering wilt zien, gaat u terug naar uw functie in de portal en selecteert u de pijl onder aan het scherm om de **logboeken**uit te vouwen.

   ![De viewer voor functielogboeken in Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

