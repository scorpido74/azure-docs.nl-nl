---
title: Bot-beveiliging configureren voor WAF met de voor deur van Azure (preview)
description: Meer informatie over Web Application firewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 95660f764d28172ecb55a4952b785fea5f2aa4bb
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186702"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Bot-beveiliging voor Web Application firewall configureren (preview-versie)
In dit artikel wordt beschreven hoe u de bot-beveiligings regel configureert in azure Web Application firewall (WAF) voor de voor deur met behulp van Azure CLI, Azure PowerShell of Azure Resource Manager sjabloon.

Een set Managed bot Protection kan worden ingeschakeld voor uw WAF om aangepaste acties uit te voeren op aanvragen van bekende schadelijke IP-adressen. De IP-adressen worden vanuit de micro soft Threat Intelligence-feed gebrond. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) voorziet in micro soft Threat Intelligence en wordt gebruikt door meerdere services, waaronder Azure Security Center.

> [!IMPORTANT]
> De set bot Protection-regelset bevindt zich momenteel in een open bare preview en wordt weer gegeven met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben.  Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

Maak een basis WAF-beleid voor de voor deur door de instructies te volgen die worden beschreven in [een WAF-beleid maken voor Azure front deur met behulp van de Azure Portal](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Regel set voor bot-beveiliging inschakelen

1. Klik op de pagina basis beleid die u in de voor gaande sectie hebt gemaakt onder **instellingen**op **regels**.
2. Schakel op de pagina Details, onder de sectie **regels beheren** , in de vervolg keuzelijst het selectie vakje in voor de regel **BotProtection-Preview-0,1**en Selecteer hierboven **Opslaan** hierboven.
    
   ![Bot-beveiligings regel](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [bewaken van WAF](waf-front-door-monitor.md).
