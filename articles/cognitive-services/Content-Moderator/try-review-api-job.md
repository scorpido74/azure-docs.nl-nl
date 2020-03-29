---
title: Moderatietaken gebruiken met de REST API-console - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: Gebruik de taakbewerkingen van de Api controleren om end-to-end contentmoderatietaken voor afbeeldings- of tekstinhoud te starten in Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935937"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Moderatietaken definiëren en gebruiken (REST)

Een moderatietaak dient als een soort wrapper voor de functionaliteit van contentmoderatie, workflows en reviews. In deze handleiding ziet u hoe u de API's voor taakREST gebruiken om inhoudsmoderatietaken te starten en te controleren. Zodra u de structuur van de API's begrijpt, u deze oproepen eenvoudig naar elk REST-compatibel platform porten.

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account aan op de site van het [hulpprogramma Inhoudsmoderator.](https://contentmoderator.cognitive.microsoft.com/)
- (Optioneel) [Definieer een aangepaste werkstroom](./Review-Tool-User-Guide/Workflows.md) die u met uw taak wilt gebruiken; u ook de standaardwerkstroom gebruiken.

## <a name="create-a-job"></a>Een taak maken

Als u een moderatietaak wilt maken, gaat u naar de referentiepagina [Taak maken - API maken](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) en selecteert u de knop voor uw abonnementsregio (u dit vinden in de URL van eindpunt op de pagina **Referenties** van het [gereedschap Controleren).](https://contentmoderator.cognitive.microsoft.com/) Hiermee wordt de API-console gestart, waar u eenvoudig REST API-aanroepen samenstellen en uitvoeren.

![Taak - Paginaregioselectie maken](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Rest-oproepparameters invoeren

Voer de volgende waarden in om de REST-aanroep te construeren:

- **teamName:** de team-id die u hebt gemaakt bij het instellen van uw [account met het beoordelingsprogramma](https://contentmoderator.cognitive.microsoft.com/) (gevonden in het veld **Id** op het scherm Referenties van uw gereedschap Controleren).
- **ContentType**: Dit kan "Image", "Text" of "Video" zijn.
- **ContentId:** een aangepaste id-tekenreeks. Deze tekenreeks wordt doorgegeven aan de API en wordt teruggestuurd via de callback. Het is handig voor het koppelen van interne id's of metagegevens aan de resultaten van een moderatietaak.
- **Werkstroomnaam:** de naam van de werkstroom die u eerder hebt gemaakt (of 'standaard' voor de standaardwerkstroom).
- **CallbackEndpoint**: (Optioneel) De URL om terugbelgegevens te ontvangen wanneer de beoordeling is voltooid.
- **Ocp-Apim-Subscription-Key:** Uw contentmoderatorsleutel. U vindt dit op het tabblad **Instellingen** van het [gereedschap Controleren](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Vul de aanvraaginstantie in

De hoofdtekst van uw REST-oproep bevat één veld, **ContentValue**. Plak de inhoud van de ruwe tekst in als u tekst modereert of voer een afbeelding of video-URL in als u afbeelding/video modereert. U de volgende URL van de voorbeeldafbeelding gebruiken:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Taak - Consolequeryparameters, kopteksten en hoofdvak Aanvragen maken](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Uw aanvraag indienen

Selecteer **Verzenden**. Als de bewerking **Response status** slaagt, `200 OK`is de status Antwoord en wordt in het vak **Antwoord-inhoud** een id voor de taak weergegeven. Kopieer deze id naar gebruik in de volgende stappen.

![Controleren - Het inhoudsvak consolerespons maken geeft de controle-id weer](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>De status van een taak krijgen

Als u de status en details van een lopende of voltooide taak wilt krijgen, gaat u naar de pagina [Taak - API-verwijzing downloaden](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) en selecteert u de knop voor uw regio (de regio waarin uw sleutel wordt beheerd).

![Taak - Regioselectie krijgen](images/test-drive-region.png)

Voer de parameters voor de REST-oproep in zoals in de bovenstaande sectie. Voor deze stap is **JobId** de unieke ID-tekenreeks die u hebt ontvangen toen u de taak maakte. Selecteer **Verzenden**. Als de bewerking **Response status** slaagt, `200 OK`is de status Antwoord en wordt de taak **in** JSON-indeling weergegeven in JSON-indeling, zoals:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Job - Ontvang rest-oproepantwoord](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Bekijk de nieuwe review(s)

Als uw inhoudstaak heeft geleid tot het maken van een beoordeling, u deze bekijken in het [hulpprogramma Controleren](https://contentmoderator.cognitive.microsoft.com). Selecteer > **Image**/**Tekstvideo controleren**/**(afhankelijk** van de inhoud die u hebt gebruikt). **Review** De inhoud moet verschijnen, klaar voor menselijke beoordeling. Nadat een menselijke moderator de automatisch toegewezen tags en voorspellingsgegevens heeft beoordeeld en een definitieve moderatiebeslissing heeft ingediend, stuurt de taak-API al deze informatie naar het aangewezen eindpunt van callback.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u geleerd hoe u inhoudsbeheertaken maken en opvragen met behulp van de REST-API. Integreer vervolgens taken in een end-to-end moderatiescenario, zoals de zelfstudie voor [e-commercemoderatie.](./ecommerce-retail-catalog-moderation.md)