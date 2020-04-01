---
title: Externe VM-toegang beveiligen in Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het beveiligen van externe toegang tot VM's met behulp van Network Policy Server (NPS) en Azure Multi-Factor Authentication met een Implementatie van Extern bureaublad-services in een beheerd beheerd domein van Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: f2a222c6a382fa2c316211e293547780a8778177
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423143"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Externe toegang tot virtuele machines beveiligen in Azure Active Directory Domain Services

Als u externe toegang wilt beveiligen tot virtuele machines (VM's) die worden uitgevoerd in een azure active directory domain services (Azure AD DS) beheerd domein, u RdS (Remote Desktop Services) en Network Policy Server (NPS) gebruiken. Azure AD DS verifieert gebruikers wanneer ze toegang aanvragen via de RDS-omgeving. Voor verbeterde beveiliging u Azure Multi-Factor Authentication integreren om een extra verificatieprompt te geven tijdens aanmeldingsgebeurtenissen. Azure Multi-Factor Authentication maakt gebruik van een extensie voor NPS om deze functie te bieden.

> [!IMPORTANT]
> De aanbevolen manier om veilig verbinding te maken met uw VM's in een door Azure AD DS beheerd domein is het gebruik van Azure Bastion, een volledig door platforms beheerde PaaS-service die u inuw virtuele netwerk indient. Een bastionhost biedt veilige en naadloze RDP-connectiviteit (Remote Desktop Protocol) rechtstreeks in de Azure-portal via SSL. Wanneer u verbinding maakt via een bastionhost, hebben uw VM's geen openbaar IP-adres nodig en hoeft u geen netwerkbeveiligingsgroepen te gebruiken om toegang tot RDP op TCP-poort 3389 bloot te stellen.
>
> We raden u ten zeerste aan Azure Bastion te gebruiken in alle regio's waar het wordt ondersteund. Volg in regio's zonder azure bastion-beschikbaarheid de stappen die in dit artikel worden beschreven totdat Azure Bastion beschikbaar is. Zorg ervoor dat openbare IP-adressen worden toegewezen aan VM's die zijn gekoppeld aan Azure AD DS, waar al het inkomende RDP-verkeer is toegestaan.
>
> Zie Wat is Azure Bastion voor meer [informatie?][bastion-overview]

In dit artikel ziet u hoe u RDS configureert in Azure AD DS en optioneel de AZURE Multi-Factor Authentication NPS-extensie gebruikt.

![Extern bureaublad-services (RDS) overzicht](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Vereisten

Om dit artikel te voltooien, hebt u de volgende bronnen nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Maak en configureer indien nodig [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance].
* Een *subnet voor workloads* dat is gemaakt in uw virtuele Azure Directory Domain Services-netwerk.
    * Configureer indien nodig [virtuele netwerken voor een beheerd Azure Directory Domain Services-domein][configure-azureadds-vnet].
* Een gebruikersaccount dat lid is van de Azure *AD DC-beheerdersgroep* in uw Azure AD-tenant.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>De extern bureaublad-omgeving implementeren en configureren

Maak minimaal twee Azure VM's met Windows Server 2016 of Windows Server 2019 om aan de slag te gaan. Voor redundantie en hoge beschikbaarheid van uw Extern bureaublad-omgeving (RD) u later extra hosts toevoegen en laden.

Een voorgestelde RDS-implementatie bevat de volgende twee VM's:

* *RDGVM01* - Voert de Extern bureaublad Connection Broker-server, Extern bureaublad-webtoegangsserver en Extern bureaublad-gatewayserver uit.
* *RDSHVM01* - Voert de Extern bureaublad-sessiehostserver uit.

Zorg ervoor dat VM's worden geïmplementeerd in *een* subnet van uw Azure AD DS-netwerk en sluit je vervolgens aan bij de VM's voor het beheerde Azure AD DS-domein. Zie voor meer informatie hoe u [een Windows Server VM maakt en lid maakt van een door Azure AD DS beheerd domein][tutorial-create-join-vm].

De implementatie van de Rd-omgeving bevat een aantal stappen. De bestaande rd-implementatiehandleiding kan worden gebruikt zonder specifieke wijzigingen die moeten worden gebruikt in een door Azure AD DS beheerd domein:

1. Meld u aan bij VM's die zijn gemaakt voor de RD-omgeving met een account dat deel uitmaakt van de azure *AD DC-beheerdersgroep,* zoals *contosoadmin*.
1. Als u RDS wilt maken en configureren, gebruikt u de bestaande [implementatiehandleiding voor extern bureaublad-omgeving][deploy-remote-desktop]. Verdeel de rd-servercomponenten naar wens over uw Azure VM's.
1. Als u toegang wilt bieden via een webbrowser, [stelt u de extern bureaublad-webclient in voor uw gebruikers.][rd-web-client]

Met RD geïmplementeerd in het beheerde Azure AD DS-domein, u de service beheren en gebruiken zoals u dat zou doen met een on-premises AD DS-domein.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>NPS en de Azure MFA NPS-extensie implementeren en configureren

Als u de beveiliging van de aanmeldingservaring van de gebruiker wilt verhogen, u de RD-omgeving optioneel integreren met Azure Multi-Factor Authentication. Met deze configuratie ontvangen gebruikers een extra prompt tijdens het aanmelden om hun identiteit te bevestigen.

Om deze mogelijkheid te bieden, wordt een extra Network Policy Server (NPS) in uw omgeving geïnstalleerd, samen met de Azure Multi-Factor Authentication NPS-extensie. Deze extensie integreert met Azure AD om de status van multi-factor authenticatieprompts op te vragen en terug te geven.

Gebruikers moeten zijn [geregistreerd om Azure Multi-Factor Authentication te gebruiken,][user-mfa-registration]waarvoor mogelijk aanvullende Azure AD-licenties nodig zijn.

Als u Azure Multi-Factor Authentication wilt integreren in uw Azure AD DS-extern bureaublad-omgeving, maakt u een NPS-server en installeert u de extensie:

1. Maak een extra Windows Server 2016- of 2019-vm, zoals *NPSVM01,* die is verbonden met een *subnet met workloads* in uw virtuele Azure AD DS-netwerk. Word lid van de VM naar het beheerde Azure AD DS-domein.
1. Meld u aan bij NPS VM als account dat deel uitmaakt van de azure *AD DC Administrators-groep,* zoals *contosoadmin*.
1. Selecteer vanuit **Serverbeheer** **Rollen en onderdelen toevoegen**en installeer vervolgens de rol *Netwerkbeleid en Toegangsservices.*
1. Gebruik het bestaande how-to-artikel om [de Azure MFA NPS-extensie][nps-extension]te installeren en te configureren.

Als de NPS-server en azure multi-factor authentication NPS-extensie zijn geïnstalleerd, voltooit u de volgende sectie om deze te configureren voor gebruik met de RD-omgeving.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>Extern bureaublad-gateway en Azure-multifactorverificatie integreren

Als u de AZURE Multi-Factor Authentication NPS-extensie wilt integreren, gebruikt u het bestaande artikel how-to om [uw Extern bureaublad-gateway-infrastructuur te integreren met behulp van de NPS-extensie (Network Policy Server) en Azure AD.][azure-mfa-nps-integration]

De volgende aanvullende configuratieopties zijn nodig om te integreren met een door Azure AD DS beheerd domein:

1. [Registreer de NPS-server][register-nps-ad]niet in Active Directory. Deze stap mislukt in een door Azure AD DS beheerd domein.
1. Schakel in [stap 4 het netwerkbeleid te configureren,][create-nps-policy]schakel ook het selectievakje in om **inbeleigenschappen van gebruikersaccounts**te negeren .
1. Als u Windows Server 2019 gebruikt voor de NPS-server en azure Multi-Factor Authentication NPS-extensie, voert u de volgende opdracht uit om het beveiligde kanaal bij te werken zodat de NPS-server correct kan communiceren:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Gebruikers wordt nu gevraagd om een extra verificatiefactor wanneer ze zich aanmelden, zoals een sms-bericht of prompt in de Microsoft Authenticator-app.

## <a name="next-steps"></a>Volgende stappen

Zie [Extern bureaublad-services - Hoge beschikbaarheid][rds-high-availability]voor meer informatie over het verbeteren van de tolerantie van uw implementatie.

Zie Hoe het werkt voor meer informatie over het beveiligen van de aanmelding van [gebruikers: Azure Multi-Factor Authentication][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
