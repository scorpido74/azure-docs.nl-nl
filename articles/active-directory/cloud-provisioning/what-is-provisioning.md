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
ms.openlocfilehash: 28513c57101af67695d10056b3dc8e6537dcddb2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712554"
---
# <a name="what-is-identity-provisioning"></a>Wat is identiteitsinrichting?

Vandaag de dag maken organisaties en bedrijven steeds meer gebruik van een mix van on-premises toepassingen en cloudtoepassingen.  Gebruikers moeten on-premises en in de Cloud toegang hebben tot toepassingen. Er moet sprake zijn van een enkele identiteit in deze verschillende toepassingen (on-premises en in de Cloud).

Inrichting is het proces van het maken van een object op basis van bepaalde voor waarden, waarbij het object up-to-date wordt gehouden en het object wordt verwijderd wanneer niet langer aan de voor waarden wordt voldaan. Wanneer een nieuwe gebruiker bijvoorbeeld lid wordt van uw organisatie, wordt die gebruiker in het HR-systeem ingevoerd.  Op dat moment kan het inrichten van een bijbehorend gebruikers account in de Cloud, in Active Directory en verschillende toepassingen waartoe de gebruiker toegang nodig heeft, maken.  Op deze manier kan de gebruiker aan de slag gaan met het werken en toegang hebben tot de toepassingen en systemen die ze nodig hebben op dag één. 

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

In Azure Active Directory (Azure AD) verwijst de inrichting van de term **[app](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** naar het automatisch maken van gebruikers-id's en-rollen in de Cloud toepassingen waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikers identiteiten, omvat automatische inrichting het onderhoud en de verwijdering van gebruikers identiteiten als status of rollen worden gewijzigd. Veelvoorkomende scenario's zijn onder andere het inrichten van een Azure AD-gebruiker in toepassingen zoals [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Sales Force](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)en meer.

## <a name="directory-provisioning"></a>Directory-inrichting

![Cloud inrichting](media/what-is-provisioning/cloud4.png)

On-premises inrichting omvat het inrichten van on-premises bronnen (zoals Active Directory) naar Azure AD.  

Het meest voorkomende scenario zou zijn wanneer een gebruiker in Active Directory (AD) is ingericht in azure AD.

Dit is bereikt door Azure AD Connect synchronisatie, Azure AD Connect Cloud inrichting en Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Volgende stappen 

- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)
- [Cloud inrichting installeren](how-to-install.md)
