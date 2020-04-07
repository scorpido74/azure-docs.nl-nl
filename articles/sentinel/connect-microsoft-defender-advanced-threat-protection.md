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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756356"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Waarschuwingen verbinden van Geavanceerde bedreigingsbeveiliging van Microsoft Defender 


> [!IMPORTANT]
> Inname van Microsoft Defender Advanced Threat Protection-waarschuwingen is momenteel openbaar.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
 

Met de [Microsoft Defender Advanced Threat Protection-connector](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) u waarschuwingen van Microsoft Defender Advanced Threat Protection streamen naar Azure Sentinel. Zo u beveiligingsgebeurtenissen in uw organisatie beter analyseren en speelboeken maken voor een effectieve en onmiddellijke reactie.

## <a name="prerequisites"></a>Vereisten

- U moet in het beschikt over een geldige licentie voor Microsoft Defender Advanced Threat Protection, zoals beschreven in [Microsoft Defender ATP-implementatie instellen.](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing) 
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
