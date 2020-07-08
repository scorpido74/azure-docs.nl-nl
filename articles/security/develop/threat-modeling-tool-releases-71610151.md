---
title: Microsoft Threat Modeling Tool release 10/16/2019-Azure
description: De release opmerkingen voor het hulp programma voor het maken van bedreigingen vastleggen
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552046"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool-updaterelease 7.1.61015.1 - 16 oktober 2019

Versie 7.1.61015.1 van de Microsoft Threat Modeling Tool (TMT) is uitgebracht op 16 2019 oktober en bevat de volgende wijzigingen:

- Verbeterde toegankelijkheid
- Opgeloste fouten
- Nieuwe stencils voor Azure Logic Apps en Azure Data Explorer

## <a name="notable-bug-fixes"></a>Belangrijkste oplossingen voor oplossingen

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Verbeterde achterwaartse compatibiliteit met bestanden die zijn gemaakt in ' Threat Modeling Tool 2016 '

Verschillende bugs met betrekking tot het openen of weer geven van bedreigings model bestanden die zijn gemaakt in ' Threat Modeling Tool 2016 ' zijn opgelost.

## <a name="feature-enhancements"></a>Functie verbeteringen

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Nieuwe stencils voor Azure Logic Apps en Azure Data Explorer

Nieuwe stencils voor Azure Logic Apps en Azure Data Explorer zijn toegevoegd aan het Azure-stencil, samen met de bijbehorende dreigingen en oplossingen.

![Azure Logic Apps-en Azure Data Explorer stencils](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Bekende problemen

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Fouten met betrekking tot prioriteits waarden buiten de verwachte bereiken

Sommige klanten hebben het volgende fout bericht gerapporteerd bij het openen van bestanden die zijn gemaakt in het Threat Modeling Tool 2016 of aangepaste sjablonen:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Dit probleem wordt onderzocht

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen
  - [Micro soft Windows 10 jubileum update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) of hoger
- .NET-versie vereist
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) of hoger
- Aanvullende vereisten
  - Er is een Internet verbinding vereist voor het ontvangen van updates voor het hulp programma en voor sjablonen.

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.Microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)en bevat informatie [over het gebruik van het hulp programma](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Volgende stappen

Down load de nieuwste versie van de [Microsoft Threat Modeling tool](https://aka.ms/threatmodelingtool).
