---
title: Verbinding maken met een virtueel bureau blad van Windows met de webclient-Azure
description: Verbinding maken met het virtuele bureau blad van Windows via de webclient.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4bb54db8a18baa237f1c32c223b5f609638c7523
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080621"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Verbinding maken met een virtueel bureau blad van Windows met de webclient

>[!IMPORTANT]
>Deze inhoud is van toepassing op de update uit het voorjaar van 2020 met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/connect-web-2019.md) als u de release van Windows Virtual Desktop uit het najaar van 2019 zonder Azure Resource Manager-objecten gebruikt.
>
> De update van Windows Virtual Desktop uit het voorjaar van 2020 is momenteel beschikbaar als openbare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de webclient kunt u vanuit een webbrowser toegang krijgen tot uw virtuele bureau blad-resources zonder het langdurige installatie proces.

>[!NOTE]
>De webclient heeft momenteel geen ondersteuning voor mobiele besturings systemen.

## <a name="supported-operating-systems-and-browsers"></a>Ondersteunde besturingssystemen en browsers

Hoewel een voor HTML5 geschikte browser zou moeten werken, ondersteunen we de volgende besturings systemen en browsers officieel.

| Browser           | Ondersteund besturings systeem                     | Opmerkingen               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versie 55 of hoger |
| Google Chrome     | Windows-, macOS-, Linux-en Chrome-besturings systeem |                     |

## <a name="access-remote-resources-feed"></a>Toegang tot externe resources-feed

Ga in een browser naar de Azure Resource Manager-geïntegreerde versie van de Windows Virtual Desktop-WebClient op <https://rdweb.wvd.microsoft.com/arm/webclient> en meld u aan met uw gebruikers account.

>[!NOTE]
>Als u de versie van het Windows-bureau blad Virtual Desktop 2019 zonder Azure Resource Manager-integratie gebruikt, kunt u in plaats daarvan verbinding maken met uw resources <https://rdweb.wvd.microsoft.com/webclient> .

>[!NOTE]
>Als u zich al hebt aangemeld met een ander Azure Active Directory account dan dat u wilt gebruiken voor virtueel bureau blad van Windows, moet u zich afmelden of een persoonlijk browser venster gebruiken.

Nadat u zich hebt aangemeld, ziet u nu een lijst met resources. U kunt resources starten door ze te selecteren zoals u een normale app op het tabblad **alle resources** zou doen.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het gebruik van de webclient naar aan [de slag met de webclient](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
