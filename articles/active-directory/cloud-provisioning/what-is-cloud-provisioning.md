---
title: Wat is Azure AD Connect cloud provisioning. | Microsoft Docs
description: Beschrijft Azure AD Connect cloud provisioning.
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
ms.openlocfilehash: a86d34fca9a88b0df601533a0f3de1cc97ad1a2f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050599"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Wat is Azure AD Connect-cloudinrichting?
Azure AD Connect cloud provisioning is een nieuwe Microsoft-agent die is ontworpen om uw hybride identiteitsdoelen voor synchronisatie van gebruikers, groepen en contactpersonen naar Azure AD te bereiken en te verwezenlijken.  Het kan worden gebruikt naast Azure AD Connect-synchronisatie en biedt de volgende voordelen:
    
- Ondersteuning voor het synchroniseren met een Azure AD-tenant vanuit een active directory-forestomgeving met meerdere forestverbindingen: de veelvoorkomende scenario's omvatten fusie & overname, waarbij de AD-forests van het overgenomen bedrijf zijn geïsoleerd van de AD-forests van het moederbedrijf en bedrijven die historisch gezien meerdere AD-forests hebben gehad.
- Vereenvoudigde installatie met lichtgewicht provisioning agents: de agents fungeren als een brug van AD naar Azure AD, waarbij alle synchronisatieconfiguratie wordt beheerd in de cloud. 
- Meerdere inrichtingsagents kunnen worden gebruikt om implementaties met hoge beschikbaarheid te vereenvoudigen, met name voor organisaties die vertrouwen op wachtwoordhashsynchronisatie van AD naar Azure AD.


![Wat is Azure AD Connect?](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Hoe verschilt azure AD Connect cloudprovisioning van Azure AD Connect sync?
Met Azure AD Connect-cloudinrichting wordt de inrichting van AD naar Azure AD georkestreerd in Microsoft Online Services. Een organisatie hoeft alleen in hun on-premises en iaas-omgeving een lichtgewicht agent te implementeren die fungeert als een brug tussen Azure AD en AD. De inrichtingsconfiguratie wordt opgeslagen in Azure AD en beheerd als onderdeel van de service.

In de volgende tabel vindt u een vergelijking tussen Azure AD Connect en Azure AD Connect-cloudinrichting:

| Functie | Azure Active Directory Connect-synchronisatie| Azure Active Directory Connect-cloudinrichting |
|:--- |:---:|:---:|
|Verbinding maken met één on-premises AD-forest|● |● |
| Verbinding maken met meerdere on-premises AD-forests |● |● |
| Verbinding maken met meerdere losgekoppelde on-premises AD-forests | |● |
| Lichtgewicht agent installatiemodel | |● |
| Meerdere actieve agenten voor hoge beschikbaarheid | |● |
| Verbinding maken met LDAP-mappen|●| | 
| Ondersteuning voor gebruikersobjecten |● |● |
| Ondersteuning voor groepsobjecten |● |● |
| Ondersteuning voor contactobjecten |● |● |
| Ondersteuning voor apparaatobjecten |● | |
| Basisaanpassing toestaan voor kenmerkstromen |● |● |
| Sychronize Exchange online attributen |● |● |
| Extensiekenmerken 1-15 synchroniseren |● |● |
| Door de klant gedefinieerde AD-kenmerken synchroniseren (directory-extensies) |● | |
| Ondersteuning voor wachtwoordhashsynchronisatie |●|●|
| Ondersteuning voor pass-through-verificatie |●||
| Steun voor federatie |●|●|
| Naadloze eenmalige aanmelding|● |●|
| Biedt ondersteuning voor installatie op een domeincontroller |● |● |
| Ondersteuning voor Windows Server 2012 en Windows Server 2012 R2 |● |● |
| Filteren op domeinen/oe's/groepen |● |● |
| Filteren op kenmerkwaarden van objecten |● | |
| Toestaan dat minimale set kenmerken worden gesynchroniseerd (MinSync) |● |● |
| Toestaan dat het verwijderen van kenmerken van AD naar Azure AD stroomt |● |● |
| Geavanceerd aanpassen voor kenmerkstromen toestaan |● | |
| Ondersteuning voor terugschrijven (wachtwoorden, apparaten, groepen) |● | |
| Ondersteuning voor Azure AD Domain Services|● | |
| [Hybride terugschrijftekst van Exchange](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Ondersteuning voor meer dan 50.000 objecten per AD-domein |● | |

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Cloudprovisioning installeren](how-to-install.md)
