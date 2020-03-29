---
title: Azure AD-appproxy en Qlik-inzicht| Microsoft Documenten
description: Schakel Toepassingsproxy in de Azure-portal in en installeer de connectors voor de omgekeerde proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edf63da0fd09f829f936b54eb088c34dfe029a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037000"
---
# <a name="application-proxy-and-qlik-sense"></a>Application Proxy en Qlik Sense 
Azure Active Directory Application Proxy en Qlik Sense werken samen om ervoor te zorgen dat u eenvoudig Application Proxy gebruiken om externe toegang te bieden voor uw Qlik Sense-implementatie.  

## <a name="prerequisites"></a>Vereisten 
De rest van dit scenario gaat ervan uit dat u het volgende hebt gedaan:
 
- Geconfigureerde [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Een toepassingsproxyconnector geïnstalleerd](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Uw toepassingen publiceren in Azure 
Als u QlikSense wilt publiceren, moet u twee toepassingen publiceren in Azure.  

### <a name="application-1"></a>Aanvraag #1: 
Volg deze stappen om uw app te publiceren. Zie [Toepassingen publiceren met Azure AD Application Proxy](application-proxy-add-on-premises-application.md)voor een meer gedetailleerde walkthrough van stappen 1-8. 


1. Meld u aan bij de Azure Portal als globale beheerder. 
2. Selecteer **Azure Active Directory** > **Enterprise-toepassingen**. 
3. Selecteer **Toevoegen** boven aan het blad. 
4. Selecteer **On-premises toepassing**. 
5. Vul de vereiste velden in met informatie over uw nieuwe app. Gebruik de volgende richtlijnen voor de instellingen: 
   - **Interne URL**: Deze toepassing moet een interne URL hebben die de QlikSense-URL zelf is. Https **&#58;-demo.qlikemm.com:4244** 
   - **Verificatiemethode:** Azure Active Directory (aanbevolen, maar niet vereist) 
1. Selecteer **Toevoegen** aan de onderkant van het blad. Uw toepassing wordt toegevoegd en het menu snel beginnen wordt geopend. 
2. Selecteer in het menu Snelstarten de optie **Een gebruiker toewijzen voor het testen**en voeg ten minste één gebruiker toe aan de toepassing. Zorg ervoor dat dit testaccount toegang heeft tot de on-premises toepassing. 
3. Selecteer **Toewijzen** om de testgebruikerstoewijzing op te slaan. 
4. (Optioneel) Selecteer in het beheerblad van de app de optie Eén aanmelding. Kies **Kerberos Constrained Delegation** in het vervolgkeuzemenu en vul de vereiste velden in op basis van uw Qlik-configuratie. Selecteer **Opslaan**. 

### <a name="application-2"></a>Toepassing #2: 
Volg dezelfde stappen als voor application #1, met de volgende uitzonderingen: 

**Stap #5**: De interne URL moet nu de QlikSense-URL zijn met de verificatiepoort die door de toepassing wordt gebruikt. De standaardinstelling is **4244** voor HTTPS en **4248** voor HTTP voor QlikSense-releases vóór april 2018. De standaardinstelling voor QlikSense-releases na april 2018 is **443** voor HTTPS en **80** voor HTTP.  Ex: **https&#58;-demo.qlik.com:4244**</br></br>
**Stap #10:** Stel SSO niet in en laat de **single sign-on uitgeschakeld**
 
 
## <a name="testing"></a>Testen 
Uw aanvraag is nu klaar om te testen. Toegang tot de externe URL die u hebt gebruikt om QlikSense te publiceren in toepassing #1 en aan te loggen als gebruiker die aan beide toepassingen is toegewezen.  

## <a name="additional-references"></a>Aanvullende naslaginformatie
Voor meer informatie over het publiceren van Qlik Sense met Application Proxy, verwijzen naar de volgende artikelen van de Qlik Community: 
- [Azure AD met geïntegreerde Windows-verificatie met behulp van een beperkte Kerberos-delegatie met Qlik-inzicht](https://community.qlik.com/docs/DOC-20183)
- [Qlik Sense-integratie met Azure AD-toepassingsproxy](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingen publiceren met toepassingsproxy (Engelstalig artikel)](application-proxy-add-on-premises-application.md)
- [Werken met toepassingsproxyconnectors](application-proxy-connector-groups.md)

