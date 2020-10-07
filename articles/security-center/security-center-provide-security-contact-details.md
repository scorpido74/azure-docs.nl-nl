---
title: Contactgegevens voor beveiliging in Azure Security Center opgeven | Microsoft Docs
description: In dit document wordt beschreven hoe u de contactgegevens voor beveiliging in Azure Security Center kunt opgeven.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: aa35d1325e339af515c8bfc052d1af524b464e09
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91446013"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>E-mailmeldingen voor beveiligingswaarschuwingen instellen 

Als u er zeker van wilt zijn dat de juiste mensen in uw organisatie op de hoogte worden gesteld van beveiligingswaarschuwingen in uw omgeving, voert u de betreffende e-mailadressen in op de instellingenpagina **E-mailmeldingen**.

Bij het instellen van uw meldingen kunt u de e-mails configureren voor verzending naar specifieke personen of naar iedereen met een specifieke Azure-rol voor een abonnement. 

Security Center beperkt het aantal uitgaande e-mails om te voorkomen dat waarschuwingen niet meer serieus worden genomen. Security Center verzendt voor elk abonnement:

- maximaal **vier** e-mails per dag voor waarschuwingen met een **hoog ernstniveau**
- maximaal **twee** e-mails per dag voor waarschuwingen met een **gemiddeld ernstniveau**
- maximaal **één** e-mail per dag voor waarschuwingen met een **laag ernstniveau**


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="De details van de contactpersoon configureren die e-mails over beveiligingswaarschuwingen ontvangt." :::

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemeen verkrijgbaar (GA)|
|Prijzen:|Gratis|
|Vereiste rollen en machtigingen:|**Beveiligingsbeheerder**<br>**Abonnementeigenaar** |
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) US Gov (gedeeltelijk)<br>![Nee](./media/icons/no-icon.png) China Gov, Other Gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>E-mailmeldingen voor waarschuwingen instellen <a name="email"></a>

U kunt e-mailmeldingen verzenden naar individuele personen of naar alle gebruikers met specifieke Azure-rollen.

1. Selecteer in het gebied **Prijzen en instellingen** van Security Center het relevante abonnement en selecteer **E-mailmailmeldingen**.

1. De ontvangers voor uw meldingen definiëren:

    - Selecteer in de vervolgkeuzelijst een van de beschikbare rollen.
    - En/of voer specifieke e-mailadressen in, gescheiden door komma's. U kunt een onbeperkt aantal e-mailadressen invoeren.

1. Als u de contactgegevens voor beveiliging wilt toepassen op uw abonnement, selecteert u **Opslaan**.


## <a name="see-also"></a>Zie tevens
Zie de volgende onderwerpen voor meer informatie over beveiligingswaarschuwingen:

* [Beveiligingswaarschuwingen: een referentiegids](alerts-reference.md) -- Meer informatie over de beveiligingswaarschuwingen die u kunt zien in de module Threat Protection van Azure Security Center
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md) -- Meer informatie over hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren
* [Werkstroomautomatisering](workflow-automation.md) -- Automatiseren van reacties op waarschuwingen met aangepaste meldingslogica