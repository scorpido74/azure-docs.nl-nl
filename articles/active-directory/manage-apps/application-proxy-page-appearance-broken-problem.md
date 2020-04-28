---
title: App-pagina wordt niet correct weer gegeven voor de app Application proxy | Microsoft Docs
description: Richt lijnen wanneer de pagina niet correct wordt weer gegeven in een toepassings proxy toepassing die u hebt geïntegreerd met Azure AD
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "68381428"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Toepassings pagina wordt niet correct weer gegeven voor een toepassings proxy toepassing

Dit artikel helpt u bij het oplossen van problemen met Azure Active Directory-toepassingsproxy-toepassingen wanneer u naar de pagina navigeert, maar iets op de pagina wordt niet correct weer geven.

## <a name="overview"></a>Overzicht
Wanneer u een app voor een toepassings proxy publiceert, zijn alleen pagina's onder uw root toegankelijk bij het openen van de toepassing. Als de pagina niet correct wordt weer gegeven, ontbreekt er mogelijk een aantal pagina bronnen in de interne basis-URL die voor de toepassing wordt gebruikt. Zorg ervoor dat u *alle* resources voor de pagina hebt gepubliceerd als onderdeel van uw toepassing om dit probleem op te lossen.

U kunt controleren of ontbrekende resources het probleem zijn door uw netwerk vastleggen (zoals Fiddler of F12-hulpprogram ma's in Internet Explorer/micro soft Edge) te openen, de pagina te laden en te zoeken naar 404-fouten. Hiermee wordt aangegeven dat de pagina's die momenteel niet kunnen worden gevonden en dat u ze moet publiceren.

Als voor beeld van dit geval wordt ervan uitgegaan dat u een onkosten toepassing hebt gepubliceerd `http://myapps/expenses`met behulp van de interne URL `http://myapps/style.css`, maar de app maakt gebruik van het opmaak model. In dit geval wordt het opmaak model niet gepubliceerd in uw toepassing, waardoor het laden van de app onkosten een 404-fout genereert tijdens het laden van style. CSS. In dit voor beeld wordt het probleem opgelost door de toepassing te publiceren met een interne `http://myapp/`URL.

## <a name="problems-with-publishing-as-one-application"></a>Problemen met publiceren als één toepassing

Als het niet mogelijk is om alle resources binnen dezelfde toepassing te publiceren, moet u meerdere toepassingen publiceren en koppelingen ertussen inschakelen.

Hiervoor raden wij u aan de oplossing voor [aangepaste domeinen](application-proxy-configure-custom-domain.md) te gebruiken. Deze oplossing vereist echter dat u eigenaar bent van het certificaat voor uw domein en dat uw toepassingen volledig gekwalificeerde domein namen (FQDN) gebruiken. Zie de [documentatie problemen oplossen met verbroken koppelingen](application-proxy-page-links-broken-problem.md)voor andere opties.

## <a name="next-steps"></a>Volgende stappen
[Toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md)
