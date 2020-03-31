---
title: Beveiligingsbewerkingen & Microsoft Graph Security integreren en beheren
description: De bescherming, detectie en reactie van uw app verbeteren met Microsoft Graph Security & Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: b4f51b192d1a7c0ee14a769321793753e8217dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598830"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Bescherming tegen bedreigingen verbeteren door beveiligingsbewerkingen te integreren met Microsoft Graph Security & Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de Microsoft Graph [Security-connector](https://docs.microsoft.com/graph/security-concept-overview) u de manier waarop uw app bedreigingen detecteert, beschermt en reageert, verbeteren door geautomatiseerde workflows te maken voor de integratie van Microsoft-beveiligingsproducten, -services en -partners. U bijvoorbeeld [playbooks](../security-center/security-center-playbooks.md) van Azure Security Center maken die microsoft Graph Security-entiteiten bewaken en beheren, zoals waarschuwingen. Hier volgen enkele scenario's die worden ondersteund door de Microsoft Graph Security-connector:

* Ontvang waarschuwingen op basis van query's of op een waarschuwings-id. U bijvoorbeeld een lijst krijgen met waarschuwingen met hoge ernst.

* Waarschuwingen bijwerken. U bijvoorbeeld waarschuwingstoewijzingen bijwerken, opmerkingen toevoegen aan waarschuwingen of waarschuwingen taggen.

* Controleer wanneer waarschuwingen worden gemaakt of gewijzigd door [waarschuwingsabonnementen (webhooks) te](https://docs.microsoft.com/graph/api/resources/webhooks)maken.

* Beheer uw waarschuwingsabonnementen. U bijvoorbeeld actieve abonnementen krijgen, de vervaldatum voor een abonnement verlengen of abonnementen verwijderen.

De werkstroom van uw logische app kan acties gebruiken die reacties van de Microsoft Graph Security-connector ontvangen en die uitvoer beschikbaar maken voor andere acties in uw werkstroom. U ook andere acties in uw werkstroom gebruiken voor de uitvoer van de microsoft graph-connectoracties. Als u bijvoorbeeld waarschuwingen met hoge ernst ontvangt via de Microsoft Graph Security-connector, u deze waarschuwingen in een e-mailbericht verzenden met behulp van de Outlook-connector. 

Zie het overzicht van de [Microsoft Graph Security API](https://aka.ms/graphsecuritydocs)voor meer informatie over Microsoft Graph Security. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Zie [Wat is Flow?](https://flow.microsoft.com/) of Wat is PowerApps als u op zoek bent naar Microsoft Flow of [PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Als u de Microsoft Graph Security-connector wilt gebruiken, moet u de Azure Active Directory (AD) *expliciet tenantbeheerderstoestemming hebben gegeven*, als onderdeel van de [Microsoft Graph Security-verificatievereisten](https://aka.ms/graphsecurityauth). Voor deze toestemming is de toepassings-id en naam van de Microsoft Graph Security-connector vereist, die u ook vinden in de [Azure-portal:](https://portal.azure.com)

  | Eigenschap | Waarde |
  |----------|-------|
  | **Toepassingsnaam** | `MicrosoftGraphSecurityConnector` |
  | **Toepassings-id** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Als u toestemming wilt verlenen voor de connector, kan uw Azure AD-tenantbeheerder de volgende stappen volgen:

  * [Geef toestemming van de tenant-beheerder voor Azure AD-toepassingen](../active-directory/develop/v2-permissions-and-consent.md).

  * Tijdens de eerste uitvoering van uw logische app, kan uw app toestemming vragen aan uw Azure AD-tenantbeheerder via de [toestemmingservaring van de toepassing](../active-directory/develop/application-consent-experience.md).
   
* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waarin u toegang wilt krijgen tot uw Microsoft Graph Security-entiteiten, zoals waarschuwingen. Als u een Microsoft Graph Security-trigger wilt gebruiken, hebt u een lege logische app nodig. Als u een Beveiligingsactie van Microsoft Graph wilt gebruiken, hebt u een logische app nodig die begint met de juiste trigger voor uw scenario.

## <a name="connect-to-microsoft-graph-security"></a>Verbinding maken met Microsoft Graph-beveiliging 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Voeg voor lege logische apps de trigger en eventuele andere acties toe die u wilt voordat u een Microsoft Graph Security-actie toevoegt.

   -of-

   Selecteer **Nieuwe stap**voor bestaande logische apps onder de laatste stap waarin u een beveiligingsactie van Microsoft Graph wilt toevoegen.

   -of-

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. Selecteer het plusteken (+) dat wordt weergegeven en selecteer **Een actie toevoegen**.

1. Voer in het zoekvak 'microsoft graph security' in als uw filter. Selecteer in de lijst met acties de gewenste actie.

1. Meld u aan met uw Microsoft Graph-beveiligingsreferenties.

1. Geef de benodigde details voor uw geselecteerde actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="add-triggers"></a>Triggers toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een specifieke gebeurtenis plaatsvindt of wanneer aan een specifieke voorwaarde is voldaan. Elke keer dat de trigger wordt geactiveerd, maakt de Logic Apps-engine een logische app-instantie en wordt de werkstroom van uw app uitgevoerd.

> [!NOTE] 
> Wanneer een trigger wordt geactiveerd, verwerkt de trigger alle nieuwe waarschuwingen. Als er geen waarschuwingen worden ontvangen, wordt de triggerrun overgeslagen. De volgende triggerpoll gebeurt op basis van het herhalingsinterval dat u opgeeft in de eigenschappen van de trigger.

In dit voorbeeld ziet u hoe u een werkstroom voor logische apps starten wanneer er nieuwe waarschuwingen naar uw app worden verzonden.

1.  Maak in de Azure-portal of Visual Studio een lege logische app, waarmee de Logic App Designer wordt geopend. In dit voorbeeld wordt de Azure-portal gebruikt.

1.  Voer op de ontwerper in het zoekvak 'microsoft graph security' in als uw filter. Selecteer deze trigger in de lijst triggers: **Op alle nieuwe waarschuwingen**

1.  Geef in de trigger informatie over de waarschuwingen die u wilt controleren. Open de lijst **Nieuwe parameter toevoegen** voor meer eigenschappen en selecteer een parameter om die eigenschap aan de trigger toe te voegen.

   | Eigenschap | Eigenschap (JSON) | Vereist | Type | Beschrijving |
   |----------|-----------------|----------|------|-------------|
   | **Interval** | `interval` | Ja | Geheel getal | Een positief geheel getal dat beschrijft hoe vaak de werkstroom wordt uitgevoerd op basis van de frequentie. Hier zijn de minimale en maximale intervallen: <p><p>- Maand: 1-16 maanden <br>- Dag: 1-500 dagen <br>- Uur: 1-12.000 uur <br>- Minuut: 1-72.000 minuten <br>- Tweede: 1-9.999.999 seconden <p>Als het interval bijvoorbeeld 6 is en de frequentie 'Maand' is, is de herhaling om de 6 maanden. |
   | **Frequentie** | `frequency` | Ja | Tekenreeks | De eenheid van de tijd voor de herhaling: **Tweede**, **Minuut**, **Uur**, **Dag,** **Week,** of **Maand** |
   | **Tijdzone** | `timeZone` | Nee | Tekenreeks | Geldt alleen wanneer u een begintijd opgeeft omdat deze trigger geen [UTC-verschuiving](https://en.wikipedia.org/wiki/UTC_offset)accepteert. Selecteer de tijdzone die u wilt toepassen. |
   | **Begintijd** | `startTime` | Nee | Tekenreeks | Geef een begindatum en -tijd op in deze indeling: <p><p>YYYY-MM-DDThh:mm:ss als u een tijdzone selecteert <p>-of- <p>YYYY-MM-DDThh:mm:ssZ als u geen tijdzone selecteert <p>Als u bijvoorbeeld 18 september 2017 om 14:00 uur wilt, geeft u '2017-09-18T14:00:00' op en selecteert u een tijdzone zoals Pacific Standard Time. Of geef '2017-09-18T14:00:00Z' op zonder tijdzone. <p>**Let op:** Deze begintijd heeft een maximum van 49 jaar in de toekomst en moet de [ISO 8601-datumtijdspecificatie](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) in [utc-datumtijdnotatie](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)volgen, maar zonder [utc-offset](https://en.wikipedia.org/wiki/UTC_offset). Als u geen tijdzone selecteert, moet u de letter 'Z' aan het einde toevoegen zonder spaties. Deze "Z" verwijst naar de gelijkwaardige [nautische tijd](https://en.wikipedia.org/wiki/Nautical_time). <p>Voor eenvoudige schema's is de begintijd de eerste gebeurtenis, terwijl voor complexe schema's de trigger niet eerder dan de begintijd wordt geactiveerd. [*Op welke manieren kan ik de begindatum en -tijd gebruiken?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Wanneer u klaar bent, selecteert u op de werkbalk van de ontwerper de optie **Opslaan**.

1.  Ga nu verder met het toevoegen van een of meer acties aan uw logische app voor de taken die u wilt uitvoeren met de triggerresultaten.

## <a name="add-actions"></a>Acties toevoegen

Hier vindt u meer specifieke informatie over het gebruik van de verschillende acties die beschikbaar zijn met de Microsoft Graph Security-connector.

### <a name="manage-alerts"></a>Waarschuwingen beheren

Als u de meest recente resultaten wilt filteren, sorteren of de meest recente resultaten wilt opvragen, geeft u *alleen* de [ODATA-queryparameters op die worden ondersteund door Microsoft Graph.](https://docs.microsoft.com/graph/query-parameters) *Geef bijvoorbeeld* `https://graph.microsoft.com/v1.0/security/alerts`niet de volledige basis-URL of de HTTP-actie of de `GET` bewerking op. `PATCH` Hier is een specifiek voorbeeld dat de parameters voor een actie **Waarschuwingen ontvangen** weergeeft wanneer u een lijst met waarschuwingen met hoge ernst wilt:

`Filter alerts value as Severity eq 'high'`

Zie de [referentiedocumentatie voor microsoft graph security alerts](https://docs.microsoft.com/graph/api/alert-list)voor meer informatie over de query's die u met deze connector gebruiken. Als u verbeterde ervaringen met deze connector wilt maken, leest u meer over de waarschuwingen voor [schema-eigenschappen](https://docs.microsoft.com/graph/api/resources/alert) die de connector ondersteunt.

| Actie | Beschrijving |
|--------|-------------|
| **Ontvang waarschuwingen** | Ontvang waarschuwingen gefilterd op basis van een `Provider eq 'Azure Security Center' or 'Palo Alto Networks'`of meer [waarschuwingseigenschappen,](https://docs.microsoft.com/graph/api/resources/alert)bijvoorbeeld . | 
| **Ontvang een waarschuwing per id** | Ontvang een specifieke waarschuwing op basis van de waarschuwings-ID. | 
| **Waarschuwing bijwerken** | Een specifieke waarschuwing bijwerken op basis van de waarschuwings-ID. Zie de [bewerkbare eigenschappen voor waarschuwingen](https://docs.microsoft.com/graph/api/alert-update)om ervoor te zorgen dat u de vereiste en bewerkbare eigenschappen in uw aanvraag doorstaat. Als u bijvoorbeeld een waarschuwing wilt toewijzen aan een beveiligingsanalist, zodat deze kan worden onderzocht, u de **eigenschap Toegewezen van** de waarschuwing bijwerken. |
|||

### <a name="manage-alert-subscriptions"></a>Waarschuwingsabonnementen beheren

Microsoft Graph ondersteunt [*abonnementen*](https://docs.microsoft.com/graph/api/resources/subscription)of [*webhooks.*](https://docs.microsoft.com/graph/api/resources/webhooks) Als u abonnementen wilt ontvangen, bijwerken of verwijderen, geeft u de [ODATA-queryparameters die door Microsoft Graph worden ondersteund,](https://docs.microsoft.com/graph/query-parameters) op aan de microsoft graph-entiteit sbouwen en neemt u de ODATA-query op. `security/alerts` *Neem* bijvoorbeeld de basis-URL niet `https://graph.microsoft.com/v1.0`op. Gebruik in plaats daarvan de indeling in dit voorbeeld:

`security/alerts?$filter=status eq 'New'`

| Actie | Beschrijving |
|--------|-------------|
| **Abonnementen maken** | [Maak een abonnement](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) dat u op de hoogte stelt van eventuele wijzigingen. U dit abonnement filteren op de specifieke waarschuwingstypen die u wilt. U bijvoorbeeld een abonnement maken dat u op de hoogte stelt van waarschuwingen met hoge ernst. |
| **Actieve abonnementen kopen** | [Ontvang niet-verlopen abonnementen](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Abonnement bijwerken** | [Werk een abonnement bij](https://docs.microsoft.com/graph/api/subscription-update) door de abonnements-ID op te geven. Als u bijvoorbeeld uw abonnement wilt verlengen, `expirationDateTime` kunt u de eigenschap van het abonnement bijwerken. | 
| **Abonnement verwijderen** | [Een abonnement verwijderen](https://docs.microsoft.com/graph/api/subscription-delete) door de abonnements-ID op te geven. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Indicatoren voor bedreigingsinformatie beheren

Als u de meest recente resultaten wilt filteren, sorteren of de meest recente resultaten wilt opvragen, geeft u *alleen* de [ODATA-queryparameters op die worden ondersteund door Microsoft Graph.](https://docs.microsoft.com/graph/query-parameters) *Geef bijvoorbeeld* `https://graph.microsoft.com/beta/security/tiIndicators`niet de volledige basis-URL of de HTTP-actie of de `GET` bewerking op. `PATCH` Hier is een specifiek voorbeeld dat de parameters voor een actie Get `DDoS` **tiIndicators** weergeeft wanneer u een lijst met het type bedreiging wilt:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Zie ['Optionele queryparameters' in de referentiedocumentatie voor microsoft graph-beveiligingsinformatieindicatoren](https://docs.microsoft.com/graph/api/tiindicators-list?view=graph-rest-beta&tabs=http)voor meer informatie over de query's die u met deze connector gebruiken. Als u verbeterde ervaringen met deze connector wilt opbouwen, leest u meer over de [threat intelligence-indicator voor schema-eigenschappen](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta) die de connector ondersteunt.

| Actie | Beschrijving |
|--------|-------------|
| **Informatie-indicatoren voor bedreigingsinformatie** | Get tiIndicators gefilterd op basis van een of meer [tiIndicator-eigenschappen,](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta)bijvoorbeeld`threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Krijg threat intelligence indicator per ID** | Krijg een specifieke tiIndicator op basis van de tiIndicator ID. | 
| **Threat Intelligence-indicator maken** | Maak een nieuwe tiIndicator door te posten in de tiIndicators collectie. Als u ervoor wilt zorgen dat u de vereiste eigenschappen in uw aanvraag doorgeeft, raadpleegt u de [vereiste eigenschappen voor het maken van tiIndicator.](https://docs.microsoft.com/graph/api/tiindicators-post?view=graph-rest-beta&tabs=http) |
| **Meerdere threat intelligence-indicatoren indienen** | Maak meerdere nieuwe tiIndicators door het plaatsen van een tiIndicators collectie. Als u ervoor wilt zorgen dat u de vereiste eigenschappen in uw aanvraag doorgeeft, raadpleegt u de [vereiste eigenschappen voor het indienen van meerdere tiIndicators.](https://docs.microsoft.com/graph/api/tiindicator-submittiindicators?view=graph-rest-beta&tabs=http) |
| **Indicator voor bedreigingsinformatie bijwerken** | Update een specifieke tiIndicator op basis van de tiIndicator ID. Zie de [bewerkbare eigenschappen voor tiIndicator](https://docs.microsoft.com/graph/api/tiindicator-update?view=graph-rest-beta&tabs=http)om ervoor te zorgen dat u de vereiste en bewerkbare eigenschappen in uw aanvraag passeert. Als u bijvoorbeeld de actie wilt bijwerken die moet worden toegepast als de indicator is gekoppeld vanuit het targetProduct-beveiligingsprogramma, u de **actieeigenschap** van de tiIndicator bijwerken. |
| **Meerdere threat intelligence indicatoren bijwerken** | Meerdere tiIndicators bijwerken. Als u de vereiste eigenschappen in uw aanvraag wilt doorgeven, raadpleegt u de [vereiste eigenschappen voor het bijwerken van meerdere tiIndicators.](https://docs.microsoft.com/graph/api/tiindicator-updatetiindicators?view=graph-rest-beta&tabs=http) |
| **Threat Intelligence-indicator verwijderen op ID** | Verwijder een specifieke tiIndicator op basis van de tiIndicator ID. |
| **Meerdere threat intelligence-indicatoren verwijderen door iD's** | Verwijder meerdere tiIndicators door hun ID's. Als u ervoor wilt zorgen dat u de vereiste eigenschappen in uw aanvraag doorgeeft, raadpleegt u de [vereiste eigenschappen voor het verwijderen van meerdere tiIndicators door id's.](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicators?view=graph-rest-beta&tabs=http) |
| **Meerdere threat intelligence-indicatoren verwijderen door externe-geïdentificeerden** | Verwijder meerdere tiIndicators door de externe id's. Als u ervoor wilt zorgen dat u de vereiste eigenschappen in uw aanvraag doorgeeft, raadpleegt u de [vereiste eigenschappen voor het verwijderen van meerdere tiIndicators door externe id's.](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicatorsbyexternalid?view=graph-rest-beta&tabs=http) |
|||

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](https://aka.ms/graphsecurityconnectorreference)van de connector.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
