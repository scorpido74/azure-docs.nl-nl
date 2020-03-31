---
title: Fixes for the "You can't get there from here" error - Azure AD
description: Vind potentiële oplossingen waarom je de foutmelding 'Je er niet vanaf hier komen' krijgt.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 2484de4e554d16ba049d206981a44654ede28a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190031"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Mogelijke oplossingen voor de foutmelding Je er niet komen vanaf hier foutmelding

Terwijl u toegang hebt tot de interne webapps of -services van uw organisatie, krijgt u mogelijk een foutmelding met de **tekst: 'U er vanaf hier niet komen.** Dit bericht betekent dat uw organisatie een beleid heeft ingevoerd dat voorkomt dat uw apparaat toegang krijgt tot de bronnen van uw organisatie. Hoewel u uiteindelijk contact moet opnemen met uw helpdesk om dit probleem op te lossen, u het eerst proberen.

## <a name="make-sure-youre-using-a-supported-browser"></a>Zorg ervoor dat u een ondersteunde browser gebruikt
Als u de **U er niet komen vanaf hier** bericht te zeggen dat je probeert om toegang te krijgen tot de sites van uw organisatie vanuit een niet-ondersteunde browser, controleer welke browser je draait.

![Foutbericht met betrekking tot browserondersteuning](media/user-help-device-remediation/browser-version.png)

Om dit probleem op te lossen, moet u een ondersteunde browser installeren en uitvoeren op basis van uw besturingssysteem. Als u Windows 10 gebruikt, zijn de ondersteunde browsers Microsoft Edge, Internet Explorer en Google Chrome. Als u een ander besturingssysteem gebruikt, u de volledige lijst [met ondersteunde browsers](../conditional-access/concept-conditional-access-conditions.md#supported-browsers)controleren.

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Zorg ervoor dat u een ondersteund besturingssysteem gebruikt
Zorg ervoor dat u een ondersteunde versie van het besturingssysteem uitvoert, waaronder:

- **Windows-client.** Windows 7 of hoger.

- **Windows Server.** Windows Server 2008 R2 of hoger.

- **Macos.** macOS X of hoger

- **Android en iOS.** Nieuwste versie van mobiele Android- en iOS-besturingssystemen

Om dit probleem op te lossen, moet u een ondersteund besturingssysteem installeren en uitvoeren.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Controleer of uw apparaat is aangesloten bij uw netwerk
Als u het **bericht U er niet komen vanaf dit** bericht dat uw apparaat niet voldoet aan het toegangsbeleid van uw organisatie, controleert u of u lid bent geworden van uw apparaat op het netwerk van uw organisatie.

![Foutbericht met betrekking tot de vraag of u zich in uw netwerk bevindt](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Controleren of uw apparaat is aangesloten bij uw netwerk
1. Meld u aan bij Windows met uw werk- of schoolaccount. Bijvoorbeeld alain@contoso.com.

2. Maak verbinding met het netwerk van uw organisatie via een VPN (Virtual Private Network) of DirectAccess.

3. Nadat u bent verbonden, drukt u op de **Windows-logotoets+L** om uw apparaat te vergrendelen.

4. Ontgrendel uw apparaat met uw werk- of schoolaccount en probeer vervolgens opnieuw toegang te krijgen tot de problematische app of -service.

    Als u de foutmelding U hier niet opnieuw **komen,** selecteert u de koppeling **Meer details** en neemt u contact op met uw helpdesk met de details.

### <a name="to-join-your-device-to-your-network"></a>Uw apparaat aansluiten bij uw netwerk
Als uw apparaat niet is aangesloten bij het netwerk van uw organisatie, u een van de twee dingen doen:

- **Sluit je aan bij je werkapparaat.** Sluit uw Windows 10-apparaat in handen van uw bedrijf aan het netwerk van uw organisatie, zodat u toegang hebt tot mogelijk beperkte bronnen. Zie [Uw werkapparaat aansluiten bij het netwerk van uw organisatie voor](user-help-join-device-on-network.md)meer informatie en stapsgewijze instructies.

- **Registreer uw persoonlijke apparaat voor het werk.** Registreer uw persoonlijke apparaat, meestal een telefoon of tablet, op het netwerk van uw organisatie. Nadat uw apparaat is geregistreerd, heeft het toegang tot de beperkte resources van uw organisatie. Zie [Uw persoonlijke apparaat registreren op](user-help-register-device-on-network.md)het netwerk van uw organisatie voor meer informatie en stapsgewijze instructies.

## <a name="next-steps"></a>Volgende stappen
- [Wat is de MyApps portal?](active-directory-saas-access-panel-introduction.md)

- [Aanmelden met uw telefoon, niet met uw wachtwoord](user-help-auth-app-sign-in.md)
