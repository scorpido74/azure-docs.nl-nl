---
title: Handelspartnerovereenkomsten
description: Overeenkomsten tussen handelspartners maken en beheren met Azure Logic Apps en Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790738"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Overeenkomsten voor handelspartners maken en beheren in Azure Logic Apps

Een [partnerovereenkomst](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*agreement* helpt organisaties en bedrijven naadloos met elkaar te communiceren door het specifieke industriestandaardprotocol te definiëren dat u gebruiken bij het uitwisselen van B2B-berichten (business-to-business). Overeenkomsten bieden gemeenschappelijke voordelen, bijvoorbeeld:

* Stel organisaties in staat om informatie uit te wisselen met behulp van een bekende indeling.
* Verbeter de efficiëntie bij het uitvoeren van B2B-transacties.
* Zijn eenvoudig te maken, te beheren en te gebruiken voor het bouwen van bedrijfsintegratieoplossingen.

In dit artikel ziet u hoe u een AS2-, EDIFACT- of X12-overeenkomst maakt die u gebruiken bij het bouwen van bedrijfsintegratieoplossingen voor B2B-scenario's met behulp van het [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) en Azure Logic [Apps.](../logic-apps/logic-apps-overview.md) Nadat u een overeenkomst hebt gemaakt, u vervolgens de AS2-, EDIFACT- of X12-connectors gebruiken voor het uitwisselen van B2B-berichten.

Zie [Exchange RosettaNet-berichten](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)om overeenkomsten te maken voor het uitwisselen van RosettaNet-berichten.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) voor het opslaan van uw overeenkomst en andere B2B-artefacten. Dit integratieaccount moet zijn gekoppeld aan uw Azure-abonnement.

* Ten minste twee [handelspartners](../logic-apps/logic-apps-enterprise-integration-partners.md) die u al hebt gemaakt in uw integratieaccount. Een overeenkomst vereist zowel een gastpartner als een gastpartner. Beide partners moeten dezelfde kwalificatie voor 'bedrijfsidentiteit' gebruiken als de overeenkomst die u wilt maken, zoals AS2, X12 of EDIFACT.

* Optioneel: de logische app waarin u uw overeenkomst wilt gebruiken en een trigger waarmee de werkstroom van uw logische app wordt gestart. Om gewoon je integratieaccount en B2B-artefacten te maken, heb je geen logische app nodig. Voordat uw logica-app de B2B-artefacten in uw integratieaccount kan gebruiken, moet u uw integratieaccount echter koppelen aan uw logica-app. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Afspraken maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
Selecteer **alle services**in het hoofdmenu van Azure . Voer in het zoekvak 'integratie' in als filter. Selecteer deze bron in de resultaten: **Integratieaccounts**

   ![Uw integratieaccount zoeken](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Selecteer **onder Integratieaccounts**het integratieaccount waar u de overeenkomst wilt maken.

   ![Selecteer het integratieaccount waar u de overeenkomst wilt maken](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. Kies in het rechterdeelvenster onder **Componenten**de tegel **Overeenkomsten.**

   ![Kies 'Overeenkomsten'](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Kies Onder **Overeenkomsten**de optie **Toevoegen**. Geef in het deelvenster **Toevoegen** informatie over uw overeenkomst, bijvoorbeeld:

   ![Kies 'Toevoegen'](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | <*overeenkomstnaam*> | De naam voor uw overeenkomst |
   | **Type overeenkomst** | Ja | **AS2,** **X12**of **EDIFACT** | Het protocoltype voor uw overeenkomst. Wanneer u uw overeenkomstbestand maakt, moet de inhoud in dat bestand overeenkomen met het type overeenkomst. | |  
   | **Gastpartner** | Ja | <*host-partner-naam*> | De hostpartner vertegenwoordigt de organisatie die de overeenkomst specificeert |
   | **Host identiteit** | Ja | <*host-partner-id*> | De id van de hostpartner |
   | **Gastpartner** | Ja | <*gast-partner-naam*> | De gastpartner vertegenwoordigt de organisatie die zaken doet met de gastpartner |
   | **Gastidentiteit** | Ja | <*gast-partner-id*> | De id van de gastpartner |
   | **Instellingen voor ontvangen** | Varieert | Varieert | Deze eigenschappen geven aan hoe de hostpartner alle binnenkomende berichten van de gastpartner in de overeenkomst ontvangt. Zie voor meer informatie het desbetreffende type overeenkomst: <p>- [AS2-berichtinstellingen](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [INSTELLINGEN voor EDIFACT-berichten](logic-apps-enterprise-integration-edifact.md) <br>- [X12-berichtinstellingen](logic-apps-enterprise-integration-x12.md) |
   | **Verzendinstellingen** | Varieert | Varieert | Deze eigenschappen geven aan hoe de hostpartner alle uitgaande berichten naar de gastpartner in de overeenkomst verzendt. Zie voor meer informatie het desbetreffende type overeenkomst: <p>- [AS2-berichtinstellingen](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [INSTELLINGEN voor EDIFACT-berichten](logic-apps-enterprise-integration-edifact.md) <br>- [X12-berichtinstellingen](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Wanneer u klaar bent met het maken van uw overeenkomst, kiest u op de pagina **Toevoegen** de optie **OK**en keert u terug naar uw integratieaccount.

   De **lijst Overeenkomsten** toont nu uw nieuwe overeenkomst.

## <a name="edit-agreements"></a>Overeenkomsten bewerken

1. Selecteer in de [Azure-portal](https://portal.azure.com)in het hoofdmenu van Azure de optie **Alle services**.

1. Voer in het zoekvak 'integratie' in als filter. Selecteer deze bron in de resultaten: **Integratieaccounts**

1. Selecteer **onder Integratieaccounts**het integratieaccount met de overeenkomst die u wilt bewerken.

1. Kies in het rechterdeelvenster onder **Componenten**de tegel **Overeenkomsten.**

1. Selecteer **onder Overeenkomsten**uw overeenkomst en kies **Bewerken**.

1. Breng en sla vervolgens uw wijzigingen op.

## <a name="delete-agreements"></a>Overeenkomsten verwijderen

1. Selecteer in de [Azure-portal](https://portal.azure.com)in het hoofdmenu van Azure de optie **Alle services**.

1. Voer in het zoekvak 'integratie' in als filter. Selecteer deze bron in de resultaten: **Integratieaccounts**

1. Selecteer **onder Integratieaccounts**het integratieaccount met de overeenkomst die u wilt verwijderen.

1. Kies in het rechterdeelvenster onder **Componenten**de tegel **Overeenkomsten.**

1. Selecteer **onder Overeenkomsten**uw overeenkomst en kies **Verwijderen**.

1. Controleer of u de geselecteerde overeenkomst wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [AS2-berichten uitwisselen](logic-apps-enterprise-integration-as2.md)
* [EDIFACT-berichten uitwisselen](logic-apps-enterprise-integration-edifact.md)
* [X12-berichten uitwisselen](logic-apps-enterprise-integration-x12.md)
