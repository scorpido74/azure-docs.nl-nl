---
title: Microsoft Defender ATP-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Microsoft Defender Advanced Threat Protection-gegevens met Azure Sentinel.
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
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588209"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Waarschuwingen verbinden van Geavanceerde bedreigingsbeveiliging van Microsoft Defender 


> [!IMPORTANT]
> Inname van Microsoft Defender Advanced Threat Protection logs is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
 

U waarschuwingen van [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) met één klik naar Azure Sentinel streamen. Met deze verbinding u de waarschuwingen van Microsoft Defender Advanced Threat Protection streamen naar Azure Sentinel. 

## <a name="prerequisites"></a>Vereisten

- Geldige licentie voor Microsoft Defender Advanced Threat Protection die is ingeschakeld zoals beschreven in [Licentiebepaling valideren en volledig zijn ingesteld voor Microsoft Defender Advanced Threat Protection.](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing) 
- U moet een beheerder of beveiligingsbeheerder zijn op de Azure Sentinel-tenant.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Verbinding maken met Geavanceerde bedreigingsbeveiliging van Microsoft Defender

Als Microsoft Defender Advanced Threat Protection wordt geïmplementeerd en uw gegevens inneemt, kunnen de waarschuwingen eenvoudig worden gestreamd naar Azure Sentinel.


1. Selecteer in Azure Sentinel **gegevensconnectoren**, klik op de tegel **Geavanceerde bedreigingsbeveiliging van Microsoft Defender** en selecteer De **connectorpagina openen**.
1. Klik op **Verbinden**. 
1. Als u het relevante schema wilt gebruiken in Log Analytics voor de Defender ATP-waarschuwingen, zoekt u naar **SecurityAlert** en de **naam van** de provider is **MDATP**.




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Microsoft Defender ATP verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)
