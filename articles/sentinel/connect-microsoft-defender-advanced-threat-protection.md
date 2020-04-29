---
title: Micro soft Defender ATP-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van gegevens van micro soft Defender Advanced Threat Protection naar Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756356"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Verbinding maken met waarschuwingen van micro soft Defender Advanced Threat Protection 


> [!IMPORTANT]
> Opname van waarschuwingen van micro soft Defender Advanced Threat Protection is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen.
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Met de [micro soft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) -connector kunt u waarschuwingen van micro soft Defender Advanced Threat Protection streamen naar Azure Sentinel. Op die manier kunt u beveiligings gebeurtenissen in uw organisatie uitgebreid analyseren en playbooks bouwen voor een effectief en direct antwoord.

## <a name="prerequisites"></a>Vereisten

- U moet een geldige licentie voor micro soft Defender Advanced Threat Protection hebben, zoals beschreven in [micro soft Defender ATP-implementatie instellen](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- U moet een beheerder of beveiligings beheerder zijn op de Azure Sentinel-Tenant.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Verbinding maken met micro soft Defender Advanced Threat Protection

Als micro soft Defender Advanced Threat Protection wordt geïmplementeerd en uw gegevens worden opgenomen, kunnen de waarschuwingen eenvoudig worden gestreamd naar Azure Sentinel.


1. In azure Sentinel selecteert u **Data connectors**, klikt u op de tegel **micro soft Defender Advanced Threat Protection** en selecteert u de **pagina connector openen**.
1. Klik op **Verbinden**. 
1. Als u het relevante schema in Log Analytics voor de Defender ATP-waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert** en de **provider naam** **MDATP**.




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u met micro soft Defender ATP verbinding maakt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).
