---
title: Microsoft Threat Modeling Tool release 03/22/2020-Azure
description: De release opmerkingen voor het hulp programma voor het maken van bedreigingen vastleggen
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80146860"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool Update release 7.3.00316.1-03/22/2020

Versie 7.3.00316.1 van de Microsoft Threat Modeling Tool (TMT) is uitgebracht op 22 2020 maart en bevat de volgende wijzigingen:

- Verbeterde toegankelijkheid
- Opgeloste fouten
- Nieuwe DiagramReader-functie

## <a name="notable-bug-fixes"></a>Belangrijkste oplossingen voor oplossingen

### <a name="exporting-the-threat-list-to-csv"></a>De lijst met bedreigingen exporteren naar CSV

De functie exporteren naar CSV was inconsistent en selecteert welke velden uit de lijst met bedreigingen moeten worden geëxporteerd. Nu worden alle velden uit de lijst met bedreigingen geëxporteerd naar het CSV-bestand. 

### <a name="ux-bugs"></a>UX-fouten

- De Help-menu's in de primaire werk stroom (maken/openen/analyseren) en de sjabloon editor-ervaring hebben nu consistente menu opties.
- De zoek balk in het deel venster stencils heeft nu een standaard cursor en de juiste labels zijn toegevoegd.

## <a name="new-features"></a>Nieuwe functies

### <a name="diagramreader-feature-has-been-added"></a>De functie DiagramReader is toegevoegd

Er is een nieuwe DiagramReader-functie toegevoegd in het hoofd menu terwijl een model is geopend. Met dit onderdeel wordt de grafische weer gave van het model geconverteerd naar een op tekst gebaseerde afspiegeling. 

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen:
  - [Micro soft Windows 10 jubileum update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) of hoger
- .NET-versie vereist:
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) of hoger
- Aanvullende vereisten:
  - Een Internet verbinding voor het ontvangen van updates voor het hulp programma en voor sjablonen

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.Microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)en bevat informatie [over het gebruik van het hulp programma](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Volgende stappen

Down load de nieuwste versie van de [Microsoft Threat Modeling tool](https://aka.ms/threatmodelingtool).
