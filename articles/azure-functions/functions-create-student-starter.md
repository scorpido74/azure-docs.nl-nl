---
title: Een functie maken met Azure for Students Starter
description: Meer informatie over het maken van een Azure-functie vanuit een Azure for Student Starter-abonnement
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: c7dd88bf0ead558a0c4951baf38543566d805caa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756474"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Een functie maken met Azure for Students Starter

In deze zelfstudie maken we een HTTP-functie 'hallo wereld' in een Azure for Students Starter-abonnement. We doorlopen ook wat er beschikbaar is in Azure-functies in dit abonnementstype.

Microsoft *Azure for Students Starter* brengt u op weg met de Azure-producten die u in de cloud moet ontwikkelen zonder kosten voor u. [Lees hier meer over deze aanbieding.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Met Azure Functions kunt u uw code in een [serverloze](https://azure.microsoft.com/solutions/serverless/) omgeving uitvoeren zonder dat u eerst een virtuele machine moet maken of een webtoepassing moet publiceren. [Lees hier meer over functies.](./functions-overview.md)

## <a name="create-a-function"></a>Een functie maken

 In dit onderwerp leert u hoe u Functies gebruikt om een HTTP-geactiveerde functie 'hallo wereld' te maken in de Azure-portal.

![Functie-app maken in Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com>.

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functies. Met een functie-app u functies groeperen als een logische eenheid voor eenvoudiger beheer, implementatie, schalen en delen van resources.

1. Selecteer de knop **Een resource maken** in de linkerbovenhoek van de Azure-portal. Selecteer vervolgens **Compute** > **Function App**.

    ![Een functie-app maken in Azure Portal](./media/functions-create-student-starter/function-app-create-flow.png)

2. Gebruik voor de functie-app de instellingen die in de tabel onder de afbeelding zijn opgegeven.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **App-naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z`, `0-9` en `-`.  | 
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
   | **[App-serviceplan/locatie](./functions-scale.md)** | Nieuw | Het hostingplan dat bepaalt naar welke regio uw functie-app wordt geïmplementeerd en de dichtheid van uw resources. Meerdere functie-apps die naar hetzelfde abonnement worden geïmplementeerd, delen allemaal dezelfde gratis instantie. Dit is een beperking van het Student Starter plan. De volledige hostingopties [worden hier uitgelegd.](./functions-scale.md)|
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.NET** voor C#- en F#-functies. |
    |**[Application Insights](./functions-monitoring.md)**| Ingeschakeld | Application Insights wordt gebruikt om de logboeken van uw functie-app op te slaan en te analyseren. Het is standaard ingeschakeld als u een locatie kiest die Application Insights ondersteunt. Application Insights kan voor elke functie worden ingeschakeld door handmatig een regio in de buurt te kiezen om Application Insights te implementeren. Zonder Application Insights u alleen live streaming logs bekijken.

3. Selecteer **App Service-abonnement/locatie** hierboven om een andere locatie te kiezen

4. Selecteer **Nieuw maken** en geef uw abonnement een unieke naam.

5. Selecteer de locatie die het dichtst bij u in de buurt is. [Bekijk hier een volledige kaart van Azure-regio's.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Selecteer **Maken** om de functie-app in te richten en te implementeren.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Selecteer het meldingspictogram in de rechterbovenhoek van de portal en zoek het bericht **Implementatie voltooid**.

    ![Instellingen voor nieuwe functie-apps definiëren](./media/functions-create-student-starter/function-app-create-notification.png)

8. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken.

Vervolgens maakt u een functie in de nieuwe functie-app.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Een door HTTP geactiveerde functie maken

1. Vouw de nieuwe functie-app **+** uit, selecteer vervolgens de knop naast **Functies,** kies **In-portal**en selecteer **Doorgaan**.

    ![De Quick Start-pagina 'Kiezen' van het Functions-platform.](./media/functions-create-student-starter/function-app-quickstart-choose-portal.png)

1. Kies **WebHook + API** en selecteer vervolgens **Maken**.

    ![De Quick Start van Azure Functions in Azure Portal.](./media/functions-create-student-starter/function-app-quickstart-node-webhook.png)

Een functie wordt gemaakt met een taalspecifieke sjabloon voor een door HTTP getriggerde functie.

U kunt de nieuwe functie nu uitvoeren door een HTTP-aanvraag te verzenden.

## <a name="test-the-function"></a>De functie testen

1. Klik in de nieuwe functie rechtsboven op **</> Functie-URL ophalen**, selecteer **Standaard (functietoets)** en klik vervolgens op **Kopiëren**. 

    ![De functie-URL vanuit Azure Portal kopiëren](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. Plak de URL van de functie in de adresbalk van uw browser. Voeg waarde `&name=<yourname>` voor de querytekenreeks toe aan het einde van deze URL, en druk op de toets `Enter` op het toetsenbord om de aanvraag uit te voeren. U ziet het geretourneerde antwoord van de functie nu in de browser.  

    Het volgende voorbeeld toont het antwoord in de browser:

    ![Het antwoord van de functie in de browser.](./media/functions-create-student-starter/function-app-browser-testing.png)

    De aanvraag-URL bevat een sleutel die standaard is vereist, en waarmee u via HTTP toegang hebt tot de functie.

3. Wanneer uw functie wordt uitgevoerd, wordt traceringsinformatie naar de logboeken geschreven. Als u de traceringsuitvoer van de vorige uitvoering wilt zien, gaat u terug naar de functie in de portal en klikt u op de pijl onder aan het scherm om de **logboeken** uit te vouwen.

   ![De viewer voor functielogboeken in Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Ondersteunde functies in Azure voor studenten Starter

In Azure for Students Starter hebt u toegang tot de meeste functies van de runtime van Azure-functies, met verschillende belangrijke beperkingen die hieronder worden vermeld:

* De HTTP-trigger is het enige triggertype dat wordt ondersteund.
    * Alle input en alle output bindingen worden ondersteund! [Bekijk hier de volledige lijst.](functions-triggers-bindings.md)
* Ondersteunde talen: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [Bekijk hier talen die worden ondersteund in hogere plannen](supported-languages.md)
* Windows is het enige ondersteunde besturingssysteem.
* De schaal is beperkt tot [één gratis laagexemplaar](https://azure.microsoft.com/pricing/details/app-service/windows/) dat maximaal 60 minuten per dag wordt uitgevoerd. U schaalt automatisch van 0 naar 1 instantie als HTTP-verkeer wordt ontvangen, maar verder niet.
* Alleen [versie 2.x en later](functions-versions.md) van de uitvoering van functies wordt ondersteund.
* Alle tooling voor ontwikkelaars wordt ondersteund voor het bewerken en publiceren van functies. Dit omvat VS Code, Visual Studio, de Azure CLI en de Azure-portal. Als u iets anders dan de portal wilt gebruiken, moet u eerst een app in de portal maken en die app vervolgens kiezen als implementatiedoel in uw voorkeurstool.

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app gemaakt met een eenvoudige HTTP-geactiveerde functie! Nu u lokale tooling, meer talen, monitoring en integraties verkennen.

 * [Uw eerste functie maken met Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Uw eerste functie maken met Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [JavaScript-ontwikkelaarshandleiding voor Azure Functions](./functions-reference-node.md)
 * [Azure-functies gebruiken om verbinding te maken met een Azure SQL-database](./functions-scenario-database-table-cleanup.md)
 * [Meer informatie over HTTP-bindingen voor Azure-functies](./functions-bindings-http-webhook.md).
 * [Uw Azure-functies bewaken](./functions-monitoring.md)
