---
title: Toegang tot Azure AD App Proxy-apps in Teams | Microsoft Documenten
description: Gebruik Azure AD Application Proxy om toegang te krijgen tot uw on-premises toepassing via Microsoft Teams.
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
ms.date: 09/05/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037e005993a54e525560571a6d893197af99b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807760"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Toegang tot uw on-premises toepassingen via Microsoft Teams

Azure Active Directory Application Proxy biedt u één aanmelding bij on-premises toepassingen, waar u ook bent. Microsoft Teams stroomlijnt uw gezamenlijke inspanningen op één plek. Als u de twee samenintegreert, kunnen uw gebruikers in elke situatie productief zijn met hun teamgenoten.

Uw gebruikers kunnen cloud-apps toevoegen aan hun Teams-kanalen [met behulp van tabbladen,](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US)maar hoe zit het met de SharePoint-sites of planningstool die on-premises worden gehost? Application Proxy is de oplossing. Ze kunnen apps die via Application Proxy zijn gepubliceerd toevoegen aan hun kanalen met dezelfde externe URL's die ze altijd gebruiken om op afstand toegang te krijgen tot hun apps. En omdat Application Proxy verifieert via Azure Active Directory, krijgen uw gebruikers één aanmeldingservaring.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>De toepassingsproxyconnector installeren en uw app publiceren

Als u dit nog niet hebt gedaan, [configureert u Application Proxy voor uw tenant en installeert u de connector.](application-proxy-add-on-premises-application.md) [Publiceer vervolgens uw on-premises toepassing](application-proxy-add-on-premises-application.md) voor externe toegang. Wanneer u de app publiceert, noteer dan de externe URL omdat deze wordt gebruikt om de app aan Teams toe te voegen.

Als uw apps al zijn gepubliceerd, maar de externe URL's niet meer worden onthouden, zoekt u ze op in de [Azure-portal.](https://portal.azure.com) Meld u aan en navigeer vervolgens naar **Azure Active Directory** > **Enterprise-toepassingen** > **Alle toepassingen** > uw app > **toepassingsproxy**selecteren.

## <a name="add-your-app-to-teams"></a>Uw app toevoegen aan Teams

Zodra u de app publiceert via Application Proxy, laat u uw gebruikers weten dat ze deze direct als tabblad in hun Teams-kanalen kunnen toevoegen en vervolgens is de app beschikbaar voor iedereen in het team om te gebruiken. Laat ze deze drie stappen volgen:

1. Navigeer naar het Ploegenkanaal waar u deze **+** app wilt toevoegen en selecteer om een tabblad toe te voegen.

   ![Selecteer + om een tabblad toe te voegen in Teams](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Selecteer **Website** op de tabopties.

   ![Website selecteren in het tabbladscherm Toevoegen](./media/application-proxy-integrate-with-teams/website.png)

1. Geef het tabblad een naam en stel de URL in op de externe URL van de toepassingsproxy.

   ![Het tabblad een naam geven en de externe URL toevoegen](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Zodra een lid van een team het tabblad toevoegt, wordt het weergegeven voor iedereen in het kanaal. Gebruikers die toegang hebben tot de app krijgen toegang tot één aanmelding met de referenties die ze voor Microsoft Teams gebruiken. Gebruikers die geen toegang hebben tot de app, kunnen het tabblad in Teams zien, maar worden geblokkeerd totdat u ze machtigingen geeft aan de on-premises app en de gepubliceerde Azure-portalversie van de app.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [publiceren van on-premises SharePoint-sites](application-proxy-integrate-with-sharepoint-server.md) met Application Proxy.
- Configureer uw apps om [aangepaste domeinen](application-proxy-configure-custom-domain.md) te gebruiken voor hun externe URL.
