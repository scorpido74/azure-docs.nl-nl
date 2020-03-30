---
title: Veelgestelde vragen over Azure AD Connect-cloudinrichting
description: In dit document worden veelgestelde vragen voor cloudprovisioning beschreven.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc1baa86bb81c8975587e84427a72ccc044805e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77916571"
---
# <a name="azure-active-directory-connect-faq"></a>Veelgestelde vragen over Azure Active Directory Connect

Lees meer over veelgestelde vragen voor Azure Active Directory (Azure AD) Connect cloud provisioning.

## <a name="general-installation"></a>Algemene installatie

**V: Hoe vaak wordt cloudprovisioning uitgevoerd?**

Cloud provisioning is gepland om elke 2 minuten uit te voeren. Elke 2 minuten worden alle wijzigingen van de gebruikers-, groeps- en wachtwoordhash in Azure AD aangebracht.

**V: Het zien van wachtwoordhashsynchronisatiefouten bij de eerste run. Waarom?**

Dit is normaal gedrag. De fouten zijn te wijten aan het gebruikersobject dat niet aanwezig is in Azure AD. Zodra de gebruiker is ingericht voor Azure AD, moeten wachtwoordhashes in de volgende uitvoering worden ingericht. Wacht op een paar runs en bevestig dat wachtwoordhashsynchronisatie de fouten niet meer heeft.

**V: Wat gebeurt er als de Active Directory-instantie kenmerken heeft die niet worden ondersteund door cloudprovisoning (bijvoorbeeld directory-extensies)?**

Cloudprovisioning wordt uitgevoerd en de ondersteunde kenmerken ingericht. De niet-ondersteunde kenmerken worden niet in- en opgenomen in Azure AD. Controleer de directory-extensies in Active Directory en zorg ervoor dat dit kenmerk niet naar Azure AD hoeft te worden doorgegeven. Als een of meer kenmerken vereist zijn, u azure AD Connect-synchronisatie gebruiken of de vereiste informatie verplaatsen naar een van de ondersteunde kenmerken (bijvoorbeeld extensiekenmerken 1-15).

**V: Wat is het verschil tussen Azure AD Connect-synchronisatie en cloudprovisioning?**

Met Azure AD Connect-synchronisatie wordt de inrichting uitgevoerd op de on-premises synchronisatieserver. De configuratie wordt opgeslagen op de on-premises synchronisatieserver. Met Azure AD Connect-cloudinrichting wordt de inrichtingsconfiguratie opgeslagen in de cloud en wordt deze in de cloud uitgevoerd als onderdeel van de Azure AD-inrichtingsservice. 

**V: Kan ik cloudprovisioning gebruiken om te synchroniseren vanuit meerdere Active Directory-forests?**

Ja. Cloudprovisioning kan worden gebruikt om te synchroniseren vanuit meerdere Active Directory-forests. In de omgeving met meerdere bossen moeten alle verwijzingen (bijvoorbeeld beheerder) zich binnen het domein bevinden.  

**V: Hoe wordt de agent bijgewerkt?**

De agents worden automatisch geüpgraded door Microsoft. Voor het IT-team vermindert dit de last van het moeten testen en valideren van nieuwe agentversies. 

**V: Kan ik de automatische upgrade uitschakelen?**

Er is geen ondersteunde manier om automatische upgrade uit te schakelen.

**V: Kan ik het bronanker voor cloudprovisioning wijzigen?**

Standaard gebruikt cloudprovisioning ms-ds-consistency-GUID met een terugval naar ObjectGUID als bronanker. Er is geen ondersteunde manier om het bronanker te wijzigen.

**V: Ik zie nieuwe serviceprincipals met de AD-domeinnaam(en) bij het gebruik van cloudprovisioning. Is het verwacht?**

Ja, cloudprovisioning maakt een serviceprincipal voor de inrichtingsconfiguratie met de domeinnaam als hoofdnaam van de service. Breng geen wijzigingen aan in de hoofdconfiguratie van de service.

**V: Wat gebeurt er als een gesynchroniseerde gebruiker bij de volgende aanmelding het wachtwoord moet wijzigen?**

Als wachtwoordhashsynchronisatie is ingeschakeld in cloudprovisioning en de gesynchroniseerde gebruiker is verplicht om wachtwoord te wijzigen bij de volgende aanmelding in on-premises AD, biedt cloudprovisioning geen ingerichte wachtwoordhash in Azure AD. Zodra de gebruiker het wachtwoord wijzigt, wordt de hash van het gebruikerswachtwoord ingericht van AD naar Azure AD.

**V: Ondersteunt cloudprovisioning writeback van ms-ds-consistencyGUID voor elk object?**

Nee, cloudprovisioning biedt geen ondersteuning voor writeback van ms-ds-consistencyGUID voor elk object (inclusief gebruikersobjecten). 

**V: Ik voorstel gebruikers met behulp van cloudprovisioning. Ik heb de configuratie verwijderd. Waarom zie ik nog steeds de oude gesynchroniseerde objecten in Azure AD?** 

Wanneer u de configuratie verwijdert, worden de gesynchroniseerde objecten in Azure AD niet opgeschoond in de cloudprovisioning. Als u ervoor wilt zorgen dat u de oude objecten niet hebt, wijzigt u het bereik van de configuratie in een lege groep of organisatie-eenheden. Zodra de inrichting wordt uitgevoerd en de objecten opschonen, schakelt u de configuratie uit en verwijdert u deze. 

**V: Wat betekent het dat Exchange hybrid niet wordt ondersteund?**

Met de functie Hybride implementatie voor Exchange kunnen on-premises en in Office 365 meerdere Exchange-postbussen naast elkaar bestaan. Azure AD Connect synchroniseert een specifieke set kenmerken vanuit Azure AD naar uw on-premises directory.  De cloudprovisioning-agent synchroniseert deze kenmerken momenteel niet terug in uw on-premises directory en wordt dus niet ondersteund als vervanging voor Azure AD Connect.

**V: Kan ik de cloudinrichtingsagent installeren op Windows Server Core?**

Nee, het installeren van de agent op de serverkern wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
