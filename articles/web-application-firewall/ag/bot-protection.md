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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516862"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Bot-beveiliging configureren voor Web Application firewall op Azure-toepassing gateway (preview-versie)

In dit artikel wordt beschreven hoe u een bot-beveiligings regel configureert in azure Web Application firewall (WAF) voor Application Gateway met behulp van de Azure Portal. 

U kunt een set beheerde bot-beveiliging inschakelen voor uw WAF om aanvragen van bekende schadelijke IP-adressen te blok keren of te registreren. De IP-adressen worden vanuit de micro soft Threat Intelligence-feed gebrond. Microsoft Intelligent Security Graph is de bron van Bedreigingsinformatie van Microsoft en wordt gebruikt door meerdere services, waaronder Azure Security Center.

> [!NOTE]
> De set bot Protection-regelset bevindt zich momenteel in een open bare preview en wordt weer gegeven met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Maak een basis WAF-beleid voor Application Gateway door de instructies te volgen die worden beschreven in [firewall-beleids regels voor webtoepassingen maken voor Application Gateway](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Regel set voor bot-beveiliging inschakelen

1. Selecteer op de pagina **basis** beleid dat u eerder hebt gemaakt onder **instellingen**de optie **regels**.  

2. Schakel op de pagina Details, onder de sectie **regels beheren** , in de vervolg keuzelijst het selectie vakje voor de bot-beveiligings regel in en selecteer vervolgens **Opslaan**.

> [!div class="mx-imgBorder"]
> ![bot-beveiliging](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Volgende stappen

Zie [aangepaste regels voor Web Application firewall v2 op Azure-toepassing gateway](custom-waf-rules-overview.md)voor meer informatie over aangepaste regels.