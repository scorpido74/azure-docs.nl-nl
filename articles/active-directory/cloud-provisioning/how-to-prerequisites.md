---
title: Vereisten voor het inrichten van Azure AD Connect Cloud in azure AD
description: In dit onderwerp worden de vereisten en de hardwarevereisten voor de Cloud inrichting beschreven.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155edf72a60e079a609853e953e3cf66024cc83c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794248"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Vereisten voor het inrichten van Azure AD Connect Cloud
Dit onderwerp bevat richt lijnen voor het kiezen en gebruiken van Azure AD Connect Cloud inrichting als uw identiteits oplossing.



## <a name="cloud-provisioning-agent-requirements"></a>Vereisten voor de inrichtings agent voor Cloud
U hebt het volgende nodig voor het gebruik van Azure AD Connect Cloud inrichting:
    
- een globaal beheerders account voor uw Azure AD-Tenant
- een on-premises server voor de inrichtings agent met Windows 2012 R2 of hoger
- on-premises firewall configuraties

De rest van het document bevat stapsgewijze instructies voor deze vereiste onderdelen.

### <a name="in-the-azure-active-directory-admin-center"></a>In het Azure Active Directory-beheer centrum

1. Maak een alleen-Cloud account voor globale beheerders op uw Azure AD-Tenant. Op deze manier kunt u de configuratie van uw Tenant beheren als uw on-premises Services mislukken of niet meer beschikbaar zijn. Meer informatie over [het toevoegen van een globale beheerders account voor de Cloud](../active-directory-users-create-azure-portal.md). Het volt ooien van deze stap is van cruciaal belang om ervoor te zorgen dat de Tenant niet wordt vergrendeld.
2. Voeg een of meer [aangepaste domein namen](../active-directory-domains-add-azure-portal.md) toe aan uw Azure AD-Tenant. Uw gebruikers kunnen zich aanmelden met een van deze domein namen.

### <a name="in-your-on-premises-environment"></a>In uw on-premises omgeving

1. De identiteit van een hostserver die lid is van een domein en waarop Windows Server 2012 R2 of hoger wordt uitgevoerd met mini maal 4 GB RAM en .NET 4.7.1 + runtime 

2. Als er een firewall is tussen uw servers en Azure AD, moet u de volgende items configureren:
   - Zorg ervoor dat agenten *uitgaande* aanvragen kunnen indienen bij Azure AD via de volgende poorten:

     | Poortnummer | Hoe dat wordt gebruikt |
     | --- | --- |
     | **80** | Hiermee worden de certificaatintrekkingslijsten (Crl's) gedownload tijdens het valideren van het SSL-certificaat |
     | **443** | Hiermee wordt alle uitgaande communicatie met de service verwerkt |
     | **8080** (optioneel) | Agents rapporteren hun status elke tien minuten via poort 8080, als poort 443 niet beschikbaar is. Deze status wordt weer gegeven in de Azure AD-Portal. Poort 8080 wordt _niet_ gebruikt voor gebruikers aanmeldingen. |
     
     Als uw firewall regels afdwingt op basis van de oorspronkelijke gebruikers, opent u deze poorten voor verkeer van Windows-services die als een netwerk service worden uitgevoerd.
   - Als u met uw firewall of proxy veilige achtervoegsels kunt opgeven, voegt u verbindingen toe aan **\*. msappproxy.net** en **\*. servicebus.Windows.net**. Als dat niet het geval is, verleent u toegang tot de [IP-bereiken van het Azure-Data Center](https://www.microsoft.com/download/details.aspx?id=41653), die wekelijks worden bijgewerkt.
   - Uw agenten hebben toegang tot **login.Windows.net** en **login.microsoftonline.com** nodig voor de eerste registratie. Open ook uw firewall voor deze Url's.
   - Deblokkeren van de certificaat validatie de volgende Url's: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**en **www\.Microsoft.com:80**. Omdat deze Url's worden gebruikt voor certificaat validatie met andere micro soft-producten, is het mogelijk dat deze Url's al worden gedeblokkeerd.

### <a name="verify-the-port"></a>De poort controleren
Als u wilt controleren of het Azure luistert op poort 443 en dat uw agent ermee kan communiceren, kunt u het volgende gebruiken:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Met deze test wordt gecontroleerd of uw agents kunnen communiceren met Azure via poort 443.  Open een browser en navigeer naar de bovenstaande URL van de server waarop de agent is geïnstalleerd.
![Services](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Aanvullende vereisten
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-vereisten

>[!NOTE]
>De Transport Layer Security (TLS) is een protocol dat zorgt voor beveiligde communicatie.  Het wijzigen van de TLS-instellingen is van invloed op het hele forest.  Zie [Update voor het inschakelen van TLS 1,1 en tls 1,2 als standaard beveiligde protocollen in WinHTTP in Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) voor meer informatie.

Op de Windows-Server die als host voor de Azure AD Connect Cloud-inrichtings agent gaat, moet TLS 1,2 zijn ingeschakeld voordat u deze installeert.

TLS 1.2 inschakelen:

1. Stel de volgende registersleutels in:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Start de server opnieuw op.


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)

