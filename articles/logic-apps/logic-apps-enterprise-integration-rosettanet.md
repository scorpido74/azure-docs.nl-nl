---
title: RosettaNet berichten voor B2B integratie
description: RosettaNet-berichten uitwisselen in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792419"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Exchange RosettaNet-berichten voor B2B-bedrijfsintegratie in Azure Logic Apps

[RosettaNet](https://resources.gs1us.org) is een non-profit consortium dat standaardprocessen heeft opgezet voor het delen van bedrijfsinformatie. Deze normen worden vaak gebruikt voor supply chain processen en zijn wijdverbreid in de halfgeleider-, elektronica- en logistieke industrie. Het RosettaNet-consortium creëert en onderhoudt Partner Interface Processes (PIPs), die gemeenschappelijke definities van bedrijfsprocessen bieden voor alle RosettaNet-berichtenuitwisselingen. RosettaNet is gebaseerd op XML en definieert berichtrichtlijnen, interfaces voor bedrijfsprocessen en implementatiekaders voor communicatie tussen bedrijven.

In [Azure Logic Apps](../logic-apps/logic-apps-overview.md)helpt de RosettaNet-connector u bij het maken van integratieoplossingen die RosettaNet-standaarden ondersteunen. De connector is gebaseerd op RosettaNet Implementation Framework (RNIF) versie 2.0.01. RNIF is een open netwerkapplicatieframework waarmee zakelijke partners rosettanet-pip's gezamenlijk kunnen uitvoeren. Dit framework definieert de berichtstructuur, de behoefte aan bevestigingen, MIME-codering (Multipurpose Internet Mail Extensions) en de digitale handtekening.

Met name de connector biedt deze mogelijkheden:

* Codeer of ontvang RosettaNet-berichten.
* Decoderen of verzenden RosettaNet berichten.
* Wacht op de reactie en het genereren van melding van falen.

Voor deze mogelijkheden ondersteunt de connector alle PIP's die zijn gedefinieerd door RNIF 2.0.01. Communicatie met de partner kan synchroon of asynchroon zijn.

## <a name="rosettanet-concepts"></a>RosettaNet concepten

Hier zijn enkele concepten en termen die uniek zijn voor de RosettaNet-specificatie en belangrijk zijn bij het bouwen van rosettanet-gebaseerde integraties:

* **Pip**

  De RosettaNet-organisatie creëert en onderhoudt Partner Interface Processes (PIPs), die gemeenschappelijke definities van bedrijfsprocessen bieden voor alle RosettaNet-berichtenuitwisselingen. Elke PIP-specificatie biedt een DTD-bestand (documenttype definition) en een document voor berichtrichtlijnen. Het DTD-bestand definieert de berichtstructuur voor service-inhoud. In het document met berichtrichtsnoer, dat een html-bestand voor menselijk gebruik is, worden beperkingen op elementniveau gespecificeerd. Samen bieden deze bestanden een volledige definitie van het bedrijfsproces.

   PIP's worden gecategoriseerd door een bedrijfsfunctie op hoog niveau of cluster en een subfunctie of segment. '3A4' is bijvoorbeeld de PIP voor inkooporder, terwijl '3' de functie Orderbeheer is en '3A' de subfunctie Quote & Orderentry is. Zie voor meer informatie de [RosettaNet-site.](https://resources.gs1us.org)

* **Actie**

  Actieberichten zijn bedrijfsberichten die worden uitgewisseld tussen partners als onderdeel van een PIP.

* **Signaal**

   Een deel van een PIP, signaalberichten zijn bevestigingen die worden verzonden naar aanleiding van actieberichten.

* **Eén actie en dubbele actie**

  Voor een PIP met één actie is het enige antwoord een bevestigingssignaalbericht. Voor een PIP met dubbele actie ontvangt de initiator een antwoordbericht en antwoordt hij met een bevestiging naast de berichtstroom met één actie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) voor het opslaan van uw overeenkomst en andere B2B-artefacten. Dit integratieaccount moet zijn gekoppeld aan uw Azure-abonnement.

* Ten minste twee [partners](../logic-apps/logic-apps-enterprise-integration-partners.md) die zijn gedefinieerd in uw integratieaccount en geconfigureerd met de kwalificatie voor "DUNS" onder **Bedrijfsidentiteiten**

* Een PIP-procesconfiguratie, die nodig is om RosettaNet-berichten te verzenden of te ontvangen, in uw integratieaccount. De procesconfiguratie slaat alle PIP-configuratiekenmerken op. U dan verwijzen naar deze configuratie wanneer u een overeenkomst met de partner maakt. Zie [PIP-procesconfiguratie toevoegen](#add-pip)als u een PIP-procesconfiguratie wilt maken in uw integratieaccount.

* Optionele [certificaten](../logic-apps/logic-apps-enterprise-integration-certificates.md) voor het versleutelen, ontsleutelen of ondertekenen van de berichten die u uploadt naar het integratieaccount. Certificaten zijn alleen vereist als u ondertekening of versleuteling gebruikt.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Pip-procesconfiguratie toevoegen

Voer de volgende stappen uit om een PIP-procesconfiguratie toe te voegen aan uw integratieaccount:

1. Zoek en open uw integratieaccount in de [Azure-portal.](https://portal.azure.com)

1. Selecteer in het deelvenster **Overzicht** de **TEGEL RosettaNet PIP.**

   ![RosettaNet-tegel kiezen](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Kies Onder **RosettaNet PIP**de optie **Toevoegen**. Geef uw PIP-gegevens op.

   ![RosettaNet PIP-details toevoegen](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam** | Ja | Uw PIP-naam |
   | **PIP-code** | Ja | De PIP driecijferige code. Zie [RosettaNet PIPs voor](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)meer informatie. |
   | **PIP-versie** | Ja | Het PIP-versienummer, dat beschikbaar is op basis van de geselecteerde PIP-code |
   ||||

   Ga voor meer informatie over deze PIP-eigenschappen naar de [website van RosettaNet.](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)

1. Wanneer u klaar bent, kiest u **OK,** waardoor de PIP-configuratie wordt gemaakt.

1. Als u de procesconfiguratie wilt weergeven of bewerken, selecteert u de PIP en kiest **u Bewerken als JSON**.

   Alle procesconfiguratie-instellingen zijn afkomstig van de specificaties van de PIP. Logic Apps vult de meeste instellingen af met de standaardwaarden die de meest gebruikte waarden voor deze eigenschappen zijn.

   ![RosettaNet PIP-configuratie bewerken](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Controleer of de instellingen overeenkomen met de waarden in de juiste PIP-specificatie en voldoen aan uw bedrijfsbehoeften. Werk indien nodig de waarden in JSON bij en sla deze wijzigingen op.

## <a name="create-rosettanet-agreement"></a>RosettaNet-overeenkomst maken

1. Zoek en open uw integratieaccount in de [Azure-portal,](https://portal.azure.com)als deze nog niet is geopend.

1. Selecteer in het deelvenster **Overzicht** de tegel **Overeenkomsten.**

   ![Tegel Overeenkomsten kiezen](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Kies Onder **Overeenkomsten**de optie **Toevoegen**. Geef uw instemmingsgegevens op.

   ![Details van de overeenkomst toevoegen](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam** | Ja | De naam van de overeenkomst |
   | **Type overeenkomst** | Ja | Selecteer **RosettaNet**. |
   | **Gastpartner** | Ja | Een overeenkomst vereist zowel een gast- als gastpartner. De hostpartner vertegenwoordigt de organisatie die de overeenkomst configureert. |
   | **Host identiteit** | Ja | Een id voor de hostpartner |
   | **Gastpartner** | Ja | Een overeenkomst vereist zowel een gast- als gastpartner. De gastpartner vertegenwoordigt de organisatie die zaken doet met de gastpartner. |
   | **Gastidentiteit** | Ja | Een id voor de gastpartner |
   | **Instellingen voor ontvangen** | Varieert | Deze eigenschappen zijn van toepassing op alle berichten die door de hostpartner zijn ontvangen |
   | **Verzendinstellingen** | Varieert | Deze eigenschappen zijn van toepassing op alle berichten die door de hostpartner worden verzonden |  
   | **RosettaNet PIP referenties** | Ja | De PIP-verwijzingen naar de overeenkomst. Alle RosettaNet-berichten vereisen PIP-configuraties. |
   ||||

1. Als u uw overeenkomst wilt instellen voor het ontvangen van binnenkomende berichten van de gastpartner, selecteert u **Instellingen ontvangen**.

   ![Instellingen voor ontvangen](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Als u ondertekening of versleuteling voor binnenkomende berichten wilt inschakelen, moet onder **Berichten**de optie **Bericht worden ondertekend** of moet Bericht respectievelijk worden **versleuteld.**

      | Eigenschap | Vereist | Beschrijving |
      |----------|----------|-------------|
      | **Bericht moet worden ondertekend** | Nee | Teken binnenkomende berichten met het geselecteerde certificaat. |
      | **Certificaat** | Ja, als ondertekenen is ingeschakeld | Het certificaat dat u wilt gebruiken voor ondertekening |
      | **Versleuteling van berichten inschakelen** | Nee | Inkomende berichten versleutelen met het geselecteerde certificaat. |
      | **Certificaat** | Ja, als versleuteling is ingeschakeld | Het certificaat dat moet worden gebruikt voor versleuteling |
      ||||

   1. Selecteer onder elke selectie het desbetreffende [certificaat](./logic-apps-enterprise-integration-certificates.md), dat u eerder aan uw integratieaccount hebt toegevoegd, om te gebruiken voor ondertekening of versleuteling.

1. Als u uw overeenkomst wilt instellen voor het verzenden van berichten naar de gastpartner, selecteert u **Instellingen verzenden**.

   ![Instellingen verzenden](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Als u ondertekening of versleuteling voor uitgaande berichten wilt inschakelen, selecteert u onder **Berichten**respectievelijk **berichtondertekening** of **Versleuteling van berichten inschakelen.** Selecteer onder elke selectie het betreffende algoritme en [certificaat](./logic-apps-enterprise-integration-certificates.md), dat u eerder aan uw integratieaccount hebt toegevoegd, om te gebruiken voor ondertekening of versleuteling.

      | Eigenschap | Vereist | Beschrijving |
      |----------|----------|-------------|
      | **Handtekening van berichten inschakelen** | Nee | Teken uitgaande berichten met het geselecteerde ondertekeningsalgoritme en -certificaat. |
      | **Ondertekeningsalgoritme** | Ja, als ondertekenen is ingeschakeld | Het te gebruiken tekenalgoritme op basis van het geselecteerde certificaat |
      | **Certificaat** | Ja, als ondertekenen is ingeschakeld | Het certificaat dat u wilt gebruiken voor ondertekening |
      | **Versleuteling van berichten inschakelen** | Nee | Versleutel uitgaande met het geselecteerde versleutelingsalgoritme en -certificaat. |
      | **Coderingsalgoritme** | Ja, als versleuteling is ingeschakeld | Het te gebruiken versleutelingsalgoritme op basis van het geselecteerde certificaat |
      | **Certificaat** | Ja, als versleuteling is ingeschakeld | Het certificaat dat moet worden gebruikt voor versleuteling |
      ||||

   1. Geef **onder Eindpunten**de vereiste URL's op die moeten worden gebruikt voor het verzenden van actieberichten en bevestigingen.

      | Eigenschap | Vereist | Beschrijving |
      |----------|----------|-------------|
      | **Actie-URL** |  Ja | De URL die u wilt gebruiken voor het verzenden van actieberichten. De URL is een vereist veld voor zowel synchrone als asynchrone berichten. |
      | **URL voor bevestiging** | Ja | De URL die u wilt gebruiken voor het verzenden van bevestigingsberichten. De URL is een vereist veld voor asynchrone berichten. |
      ||||

1. Selecteer **RosettaNet PIP referenties**om uw overeenkomst met de RosettaNet PIP referenties voor partners in te stellen. Selecteer **onder PIP-naam**de naam voor uw eerder gemaakte PIP.

   ![PIP-verwijzingen](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Uw selectie vult de resterende eigenschappen, die zijn gebaseerd op de PIP die u in uw integratieaccount hebt ingesteld. Indien nodig u de **PIP-rol**wijzigen.

   ![Geselecteerde PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Nadat u deze stappen hebt voltooid, bent u klaar om RosettaNet-berichten te verzenden of te ontvangen.

## <a name="rosettanet-templates"></a>RosettaNet-sjablonen

Om de ontwikkeling te versnellen en integratiepatronen aan te bevelen, u logische app-sjablonen gebruiken voor het decoderen en coderen van RosettaNet-berichten. Wanneer u een logische app maakt, u kiezen in de sjabloongalerie in Logic App Designer. U deze sjablonen ook vinden in de [GitHub-repository voor Azure Logic Apps.](https://github.com/Azure/logicapps)

![RosettaNet-sjablonen](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>RosettaNet-berichten ontvangen of decoderen

1. [Een lege logische app maken](quickstart-create-first-logic-app-workflow.md).

1. [Koppel uw integratieaccount](logic-apps-enterprise-integration-create-integration-account.md#link-account) aan uw logische app.

1. Voordat u een actie toevoegen om het RosettaNet-bericht te decoderen, moet u een trigger toevoegen voor het starten van uw logische app, zoals een trigger voor aanvragen.

1. Kies **Nieuwe stap**na het toevoegen van de trigger .

   ![Trigger voor aanvragen toevoegen](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Voer in het zoekvak "rosettanet" in en selecteer deze actie: **RosettaNet Decode**

   ![Zoeken en selecteren "RosettaNet Decode" actie](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Geef de informatie voor de eigenschappen van de actie:

   ![Actiedetails geven](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Bericht** | Ja | Het RosettaNet-bericht dat moet worden gedecodeerd  |
   | **Headers** | Ja | De HTTP-headers die de waarden voor de versie, de RNIF-versie, en het antwoordtype bevatten, die het communicatietype tussen de partners aangeeft en synchroon of asynchroon kunnen zijn |
   | **Rol** | Ja | De rol van de gastpartner in de PIP |
   ||||

   Vanuit de actie RosettaNet Decode bevat de uitvoer, samen met andere eigenschappen, **uitgaand signaal**, dat u kiezen om te coderen en terug te keren naar de partner, of andere actie op die uitvoer te ondernemen.

## <a name="send-or-encode-rosettanet-messages"></a>RosettaNet-berichten verzenden of coderen

1. [Een lege logische app maken](quickstart-create-first-logic-app-workflow.md).

1. [Koppel uw integratieaccount](logic-apps-enterprise-integration-create-integration-account.md#link-account) aan uw logische app.

1. Voordat u een actie toevoegen om het RosettaNet-bericht te coderen, moet u een trigger toevoegen voor het starten van uw logische app, zoals een trigger voor aanvragen.

1. Kies **Nieuwe stap**na het toevoegen van de trigger .

   ![Trigger voor aanvragen toevoegen](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Voer in het zoekvak "rosettanet" in en selecteer deze actie: **RosettaNet Encode**

   ![Zoek en selecteer actie "RosettaNet Encode"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Geef de informatie voor de eigenschappen van de actie:

   ![Actiedetails geven](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Bericht** | Ja | Het RosettaNet-bericht om te coderen  |
   | **Gastpartner** | Ja | De naam van de hostpartner |
   | **Gastpartner** | Ja | De naam van de gastpartner |
   | **PIP-code** | Ja | De PIP-code |
   | **PIP-versie** | Ja | De PIP-versie |  
   | **PIP-instantie-identiteit** | Ja | De unieke id voor dit PIP-bericht |  
   | **Berichttype** | Ja | Het type van het bericht dat moet worden gecodeerd |  
   | **Rol** | Ja | De rol van de gastpartner |
   ||||

   Het gecodeerde bericht is nu klaar om naar de partner te sturen.

1. Als u het gecodeerde bericht wilt verzenden, wordt in dit voorbeeld de **HTTP-actie** gebruikt, die wordt omgedoopt tot 'HTTP - Gecodeerd bericht verzenden naar partner'.

   ![HTTP-actie voor het verzenden van RosettaNet-bericht](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Volgens RosettaNet-normen worden zakelijke transacties alleen als voltooid beschouwd wanneer alle stappen die door de PIP zijn gedefinieerd, zijn voltooid.

1. Nadat de host het gecodeerde bericht naar de partner stuurt, wacht de host op het signaal en de bevestiging. Als u deze taak wilt uitvoeren, voegt u de **RosettaNet toe die wacht op reactieactie.**

   ![Voeg de actie "RosettaNet wait for response" toe](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   De gebruiksduur voor het wachten en het aantal nieuwe pogingen zijn gebaseerd op de PIP-configuratie in uw integratieaccount. Als het antwoord niet wordt ontvangen, genereert deze actie een foutmelding. Als u nieuwe pogingen wilt verwerken, plaatst u altijd de **acties Encode** en **Wachten op respons** in een lus **Tot.**

   ![Tot lus met RosettaNet acties](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het valideren, transformeren en andere berichtbewerkingen met het [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
