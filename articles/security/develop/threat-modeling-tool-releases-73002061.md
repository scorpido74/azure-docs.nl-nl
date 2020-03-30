---
title: Microsoft Threat Modeling Tool release 02/11/2020 - Azure
description: Het documenteren van de releasenotes voor het hulpprogramma voor bedreigingsmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624837"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool update release 7.3.00206.1 - 02/11/2020

Versie 7.3.00206.1 van de Microsoft Threat Modeling Tool (TMT) is uitgebracht op 11 februari 2020 en bevat de volgende wijzigingen:

- Opgeloste fouten

## <a name="notable-bug-fixes"></a>Opmerkelijke bugfixes

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Fouten met betrekking tot prioriteitswaarden buiten de verwachte bereiken

Sommige klanten hadden gemeld het volgende foutbericht te ontvangen bij het openen van bestanden die zijn gemaakt in de 'Threat Modeling Tool 2016' of aangepaste sjablonen:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Dit probleem is opgelost in deze release.

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) of hoger
- .NET-versie vereist
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) of hoger
- Aanvullende vereisten
  - Er is een internetverbinding vereist om updates van de tool en sjablonen te ontvangen.

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)en bevat informatie [over het gebruik van de tool.](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)

## <a name="next-steps"></a>Volgende stappen

Download de nieuwste versie van de [Microsoft Threat Modeling Tool.](https://aka.ms/threatmodelingtool)
