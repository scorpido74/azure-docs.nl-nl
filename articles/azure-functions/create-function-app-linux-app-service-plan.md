---
title: Een functie-app op Linux maken vanuit de Azure-portal
description: Leer hoe u uw eerste serverloze Azure-functie kunt maken met behulp van Azure Portal.
ms.topic: how-to
ms.date: 02/28/2019
ms.openlocfilehash: b2de36faf07ad661ff8817adc48b726f54990ceb
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754128"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Een functie-app op Linux maken in een Azure App Service-abonnement

Met Azure Functions kunt u uw functies voor Linux hosten in een standaardcontainer van Azure App Service. In dit artikel u de [Azure-portal](https://portal.azure.com) gebruiken om een door Linux gehoste functie-app te maken die wordt uitgevoerd in een [App Service-abonnement.](functions-scale.md#app-service-plan) U kunt ook [uw eigen aangepaste container](functions-create-function-linux-custom-image.md) gebruiken.

![Functie-app maken in Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com>.

## <a name="create-a-function-app"></a>Een functie-app maken

U moet beschikken over een functie-app om de uitvoering van uw functies in Linux te hosten. De functie-app biedt een omgeving waarin uw functiecode kan worden uitgevoerd. Hiermee u functies groeperen als een logische eenheid voor eenvoudiger beheer, implementatie, schalen en delen van resources. In dit artikel maakt u een App Service-abonnement wanneer u uw functie-app maakt.

1. Selecteer de knop **Een resource maken** in de linkerbovenhoek van Azure Portal. Selecteer vervolgens **Compute** > **Functie-app**.

    ![Een functie-app maken in Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Gebruik voor de functie-app de instellingen die in de tabel onder de afbeelding zijn opgegeven.

    ![Instellingen voor nieuwe functie-apps definiëren](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **App-naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z`, `0-9` en `-`.  | 
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **Besturingssysteem** | Linux | De functie-app draait op Linux. |
    | **Publiceren** | Code | De standaard Linux-container voor uw **Runtime Stack** wordt gebruikt. Het enige wat u hoeft te bieden is uw projectcode voor functie-apps. Een andere optie is het publiceren van een aangepaste [Docker-afbeelding.](functions-create-function-linux-custom-image.md) |
    | **[Hostingplan](functions-scale.md)** | App Service-plan | Hostingabonnement dat definieert hoe resources worden toegewezen aan uw functie-app. Wanneer u een App Service-abonnement uitvoert, u de [schaling van uw functie-app](functions-scale.md)beheren.  |
    | **App Service-plan/-locatie** | Plan maken | Kies **Nieuw maken** en een naam van een **App-serviceplan** opgeven. Kies een **locatie** in een [regio](https://azure.microsoft.com/regions/) bij u in de buurt of in de buurt van andere services waartoe uw functies toegang hebben. Kies de gewenste **[prijscategorie.](https://azure.microsoft.com/pricing/details/app-service/linux/)** <br/>U niet zowel Linux- als Windows-functie-apps uitvoeren in hetzelfde App Service-abonnement. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.NET** voor C#- en F#-functies. |
    | **[Storage](../storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat voldoet aan de [vereisten voor een opslagaccount](storage-considerations.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Ingeschakeld | Application Insights is standaard uitgeschakeld. We raden u aan de integratie van Application Insights nu in te schakelen en een hostinglocatie te kiezen in de buurt van de locatie van uw App Service-abonnement. Zie [Azure-functies controleren](functions-monitoring.md)als u dit later wilt doen.  |

3. Selecteer **Maken** om de functie-app in te richten en te implementeren.

4. Selecteer het meldingspictogram in de rechterbovenhoek van de portal en zoek het bericht **Implementatie voltooid**.

    ![Instellingen voor nieuwe functie-apps definiëren](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken.

Vervolgens maakt u een functie in de nieuwe functie-app. Zelfs nadat uw functie-app beschikbaar is, kan het enkele minuten duren voordat de functie-app volledig is geïnitialiseerd.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Een door HTTP geactiveerde functie maken

In deze sectie ziet u hoe u een functie maakt in uw nieuwe functie-app in de portal.

> [!NOTE]
> De ervaring voor het ontwikkelen van portalen kan handig zijn voor het uitproberen van Azure-functies. Voor de meeste scenario's u overwegen uw functies lokaal te ontwikkelen en het project te publiceren naar uw functie-app met behulp van [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) of de Azure Functions Core [Tools.](functions-run-local.md#create-a-local-functions-project)  

1. Kies in de nieuwe functie-app het tabblad **Overzicht** en kies na het laadt volledig **+ Nieuwe functie**.

    ![Een nieuwe functie maken op het tabblad Overzicht](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Kies in het tabblad **Snelstart** de optie **In-portal**en selecteer **Doorgaan**.

    ![Kies uw functieontwikkelingsplatform.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Kies **WebHook + API** en selecteer vervolgens **Maken**.

    ![De Quick Start van Azure Functions in Azure Portal.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

Een functie wordt gemaakt met een taalspecifieke sjabloon voor een door HTTP getriggerde functie.

U kunt de nieuwe functie nu uitvoeren door een HTTP-aanvraag te verzenden.

## <a name="test-the-function"></a>De functie testen

1. Klik in de nieuwe functie rechtsboven op **</> Functie-URL ophalen**, selecteer **Standaard (functietoets)** en klik vervolgens op **Kopiëren**. 

    ![De functie-URL vanuit Azure Portal kopiëren](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. Plak de URL van de functie in de adresbalk van uw browser. Voeg waarde `&name=<yourname>` voor de querytekenreeks toe aan het einde van deze URL, en druk op de toets `Enter` op het toetsenbord om de aanvraag uit te voeren. U ziet het geretourneerde antwoord van de functie nu in de browser.  

    Het volgende voorbeeld toont het antwoord in de browser:

    ![Het antwoord van de functie in de browser.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    De aanvraag-URL bevat een sleutel die standaard is vereist, en waarmee u via HTTP toegang hebt tot de functie.

3. Wanneer uw functie wordt uitgevoerd, wordt traceringsinformatie naar de logboeken geschreven. Als u de traceringsuitvoer van de vorige uitvoering wilt zien, gaat u terug naar de functie in de portal en klikt u op de pijl onder aan het scherm om de **logboeken** uit te vouwen.

   ![De viewer voor functielogboeken in Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app met een eenvoudige door HTTP geactiveerde functie gemaakt.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Zie [Azure Functions HTTP bindings](functions-bindings-http-webhook.md) (Azure Functions-HTTP-bindingen) voor meer informatie.
