---
title: 'Azure AD Connect: apparaatopties | Microsoft Documenten'
description: In dit document worden de apparaatopties beschreven die beschikbaar zijn in Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ddcdb67ef079cfa23902a1dcb03b0ec61077fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109530"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: apparaatopties

In de volgende documentatie vindt u informatie over de verschillende apparaatopties die beschikbaar zijn in Azure AD Connect. U Azure AD Connect gebruiken om de volgende twee bewerkingen te configureren: 
* **Hybride Azure AD join:** Als uw omgeving een on-premises AD-voetafdruk heeft en u de voordelen van Azure AD wilt, u hybride Azure AD-apparaten implementeren. Deze apparaten worden samengevoegd met zowel uw on-premises Active Directory als uw Azure Active Directory.
* **Apparaatterugschrijven**: Apparaatterugschrijven wordt gebruikt om voorwaardelijke toegang in te schakelen op basis van apparaten met beveiligde APPARATEN (2012 R2 of hoger)

## <a name="configure-device-options-in-azure-ad-connect"></a>Apparaatopties configureren in Azure AD Connect

1.  Azure AD Connect uitvoeren. Selecteer **apparaatopties configureren**op de pagina **Extra taken** .  Klik op **Volgende**.
    ![Apparaatopties configureren](./media/how-to-connect-device-options/deviceoptions.png) 

    Op de pagina **Overzicht** worden de details weergegeven.
    ![Overzicht](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > De nieuwe apparaatopties configureren zijn alleen beschikbaar in versie 1.1.819.0 en nieuwer.

2.  Nadat u de referenties voor Azure AD hebt verstrekt, u de bewerking kiezen die moet worden uitgevoerd op de pagina Apparaatopties.
    ![Apparaatbewerkingen](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Volgende stappen

* [Hybride Azure AD-lid configureren](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Terugschrijven van apparaten configureren / uitschakelen](how-to-connect-device-writeback.md)

