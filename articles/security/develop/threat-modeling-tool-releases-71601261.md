---
title: Microsoft Threat Modeling Tool release 1/29/2019
titleSuffix: Azure
description: Het documenteren van de releasenotes voor het hulpprogramma voor bedreigingsmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 7d0be8d7243331264c10a407e3d78370ea798928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269775"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool-updaterelease 7.1.60126.1 - 1/29/2019

Versie 7.1.60126.1 van de Microsoft Threat Modeling Tool is uitgebracht op 29 januari 2019 en bevat de volgende wijzigingen:

- De minimaal vereiste versie van .NET is verhoogd naar [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- De minimaal vereiste versie van Windows is verhoogd naar [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vanwege de .NET-afhankelijkheid.
- Er is een functie voor modelvalidatie toegevoegd aan het menu Opties van het gereedschap.
- Verschillende koppelingen in de bedreigingseigenschappen zijn bijgewerkt.
- Kleine UX-wijzigingen in het startscherm van de tool.
- De Threat Modeling Tool erft nu de TLS-instellingen van het hostbesturingssysteem en wordt ondersteund in omgevingen waarvoor TLS 1.2 of meer nodig is.

## <a name="feature-changes"></a>Functiewijzigingen

### <a name="model-validation-option"></a>Modelvalidatie, optie

Op basis van feedback van klanten is er een optie aan het hulpprogramma toegevoegd om de modelvalidatie in of uit te schakelen. Als uw sjabloon voorheen één unidirectionele gegevensstroom tussen twee objecten heeft gebruikt, hebt u mogelijk een foutbericht ontvangen in het frame Berichten met de vermelding: ObjectsName vereist ten minste één 'Elke'. Als u modelvalidatie uitschakelt, wordt voorkomen dat deze waarschuwingen in de weergave worden weergegeven.

De optie om modelvalidatie in- en uit te schakelen is te vinden in het menu Instellingen->instellingen bestand->. De standaardwaarde voor deze instelling is uitgeschakeld.

![Modelvalidatie, optie](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) of hoger
- .NET-versie vereist
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) of hoger
- Aanvullende vereisten
  - Er is een internetverbinding vereist om updates van de tool en sjablonen te ontvangen.

## <a name="known-issues"></a>Bekende problemen

### <a name="unsupported-systems"></a>Niet-ondersteunde systemen

#### <a name="issue"></a>Probleem

Gebruikers van Windows 10-systemen die .NET 4.7.1 of hoger niet kunnen installeren, zoals Windows 10 Enterprise LTSB (versie 1507), kunnen het hulpprogramma niet openen na het upgraden. Deze oudere versies van Windows worden niet langer ondersteund platforms voor de Threat Modeling Tool, en mag niet de nieuwste update te installeren.

#### <a name="workaround"></a>Tijdelijke oplossing

Gebruikers van Windows 10 Enterprise LTSB (versie 1507) die de nieuwste update hebben geïnstalleerd, kunnen terugkeren naar de vorige versie van het hulpprogramma voor bedreigingsmodellering via het dialoogvenster Verwijderen in Apps &-functies.

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.microsoft.com](threat-modeling-tool.md)en bevat informatie [over het gebruik van de tool.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Volgende stappen

Download de nieuwste versie van de [Microsoft Threat Modeling Tool.](https://aka.ms/threatmodelingtool)
