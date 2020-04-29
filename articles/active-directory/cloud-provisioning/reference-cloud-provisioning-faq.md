---
title: Veelgestelde vragen over Azure AD Connect-cloudinrichting
description: In dit document worden veelgestelde vragen over het inrichten van Clouds beschreven.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77916571"
---
# <a name="azure-active-directory-connect-faq"></a>Veelgestelde vragen over Azure Active Directory Connect

Meer informatie over veelgestelde vragen over Azure Active Directory (Azure AD) Connect Cloud provisioning.

## <a name="general-installation"></a>Algemene installatie

**V: hoe vaak wordt de Cloud inrichting uitgevoerd?**

De inrichting van de Cloud is gepland om elke 2 minuten te worden uitgevoerd. Elke 2 minuten, alle gebruikers-, groep-en wachtwoord hash-wijzigingen worden ingericht in azure AD.

**V: bekijken van mislukte wachtwoord-hash-synchronisaties bij de eerste uitvoering. Waarom?**

Dit is normaal gedrag. De fouten worden veroorzaakt doordat het gebruikers object niet aanwezig is in azure AD. Zodra de gebruiker is ingericht voor Azure AD, moeten wacht woord-hashes in de volgende uitvoering worden ingericht. Wacht op een aantal uitvoeringen en controleer of de wachtwoord-hash-synchronisatie geen fouten meer bevat.

**V: wat gebeurt er als het Active Directory-exemplaar kenmerken heeft die niet worden ondersteund door Cloud provisoning (bijvoorbeeld Directory-extensies)?**

De inrichting van de Cloud wordt uitgevoerd en de ondersteunde kenmerken worden ingericht. De niet-ondersteunde kenmerken worden niet ingericht in azure AD. Controleer de Directory-extensies in Active Directory en zorg ervoor dat u dit kenmerk niet nodig hebt om naar Azure AD te stromen. Als er een of meer kenmerken zijn vereist, kunt u overwegen Azure AD Connect synchronisatie te gebruiken of de vereiste gegevens te verplaatsen naar een van de ondersteunde kenmerken (bijvoorbeeld extensie kenmerken 1-15).

**V: wat is het verschil tussen Azure AD Connect synchronisatie en Cloud inrichting?**

Met Azure AD Connect Sync wordt het inrichten uitgevoerd op de on-premises synchronisatie server. De configuratie wordt opgeslagen op de on-premises synchronisatie server. Bij Azure AD Connect Cloud inrichting wordt de inrichtings configuratie opgeslagen in de Cloud en uitgevoerd in de Cloud als onderdeel van de Azure AD-inrichtings service. 

**V: kan ik het inrichten van Clouds voor het synchroniseren vanuit meerdere Active Directory forests gebruiken?**

Ja. Cloud inrichting kan worden gebruikt om te synchroniseren vanuit meerdere Active Directory forests. In de omgeving met meerdere forests moeten alle verwijzingen (voor beeld, Manager) binnen het domein zijn.  

**V: hoe wordt de agent bijgewerkt?**

De agents worden automatisch bijgewerkt door micro soft. Voor het IT-team vermindert dit de belasting van het testen en valideren van nieuwe agent versies. 

**V: kan ik de automatische upgrade uitschakelen?**

Er wordt geen ondersteunde manier geboden om automatische upgrades uit te scha kelen.

**V: kan ik het bron anker voor Cloud inrichting wijzigen?**

Cloud inrichting maakt standaard gebruik van MS-DS-consistentie-GUID met een terugval op ObjectGUID als bron anker. Er is geen ondersteunde manier om het bron anker te wijzigen.

**V: Ik zie nieuwe service-principals met de AD-domein naam (en) bij het gebruik van Cloud inrichting. Wordt deze verwacht?**

Ja, het inrichten van Clouds maakt een service-principal voor de inrichtings configuratie met de domein naam als Service Principal Name. Breng geen wijzigingen aan in de configuratie van de Service-Principal.

**V: wat gebeurt er wanneer een gesynchroniseerde gebruiker het wacht woord moet wijzigen bij de volgende aanmelding?**

Als wacht woord-hash-synchronisatie is ingeschakeld in de Cloud inrichting en de gesynchroniseerde gebruiker moet het wacht woord wijzigen bij de volgende aanmelding bij een on-premises AD, wordt in de Cloud inrichting niet de wachtwoord-hash voor Azure AD ingesteld op gewijzigd. Zodra de gebruiker het wacht woord heeft gewijzigd, wordt de hash van het gebruikers wachtwoord van AD naar Azure AD ingericht.

**V: Cloud inrichting ondersteunt het terugschrijven van MS-DS-consistencyGUID voor elk object?**

Nee, Cloud inrichting biedt geen ondersteuning voor het terugschrijven van MS-DS-consistencyGUID voor een object (inclusief gebruikers objecten). 

**V: ik richt gebruikers in met het inrichten van de Cloud. Ik heb de configuratie verwijderd. Waarom zie ik nog steeds de oude gesynchroniseerde objecten in azure AD?** 

Wanneer u de configuratie verwijdert, worden de gesynchroniseerde objecten in azure AD niet opgeschoond met Cloud inrichting. Om ervoor te zorgen dat u niet over de oude objecten beschikt, wijzigt u het bereik van de configuratie in een lege groep of organisatie-eenheden. Wanneer de inrichting wordt uitgevoerd en de objecten worden opgeschoond, kunt u de configuratie uitschakelen en verwijderen. 

**V: wat betekent het dat Exchange hybride niet wordt ondersteund?**

Met de functie Hybride implementatie voor Exchange kunnen on-premises en in Office 365 meerdere Exchange-postbussen naast elkaar bestaan. Azure AD Connect synchroniseert een specifieke set kenmerken vanuit Azure AD naar uw on-premises directory.  De Cloud Provisioning agent synchroniseert deze kenmerken momenteel niet terug in uw on-premises Directory en wordt daarom niet ondersteund als vervanging voor Azure AD Connect.

**V: kan ik de inrichtings agent voor de Cloud installeren op Windows Server Core?**

Nee, het installeren van de agent op Server Core wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
