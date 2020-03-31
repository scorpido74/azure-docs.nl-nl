---
title: Veelgestelde vragen over Azure AD Domain Services | Microsoft Documenten
description: Lees en begrijp enkele van de veelgestelde vragen over configuratie, beheer en beschikbaarheid voor Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: a57826c79babded6e616548879a5ec0c223307d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946440"
---
# <a name="frequently-asked-questions-faqs"></a>Veelgestelde vragen (veelgestelde vragen)

Op deze pagina worden veelgestelde vragen beantwoord over Azure Active Directory Domain Services.

## <a name="configuration"></a>Configuratie

* [Kan ik meerdere beheerde domeinen maken voor één Azure AD-map?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Kan ik Azure AD Domain Services inschakelen in een virtueel netwerk van Classic?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Kan ik Azure AD Domain Services inschakelen in een virtueel Azure Resource Manager-netwerk?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Kan ik mijn bestaande beheerde domein migreren van een klassiek virtueel netwerk naar een virtueel netwerk van Resource Manager?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Kan ik Azure AD Domain Services inschakelen in een Azure CSP-abonnement (Cloud Solution Provider) ?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Kan ik Azure AD Domain Services inschakelen in een gefedereerde Azure AD-map? Ik synchroniseer wachtwoordhashes niet met Azure AD. Kan ik Azure AD Domain Services inschakelen voor deze directory?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Kan ik Azure AD Domain Services beschikbaar maken in meerdere virtuele netwerken binnen mijn abonnement?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Kan ik Azure AD Domain Services inschakelen met PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Kan ik Azure AD Domain Services inschakelen met behulp van een Resource Manager-sjabloon?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Kan ik domeincontrollers toevoegen aan een beheerd Azure AD Domain Services-domein?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Kunnen gastgebruikers die zijn uitgenodigd voor mijn directory Azure AD Domain Services gebruiken?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Kan ik een bestaand beheerd Azure AD Domain Services-domein verplaatsen naar een ander abonnement, resourcegroep, regio of virtueel netwerk?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Bevat Azure AD Domain Services opties voor hoge beschikbaarheid?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kan ik meerdere beheerde domeinen maken voor één Azure AD-map?
Nee. U slechts één beheerd domein maken dat wordt onderhouden door Azure AD Domain Services voor één Azure AD-map.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Kan ik Azure AD Domain Services inschakelen in een virtueel netwerk van Classic?
Klassieke virtuele netwerken worden niet ondersteund voor nieuwe implementaties. Bestaande beheerde domeinen die zijn geïmplementeerd in virtuele klassieke netwerken blijven worden ondersteund totdat ze op 1 maart 2023 met pensioen zijn. Voor bestaande implementaties u [Azure AD Domain Services migreren van het virtuele netwerkmodel Classic naar Resource Manager](migrate-from-classic-vnet.md).

Zie voor meer informatie het [officiële afschrijvingsbericht](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kan ik Azure AD Domain Services inschakelen in een virtueel Azure Resource Manager-netwerk?
Ja. Azure AD Domain Services kunnen worden ingeschakeld in een virtueel Azure Resource Manager-netwerk. Klassieke Virtuele Azure-netwerken zijn niet meer beschikbaar wanneer u een beheerd domein maakt.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kan ik mijn bestaande beheerde domein migreren van een virtueel netwerk van Classic naar een virtueel netwerk van Resource Manager?
Ja. Zie [Azure AD Domain Services migreren van het virtuele netwerkmodel Classic naar Resource Manager](migrate-from-classic-vnet.md)voor meer informatie.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kan ik Azure AD Domain Services inschakelen in een Azure CSP-abonnement (Cloud Solution Provider) ?
Ja. Zie voor meer informatie hoe u [Azure AD Domain Services in Azure CSP-abonnementen inschakelt.](csp.md)

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kan ik Azure AD Domain Services inschakelen in een gefedereerde Azure AD-map? Ik synchroniseer wachtwoordhashes niet met Azure AD. Kan ik Azure AD Domain Services inschakelen voor deze directory?
Nee. Om gebruikers te verifiëren via NTLM of Kerberos, heeft Azure AD Domain Services toegang nodig tot de wachtwoordhashes van gebruikersaccounts. In een federatieve map worden wachtwoordhashes niet opgeslagen in de Azure AD-map. Azure AD Domain Services werkt daarom niet met dergelijke Azure AD-mappen.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kan ik Azure AD Domain Services beschikbaar maken in meerdere virtuele netwerken binnen mijn abonnement?
De service zelf ondersteunt dit scenario niet direct. Uw beheerde domein is slechts in één virtueel netwerk tegelijk beschikbaar. U echter de connectiviteit tussen meerdere virtuele netwerken configureren om Azure AD Domain Services bloot te stellen aan andere virtuele netwerken. Zie voor meer informatie [hoe u virtuele netwerken in Azure verbinden met VPN-gateways](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) of [virtuele netwerkpeering.](../virtual-network/virtual-network-peering-overview.md)

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kan ik Azure AD Domain Services inschakelen met PowerShell?
Ja. Zie voor meer informatie [hoe u Azure AD Domain Services inschakelt met PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kan ik Azure AD Domain Services inschakelen met behulp van een Resource Manager-sjabloon?
Ja, u een beheerd Azure AD Domain Services-domein maken met behulp van een resourcemanagersjabloon. Er moet een serviceprincipal en Azure AD-groep voor beheer worden gemaakt met behulp van de Azure-portal of Azure PowerShell voordat de sjabloon wordt geïmplementeerd. Zie [Een door Azure AD DS beheerd domein maken met behulp van een Azure Resource Manager-sjabloon](template-create-instance.md)voor meer informatie. Wanneer u een beheerd Azure AD Domain Services-domein maakt in de Azure-portal, is er ook een optie om de sjabloon te exporteren voor gebruik met extra implementaties.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kan ik domeincontrollers toevoegen aan een beheerd Azure AD Domain Services-domein?
Nee. Het domein van Azure AD Domain Services is een beheerd domein. U hoeft domeincontrollers voor dit domein niet in te richten, te configureren of anderszins te beheren. Deze beheeractiviteiten worden als service door Microsoft aangeboden. Daarom u geen extra domeincontrollers (lezen-schrijven of alleen-lezen) toevoegen voor het beheerde domein.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Kunnen gastgebruikers die zijn uitgenodigd voor mijn directory Azure AD Domain Services gebruiken?
Nee. Gastgebruikers die zijn uitgenodigd voor uw Azure AD-map met behulp van het [Azure AD B2B-uitnodigingsproces,](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) worden gesynchroniseerd met uw beheerde Azure AD Domain Services-domein. Wachtwoorden voor deze gebruikers worden echter niet opgeslagen in uw Azure AD-map. Azure AD Domain Services kan NTLM en Kerberos hashes voor deze gebruikers daarom niet synchroniseren in uw beheerde domein. Dergelijke gebruikers kunnen zich niet aanmelden of deelnemen aan computers in het beheerde domein.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Kan ik een bestaand beheerd Azure AD Domain Services-domein verplaatsen naar een ander abonnement, resourcegroep, regio of virtueel netwerk?
Nee. Nadat u een beheerd Azure AD Domain Services-domein hebt gemaakt, u de instantie niet verplaatsen naar een andere brongroep, virtueel netwerk, abonnement, enz. Zorg ervoor dat u het meest geschikte abonnement, resourcegroep, regio en virtueel netwerk selecteert wanneer u het Azure AD DS-exemplaar implementeert.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Bevat Azure AD Domain Services opties voor hoge beschikbaarheid?

Ja. Elk beheerd beheerd domein van Azure AD Domain Services bevat twee domeincontrollers. U beheert of maakt geen verbinding met deze domeincontrollers, ze maken deel uit van de beheerde service. Als u Azure AD Domain Services implementeert in een regio die beschikbaarheidszones ondersteunt, worden de domeincontrollers verdeeld over zones. In regio's die beschikbaarheidszones niet ondersteunen, worden de domeincontrollers verdeeld over beschikbaarheidssets. U hebt geen configuratieopties of beheercontrole over deze distributie. Zie [Beschikbaarheidsopties voor virtuele machines in Azure voor](../virtual-machines/windows/availability.md)meer informatie.

## <a name="administration-and-operations"></a>Administratie en werkzaamheden

* [Kan ik verbinding maken met de domeincontroller voor mijn beheerde domein met Extern bureaublad?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Ik heb Azure AD Domain Services ingeschakeld. Welk gebruikersaccount gebruik ik om lid te worden van machines voor het domein join en dit domein?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Heb ik domeinbeheerdersbevoegdheden voor het beheerde domein dat wordt geleverd door Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan ik groepslidmaatschappen wijzigen met LDAP of andere AD-beheertools op beheerde domeinen?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Hoe lang duurt het voordat wijzigingen die ik in mijn Azure AD-map aanbreng, zichtbaar zijn in mijn beheerde domein?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Kan ik het schema van het beheerde domein van Azure AD Domain Services uitbreiden?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan ik DNS-records wijzigen of toevoegen in mijn beheerde domein?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Wat is het wachtwoordlevenslangbeleid op een beheerd domein?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Biedt Azure AD Domain Services bescherming voor ad-accountvergrendeling?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kan ik verbinding maken met de domeincontroller voor mijn beheerde domein met Extern bureaublad?
Nee. U hebt geen machtigingen om verbinding te maken met domeincontrollers voor het beheerde domein met Extern bureaublad. Leden van de groep *AAD DC-beheerders* kunnen het beheerde domein beheren met ad-beheertools zoals het Active Directory Administration Center (ADAC) of AD PowerShell. Deze hulpprogramma's worden geïnstalleerd met de functie *Hulpprogramma's voor extern serverbeheer* op een Windows-server die is verbonden met het beheerde domein. Zie Een beheer-VM maken voor meer informatie [om een beheerd Azure AD Domain Services-domein te configureren en te beheren.](tutorial-create-management-vm.md)

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Ik heb Azure AD Domain Services ingeschakeld. Welk gebruikersaccount gebruik ik om lid te worden van machines voor het domein join en dit domein?
Elk gebruikersaccount dat deel uitmaakt van het beheerde Azure AD DS-domein, kan lid worden van een VM. Leden van de groep *AAD DC-beheerders* krijgen toegang tot extern bureaublad tot machines die zijn verbonden met het beheerde domein.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Heb ik domeinbeheerdersbevoegdheden voor het beheerde domein dat wordt geleverd door Azure AD Domain Services?
Nee. U krijgt geen beheerdersbevoegdheden op het beheerde domein. *Domeinbeheerders-* en *Enterprise Administrator-bevoegdheden* zijn niet beschikbaar voor u om binnen het domein te gebruiken. Leden van de domeinbeheerder of bedrijfsbeheerdersgroepen in uw on-premises Active Directory krijgen ook geen bevoegdheden voor domein- en bedrijfsbeheerders op het beheerde domein.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kan ik groepslidmaatschappen wijzigen met LDAP of andere AD-beheertools op beheerde domeinen?
Gebruikers en groepen die zijn gesynchroniseerd van Azure Active Directory naar Azure AD Domain Services kunnen niet worden gewijzigd omdat hun oorsprong Azure Active Directory is. Elke gebruiker of groep die afkomstig is uit het beheerde domein kan worden gewijzigd.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Hoe lang duurt het voordat wijzigingen die ik in mijn Azure AD-map aanbreng, zichtbaar zijn in mijn beheerde domein?
Wijzigingen die zijn aangebracht in uw Azure AD-map met behulp van de Azure AD UI of PowerShell worden automatisch gesynchroniseerd met uw beheerde domein. Dit synchronisatieproces wordt op de achtergrond uitgevoerd. Er is geen gedefinieerde periode voor deze synchronisatie om alle objectwijzigingen te voltooien.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kan ik het schema van het beheerde domein van Azure AD Domain Services uitbreiden?
Nee. Het schema wordt beheerd door Microsoft voor het beheerde domein. Schema-extensies worden niet ondersteund door Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kan ik DNS-records wijzigen of toevoegen in mijn beheerde domein?
Ja. Leden van de groep *AAD DC-beheerders* krijgen *DNS Administrator-bevoegdheden* om DNS-records in het beheerde domein te wijzigen. Deze gebruikers kunnen de DNS Manager-console gebruiken op een machine waarop Windows Server is verbonden met het beheerde domein om DNS te beheren. Als u de DNS-beheerconsole wilt gebruiken, installeert u *DNS-serverhulpprogramma's,* die deel uitmaken van de optionele functie Extern *serverbeheerbeheer* op de server. Zie [DNS beheren in een beheerd Azure AD Domain Services-domein](manage-dns.md)voor meer informatie.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Wat is het wachtwoordlevenslangbeleid op een beheerd domein?
De standaardwachtwoordlevensduur op een beheerd Azure AD Domain Services-domein is 90 dagen. Deze wachtwoordlevensduur wordt niet gesynchroniseerd met de wachtwoordlevensduur die is geconfigureerd in Azure AD. Daarom u een situatie hebben waarin de wachtwoorden van gebruikers verlopen in uw beheerde domein, maar nog steeds geldig zijn in Azure AD. In dergelijke scenario's moeten gebruikers hun wachtwoord wijzigen in Azure AD en wordt het nieuwe wachtwoord gesynchroniseerd met uw beheerde domein. Bovendien worden de *wachtwoord-niet-verlopen* en worden de kenmerken van het wachtwoord-niet-verlopen en *de gebruiker-must-change-password-at-next-logon-kenmerken* voor gebruikersaccounts niet gesynchroniseerd met uw beheerde domein.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Biedt Azure AD Domain Services bescherming voor ad-accountvergrendeling?
Ja. Vijf ongeldige wachtwoordpogingen binnen 2 minuten op het beheerde domein zorgen ervoor dat een gebruikersaccount gedurende 30 minuten wordt vergrendeld. Na 30 minuten wordt het gebruikersaccount automatisch ontgrendeld. Ongeldige wachtwoordpogingen in het beheerde domein sluiten het gebruikersaccount in Azure AD niet uit. Het gebruikersaccount is alleen vergrendeld binnen uw beheerde Azure AD Domain Services-domein. Zie [Beleid voor wachtwoord- en accountuitsluiting seis van beheerde domeinen](password-policy.md)voor meer informatie.

## <a name="billing-and-availability"></a>Facturering en beschikbaarheid

* [Is Azure AD Domain Services een betaalde service?](#is-azure-ad-domain-services-a-paid-service)
* [Is er een gratis proefperiode voor de dienst?](#is-there-a-free-trial-for-the-service)
* [Kan ik een beheerd Azure AD Domain Services-domein onderbreken?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan ik Azure AD Domain Services naar een andere regio failoveren voor een DR-gebeurtenis?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan ik Azure AD Domain Services krijgen als onderdeel van Enterprise Mobility Suite (EMS)? Heb ik Azure AD Premium nodig om Azure AD Domain Services te gebruiken?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [In welke Azure-regio's is de service beschikbaar?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Is Azure AD Domain Services een betaalde service?
Ja. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/active-directory-ds/) voor meer informatie.

### <a name="is-there-a-free-trial-for-the-service"></a>Is er een gratis proefperiode voor de dienst?
Azure AD Domain Services is opgenomen in de gratis proefversie voor Azure. U zich aanmelden voor een [gratis proefversie van Azure van een maand.](https://azure.microsoft.com/pricing/free-trial/)

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kan ik een beheerd Azure AD Domain Services-domein onderbreken?
Nee. Nadat u een beheerd Azure AD Domain Services-domein hebt ingeschakeld, is de service beschikbaar binnen het geselecteerde virtuele netwerk totdat u het beheerde domein verwijdert. Er is geen manier om de service te pauzeren. Facturering gaat op uurbasis door totdat u het beheerde domein verwijdert.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Kan ik Azure AD Domain Services naar een andere regio failoveren voor een DR-gebeurtenis?
Nee. Azure AD Domain Services biedt momenteel geen georedundant implementatiemodel. Het is beperkt tot één virtueel netwerk in een Azure-regio. Als u meerdere Azure-regio's wilt gebruiken, moet u uw Active Directory-domeincontrollers uitvoeren op Azure IaaS VM's. Zie [Uw on-premises Active Directory-domein uitbreiden naar Azure voor](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)architectuurrichtlijnen.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kan ik Azure AD Domain Services krijgen als onderdeel van Enterprise Mobility Suite (EMS)? Heb ik Azure AD Premium nodig om Azure AD Domain Services te gebruiken?
Nee. Azure AD Domain Services is een Azure-service voor betalen per gebruik en maakt geen deel uit van EMS. Azure AD Domain Services kunnen worden gebruikt met alle edities van Azure AD (Gratis en Premium). U wordt per uur gefactureerd, afhankelijk van het gebruik.

### <a name="what-azure-regions-is-the-service-available-in"></a>In welke Azure-regio's is de service beschikbaar?
Raadpleeg de pagina [Azure Services per regio](https://azure.microsoft.com/regions/#services/) om een lijst te zien met de Azure-regio's waar Azure AD Domain Services beschikbaar is.

## <a name="troubleshooting"></a>Problemen oplossen

Raadpleeg de [handleiding voor probleemoplossing](troubleshoot.md) voor oplossingen voor veelvoorkomende problemen bij het configureren of beheren van Azure AD Domain Services.

## <a name="next-steps"></a>Volgende stappen

Zie Wat is Azure Active [Directory Domain Services voor](overview.md)meer informatie over Azure AD Domain Services.

Zie [Een Azure Active Directory Domain Services-exemplaar maken en configureren](tutorial-create-instance.md)om aan de slag te gaan.
