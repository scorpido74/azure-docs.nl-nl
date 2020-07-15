---
title: Wat is synchronisatie van wachtwoord-hashes met Azure AD? | Microsoft Docs
description: Beschrijving van wachtwoord-hashsynchronisatie.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e40eecce3ff0ea5b252fd135ae553fa20e0a77b
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85386348"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Wat is synchronisatie van wachtwoord-hashes met Azure AD?
Wachtwoord-hashsynchronisatie is een van de aanmeldingsmethoden voor hybride identiteit. Azure AD Connect synchroniseert een hash, van de hash, van het wachtwoord van een gebruiker vanuit een on-premises Active Directory-exemplaar naar een Azure AD-exemplaar in de cloud.

Wachtwoord-hashsynchronisatie is een uitbreiding op de adreslijstsynchronisatiefunctie die is geïmplementeerd door Azure AD Connect-synchronisatie. U kunt deze functie gebruiken voor aanmelding bij Azure AD-services zoals Office 365. U meldt zich aan bij de service met hetzelfde wachtwoord als waarmee u zich aanmeldt bij uw on-premises Active Directory-exemplaar.

![Wat is Azure AD Connect?](./media/how-to-connect-password-hash-synchronization/arch1.png)

Wachtwoord-hashsynchronisatie helpt u om het aantal wachtwoorden te verminderen. Hiermee hoeven uw gebruikers slechts één wachtwoord te onderhouden. Met wachtwoord-hashsynchronisatie kunt u:

* De productiviteit van uw gebruikers verhogen.
* De kosten van uw helpdesk verlagen.  

Wachtwoord-hashsynchronisatie maakt ook [detectie van uitgelekte referenties](../identity-protection/concept-identity-protection-risks.md#user-risk) voor uw hybride account mogelijk. Microsoft werkt samen met wetshandhavers en onderzoekers die het 'dark web' bestuderen om openbaar beschikbare gebruikersnaam/wachtwoord-paren te vinden. Als een van deze paren overeenkomt met een van onze gebruikers, wordt het betreffende account verplaatst naar 'hoog risico'. 

>[!NOTE]
> Alleen nieuwe gelekte referenties die worden gevonden nadat u PHS inschakelt, worden verwerkt voor uw tenant. Er wordt geen verificatie uitgevoerd van eerder gevonden referentieparen.


Als u besluit [federatie met Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) te gebruiken, kunt u optioneel als back-up wachtwoordhashsynchronisatie instellen als aanmeldingsmethode.

Als u wachtwoord-hashsynchronisatie in uw omgeving wilt gebruiken, moet u:

* Azure AD Connect installeren.  
* Adreslijstsynchronisatie tussen uw on-premises Active Directory-exemplaar en uw Azure Active Directory-exemplaar configureren.
* Wachtwoord-hashsynchronisatie inschakelen.



Zie [Wat is hybride identiteit?](whatis-hybrid-identity.md) voor meer informatie.




## <a name="next-steps"></a>Volgende stappen

- [Wat is hybride identiteit?](whatis-hybrid-identity.md)
- [Wat zijn Azure AD Connect en Connect Health?](whatis-azure-ad-connect.md)
- [Wat is pass-through-verificatie (PTA)?](how-to-connect-pta.md)
- [Wat is federatie?](whatis-fed.md)
- [Wat is eenmalige aanmelding?](how-to-connect-sso.md)
- [Hoe synchronisatie van wachtwoord-hashes werkt](how-to-connect-password-hash-synchronization.md)
