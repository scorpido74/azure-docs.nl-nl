---
title: Een functie-app in Linux maken op basis van de Azure Portal
description: Leer hoe u uw eerste serverloze Azure-functie kunt maken met behulp van Azure Portal.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 5aae60900a61c28f6c53f89b8e273daccb047eef
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116000"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Een functie-app in Linux maken in een Azure App Service plan

Met Azure Functions kunt u uw functies voor Linux hosten in een standaardcontainer van Azure App Service. In dit artikel wordt uitgelegd hoe u de [Azure Portal](https://portal.azure.com) gebruikt voor het maken van een door Linux gehoste functie-app die wordt uitgevoerd in een [app service plan](functions-scale.md#app-service-plan). U kunt ook [uw eigen aangepaste container](functions-create-function-linux-custom-image.md) gebruiken.

![Functie-app maken in Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com>.

## <a name="create-a-function-app"></a>Een functie-app maken

U moet beschikken over een functie-app om de uitvoering van uw functies in Linux te hosten. De functie-app biedt een omgeving waarin uw functiecode kan worden uitgevoerd. Hiermee kunt u functies groeperen als logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen. In dit artikel maakt u een App Service plan wanneer u de functie-app maakt.

1. Selecteer in het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**.

1. Selecteer op de pagina **Nieuw** de optie **reken**  >  **functie-app**.

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Een functie-app maken in Azure Portal":::

1. Gebruik de instellingen voor de functie-app, zoals opgegeven in de volgende tabel op de pagina **basis beginselen** .

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **[Resource groep](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **functie-app naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z` (niet hoofdletter gevoelig), `0-9` en `-` .  |
    |**Publiceren**| **Code** (standaard) | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.net core** voor C#-en F #-functies. |
    |**Versie**| Versienummer | Kies de versie van de geïnstalleerde runtime.  |
    |**Deel**| Voorkeurs regio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="De pagina Basisinformatie":::

1. Selecteer **volgende: hosten**. Voer op de **Hosting** pagina de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Storage-account](../storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslag accounts moeten tussen de 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat moet voldoen aan de [vereisten voor het opslag account](../azure-functions/functions-scale.md#storage-account-requirements). |
    |**Besturingssysteem**| **Linux** | Er wordt vooraf een besturings systeem geselecteerd voor u op basis van de selectie van de runtime stack, maar u kunt de instelling wijzigen, indien nodig. |
    | **[Plannen](../azure-functions/functions-scale.md)** | **Verbruik (Serverloos)** | Hostingabonnement dat definieert hoe resources worden toegewezen aan uw functie-app. In het standaard **verbruiks** plan worden resources dynamisch toegevoegd als vereist door uw functies. In deze [serverloze](https://azure.microsoft.com/overview/serverless-computing/) hosting betaalt u alleen voor de tijd dat uw functies worden uitgevoerd. Wanneer u in een App Service-plan uitvoert, moet u het [Schalen van uw functie-app](../azure-functions/functions-scale.md) beheren.  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Hosting pagina":::

1. Selecteer **volgende: bewaken**. Voer op de pagina **controle** de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Ja** (standaard instelling) | Hiermee maakt u een Application Insights bron van dezelfde *app-naam* in de dichtstbijzijnde ondersteunde regio. Door deze instelling uit te vouwen of **nieuwe maken**te selecteren, kunt u de naam van de Application Insights wijzigen of een andere regio kiezen in een [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u uw gegevens wilt opslaan. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Bewakings pagina":::

1. Selecteer **controleren + maken** om de selecties van de app-configuratie te controleren.

1. Controleer uw instellingen op de pagina **controleren en maken** en selecteer vervolgens **maken** om de functie-app in te richten en te implementeren.

1. Selecteer het pictogram **meldingen** in de rechter bovenhoek van de portal en Bekijk het bericht **implementatie voltooid** .

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U kunt ook **vastmaken aan dash board**selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app-resource vanuit uw dash board.

    ![Implementatiemelding](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Zelfs nadat de functie-app beschikbaar is, kan het enkele minuten duren voordat deze volledig is geïnitialiseerd.

Vervolgens maakt u een functie in de nieuwe functie-app.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Een HTTP-trigger functie maken

In deze sectie wordt beschreven hoe u een functie maakt in de nieuwe functie-app in de portal.

> [!NOTE]
> De portal-ontwikkelings ervaring kan handig zijn om Azure Functions uit te proberen. Voor de meeste scenario's kunt u uw functies lokaal ontwikkelen en het project publiceren naar uw functie-app met behulp van [Visual Studio code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) of de [Azure functions core tools](functions-run-local.md#create-a-local-functions-project).  

1. Selecteer in het menu links van het venster **functies** de optie **functies**en selecteer vervolgens **toevoegen** in het bovenste menu. 
 
1. Selecteer **http-trigger**in het venster **nieuwe functie** .

    ![HTTP-trigger functie kiezen](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. Accepteer in het venster **nieuwe functie** de standaard naam voor de **nieuwe functie**of voer een nieuwe naam in. 

1. Kies **anoniem** in de vervolg keuzelijst **autorisatie niveau** en selecteer vervolgens **functie maken**.

    Azure maakt de HTTP-activerings functie. U kunt de nieuwe functie nu uitvoeren door een HTTP-aanvraag te verzenden.

## <a name="test-the-function"></a>De functie testen

1. Selecteer in de nieuwe functie HTTP-trigger **code + test** in het menu links en selecteer **functie-URL ophalen** in het bovenste menu.

    ![Functie-URL ophalen selecteren](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. Selecteer in het dialoog venster **functie-URL ophalen** de optie **standaard** in de vervolg keuzelijst en selecteer vervolgens het pictogram **kopiëren naar klem bord** . 

    ![De functie-URL vanuit Azure Portal kopiëren](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Plak de URL van de functie in de adresbalk van uw browser. Voeg de query reeks waarde `?name=<your_name>` toe aan het einde van deze URL en druk op ENTER om de aanvraag uit te voeren. 

    Het volgende voorbeeld toont het antwoord in de browser:

    ![Het antwoord van de functie in de browser.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    De aanvraag-URL bevat een sleutel die standaard is vereist, en waarmee u via HTTP toegang hebt tot de functie.

1. Wanneer uw functie wordt uitgevoerd, wordt traceringsinformatie naar de logboeken geschreven. Als u de uitvoer van de tracering wilt zien, gaat u terug naar de pagina **code en test** in de portal en vouwt u de pijl **Logboeken** onder aan de pagina uit.

   ![De viewer voor functielogboeken in Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app met een eenvoudige HTTP-activerings functie gemaakt.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Zie [Azure Functions HTTP bindings](functions-bindings-http-webhook.md) (Azure Functions-HTTP-bindingen) voor meer informatie.
