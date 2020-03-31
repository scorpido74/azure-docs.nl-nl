---
title: Active Directory-gebaseerde services vergelijken in Azure | Microsoft Documenten
description: In dit overzicht vergelijkt u de verschillende identiteitsaanbiedingen voor Active Directory Domain Services, Azure Active Directory en Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: d2495605cccf658b15e812fd85fd65671e84d15b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76544273"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Zelfbeheerde Active Directory Domain Services, Azure Active Directory en beheerde Azure Active Directory Domain Services vergelijken

Om toepassingen, services of apparaten toegang te bieden tot een centrale identiteit, zijn er drie veelvoorkomende manieren om Active Directory-gebaseerde services in Azure te gebruiken. Deze keuze in identiteitsoplossingen geeft u de flexibiliteit om de meest geschikte directory te gebruiken voor de behoeften van uw organisatie. Als u bijvoorbeeld meestal cloudgebruikers beheert die mobiele apparaten uitvoeren, heeft het mogelijk geen zin om uw eigen AD DS-identiteitsoplossing (Active Directory Domain Services) te bouwen en uit te voeren. In plaats daarvan u azure Active Directory gewoon gebruiken.

Hoewel de drie op Active Directory gebaseerde identiteitsoplossingen een gemeenschappelijke naam en technologie delen, zijn ze ontworpen om services te leveren die voldoen aan verschillende eisen van de klant. Op hoog niveau zijn deze identiteitsoplossingen en functiesets:

* **Active Directory Domain Services (AD DS)** - LDAP-server (Enterprise-ready lightweight directory access protocol) die belangrijke functies biedt, zoals identiteit en verificatie, computerobjectbeheer, groepsbeleid en vertrouwensrelaties.
    * AD DS is een centraal onderdeel in veel organisaties met een on-premises IT-omgeving en biedt kernfuncties voor gebruikersaccountverificatie en computerbeheer.
* **Azure Active Directory (Azure AD)** - Cloudgebaseerd identiteits- en beheer van mobiele apparaten die gebruikersaccount- en verificatieservices biedt voor bronnen zoals Office 365, de Azure-portal of SaaS-toepassingen.
    * Azure AD kan worden gesynchroniseerd met een on-premises AD DS-omgeving om één identiteit te bieden aan gebruikers die native in de cloud werken.
* **Azure Active Directory Domain Services (Azure AD DS)** - Biedt beheerde domeinservices met een subset van volledig compatibele traditionele AD DS-functies, zoals domeinjoin, groepsbeleid, LDAP en Kerberos / NTLM-verificatie.
    * Azure AD DS integreert met Azure AD, dat zelf kan synchroniseren met een on-premises AD DS-omgeving. Deze mogelijkheid breidt centrale identiteitsgebruiksgevallen uit tot traditionele webtoepassingen die in Azure worden uitgevoerd als onderdeel van een lift-and-shift-strategie.

In dit overzichtsartikel wordt vergeleken met hoe deze identiteitsoplossingen kunnen samenwerken of onafhankelijk kunnen worden gebruikt, afhankelijk van de behoeften van uw organisatie.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS en zelfbeheerde AD DS

Als u toepassingen en services hebt die toegang nodig hebben tot traditionele verificatiemechanismen zoals Kerberos of NTLM, zijn er twee manieren om Active Directory Domain Services in de cloud aan te bieden:

* Een *beheerd* domein dat u maakt met Azure Active Directory Domain Services (Azure AD DS). Microsoft maakt en beheert de vereiste resources.
* Een *zelfbeheerd* domein dat u maakt en configureert met behulp van traditionele bronnen zoals virtuele machines (VM's), Windows Server-gast-besturingssysteem en AD DS (Active Directory Domain Services). Vervolgens blijft u deze resources beheren.

Met Azure AD DS worden de kernservicecomponenten geïmplementeerd en voor u onderhouden door Microsoft als *beheerde* domeinervaring. U implementeert, beheert, patcht en beveiligt de AD DS-infrastructuur niet voor onderdelen zoals de VM's, Windows Server OS of domeincontrollers (DC's).

Azure AD DS biedt een kleinere subset van functies voor de traditionele zelfbeheerde AD DS-omgeving, waardoor een deel van de complexiteit van het ontwerp en beheer wordt verminderd. Er zijn bijvoorbeeld geen AD-forests, domein- en replicatiekoppelingen om te ontwerpen en te onderhouden. Voor toepassingen en services die in de cloud worden uitgevoerd en toegang tot traditionele verificatiemechanismen zoals Kerberos of NTLM nodig hebben, biedt Azure AD DS een beheerde domeinervaring met de minimale hoeveelheid administratieve overhead.

Wanneer u een zelfbeheerde AD DS-omgeving implementeert en uitvoert, moet u alle bijbehorende infrastructuur- en directorycomponenten onderhouden. Er is extra overhead onderhoud met een zelfbeheerde AD DS-omgeving, maar u vervolgens extra taken uitvoeren, zoals het schema uitbreiden of forestvertrouwensrelaties maken.

Veelgebruikte implementatiemodellen voor een zelfbeheerde AD DS-omgeving die identiteit biedt aan toepassingen en services in de cloud, zijn onder andere:

* **Standalone AD DS -** Azure VM's zijn geconfigureerd als domeincontrollers en er wordt een aparte AD DS-omgeving gemaakt die alleen in de cloud staat. Deze AD DS-omgeving integreert niet met een on-premises AD DS-omgeving. Een andere set referenties wordt gebruikt om u aan te melden en VM's in de cloud te beheren.
* **Implementatie van resourceforest** - Azure VM's zijn geconfigureerd als domeincontrollers en er wordt een AD DS-domein gemaakt dat deel uitmaakt van een bestaand forest. Een vertrouwensrelatie wordt vervolgens geconfigureerd naar een on-premises AD DS-omgeving. Andere Azure VM's kunnen domein-join en dit bronforest in de cloud. Gebruikersverificatie verloopt via een VPN / ExpressRoute-verbinding met de on-premises AD DS-omgeving.
* **On-premises domein uitbreiden naar Azure** - Een virtueel Azure-netwerk maakt verbinding met een on-premises netwerk via een VPN / ExpressRoute-verbinding. Azure VM's maken verbinding met dit virtuele Azure-netwerk, waarmee ze kunnen deelnemen aan de on-premises AD DS-omgeving.
    * Een alternatief is het maken van Azure VM's en deze te promoten als replicadomeincontrollers uit het on-premises AD DS-domein. Deze domeincontrollers repliceren via een VPN / ExpressRoute-verbinding met de on-premises AD DS-omgeving. Het on-premises AD DS-domein wordt effectief uitgebreid naar Azure.

In de volgende tabel worden enkele functies beschreven die u mogelijk nodig hebt voor uw organisatie en de verschillen tussen een beheerd Azure AD DS-domein of een zelfbeheerd AD DS-domein:

| **Functie** | **Azure AD DS** | **Zelfbeheerde AD DS** |
| ----------- |:---------------:|:----------------------:|
| **Beheerde service**                               | **&#x2713;** | **&#x2715;** |
| **Beveiligde implementaties**                            | **&#x2713;** | Beheerder beveiligt de implementatie |
| **DNS-server**                                    | **&#x2713;** (managed service) |**&#x2713;** |
| **Bevoegdheden voor domein- of Ondernemingsbeheerders** | **&#x2715;** | **&#x2713;** |
| **Domeindeelname**                                   | **&#x2713;** | **&#x2713;** |
| **Domeinverificatie met NTLM en Kerberos** | **&#x2713;** | **&#x2713;** |
| **Kerberos beperkte delegatie**               | Op basis van bronnen | Op basis van resources & account gebaseerd|
| **Aangepaste ou-structuur**                           | **&#x2713;** | **&#x2713;** |
| **Groepsbeleid**                                  | **&#x2713;** | **&#x2713;** |
| **Schema-uitbreidingen**                             | **&#x2715;** | **&#x2713;** |
| **AD-domein / forestvertrouwensrelaties**                     | **&#x2713;** (alleen eenenkele uitgaande forestvertrouwensrelaties) | **&#x2713;** |
| **Beveiligde LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP gelezen**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP-schrijven**                                    | **&#x2713;** (binnen het beheerde domein) | **&#x2713;** |
| **Geogedistribueerde implementaties**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS en Azure AD

Met Azure AD u de identiteit beheren van apparaten die door de organisatie worden gebruikt en de toegang tot bedrijfsbronnen vanaf die apparaten beheren. Gebruikers kunnen hun persoonlijke apparaat (een byo-model (bring-your-own) ook registreren bij Azure AD, dat het apparaat een identiteit geeft. Azure AD verifieert het apparaat vervolgens wanneer een gebruiker zich aanmeldt bij Azure AD en het apparaat gebruikt om toegang te krijgen tot beveiligde bronnen. Het apparaat kan worden beheerd met behulp van MDM-software (Mobile Device Management), zoals Microsoft Intune. Met deze beheermogelijkheid u de toegang tot gevoelige bronnen beperken tot beheerde en beleidscompatibele apparaten.

Traditionele computers en laptops kunnen ook deelnemen aan Azure AD. Dit mechanisme biedt dezelfde voordelen van het registreren van een persoonlijk apparaat bij Azure AD, zoals gebruikers toestaan zich bij het apparaat aan te melden met hun bedrijfsreferenties.

Azure AD-apparaten bieden u de volgende voordelen:

* SSO (Single-sign-on) voor toepassingen die zijn beveiligd door Azure AD.
* Roaming die voldoet aan het bedrijfsbeleid van gebruikersinstellingen op verschillende apparaten.
* Toegang tot de Windows Store voor Bedrijven met bedrijfsreferenties.
* Windows Hello voor Bedrijven.
* Beperkte toegang tot apps en bronnen vanaf apparaten die voldoen aan het bedrijfsbeleid.

Apparaten kunnen worden gekoppeld aan Azure AD met of zonder een hybride implementatie die een on-premises AD DS-omgeving bevat. In de volgende tabel worden de algemene eigendomsmodellen voor apparaten beschreven en hoe deze doorgaans aan een domein worden samengevoegd:

| **Type apparaat**                                        | **Apparaatplatformen**             | **Mechanisme**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Persoonlijke apparaten                                          | Windows 10, iOS, Android, Mac OS | Azure AD-geregistreerd    |
| Apparaat dat eigendom is van een organisatie, niet is aangesloten bij on-premises AD DS | Windows 10                       | Neemt deel aan Azure AD        |
| Apparaat dat eigendom is van een organisatie, is samengevoegd met een on-premises AD DS  | Windows 10                       | lid is van hybride Azure Active Directory |

Op een door Azure gekoppeld of geregistreerd apparaat gebeurt gebruikersverificatie met behulp van moderne OAuth/ OpenID Connect-gebaseerde protocollen. Deze protocollen zijn ontworpen om te werken via het internet, dus zijn geweldig voor mobiele scenario's waar gebruikers toegang tot zakelijke middelen van overal.

Met Azure AD DS-apparaten kunnen toepassingen de Kerberos- en NTLM-protocollen gebruiken voor verificatie, zodat oudere toepassingen kunnen worden gemigreerd om op Azure VM's te worden uitgevoerd als onderdeel van een lift-and-shift-strategie. In de volgende tabel worden verschillen beschreven in de manier waarop de apparaten worden weergegeven en u zich verifiëren ten opzichte van de map:

| **Aspect**                      | **Azure AD-verbonden**                                 | **Azure AD DS-lid**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Apparaat bestuurd door            | Azure AD                                            | Beheerd Azure AD DS-domein                                                |
| Afbeelding in de map | Apparaatobjecten in de Azure AD-map            | Computerobjecten in het beheerde Azure AD DS-domein                        |
| Authentication                  | OAuth / OpenID Connect gebaseerde protocollen              | Kerberos- en NTLM-protocollen                                               |
| Beheer                      | Mobile Device Management (MDM) software zoals Intune | Groepsbeleid                                                              |
| Netwerken                      | Werkt via het internet                             | Moet verbonden zijn met of worden gekeken met het virtuele netwerk waar het beheerde domein wordt geïmplementeerd |
| Geweldig voor...                    | Mobiele apparaten voor eindgebruikers                  | Server VM's geïmplementeerd in Azure                                              |

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt met het gebruik van Azure AD DS, [maakt u een door Azure AD DS beheerd domein met behulp van de Azure-portal.][tutorial-create]

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
