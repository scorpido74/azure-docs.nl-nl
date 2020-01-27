---
title: Maak uw functies beschikbaar met OpenAPI met behulp van Azure API Management
description: Maak een definitie van een OpenAPI waarmee andere apps en services uw functie in Azure aanroepen.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 56e2182b408b4e8b1f89eee7458a27c5dd54bb55
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562010"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Een OpenAPI-definitie maken voor een serverloze API met behulp van Azure API Management

REST-Api's worden vaak beschreven met behulp van een OpenAPI-definitie. Deze definitie bevat informatie over welke bewerkingen beschikbaar zijn in een API en hoe de gegevens van de aanvraag en respons voor de API moeten worden opgebouwd.

In deze zelfstudie maakt u een functie waarmee wordt bepaald of een noodherstelproces op een windturbine rendabel is. Vervolgens maakt u een OpenAPI-definitie voor de functie-app met behulp van [Azure API Management](../api-management/api-management-key-concepts.md) , zodat de functie kan worden aangeroepen vanuit andere apps en services.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een maken functie in Azure
> * Een OpenAPI-definitie genereren met behulp van Azure API Management
> * De definitie testen door het aanroepen van de functie
> * De OpenAPI-definitie downloaden

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functies. Met een functie-app kunt u functies groeperen als een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>De functie maken

In deze zelf studie wordt een door HTTP geactiveerde functie gebruikt die twee para meters nodig heeft:

* De geschatte tijd voor het maken van een turbine reparatie, in uren.
* De capaciteit van de turbine, in kilo watt. 

De functie berekend vervolgens hoeveel een reparatie kost en hoeveel omzet de turbine in een periode van 24 uur zou kunnen maken. De door HTTP geactiveerde functie maken in de [Azure Portal](https://portal.azure.com):

1. Vouw de functie-app uit en klik op de knop **+** naast **Functies**. Selecteer **in-portal** > **door gaan**.

1. Selecteer **meer sjablonen...** en selecteer vervolgens **volt ooien en sjablonen weer geven**

1. Selecteer HTTP-trigger, typ `TurbineRepair` voor de functie **naam**, kies `Function` voor **[verificatie niveau](functions-bindings-http-webhook.md#http-auth)** en selecteer vervolgens **maken**.  

    ![De HTTP-functie voor OpenAPI maken](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Vervang de inhoud van het script bestand run C# . CSX door de volgende code en kies vervolgens **Opslaan**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Deze functiecode retourneert een bericht van `Yes` of `No` om aan te geven of een noodherstelproces rendabel is, evenals de kans op omzet die de turbine vertegenwoordigt en de kosten om de turbine te repareren.

1. Als u de functie wilt testen, klikt u helemaal rechts op **testen** om het tabblad testen uit te vouwen. Voer de volgende waarde in voor de **hoofd tekst**van de aanvraag en klik vervolgens op **uitvoeren**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Test de functie in de Azure Portal](media/functions-openapi-definition/test-function.png)

    De volgende waarde wordt in de hoofdtekst van het antwoord geretourneerd.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

U hebt nu een functie die de kosteneffectiviteit van noodreparaties bepaalt. Vervolgens genereert u een OpenAPI-definitie voor de functie-app.

## <a name="generate-the-openapi-definition"></a>De OpenAPI-definitie genereren

U nu kunt de OpenAPI-definitie genereren.

1. Selecteer de functie-app en klik vervolgens in **platform functies**op **API Management** en selecteer **nieuwe maken** onder **API Management**.

    ![API Management selecteren in platform functies](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Gebruik de API Management instellingen zoals opgegeven in de tabel onder de afbeelding.

    ![Nieuwe API Management-service maken](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Wereldwijd unieke naam | Er wordt een naam gegenereerd op basis van de naam van uw functie-app. |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resource wordt gemaakt. |  
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Dezelfde resource als uw functie-app, die voor u moet worden ingesteld. |
    | **Locatie** | US - west | Kies de locatie vs-West. |
    | **Naam van de organisatie** | Contoso | De naam van de organisatie die wordt gebruikt in de ontwikkelaars Portal en voor e-mail meldingen. |
    | **E-mailadres van de beheerder** | uw e-mail adres | E-mail die systeem meldingen van API Management heeft ontvangen. |
    | **Prijscategorie** | Verbruik (preview-versie) | Verbruiks laag is in Preview en is niet beschikbaar in alle regio's. Zie de [pagina met prijzen voor API Management](https://azure.microsoft.com/pricing/details/api-management/) voor de volledige prijs informatie |

1. Kies **Maken** om het API Management-exemplaar te maken. Dit kan enkele minuten duren.

1. Selecteer **Application Insights inschakelen** om logboeken naar dezelfde locatie te verzenden als de functie toepassing, accepteer de resterende standaard waarden en selecteer **koppelings-API**.

1. De **Import Azure functions** wordt geopend met de functie **TurbineRepair** gemarkeerd. Kies **Selecteren** om door te gaan.

    ![Azure Functions importeren in API Management](media/functions-openapi-definition/import-function-openapi.png)

1. Accepteer de standaard instellingen op de pagina **maken op basis van functie-app** en selecteer **maken** .

    ![Maken op basis van functie-app](media/functions-openapi-definition/create-function-openapi.png)

De API is nu gemaakt voor de functie.

## <a name="test-the-api"></a>De API testen

Voordat u de OpenAPI-definitie gebruikt, moet u controleren of de API werkt.

1. Selecteer op het tabblad **testen** van de functie **post** -bewerking.

1. Voer waarden in voor **uren** en **capaciteit**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Klik op **verzenden**en Bekijk het HTTP-antwoord.

    ![Functie-API testen](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>De OpenAPI-definitie downloaden

Als uw API werkt zoals verwacht, kunt u de OpenAPI-definitie downloaden.

1. Selecteer **OpenAPI definitie downloaden** boven aan de pagina.
   
   ![OpenAPI-definitie downloaden](media/functions-openapi-definition/download-definition.png)

2. Open het gedownloade JSON-bestand en controleer de definitie.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt API Management-integratie gebruikt voor het genereren van een OpenAPI definitie van uw functies. U kunt de definitie in API Management nu bewerken in de portal. U kunt ook [meer te weten komen over API Management](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [De OpenAPI-definitie in API Management bewerken](../api-management/edit-api.md)
