---
title: Bot-beveiliging configureren voor Azure Web Application firewall (WAF)
description: Meer informatie over het configureren van bot-beveiliging voor Web Application firewall (WAF) op Azure-toepassing gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "73516862"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Bot-beveiliging configureren voor Web Application firewall op Azure-toepassing gateway (preview-versie)

In dit artikel wordt beschreven hoe u een bot-beveiligings regel configureert in azure Web Application firewall (WAF) voor Application Gateway met behulp van de Azure Portal. 

U kunt een set beheerde bot-beveiliging inschakelen voor uw WAF om aanvragen van bekende schadelijke IP-adressen te blok keren of te registreren. De IP-adressen zijn afkomstig uit de feed Bedreigingsinformatie van Microsoft. Intelligent Security Graph voorziet de feed van gegevens en wordt gebruikt door verschillende services, waaronder Azure Security Center.

> [!NOTE]
> De set bot Protection-regelset bevindt zich momenteel in een open bare preview en wordt weer gegeven met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)-   voor beelden voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Maak een basis WAF-beleid voor Application Gateway door de instructies te volgen die worden beschreven in [firewall-beleids regels voor webtoepassingen maken voor Application Gateway](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Regel set voor bot-beveiliging inschakelen

1. Selecteer op de pagina **basis** beleid dat u eerder hebt gemaakt onder **instellingen**de optie **regels**.  

2. Schakel op de pagina Details onder de sectie **regels beheren**in   het vervolg keuzemenu het selectie vakje voor de bot-beveiligings regel in en selecteer vervolgens **Opslaan**.

> [!div class="mx-imgBorder"]
> ![Bot-bescherming](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Volgende stappen

Zie [aangepaste regels voor Web Application firewall v2 op Azure-toepassing gateway](custom-waf-rules-overview.md)voor meer informatie over aangepaste regels.