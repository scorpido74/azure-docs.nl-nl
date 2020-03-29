---
title: Probleem met het maken van een application proxy-toepassing | Microsoft Documenten
description: Problemen oplossen bij het maken van toepassingsproxytoepassingen in de Azure AD-beheerportal
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
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
ms.openlocfilehash: 104b98cba0948ec5d0896877e54eab1e7cd4049f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825808"
---
# <a name="problem-creating-an-application-proxy-application"></a>Probleem met het maken van een toepassingsproxytoepassing 

Hieronder staan enkele van de veelvoorkomende problemen waarmee mensen worden geconfronteerd bij het maken van een nieuwe applicatieproxytoepassing.

## <a name="recommended-documents"></a>Aanbevolen documenten 

Zie [Toepassingen publiceren met Azure AD Application Proxy](application-proxy-add-on-premises-application.md)voor meer informatie over het maken van een application proxy-toepassing via de beheerportal.

Als u de stappen in dat document volgt en er een fout optreedt bij het maken van de toepassing, raadpleegt u de foutgegevens voor informatie en suggesties voor het oplossen van de toepassing. De meeste foutmeldingen bevatten een voorgestelde oplossing. 

## <a name="specific-things-to-check"></a>Specifieke dingen om te controleren

Controleer het als volgt om veelvoorkomende fouten te voorkomen:

-   U bent een beheerder met toestemming om een application proxy-toepassing te maken

-   De interne URL is uniek

-   De externe URL is uniek

-   De URL's beginnen met http of https en eindigen met een "/"

-   De URL moet een domeinnaam zijn en geen IP-adres

Het foutbericht moet in de rechterbovenhoek worden weergegeven wanneer u de toepassing maakt. U ook het meldingspictogram selecteren om de foutmeldingen te zien.

   ![Meldingsprompt](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Volgende stappen
[Toepassingsproxy inschakelen in de Azure-portal](application-proxy-add-on-premises-application.md)
