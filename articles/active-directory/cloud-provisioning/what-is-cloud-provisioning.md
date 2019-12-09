---
title: Wat is Azure AD Connect Cloud inrichting. | Microsoft Docs
description: Hierin wordt Azure AD Connect Cloud inrichting beschreven.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333a3b93d872c1a99d1ec18d4941adc8986d4a03
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74914558"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Wat is Azure AD Connect-cloudinrichting?
Azure AD Connect Cloud inrichting is een nieuwe micro soft-agent die is ontworpen om tegemoet te komen aan uw hybride identiteits doelstellingen voor synchronisatie van gebruikers, groepen en contact personen naar Azure AD.  Deze kan naast Azure AD Connect Sync worden gebruikt en biedt de volgende voor delen:
    
- Ondersteuning voor het synchroniseren naar een Azure AD-Tenant vanuit een niet-verbonden omgeving met meerdere forests Active Directory-forest: de algemene scenario's omvatten fusie & overname, waarbij de AD-forests van het overgenomen bedrijf worden geïsoleerd van de AD van het bovenliggende bedrijf. forests en bedrijven die historisch meerdere AD-forests hebben.
- Vereenvoudigde installatie met licht gewicht: de agents fungeren als een brug van AD naar Azure AD, met alle synchronisatie configuratie die in de Cloud wordt beheerd. 
- Meerdere inrichtings agenten kunnen worden gebruikt om implementaties met een hoge Beschik baarheid te vereenvoudigen, met name kritiek voor organisaties die vertrouwen op wachtwoord hash-synchronisatie van AD naar Azure AD.


![Wat is Azure AD Connect?](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Hoe wijkt Azure AD Connect Cloud inrichting af van Azure AD Connect synchronisatie?
Met Azure AD Connect Cloud inrichting wordt het inrichten van AD naar Azure AD ingedeeld in micro soft Online Services. Een organisatie hoeft alleen in hun on-premises en IaaS omgeving te implementeren, een licht gewicht agent die fungeert als een brug tussen Azure AD en AD. De inrichtings configuratie wordt opgeslagen in azure AD en beheerd als onderdeel van de service.

De volgende tabel bevat een vergelijking tussen Azure AD Connect en Azure AD Connect Cloud inrichting:

| Functie | Azure Active Directory Connect synchronisatie| Azure Active Directory Connect Cloud inrichting |
|:--- |:---:|:---:|
|Verbinding maken met één on-premises AD-forest|● |● |
| Verbinding maken met meerdere on-premises AD-forests |● |● |
| Verbinding maken met meerdere niet-verbonden on-premises AD-forests | |● |
| Lightweight agent-installatie model | |● |
| Meerdere actieve agents voor hoge Beschik baarheid | |● |
| Verbinding maken met LDAP-directory's|●| | 
| Ondersteuning voor gebruikers objecten |● |● |
| Ondersteuning voor groeps objecten |● |● |
| Ondersteuning voor contact objecten |● |● |
| Ondersteuning voor apparaatgroepen |● | |
| Basis aanpassing toestaan voor kenmerk stromen |● |● |
| Door de klant gedefinieerde AD-kenmerken (Directory-extensies) synchroniseren |● | |
| Ondersteuning voor wachtwoord-hash-synchronisatie |●|●|
| Ondersteuning voor Pass-Through-verificatie |●||
| Ondersteuning voor Federatie |●|●|
| Naadloze eenmalige aanmelding|● |●|
| Biedt ondersteuning voor installatie op een domeincontroller |● |● |
| Ondersteuning voor Windows Server 2012 en Windows Server 2012 R2 |● |● |
| Filteren op domeinen/Ou's/groepen |● |● |
| Filteren op kenmerkwaarden van objecten |● | |
| Toestaan dat minimale set kenmerken worden gesynchroniseerd (MinSync) |● |● |
| Toestaan dat het verwijderen van kenmerken van AD naar Azure AD stroomt |● |● |
| Geavanceerd aanpassen voor kenmerkstromen toestaan |● | |
| Ondersteuning voor write-back (wacht woorden, apparaten, groepen) |● | |
| Ondersteuning voor Azure AD Domain Services|● | |

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Cloud inrichting installeren](how-to-install.md)
