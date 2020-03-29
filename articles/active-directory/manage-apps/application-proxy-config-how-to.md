---
title: Een toepassingsproxytoepassing configureren | Microsoft Documenten
description: Meer informatie over het maken en configureren van een APplication Proxy-toepassing in een paar eenvoudige stappen
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
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aaf2eb282bc3fd0b9f3853ce493c479a3d3c3a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807860"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Een toepassingsproxy-toepassing configureren

Met dit artikel u begrijpen hoe u een toepassingsproxytoepassing binnen Azure AD configureert om uw on-premises toepassingen aan de cloud bloot te stellen.

## <a name="recommended-documents"></a>Aanbevolen documenten

Volg de [publicatietoepassingen met Azure AD Application Proxy](application-proxy-add-on-premises-application.md)voor meer informatie over de initiële configuraties en het maken van een application proxy-toepassing via de beheerportal.

Zie [Toepassingsproxy inschakelen in de Azure-portal](application-proxy-add-on-premises-application.md)voor meer informatie over het configureren van connectors.

Zie Werken met aangepaste domeinen in [Azure AD Application Proxy](application-proxy-configure-custom-domain.md)voor informatie over het uploaden van certificaten en het gebruik van aangepaste domeinen.

## <a name="create-the-applicationsetting-the-urls"></a>De toepassing maken/de URL's instellen

Als u de stappen in de [publicatietoepassingen volgt met azure AD-toepassingsproxydocumentatie](application-proxy-add-on-premises-application.md) en er een fout optreedt bij het maken van de toepassing, raadpleegt u de foutgegevens voor informatie en suggesties voor het oplossen van de toepassing. De meeste foutmeldingen bevatten een voorgestelde oplossing. Controleer het als volgt om veelvoorkomende fouten te voorkomen:

- U bent een beheerder met toestemming om een application proxy-toepassing te maken
- De interne URL is uniek
- De externe URL is uniek
- De URL's beginnen met http of https en eindigen met een "/"
- De URL moet een domeinnaam zijn, geen IP-adres

Het foutbericht moet in de rechterbovenhoek worden weergegeven wanneer u de toepassing maakt. U ook het meldingspictogram selecteren om de foutmeldingen te zien.

![Hiermee wordt weergegeven waar u de meldingsprompt vinden in de Azure-portal](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Connectors/connectorgroepen configureren

Als u problemen ondervindt bij het configureren van uw toepassing vanwege waarschuwing over de connectors en connectorgroepen, raadpleegt u instructies voor het inschakelen van Application Proxy voor meer informatie over het downloaden van connectors. Als u meer wilt weten over connectoren, raadpleegt u de [documentatie van de connectors.](application-proxy-connectors.md)

Als uw connectors inactief zijn, betekent dit dat ze de service niet kunnen bereiken. Dit komt vaak omdat alle benodigde poorten niet open zijn. Zie het gedeelte vereisten van de inschakelen toepassingsproxy-documentatie om een lijst met vereiste poorten te bekijken.

## <a name="upload-certificates-for-custom-domains"></a>Certificaten uploaden voor aangepaste domeinen

Met aangepaste domeinen u het domein van uw externe URL's opgeven. Als u aangepaste domeinen wilt gebruiken, moet u het certificaat voor dat domein uploaden. Zie Werken met aangepaste domeinen [in Azure AD Application Proxy](application-proxy-configure-custom-domain.md)voor informatie over het gebruik van aangepaste domeinen en certificaten.

Als u problemen ondervindt bij het uploaden van uw certificaat, zoekt u naar de foutmeldingen in de portal voor meer informatie over het probleem met het certificaat. Veelvoorkomende certificaatproblemen zijn onder andere:

- Verlopen certificaat
- Certificaat is zelf ondertekend
- Certificaat ontbreekt de privésleutel

Het foutbericht wordt in de rechterbovenhoek weergegeven terwijl u het certificaat probeert te uploaden. U ook het meldingspictogram selecteren om de foutmeldingen te zien.

## <a name="next-steps"></a>Volgende stappen

[Toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md)
