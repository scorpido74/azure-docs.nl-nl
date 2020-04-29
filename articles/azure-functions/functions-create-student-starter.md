---
title: Een functie maken met behulp van Azure for students starter
description: Meer informatie over het maken van een Azure-functie vanuit een Azure for students starter-abonnement
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: c7dd88bf0ead558a0c4951baf38543566d805caa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756474"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Een functie maken met behulp van Azure for students starter

In deze zelf studie maakt u een ' Hallo wereld ' HTTP-functie in een Azure for students starter-abonnement. Er wordt ook uitgelegd wat er beschikbaar is in Azure Functions in dit type abonnement.

Met micro soft *Azure for students starter* kunt u aan de slag met de Azure-producten die u nodig hebt om gratis te ontwikkelen in de Cloud. [Meer informatie over deze aanbieding vindt u hier.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Met Azure Functions kunt u uw code in een [serverloze](https://azure.microsoft.com/solutions/serverless/) omgeving uitvoeren zonder dat u eerst een virtuele machine moet maken of een webtoepassing moet publiceren. [Meer informatie over functies vindt u hier.](./functions-overview.md)

## <a name="create-a-function"></a>Een functie maken

 In dit onderwerp leert u hoe u functies kunt gebruiken om een HTTP-geactiveerde functie Hallo wereld te maken in de Azure Portal.

![Functie-app maken in Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com>.

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functies. Met een functie-app kunt u functies groeperen als een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen.

1. Selecteer de knop **een resource maken** in de linkerbovenhoek van de Azure Portal. Selecteer vervolgens **reken** > **functie-app**.

    ![Een functie-app maken in Azure Portal](./media/functions-create-student-starter/function-app-create-flow.png)

2. Gebruik voor de functie-app de instellingen die in de tabel onder de afbeelding zijn opgegeven.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **App-naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z`, `0-9` en `-`.  | 
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. | 
    | **[Resource groep](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
   | **[Abonnement/locatie App Service](./functions-scale.md)** | Nieuw | Het hosting abonnement dat bepaalt in welke regio uw functie-app wordt geïmplementeerd en wat de densiteit van uw resources is. Meerdere functie-apps die zijn geïmplementeerd op hetzelfde abonnement, delen dezelfde afzonderlijke gratis instantie. Dit is een beperking van het Student Starter-abonnement. De volledige hosting opties worden [hier beschreven.](./functions-scale.md)|
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.NET** voor C#- en F#-functies. |
    |**[Application Insights](./functions-monitoring.md)**| Ingeschakeld | Application Insights wordt gebruikt om de logboeken van uw functie-app op te slaan en te analyseren. Deze optie is standaard ingeschakeld als u een locatie kiest die Application Insights ondersteunt. Application Insights kunnen voor elke functie worden ingeschakeld door hand matig een regio in de buurt te kiezen die u wilt implementeren Application Insights. Zonder Application Insights, kunt u alleen live streaming-logboeken weer geven.

3. Selecteer hierboven **app service plan/locatie** om een andere locatie te kiezen

4. Selecteer **nieuwe maken** en geef uw plan een unieke naam.

5. Selecteer de locatie die het dichtst bij u ligt. [Bekijk hier een volledig overzicht van Azure-regio's.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Selecteer **Maken** om de functie-app in te richten en te implementeren.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Selecteer het meldingspictogram in de rechterbovenhoek van de portal en zoek het bericht **Implementatie voltooid**.

    ![Instellingen voor nieuwe functie-apps definiëren](./media/functions-create-student-starter/function-app-create-notification.png)

8. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken.

Vervolgens maakt u een functie in de nieuwe functie-app.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Een door HTTP geactiveerde functie maken

1. Vouw uw nieuwe functie-app uit, selecteer **+** de knop naast **functies**, kies **in-portal**en selecteer **door gaan**.

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

## <a name="supported-features-in-azure-for-students-starter"></a>Ondersteunde functies in azure for students starter

In azure for students starter hebt u toegang tot de meeste functies van de Azure Functions runtime, met de volgende sleutel beperkingen:

* De HTTP-trigger is het enige type trigger dat wordt ondersteund.
    * Alle invoer-en uitvoer bindingen worden ondersteund. [Bekijk hier de volledige lijst.](functions-triggers-bindings.md)
* Ondersteunde talen: 
    * C# (.NET Core 2)
    * Java script (node. js 8 & 10)
    * F # (.NET Core 2)
    * [Bekijk hier de talen die in hogere plannen worden ondersteund](supported-languages.md)
* Windows is het enige besturings systeem dat wordt ondersteund.
* De schaal is beperkt tot [één exemplaar van een gratis laag](https://azure.microsoft.com/pricing/details/app-service/windows/) dat elke dag maxi maal 60 minuten wordt uitgevoerd. U kunt serverlessly schalen van 0 naar 1 automatisch als HTTP-verkeer wordt ontvangen, maar niet verder.
* Alleen [versie 2. x en hoger](functions-versions.md) van de functions-runtime wordt ondersteund.
* Alle hulpprogram ma's voor ontwikkel aars worden ondersteund voor het bewerken en publiceren van functies. Dit omvat VS code, Visual Studio, de Azure CLI en de Azure Portal. Als u andere items dan de portal wilt gebruiken, moet u eerst een app in de portal maken en vervolgens die app als een implementatie doel in uw favoriete hulp programma kiezen.

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app gemaakt met een eenvoudige HTTP-geactiveerde functie. U kunt nu de lokale tool, meer talen, bewaking en integraties verkennen.

 * [Uw eerste functie maken met Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Uw eerste functie maken met Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Ontwikkelaars handleiding voor Azure Functions java script](./functions-reference-node.md)
 * [Azure Functions gebruiken om verbinding te maken met een Azure SQL Database](./functions-scenario-database-table-cleanup.md)
 * [Meer informatie over Azure functions HTTP-bindingen](./functions-bindings-http-webhook.md).
 * [Uw Azure Functions bewaken](./functions-monitoring.md)
