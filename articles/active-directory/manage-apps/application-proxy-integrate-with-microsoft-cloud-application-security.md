---
title: On-premises apps integreren met Cloud App Security - Azure AD
description: Een on-premises toepassing configureren in Azure Active Directory om te werken met Microsoft Cloud App Security (MCAS). Gebruik het MCAS Conditional Access App Control om sessies in realtime te controleren en te beheren op basis van beleid voor voorwaardelijke toegang. U deze beleidsregels toepassen op on-premises toepassingen die Application Proxy gebruiken in Azure Active Directory (Azure AD).
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb97f9dd87277215a5d4708d3a6f49564c490204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275500"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Realtime bewaking van toepassingstoegang configureren met Microsoft Cloud App Security en Azure Active Directory
Configureer een on-premises toepassing in Azure Active Directory (Azure AD) om Microsoft Cloud App Security (MCAS) te gebruiken voor realtime bewaking. MCAS gebruikt Conditional Access App Control om sessies in realtime te controleren en te beheren op basis van beleid voor voorwaardelijke toegang. U deze beleidsregels toepassen op on-premises toepassingen die Application Proxy gebruiken in Azure Active Directory (Azure AD).

Hier volgen enkele voorbeelden van de typen beleidsregels die u maken met MCAS:

- Het downloaden van gevoelige documenten op onbeheerde apparaten blokkeren of beveiligen.
- Controleer wanneer gebruikers met een hoog risico zich aanmelden bij toepassingen en log vervolgens hun acties in vanuit de sessie. Met deze informatie u gebruikersgedrag analyseren om te bepalen hoe u sessiebeleid toepast.
- Gebruik clientcertificaten of apparaatnaleving om de toegang tot specifieke toepassingen vanaf niet-beheerde apparaten te blokkeren.
- Gebruikerssessies van niet-bedrijfsnetwerken beperken. U beperkte toegang geven aan gebruikers die toegang hebben tot een toepassing van buiten uw bedrijfsnetwerk. Deze beperkte toegang kan de gebruiker bijvoorbeeld blokkeren om gevoelige documenten te downloaden.

Zie [Apps beveiligen met voorwaardelijke toegangsapp besturingselement voor Microsoft Cloud-apps voor](/cloud-app-security/proxy-intro-aad)meer informatie .

## <a name="requirements"></a>Vereisten

Licentie:

- EMS E5-licentie, of 
- Azure Active Directory Premium P1 en MCAS Standalone.

On-premises toepassing:

- De on-premises toepassing moet Kerberos Constrained Delegation (KCD) gebruiken

Toepassingsproxy configureren:

- Configureer Azure AD om Toepassingsproxy te gebruiken, inclusief het voorbereiden van uw omgeving en het installeren van de toepassingsproxyconnector. Zie [Een on-premises toepassingen voor externe toegang](application-proxy-add-on-premises-application.md)toevoegen via Application Proxy in Azure AD voor een zelfstudie. 

## <a name="add-on-premises-application-to-azure-ad"></a>On-premises toepassing toevoegen aan Azure AD

Voeg een on-premises toepassing toe aan Azure AD. Zie [Een on-premises app toevoegen aan Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)voor een snelle start. Wanneer u de toepassing toevoegt, moet u de volgende twee instellingen instellen in het **on-premises toepassingsblad toevoegen:**

- **Pre-verificatie:** Voer **Azure Active Directory in**.
- **URL's vertalen in toepassingstekst:** **Kies Ja**.

Deze twee instellingen zijn vereist om de toepassing met MCAS te laten werken.

## <a name="test-the-on-premises-application"></a>Test de on-premises applicatie

Nadat u uw toepassing aan Azure AD hebt toegevoegd, gebruikt u de stappen in [De toepassing testen](application-proxy-add-on-premises-application.md#test-the-application) om een gebruiker toe te voegen voor het testen en de aanmelding te testen. 

## <a name="deploy-conditional-access-app-control"></a>Beheer van voorwaardelijke toegangs-apps implementeren

Als u uw toepassing wilt configureren met het beheer van de toepassingsbesturingselement voor voorwaardelijke toegang, volgt u de instructies in [Het beheer van voorwaardelijke toegangstoepassingen voor Azure AD-apps.](/cloud-app-security/proxy-deployment-aad)


## <a name="test-conditional-access-app-control"></a>Besturingselement voorwaardelijke toegang-app testen

Als u de implementatie van Azure AD-toepassingen wilt testen met beheer van voorwaardelijke toegangstoepassingen, volgt u de instructies in [De implementatie voor Azure AD-apps testen.](/cloud-app-security/proxy-deployment-aad)





