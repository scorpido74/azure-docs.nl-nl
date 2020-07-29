---
title: AS2-berichtinstellingen
description: Naslag Gids voor AS2-instellingen voor verzenden en ontvangen in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74793030"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Naslag informatie voor AS2-bericht instellingen in Azure Logic Apps met Enterprise Integration Pack

Deze verwijzing beschrijft de eigenschappen die u kunt instellen om op te geven hoe een AS2-overeenkomst berichten verwerkt die worden verzonden en ontvangen tussen handels partners. Stel deze eigenschappen in op basis van uw overeenkomst met de partner die berichten met u uitwisselt.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 ontvangen instellingen

![Selecteer instellingen voor ontvangen](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Eigenschap | Vereist | Beschrijving |
|----------|----------|-------------|
| **Bericht eigenschappen onderdrukken** | No | Onderdrukt de eigenschappen van inkomende berichten met de instellingen van uw eigenschap. |
| **Het bericht moet worden ondertekend** | No | Hiermee geeft u op of alle inkomende berichten digitaal moeten worden ondertekend. Als u ondertekening nodig hebt, selecteert u in de lijst **certificaat** een bestaand openbaar certificaat van een gast partner voor het valideren van de hand tekening op de berichten. Als u geen certificaat hebt, kunt u meer te weten komen over het [toevoegen van certificaten](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Het bericht moet worden versleuteld** | No | Hiermee wordt aangegeven of alle inkomende berichten moeten worden versleuteld. Niet-versleutelde berichten worden geweigerd. Als u versleuteling nodig hebt, selecteert u in de lijst **certificaat** een bestaand certificaat van een host-partner voor het ontsleutelen van inkomende berichten. Als u geen certificaat hebt, kunt u meer te weten komen over het [toevoegen van certificaten](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Het bericht moet worden gecomprimeerd** | No | Hiermee geeft u op of alle inkomende berichten moeten worden gecomprimeerd. Niet-gecomprimeerde berichten worden geweigerd. |
| **Bericht-ID-duplicaten niet toestaan** | No | Hiermee wordt aangegeven of berichten met dubbele Id's mogen worden toegestaan. Als u dubbele Id's niet toestaat, selecteert u het aantal dagen tussen de controles. U kunt ook kiezen of u dubbele items wilt onderbreken. |
| **MDN tekst** | No | Hiermee geeft u de standaard melding voor het verplaatsen van berichten (MDN) op die u wilt verzenden naar de afzender van het bericht. |
| **MDN verzenden** | No | Hiermee geeft u op of synchrone MDNs moeten worden verzonden voor ontvangen berichten.  |
| **Ondertekende MDN verzenden** | No | Hiermee wordt aangegeven of een ondertekende MDNs moet worden verzonden voor ontvangen berichten. Als u ondertekening nodig hebt, selecteert u in de lijst met **Mic-algoritmen** het algoritme dat moet worden gebruikt voor het ondertekenen van berichten. |
| **Asynchrone MDN verzenden** | No | Hiermee geeft u op of MDNs asynchroon moet worden verzonden. Als u asynchrone MDNs selecteert, geeft u in het vak **URL** de URL op voor het verzenden van de MDNs. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Instellingen voor verzenden AS2

![Selecteer instellingen verzenden](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Eigenschap | Vereist | Beschrijving |
|----------|----------|-------------|
| **Bericht ondertekening inschakelen** | No | Hiermee geeft u op of alle uitgaande berichten digitaal moeten worden ondertekend. Als u zich moet ondertekenen, selecteert u deze waarden: <p>-Selecteer in de lijst **handtekening algoritme** het algoritme dat moet worden gebruikt voor het ondertekenen van berichten. <br>-Selecteer in de lijst **certificaat** een bestaand privé certificaat van een host-partner voor het ondertekenen van berichten. Als u geen certificaat hebt, kunt u meer te weten komen over het [toevoegen van certificaten](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Bericht versleuteling inschakelen** | No | Hiermee geeft u op of alle uitgaande berichten moeten worden versleuteld. Als u versleuteling nodig hebt, selecteert u deze waarden: <p>-Selecteer in de lijst **versleutelings algoritme** het algoritme voor het open bare certificaat van de gast partner die moet worden gebruikt voor het versleutelen van berichten. <br>-Selecteer in de lijst **certificaat** een bestaand privé certificaat van een gast partner voor het versleutelen van uitgaande berichten. Als u geen certificaat hebt, kunt u meer te weten komen over het [toevoegen van certificaten](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Bericht compressie inschakelen** | No | Hiermee geeft u op of alle uitgaande berichten moeten worden gecomprimeerd. |
| **HTTP-headers vouwen** | No | Hiermee wordt de HTTP- `content-type` header op één regel geplaatst. |
| **Bestands naam in MIME-header verzenden** | No | Hiermee geeft u op of de bestands naam in de MIME-header moet worden meegenomen. |
| **MDN aanvragen** | No | Hiermee geeft u op of meldingen over het verplaatsen van berichten (MDNs) moeten worden ontvangen voor alle uitgaande berichten. |
| **Ondertekende MDN aanvragen** | No | Hiermee wordt aangegeven of een ondertekende MDNs moet worden ontvangen voor alle uitgaande berichten. Als u ondertekening nodig hebt, selecteert u in de lijst met **Mic-algoritmen** het algoritme dat moet worden gebruikt voor het ondertekenen van berichten. |
| **Asynchrone MDN aanvragen** | No | Hiermee wordt aangegeven of MDNs asynchroon moet worden ontvangen. Als u asynchrone MDNs selecteert, geeft u in het vak **URL** de URL op voor het verzenden van de MDNs. |
| **NRR inschakelen** | No | Hiermee wordt aangegeven of ontvangst (non-weerleg baarheid) vereist is (NRR). Dit communicatie kenmerk geeft aan dat de gegevens zijn ontvangen als beschreven. |
| **SHA2-algoritme-indeling** | No | Hiermee geeft u de indeling van de MIC-algoritme op die moet worden gebruikt voor het ondertekenen van de kopteksten voor uitgaande AS2-berichten of MDN |
||||

## <a name="next-steps"></a>Volgende stappen

[AS2-berichten uitwisselen](../logic-apps/logic-apps-enterprise-integration-as2.md)
