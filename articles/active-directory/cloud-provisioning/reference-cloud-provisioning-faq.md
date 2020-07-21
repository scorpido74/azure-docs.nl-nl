---
title: Veelgestelde vragen over Azure AD Connect-cloudinrichting
description: In dit document worden veelgestelde vragen over het inrichten van Clouds beschreven.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: efcf2df4e472d022fcdec0c9b7c69c73192c503f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518467"
---
# <a name="azure-active-directory-connect-cloud-provisioning-faq"></a>Veelgestelde vragen over het inrichten van Clouds Azure Active Directory Connect

Meer informatie over veelgestelde vragen over Azure Active Directory (Azure AD) Connect Cloud provisioning.

## <a name="general-installation"></a>Algemene installatie

**V: hoe vaak wordt de Cloud inrichting uitgevoerd?**

De inrichting van de Cloud is gepland om elke 2 minuten te worden uitgevoerd. Elke 2 minuten, alle gebruikers-, groep-en wachtwoord hash-wijzigingen worden ingericht in azure AD.

**V: bekijken van mislukte wachtwoord-hash-synchronisaties bij de eerste uitvoering. Waarom?**

Dit is normaal. De fouten worden veroorzaakt doordat het gebruikers object niet aanwezig is in azure AD. Zodra de gebruiker is ingericht voor Azure AD, moeten wacht woord-hashes in de volgende uitvoering worden ingericht. Wacht op een aantal uitvoeringen en controleer of de wachtwoord-hash-synchronisatie geen fouten meer bevat.

**V: wat gebeurt er als het Active Directory-exemplaar kenmerken heeft die niet worden ondersteund door Cloud inrichting (bijvoorbeeld Directory-extensies)?**

De inrichting van de Cloud wordt uitgevoerd en de ondersteunde kenmerken worden ingericht. De niet-ondersteunde kenmerken worden niet ingericht in azure AD. Controleer de Directory-extensies in Active Directory en zorg ervoor dat u deze kenmerken niet nodig hebt om naar Azure AD te stromen. Als er een of meer kenmerken zijn vereist, kunt u overwegen Azure AD Connect synchronisatie te gebruiken of de vereiste gegevens te verplaatsen naar een van de ondersteunde kenmerken (bijvoorbeeld extensie kenmerken 1-15).

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

Als wacht woord-hash-synchronisatie is ingeschakeld in Cloud inrichting en de gesynchroniseerde gebruiker moet het wacht woord wijzigen bij de volgende aanmelding bij een on-premises AD, wordt de wachtwoord-hash van ' to-changed ' in azure AD niet door de Cloud inrichting ingericht. Zodra de gebruiker het wacht woord heeft gewijzigd, wordt de hash van het gebruikers wachtwoord van AD naar Azure AD ingericht.

**V: Cloud inrichting ondersteunt het terugschrijven van MS-DS-consistencyGUID voor elk object?**

Nee, Cloud inrichting biedt geen ondersteuning voor het terugschrijven van MS-DS-consistencyGUID voor een object (inclusief gebruikers objecten). 

**V: ik richt gebruikers in met het inrichten van de Cloud. Ik heb de configuratie verwijderd. Waarom zie ik nog steeds de oude gesynchroniseerde objecten in azure AD?** 

Wanneer u de configuratie verwijdert, worden de gesynchroniseerde objecten in azure AD niet automatisch door Cloud inrichting verwijderd. Om ervoor te zorgen dat u niet over de oude objecten beschikt, wijzigt u het bereik van de configuratie in een lege groep of organisatie-eenheden. Wanneer de inrichting wordt uitgevoerd en de objecten worden opgeschoond, kunt u de configuratie uitschakelen en verwijderen. 

**V: wat betekent het dat Exchange hybride niet wordt ondersteund?**

Met de functie Hybride implementatie voor Exchange kunnen on-premises en in Office 365 meerdere Exchange-postbussen naast elkaar bestaan. Azure AD Connect synchroniseert een specifieke set kenmerken vanuit Azure AD naar uw on-premises directory.  De Cloud Provisioning agent synchroniseert deze kenmerken momenteel niet terug in uw on-premises Directory en wordt daarom niet ondersteund als vervanging voor Azure AD Connect.

**V: kan ik de inrichtings agent voor de Cloud installeren op Windows Server Core?**

Nee, het installeren van de agent op Server Core wordt niet ondersteund.

**V: kan ik een staging-server met de Cloud inrichtings agent gebruiken?**

Nee, staging-servers worden niet ondersteund.

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
