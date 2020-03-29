---
title: Handelspartners toevoegen voor B2B-integraties
description: Handelspartners maken in uw integratieaccount om te gebruiken met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792436"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Handelspartners toevoegen aan integratieaccounts voor Azure Logic Apps

In [Azure Logic Apps](../logic-apps/logic-apps-overview.md)u b2b-integratieworkflows (business-to-business) maken met behulp van een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) met uw logische apps. Als u uw organisatie en anderen wilt vertegenwoordigen, maakt en voegt u handelspartners toe als artefacten aan uw integratieaccount. Partners zijn entiteiten die deelnemen aan B2B-transacties en berichten met elkaar uitwisselen.

Voordat u deze partners maakt, moet u informatie bespreken en delen met uw partners over het identificeren en valideren van de berichten die de andere verzenden. Nadat u het eens bent geworden over deze gegevens, bent u klaar om partners in uw integratieaccount aan te maken.

## <a name="partner-roles-in-integration-accounts"></a>Partnerrollen in integratieaccounts

Als u de details wilt definiëren over de berichten die met uw partners worden uitgewisseld, maakt en voegt u [overeenkomsten](../logic-apps/logic-apps-enterprise-integration-agreements.md) als artefacten toe aan uw integratieaccount. Voor overeenkomsten zijn ten minste twee partners in uw integratieaccount vereist. Uw organisatie is altijd de *gastpartner* in de overeenkomst. De organisatie die berichten uitwisselt met uw organisatie is de *gastpartner.* Een gastpartner kan een ander bedrijf zijn, of zelfs een afdeling in uw eigen organisatie. Nadat u deze partners hebt toegevoegd, u een overeenkomst maken.

In een overeenkomst geeft u de details op voor het verwerken van binnenkomende en uitgaande berichten vanuit het perspectief van de hostpartner. Voor binnenkomende berichten geven de **instellingen voor ontvangen** aan hoe de hostpartner berichten ontvangt van de gastpartner in de overeenkomst. Voor uitgaande berichten geven de **verzendinstellingen** op hoe de hostpartner berichten naar de gastpartner verzendt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) voor het opslaan van uw partners, overeenkomsten en andere B2B-artefacten. Dit integratieaccount moet zijn gekoppeld aan uw Azure-abonnement.

## <a name="create-partner"></a>Partner maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **alle services**in het hoofdmenu van Azure . Typ in het zoekvak 'integratie' en selecteer **Integratieaccounts**.

   ![Selecteer 'Integratieaccounts'](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Selecteer **onder Integratieaccounts**het integratieaccount waar u uw partners wilt toevoegen.

   ![Integratieaccount selecteren](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Kies de tegel **Partners.**

   ![De tegel 'Partners' kiezen](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. Kies Onder **Partners**de optie **Toevoegen**. Geef **onder Partner toevoegen**de gegevens van de partner op, zoals beschreven in de onderstaande tabel.

   ![Kies 'Toevoegen' en geef partnergegevens op](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam** | Ja | De naam van de partner |
   | **Kwalificatie** | Ja | Het authenticerende orgaan dat unieke bedrijfsidentiteiten biedt aan organisaties, bijvoorbeeld **D-U-N-S (Dun & Bradstreet).** <p>Partners kunnen kiezen voor een wederzijds gedefinieerde bedrijfsidentiteit. Selecteer voor deze scenario's **Wederzijds gedefinieerd** voor EDIFACT of Wederzijds **gedefinieerd (X12)** voor X12. <p>Selecteer voor RosettaNet alleen **DUNS,** wat de standaard is. |
   | **Waarde** | Ja | Een waarde die de documenten identificeert die uw logische apps ontvangen. <p>Voor RosettaNet moet deze waarde een negencijferig getal zijn dat overeenkomt met het DUNS-nummer. |
   ||||

   > [!NOTE]
   > Voor partners die RosettaNet gebruiken, u aanvullende informatie opgeven door deze partners eerst te maken en [deze vervolgens te bewerken.](#edit-partner)

1. Als u klaar bent, kiest u **Done**.

   Uw nieuwe partner wordt nu weergegeven in de **lijst Partners.** De tegel **Partners** werkt ook het huidige aantal partners bij.

   ![Nieuwe partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Partner bewerken

1. Zoek en selecteer uw integratieaccount in de [Azure-portal.](https://portal.azure.com)
Kies de tegel **Partners.**

   ![De tegel 'Partners' kiezen](./media/logic-apps-enterprise-integration-partners/edit.png)

1. Selecteer **onder Partners**de partner die u wilt bewerken en kies **Bewerken**. Breng **onder Bewerken**uw wijzigingen aan.

   ![Wijzigingen aanbrengen en opslaan](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Voor RosettaNet u onder **RosettaNet Partner Properties**deze aanvullende informatie opgeven:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Partnerclassificatie** | Nee | Het organisatietype van de partner |
   | **Supply chain code** | Nee | De supply chain code van de partner, bijvoorbeeld "Informatietechnologie" of "Elektronische componenten" |
   | **Naam van contactpersoon** | Nee | De naam van de partner |
   | **Email** | Nee | Het e-mailadres van de partner |
   | **Fax** | Nee | Het faxnummer van de partner |
   | **Telefoon** | Nee | Het telefoonnummer van de partner |
   ||||

1. Wanneer u klaar bent, kiest **u OK** om uw wijzigingen op te slaan.

## <a name="delete-partner"></a>Partner verwijderen

1. Zoek en selecteer uw integratieaccount in de [Azure-portal.](https://portal.azure.com) Kies de tegel **Partners.**

   ![De tegel 'Partners' kiezen](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. Selecteer **onder Partners**de partner die u wilt verwijderen. Kies **Verwijderen**.

   ![Partner verwijderen](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [overeenkomsten](../logic-apps/logic-apps-enterprise-integration-agreements.md)