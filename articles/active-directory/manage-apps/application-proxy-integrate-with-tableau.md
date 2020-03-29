---
title: Azure Active Directory Application Proxy en Tableau | Microsoft Documenten
description: Meer informatie over het gebruik van Azure Active Directory (Azure AD) Application Proxy om externe toegang te bieden voor uw Tableau-implementatie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1aa99e7e71ad78a62c1a9da303b2ecc8347ebeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65783854"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy van Azure Active Directory-toepassing en Tableau 

Azure Active Directory Application Proxy en Tableau zijn een partnerschap aangegaan om ervoor te zorgen dat u eenvoudig Application Proxy gebruiken om externe toegang te bieden voor uw Tableau-implementatie. In dit artikel wordt uitgelegd hoe u dit scenario configureert.  

## <a name="prerequisites"></a>Vereisten 

Het scenario in dit artikel gaat ervan uit dat u:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) geconfigureerd. 

- Een [Application Proxy-connector](application-proxy-add-on-premises-application.md) geïnstalleerd. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Application Proxy inschakelen voor Tableau 

Application Proxy ondersteunt de OAuth 2.0 Grant Flow, die nodig is om Tableau goed te laten werken. Dit betekent dat er geen speciale stappen meer nodig zijn om deze toepassing in te schakelen, anders dan deze te configureren door de onderstaande publicatiestappen te volgen.


## <a name="publish-your-applications-in-azure"></a>Uw toepassingen publiceren in Azure 

Als u Tableau wilt publiceren, moet u een toepassing publiceren in de Azure Portal.

Voor:

- Gedetailleerde instructies voor stappen 1-8, zie [Toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md). 
- Zie de Tableau-documentatie voor informatie over het vinden van Tableau-waarden voor de velden App Proxy.  

**Uw app publiceren:** 


1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als toepassingsbeheerder. 

2. Selecteer **Azure Active Directory > Enterprise-toepassingen**. 

3. Selecteer **Toevoegen** boven aan het blad. 

4. Selecteer **On-premises toepassing**. 

5. Vul de vereiste velden in met informatie over uw nieuwe app. Gebruik de volgende richtlijnen voor de instellingen: 

    - **Interne URL**: Deze toepassing moet een interne URL hebben die de Tableau-URL zelf is. Bijvoorbeeld `https://adventure-works.tableau.com`. 

    - **Verificatiemethode**voor vooraf verificatie: Azure Active Directory (aanbevolen, maar niet vereist). 

6. Selecteer **Toevoegen** boven aan het blad. Uw toepassing wordt toegevoegd en het menu snel beginnen wordt geopend. 

7. Selecteer in het menu Snelstarten de optie **Een gebruiker toewijzen voor het testen**en voeg ten minste één gebruiker toe aan de toepassing. Zorg ervoor dat dit testaccount toegang heeft tot de on-premises toepassing. 

8. Selecteer **Toewijzen** om de testgebruikerstoewijzing op te slaan. 

9. (Optioneel) Selecteer op de pagina appbeheer de optie **Eén aanmelding**. Kies **Geïntegreerde Windows-verificatie** in het vervolgkeuzemenu en vul de vereiste velden in op basis van uw Tableau-configuratie. Selecteer **Opslaan**. 

 

## <a name="testing"></a>Testen 

Uw aanvraag is nu klaar om te testen. Toegang tot de externe URL die u hebt gebruikt om Tableau te publiceren en in te loggen als gebruiker die aan beide toepassingen is toegewezen.



## <a name="next-steps"></a>Volgende stappen

Zie Hoe u beveiligde externe [toegang tot on-premises toepassingen](application-proxy.md)bieden voor meer informatie over Azure AD-toepassingsproxy.

