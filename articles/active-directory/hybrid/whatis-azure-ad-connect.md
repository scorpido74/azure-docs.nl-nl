---
title: Wat zijn Azure AD Connect en Connect Health? | Microsoft Docs
description: Hier worden de hulpprogramma's beschreven die worden gebruikt om uw on-premises omgeving met Azure AD te synchroniseren en bewaken.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e60c35a32152d4adec72fb507becc0db535036f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631729"
---
# <a name="what-is-azure-ad-connect"></a>Wat is Azure AD Connect?

Azure AD Connect is het programma van Microsoft dat is ontworpen om te voldoen aan uw doelstellingen voor een hybride identiteit.  Deze biedt de volgende functies:
     
- [Wachtwoord-hashsynchronisatie](whatis-phs.md): een aanmeldingsmethode waarmee de hash van een on-premises AD-wachtwoord van een gebruiker wordt gesynchroniseerd met Azure AD.
- [Pass through-verificatie](how-to-connect-pta.md): een aanmeldingsmethode waarmee gebruikers hetzelfde wachtwoord on-premises en in de cloud kunnen gebruiken, zonder dat er een extra infrastructuur van een federatieve omgeving vereist is.
- [Integratie van federatie](how-to-connect-fed-whatis.md): een optioneel onderdeel van Azure AD Connect dat kan worden gebruikt om een hybride omgeving te configureren met behulp van een on-premises AD FS-infrastructuur. Het onderdeel biedt ook beheermogelijkheden voor AD FS, zoals het verlengen van certificaten en aanvullende implementaties van AD FS-servers.
- [Synchronisatie](how-to-connect-sync-whatis.md): verantwoordelijk voor het maken van gebruikers, groepen en andere objecten.  Het kan ook gebruikt worden om te controleren of de identiteitsinformatie van uw on-premises gebruikers en groepen overeenkomt met de cloud.  Deze synchronisatie bevat ook wachtwoord-hashes.
- [Statusbewaking](whatis-hybrid-identity-health.md) - Azure AD Connect Health kan robuuste bewaking bieden en een centrale locatie bieden in de Azure-portal om deze activiteit te bekijken. 


![Wat is Azure AD Connect?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Wat is Azure AD Connect Health?

Azure Active Directory (Azure AD) Connect Health biedt robuuste bewaking van uw on-premises infrastructuur voor identiteiten. Hiermee kunt u een betrouwbare verbinding met Office 365 en Microsoft Online Services onderhouden.  Deze betrouwbaarheid wordt bereikt door bewakingsmogelijkheden voor uw belangrijkste identiteitsonderdelen in te stellen. Bovendien worden daardoor de belangrijkste gegevenspunten over deze onderdelen gemakkelijk toegankelijk.

Deze informatie vindt u in de [portal voor Azure AD Connect Health](https://aka.ms/aadconnecthealth). Met de portal voor Azure AD Connect Health kunt u waarschuwingen bekijken, de prestaties bewaken, het gebruik analyseren en nog veel meer. Met Azure AD Connect Health ziet u in één oogopslag de status van uw belangrijkste identiteitsonderdelen.

![Wat is Azure AD Connect Health?](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Waarom Azure AD Connect gebruiken?
Wanneer u uw on-premises directory's integreert met Azure AD, worden uw gebruikers productiever omdat zij één identiteit hebben voor toegang tot zowel cloud als on-premises resources. Gebruikers en organisaties kunnen profiteren van:

* Gebruikers kunnen één identiteit gebruiken voor toegang tot on-premises toepassingen en cloudservices zoals Office 365.
* Eén hulpprogramma voor gemakkelijke implementatie voor synchronisatie en aanmelden.
* Biedt de nieuwste mogelijkheden voor uw scenario's. Azure AD Connect vervangt oudere versies van hulpprogramma's voor identiteitsintegratie, zoals DirSync en Azure AD Sync. Zie [Hybrid Identity directory integration tools comparison](plan-hybrid-identity-design-considerations-tools-comparison.md)voor meer informatie.

## <a name="why-use-azure-ad-connect-health"></a>Waarom het handig is om Azure AD Connect Health te gebruiken
Wanneer u Azure AD gebruikt, worden uw gebruikers productiever omdat zij over één identiteit beschikken voor toegang tot zowel cloud- als on-premises resources. Hiermee zorgt u voor een betrouwbare omgeving, zodat gebruikers toegang tot resources hebben, wordt een hele uitdaging.  Azure AD Connect Health helpt om inzicht te verkrijgen in uw on-premises infrastructuur voor identiteiten waardoor de betrouwbaarheid van deze omgeving wordt gegarandeerd. Het is net zo eenvoudig als het installeren van een agent op een van uw on-premises identiteitsservers.

Azure AD Connect Health voor AD FS biedt ondersteuning voor AD FS 2.0 in Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 en Windows Server 2016. De oplossing biedt ook ondersteuning voor de bewaking van AD FS-proxy- of webtoepassingsproxyservers die verificatie-ondersteuning bieden voor extranettoegang. De Health-agent kan snel en eenvoudig worden geïnstalleerd, waarna u met Azure AD Connect Health voor AD FS beschikt over een set hoofdfuncties:

Belangrijkste voordelen en best practices:

|Belangrijkste voordelen|Beste praktijken|
|-----|-----|
|Verbeterde beveiliging|[Trends voor vergrendeling van extranet](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Rapport over mislukte aanmeldingen](how-to-connect-health-adfs-risky-ip.md)</br>[In privacy compliant](reference-connect-health-user-privacy.md)|
|Ontvang een waarschuwing over [alle kritieke ADFS-systeemproblemen](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Serverconfiguratie en -beschikbaarheid</br>[Prestaties en connectiviteit](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Regelmatig onderhoud|
|Eenvoudig te implementeren en te beheren|[Installatie van snelle agent](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)</br>Agent automatisch upgraden naar meest recente versie</br>Gegevens binnen enkele minuten beschikbaar in de portal|
Uitgebreide [gebruiksgegevens](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|Meest gebruikte toepassingen</br>Netwerklocaties en TCP-verbinding</br>Tokenaanvragen per server|
|Goede gebruikerservaring|Dashboardstijl van Azure-portal</br>[Waarschuwingen per mail](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>Licentievereisten voor het gebruik van Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Licentievereisten voor het gebruik van Azure AD Connect-status
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Volgende stappen

- [Hardware en vereisten](how-to-connect-install-prerequisites.md) 
- [Snelle instellingen](how-to-connect-install-express.md)
- [Aangepaste instellingen](how-to-connect-install-custom.md)
- [Azure AD Connect Health-agents installeren](how-to-connect-health-agent-install.md) 
