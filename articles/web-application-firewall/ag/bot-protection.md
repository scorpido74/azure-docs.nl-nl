---
title: Botbeveiliging configureren voor Azure Web Application Firewall (WAF)
description: Meer informatie over het configureren van botbeveiliging voor Web Application Firewall (WAF) op Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516862"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Botbeveiliging configureren voor Web Application Firewall op Azure Application Gateway (Preview)

In dit artikel ziet u hoe u een botbeveiligingsregel configureert in Azure Web Application Firewall (WAF) voor Application Gateway met behulp van de Azure-portal. 

U een beheerde botbeveiligingsregel inschakelen waarmee uw WAF aanvragen van bekende schadelijke IP-adressen kan blokkeren of registreren. De IP-adressen zijn afkomstig van de Microsoft Threat Intelligence-feed. Intelligent Security Graph is de drijvende kracht achter Microsoft-bedreigingsinformatie en wordt gebruikt door meerdere services, waaronder Azure Security Center.

> [!NOTE]
> De ingestelde beveiligingsregel voor bot is momenteel in openbare preview en wordt voorzien van een preview-serviceniveauovereenkomst. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Zie [de aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Maak een basisWAF-beleid voor Application Gateway door de instructies te volgen die zijn beschreven in [het beleid voor webtoepassingsfirewall maken voor application gateway.](create-waf-policy-ag.md)

## <a name="enable-bot-protection-rule-set"></a>Regelset voor botbeveiliging inschakelen

1. Selecteer **Regels**op de pagina **Basisbeleid** die u eerder hebt gemaakt. **Settings**  

2. Schakel op de pagina Details onder de sectie **Regels** beheren in het vervolgkeuzemenu het selectievakje voor de botbeveiligingsregel in en selecteer **Opslaan**.

> [!div class="mx-imgBorder"]
> ![Bot-bescherming](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Volgende stappen

Zie Aangepaste regels voor [Web Application Firewall v2 op Azure Application Gateway](custom-waf-rules-overview.md)voor meer informatie over aangepaste regels.