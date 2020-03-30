---
title: MFA vereisen van niet-vertrouwde netwerken - Azure Active Directory
description: Meer informatie over het configureren van een beleid voor voorwaardelijke toegang in Azure Active Directory (Azure AD) voor toegangspogingen van niet-vertrouwde netwerken.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74379993"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Hoe: MFA vereisen voor toegang van niet-vertrouwde netwerken met voorwaardelijke toegang   

Azure Active Directory (Azure AD) maakt single sign-on op apparaten, apps en services overal mogelijk. Uw gebruikers hebben toegang tot uw cloud-apps, niet alleen vanuit het netwerk van uw organisatie, maar ook vanaf elke niet-vertrouwde internetlocatie. Een gangbare aanbevolen toepassing voor toegang via niet-vertrouwde netwerken is het vereisen van multi-factor authenticatie (MFA).

In dit artikel vindt u de informatie die u nodig hebt om een beleid voor voorwaardelijke toegang te configureren waarvoor MFA vereist is voor toegang van niet-vertrouwde netwerken. 

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met: 

- De [basisconcepten](overview.md) van Azure AD Conditional Access 
- De [aanbevolen procedures](best-practices.md) voor het configureren van beleid voor voorwaardelijke toegang in de Azure-portal

## <a name="scenario-description"></a>Scenariobeschrijving

Om de balans tussen beveiliging en productiviteit onder de knie te krijgen, kan het voldoende zijn dat u alleen een wachtwoord nodig hebt voor aanmeldingen uit het netwerk van uw organisatie. Voor toegang vanaf een niet-vertrouwde netwerklocatie is er echter een verhoogd risico dat aanmeldingen niet worden uitgevoerd door legitieme gebruikers. Om deze zorg aan te pakken, u de toegang van niet-vertrouwde netwerken blokkeren. U ook multi-factor authenticatie (MFA) vereisen om extra zekerheid terug te krijgen dat een poging is gedaan door de rechtmatige eigenaar van het account. 

Met Voorwaardelijke toegang tot Azure AD u deze vereiste beantwoorden met één enkel beleid dat toegang verleent: 

- Naar geselecteerde cloud-apps
- Voor geselecteerde gebruikers en groepen  
- Multifactorauthenticatie vereisen 
- Wanneer de toegang afkomstig is van: 
   - Een locatie die niet wordt vertrouwd

## <a name="implementation"></a>Implementatie

De uitdaging van dit scenario is om *toegang vanaf een niet-vertrouwde netwerklocatie* te vertalen naar een voorwaarde voor voorwaardelijke toegang. In een beleid voor voorwaardelijke toegang u de [locatievoorwaarde](location-condition.md) configureren om scenario's aan te pakken die gerelateerd zijn aan netwerklocaties. Met de locatievoorwaarde u benoemde locaties selecteren, logische groeperingen van IP-adresbereiken, landen en regio's.  

Uw organisatie bezit doorgaans een of meer adresbereiken, bijvoorbeeld 199.30.16.0 - 199.30.16.15.
U een benoemde locatie configureren op:

- Dit bereik opgeven (199.30.16.0/28) 
- Een beschrijvende naam toewijzen, zoals **Bedrijfsnetwerk** 

In plaats van te proberen te definiëren wat alle locaties zijn die niet worden vertrouwd, u:

- Elke locatie opnemen 

   ![Voorwaardelijke toegang](./media/untrusted-networks/02.png)

- Alle vertrouwde locaties uitsluiten 

   ![Voorwaardelijke toegang](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Implementatie van beleid

Met de in dit artikel beschreven aanpak u nu een beleid voor voorwaardelijke toegang configureren voor niet-vertrouwde locaties. Om ervoor te zorgen dat uw beleid werkt zoals verwacht, is de aanbevolen best practice om het te testen voordat u het uitrolt in productie. Gebruik in het ideale idealiter een testtenant om te controleren of uw nieuwe beleid werkt zoals bedoeld. Zie [Een nieuw beleid implementeren](best-practices.md#how-should-you-deploy-a-new-policy)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Zie Wat is voorwaardelijke toegang in [Azure Active Directory als](../active-directory-conditional-access-azure-portal.md) u meer wilt weten over voorwaardelijke toegang?
