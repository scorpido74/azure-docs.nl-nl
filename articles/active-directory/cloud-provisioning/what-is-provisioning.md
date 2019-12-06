---
title: Wat is identiteits inrichting met Azure AD? | Microsoft Docs
description: Hierin wordt een overzicht gegeven van het inrichten van identiteiten.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 166fb9320672e63b8c53717133dc61aa93f57a62
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868640"
---
# <a name="what-is-identity-provisioning"></a>Wat is identiteits inrichting?

Vandaag de dag maken organisaties en bedrijven steeds meer gebruik van een mix van on-premises toepassingen en cloudtoepassingen.  Gebruikers moeten on-premises en in de Cloud toegang hebben tot toepassingen. Er moet sprake zijn van een enkele identiteit in deze verschillende toepassingen (on-premises en in de Cloud).

Inrichting is het proces van het maken van een object op basis van bepaalde voor waarden, waarbij het object tot de datum en het verwijderen van het object wordt verwijderd wanneer niet langer aan de voor waarden wordt voldaan. Wanneer een nieuwe gebruiker bijvoorbeeld lid wordt van uw organisatie, wordt die gebruiker in het HR-systeem ingevoerd.  Op dat moment kan het inrichten van een bijbehorend gebruikers account in de Cloud, in Active Directory en verschillende toepassingen waartoe de gebruiker toegang nodig heeft, maken.  Op deze manier kan de gebruiker aan de slag gaan met het werken en toegang hebben tot de toepassingen en systemen die ze nodig hebben op dag één. 

![Cloud inrichting](media/what-is-provisioning/cloud1.png)

Met betrekking tot Azure Active Directory kan inrichten worden uitgesplitst in de volgende belang rijke scenario's.  

- **[HR-inrichting inrichten](#hr-driven-provisioning)**  
- **[App-inrichting](#app-provisioning)**  
- **[Directory-inrichting](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>HR-inrichting inrichten

![Cloud inrichting](media/what-is-provisioning/cloud2.png)

Het inrichten van een uur naar de Cloud bestaat uit het maken van objecten (gebruikers, rollen, groepen enzovoort) op basis van de informatie in uw HR-systeem.  

Het meest voorkomende scenario zou zijn, wanneer een nieuwe werk nemer aan uw bedrijf deelneemt, ze worden ingevoerd in het HR-systeem.  Als dit het geval is, worden ze ingericht in de Cloud.  In dit geval Azure AD.  Het inrichten van HR kan betrekking hebben op de volgende scenario's. 

- **Nieuwe werk nemers inhuren** : wanneer een nieuwe werk nemer wordt toegevoegd aan de Cloud, wordt automatisch een gebruikers account gemaakt in Active Directory, Azure Active Directory en optioneel Office 365 en andere SaaS-toepassingen die worden ondersteund door Azure AD, met een terugschrijf bewerking van het e-mail adres naar de Cloud.
- **Updates van werknemers kenmerken en-profielen** : wanneer een werknemers record wordt bijgewerkt in de Cloud (zoals hun naam, titel of Manager), wordt het gebruikers account automatisch bijgewerkt in Active Directory, Azure Active Directory en optioneel Office 365 en andere SaaS-toepassingen die worden ondersteund door Azure AD.
- **Beëindiging van werk nemers** : wanneer een werk nemer in de Cloud wordt beëindigd, wordt het gebruikers account automatisch uitgeschakeld in Active Directory, Azure Active Directory en optioneel Office 365 en andere SaaS-toepassingen die worden ondersteund door Azure AD.
- Opnieuw **inhuren van werk nemers** : wanneer een werk nemer in de Cloud HR opnieuw wordt ingehuurd, kan het oude account automatisch opnieuw worden geactiveerd of worden ingericht (afhankelijk van uw voor keur) tot Active Directory, Azure Active Directory en optioneel Office 365 en andere SaaS-toepassingen die worden ondersteund door Azure AD.


## <a name="app-provisioning"></a>App-inrichting

![Cloud inrichting](media/what-is-provisioning/cloud3.png)

Het inrichten van apps omvat het inrichten van gebruikers en rollen in de toepassingen waartoe de gebruiker toegang nodig heeft.  

Het meest voorkomende scenario zou zijn wanneer een gebruiker in azure AD is ingericht in O365 of Sales Force.

## <a name="directory-provisioning"></a>Directory-inrichting

![Cloud inrichting](media/what-is-provisioning/cloud4.png)

On-premises inrichting omvat het inrichten van on-premises bronnen (zoals Active Directory) naar Azure AD.  

Het meest voorkomende scenario zou zijn wanneer een gebruiker in Active Directory (AD) is ingericht in azure AD.

Dit is bereikt door Azure AD Connect synchronisatie, Azure AD Connect Cloud inrichting en Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Volgende stappen 

- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)
- [Cloud inrichting installeren](how-to-install.md)