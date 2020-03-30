---
title: Hybride Azure Active Directory joinplannen - Azure Active Directory
description: Lees hoe u hybride Azure Active Directory-gekoppelde apparaten kunt configureren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152ff52ce52b573d7f24cbb2fafc944b1794f6d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129263"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>How To: Uw hybride Azure Active Directory join-implementatie plannen

Op een vergelijkbare manier als een gebruiker is een apparaat een andere kernidentiteit die u wilt beschermen en gebruiken om uw resources op elk gewenst moment en vanaf elke locatie te beschermen. U dit doel bereiken door apparaatidentiteiten in Azure AD te plaatsen en te beheren met een van de volgende methoden:

- Azure AD-koppeling
- Hybride Azure AD-koppeling
- Azure AD-registratie

Door uw apparaten naar Azure AD te brengen, optimaliseert u de productiviteit van uw gebruikers via eenmalige aanmelding (SSO) bij al uw on-premises bronnen en cloudbronnen. Tegelijkertijd u de toegang tot uw cloud- en on-premises bronnen beveiligen met [voorwaardelijke toegang.](../active-directory-conditional-access-azure-portal.md)

Als u een on-premises Active Directory-omgeving (AD) hebt en u wilt deelnemen aan uw AD-domeinverbonden computers in Azure AD, u dit bereiken door hybride Azure AD-lid te maken. In dit artikel vindt u de bijbehorende stappen om een hybride Azure AD-deelname in uw omgeving te implementeren. 

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met het beheer van de identiteit van het [apparaat in Azure Active Directory.](../device-management-introduction.md)

> [!NOTE]
> De minimaal vereiste versie van de domeincontroller voor Windows 10 hybride Azure AD join is Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Uw implementatie plannen

Als u uw hybride Azure AD-implementatie wilt plannen, moet u vertrouwd raken met:

|   |   |
| --- | --- |
| ![Selecteren][1] | Ondersteunde apparaten controleren |
| ![Selecteren][1] | Bekijk dingen die je moet weten |
| ![Selecteren][1] | Gecontroleerde validatie van hybride Azure AD-lid controleren |
| ![Selecteren][1] | Selecteer uw scenario op basis van uw identiteitsinfrastructuur |
| ![Selecteren][1] | On-premises AD UPN-ondersteuning voor hybride Azure AD-lid bekijken |

## <a name="review-supported-devices"></a>Ondersteunde apparaten controleren

Hybride Azure AD join ondersteunt een breed scala aan Windows-apparaten. Omdat de configuratie voor apparaten met oudere versies van Windows extra of verschillende stappen vereist, worden de ondersteunde apparaten gegroepeerd in twee categorieën:

### <a name="windows-current-devices"></a>Windows huidige apparaten

- Windows 10
- Windows Server 2016
  - **Opmerking**: Azure National-cloudklanten hebben versie 1809 nodig
- Windows Server 2019

Voor apparaten waarop het Windows-bureaubladbesturingssysteem wordt uitgevoerd, wordt de ondersteunde versie weergegeven in dit artikel [Windows 10-releasegegevens](/windows/release-information/). Als aanbevolen aanbevolen aanbevolen toepassing raadt Microsoft u aan te upgraden naar de nieuwste versie van Windows 10.

### <a name="windows-down-level-devices"></a>Windows-apparaten op downniveau

- Windows 8.1
- Windows 7-ondersteuning is beëindigd op 14 januari 2020. Zie [Ondersteuning voor Windows 7 is beëindigd voor](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)meer informatie.
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Zie Voorbereiden op [het einde van de ondersteuning van Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008)en 2008 R2 voor ondersteuningsinformatie over Windows Server 2008 en 2008 R2.

Als eerste planningsstap moet u uw omgeving bekijken en bepalen of u apparaten op laagniveau van Windows moet ondersteunen.

## <a name="review-things-you-should-know"></a>Bekijk dingen die je moet weten

### <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's
- Hybride Azure AD-lid wordt momenteel niet ondersteund als uw omgeving bestaat uit één AD-forest dat identiteitsgegevens synchroniseert met meer dan één Azure AD-tenant.

- Hybride Azure AD-join wordt niet ondersteund voor Windows Server met de DC-rol (Domain Controller).

- Hybride Azure AD-join wordt niet ondersteund op Windows-apparaten op downniveau wanneer u roaming of gebruikersprofielroaming of een verplicht profiel gebruikt.

- Server Core OS biedt geen ondersteuning voor elke vorm van apparaatregistratie.

### <a name="os-imaging-considerations"></a>Overwegingen voor beeldvorming van het besturingssysteem
- Als u vertrouwt op het Systeemvoorbereidingsprogramma (Sysprep) en als u een **afbeelding van voor Windows 10 1809** gebruikt voor installatie, moet u ervoor zorgen dat deze afbeelding niet afkomstig is van een apparaat dat al is geregistreerd bij Azure AD als hybride Azure AD-lid wordt.

- Als u een vm-momentopname (Virtual Machine) gebruikt om extra VM's te maken, moet u ervoor zorgen dat momentopname niet afkomstig is van een vm die al is geregistreerd bij Azure AD, omdat hybride Azure AD lid wordt.

- Als u [Unified Write Filter](/windows-hardware/customize/enterprise/unified-write-filter) en vergelijkbare technologieën gebruikt die wijzigingen in de schijf wissen bij het opnieuw opstarten, moeten ze worden toegepast nadat het apparaat hybride Azure AD is toegetreden. Als u dergelijke technologieën inschakelt voordat hybride Azure AD-lid wordt voltooid, wordt het apparaat bij elke herstart ongedaan gemaakt

### <a name="handling-devices-with-azure-ad-registered-state"></a>Apparaten verwerken met azure AD-status
Als uw Windows 10-domein is samengevoegd met [Azure AD dat is geregistreerd bij](overview.md#getting-devices-in-azure-ad) uw tenant, kan dit leiden tot een dubbele status van Hybride Azure AD en azure AD-geregistreerd apparaat. We raden u aan te upgraden naar Windows 10 1803 (met KB4489894 toegepast) of hoger om dit scenario automatisch aan te pakken. In pre-1803-versies moet u de status van Azure AD-status handmatig verwijderen voordat u Hybride Azure AD-deelname inschakelt. In 1803 en hoger releases, de volgende wijzigingen zijn aangebracht om deze dubbele toestand te voorkomen:

- Elke bestaande Azure AD-status voor een gebruiker wordt automatisch verwijderd <i>nadat het apparaat hybride Azure AD is toegetreden en dezelfde gebruiker zich aanmeldt.</i> Als gebruiker A bijvoorbeeld een Azure AD-status op het apparaat had, wordt de dubbele status voor gebruiker A alleen opgeschoond wanneer gebruiker A zich aanmeldt bij het apparaat. Als er meerdere gebruikers op hetzelfde apparaat zijn, wordt de dubbele status afzonderlijk opgeschoond wanneer deze gebruikers zich aanmelden.
- U voorkomen dat uw domein is verbonden als Azure AD-register door deze registersleutel toe te voegen - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, BlockAADWorkplaceJoin=dword:00000001.
- Als u Windows Hello for Business hebt geconfigureerd, moet de gebruiker in Windows 10 1803 windows Hello for Business opnieuw instellen nadat de dubbele status is opgeschoond. Dit probleem is verholpen met KB4512509

> [!NOTE]
> Het door Azure AD geregistreerde apparaat wordt niet automatisch verwijderd als het wordt beheerd door Intune.

### <a name="additional-considerations"></a>Aanvullende overwegingen
- Zie [Apparaatidentiteit en desktopvirtualisatie](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure)als uw omgeving gebruik maakt van virtuele desktopinfrastructuur (VDI).

- Hybride Azure AD join wordt ondersteund voor FIPS-compatibele TPM 2.0 en wordt niet ondersteund voor TPM 1.2. Als uw apparaten FIPS-compatibele TPM 1.2 hebben, moet u ze uitschakelen voordat u verdergaat met hybride Azure AD-lid. Microsoft biedt geen tools voor het uitschakelen van de FIPS-modus voor TPM's, omdat deze afhankelijk is van de TPM-fabrikant. Neem contact op met uw hardware-OEM voor ondersteuning. 

- Vanaf de release van Windows 10 1903 worden TPM's 1.2 niet gebruikt met hybride Azure AD-join en worden apparaten met die TPM's beschouwd alsof ze geen TPM hebben.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Gecontroleerde validatie van hybride Azure AD-lid controleren

Wanneer alle vereisten zijn ingevoerd, registreren Windows-apparaten zich automatisch als apparaten in uw Azure AD-tenant. De status van deze apparaatidentiteiten in Azure AD wordt hybride Azure AD-lid genoemd. Meer informatie over de concepten in dit artikel vindt u in het artikel [Inleiding tot apparaatidentiteitsbeheer in Azure Active Directory](overview.md).

Organisaties willen mogelijk een gecontroleerde validatie van hybride Azure AD-join uitvoeren voordat ze deze in hun hele organisatie tegelijk inschakelen. Bekijk de [artikelgecontroleerde validatie van hybride Azure AD join](hybrid-azuread-join-control.md) om te begrijpen hoe dit te bereiken.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selecteer uw scenario op basis van uw identiteitsinfrastructuur

Hybride Azure AD-join werkt met zowel beheerde als federatieve omgevingen, afhankelijk van of de UPN routeerbaar of niet-routeerbaar is. Zie onderaan de pagina voor tabel over ondersteunde scenario's.  

### <a name="managed-environment"></a>Beheerde omgeving

Een beheerde omgeving kan worden geïmplementeerd via [Password Hash Sync (PHS)](/azure/active-directory/hybrid/whatis-phs) of [Pass Through Authentication (PTA)](/azure/active-directory/hybrid/how-to-connect-pta) met [Seamless Single Sign On.](/azure/active-directory/hybrid/how-to-connect-sso)

Voor deze scenario's hoeft u geen federatieserver te configureren voor verificatie.

### <a name="federated-environment"></a>Federatieve omgeving

Een federatieve omgeving moet een identiteitsprovider hebben die de volgende vereisten ondersteunt. Als u een federatieve omgeving hebt met Active Directory Federation Services (AD FS), worden de onderstaande vereisten al ondersteund.

- **WIAORMULTIAUTHN claim:** Deze claim is vereist voor hybride Azure AD join voor Windows down-level apparaten.
- **WS-Trust-protocol:** Dit protocol is vereist om windows huidige hybride Azure AD-apparaten te verifiëren met Azure AD. Wanneer u AD FS gebruikt, moet u de volgende WS-Trust-eindpunten inschakelen:`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Zowel **adfs/services/trust/2005/windowstransport** of **adfs/services/trust/13/windowstransport** moet alleen worden ingeschakeld als intranet gerichte eindpunten en mogen NIET worden blootgesteld als extranetgerichte eindpunten via de Web Application Proxy. Zie [Windows-eindpunten voor WS-Trust uitschakelen op de proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)voor meer informatie over het uitschakelen van Windows-eindpunten voor WS-Trust. U zien welke eindpunten zijn ingeschakeld via de AD FS-beheerconsole onder > **Serviceeindpunten**. **Service**

> [!NOTE]
> Azure AD biedt geen ondersteuning voor smartcards of certificaten in beheerde domeinen.

Vanaf versie 1.1.819.0 bevat Azure AD Connect een wizard om hybride Azure AD-koppeling te configureren. Met de wizard kunt u het configuratieproces aanzienlijk vereenvoudigen. Als het installeren van de vereiste versie van Azure AD Connect geen optie voor u is, raadpleegt u hoe u [apparaatregistratie handmatig configureert.](hybrid-azuread-join-manual.md) 

Op basis van het scenario dat overeenkomt met uw identiteitsinfrastructuur, zie:

- [Hybride Azure Active Directory join configureren voor federatieve omgeving](hybrid-azuread-join-federated-domains.md)
- [Hybride Azure Active Directory join configureren voor beheerde omgeving](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Upn-ondersteuning voor on-premises AD-gebruikers bekijken voor hybride Azure AD

Soms kunnen upn's van uw on-premises AD-gebruikers verschillen van uw Azure AD-UPN's. In dergelijke gevallen biedt Windows 10 Hybrid Azure AD join beperkte ondersteuning voor on-premises AD UPN's op basis van de [verificatiemethode,](/azure/security/fundamentals/choose-ad-authn)het domeintype en de Windows 10-versie. Er zijn twee soorten on-premises AD-UPN's die in uw omgeving kunnen bestaan:

- Routable users UPN: Een routable UPN heeft een geldig geverifieerd domein, dat is geregistreerd bij een domeinregistrar. Als contoso.com bijvoorbeeld het primaire domein in Azure AD is, is contoso.org het primaire domein in on-premises AD dat eigendom is van Contoso en [is geverifieerd in Azure AD](/azure/active-directory/fundamentals/add-custom-domain)
- Niet-routable gebruikers UPN: Een niet-routable UPN heeft geen geverifieerd domein. Het is alleen van toepassing binnen het privénetwerk van uw organisatie. Als contoso.com bijvoorbeeld het primaire domein in Azure AD is, is contoso.local het primaire domein in on-premises AD, maar is het geen verifieerbaar domein op internet en wordt het alleen binnen het netwerk van Contoso gebruikt.

> [!NOTE]
> De informatie in deze sectie is alleen van toepassing op een on-premises gebruikers UPN. Het is niet van toepassing op een on-premises computerdomeinachtervoegsel (voorbeeld: computer1.contoso.local).

In de onderstaande tabel vindt u informatie over ondersteuning voor deze on-premises AD-UPN's in Windows 10 Hybrid Azure AD join

| Type of on-premises AD UPN | Domeintype | Windows 10-versie | Beschrijving |
| ----- | ----- | ----- | ----- |
| Routeerbaar | Federatief | Vanaf 1703 release | Algemeen verkrijgbaar |
| Niet-routeerbaar | Federatief | Vanaf 1803 release | Algemeen verkrijgbaar |
| Routeerbaar | Beheerd | Vanaf 1803 release | Azure AD SSPR op Windows-vergrendelingsscherm wordt over het algemeen beschikbaar |
| Niet-routeerbaar | Beheerd | Niet ondersteund | |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Hybride Azure Active Directory join configureren voor federatieve omgeving](hybrid-azuread-join-federated-domains.md)
> [Configureren hybride Azure Active Directory join voor beheerde omgeving](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
