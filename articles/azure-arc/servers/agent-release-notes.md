---
title: Wat is er nieuw in de agent voor Azure Arc enabled-servers (preview-versie)
description: Dit artikel bevat opmerkingen bij de release van de agent voor Azure Arc-servers (preview). Voor veel van de samen vattingen vindt u koppelingen naar aanvullende informatie.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236693"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Wat is er nieuw in de agent voor Azure Arc enabled-servers (preview-versie)

De met Azure Arc ingeschakelde servers (preview) verbonden machine agent ontvangt voortdurend verbeteringen. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten

## <a name="august-2020"></a>Augustus 2020

Versie: 0,11

- Ondersteuning voor Ubuntu 20,04.

- Verbeteringen van de betrouw baarheid van uitbreidings implementaties.

### <a name="known-issues"></a>Bekende problemen

Als u een oudere versie van de Linux-agent gebruikt en deze hebt geconfigureerd voor het gebruik van een proxy server, moet u na de upgrade de instelling van de proxy server opnieuw configureren. U kunt dit doen `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Volgende stappen

Voordat u met de Arc ingeschakelde servers (preview) op meerdere hybride computers evalueert of inschakelt, raadpleegt u het overzicht van de [verbonden machine agent](agent-overview.md) om de vereisten, technische details over de agent en implementatie methoden te begrijpen.