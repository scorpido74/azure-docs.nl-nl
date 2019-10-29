---
title: Active Directory-gebaseerde services in azure vergelijken | Microsoft Docs
description: In dit overzicht kunt u de verschillende identiteits aanbiedingen voor Active Directory Domain Services, Azure Active Directory en Azure Active Directory Domain Services vergelijken.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: f1759bd2cc82073378c5668682f4cecf8482c2d3
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73042262"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Zelf-beheerde Active Directory Domain Services, Azure Active Directory en beheerde Azure Active Directory Domain Services vergelijken

Om toepassingen, services of apparaten toegang te geven tot een centrale identiteit, zijn er drie veelgebruikte manieren om op Active Directory gebaseerde services in azure te gebruiken. Deze keuze in identiteits oplossingen biedt u de flexibiliteit om de meest geschikte directory te gebruiken voor de behoeften van uw organisatie. Als u bijvoorbeeld voornamelijk Cloud gebruikers beheert die mobiele apparaten uitvoeren, is het wellicht niet zinvol om uw eigen Active Directory Domain Services-identiteits oplossing te bouwen en uit te voeren. In plaats daarvan kunt u Azure Active Directory gebruiken.

Hoewel de drie op Active Directory gebaseerde identiteits oplossingen een gemeen schappelijke naam en technologie delen, zijn ze ontworpen om services te leveren die voldoen aan verschillende eisen van klanten. Op hoog niveau zijn de volgende identiteits oplossingen en functie sets:

* **Active Directory Domain Services (AD DS)** -een bedrijfs klare LDAP-server (Lightweight Directory Access Protocol) die belang rijke functies biedt, zoals identiteit en verificatie, computer object beheer, groeps beleid en vertrouwens relaties.
    * AD DS is een centraal onderdeel in veel organisaties met een on-premises IT-omgeving en biedt basis verificatie van gebruikers accounts en functies voor computer beheer.
* **Azure Active Directory (Azure AD)** -Cloud identiteit en Mobile Device Management die gebruikers accounts en verificatie services biedt voor resources zoals Office 365, de Azure portal of SaaS-toepassingen.
    * Azure AD kan worden gesynchroniseerd met een on-premises AD DS omgeving om één identiteit te bieden aan gebruikers die standaard werken in de Cloud.
* **Azure Active Directory Domain Services (Azure AD DS)** : biedt beheerde domein Services met een subset van volledig compatibele traditionele AD DS functies, zoals domein deelname, groeps beleid, LDAP en Kerberos/NTLM-verificatie.
    * Azure AD DS integreert met Azure AD, dat zelf kan worden gesynchroniseerd met een on-premises AD DS omgeving, om gebruiks voorbeelden van de centrale identiteit uit te breiden naar traditionele webtoepassingen die worden uitgevoerd in azure als onderdeel van een lift-en-Shift-strategie.

In dit overzichts artikel vindt u informatie over de manier waarop deze identiteits oplossingen kunnen samen werken, afhankelijk van de behoeften van uw organisatie.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure-AD DS en zelf-beheerde AD DS

Als u toepassingen en services hebt die toegang nodig hebben tot traditionele verificatie mechanismen zoals Kerberos of NTLM, zijn er twee manieren om Active Directory Domain Services in de cloud te bieden:

* Een *beheerd* domein dat u maakt met behulp van Azure Active Directory Domain Services. Micro soft maakt en beheert de vereiste resources.
* Een *zelf beheerd* domein dat u maakt en configureert met traditionele resources zoals virtuele machines (vm's), Windows Server-gast besturingssysteem en Active Directory Domain Services. U kunt deze resources vervolgens blijven beheren.

Met Azure AD DS worden de belangrijkste service onderdelen door micro soft geïmplementeerd en beheerd als een *beheerde* domein ervaring. U kunt de AD DS-infra structuur voor onderdelen zoals de Vm's, Windows Server-besturings systemen of domein controllers (Dc's) niet implementeren, beheren, patchen en beveiligen. Azure AD DS biedt een kleinere subset van functies voor traditioneel zelf beheerde AD DS omgeving, waardoor de complexiteit van het ontwerp en beheer wordt verminderd. Er zijn bijvoorbeeld geen AD-forests, domein, sites en replicatie koppelingen om te ontwerpen en te onderhouden. Voor toepassingen en services die worden uitgevoerd in de Cloud en toegang moeten hebben tot traditionele verificatie mechanismen zoals Kerberos of NTLM, biedt Azure AD DS een beheerde domein ervaring met de minimale hoeveelheid administratieve overhead.

Wanneer u een zelf-beheerde AD DS omgeving implementeert en uitvoert, moet u alle bijbehorende infra structuur en Directory onderdelen onderhouden. Er is extra onderhouds overhead met een zelfbeheerde AD DS omgeving, maar u kunt vervolgens extra taken uitvoeren, zoals het schema uitbreiden of forest-vertrouwens relaties maken. Algemene implementatie modellen voor een zelf beheerde AD DS omgeving die identiteit biedt aan toepassingen en services in de Cloud, bevatten het volgende:

* **Zelfstandige alleen-cloud AD DS** : Azure-vm's worden geconfigureerd als domein controllers en er wordt een aparte AD DS omgeving voor de Cloud gemaakt. Deze AD DS omgeving kan niet worden geïntegreerd met een on-premises AD DS omgeving. Er wordt een andere set referenties gebruikt voor het aanmelden bij en het beheren van virtuele machines in de Cloud.
* **Implementatie van resource-forest** -Azure vm's zijn geconfigureerd als domein controllers en een AD DS domein als onderdeel van een bestaand forest wordt gemaakt. Vervolgens wordt er een vertrouwens relatie geconfigureerd voor een on-premises AD DS omgeving. Andere virtuele machines van Azure kunnen samen voegen met dit bron-forest in de Cloud. Gebruikers verificatie wordt uitgevoerd via een VPN-ExpressRoute verbinding met de on-premises AD DS omgeving.
* **On-premises domein uitbreiden naar Azure** : een virtueel Azure-netwerk maakt verbinding met een on-premises netwerk met behulp van een VPN-ExpressRoute-verbinding. Azure Vm's maken verbinding met dit virtuele Azure-netwerk, waarmee ze lid kunnen worden van de on-premises AD DS omgeving.
    * U kunt ook virtuele Azure-machines maken en deze promo veren als replica domein controllers van het on-premises AD DS domein. Deze domein controllers repliceren via een VPN-ExpressRoute-verbinding naar de on-premises AD DS omgeving. De on-premises AD DS domein is effectief uitgebreid naar Azure.

De volgende tabel bevat een overzicht van enkele van de functies die u nodig hebt voor uw organisatie en de verschillen tussen een beheerd Azure AD DS domein of een zelf beheerd AD DS domein:

| **Functie** | **Azure AD DS** | **Zelf-beheerde AD DS** |
| ----------- |:---------------:|:----------------------:|
| **Beheerde service**                               | **&#x2713;** | **&#x2715;** |
| **Beveiligde implementaties**                            | **&#x2713;** | De beheerder beveiligt de implementatie |
| **DNS-server**                                    | **&#x2713;** (beheerde service) |**&#x2713;** |
| **Domein-of ondernemings Administrator bevoegdheden** | **&#x2715;** | **&#x2713;** |
| **Domeindeelname**                                   | **&#x2713;** | **&#x2713;** |
| **Domein authenticatie met NTLM en Kerberos** | **&#x2713;** | **&#x2713;** |
| **Beperkte Kerberos-overdracht**               | Op basis van resources | Op resources gebaseerd & op basis van een account|
| **Aangepaste OE-structuur**                           | **&#x2713;** | **&#x2713;** |
| **groepsbeleid**                                  | **&#x2713;** | **&#x2713;** |
| **Schema-uitbrei dingen**                             | **&#x2715;** | **&#x2713;** |
| **Vertrouwens relaties AD-domein/forests**                     | **&#x2715;** | **&#x2713;** |
| **Secure LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP lezen**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP-schrijf bewerkingen**                                    | **&#x2713;** (binnen het beheerde domein) | **&#x2713;** |
| **Geografisch gedistribueerde implementaties**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS en Azure AD

Met Azure AD kunt u de identiteit beheren van apparaten die door de organisatie worden gebruikt en de toegang tot bedrijfs bronnen vanaf deze apparaten regelen. Gebruikers kunnen hun persoonlijke apparaat (een eigen of BYO, model) registreren bij Azure AD, dat het apparaat een identiteit geeft. Azure AD kan vervolgens het apparaat verifiëren wanneer een gebruiker zich aanmeldt bij Azure AD en het apparaat gebruikt voor toegang tot beveiligde bronnen. Het apparaat kan worden beheerd met Mobile Device Management (MDM)-software, zoals Microsoft Intune. Met deze beheer mogelijkheid kunt u de toegang tot gevoelige bronnen beperken tot beheerde en beleids compatibele apparaten.

Traditionele computers en laptops kunnen ook worden toegevoegd aan Azure AD. Dit mechanisme biedt dezelfde voor delen van het registreren van een persoonlijk apparaat met Azure AD, zoals het toestaan van gebruikers om zich aan te melden bij het apparaat met hun bedrijfs referenties. Apparaten die zijn toegevoegd aan Azure AD bieden de volgende voor delen:

* Eenmalige aanmelding (SSO) voor toepassingen die zijn beveiligd met Azure AD.
* Bedrijfs beleid: congebaseerde roaming van gebruikers instellingen op verschillende apparaten.
* Toegang tot de Windows Store voor bedrijven met behulp van bedrijfs referenties.
* Windows hello voor bedrijven.
* Beperkte toegang tot apps en bronnen van apparaten die voldoen aan het bedrijfs beleid.

Apparaten kunnen worden gekoppeld aan Azure AD met of zonder een hybride implementatie die een on-premises AD DS omgeving bevat. De volgende tabel bevat een overzicht van algemene modellen voor het eigendom van apparaten en hoe deze normaal gesp roken worden toegevoegd aan een domein:

| **Type apparaat**                                        | **Apparaat platforms**             | **Handler**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Persoonlijke apparaten                                          | Windows 10, iOS, Android Mac OS | Geregistreerde Azure AD    |
| Het apparaat dat eigendom is van de organisatie en niet is gekoppeld aan on-premises AD DS | Windows 10                       | Toegevoegd aan Azure AD        |
| Apparaat waarvan de organisatie is verbonden met een on-premises AD DS  | Windows 10                       | Hybride Azure AD-join |

Op een met Azure AD toegevoegde of geregistreerde apparaten gebeurt de verificatie van de gebruiker via moderne OAuth/OpenID Connect Connect-protocollen. Deze protocollen zijn ontworpen om via internet te werken. het is dus handig voor mobiele scenario's waarin gebruikers vanaf elke locatie toegang hebben tot bedrijfs bronnen. Met Azure AD DS-apparaten kunnen toepassingen gebruikmaken van de Kerberos-en NTLM-protocollen voor verificatie, zodat oudere toepassingen worden ondersteund die worden gemigreerd om te worden uitgevoerd op virtuele Azure-machines als onderdeel van een lift-en-Shift-strategie. De volgende tabel bevat een overzicht van de verschillen in de weer gave van de apparaten en kan zichzelf verifiëren voor de Directory:

| **-**                      | **Toegevoegd aan Azure AD**                                 | **Azure AD DS-lid**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Apparaat beheerd door            | Azure AD                                            | Beheerd domein door Azure AD DS                                                |
| Vertegenwoordiging in de Directory | Apparaatgroepen in de Azure AD-adres lijst            | Computer objecten in het door Azure AD DS beheerde domein                        |
| Verificatie                  | Op OAuth/OpenID Connect Connect gebaseerde protocollen              | Kerberos-en NTLM-protocollen                                               |
| Beheer                      | MDM-software (Mobile Device Management), zoals intune | groepsbeleid                                                              |
| Networking                      | Werkt via Internet                             | Moet zijn verbonden met of moeten worden gekoppeld aan het virtuele netwerk waarop het beheerde domein is geïmplementeerd |
| Geweldig voor...                    | Mobiele of desktop apparaten voor eind gebruikers                  | Server-Vm's die zijn geïmplementeerd in azure                                              |

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan met Azure AD DS, [maakt u een met azure AD DS beheerd domein met behulp van de Azure Portal][tutorial-create].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
