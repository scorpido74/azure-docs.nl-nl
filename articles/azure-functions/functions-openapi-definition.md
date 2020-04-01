---
title: Uw functies blootstellen met OpenAPI met Azure API Management
description: Maak een definitie van een OpenAPI waarmee andere apps en services uw functie in Azure aanroepen.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9465209467c83f7de075d16e724459c307d55bd3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77210205"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Een OpenAPI-definitie maken voor een serverloze API met Azure API-beheer

REST API's worden vaak beschreven met behulp van een OpenAPI-definitie. Deze definitie bevat informatie over welke bewerkingen beschikbaar zijn in een API en hoe de gegevens van de aanvraag en respons voor de API moeten worden opgebouwd.

In deze zelfstudie maakt u een functie waarmee wordt bepaald of een noodherstelproces op een windturbine rendabel is. Vervolgens maakt u een OpenAPI-definitie voor de functie-app met [Azure API Management,](../api-management/api-management-key-concepts.md) zodat de functie kan worden aangeroepen vanuit andere apps en services.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een maken functie in Azure
> * Een OpenAPI-definitie genereren met Azure API-beheer
> * De definitie testen door het aanroepen van de functie
> * De OpenAPI-definitie downloaden

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functies. Met een functie-app u functies groeperen als een logische eenheid voor eenvoudiger beheer, implementatie, schalen en delen van resources.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>De functie maken

Deze zelfstudie maakt gebruik van een HTTP-geactiveerde functie die twee parameters nodig heeft:

* De geschatte tijd om een turbine reparatie te maken, in uren.
* De capaciteit van de turbine, in kilowatt. 

De functie berekend vervolgens hoeveel een reparatie kost en hoeveel omzet de turbine in een periode van 24 uur zou kunnen maken. Ga als u de http-geactiveerde functie in de [Azure-portal wilt maken:](https://portal.azure.com)

1. Vouw de functie-app **+** uit en selecteer de knop naast **Functies**. Selecteer **In-portal** > **Doorgaan**.

1. Selecteer **Meer sjablonen...** en selecteer **vervolgens Sjablonen voltooien en weergeven**

1. Selecteer HTTP-trigger, `TurbineRepair` typ voor de **functienaam,** kies `Function` voor **[Verificatieniveau](functions-bindings-http-webhook-trigger.md#http-auth)** en selecteer Vervolgens **Maken**.  

    ![Http-functie maken voor OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Vervang de inhoud van het scriptbestand run.csx C# door de volgende code en kies **Opslaan:**

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

1. Als u de functie wilt testen, klikt u uiterst rechts op **Testen** om het testtabblad uit te vouwen. Voer de volgende waarde in voor de **hoofdtekst Van aanvraag**en klik op **Uitvoeren**.

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

1. Selecteer de functie-app en vervolgens in **Platformfuncties,** kies **API-beheer** en selecteer **Nieuw maken** onder **API-beheer**.

    ![API-beheer kiezen in platformfuncties](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Gebruik de instellingen voor API-beheer zoals opgegeven in de tabel onder de afbeelding.

    ![Nieuwe API Management-service maken](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Wereldwijd unieke naam | Er wordt een naam gegenereerd op basis van de naam van uw functie-app. |
    | **Abonnement** | Uw abonnement | Het abonnement waaronder deze nieuwe bron wordt gemaakt. |  
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Dezelfde bron als uw functie-app, die voor u moet worden ingesteld. |
    | **Locatie** | VS - west | Kies de locatie West US. |
    | **Naam van de organisatie** | Contoso | De naam van de organisatie die wordt gebruikt in de ontwikkelaarsportal en voor e-mailmeldingen. |
    | **E-mailadres van de beheerder** | uw e-mail | E-mail die systeemmeldingen van API-beheer heeft ontvangen. |
    | **Prijslaag** | Verbruik (voorbeeld) | De verbruikslaag is in preview en is niet in alle regio's beschikbaar. Zie de prijspagina API Management voor meer informatie over de prijzen van [API-beheer](https://azure.microsoft.com/pricing/details/api-management/) |

1. Kies **Maken** om het API Management-exemplaar te maken. Dit kan enkele minuten duren.

1. Selecteer **Toepassingsinzichten inschakelen** om logboeken naar dezelfde plaats als de functietoepassing te verzenden, accepteer vervolgens de resterende standaardinstellingen en selecteer **Koppelings-API**.

1. De **Azure-functies importeren** wordt geopend met de functie **TurbineRepair** gemarkeerd. Kies **Selecteren** om door te gaan.

    ![Azure-functies importeren in API-beheer](media/functions-openapi-definition/import-function-openapi.png)

1. Accepteer op de pagina **Maken van functie-app** de standaardinstellingen en selecteer **Maken**

    ![Maken op functie-app](media/functions-openapi-definition/create-function-openapi.png)

De API is nu gemaakt voor de functie.

## <a name="test-the-api"></a>De API testen

Voordat u de OpenAPI-definitie gebruikt, moet u controleren of de API werkt.

1. Selecteer op het tabblad **Testen** van uw functie de optie **POST-bewerking.**

1. Waarden voor **uren** en **capaciteit invoeren**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Klik **op Verzenden**en bekijk vervolgens het HTTP-antwoord.

    ![API van testfunctie](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>De OpenAPI-definitie downloaden

Als uw API werkt zoals verwacht, u de OpenAPI-definitie downloaden.

1. Selecteer **OpenAPI-definitie downloaden** boven aan de pagina.
   
   ![OpenAPI-definitie downloaden](media/functions-openapi-definition/download-definition.png)

2. Open het gedownloade JSON-bestand en bekijk de definitie.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt API Management-integratie gebruikt om een OpenAPI-definitie van uw functies te genereren. U de definitie nu bewerken in API-beheer in de portal. U ook [meer te weten komen over API-beheer.](../api-management/api-management-key-concepts.md)

> [!div class="nextstepaction"]
> [De OpenAPI-definitie bewerken in API-beheer](../api-management/edit-api.md)
