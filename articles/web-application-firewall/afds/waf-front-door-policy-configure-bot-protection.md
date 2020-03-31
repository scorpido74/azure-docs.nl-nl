---
title: Botbeveiliging configureren voor webtoepassingsfirewall met Azure Front Door (Voorbeeld)
description: Lees webtoepassingsfirewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934657"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Botbeveiliging configureren voor Web Application Firewall (Voorbeeld)
In dit artikel ziet u hoe u botbeveiligingsregels configureert in Azure Web Application Firewall (WAF) voor voordeur met behulp van Azure-portal. Botbeveiligingsregel kan ook worden geconfigureerd met de sjabloon CLI, Azure PowerShell of Azure Resource Manager.

> [!IMPORTANT]
> Bot bescherming regel ingesteld is momenteel in openbare preview en is voorzien van een preview service level overeenkomst. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben.  Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

Maak een basisWAF-beleid voor voordeur door de instructies te volgen die zijn beschreven in [Een WAF-beleid maken voor Azure Front Door met behulp van de Azure-portal.](waf-front-door-create-portal.md)

## <a name="enable-bot-protection-rule-set"></a>Regelset voor botbeveiliging inschakelen

Zoek op de pagina **Beheerde regels** bij het maken van een firewallbeleid voor webtoepassingen eerst de sectie **Beheerde regelset,** schakel het selectievakje in voor de regel **Microsoft_BotManager_1.0** in het vervolgkeuzemenu en selecteer **Vervolgens Controleren + Maken**.

   ![Botbeveiligingsregel](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [monitoren van WAF](waf-front-door-monitor.md).
