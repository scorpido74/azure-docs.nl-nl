---
title: Metagegevens van integratieaccountsartefacten beheren
description: Artefact-metagegevens toevoegen of ophalen uit integratieaccounts in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792467"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Artefact-metagegevens beheren in integratieaccounts met Azure Logic Apps en Enterprise Integration Pack

U aangepaste metagegevens definiëren voor artefacten in integratieaccounts en die metagegevens krijgen tijdens runtime die uw logische app kan gebruiken. U bijvoorbeeld metagegevens voor artefacten, zoals partners, overeenkomsten, schema's en kaarten, allemaal metagegevens opslaan met sleutelwaardeparen. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, <a href="https://azure.microsoft.com/free/" target="_blank">meld u dan aan voor een gratis Azure-account</a>.

* Een [basisintegratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) met de artefacten waar u metagegevens wilt toevoegen, bijvoorbeeld: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Overeenkomst](logic-apps-enterprise-integration-agreements.md)
  * [Schema](logic-apps-enterprise-integration-schemas.md)
  * [Kaart](logic-apps-enterprise-integration-maps.md)

* Een logische app die is gekoppeld aan de integratie-account en artefact metadata die u wilt gebruiken. Als uw logische app nog niet is gekoppeld, leest u [hoe u logische apps koppelt aan integratieaccounts.](logic-apps-enterprise-integration-create-integration-account.md#link-account) 

  Als u nog geen logische app hebt, leest u [hoe u logische apps maakt.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 
  Voeg de trigger en acties toe die u wilt gebruiken voor het beheren van artefact-metagegevens. Of, om gewoon dingen uit te proberen, voeg een trigger zoals **Verzoek** of **HTTP** aan uw logica app.

## <a name="add-metadata-to-artifacts"></a>Metagegevens toevoegen aan artefacten

1. Gebruik de referenties van uw Azure-account om u aan melden bij het <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Zoek en open uw integratieaccount.

1. Selecteer het artefact waar u metagegevens wilt toevoegen en kies **Bewerken**. Voer de metagegevens van dat artefact in, bijvoorbeeld:

   ![Metagegevens invoeren](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Als u klaar bent, kiest u **Done**.

1. Als u deze metagegevens wilt weergeven in de JSON-definitie (JavaScript Object Notation) voor het integratieaccount, kiest **u Bewerken als JSON** zodat de JSON-editor wordt geopend: 

   ![JSON voor metagegevens van partners](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Artefact-metagegevens ophalen

1. Open in de Azure-portal de logische app die is gekoppeld aan het gewenste integratieaccount. 

1. Als u in de Logic App Designer de stap toevoegt voor het verkrijgen van metagegevens onder de trigger of laatste actie in de werkstroom, kiest u **Nieuwe stap** > **Een actie toevoegen**. 

1. Voer in het zoekvak 'integratieaccount' in. Kies **Alle** onder het zoekvak. Selecteer deze actie in de lijst met acties: **Integratieaccount Artefact-opzoeking - Integratieaccount**

   ![Selecteer 'Artefact-opzoeknaar voor integratieaccount'](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Geef deze informatie op voor het artefact dat u wilt vinden:

   | Eigenschap | Vereist | Waarde | Beschrijving | 
   |----------|---------|-------|-------------| 
   | **Artefacttype** | Ja | **Schema**, **Kaart**, **Partner,** **Overeenkomst**of een aangepast type | Het type voor het artefact dat u wilt | 
   | **Artefact-naam** | Ja | <*artefact-naam*> | De naam voor het artefact dat u wilt | 
   ||| 

   Stel dat u de metagegevens voor een partnerartefact wilt krijgen:

   ![Artefacttype selecteren en artefactnaam invoeren](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Voeg de actie toe die u wilt voor het verwerken van die metagegevens, bijvoorbeeld:

   1. Kies onder de actie **Artefact-opzoekactie integratieaccount** de optie **Volgende stap**en selecteer **Een actie toevoegen**. 

   1. Voer in het zoekvak 'http' in. Kies onder het zoekvak **Ingebouwde ins**en selecteer deze actie: **HTTP - HTTP**

      ![HTTP-actie toevoegen](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Geef informatie op voor de artefact-metagegevens die u wilt beheren. 

      Stel dat u de `routingUrl` metagegevens wilt krijgen die eerder in dit onderwerp zijn toegevoegd. Dit zijn de eigenschapswaarden die u mogelijk opgeeft: 

      | Eigenschap | Vereist | Waarde | Beschrijving | 
      |----------|----------|-------|-------------| 
      | **Methode** | Ja | <*operation-to-run*> | De HTTP-bewerking die moet worden uitgevoerd op het artefact. Deze HTTP-actie maakt bijvoorbeeld gebruik van de **GET-methode.** | 
      | **Uri** | Ja | <*metagegevens-locatie*> | Als u `routingUrl` toegang wilt krijgen tot de waarde van metagegevens van het artefact dat u hebt opgehaald, u een expressie gebruiken, bijvoorbeeld: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Headers** | Nee | <*koptekstwaarden*> | Kopkoppenuitvoer van de trigger die u wilt doorgeven aan de HTTP-actie. Bijvoorbeeld om de eigenschapswaarde `headers` van de trigger door te geven: u een expressie gebruiken, bijvoorbeeld: <p>`@triggeroutputs()['headers']` | 
      | **Hoofdtekst** | Nee | <*body-inhoud*> | Alle andere inhoud die u door de `body` eigenschap van de HTTP-actie wilt doorgeven. In dit voorbeeld worden `properties` de waarden van het artefact doorgegeven aan de HTTP-actie: <p>1. Klik in de eigenschap **Body** zodat de lijst met dynamische inhoud wordt weergegeven. Als er geen eigenschappen worden weergegeven, kiest **u Meer weergeven**. <br>2. Selecteer **eigenschappen**in de lijst met dynamische inhoud onder **Integratieaccount artefact opzoeken**. | 
      |||| 

      Bijvoorbeeld:

      ![Waarden en expressies opgeven voor HTTP-actie](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Als u de informatie wilt controleren die u voor de HTTP-actie hebt opgegeven, bekijkt u de JSON-definitie van uw logische app. Kies op de werkbalk Logic App Designer de **codeweergave,** zodat de JSON-definitie van de app wordt weergegeven, bijvoorbeeld:

      ![Json-definitie van logische app](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Nadat u bent overgestapt naar de Logic App Designer, worden alle expressies die u nu hebt gebruikt, opgelost weergegeven, bijvoorbeeld:

      ![Opgeloste expressies in Logic App Designer](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over overeenkomsten](logic-apps-enterprise-integration-agreements.md)
