---
title: De inrichten van gebruikers in de Azure AD Gallery-app duurt uren of langer
description: Hoe u erachter komen waarom het inrichten van uw aanvraag langer kan duren dan u had verwacht
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522642"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Het inrichten van gebruikers op een Azure AD Gallery-toepassing duurt uren of langer

Wanneer de eerste cyclus automatische inrichting voor een toepassing inschakelt, kan de eerste cyclus 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-map en het aantal gebruikers dat in het bereik is voor inrichten. 

Latere synchronisaties na de eerste cyclus zijn sneller, omdat de inrichtingsservice watermerken opslaat die de status van beide systemen na de eerste cyclus vertegenwoordigen, waardoor de prestaties van de volgende synchronisaties worden verbeterd.

## <a name="how-to-improve-provisioning-performance"></a>Hoe de provisioning prestaties te verbeteren

Als de eerste cyclus meer dan een paar uur duurt, is er één ding dat u doen om de prestaties te verbeteren:

-   **Gebruikersscopingfilters.** Met scopingfilters u de gegevens die de inrichtingsservice uit Azure AD haalt, verfijnen door gebruikers uit te filteren op basis van specifieke kenmerkwaarden. Zie [Op kenmerken gebaseerde toepassingsinrichting met scopingfilters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie over scopingfilters.

## <a name="next-steps"></a>Volgende stappen
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory)

