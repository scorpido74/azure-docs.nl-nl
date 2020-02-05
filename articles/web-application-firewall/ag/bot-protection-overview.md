---
title: Overzicht van WAF op Azure-toepassing gateway bot
titleSuffix: Azure Web Application Firewall
description: Dit artikel bevat een overzicht van Web Application Firewall (WAF) op Application Gateway bot-beveiliging
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027093"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Overzicht van Azure Web Application firewall op Azure-toepassing gateway-bot

Ongeveer 20% van het Internet verkeer is van een beschadigd bots. Dit zijn dingen zoals uitval, scannen en zoeken naar beveiligings problemen in uw webtoepassing. Wanneer deze botsingen worden gestopt bij de Web Application firewall (WAF), kunnen ze niet worden aangevallen. Ze kunnen ook geen resources en services gebruiken, zoals uw back-end en andere onderliggende infra structuur.

U kunt een set beheerde bot-beveiliging inschakelen voor uw WAF om aanvragen van bekende schadelijke IP-adressen te blok keren of te registreren. De IP-adressen worden vanuit de micro soft Threat Intelligence-feed gebrond. Microsoft Intelligent Security Graph is de bron van Bedreigingsinformatie van Microsoft en wordt gebruikt door meerdere services, waaronder Azure Security Center.

> [!IMPORTANT]
> De set bot Protection-regelset bevindt zich momenteel in een open bare preview en wordt weer gegeven met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="use-with-owasp-rulesets"></a>Gebruiken met OWASP-rules

U kunt de bot Protection-ruleset naast een van de OWASP-Rules (2.2.9, 3,0 en 3,1) gebruiken. Op elk gewenst moment kan slechts één OWASP ruleset worden gebruikt. De bot Protection ruleset bevat een extra regel die wordt weer gegeven in een eigen ruleset. Het object heeft de titel **Microsoft_BotManagerRuleSet_0**1 en u kunt het in-of uitschakelen zoals de andere OWASP-regels.

![Bot-ruleSet](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>RuleSet-update

De lijst met ongeldige IP-adressen voor de bot-beperking is meerdere keren per dag bijgewerkt vanaf de micro soft Threat Intelligence-feed om gesynchroniseerd te blijven met het bots. Uw webtoepassingen worden voortdurend beschermd, zelfs als de bot-aanvals vectoren veranderen.

## <a name="next-steps"></a>Volgende stappen

- [Bot-beveiliging configureren voor Web Application firewall op Azure-toepassing gateway (preview-versie)](bot-protection.md)
