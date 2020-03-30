---
title: AS2-berichten verzenden en ontvangen voor B2B
description: Exchange AS2-berichten voor B2B-bedrijfsintegratiescenario's met Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0ce813e91750db3cdfa1e651a68fbb82d593eb32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650556"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange AS2-berichten voor B2B-bedrijfsintegratie in Azure Logic Apps met Enterprise Integration Pack

> [!IMPORTANT]
> De originele AS2-connector wordt afgeschaft, dus zorg ervoor dat u in plaats daarvan de **AS2 (v2)-connector** gebruikt. Deze versie biedt dezelfde mogelijkheden als de oorspronkelijke versie, is inheems in de runtime van Logic Apps en biedt aanzienlijke prestatieverbeteringen in termen van doorvoer en berichtgrootte. De native v2-connector vereist ook niet dat u een verbinding maakt met uw integratieaccount. Zorg er in plaats daarvan, zoals beschreven in de vereisten, voor dat u uw integratieaccount koppelt aan de logische app waar u de connector wilt gebruiken.

Als u wilt werken met AS2-berichten in Azure Logic Apps, u de AS2-connector gebruiken, die triggers en acties biedt voor het beheer van AS2-communicatie. Om bijvoorbeeld de veiligheid en betrouwbaarheid bij het verzenden van berichten vast te stellen, u de volgende acties gebruiken:

* [ **AS2 Codeer** actie](#encode) voor het leveren van versleuteling, digitale ondertekening en bevestigingen via Message Disposition Notifications (MDN), die helpen bij het ondersteunen van niet-afwijzing. Met deze actie worden bijvoorbeeld AS2/HTTP-kopteksten uitgevoerd en worden deze taken uitgevoerd wanneer deze zijn geconfigureerd:

  * Tekent uitgaande berichten.
  * Versleutelt uitgaande berichten.
  * Comprimeert het bericht.
  * Verzendt de bestandsnaam in de MIME-header.

* [ **AS2 Decoderen** actie](#decode) voor het verstrekken van decryptie, digitale ondertekening, en bevestigingen via Message Disposition Notifications (MDN). Met deze actie worden bijvoorbeeld de volgende taken uitgevoerd:

  * Verwerkt AS2/HTTP-headers.
  * Hiermee worden ontvangen MDN's verzoend met de oorspronkelijke uitgaande berichten.
  * Updates en correlaten van records in de niet-verwerpingsdatabase.
  * Schrijft records voor AS2 status rapportage.
  * De inhoud van het laadvermogen wordt uitgevoerd als base64-gecodeerd.
  * Hiermee bepaalt u of MDN's vereist zijn. Op basis van de AS2-overeenkomst bepaalt u of MDN's synchroon of asynchroon moeten zijn.
  * Hiermee genereert u synchrone of asynchrone MDN's op basis van de AS2-overeenkomst.
  * Hiermee stelt u de correlatietokens en -eigenschappen in op MDN's.

  Met deze actie worden deze taken ook uitgevoerd wanneer deze zijn geconfigureerd:

  * Controleert de handtekening.
  * Decodeert de berichten.
  * Decomprimeert het bericht.
  * Dubbele duplicaat van bericht-id controleren en weigeren.

In dit artikel ziet u hoe u de as2-coderings- en decoderingsacties toevoegt aan een bestaande logische app.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* De logische app van waaruit u de AS2-connector wilt gebruiken en een trigger waarmee de werkstroom van uw logische app wordt gestart. De AS2-connector biedt alleen acties, geen triggers. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw Azure-abonnement en is gekoppeld aan de logische app waarin u de AS2-connector wilt gebruiken. Zowel uw logische app als integratieaccount moeten op dezelfde locatie of azure-regio bestaan.

* Ten minste twee [handelspartners](../logic-apps/logic-apps-enterprise-integration-partners.md) die u al hebt gedefinieerd in uw integratieaccount met behulp van de AS2-identiteitskwalificatie.

* Voordat u de AS2-connector gebruiken, [agreement](../logic-apps/logic-apps-enterprise-integration-agreements.md) moet u een AS2-overeenkomst tussen uw handelspartners maken en die overeenkomst opslaan in uw integratieaccount.

* Als u [Azure Key Vault](../key-vault/key-vault-overview.md) gebruikt voor certificaatbeheer, controleert u of uw kluissleutels de **versleutelings-** en **decryptebewerkingen** toestaan. Anders mislukken de coderings- en decoderingsacties.

  Ga in de Azure-portal naar de sleutel in uw sleutelkluis, bekijk de **toegestane bewerkingen**van uw sleutel en controleer of de **bewerkingen Versleutelen** en **decoderen** zijn geselecteerd, bijvoorbeeld:

  ![Vault-sleutelbewerkingen controleren](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2-berichten coderen

1. Als u dat nog niet hebt gedaan, opent u in de [Azure-portal](https://portal.azure.com)uw logische app in de Logic App Designer.

1. Voeg in de ontwerper een nieuwe actie toe aan uw logica-app.

1. Selecteer **onder Een actie kiezen** en het zoekvak de optie **Alles**. Typ in het zoekvak 'as2-coderen' en zorg ervoor dat u de actie AS2 (v2) selecteert: **AS2 Coderen**

   ![Selecteer 'AS2 Coderen'](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Geef nu informatie voor deze eigenschappen:

   | Eigenschap | Beschrijving |
   |----------|-------------|
   | **Bericht om te coderen** | Het bericht payload |
   | **AS2 van** | De id voor de afzender van het bericht zoals opgegeven door uw AS2-overeenkomst |
   | **AS2 naar** | De id voor de berichtontvanger zoals opgegeven door uw AS2-overeenkomst |
   |||

   Bijvoorbeeld:

   ![Eigenschappen voor het coderen van berichten](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Als u problemen ondervindt bij het verzenden van ondertekende of versleutelde berichten, u overwegen verschillende SHA256-algoritmeindelingen uit te proberen. De AS2-specificatie geeft geen informatie over SHA256-indelingen, dus elke provider gebruikt zijn eigen implementatie of indeling.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2-berichten decoderen

1. Als u dat nog niet hebt gedaan, opent u in de [Azure-portal](https://portal.azure.com)uw logische app in de Logic App Designer.

1. Voeg in de ontwerper een nieuwe actie toe aan uw logica-app.

1. Selecteer **onder Een actie kiezen** en het zoekvak de optie **Alles**. Voer in het zoekvak 'as2-decode' in en zorg ervoor dat u de as2-actie (v2) selecteert: **AS2 Decoderen**

   ![Selecteer 'AS2 Decoderen'](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Selecteer deze waarden uit eerdere trigger- of actie-uitvoervoor **het bericht dat moet worden coderen** en de eigenschappen van de **berichtenkoppen.**

   Stel dat uw logica-app berichten ontvangt via een trigger verzoek. U de uitgangen van die trigger selecteren.

   ![Hoofdtekst en kopteksten selecteren in uitvoeraanvragen](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Voorbeeld

Zie de [as2-logische appsjabloon en -scenario](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)als u wilt proberen een volledig operationele logica-app en voorbeeld as2-scenario te implementeren.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/as2/)de connector voor meer technische details over deze connector, zoals acties en limieten zoals beschreven in het Swagger-bestand van de connector. 

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de oorspronkelijke ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
