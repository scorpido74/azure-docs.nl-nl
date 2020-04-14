---
title: Aangepaste vaardigheid formulierherkenning (C#)
titleSuffix: Azure Cognitive Search
description: Meer informatie over het maken van een aangepaste vaardigheid voor formulierherkenning met C# en Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274573"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Voorbeeld: een aangepaste vaardigheid voor formulierherkenning maken

In dit azure cognitive search-vaardigheidssetvoorbeeld leert u hoe u een aangepaste vaardigheid voor formulierherkenning maakt met C# en Visual Studio. Form Recognizer analyseert documenten en haalt sleutel-/waardeparen en tabelgegevens uit. Door Form Recognizer in de [aangepaste vaardigheidsinterface](cognitive-search-custom-skill-interface.md)te verpakken, u deze mogelijkheid toevoegen als een stap in een end-to-end verrijkingspijplijn. De pijplijn kan vervolgens de documenten laden en andere transformaties uitvoeren.

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (elke editie).
- Ten minste vijf vormen van hetzelfde type. U voorbeeldgegevens gebruiken die bij deze handleiding zijn verstrekt.

## <a name="create-a-form-recognizer-resource"></a>Een bron voor formulierherkenning maken

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Uw model trainen

U moet een formulierherkenningsmodel trainen met uw invoerformulieren voordat u deze vaardigheid gebruikt. Volg de [cURL snelstart](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract) om te leren hoe je een model te trainen. U de voorbeeldformulieren gebruiken die in die quickstart zijn opgegeven, of u uw eigen gegevens gebruiken. Zodra het model is getraind, kopieert u de ID-waarde naar een veilige locatie.

## <a name="set-up-the-custom-skill"></a>De aangepaste vaardigheid instellen

Deze zelfstudie maakt gebruik van het [AnalyzeForm-project](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) in de GitHub-opslagplaats [Azure Search Power Skills.](https://github.com/Azure-Samples/azure-search-power-skills) Kloon deze repository naar uw lokale machine en navigeer naar **Vision/AnalyzeForm/** om toegang te krijgen tot het project. Open vervolgens _AnalyzeForm.csproj_ in Visual Studio. Met dit project wordt een Azure-functiebron gemaakt die voldoet aan de [aangepaste vaardigheidsinterface](cognitive-search-custom-skill-interface.md) en kan worden gebruikt voor Azure Cognitive Search-verrijking. Het neemt formulierdocumenten als invoer aan en het voert (als tekst) de sleutel/waardeparen uit die u opgeeft.

Voeg eerst omgevingsvariabelen op projectniveau toe. Zoek het project **AnalyzeForm** in het linkerdeelvenster, klik er met de rechtermuisknop op en selecteer **Eigenschappen**. Klik **in** het venster Eigenschappen op het tabblad **Foutopsporing** en zoek het veld **Omgevingsvariabelen.** Klik **op Toevoegen** om de volgende variabelen toe te voegen:
* `FORMS_RECOGNIZER_ENDPOINT_URL`met de waarde die is ingesteld op de URL van uw eindpunt.
* `FORMS_RECOGNIZER_API_KEY`met de waarde die is ingesteld op uw abonnementssleutel.
* `FORMS_RECOGNIZER_MODEL_ID`met de waarde ingesteld op de ID van het model dat u hebt getraind.
* `FORMS_RECOGNIZER_RETRY_DELAY`met de waarde ingesteld op 1000. Deze waarde is de tijd in milliseconden waarop het programma wacht voordat de query opnieuw wordt geprobeerd.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`met de waarde ingesteld op 100. Deze waarde is het aantal keren dat het programma de service opvraagt terwijl het probeert een succesvol antwoord te krijgen.

Open _vervolgens AnalyzeForm.cs_ en `fieldMappings` zoek de variabele, die verwijst naar het *veldtoewijzingen.json-bestand.* Dit bestand (en de variabele waarnaar verwijst) definieert de lijst met sleutels die u uit uw formulieren wilt extraheren en een aangepast label voor elke sleutel. Een waarde `{ "Address:", "address" }, { "Invoice For:", "recipient" }` van middelen die het script bijvoorbeeld alleen `Address:` opslaat, slaat alleen de waarden op voor de gedetecteerde en `Invoice For:` velden en labelt deze waarden met `"address"` respectievelijk. `"recipient"`

Tot slot, `contentType` let op de variabele. In dit script wordt het opgegeven formulierherkenningsmodel uitgevoerd op externe documenten `application/json`waarnaar wordt verwezen op URL, zodat het inhoudstype . Als u lokale bestanden wilt analyseren door hun bytestreams op te nemen `contentType` in de HTTP-aanvragen, moet u het mimetype wijzigen in het juiste [MIME-type](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) voor uw bestand.

## <a name="test-the-function-from-visual-studio"></a>De functie testen vanuit Visual Studio

Nadat u uw project hebt bewerkt, slaat u het op en stelt u het **AnalyzeForm-project** in als het opstartproject in Visual Studio (als het nog niet is ingesteld). Druk vervolgens op **F5** om de functie in uw lokale omgeving uit te voeren. Gebruik een REST-service zoals [Postman](https://www.postman.com/) om de functie te bellen.

### <a name="http-request"></a>HTTP-aanvraag

U doet het volgende verzoek om de functie te bellen.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Aanvraagbody

Begin met de onderstaande sjabloon voor het aanvraaglichaam.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Hier moet u de URL opgeven van een formulier dat hetzelfde type heeft als de formulieren waarmee u hebt getraind. Voor testdoeleinden u een van uw trainingsformulieren gebruiken. Als u de cURL quickstart hebt gevolgd, bevinden uw formulieren zich in een Azure blob-opslagaccount. Open Azure Storage Explorer, zoek een formulierbestand, klik er met de rechtermuisknop op en selecteer **Gedeelde toegangshandtekening downloaden**. In het volgende dialoogvenster wordt een URL en SAS-token weergegeven. Voer deze tekenreeksen in de `"formUrl"` velden en `"formSasToken"` velden van uw aanvraaglichaam, respectievelijk.

> [!div class="mx-imgBorder"]
> ![Azure-opslagverkenner; een pdf-document is geselecteerd](media/cognitive-search-skill-form/form-sas.png)

Als u een extern document wilt analyseren dat zich niet in `"formUrl"` Azure blob-opslag bevindt, plakt u de URL in het veld en laat u het `"formSasToken"` veld leeg.

> [!NOTE]
> Wanneer de vaardigheid is geïntegreerd in een skillset, worden de URL en het token geleverd door Cognitive Search.

### <a name="response"></a>Antwoord

U ziet een antwoord dat vergelijkbaar is met het volgende voorbeeld:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>De functie publiceren in Azure

Wanneer u tevreden bent met het functiegedrag, u het publiceren.

1. Klik in de **Solution Explorer** in Visual Studio met de rechtermuisknop op het project en selecteer **Publiceren**. Kies **Nieuwe** > **publicatie maken**.
1. Als u Visual Studio nog niet hebt gekoppeld aan uw Azure-account, selecteert u **Een account toevoegen....**
1. Volg de aanwijzingen op het scherm. Geef een unieke naam op voor uw app-service, het Azure-abonnement, de brongroep, het hostingplan en het opslagaccount dat u wilt gebruiken. U een nieuwe brongroep, een nieuw hostingplan en een nieuw opslagaccount maken als u deze nog niet hebt. Wanneer u klaar bent, selecteert u **Maken**.
1. Nadat de implementatie is voltooid, ziet u de URL van de site. Deze URL is het adres van uw functie-app in Azure. Sla het op een tijdelijke locatie op.
1. Navigeer in de [Azure-portal](https://portal.azure.com)naar de resourcegroep en zoek naar de `AnalyzeForm` functie die u hebt gepubliceerd. Zie hosttoetsen onder de sectie **Beheren.** Kopieer de *standaardhostsleutel* en sla deze op een tijdelijke locatie op.

## <a name="connect-to-your-pipeline"></a>Verbinding maken met uw pijplijn

Als u deze vaardigheid wilt gebruiken in een pijplijn voor cognitief zoeken, moet u een vaardigheidsdefinitie toevoegen aan uw vaardigheden. Het volgende JSON-blok is een voorbeeldvaardigheidsdefinitie (u moet de ingangen en uitvoer bijwerken om uw specifieke scenario en vaardighedenomgeving weer te geven). Vervang `AzureFunctionEndpointUrl` de URL van `AzureFunctionDefaultHostKey` uw functie en vervang deze door uw hostsleutel.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u een aangepaste vaardigheid gemaakt vanuit de Azure Form Recognizer-service. Zie de volgende bronnen voor meer informatie over aangepaste vaardigheden. 

* [Azure Search Power Skills: een opslagplaats van aangepaste vaardigheden](https://github.com/*zure-Samples/azure-search-power-skills)
* [Een aangepaste vaardigheid toevoegen aan een AI-verrijkingspijplijn](cognitive-search-custom-skill-interface.md)
* [Een set vaardigheden definiëren](cognitive-search-defining-skillset.md)
* [Een skillset (REST) maken](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [Met kaart verrijkte velden toewijzen](cognitive-search-output-field-mapping.md)
