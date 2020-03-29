---
title: XML valideren voor B2B-bedrijfsintegratie
description: XML valideren met behulp van schema's in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792165"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML valideren voor B2B-bedrijfsintegratie in Azure Logic Apps met Enterprise Integration Pack

Vaak moeten de handelspartners in een overeenkomst in B2B-scenario's ervoor zorgen dat de berichten die ze uitwisselen geldig zijn voordat een gegevensverwerking kan beginnen. U documenten valideren op basis van een vooraf gedefinieerd schema met behulp van de XML-validatieactie die beschikbaar is met het Enterprise Integration Pack.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meldt u zich aan voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* Een lege of bestaande logische app waarbij u de XML-validatieactie wilt gebruiken. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw Azure-abonnement, is gekoppeld aan de logische app waarbij u de XML-validatieactie wilt gebruiken en bevat het schema dat u wilt gebruiken voor het valideren van XML-inhoud. Zowel uw logische app als integratieaccount moeten op dezelfde locatie of azure-regio bestaan.

## <a name="add-xml-validation-action"></a>XML-validatieactie toevoegen

1. Open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Als u een lege logische app hebt, voert u in `HTTP request` het zoekvak in het zoekvak het filter in en selecteert u de trigger **wanneer een HTTP-aanvraag wordt ontvangen.** Ga anders verder met de volgende stap.

1. Selecteer Onder de laatste stap in uw werkstroom de optie **Nieuwe stap**.

   Als u een actie tussen bestaande stappen wilt toevoegen, verplaatst u de**+** aanwijzer over de pijl die deze stappen verbindt, zodat het plusteken ( ) wordt weergegeven. Selecteer dat plusteken en selecteer **Een actie toevoegen**.

1. Selecteer **Ingebouwde**actie onder **Een actie kiezen**. Voer in het `xml validation` zoekvak in als filter. Selecteer **XML-validatie**in de lijst met acties .

   ![De actie 'XML-validatie' zoeken en selecteren](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Als u de XML-inhoud wilt opgeven die u wilt valideren, klikt u in het vak **Inhoud** zodat de lijst met dynamische inhoud wordt weergegeven.

   ![Lijst met dynamische inhoud openen](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   De lijst met dynamische inhoud toont eigenschapstokens die de uitvoer van de vorige stappen in de werkstroom weergeven. Als de lijst geen verwachte eigenschap weergeeft, controleert u de trigger- of actiekop of u Meer **weergeven**.

1. Selecteer in de lijst dynamische inhoud de eigenschap met de inhoud die u wilt valideren.

   In dit voorbeeld selecteert u de uitvoer **van het hoofd** van de trigger.

   ![Inhoud selecteren om te valideren](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Als u het schema wilt opgeven dat u wilt gebruiken voor validatie, opent u de lijst **Schemanaam** en selecteert u het validatieschema dat u aan uw gekoppelde integratieaccount hebt toegevoegd.

   ![Schema selecteren dat u wilt gebruiken voor validatie](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Sla uw logische app op.

   Je bent nu klaar met het instellen van je validatie. In een echte wereld-app u de gevalideerde gegevens opslaan in een LOB-app (Line-of-Business) zoals SalesForce. Als u de gevalideerde uitvoer naar Salesforce wilt verzenden, voegt u een actie toe.

1. Als u uw validatieactie wilt testen, u een verzoek verzenden om de werkstroom van uw logische app te activeren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)