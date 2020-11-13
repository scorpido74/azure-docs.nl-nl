---
title: Extern bureaublad-gateway services met Azure Active Directory
description: Richt lijnen voor architectuur over het bereiken van Extern bureaublad-gateway services met Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57466cccb71444d5711a9221c324a107757c5e82
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576792"
---
# <a name="remote-desktop-gateway-services"></a>Extern bureaublad-gateway services

Een standaard-Extern bureaublad-services-implementatie (RDS) omvat diverse [extern bureaublad functie Services](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) die worden uitgevoerd op Windows Server. De RDS-implementatie met Azure Active Directory (Azure AD)-toepassings proxy heeft een permanente uitgaande verbinding van de server waarop de connector service wordt uitgevoerd. Andere implementaties verlaten open binnenkomende verbindingen via een load balancer. Met dit verificatie patroon kunt u meer soorten toepassingen aanbieden door on-premises toepassingen te publiceren via Extern bureaublad-services. Het vermindert ook de kwets baarheid van de implementatie door Azure AD-toepassingsproxy te gebruiken.

## <a name="use-when"></a>Wanneer gebruiken

U moet externe toegang bieden en uw Extern bureaublad-services-implementatie beveiligen met verificatie vooraf.

![architectuur diagram](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>Onderdelen van systeem

* **Gebruiker** : toegang tot RDS die wordt bediend door de toepassings proxy.

* **Webbrowser** : het onderdeel waarmee de gebruiker communiceert om toegang te krijgen tot de externe URL van de toepassing.

* **Azure AD** : verifieert de gebruiker. 

* **Application proxy-service** : fungeert als omgekeerde proxy voor het door sturen van de aanvraag van de gebruiker naar RDS. Toepassings proxy kan ook beleid voor voorwaardelijke toegang afdwingen. 

* **Extern bureaublad-services** : fungeert als een platform voor afzonderlijke gevirtualiseerde toepassingen, biedt veilige toegang tot mobiele en externe Bureau bladen en biedt eind gebruikers de mogelijkheid om hun toepassingen en Bureau bladen vanuit de Cloud uit te voeren. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Extern bureaublad-gateway services implementeren met Azure AD

* [Extern bureau blad publiceren met Azure AD-toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services) 

* [Een on-premises toepassing toevoegen voor externe toegang via toepassings proxy in azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
