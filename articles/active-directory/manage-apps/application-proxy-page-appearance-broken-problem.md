---
title: App-pagina wordt niet correct weergegeven voor de app Application Proxy | Microsoft Documenten
description: Richtlijnen wanneer de pagina niet correct wordt weergegeven in een toepassingsproxytoepassing die u hebt geïntegreerd met Azure AD
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
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13e73f0ed56648ce162f00d6df5e7b86a922ca01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381428"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Toepassingspagina wordt niet correct weergegeven voor een toepassing van toepassingsproxy

Met dit artikel u problemen met Azure Active Directory Application Proxy-toepassingen oplossen wanneer u naar de pagina navigeert, maar iets op de pagina ziet er niet correct uit.

## <a name="overview"></a>Overzicht
Wanneer u een application proxy-app publiceert, zijn alleen pagina's onder uw hoofdpagina toegankelijk wanneer u de toepassing opent. Als de pagina niet correct wordt weergegeven, mist de interne URL van de hoofdmap die voor de toepassing wordt gebruikt mogelijk bepaalde paginabronnen. Als u dit wilt oplossen, moet u ervoor zorgen dat u *alle* bronnen voor de pagina hebt gepubliceerd als onderdeel van uw toepassing.

U controleren of ontbrekende bronnen het probleem is door uw netwerktracker te openen (zoals Fiddler- of F12-hulpprogramma's in Internet Explorer/Microsoft Edge), de pagina te laden en te zoeken naar 404 fouten. Dit geeft aan dat de pagina's momenteel niet kunnen worden gevonden en dat u ze moet publiceren.

Stel dat u in dit geval een onkostentoepassing `http://myapps/expenses`hebt gepubliceerd met de `http://myapps/style.css`interne URL, maar dat de app de stijlpagina gebruikt. In dit geval wordt de stylesheet niet gepubliceerd in uw toepassing, dus het laden van de uitgaven app gooien een 404 fout tijdens het proberen om style.css laden. In dit voorbeeld wordt het probleem opgelost door de `http://myapp/`toepassing te publiceren met een interne URL .

## <a name="problems-with-publishing-as-one-application"></a>Problemen met publiceren als één toepassing

Als het niet mogelijk is om alle bronnen binnen dezelfde toepassing te publiceren, moet u meerdere toepassingen publiceren en koppelingen tussen deze toepassingen inschakelen.

Hiervoor raden we u aan de oplossing [voor aangepaste domeinen](application-proxy-configure-custom-domain.md) te gebruiken. Deze oplossing vereist echter dat u eigenaar bent van het certificaat voor uw domein en dat uw toepassingen volledig gekwalificeerde domeinnamen (FQDN's) gebruiken. Zie voor andere opties de [documentatie voor het oplossen van verbroken koppelingen](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Volgende stappen
[Toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md)
