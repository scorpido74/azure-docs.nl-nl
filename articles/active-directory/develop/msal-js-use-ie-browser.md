---
title: Problemen op Internet Explorer (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Gebruik de Microsoft-verificatiebibliotheek voor JavaScript (MSAL.js) met de browser Internet Explorer.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695854"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Bekende problemen in Internet Explorer- en Microsoft Edge-browsers (MSAL.js)

Microsoft Authentication Library for JavaScript (MSAL.js) wordt gegenereerd voor [JavaScript ES5,](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) zodat deze kan worden uitgevoerd in Internet Explorer. Er zijn echter een paar dingen om te weten.

## <a name="run-an-app-in-internet-explorer"></a>Een app uitvoeren in Internet Explorer
Als u msal.js wilt gebruiken in toepassingen die in Internet Explorer kunnen worden uitgevoerd, moet u een verwijzing naar een belofte-polyfill toevoegen voordat u verwijst naar het MSAL.js-script.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Dit komt omdat Internet Explorer javascriptbeloften niet native ondersteunt.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Een toepassing afluisteren die wordt uitgevoerd in Internet Explorer

### <a name="running-in-production"></a>Hardlopen in productie
Het implementeren van uw toepassing naar productie (bijvoorbeeld in Azure Web-apps) werkt normaal gesproken prima, mits de eindgebruiker pop-ups heeft geaccepteerd. We hebben het getest met Internet Explorer 11.

### <a name="running-locally"></a>Lokaal actief
Als u uw toepassing die in Internet Explorer wordt uitgevoerd lokaal wilt uitvoeren en debuggen, moet u *http://localhost:1234*zich bewust zijn van de volgende overwegingen (ervan uitgaan dat u uw toepassing wilt uitvoeren als):

- Internet Explorer heeft een beveiligingsmechanisme met de naam "beveiligde modus", waardoor MSAL.js niet goed werkt. Onder de symptomen, nadat u zich aanmeldt, kan http://localhost:1234/nullde pagina worden doorgestuurd naar .

- Als u uw toepassing lokaal wilt uitvoeren en debuggen, moet u deze "beveiligde modus" uitschakelen. Hiervoor:

    1. Klik op **Internet Explorer-hulpprogramma's** (het tandwielpictogram).
    1. Selecteer **Internetopties** en vervolgens het tabblad **Beveiliging.**
    1. Klik op de **internetzone** en schakel **de schakel beveiligde modus inschakelen uit (hiervoor is het opnieuw opstarten van Internet Explorer)** vereist. Internet Explorer waarschuwt dat uw computer niet langer is beveiligd. Klik op **OK**.
    1. Internet Explorer opnieuw starten.
    1. Voer uw toepassing uit en debugt deze.

Wanneer u klaar bent, herstelt u de beveiligingsinstellingen van Internet Explorer.  Selecteer **Beveiliging Internetopties** -> **Internet Options** -> **Instellingen** -> **Alle zones opnieuw instellen op standaardniveau**.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [bekende problemen bij het gebruik van MSAL.js in Internet Explorer](msal-js-use-ie-browser.md).