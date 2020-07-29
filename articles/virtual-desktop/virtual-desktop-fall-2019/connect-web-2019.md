---
title: Verbinding maken met het virtuele bureau blad van Windows (klassieke) webclient-Azure
description: Verbinding maken met het virtuele bureau blad van Windows (klassiek) met behulp van de webclient.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: efe97c86ebfac8e130489b3105a97302866d6822
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87270376"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>Verbinding maken met het virtuele bureau blad van Windows (klassiek) met de webclient

>[!IMPORTANT]
>Deze inhoud is van toepassing op het virtuele bureau blad van Windows (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager virtueel-bureaublad objecten van Windows. Zie [dit artikel](../connect-web.md)als u probeert Azure Resource Manager virtueel-bureaublad objecten van Windows te beheren.

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

Navigeer in een browser naar de webclient met virtueel bureau blad van Windows op <https://rdweb.wvd.microsoft.com/webclient> en meld u aan met uw gebruikers account.

>[!NOTE]
>Als u een virtueel bureau blad van Windows gebruikt met Azure Resource Manager integratie, kunt u in plaats daarvan verbinding maken met uw resources <https://rdweb.wvd.microsoft.com/arm/webclient> .

>[!NOTE]
>Als u zich al hebt aangemeld met een ander Azure Active Directory account dan dat u wilt gebruiken voor virtueel bureau blad van Windows, moet u zich afmelden of een persoonlijk browser venster gebruiken.

Nadat u zich hebt aangemeld, ziet u nu een lijst met resources. U kunt resources starten door ze te selecteren zoals u een normale app op het tabblad **alle resources** zou doen.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het gebruik van de webclient naar aan [de slag met de webclient](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
