---
title: Azure AD-toepassingsproxy en Qlik Sense | Microsoft Docs
description: Toepassingsproxy inschakelen in Azure portal en installeer de Connectors voor de omgekeerde proxy.
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
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037000"
---
# <a name="application-proxy-and-qlik-sense"></a>De toepassingsproxy en Qlik Sense 
Azure Active Directory-toepassingsproxy en Qlik Sense zijn een partnerschap aangegaan samen om te controleren of dat u zich eenvoudig kunt toepassingsproxy gebruiken voor externe toegang voor uw implementatie Qlik Sense.  

## <a name="prerequisites"></a>Vereisten 
De rest van dit scenario wordt ervan uitgegaan dat u het volgende gedaan:
 
- [Qlik Sense](https://community.qlik.com/docs/DOC-19822)is geconfigureerd. 
- [Een toepassings proxy connector geïnstalleerd](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Uw toepassingen publiceren in Azure 
Als u wilt publiceren QlikSense, moet u twee toepassingen publiceren in Azure.  

### <a name="application-1"></a>Toepassing #1: 
Volg deze stappen voor het publiceren van uw app. Zie [toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md)voor een meer gedetailleerd overzicht van de stappen 1-8. 


1. Meld u aan de Azure-portal als globale beheerder. 
2. Selecteer **Azure Active Directory** > **bedrijfs toepassingen**. 
3. Selecteer **toevoegen** boven aan de Blade. 
4. Selecteer **on-premises toepassing**. 
5. Vul de vereiste velden met informatie over de nieuwe app. Gebruik de volgende richtlijnen voor de instellingen: 
   - **Interne URL**: deze toepassing moet een interne URL hebben die de QLIKSENSE-URL zelf is. Bijvoorbeeld **https&#58;//demo.qlikemm.com:4244** 
   - **Methode voor verificatie vooraf**: Azure Active Directory (aanbevolen maar niet vereist) 
1. Selecteer **toevoegen** onder aan de Blade. Uw toepassing wordt toegevoegd en het menu Snel starten wordt geopend. 
2. Selecteer in het menu snel starten de optie **een gebruiker toewijzen voor testen**en voeg ten minste één gebruiker toe aan de toepassing. Zorg ervoor dat deze testaccount toegang heeft tot de on-premises toepassing. 
3. Selecteer **toewijzen** om de gebruikers toewijzing test op te slaan. 
4. (Optioneel) Selecteer op de blade app-beheer van eenmalige aanmelding. Kies **Kerberos-beperkte delegering** in de vervolg keuzelijst en vul de vereiste velden in op basis van uw Qlik-configuratie. Selecteer **Opslaan**. 

### <a name="application-2"></a>Toepassing #2: 
Volg dezelfde stappen als voor de toepassing #1, met de volgende uitzonderingen: 

**Stap #5**: de interne URL moet nu de QlikSense URL zijn met de verificatie poort die door de toepassing wordt gebruikt. De standaard waarde is **4244** voor HTTPS en **4248** voor http voor QlikSense releases van vóór april 2018. De standaard waarde voor QlikSense-releases na april 2018 is **443** voor HTTPS en **80** voor http.  Bijvoorbeeld: **https&#58;//demo.qlik.com:4244**</br></br>
**Stap #10:** EENMALIGe aanmelding instellen en de **eenmalige aanmelding uitgeschakeld** laten
 
 
## <a name="testing"></a>Testen 
Uw toepassing is nu gereed om te testen. Toegang tot de externe URL die u gebruikt voor het publiceren van QlikSense in #-toepassing 1 en meld u aan als een gebruiker die is toegewezen aan beide toepassingen.  

## <a name="additional-references"></a>Aanvullende naslaginformatie
Raadpleeg de volgende artikelen in de Qlik-Community voor meer informatie over het publiceren van Qlik Sense met toepassings proxy: 
- [Azure AD met geïntegreerde Windows-verificatie met behulp van een Kerberos-beperkte delegering met Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Qlik Sense-integratie met Azure AD-toepassingsproxy](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingen publiceren met toepassings proxy](application-proxy-add-on-premises-application.md)
- [Werken met Application proxy-connectors](application-proxy-connector-groups.md)

