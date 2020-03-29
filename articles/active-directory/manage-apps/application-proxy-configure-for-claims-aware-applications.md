---
title: Claimsbewuste apps - Azure AD App Proxy | Microsoft Documenten
description: On-premises ASP.NET-toepassingen publiceren die ADFS-claims accepteren voor veilige externe toegang door uw gebruikers.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbc5c356ea5a542fdc12b11aff236c56b146b3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68477243"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Werken met claimsbewuste apps in Application Proxy
[Claimsbewuste apps](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) voeren een omleiding uit naar de Security Token Service (STS). De STS vraagt referenties van de gebruiker in ruil voor een token en leidt de gebruiker vervolgens door naar de toepassing. Er zijn een paar manieren om Application Proxy in staat te stellen om met deze omleidingen te werken. Gebruik dit artikel om uw implementatie te configureren voor apps die op claims zijn gericht. 

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat de STS waarnaar de claimbewuste app wordt omgeleid, beschikbaar is buiten uw on-premises netwerk. U de STS beschikbaar stellen door deze bloot te leggen via een proxy of door externe verbindingen toe te staan. 

## <a name="publish-your-application"></a>Uw toepassing publiceren

1. Publiceer uw toepassing volgens de instructies beschreven in [Publicatie toepassingen met Application Proxy](application-proxy-add-on-premises-application.md).
2. Navigeer naar de toepassingspagina in de portal en selecteer **Eén aanmelding**.
3. Als u **Azure Active Directory** als **verificatiemethode**hebt gekozen, selecteert u **Azure AD single sign-on disabled** als uw **interne verificatiemethode**. Als u **Passthrough** als **verificatiemethode**hebt gekozen, hoeft u niets te wijzigen.

## <a name="configure-adfs"></a>ADFS configureren

U ADFS op twee manieren configureren voor claimbewuste apps. De eerste is met behulp van aangepaste domeinen. De tweede is met WS-Federatie. 

### <a name="option-1-custom-domains"></a>Optie 1: Aangepaste domeinen

Als alle interne URL's voor uw toepassingen volledig gekwalificeerde domeinnamen (FQDN's) zijn, u [aangepaste domeinen](application-proxy-configure-custom-domain.md) voor uw toepassingen configureren. Gebruik de aangepaste domeinen om externe URL's te maken die hetzelfde zijn als de interne URL's. Wanneer uw externe URL's overeenkomen met uw interne URL's, werken de STS-omleidingen of uw gebruikers on-premises of op afstand zijn. 

### <a name="option-2-ws-federation"></a>Optie 2: WS-Federatie

1. Open ADFS-beheer.
2. Ga naar **Vertrouwensrelaties van relying**party, klik met de rechtermuisknop op de app die u publiceert met Application Proxy en kies **Eigenschappen**.  

   ![Relying Party Trusts klik met de rechtermuisknop op de naam van de app - screenshot](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Selecteer op het tabblad **Eindpunten** onder **Doelpunttype**de optie **WS-Federation**.
4. Voer **onder Vertrouwde URL**de URL in die u hebt ingevoerd in de toepassingsproxy onder Externe **URL** en klik op **OK**.  

   ![Een eindpunt toevoegen - Vertrouwde URL-waarde instellen - schermafbeelding](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Volgende stappen
* [Eén aanmelding inschakelen](configure-single-sign-on-non-gallery-applications.md) voor toepassingen die niet op claims zijn gebaseerd
* [Native client-apps inschakelen om te communiceren met proxytoepassingen](application-proxy-configure-native-client-application.md)


