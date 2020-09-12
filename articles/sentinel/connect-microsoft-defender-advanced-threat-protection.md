---
title: Micro soft Defender voor eindpunt gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Informatie over het verbinden van gegevens van micro soft Defender voor eind punten (voorheen micro soft Defender ATP) aan Azure Sentinel.
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
ms.openlocfilehash: 69ce6cb42fd18364a7b93faa9cc01d1f793d5cd6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657544"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Verbinding maken met waarschuwingen van micro soft Defender voor eind punt (voorheen micro soft Defender ATP) 


> [!IMPORTANT]
> Opname van micro soft Defender voor eindpunt waarschuwingen is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
 

Met de [micro soft Defender for Endpoint](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) -connector kunt u waarschuwingen van micro soft Defender naar het eind punt streamen naar Azure Sentinel. Op die manier kunt u beveiligings gebeurtenissen in uw organisatie uitgebreid analyseren en playbooks bouwen voor een effectief en direct antwoord.

## <a name="prerequisites"></a>Vereisten

- U moet een geldige licentie voor micro soft Defender voor eind punt hebben, zoals beschreven in [micro soft Defender voor endpoint-implementatie instellen](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- U moet een beheerder of beveiligings beheerder zijn op de Azure Sentinel-Tenant.


## <a name="connect-to-microsoft-defender-for-endpoint"></a>Verbinding maken met micro soft Defender voor eind punt

Als micro soft Defender voor het eind punt is geïmplementeerd en uw gegevens worden opgenomen, kunnen de waarschuwingen eenvoudig worden gestreamd naar Azure Sentinel.


1. In azure Sentinel selecteert u **gegevens connectors**, selecteert u **micro soft Defender voor eind punt** (kan nog steeds micro soft Defender Advanced Threat Protection worden genoemd) in de galerie en selecteert u de **pagina connector openen**.
1. Klik op **Verbinding maken**. 
1. Als u het relevante schema in Log Analytics voor de Defender ATP-waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert** en de **provider naam** **MDATP**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u micro soft Defender voor eind punt verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).
