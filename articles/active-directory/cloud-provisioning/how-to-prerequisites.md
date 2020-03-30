---
title: Voorwaarden voor Azure AD Connect cloud provisioning in Azure AD
description: In dit artikel worden de vereisten en hardwarevereisten beschreven die u nodig hebt voor cloudprovisioning.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332087"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Voorwaarden voor Azure AD Connect-cloudinrichting
In dit artikel vindt u richtlijnen voor het kiezen en gebruiken van Azure Active Directory (Azure AD) Connect cloudprovisioning als uw identiteitsoplossing.



## <a name="cloud-provisioning-agent-requirements"></a>Vereisten voor cloudprovisioning-agent
U hebt het volgende nodig om Azure AD Connect-cloudprovisioning te gebruiken:
    
- Een algemeen beheerdersaccount voor uw Azure AD-tenant die geen gastgebruiker is.
- Een on-premises server voor de inrichtingsagent met Windows 2012 R2 of hoger.
- On-premises firewallconfiguraties.

>[!NOTE]
>De inrichtingsagent kan momenteel alleen op Engelstalige servers worden geïnstalleerd. Het installeren van een Engels taalpakket op een niet-Engelse server is geen geldige tijdelijke oplossing en zal resulteren in de agent niet te installeren. 

De rest van het document biedt stapsgewijze instructies voor deze vereisten.

### <a name="in-the-azure-active-directory-admin-center"></a>In het Azure Active Directory-beheercentrum

1. Maak een algemeen beheerdersaccount voor de cloud op uw Azure AD-tenant. Op deze manier u de configuratie van uw tenant beheren als uw on-premises services mislukken of niet meer beschikbaar zijn. Meer informatie over het [toevoegen van een wereldwijd beheerdersaccount voor alleen cloud](../active-directory-users-create-azure-portal.md). Het voltooien van deze stap is essentieel om ervoor te zorgen dat u niet buitengesloten van uw huurder.
1. Voeg een of meer [aangepaste domeinnamen](../active-directory-domains-add-azure-portal.md) toe aan uw Azure AD-tenant. Uw gebruikers kunnen zich aanmelden met een van deze domeinnamen.

### <a name="in-your-directory-in-active-directory"></a>In uw map in Active Directory

Voer het [gereedschap IdFix uit](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) om de directorykenmerken voor te bereiden op synchronisatie.

### <a name="in-your-on-premises-environment"></a>In uw on-premises omgeving

1. Identificeer een hostserver met domeinlid met Windows Server 2012 R2 of hoger met een minimum van 4 GB RAM en .NET 4.7.1+ runtime.

1. Als er een firewall is tussen uw servers en Azure AD, configureert u de volgende items:
   - Controleer of agents *uitgaande* aanvragen kunnen indienen bij Azure AD via de volgende poorten:

        | Poortnummer | Hoe dat wordt gebruikt |
        | --- | --- |
        | **80** | Downloadt de certificaatintrekkingslijsten (CRL's) terwijl het TLS/SSL-certificaat wordt valideren.  |
        | **443** | Verwerkt alle uitgaande communicatie met de service. |
        | **8080** (optioneel) | Agenten melden hun status elke 10 minuten via poort 8080, als poort 443 niet beschikbaar is. Deze status wordt weergegeven in de Azure AD-portal. |
     
   - Als uw firewall regels afdwingt volgens de oorspronkelijke gebruikers, opent u deze poorten voor verkeer van Windows-services die als netwerkservice worden uitgevoerd.
   - Als u met uw firewall of proxy veilige achtervoegsels opgeven, voegt u verbindingen toe aan \*.msappproxy.net en \*.servicebus.windows.net. Zo niet, geef dan toegang tot de [IP-bereiken van Azure-datacenters,](https://www.microsoft.com/download/details.aspx?id=41653)die wekelijks worden bijgewerkt.
   - Uw agenten hebben toegang nodig tot login.windows.net en login.microsoftonline.com voor de eerste registratie. Open uw firewall ook voor die URL's.
   - Blokkeer de volgende URL's voor certificaatvalidatie: mscrl.microsoft.com:80,\.crl.microsoft.com:80, ocsp.msocsp.com:80 en www microsoft.com:80. Deze URL's worden gebruikt voor certificaatvalidatie met andere Microsoft-producten, dus mogelijk hebt u deze URL's al gedeblokkeerd.

### <a name="verify-the-port"></a>De poort verifiëren
Gebruik de volgende URL om te controleren of Azure luistert op poort 443 en of uw agent ermee kan communiceren:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Deze test controleert of uw agents kunnen communiceren met Azure via poort 443. Open een browser en ga naar de vorige URL vanaf de server waar de agent is geïnstalleerd.

![Verificatie van de bereikbaarheid van de poort](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Aanvullende eisen
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-vereisten

>[!NOTE]
>Transport Layer Security (TLS) is een protocol dat zorgt voor veilige communicatie. Het wijzigen van de TLS-instellingen heeft invloed op het hele forest. Zie [Bijwerken om TLS 1.1 en TLS 1.2 in te schakelen als standaardbeveiligde protocollen in WinHTTP in Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)voor meer informatie.

De Windows-server die de Azure AD Connect-cloudinrichtingsagent host, moet TLS 1.2 hebben ingeschakeld voordat u deze installeert.

Voer deze stappen uit om TLS 1.2 in te schakelen.

1. Stel de volgende registersleutels in:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Start de server opnieuw op.


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)

