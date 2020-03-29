---
title: AS2-berichtinstellingen
description: Naslaggids voor AS2-instellingen voor verzenden en ontvangen in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793030"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referentie voor AS2-berichtinstellingen in Azure Logic Apps met Enterprise Integration Pack

Deze verwijzing beschrijft de eigenschappen die u instellen voor het opgeven van hoe een AS2-overeenkomst omgaat met verzonden en ontvangen berichten tussen handelspartners. Stel deze eigenschappen in op basis van uw overeenkomst met de partner die berichten met u uitwisselt.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2-ontvangstinstellingen

![Selecteer 'Instellingen ontvangen'](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Eigenschap | Vereist | Beschrijving |
|----------|----------|-------------|
| **Berichteigenschappen overschrijven** | Nee | Overschrijft de eigenschappen van binnenkomende berichten met uw eigenschapsinstellingen. |
| **Bericht moet worden ondertekend** | Nee | Hiermee geeft u op of alle binnenkomende berichten digitaal moeten worden ondertekend. Als u wilt ondertekenen, selecteert u in de **certificaatlijst** een bestaand openbaar certificaat voor gastpartners voor het valideren van de handtekening op de berichten. Als u geen certificaat hebt, leest u meer over [het toevoegen van certificaten.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Bericht moet versleuteld zijn** | Nee | Hiermee geeft u op of alle binnenkomende berichten moeten worden versleuteld. Niet-versleutelde berichten worden geweigerd. Als u versleuteling nodig hebt, selecteert u in de **lijst Certificaat** een bestaand privécertificaat voor hostpartners voor het decoderen van binnenkomende berichten. Als u geen certificaat hebt, leest u meer over [het toevoegen van certificaten.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Bericht moet worden gecomprimeerd** | Nee | Hiermee geeft u op of alle binnenkomende berichten moeten worden gecomprimeerd. Niet-gecomprimeerde berichten worden geweigerd. |
| **Berichten-ID-duplicaten weigeren** | Nee | Hiermee geeft u op of berichten met dubbele id's moeten worden toegestaan. Als u dubbele id's niet instaat, selecteert u het aantal dagen tussen de controles. U ook kiezen of u duplicaten wilt opschorten. |
| **MDN-tekst** | Nee | Hiermee geeft u de standaardmelding voor berichtdispositie (MDN) op die u naar de afzender van het bericht wilt verzenden. |
| **MDN verzenden** | Nee | Hiermee geeft u op of synchrone MDN's voor ontvangen berichten moeten worden verzonden.  |
| **Ondertekend MDN verzenden** | Nee | Hiermee geeft u op of ondertekende MDN's moeten worden verzonden voor ontvangen berichten. Als u wilt ondertekenen, selecteert u in de lijst **MIC-algoritm** het algoritme dat u wilt gebruiken voor het ondertekenen van berichten. |
| **Asynchrone MDN verzenden** | Nee | Hiermee geeft u op of MDN's asynchroon moeten worden verzonden. Als u asynchrone MDN's selecteert, geeft u in het **vak URL** de URL op voor waar u de MDN's naartoe moet sturen. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2-verzendinstellingen

![Selecteer 'Instellingen verzenden'](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Eigenschap | Vereist | Beschrijving |
|----------|----------|-------------|
| **Handtekening van berichten inschakelen** | Nee | Hiermee geeft u op of alle uitgaande berichten digitaal moeten worden ondertekend. Als u wilt tekenen, selecteert u de volgende waarden: <p>- Selecteer in de lijst **Algoritme voor ondertekenen** het algoritme dat u wilt gebruiken voor het ondertekenen van berichten. <br>- Selecteer in de **certificaatlijst** een bestaand privécertificaat voor hostpartners voor het ondertekenen van berichten. Als u geen certificaat hebt, leest u meer over [het toevoegen van certificaten.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Versleuteling van berichten inschakelen** | Nee | Hiermee geeft u op of alle uitgaande berichten moeten worden versleuteld. Als u versleuteling nodig hebt, selecteert u de volgende waarden: <p>- Selecteer in de lijst **Versleutelingsalgoritm** het openbare certificaatalgoritme voor gastpartners dat u wilt gebruiken voor het versleutelen van berichten. <br>- Selecteer in de **certificaatlijst** een bestaand privécertificaat voor gastpartners voor het versleutelen van uitgaande berichten. Als u geen certificaat hebt, leest u meer over [het toevoegen van certificaten.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Berichtcompressie inschakelen** | Nee | Hiermee geeft u op of alle uitgaande berichten moeten worden gecomprimeerd. |
| **HTTP-headers ontvouwen** | Nee | Hiermee plaatst `content-type` u de HTTP-koptekst op één regel. |
| **Bestandsnaam verzenden in MIME-header** | Nee | Hiermee geeft u op of de bestandsnaam in de MIME-header moet worden opgenomen. |
| **MDN aanvragen** | Nee | Hiermee geeft u op of meldingen (MDN's) voor alle uitgaande berichten moeten worden ontvangen. |
| **Aanvraag ondertekend MDN** | Nee | Hiermee geeft u op of ondertekende MDN's voor alle uitgaande berichten moeten worden ontvangen. Als u wilt ondertekenen, selecteert u in de lijst **MIC-algoritm** het algoritme dat u wilt gebruiken voor het ondertekenen van berichten. |
| **Asynchrone MDN aanvragen** | Nee | Hiermee geeft u op of MDN's asynchroon moeten worden ontvangen. Als u asynchrone MDN's selecteert, geeft u in het **vak URL** de URL op voor waar u de MDN's naartoe moet sturen. |
| **NRR inschakelen** | Nee | Hiermee geeft u op of niet-verwerpingsontvangst (NRR) vereist is. Dit communicatiekenmerk levert het bewijs dat de gegevens zijn ontvangen zoals geadresseerd. |
| **SHA2-algoritmeindeling** | Nee | Hiermee geeft u de microfoon-algoritmeindeling op die moet worden gebruikt voor het ondertekenen in de kopteksten voor de uitgaande AS2-berichten of MDN |
||||

## <a name="next-steps"></a>Volgende stappen

[AS2-berichten uitwisselen](../logic-apps/logic-apps-enterprise-integration-as2.md)
