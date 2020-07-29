---
title: Microsoft Threat Modeling Tool versie 1/29/2019
titleSuffix: Azure
description: De release opmerkingen voor het hulp programma voor het maken van bedreigingen vastleggen
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 7d0be8d7243331264c10a407e3d78370ea798928
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78269775"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool-updaterelease 7.1.60126.1 - 1/29/2019

Versie 7.1.60126.1 van de Microsoft Threat Modeling Tool is uitgebracht op 29 2019 januari en bevat de volgende wijzigingen:

- De mini maal vereiste versie van .NET is verhoogd naar [.net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- De mini maal vereiste versie van Windows is verhoogd tot [Windows 10 jubileum update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vanwege de .net-afhankelijkheid.
- Er is een functie voor model validatie in-en uitschakeling toegevoegd aan het menu opties van het hulp programma.
- Er zijn verschillende koppelingen in de bedreigings eigenschappen bijgewerkt.
- Kleine UX wijzigingen in het Start scherm van het hulp programma.
- De Threat Modeling Tool neemt nu de TLS-instellingen van het hostbesturingssysteem over en wordt ondersteund in omgevingen waarvoor TLS 1,2 of hoger is vereist.

## <a name="feature-changes"></a>Functie wijzigingen

### <a name="model-validation-option"></a>Model validatie optie

Op basis van feedback van klanten is een optie toegevoegd aan het hulp programma om de model validatie in of uit te scha kelen. Als uw sjabloon een enkele eenrichtings gegevens stroom tussen twee objecten heeft gebruikt, hebt u mogelijk een fout bericht ontvangen in het bericht frame: voor object naam is ten minste één wille keurig. Door model validatie uit te scha kelen, voor komt u dat deze waarschuwingen in de weer gave worden weer gegeven.

De optie voor het in-en uitschakelen van model validatie vindt u in het menu instellingen voor bestands >->opties. De standaard waarde voor deze instelling is uitgeschakeld.

![Model validatie optie](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen
  - [Micro soft Windows 10 jubileum update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) of hoger
- .NET-versie vereist
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) of hoger
- Aanvullende vereisten
  - Er is een Internet verbinding vereist voor het ontvangen van updates voor het hulp programma en voor sjablonen.

## <a name="known-issues"></a>Bekende problemen

### <a name="unsupported-systems"></a>Niet-ondersteunde systemen

#### <a name="issue"></a>Probleem

Gebruikers van Windows 10-systemen waarop .NET 4.7.1 of hoger niet kan worden geïnstalleerd, zoals Windows 10 Enter prise LTSB (versie 1507), kunnen het hulp programma niet openen na de upgrade. Deze oudere versies van Windows worden niet meer ondersteund voor de Threat Modeling Tool en moeten de meest recente update niet installeren.

#### <a name="workaround"></a>Tijdelijke oplossing

Gebruikers van Windows 10 Enter prise LTSB (versie 1507) die de nieuwste update hebben geïnstalleerd, kunnen terugkeren naar de vorige versie van de Threat Modeling Tool via het dialoog venster Uninstall in apps &-functies.

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.Microsoft.com](threat-modeling-tool.md)en bevat informatie [over het gebruik van het hulp programma](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Volgende stappen

Down load de nieuwste versie van de [Microsoft Threat Modeling tool](https://aka.ms/threatmodelingtool).
