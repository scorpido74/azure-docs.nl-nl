---
title: Geef beveiligings contact gegevens op in Azure Security Center | Microsoft Docs
description: In dit document wordt beschreven hoe u contact gegevens van de beveiliging kunt opgeven in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 9fbd63e1b46b837350be720fadf68777927f9bff
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904755"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>E-mail meldingen instellen voor beveiligings waarschuwingen 

Om ervoor te zorgen dat de juiste personen in uw organisatie op de hoogte worden gesteld van beveiligings waarschuwingen in uw omgeving, voert u hun e-mail adres in op de pagina instellingen voor **e-mail meldingen** .

Bij het instellen van uw meldingen kunt u de e-mail berichten zo configureren dat ze worden verzonden naar specifieke personen of naar iemand met een specifieke Azure-rol voor een abonnement. 

Security Center beperkt het volume van uitgaande mails om waarschuwings intensief te voor komen. Security Center verzendt voor elk abonnement:

- Maxi maal **vier** e-mails per dag voor waarschuwingen met **hoge urgentie**
- Maxi maal **twee** e-mails per dag voor waarschuwingen met **gemiddelde urgentie**
- Maxi maal **één** e-mail adres per dag voor waarschuwingen met **lage urgentie**


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Het configureren van de details van de contact persoon die e-mail berichten over beveiligings waarschuwingen ontvangt." :::

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Algemeen beschikbaar (GA)|
|Koers|Gratis|
|Vereiste rollen en machtigingen:|**Beveiligings beheerder**<br>**Abonnements eigenaar** |
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![Yes](./media/icons/yes-icon.png) US Gov (gedeeltelijk)<br>![No](./media/icons/no-icon.png) China gov, andere gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>E-mail meldingen voor waarschuwingen instellen <a name="email"></a>

U kunt e-mail meldingen verzenden naar personen of naar alle gebruikers met specifieke Azure-rollen.

1. Klik in het gebied met **prijzen & instellingen** van Security Center, het relevante abonnement en selecteer **e-mail meldingen**.

1. De ontvangers voor uw meldingen definiëren:

    - Selecteer in de vervolg keuzelijst een van de beschik bare rollen.
    - En/of voer specifieke e-mail adressen in, gescheiden door komma's. Er is geen limiet voor het aantal e-mail adressen dat u kunt invoeren.

1. Selecteer **Opslaan**om de beveiligings gegevens van de contact persoon toe te passen op uw abonnement.


## <a name="see-also"></a>Zie ook
Zie het volgende voor meer informatie over beveiligings waarschuwingen:

* [Beveiligings waarschuwingen-een referentie handleiding](alerts-reference.md) --meer informatie over de beveiligings waarschuwingen die u kunt zien in de module Threat Protection van Azure Security Center
* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md) --meer informatie over het beheren van en reageren op beveiligings waarschuwingen
* [Werk stroom automatisering](workflow-automation.md) : Automatiseer reacties op waarschuwingen met aangepaste meldings logica