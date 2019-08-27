---
title: Verbinding maken met Dynamics 365-Azure Logic Apps
description: Records maken en beheren met Dynamics 365 (online) REST Api's en Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
manager: carmonm
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: ce83e6b1847a8f08467cb7877e517bdaace27953
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051021"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Dynamics 365-records beheren met Azure Logic Apps

Met Azure Logic Apps en de Dynamics 365-connector kunt u geautomatiseerde taken en werkstromen maken op basis van uw records in Dynamics 365. Met uw Dynamics 365-account kunnen met werkstromen records worden gemaakt, items bijgewerkt, records geretourneerd en nog veel meer. U kunt acties in uw Logic apps toevoegen die antwoorden krijgen van Dynamics 365 en de uitvoer beschikbaar maken voor andere acties. Wanneer bijvoorbeeld een item wordt bijgewerkt in Dynamics 365, kunt u een e-mail verzenden met behulp van Office 365.

In dit artikel wordt beschreven hoe u een logische app kunt maken waarmee een taak wordt gemaakt in Dynamics 365 wanneer een nieuwe lead record wordt gemaakt in Dynamics 365.
Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [Dynamics 365-account](https://dynamics.microsoft.com)

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw Dynamics 365-account. Als u uw logische app wilt starten met een Dynamics 365-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

## <a name="add-dynamics-365-trigger"></a>Dynamics 365-trigger toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Voeg eerst een Dynamics 365-trigger toe die wordt geactiveerd wanneer een nieuwe lead record wordt weer gegeven in Dynamics 365.

1. Open in de [Azure Portal](https://portal.azure.com)uw lege logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voer in het zoekvak ' Dynamics 365 ' in als uw filter. Voor dit voor beeld selecteert u onder de lijst triggers deze trigger: **Wanneer een record wordt gemaakt**

   ![Trigger selecteren](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Als u wordt gevraagd om u aan te melden bij Dynamics 365, meldt u zich nu aan.

1. Geef de volgende details op voor de trigger:

   | Eigenschap | Vereist | Description |
   |----------|----------|-------------|
   | **Organisatie naam** | Ja | De naam van het Dynamics 365-exemplaar van uw organisatie om te controleren, bijvoorbeeld ' Contoso ' |
   | **Naam van entiteit** | Ja | De naam van de entiteit die u wilt bewaken, bijvoorbeeld ' leads ' | 
   | **Frequentie** | Ja | De tijds eenheid die met intervallen moet worden gebruikt bij het controleren op updates met betrekking tot de trigger |
   | **Interval** | Ja | Het aantal seconden, minuten, uren, dagen, weken of maanden dat wordt door gegeven vóór de volgende controle |
   ||| 

   ![Details van trigger](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dynamics 365-actie toevoegen

Voeg nu de actie Dynamics 365 toe waarmee een taak record voor de nieuwe lead record wordt gemaakt.

1. Kies **nieuwe stap**onder de trigger.

1. Voer in het zoekvak ' Dynamics 365 ' in als uw filter. Selecteer in de lijst acties deze actie: **Een nieuwe record maken**

   ![Actie selecteren](./media/connectors-create-api-crmonline/select-action.png)

1. Geef de volgende actie Details op:

   | Eigenschap | Vereist | Description |
   |----------|----------|-------------|
   | **Organisatie naam** | Ja | Het Dynamics 365-exemplaar waar u de record wilt maken, dat niet hetzelfde exemplaar van de trigger moet zijn, maar ' Contoso ' in dit voor beeld is |
   | **Naam van entiteit** | Ja | De entiteit waar u de record wilt maken, bijvoorbeeld ' taken ' |
   | | |

   ![Actiedetails](./media/connectors-create-api-crmonline/action-details.png)

1. Wanneer het vak **onderwerp** wordt weer gegeven in uw actie, klikt u in het vak **onderwerp** , zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in deze lijst de veld waarden die moeten worden opgenomen in het taak record dat is gekoppeld aan de nieuwe lead record:

   | Veld | Description |
   |-------|-------------|
   | **Achternaam** | De achternaam van de lead als de primaire contact persoon in de record |
   | **Onderwerp** | De beschrijvende naam voor de lead in de record |
   | | |

   ![Details van taak record](./media/connectors-create-api-crmonline/create-record-details.png)

1. Kies **Opslaan** op de werk balk van de ontwerp functie voor uw logische app. 

1. Als u de logische app hand matig wilt starten, kiest u **uitvoeren**op de werk balk van de ontwerp functie.

   ![Logische app uitvoeren](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Maak nu een lead record in Dynamics 365 zodat u de werk stroom van uw logische app kunt activeren.

## <a name="add-filter-or-query"></a>Filter of query toevoegen

Kies **Geavanceerde opties weer geven** in deze actie om op te geven hoe u gegevens in een Dynamics 365-actie wilt filteren. U kunt vervolgens een filter of order by-query toevoegen.
U kunt bijvoorbeeld een filter query gebruiken om alleen de actieve accounts op te halen en deze records op account naam te rangschikken. Voer de volgende stappen uit voor deze taak:

1. Voer onder **filter query**deze OData-filter query in:`statuscode eq 1`

2. Selecteer onder sorteren op, wanneer de lijst **met**dynamische inhoud wordt weer gegeven, de **account naam**. 

   ![Filter en volg orde opgeven](./media/connectors-create-api-crmonline/advanced-options.png)

Zie voor meer informatie deze Dynamics 365 Customer engagement Web API-systeem query opties:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Aanbevolen procedures voor geavanceerde opties

Wanneer u een waarde opgeeft voor een veld in een actie of trigger, moet het gegevens type van de waarde overeenkomen met het veld type, ongeacht of u de waarde hand matig invoert of de waarde selecteert in de lijst met dynamische inhoud.

In deze tabel worden enkele veld typen en de vereiste gegevens typen voor hun waarden beschreven.

| Veldtype | Vereist gegevens type | Description | 
|------------|--------------------|-------------|
| Tekst velden | Eén tekst regel | Voor deze velden is één tekst regel of dynamische inhoud met het tekst type vereist. <p><p>*Voorbeeld velden*: **Beschrijving** en **categorie** | 
| Velden met gehele getallen | Geheel getal | Voor sommige velden is een geheel getal of dynamische inhoud vereist die het type geheel getal heeft. <p><p>*Voorbeeld velden*: **Percentage voltooid** en **duur** | 
| Datum velden | Datum en tijd | Voor sommige velden is een datum vereist met een notatie van mm/dd/jjjj of dynamische inhoud met het datum type. <p><p>*Voorbeeld velden*: **Gemaakt op**, **begin datum**, **werkelijke start**, **werkelijk einde**en **verval datum** | 
| Velden waarvoor een record-ID en een opzoek type zijn vereist | Primaire sleutel | Voor sommige velden die verwijzen naar een andere entiteits record zijn zowel een record-ID als een Zoek type vereist. | 
||||

Als u deze veld typen uitvouwt, ziet u voor beelden van velden in Dynamics 365-triggers en acties waarvoor zowel een record-ID als een opzoek type nodig zijn. Deze vereiste betekent dat waarden die u selecteert in de dynamische lijst, niet werken.

| Veld | Description |
|-------|-------------|
| **Eigenaar** | Moet een geldige gebruikers-ID of een team record-ID zijn. |
| **Type eigenaar** | Moet ofwel `systemusers` of `teams`zijn. |
| **Over** | Moet een geldige record-ID zijn, zoals een account-id of record-ID van de contact persoon. |
| **Betreft type** | Dit moet een opzoek type zijn, zoals `accounts` of `contacts`. |
| **De klant** | Moet een geldige record-ID zijn, zoals een account-id of record-ID van de contact persoon. |
| **Klant type** | Moet het type lookup zijn, zoals `accounts` of. `contacts` |
|||

In dit voor beeld maakt de actie met de naam **een nieuwe record maken** een nieuwe taak record:

![Taak record met record-Id's en zoek typen maken](./media/connectors-create-api-crmonline/create-record-advanced.png)

Met deze actie wordt de taak record toegewezen aan een specifieke gebruikers-ID of team record-ID, op basis van de record-ID in het veld **eigenaar** en het type zoek actie in het veld **type eigenaar** :

![Eigenaar record-ID en zoek type](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Met deze actie wordt ook een account record toegevoegd die is gekoppeld aan de record-ID die is toegevoegd in het veld **betreft** en het type zoek opdracht in het veld **betreft type** :

![Over record-ID en zoek type](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Record-ID zoeken

Voer de volgende stappen uit om een record-ID te vinden:

1. Open in Dynamics 365 een record, zoals een account record.

2. Kies een van de volgende stappen op de werk balk acties:

   * Kies **pop-out**. ![record popout](./media/connectors-create-api-crmonline/popout-record.png) 
   * Kies **een E-mail verzenden naar een koppeling** zodat u de volledige URL kunt kopiëren naar uw standaard e-mail programma.

   De record-id wordt weer gegeven in de `%7b` URL `%7d` tussen de tekens en de code ring:

   ![Record-ID zoeken](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Problemen oplossen met mislukte uitvoeringen

Als u de stappen in uw logische app wilt zoeken en controleren, kunt u de uitvoerings geschiedenis van de logische app bekijken, status, invoer, uitvoer, enzovoort.

1. Selecteer in het Azure Portal, in het hoofd menu van uw logische app, de optie **overzicht**. In de sectie **runs History** , waarin alle uitvoerings statussen voor uw logische app worden weer gegeven, selecteert u een mislukte uitvoering voor meer informatie.

   ![Uitvoerings status van logische app](./media/connectors-create-api-crmonline/run-history.png)

1. Vouw een mislukte stap uit zodat u meer informatie kunt bekijken.

   ![Uitvouw fout stap](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Bekijk de details van de stap, zoals de invoer en uitvoer, die u kunnen helpen de oorzaak van de fout te vinden.

   ![Mislukte stap-invoer en uitvoer](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Zie voor meer informatie over het oplossen van problemen met Logic apps storingen in [Logic app-fouten vaststellen](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentie pagina van de connector](/connectors/dynamicscrmonline/)voor technische details, zoals triggers, acties en limieten, zoals beschreven in het OpenAPI (voorheen Swagger)-bestand van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors
