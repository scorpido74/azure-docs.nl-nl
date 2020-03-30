---
title: Problemen met TLS/SSL oplossen (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over wat u doen met verschillende problemen met TLS/SSL-certificaten met de MSAL. Doelstelling-C bibliotheek.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 42bd016a9d0882cc97eaa800235226b2de9569a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369402"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>How to: MSAL oplossen voor iOS- en macOS TLS/SSL-problemen

In dit artikel vindt u informatie om problemen op te lossen die u tegenkomen tijdens het gebruik van de [Microsoft Authentication Library (MSAL) voor iOS en macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Netwerkproblemen

**Fout -1200**: "Er is een SSL-fout opgetreden en er kan geen beveiligde verbinding met de server worden gemaakt."

Deze fout betekent dat de verbinding niet veilig is. Dit gebeurt wanneer een certificaat ongeldig is. Voor meer informatie, inclusief welke server de `NSURLErrorFailingURLErrorKey` TLS-controle niet controleert, verwijzen wij u in het `userInfo` woordenboek van het foutobject.

Deze fout komt uit de netwerkbibliotheek van Apple. Een volledige lijst met NSURL-foutcodes is in NSURLError.h in de macOS- en iOS-sdk's. Zie URL Loading System [Error Codes](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)voor meer informatie over deze fout .

## <a name="certificate-issues"></a>Certificaatproblemen

Als de URL die een ongeldig certificaat aanbiedt verbinding maakt met de server die u wilt gebruiken als onderdeel van de verificatiestroom, is het een goede start om het probleem te diagnosticeren het testen van de URL met een SSL-validatieservice, zoals [SSL Server Test.](https://www.ssllabs.com/ssltest/analyze.html) Het test de server op een breed scala van scenario's en browsers en controleert op veel bekende kwetsbaarheden.

Standaard past de nieuwe [ATS-functie (App Transport Security) van](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) Apple een strenger beveiligingsbeleid toe op apps die TLS/SSL-certificaten gebruiken. Sommige besturingssystemen en webbrowsers zijn standaard begonnen met het afdwingen van een aantal van deze beleidsregels. Om veiligheidsredenen raden we u aan ATS niet uit te schakelen.

Certificaten met SHA-1 hashes hebben bekende kwetsbaarheden. De meeste moderne webbrowsers staan geen certificaten toe met SHA-1 hashes.

## <a name="captive-portals"></a>Captive portalen

Een captive portal presenteert een webpagina aan een gebruiker wanneer ze voor het eerst toegang tot een Wi-Fi-netwerk en nog niet is verleend toegang tot dat netwerk. Het onderschept hun internetverkeer totdat de gebruiker voldoet aan de eisen van het portaal. Netwerkfouten omdat de gebruiker geen verbinding kan maken met netwerkbronnen, wordt verwacht totdat de gebruiker verbinding maakt via de portal.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [captive portals](https://en.wikipedia.org/wiki/Captive_portal) en Apple's nieuwe App Transport [Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) functie.
