---
title: 'Zelf studie: Face Connected service'
titleSuffix: Azure Cognitive Services
description: Maak een Windows-app die gebruikmaakt van de Cognitive Services Face-service om functies van gezichten in een installatie kopie te detecteren.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: ghogen
ms.openlocfilehash: e0fe92fc7f19c3c899bcccfa9f9cc18029af049c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170243"
---
# <a name="connect-to-the-face-service-by-using-connected-services-in-visual-studio"></a>Verbinding maken met de face-service met behulp van verbonden services in Visual Studio

Door gebruik te maken van de Azure face-service, kunt u gezichten in Foto's detecteren, analyseren, organiseren en labelen.

In dit artikel en de bijbehorende artikelen vindt u informatie over het gebruik van de functie van Visual Studio Connected service voor de face-service. De mogelijkheid is beschikbaar in Visual Studio 2017 15.7 of hoger, met de Cognitive Services-extensie geïnstalleerd.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 versie 15,7 of hoger, waarbij de werk belasting voor **Web Development** is geïnstalleerd. [Download nu](https://www.visualstudio.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-face"></a>Een project maken en ondersteuning voor gezicht toevoegen

1. Maak een nieuw ASP.NET Core-webproject. Gebruik de sjabloon Leeg project. 

1. In **Solution Explorer** kiest u **Connected Service** > **Toevoegen**.
   De Connected Service-pagina wordt weergegeven met services die u aan uw project kunt toevoegen.

   ![Voeg een Connected Service menu-item toe](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. Kies in het menu van de beschikbare services, **Cognitive Services Face-API**.

   ![Kies een service om verbinding mee te maken](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Als u bent aangemeld bij Visual Studio en een Azure-abonnement hebt dat is gekoppeld aan uw account, wordt een pagina weergegeven met een vervolgkeuzelijst met uw abonnementen.

   ![Selecteer uw abonnement](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Selecteer het abonnement dat u wilt gebruiken, en kies vervolgens een naam voor de face-service, of kies de Bewerk koppeling om de automatisch gegenereerde naam te wijzigen, kies de resource groep en de prijs categorie.

   ![Details van verbonden services bewerken](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Volg de link voor meer informatie over de prijscategorieën.

1. Kies Toevoegen om ondersteuning van Connected Service toe te voegen.
   Visual Studio wijzigt uw project om de NuGet-pakketten, vermeldingen van het configuratie bestand en andere wijzigingen toe te voegen om een verbinding met de face-service te ondersteunen.

## <a name="use-the-face-service-to-detect-attributes-of-faces-in-an-image"></a>De face-service gebruiken om kenmerken van gezichten in een installatie kopie op te sporen

1. Voeg het volgende toe in Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Voeg een configuratieveld toe en voeg een constructor toe die het veld initialiseert in Opstartklasse om de configuratie in uw programma toe te voegen.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Voeg in de wwwroot-map van uw project een afbeeldingenmap toe en voeg een afbeeldingsbestand toe aan de wwwroot-map. U kunt bijvoorbeeld een van de installatie kopieën op de [pagina gezicht](https://azure.microsoft.com/services/cognitive-services/face/) van het Azure Portal gebruiken. Klik met de rechter muisknop op een van de installatie kopieën, sla het bestand op uw lokale harde schijf op, klik in Solution Explorer met de rechter muisknop op de map afbeeldingen en kies > **bestaand item** **toevoegen** om het toe te voegen aan uw project. Uw project ziet er ongeveer als volgt uit in Solution Explorer:
 
   ![afbeeldingenmap met afbeeldingsbestand](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Klik met de rechtermuisknop op het afbeeldingsbestand, kies Eigenschappen en kies vervolgens **Kopiëren indien nieuwer**.

   ![Kopiëren indien nieuwer](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Vervang de methode configure door de volgende code om toegang te krijgen tot de face-service en een installatie kopie te testen. Verander de imagePath-tekenreeks naar het juiste pad en bestandsnaam voor uw gezichtsafbeelding.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    De code in deze stap bouwt een HTTP-aanvraag met een aanroep naar het gezichts REST API met behulp van de sleutel die u hebt toegevoegd tijdens het toevoegen van de verbonden service.

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

1. Voer de webtoepassing uit en kijk wat de face-service in de installatie kopie heeft gevonden.
 
   ![Afbeelding van gezichts diensten en ingedeelde resultaten](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep als u deze niet meer nodig hebt. Hiermee verwijdert u de Cognitive Service en gerelateerde resources. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
1. Selecteer **Resourcegroep verwijderen**.
1. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Lees de [gezichts documentatie](Overview.md)voor meer informatie over de face-service.
