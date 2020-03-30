---
title: Verbinding maken met Dynamics 365
description: Records maken en beheren met Dynamics 365 (online) REST API's en Azure Logic Apps
services: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9837b68fbfba783a468712d8ba1883b198af4954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789884"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Dynamics 365-records beheren met Azure Logic Apps

Met Azure Logic Apps en de Dynamics 365-connector kunt u geautomatiseerde taken en werkstromen maken op basis van uw records in Dynamics 365. Met uw Dynamics 365-account kunnen met werkstromen records worden gemaakt, items bijgewerkt, records geretourneerd en nog veel meer. U acties opnemen in uw logische apps die reacties van Dynamics 365 ontvangen en de uitvoer beschikbaar maken voor andere acties. Wanneer een item bijvoorbeeld wordt bijgewerkt in Dynamics 365, u een e-mail verzenden met Office 365.

In dit artikel ziet u hoe u een logische app maken die een taak maakt in Dynamics 365 wanneer er een nieuwe leadrecord wordt gemaakt in Dynamics 365.
Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [Dynamics 365-account](https://dynamics.microsoft.com)

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt hebben tot uw Dynamics 365-account. Als u uw logica-app wilt starten met een Dynamics 365-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

## <a name="add-dynamics-365-trigger"></a>Dynamics 365-trigger toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Voeg eerst een Dynamics 365-trigger toe die wordt geactiveerd wanneer er een nieuwe leadrecord wordt weergegeven in Dynamics 365.

1. Open in de [Azure-portal](https://portal.azure.com)uw lege logische app in Logic App Designer, als deze nog niet is geopend.

1. Typ in het zoekvak 'Dynamics 365' als filter. Selecteer in dit voorbeeld onder de lijst triggers deze trigger: **Wanneer een record wordt gemaakt**

   ![Trigger selecteren](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Als u wordt gevraagd zich aan te melden bij Dynamics 365, meldt u zich nu aan.

1. Geef deze triggerdetails op:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam van de organisatie** | Ja | De naam voor het dynamics 365-exemplaar van uw organisatie om bijvoorbeeld 'Contoso' te controleren |
   | **Naam van entiteit** | Ja | De naam voor de entiteit om te controleren, bijvoorbeeld 'Leads', | 
   | **Frequentie** | Ja | De tijdseenheid die met intervallen moet worden gebruikt bij het controleren op updates met betrekking tot de trigger |
   | **Interval** | Ja | Het aantal seconden, minuten, uren, dagen, weken of maanden dat vóór de volgende controle voorbij gaat |
   ||| 

   ![Details activeren](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dynamics 365-actie toevoegen

Voeg nu de dynamica 365-actie toe die een taakrecord maakt voor de nieuwe leadrecord.

1. Kies **Nieuwe stap**onder uw trigger.

1. Typ in het zoekvak 'Dynamics 365' als filter. Selecteer deze actie in de lijst met acties: **Een nieuwe record maken**

   ![Actie selecteren](./media/connectors-create-api-crmonline/select-action.png)

1. Geef de volgende actiegegevens op:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam van de organisatie** | Ja | De instantie Dynamics 365 waarbij u de record wilt maken, die niet dezelfde instantie in uw trigger hoeft te zijn, maar in dit voorbeeld 'Contoso' is |
   | **Naam van entiteit** | Ja | De entiteit waarin u de record wilt maken, bijvoorbeeld 'Taken', |
   | | |

   ![Actiedetails](./media/connectors-create-api-crmonline/action-details.png)

1. Wanneer het vak **Onderwerp** in uw actie wordt weergegeven, klikt u in het vak **Onderwerp** zodat de lijst met dynamische inhoud wordt weergegeven. Selecteer in deze lijst de veldwaarden die u wilt opnemen in de taakrecord die is gekoppeld aan de nieuwe leadrecord:

   | Veld | Beschrijving |
   |-------|-------------|
   | **Achternaam** | De achternaam van de lead als primaire contactpersoon in de record |
   | **Onderwerp** | De beschrijvende naam voor het lood in de record |
   | | |

   ![Details van taakrecord](./media/connectors-create-api-crmonline/create-record-details.png)

1. Kies Opslaan voor uw logica-app op de werkbalk **van** de ontwerper. 

1. Als u de logische app handmatig wilt starten, kiest u op de werkbalk van de ontwerper de optie **Uitvoeren**.

   ![Logische app uitvoeren](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Maak nu een leadrecord in Dynamics 365, zodat u de workflow van uw logische app activeren.

## <a name="add-filter-or-query"></a>Filter of query toevoegen

Als u wilt opgeven hoe u gegevens filtert in een actie Dynamics 365, kiest u Geavanceerde opties in die actie **weergeven.** U vervolgens een filter of volgorde per query toevoegen.
U bijvoorbeeld een filterquery gebruiken om alleen de actieve accounts op te halen en deze records op accountnaam te bestellen. Voer voor deze taak de volgende stappen uit:

1. Voer **onder Filterquery**deze OData-filterquery in:`statuscode eq 1`

2. Selecteer **Accountnaam**onder **Volgorde op**, wanneer de lijst met dynamische inhoud wordt weergegeven . 

   ![Filter en volgorde opgeven](./media/connectors-create-api-crmonline/advanced-options.png)

Zie de queryopties voor dynamics 365 Customer Engagement Web API-systeemquery's voor meer informatie:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Best practices voor geavanceerde opties

Wanneer u een waarde opgeeft voor een veld in een actie of trigger, moet het gegevenstype van de waarde overeenkomen met het veldtype, ongeacht of u handmatig de waarde invoert of de waarde selecteert in de lijst dynamische inhoud.

In deze tabel worden enkele veldtypen en de vereiste gegevenstypen voor hun waarden beschreven.

| Veldtype | Vereist gegevenstype | Beschrijving | 
|------------|--------------------|-------------|
| Tekstvelden | Eén tekstregel | Deze velden vereisen één regel tekst of dynamische inhoud met het teksttype. <p><p>*Voorbeeldvelden*: **Beschrijving** en **categorie** | 
| Velden voor gehele getallen | Geheel getal | Sommige velden vereisen gehele of dynamische inhoud met het gehele getal. <p><p>*Voorbeeldvelden*: **Percentage voltooid** en **duur** | 
| Datumvelden | Datum en tijd | Voor sommige velden is een datum vereist met de mm/dd/yyyy-indeling of dynamische inhoud met het datumtype. <p><p>*Voorbeeldvelden*: **Gemaakt op**, **begindatum**, **werkelijke start,** **werkelijke einddatum**en **vervaldatum** | 
| Velden waarvoor zowel een record-id als een opzoektype nodig zijn | Primaire sleutel | Voor sommige velden die verwijzen naar een andere entiteitsrecord, is zowel een record-id als een opzoektype vereist. | 
||||

Deze veldtypen worden hier uitgebreid met voorbeeldvelden in Dynamics 365-triggers en -acties waarvoor zowel een record-id als het opzoektype vereist zijn. Deze vereiste betekent waarden die u selecteert in de dynamische lijst.

| Veld | Beschrijving |
|-------|-------------|
| **Eigenaar** | Het moet een geldige gebruikersnaam of een teamrecord-id zijn. |
| **Type eigenaar** | Moet een `systemusers` `teams`van beide zijn of. |
| **Betreffende** | Het moet een geldig record-id zijn, zoals een account-id of een record-id. |
| **Met betrekking tot type** | Moet een opzoektype zijn, zoals `accounts` of `contacts`. |
| **Klant** | Het moet een geldig record-id zijn, zoals een account-id of een record-id. |
| **Type klant** | Moet het opzoektype zijn, zoals `accounts` of `contacts`. |
|||

In dit voorbeeld maakt de actie met de naam **Een nieuwe record maken** een nieuwe taakrecord:

![Taakrecord maken met record---i-ups en opzoektypen](./media/connectors-create-api-crmonline/create-record-advanced.png)

Met deze actie wordt de taakrecord aan een specifieke gebruikersnaam of teamrecord-id toegesteld op basis van de record-id in het veld **Eigenaar** en het opzoektype in het veld **Type eigenaar:**

![Eigenaar record ID en opzoektype](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Met deze actie wordt ook een accountrecord toegevoegd die is gekoppeld aan de record-id die is toegevoegd in het veld **Met betrekking** en het opzoektype in het veld **Over tekst:**

![Met betrekking tot record-ID en opzoektype](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Record-id zoeken

Voer de volgende stappen uit om een record-id te vinden:

1. Open in Dynamics 365 een record, zoals een accountrecord.

2. Kies op de werkbalk Acties een van de volgende stappen:

   * Kies **Pop Out**. ![pop-out record](./media/connectors-create-api-crmonline/popout-record.png) 
   * Kies **E-MAIL Een LINK** zodat u de volledige URL naar uw standaard e-mailprogramma kopiëren.

   De record-id wordt weergegeven `%7b` `%7d` in de URL tussen de tekens en de codering:

   ![Record-id zoeken](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Mislukte uitvoeringen oplossen

Als u mislukte stappen in uw logica-app wilt zoeken en controleren, u de uitvoeringen van uw logische app, status, invoer, uitvoer enzovoort bekijken.

1. Selecteer **Overzicht**in de Azure-portal in het hoofdmenu van uw logische app . Selecteer in de sectie **Geschiedenis uitvoeren,** waarin alle runstatussen voor uw logische app worden weergegeven, een mislukte run voor meer informatie.

   ![Status logische app-run](./media/connectors-create-api-crmonline/run-history.png)

1. Vouw een mislukte stap uit, zodat u meer details bekijken.

   ![Mislukte stap uitvouwen](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Bekijk de details van de stap, zoals de ingangen en uitgangen, die u kunnen helpen de oorzaak achter de storing te vinden.

   ![Mislukte stap - ingangen en uitgangen](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Zie [Logische app-fouten diagnosticeren](../logic-apps/logic-apps-diagnosing-failures.md)voor meer informatie over het oplossen van logische apps .

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](/connectors/dynamicscrmonline/)de connector voor technische details, zoals triggers, acties en limieten, zoals beschreven in het OpenAPI-bestand (voorheen Swagger).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
