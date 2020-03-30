---
title: Overzicht van WAF op Azure Application Gateway-botbeveiliging
titleSuffix: Azure Web Application Firewall
description: Dit artikel geeft een overzicht van webapplication firewall (WAF) op Application Gateway bot bescherming
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027093"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Overzicht van Azure Web Application Firewall op Azure Application Gateway-botbeveiliging

Ongeveer 20% van al het internetverkeer komt van slechte bots. Ze doen dingen zoals schrapen, scannen en zoeken naar kwetsbaarheden in uw webapplicatie. Wanneer deze bots worden gestopt bij de Web Application Firewall (WAF), kunnen ze u niet aanvallen. Ze kunnen ook geen gebruik maken van uw resources en services, zoals uw backends en andere onderliggende infrastructuur.

U een beheerde botbeveiligingsregel inschakelen waarmee uw WAF aanvragen van bekende schadelijke IP-adressen kan blokkeren of registreren. De IP-adressen zijn afkomstig van de Microsoft Threat Intelligence-feed. Intelligent Security Graph is de drijvende kracht achter Microsoft-bedreigingsinformatie en wordt gebruikt door meerdere services, waaronder Azure Security Center.

> [!IMPORTANT]
> De ingestelde beveiligingsregel voor bot is momenteel in openbare preview en wordt voorzien van een preview-serviceniveauovereenkomst. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Zie [de aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="use-with-owasp-rulesets"></a>Gebruiken met OWASP-regelsets

U de regelset Botprotection gebruiken naast een van de OWASP-regelsets (2.2.9, 3.0 en 3.1). Slechts één OWASP-regelset kan op een bepaald moment worden gebruikt. De regelset voor botbeveiliging bevat een extra regel die wordt weergegeven in de eigen regelset. Het is getiteld **Microsoft_BotManagerRuleSet_0.1**, en u het in- of uitschakelen, net als de andere OWASP-regels.

![Botregelset](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Regelset-update

De lijst met bekende slechte IP-adressen van de botmitigatie wordt meerdere keren per dag bijgewerkt vanuit de Microsoft Threat Intelligence-feed om synchroon te blijven met de bots. Uw webapplicaties worden continu beschermd, zelfs als de botaanvalsvectoren veranderen.

## <a name="next-steps"></a>Volgende stappen

- [Botbeveiliging configureren voor Web Application Firewall op Azure Application Gateway (Preview)](bot-protection.md)
