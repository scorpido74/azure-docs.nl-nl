---
title: Microsoft Threat Modeling Tool release 03/22/2020 - Azure
description: Het documenteren van de releasenotes voor het hulpprogramma voor bedreigingsmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146860"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool update release 7.3.00316.1 - 03/22/2020

Versie 7.3.00316.1 van de Microsoft Threat Modeling Tool (TMT) is uitgebracht op 22 maart 2020 en bevat de volgende wijzigingen:

- Verbeterde toegankelijkheid
- Opgeloste fouten
- Nieuwe functie DiagramReader

## <a name="notable-bug-fixes"></a>Opmerkelijke bugfixes

### <a name="exporting-the-threat-list-to-csv"></a>De lijst met bedreigingen exporteren naar CSV

De export functie naar CSV was inconsequent selecteren welke velden uit de lijst met bedreigingen zou worden geëxporteerd. Nu worden alle velden uit de lijst met bedreigingen geëxporteerd naar het CSV-bestand. 

### <a name="ux-bugs"></a>UX bugs

- De Help-menu's in de primaire werkstroom (maken/openen/analyseren) en de ervaring van de sjablooneditor hebben nu consistente menuopties.
- De zoekbalk in het stencilvenster heeft nu een standaardcursor en er zijn geschikte labels toegevoegd.

## <a name="new-features"></a>Nieuwe functies

### <a name="diagramreader-feature-has-been-added"></a>DiagramReader-functie is toegevoegd

In het hoofdmenu is een nieuwe functie DiagramReader toegevoegd terwijl een model is geopend. Deze functie zal de grafische weergave van het model omzetten in een tekstgebaseerd verhaal. 

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen:
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) of hoger
- .NET-versie vereist:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) of hoger
- Aanvullende eisen:
  - Een internetverbinding om updates van de tool en sjablonen te ontvangen

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)en bevat informatie [over het gebruik van de tool.](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)

## <a name="next-steps"></a>Volgende stappen

Download de nieuwste versie van de [Microsoft Threat Modeling Tool.](https://aka.ms/threatmodelingtool)
