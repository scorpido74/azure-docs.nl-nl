---
title: Visual Studio Connected Service - Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer hoe u vanuit een ASP.NET Core-webtoepassing verbinding maakt met de Computer Vision-API met behulp van de functie Visual Studio Connected Service.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: e4308f98b6e547acd4adfb62ab78c0517247d905
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177095"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Connected Services in Visual Studio gebruiken voor verbinding met de Computer Vision-API

Dit artikel en de bijbehorende artikelen bieden details over het gebruik van de Visual Studio Connected Service-functie voor de Microsoft Cognitive Services Computer Vision-API. De mogelijkheid is beschikbaar in Visual Studio 2017 15.7 of hoger, met de Cognitive Services-extensie geïnstalleerd.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 versie 15,7 of hoger, waarbij de werk belasting voor **Web Development** is geïnstalleerd. [Download nu](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Ondersteuning voor Microsoft Cognitive Services Computer Vision-API toevoegen aan uw project

1. Maak een nieuw ASP.NET Core-webproject. Gebruik de sjabloon Leeg project. 

1. In **Solution Explorer** kiest u **Connected Service** > **Toevoegen**.
   De Connected Service-pagina wordt weergegeven met services die u aan uw project kunt toevoegen.

   ![Klik met de rechter muisknop op een Visual Studio-project: > verbonden service toevoegen](../media/vs-common/Connected-Service-Menu.PNG)

1. Kies **Microsoft Cognitive Services Computer Vision-API** in het menu van de beschikbare services.

   ![Menu Connected Services: afbeeldingen analyseren... wordt beschreven](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Als u bent aangemeld bij Visual Studio en een Azure-abonnement hebt dat is gekoppeld aan uw account, wordt een pagina weergegeven met een vervolgkeuzelijst met uw abonnementen.

   ![Computer Vision-API venster met gemarkeerde vervolg keuzelijst abonnement](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Selecteer het abonnement dat u wilt gebruiken, en kies een naam voor de Computer Vision-API, of kies de koppeling Bewerken om de automatisch gegenereerde naam te wijzigen, en kies de resourcegroep en prijscategorie.

   ![Details van verbonden services bewerken](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Volg de link voor meer informatie over de prijscategorieën.

1. Kies Toevoegen om ondersteuning van Connected Service toe te voegen.
   Visual Studio wijzigt uw project om NuGet-pakketten toe te voegen, de configuratiebestanden te bewerken en andere wijzigingen voor de ondersteuning van een verbinding met de Computer Vision-API door te voeren. In het Uitvoervenster ziet u het logboek met de gebeurtenissen voor uw project. Er verschijnt informatie die er ongeveer als volgt uitziet:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.1
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>De Computer Vision-API gebruiken voor het detecteren van afbeeldingskenmerken

1. Voeg het volgende toe in Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Voeg een configuratieveld toe en voeg een constructor toe die het veld initialiseert in de klasse `Startup` om de configuratie in uw programma in te schakelen.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Voeg in de wwwroot-map van uw project een afbeeldingenmap toe en voeg een afbeeldingsbestand toe aan de wwwroot-map. Als voorbeeld kunt u een van de afbeeldingen gebruiken op deze [Computer Vision-API-pagina](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Klik met de rechter muisknop op een van de installatie kopieën, sla het bestand op uw lokale harde schijf op, klik in Solution Explorer met de rechter muisknop op de map afbeeldingen en kies vervolgens  > **bestaand item** **toevoegen**om dit toe te voegen aan uw project. Uw project ziet er ongeveer als volgt uit in Solution Explorer: 
  
   ![Schermafbeelding van Solution Explorer met een afbeeldingsbestand geselecteerd](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Klik met de rechtermuisknop op het afbeeldingsbestand, kies Eigenschappen en kies vervolgens **Kopiëren indien nieuwer**. 

   ![venster Eigenschappen van afbeelding; Kopiëren naar uitvoer Directory ingesteld op kopiëren indien nieuwer](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Vervang de configuratiemethode door de volgende code voor toegang tot de Computer Vision-API en test een afbeelding.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // TODO: Change this to your image's path on your site. 
        string imagePath = @"images/subway.png";

        // Enable static files such as image files. 
        app.UseStaticFiles();

        string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
        string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

        HttpClient client = new HttpClient();

        // Request headers.
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

        // Request parameters. A third optional parameter is "details".
        string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

        // Assemble the URI for the REST API Call.
        string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

        HttpResponseMessage response;

        // Request body. Posts an image you've added to your site's images folder. 
        var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
        byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

        string contentString = string.Empty;
        using (ByteArrayContent content = new ByteArrayContent(byteData))
        {
            // This example uses content type "application/octet-stream".
            // The other content types you can use are "application/json" and "multipart/form-data".
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

            // Execute the REST API call.
            response = client.PostAsync(uri, content).Result;

            // Get the JSON response.
            contentString = response.Content.ReadAsStringAsync().Result;
        }

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
            await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
            await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
            await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
            await context.Response.WriteAsync("<p>");
            await context.Response.WriteAsync(JsonPrettyPrint(contentString));
            await context.Response.WriteAsync("<p>");
        });
    }
   ```

    Met de code hier wordt een HTTP-aanvraag met de URI en de afbeelding als binaire inhoud gemaakt voor een aanroep naar de Computer Vision REST-API.

1. Voeg de helperfuncties GetImageAsByteArray en JsonPrettyPrint toe.

   ```csharp
    /// <summary>
    /// Returns the contents of the specified file as a byte array.
    /// </summary>
    /// <param name="imageFilePath">The image file to read.</param>
    /// <returns>The byte array of the image data.</returns>
    static byte[] GetImageAsByteArray(string imageFilePath)
    {
        FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }

    /// <summary>
    /// Formats the given JSON string by adding line breaks and indents.
    /// </summary>
    /// <param name="json">The raw JSON string to format.</param>
    /// <returns>The formatted JSON string.</returns>
    static string JsonPrettyPrint(string json)
    {
        if (string.IsNullOrEmpty(json))
            return string.Empty;

        json = json.Replace(Environment.NewLine, "").Replace("\t", "");

        string INDENT_STRING = "    ";
        var indent = 0;
        var quoted = false;
        var sb = new StringBuilder();
        for (var i = 0; i < json.Length; i++)
        {
            var ch = json[i];
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case '}':
                case ']':
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    sb.Append(ch);
                    break;
                case '"':
                    sb.Append(ch);
                    bool escaped = false;
                    var index = i;
                    while (index > 0 && json[--index] == '\\')
                        escaped = !escaped;
                    if (!escaped)
                        quoted = !quoted;
                    break;
                case ',':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case ':':
                    sb.Append(ch);
                    if (!quoted)
                        sb.Append(" ");
                    break;
                default:
                    sb.Append(ch);
                    break;
            }
        }
        return sb.ToString();
    }
   ```

1. Voer de webtoepassing uit en zie wat de Computer Vision-API in uw afbeelding heeft gevonden.

   ![Afbeelding met opgemaakte resultaten in de Computer Vision-API](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep als u deze niet meer nodig hebt. Hiermee verwijdert u de Cognitive Service en gerelateerde resources. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Lees de [Computer Vision-API documentatie](Home.md)voor meer informatie over de computer vision-API.
