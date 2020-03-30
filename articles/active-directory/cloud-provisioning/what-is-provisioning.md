---
title: Wat is identiteitsinrichting met Azure AD? | Microsoft Docs
description: Beschrijft overzicht van identiteitsinrichting.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76712554"
---
# <a name="what-is-identity-provisioning"></a>Wat is identiteitsinrichting?

Vandaag de dag maken organisaties en bedrijven steeds meer gebruik van een mix van on-premises toepassingen en cloudtoepassingen.  Gebruikers hebben toegang nodig tot applicaties, zowel on-premises als in de cloud. Er is behoefte aan één identiteit voor deze verschillende toepassingen (zowel on-premises als cloud).

Provisioning is het proces van het maken van een object op basis van bepaalde voorwaarden, het up-to-date houden van het object en het verwijderen van het object wanneer niet langer aan de voorwaarden wordt voldaan. Wanneer een nieuwe gebruiker bijvoorbeeld lid wordt van uw organisatie, wordt die gebruiker ingevoerd in het HR-systeem.  Op dat moment kan provisioning een bijbehorend gebruikersaccount maken in de cloud, in Active Directory en verschillende toepassingen waartoe de gebruiker toegang nodig heeft.  Hierdoor kan de gebruiker aan de slag en heeft hij toegang tot de applicaties en systemen die hij nodig heeft op dag één. 

![cloudprovisioning](media/what-is-provisioning/cloud1.png)

Met betrekking tot Azure Active Directory kan inprovisioning worden opgesplitst in de volgende belangrijke scenario's.  

- **[HR-gestuurde inrichting](#hr-driven-provisioning)**  
- **[App-inrichting](#app-provisioning)**  
- **[Directory provisioning](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>HR-gestuurde inrichting

![cloudprovisioning](media/what-is-provisioning/cloud2.png)

Het inrichten van HR naar de cloud omvat het maken van objecten (gebruikers, rollen, groepen, enz.) op basis van de informatie die zich in uw HR-systeem bevindt.  

Het meest voorkomende scenario zou zijn dat wanneer een nieuwe werknemer zich bij uw bedrijf aansluit, ze worden ingevoerd in het HR-systeem.  Zodra dat gebeurt, worden ze ingericht in de cloud.  In dit geval Azure AD.  De inrichting vanuit HR kan de volgende scenario's dekken. 

- **Nieuwe werknemers inhuren** - Wanneer een nieuwe werknemer wordt toegevoegd aan cloud-HR, wordt er automatisch een gebruikersaccount gemaakt in Active Directory, Azure Active Directory en optioneel Office 365 en andere SaaS-toepassingen die worden ondersteund door Azure AD, met terugschrijfgegevens van het e-mailadres naar Cloud HR.
- **Werknemerskenmerk en profielupdates** : Wanneer een werknemersrecord wordt bijgewerkt in cloud-HR (zoals hun naam, titel of manager), wordt hun gebruikersaccount automatisch bijgewerkt in Active Directory, Azure Active Directory en optioneel Office 365 en andere SaaS-toepassingen die worden ondersteund door Azure AD.
- **Werknemersbeëindigingen** - Wanneer een werknemer wordt beëindigd in cloud-HR, wordt zijn gebruikersaccount automatisch uitgeschakeld in Active Directory, Azure Active Directory en optioneel Office 365 en andere SaaS-toepassingen die worden ondersteund door Azure AD.
- **Werknemer rehires** - Wanneer een werknemer opnieuw wordt aangenomen in cloud-HR, kan hun oude account automatisch worden gereactiveerd of opnieuw ingericht (afhankelijk van uw voorkeur) naar Active Directory, Azure Active Directory en optioneel Office 365 en andere SaaS-toepassingen die worden ondersteund door Azure AD.


## <a name="app-provisioning"></a>App-inrichting

![cloudprovisioning](media/what-is-provisioning/cloud3.png)

In Azure Active Directory (Azure AD) verwijst de term **[app-inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** naar het automatisch maken van gebruikersidentiteiten en rollen in de cloudtoepassingen waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikersidentiteiten, omvat automatische inrichting het onderhoud en verwijderen van gebruikersidentiteiten als status of rollen veranderen. Veelvoorkomende scenario's zijn het inrichten van een Azure AD-gebruiker in toepassingen zoals [Dropbox,](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial) [Salesforce,](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial) [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)en meer.

## <a name="directory-provisioning"></a>Directory provisioning

![cloudprovisioning](media/what-is-provisioning/cloud4.png)

On-premises provisioning omvat het inrichten van on-premises bronnen (zoals Active Directory) naar Azure AD.  

Het meest voorkomende scenario zou zijn wanneer een gebruiker in Active Directory (AD) is ingericht in Azure AD.

Dit is bereikt door Azure AD Connect sync, Azure AD Connect cloud provisioning en Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Volgende stappen 

- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
- [Cloudprovisioning installeren](how-to-install.md)
