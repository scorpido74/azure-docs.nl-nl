---
title: Moderatiewerkstromen definiëren met de REST API-console - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: U de API's voor azure-inhoudsmoderatorbeoordeling gebruiken om aangepaste werkstromen en drempelwaarden te definiëren op basis van uw inhoudsbeleid.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754186"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Moderatiewerkstromen definiëren en gebruiken (REST)

Werkstromen zijn op de cloud gebaseerde aangepaste filters die u gebruiken om inhoud efficiënter te verwerken. Werkstromen kunnen verbinding maken met verschillende services om inhoud op verschillende manieren te filteren en vervolgens de juiste actie te ondernemen. In deze handleiding ziet u hoe u de REST API's van de werkstroom via de API-console gebruiken om werkstromen te maken en te gebruiken. Zodra u de structuur van de API's begrijpt, u deze oproepen eenvoudig naar elk REST-compatibel platform porten.

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account aan op de site van het [hulpprogramma Inhoudsmoderator.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="create-a-workflow"></a>Een werkstroom maken

Als u een werkstroom wilt maken of bijwerken, gaat u naar de **[referentiepagina Workflow - API maken of bijwerken](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** en selecteert u de knop voor uw sleutelgebied (u dit vinden in de URL van eindpunt op de pagina **Referenties** van het [hulpprogramma Controleren).](https://contentmoderator.cognitive.microsoft.com/) Hiermee wordt de API-console gestart, waar u eenvoudig REST API-aanroepen samenstellen en uitvoeren.

![Werkstroom - Paginaregioselectie maken of bijwerken](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Rest-oproepparameters invoeren

Voer waarden in voor **team,** **werkstroomnaam**en **Ocp-Apim-Subscription-Key:**

- **team:** de team-id die u hebt gemaakt bij het instellen van uw [account met beoordelingsgereedschap](https://contentmoderator.cognitive.microsoft.com/) (gevonden in het veld **Id** op het scherm Referenties van uw gereedschap Controleren).
- **werkstroomnaam:** de naam van een nieuwe werkstroom die moet worden toegevoegd (of een bestaande naam, als u een bestaande werkstroom wilt bijwerken).
- **Ocp-Apim-Subscription-Key:** Uw contentmoderatorsleutel. U vindt dit op het tabblad **Instellingen** van het [gereedschap Controleren](https://contentmoderator.cognitive.microsoft.com).

![Werkstroom - Consolequeryparameters en -koppen maken of bijwerken](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Een werkstroomdefinitie invoeren

1. Bewerk het vak **Hoofdaanvraag** om de JSON-aanvraag in `Image` `Text`te voeren met details voor **Beschrijving** en **Type** (of ).
2. Kopieer voor **Expressie**de JSON-expressie standaardwerkstroom. Uw laatste JSON-tekenreeks moet er als volgt uitzien:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> U eenvoudige, complexe en zelfs geneste expressies voor uw werkstromen definiëren met behulp van deze API. De [werkstroom - Documentatie maken of bijwerken](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) bevat voorbeelden van complexere logica.

### <a name="submit-your-request"></a>Uw aanvraag indienen
  
Selecteer **Verzenden**. Als de bewerking **Response status** slaagt, `200 OK`wordt de status Antwoord `true`weergegeven en wordt in het vak **Antwoord-inhoud** weergegeven .

### <a name="examine-the-new-workflow"></a>De nieuwe werkstroom onderzoeken

Selecteer in het [gereedschap Controleren](https://contentmoderator.cognitive.microsoft.com/)de optie**Werkstromen** **instellingen** > . Uw nieuwe werkstroom moet in de lijst worden weergegeven.

![Gereedschapslijst met werkstromen controleren](images/workflow-console-new-workflow.PNG)

Selecteer de optie **Bewerken** voor uw werkstroom en ga naar het tabblad **Designer.** Hier ziet u een intuïtieve weergave van de JSON-logica.

![Tabblad Ontwerper voor een geselecteerde werkstroom](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Werkstroomgegevens opvragen

Als u details wilt ophalen over een bestaande werkstroom, gaat u naar de pagina **[Werkstroom - Download](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API-referentie pagina en selecteert u de knop voor uw regio (het gebied waarin uw sleutel wordt beheerd).

![Werkstroom - Regioselectie opdoen](images/test-drive-region.png)

Voer de parameters voor de REST-oproep in zoals in de bovenstaande sectie. Zorg ervoor dat deze keer de naam van de **werkstroom** de naam is van een bestaande werkstroom.

![Queryparameters en -koppen opvragen](images/workflow-get-default.PNG)

Selecteer **Verzenden**. Als de bewerking **Response status** slaagt, `200 OK`is de status Antwoord en wordt **de** werkstroom in JSON-indeling weergegeven, zoals:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van werkstromen met [taken voor inhoudsbeheer](try-review-api-job.md).