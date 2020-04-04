---
title: Uw eerste functie maken in Azure Portal
description: Leer hoe u uw eerste serverloze Azure-functie kunt maken met behulp van Azure Portal.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: quickstart
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 417ad96dc3dea25e322dbdb4d81c034a9b9c1e80
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656881"
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

1. Selecteer In het linkermenu van het venster **Functies** de optie **Functies**en selecteer vervolgens **Toevoegen in** het bovenste menu. 
 
1. Selecteer **http-trigger** in **Http trigger**het venster Nieuwe functie .

    ![Http-triggerfunctie kiezen](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Accepteer in het venster **Nieuwe functie** de standaardnaam voor **nieuwe functie**of voer een nieuwe naam in. 

1. Kies **Anoniem** in de vervolgkeuzelijst **Autorisatieniveau** en selecteer **Vervolgens Functie maken**.

    Azure maakt de HTTP-triggerfunctie. U kunt de nieuwe functie nu uitvoeren door een HTTP-aanvraag te verzenden.

## <a name="test-the-function"></a>De functie testen

1. Selecteer in de nieuwe HTTP-triggerfunctie **Code + Test** in het linkermenu en selecteer vervolgens **FUNCTIE-URL ophalen** in het bovenste menu.

    ![Functie-URL ophalen selecteren](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. Selecteer in het dialoogvenster **URL van** de functie ophalen **standaard** in de vervolgkeuzelijst en selecteer vervolgens het pictogram Kopiëren **naar klembord.** 

    ![De functie-URL vanuit Azure Portal kopiëren](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Plak de URL van de functie in de adresbalk van uw browser. Voeg de waarde `?name=<your_name>` van de querytekenreeks toe aan het einde van deze URL en druk op Enter om de aanvraag uit te voeren. 

    Het volgende voorbeeld toont het antwoord in de browser:

    ![Het antwoord van de functie in de browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    De aanvraag-URL bevat een sleutel die standaard is vereist, en waarmee u via HTTP toegang hebt tot de functie.

1. Wanneer uw functie wordt uitgevoerd, wordt traceringsinformatie naar de logboeken geschreven. Als u de traceringsuitvoer wilt zien, gaat u terug naar de pagina **Code + Test** in de portal en vouwt u de pijl **Logboeken** onder aan de pagina uit.

   ![De viewer voor functielogboeken in Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

