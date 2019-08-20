---
title: Veelgestelde vragen-Azure Active Directory Domain Services | Microsoft Docs
description: Veelgestelde vragen over Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 509bd472fe5f930ac830b8b4016129f8acb67324
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612884"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Veelgestelde vragen
Op deze pagina vindt u antwoorden op veelgestelde vragen over de Azure Active Directory Domain Services. Blijf op de hoogte met het controleren op updates.

## <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen
Raadpleeg de [hand leiding](troubleshoot.md) voor het oplossen van problemen met oplossingen voor veelvoorkomende problemen met het configureren of beheren van Azure AD Domain Services.

## <a name="configuration"></a>Configuratie
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kan ik meerdere beheerde domeinen maken voor één Azure AD-adres lijst?
Nee. U kunt slechts één beheerd domein maken dat door Azure AD Domain Services wordt onderhouden voor één Azure AD-adres lijst.  

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Kan ik Azure AD Domain Services inschakelen in een klassiek virtueel netwerk?
Klassieke virtuele netwerken worden niet ondersteund voor nieuwe implementaties. Bestaande beheerde domeinen die in klassieke virtuele netwerken zijn geïmplementeerd, worden nog steeds ondersteund.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kan ik Azure AD Domain Services inschakelen in een Azure Resource Manager virtueel netwerk?
Ja. Azure AD Domain Services kan worden ingeschakeld in een Azure Resource Manager virtueel netwerk. Klassieke virtuele netwerken van Azure worden niet meer ondersteund voor het maken van nieuwe beheerde domeinen.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kan ik mijn bestaande beheerde domein migreren van een klassiek virtueel netwerk naar een virtueel netwerk van Resource Manager?
Momenteel niet. Micro soft levert in de toekomst een mechanisme voor het migreren van uw bestaande beheerde domein vanuit een klassiek virtueel netwerk naar een virtueel netwerk van Resource Manager.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kan ik Azure AD Domain Services in een Azure CSP-abonnement (Cloud Solution Provider) inschakelen?
Ja. Bekijk hoe u Azure AD Domain Services kunt inschakelen [in azure CSP-abonnementen](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kan ik Azure AD Domain Services inschakelen in een federatieve Azure AD-adres lijst? Ik Synchroniseer geen wacht woord-hashes naar Azure AD. Kan ik Azure AD Domain Services voor deze map inschakelen?
Nee. Azure AD Domain Services moet toegang hebben tot de wacht woord-hashes van gebruikers accounts om gebruikers te verifiëren via NTLM of Kerberos. In een federatieve map worden wacht woord-hashes niet opgeslagen in de Azure AD-adres lijst. Daarom werkt Azure AD Domain Services niet met deze Azure AD-mappen.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kan ik Azure AD Domain Services beschikbaar maken in meerdere virtuele netwerken binnen mijn abonnement?
De service zelf biedt geen rechtstreekse ondersteuning voor dit scenario. Uw beheerde domein is slechts in één virtueel netwerk tegelijk beschikbaar. U kunt echter wel de connectiviteit tussen meerdere virtuele netwerken configureren om Azure AD Domain Services te bieden aan andere virtuele netwerken. Bekijk hoe u [virtuele netwerken in azure kunt verbinden](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kan ik Azure AD Domain Services inschakelen met behulp van Power shell?
Ja. Zie [Azure AD Domain Services inschakelen met behulp van Power shell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kan ik Azure AD Domain Services inschakelen met een resource manager-sjabloon?
Nee, het is momenteel niet mogelijk om Azure AD Domain Services in te scha kelen met behulp van een sjabloon. In plaats daarvan gebruikt u Power shell [om Azure AD Domain Services in te scha kelen met behulp van Power shell](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kan ik domein controllers toevoegen aan een Azure AD Domain Services beheerd domein?
Nee. Het door Azure AD Domain Services verschafte domein is een beheerd domein. U hoeft geen domein controllers in te richten, configureren of anderszins te beheren voor dit domein: deze beheer activiteiten worden geleverd als een service van micro soft. Daarom kunt u geen extra domein controllers (lezen/schrijven of alleen-lezen) toevoegen voor het beheerde domein.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Kunnen gast gebruikers die worden uitgenodigd voor mijn Directory Azure AD Domain Services gebruiken?
Nee. Gast gebruikers die zijn uitgenodigd voor uw Azure AD-adres lijst met het [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) -invite-proces, worden gesynchroniseerd met uw Azure AD Domain Services beheerde domein. Wacht woorden voor deze gebruikers worden echter niet opgeslagen in uw Azure AD-adres lijst. Daarom heeft Azure AD Domain Services geen manier om NTLM-en Kerberos-hashes te synchroniseren voor deze gebruikers in uw beheerde domein. Als gevolg hiervan kunnen dergelijke gebruikers zich niet aanmelden bij het beheerde domein of computers toevoegen aan het beheerde domein.

## <a name="administration-and-operations"></a>Beheer en bewerkingen
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kan ik verbinding maken met de domein controller voor mijn beheerde domein met behulp van Extern bureaublad?
Nee. U bent niet gemachtigd om verbinding te maken met domein controllers voor het beheerde domein via Extern bureaublad. Leden van de groep AAD DC Administrators kunnen het beheerde domein beheren met AD-beheer Programma's zoals het Active Directory Administration Center (ADAC) of AD Power shell. Deze hulpprogram ma's worden geïnstalleerd met behulp van de functie ' Remote Server Administration Tools ' op een Windows-Server die is gekoppeld aan het beheerde domein.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Ik heb Azure AD Domain Services ingeschakeld. Welk gebruikers account moet ik gebruiken om computers toe te voegen aan dit domein?
Leden van de beheer groep ' AAD DC-Administrators ' kunnen computers toevoegen aan een domein. Daarnaast krijgen leden van deze groep extern bureau blad toegang tot computers die zijn toegevoegd aan het domein.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Heb ik domein beheerders rechten voor het beheerde domein dat wordt meegeleverd door Azure AD Domain Services?
Nee. U hebt geen beheerders bevoegdheden voor het beheerde domein. De bevoegdheden domein beheerder en ondernemings Administrator zijn niet beschikbaar voor gebruik binnen het domein. Leden van de groep domein Administrators of ondernemings Administrators in uw on-premises Active Directory zijn ook geen machtigingen voor domein/ondernemings Administrator verleend voor het beheerde domein.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kan ik groepslid maatschappen wijzigen met behulp van LDAP of andere AD-beheer Programma's in beheerde domeinen?
Nee. Groepslid maatschappen kunnen niet worden gewijzigd in domeinen die worden onderhouden door Azure AD Domain Services. Hetzelfde geldt voor gebruikers kenmerken. U kunt echter groepslid maatschappen of gebruikers kenmerken in azure AD of op uw on-premises domein wijzigen. Dergelijke wijzigingen worden automatisch gesynchroniseerd naar Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Hoe lang duurt het voor de wijzigingen die ik in mijn Azure AD-adres lijst aanbrengt, worden weer gegeven in mijn beheerde domein?
Wijzigingen die zijn aangebracht in uw Azure AD-adres lijst met behulp van de Azure AD-gebruikers interface of Power shell, worden gesynchroniseerd met uw beheerde domein. Dit synchronisatie proces wordt op de achtergrond uitgevoerd. Zodra de initiële synchronisatie is voltooid, duurt het ongeveer 20 minuten voordat wijzigingen die zijn aangebracht in azure AD, worden weer gegeven in uw beheerde domein.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kan ik het schema uitbreiden van het beheerde domein dat wordt meegeleverd met Azure AD Domain Services?
Nee. Het schema wordt door micro soft beheerd voor het beheerde domein. Schema-uitbrei dingen worden niet ondersteund door Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kan ik DNS-records in mijn beheerde domein wijzigen of toevoegen?
Ja. Leden van de groep ' AAD DC Administrators ' worden de bevoegdheden van de DNS-beheerder toegekend om DNS-records in het beheerde domein te wijzigen. Ze kunnen de DNS-beheer console gebruiken op een computer met Windows Server die is gekoppeld aan het beheerde domein om DNS te beheren. Als u de DNS-beheer console wilt gebruiken, installeert u ' DNS-server Hulpprogramma's ', die deel uitmaakt van de optionele functie ' Remote Server Administration Tools ' op de server. Meer informatie over [hulpprogram ma's voor beheer, bewaking en probleem oplossing van DNS](https://technet.microsoft.com/library/cc753579.aspx) is beschikbaar op TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Wat is het beleid voor de levens duur van wacht woorden op een beheerd domein?
De standaard levensduur van het wacht woord voor een Azure AD Domain Services beheerd domein is 90 dagen. De levens duur van het wacht woord is niet gesynchroniseerd met de levens duur van het wacht woord dat is geconfigureerd in azure AD. Daarom hebt u mogelijk een situatie waarin gebruikers wachtwoorden verlopen in uw beheerde domein, maar nog steeds geldig zijn in azure AD. In dergelijke scenario's moeten gebruikers hun wacht woord wijzigen in azure AD en wordt het nieuwe wacht woord gesynchroniseerd met uw beheerde domein. Daarnaast worden de kenmerken ' wacht woord-niet verlopen ' en ' gebruiker-moet-wijzigen-bij volgende aanmelding ' voor gebruikers accounts niet gesynchroniseerd met uw beheerde domein.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Biedt Azure AD Domain Services AD-account vergrendelings beveiliging?
Ja. Vijf ongeldige wachtwoord pogingen binnen twee minuten op het beheerde domein zorgen ervoor dat een gebruikers account 30 minuten wordt vergrendeld. Na 30 minuten wordt het gebruikers account automatisch ontgrendeld. Bij ongeldige wachtwoord pogingen op het beheerde domein wordt het gebruikers account in azure AD niet vergrendeld. Het gebruikers account is alleen vergrendeld binnen uw Azure AD Domain Services beheerde domein.

## <a name="billing-and-availability"></a>Facturering en beschik baarheid
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Is er Azure AD Domain Services een betaalde service?
Ja. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/active-directory-ds/) voor meer informatie.

### <a name="is-there-a-free-trial-for-the-service"></a>Is er een gratis proef versie voor de service?
Deze service is opgenomen in de gratis proef versie voor Azure. U kunt zich registreren voor een [gratis proef versie van Azure voor één maand](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kan ik een Azure AD Domain Services beheerd domein onderbreken? 
Nee. Zodra u een Azure AD Domain Services beheerd domein hebt ingeschakeld, is de service beschikbaar in het geselecteerde virtuele netwerk totdat u het beheerde domein uitschakelt of verwijdert. Het is niet mogelijk om de service te onderbreken. De facturering blijft elk uur in beslag totdat u het beheerde domein verwijdert.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Kan ik een failover-Azure AD Domain Services naar een andere regio voor een DR-gebeurtenis?
Nee.  Azure AD Domain Services biedt momenteel geen geo-redundant implementatie model. Het is beperkt tot één virtueel netwerk in een Azure-regio. Als u meerdere Azure-regio's wilt gebruiken, moet u uw Active Directory-domein-controllers uitvoeren op virtuele Azure IaaS-machines.  Architectuur richtlijnen vindt u [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kan ik Azure AD Domain Services krijgen als onderdeel van de Enter prise Mobility Suite (EMS)? Heb ik Azure AD Premium nodig om Azure AD Domain Services te gebruiken?
Nee. Azure AD Domain Services is een Azure-service voor betalen naar gebruik en maakt geen deel uit van EMS. Azure AD Domain Services kunnen worden gebruikt in combi natie met alle versies van Azure AD (gratis en Premium). U wordt gefactureerd per uur, afhankelijk van het gebruik.

### <a name="what-azure-regions-is-the-service-available-in"></a>In welke Azure-regio's is de service beschikbaar?
Raadpleeg de pagina [Azure-Services per regio](https://azure.microsoft.com/regions/#services/) voor een overzicht van de Azure-regio's waar Azure AD Domain Services beschikbaar is.
