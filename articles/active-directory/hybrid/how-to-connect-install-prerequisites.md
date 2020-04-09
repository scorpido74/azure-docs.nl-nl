---
title: 'Azure AD Connect: vereisten en hardware | Microsoft Documenten'
description: In dit onderwerp worden de vereisten en de hardwarevereisten voor Azure AD Connect beschreven
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6446b039d90e04c9fe7fca28b361f620183a0292
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875738"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Vereisten voor Azure AD Connect
In dit onderwerp worden de vereisten en de hardwarevereisten voor Azure AD Connect beschreven.

## <a name="before-you-install-azure-ad-connect"></a>Voordat u Azure AD Connect installeert
Voordat u Azure AD Connect installeert, zijn er een paar dingen die u nodig hebt.

### <a name="azure-ad"></a>Azure AD
* Een Azure AD-tenant. U krijgt er een met een [gratis proefversie van Azure.](https://azure.microsoft.com/pricing/free-trial/) U een van de volgende portals gebruiken om Azure AD Connect te beheren:
  * De [Azure-portal](https://portal.azure.com).
  * Het [Office-portaal.](https://portal.office.com)  
* [Voeg het domein toe en verifieer](../active-directory-domains-add-azure-portal.md) het domein dat u wilt gebruiken in Azure AD. Als u bijvoorbeeld van plan bent contoso.com voor uw gebruikers te gebruiken, moet u ervoor zorgen dat dit domein is geverifieerd en dat u niet alleen het contoso.onmicrosoft.com standaarddomein gebruikt.
* Een Azure AD-tenant staat standaard 50k-objecten toe. Wanneer u uw domein verifieert, wordt de limiet verhoogd tot objecten van 300k. Als u nog meer objecten in Azure AD nodig hebt, moet u een ondersteuningsaanvraag openen om de limiet nog verder te verhogen. Als u meer dan 500k-objecten nodig hebt, hebt u een licentie nodig, zoals Office 365, Azure AD Basic, Azure AD Premium of Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Uw on-premises gegevens voorbereiden
* Gebruik [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) om fouten zoals duplicaten en opmaakproblemen in uw map te identificeren voordat u synchroniseert met Azure AD en Office 365.
* Bekijk [optionele synchronisatiefuncties die u inschakelen in Azure AD](how-to-connect-syncservice-features.md) en evalueer welke functies u moet inschakelen.

### <a name="on-premises-active-directory"></a>On-premises Active Directory
* De AD-schemaversie en het forestfunctionaliteitsniveau moeten Windows Server 2003 of hoger zijn. De domeincontrollers kunnen elke versie uitvoeren zolang aan de vereisten voor schema- en forestniveau is voldaan.
* Als u van plan bent om de **terugschrijftekst van**het functiewachtwoord te gebruiken, moeten de domeincontrollers zich op Windows Server 2008 R2 of hoger bevinden.
* De domeincontroller die door Azure AD wordt gebruikt, moet beschrijfbaar zijn. Het wordt **niet ondersteund** voor het gebruik van een RODC (alleen-lezen domeincontroller) en Azure AD Connect volgt geen schrijfomleidingen.
* Het wordt **niet ondersteund** om on-premises forests/domeinen te gebruiken met behulp van "gestippeld" (naam bevat een periode ".") NetBios namen.
* Het wordt aanbevolen om de prullenbak van Active Directory in te [schakelen.](how-to-connect-sync-recycle-bin.md)

### <a name="azure-ad-connect-server"></a>Azure AD Connect-server
>[!IMPORTANT]
>De Azure AD Connect-server bevat kritieke identiteitsgegevens en moet worden behandeld als een Tier 0-component zoals gedocumenteerd in [het Active Directory-beheermodel](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

* Azure AD Connect kan niet vóór 2019 op Small Business Server of Windows Server Essentials worden geïnstalleerd (Windows Server Essentials 2019 wordt ondersteund). De server moet Windows Server standaard of beter gebruiken.
* Het installeren van Azure AD Connect op een domeincontroller wordt niet aanbevolen vanwege beveiligingsprocedures en meer beperkende instellingen die kunnen voorkomen dat Azure AD Connect correct wordt geïnstalleerd.
* De Azure AD Connect-server moet een volledige GUI hebben geïnstalleerd. Het wordt **niet ondersteund** om te installeren op de servercore.
>[!IMPORTANT]
>Het installeren van Azure AD Connect op de server, serveressentials of servercore van Azure wordt niet ondersteund.

* Azure AD Connect moet zijn geïnstalleerd op Windows Server 2012 of hoger. Deze server moet domein lid zijn en kan een domeincontroller of een lidserver zijn.
* De Azure AD Connect-server mag geen PowerShell-transcriptiegroepbeleid hebben ingeschakeld als u de wizard Azure AD Connect gebruikt om de ADFS-configuratie te beheren. U PowerShell-transcriptie inschakelen als u de wizard Azure AD Connect gebruikt om synchronisatieconfiguratie te beheren.
* Als Active Directory Federation Services wordt geïmplementeerd, moeten de servers waarop AD FS of Web Application Proxy zijn geïnstalleerd, Windows Server 2012 R2 of hoger zijn. [Windows remote management](#windows-remote-management) moet op deze servers zijn ingeschakeld voor installatie op afstand.
* Als Active Directory Federation Services wordt geïmplementeerd, hebt u [TLS/SSL-certificaten](#tlsssl-certificate-requirements)nodig.
* Als Active Directory Federation Services wordt geïmplementeerd, moet u [naamomzetting](#name-resolution-for-federation-servers)configureren.
* Als uw globale beheerders MFA hebben **https://secure.aadcdn.microsoftonline-p.com** ingeschakeld, moet de URL in de lijst met vertrouwde sites staan. U wordt gevraagd om deze site toe te voegen aan de lijst met vertrouwde sites wanneer u wordt gevraagd om een MFA-uitdaging en deze nog niet eerder is toegevoegd. U Internet Explorer gebruiken om het toe te voegen aan uw vertrouwde sites.
* Microsoft raadt aan om uw Azure AD Connect-server te verharden om het beveiligingsaanvalsoppervlak voor dit kritieke onderdeel van uw IT-omgeving te verkleinen.  Als u de onderstaande aanbevelingen volgt, worden de beveiligingsrisico's voor uw organisatie verlaagd.

* Implementeer Azure AD Connect op een domeinverbonden server en beperk de beheerderstoegang tot domeinbeheerders of andere streng gecontroleerde beveiligingsgroepen.

Voor meer informatie zie: 

* [Beheerdersgroepen beveiligen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Ingebouwde beheerdersaccounts beveiligen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Verbetering en ondersteuning van de veiligheid door het verminderen van aanvalsoppervlakken](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Het oppervlak van de Active Directory-aanval verkleinen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server gebruikt door Azure AD Connect
* Azure AD Connect vereist een SQL Server-database voor het opslaan van identiteitsgegevens. Standaard is een SQL Server 2012 Express LocalDB (een lichte versie van SQL Server Express) geïnstalleerd. SQL Server Express heeft een groottelimiet van 10 GB waarmee u ongeveer 100.000 objecten beheren. Als u een hoger volume directoryobjecten moet beheren, moet u de installatiewizard naar een andere installatie van SQL Server wijzen. Het type SQL Server-installatie kan van invloed zijn op de [prestaties van Azure AD Connect.](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)
* Als u een andere installatie van SQL Server gebruikt, zijn deze vereisten van toepassing:
  * Azure AD Connect ondersteunt alle versies van Microsoft SQL Server van 2012 (met het nieuwste Service Pack) tot SQL Server 2019. Microsoft Azure SQL Database **wordt niet ondersteund** als een database.
  * U moet een case-ongevoelige SQL-collatie gebruiken. Deze collaties zijn \_geïdentificeerd met een CI_ in hun naam. Het wordt **niet ondersteund** om een case-gevoelige collatie te gebruiken, geïdentificeerd door \_CS_ in hun naam.
  * U slechts één synchronisatieengine per SQL-exemplaar hebben. Het wordt **niet ondersteund** om een SQL-exemplaar te delen met FIM/MIM Sync, DirSync of Azure AD Sync.

### <a name="accounts"></a>Accounts
* Een Azure AD Global Administrator-account voor de Azure AD-tenant waarmee u wilt integreren. Dit account moet een **school- of organisatieaccount** zijn en kan geen **Microsoft-account**zijn.
* Als u [expresinstellingen](reference-connect-accounts-permissions.md#express-settings-installation) of een upgrade van DirSync gebruikt, moet u een Enterprise Administrator-account hebben voor uw on-premises Active Directory.
* Als u het aangepaste installatiepad voor instellingen gebruikt, hebt u meer opties. Zie [Aangepaste installatie-instellingen voor](reference-connect-accounts-permissions.md#custom-installation-settings)meer informatie .

### <a name="connectivity"></a>Connectiviteit
* De Azure AD Connect-server heeft DNS-resolutie nodig voor zowel intranet als internet. De DNS-server moet namen kunnen oplossen in zowel uw on-premises Active Directory als de Azure AD-eindpunten.
* Als u firewalls op uw intranet hebt en u poorten moet openen tussen de Azure AD Connect-servers en uw domeincontrollers, raadpleegt u [Azure AD Connect-poorten](reference-connect-ports.md) voor meer informatie.
* Als uw proxy- of firewalllimiet voor toegang tot URL's kan worden geopend, moeten de URL's die zijn gedocumenteerd in [Office 365-URL's en IP-adresbereiken](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) worden geopend.
  * Als u de Microsoft Cloud in Duitsland of de Microsoft Azure Government-cloud gebruikt, raadpleegt u overwegingen voor [synchronisatieservice-instanties van Azure AD Connect](reference-connect-instances.md) voor URL's.
* Azure AD Connect (versie 1.1.614.0 en daarna) gebruikt tls 1.2 standaard voor het versleutelen van communicatie tussen de synchronisatieengine en Azure AD. Als TLS 1.2 niet beschikbaar is op het onderliggende besturingssysteem, valt Azure AD Connect stapsgewijs terug naar oudere protocollen (TLS 1.1 en TLS 1.0).
* Voorafgaand aan versie 1.1.614.0 gebruikt Azure AD Connect standaard TLS 1.0 voor het versleutelen van communicatie tussen de synchronisatieengine en Azure AD. Als u wilt overstappen op TLS 1.2, voert u de stappen uit in [TLS 1.2 inschakelen voor Azure AD Connect.](#enable-tls-12-for-azure-ad-connect)
* Als u een uitgaande proxy gebruikt voor het maken van verbinding met internet, moet de volgende instelling in de **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config-bestand** worden toegevoegd voor de installatiewizard en Azure AD Connect-synchronisatie om verbinding te kunnen maken met internet en Azure AD. Deze tekst moet onder aan het bestand worden ingevoerd. In deze &lt;code&gt; vertegenwoordigt PROXYADDRESS het werkelijke proxy-IP-adres of de hostnaam.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Als uw proxyserver verificatie vereist, moet het [serviceaccount](reference-connect-accounts-permissions.md#adsync-service-account) zich in het domein bevinden en moet u het aangepaste installatiepad voor instellingen gebruiken om een [aangepast serviceaccount](how-to-connect-install-custom.md#install-required-components)op te geven. Je hebt ook een andere verandering nodig dan machine.config. Met deze wijziging in machine.config reageren de installatiewizard en de synchronisatieengine op verificatieverzoeken van de proxyserver. Op alle pagina's van de installatiewizard, met uitzondering van de pagina **Configureren,** worden de referenties van de ondertekende gebruiker gebruikt. Op de pagina **Configureren** aan het einde van de wizard installatie wordt de context overgeschakeld naar het [serviceaccount](reference-connect-accounts-permissions.md#adsync-service-account) dat door u is gemaakt. De machine.config sectie moet er zo uitzien.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Wanneer Azure AD Connect een webaanvraag naar Azure AD verzendt als onderdeel van adreslijstsynchronisatie, kan het tot 5 minuten duren voordat Azure AD reageert. Het is gebruikelijk dat proxyservers een niet-actieve time-outconfiguratie hebben. Zorg ervoor dat de configuratie is ingesteld op ten minste 6 minuten of meer.

Zie MSDN over het [standaardproxy-element](https://msdn.microsoft.com/library/kd3cf2ex.aspx)voor meer informatie.  
Zie [Verbindingsproblemen oplossen](tshoot-connect-connectivity.md)voor meer informatie wanneer u problemen hebt met de connectiviteit.

### <a name="other"></a>Overige
* Optioneel: een testgebruikersaccount om synchronisatie te verifiëren.

## <a name="component-prerequisites"></a>Componentvereisten
### <a name="powershell-and-net-framework"></a>PowerShell en .NET Framework
Azure AD Connect is afhankelijk van Microsoft PowerShell en .NET Framework 4.5.1. U hebt deze versie of een latere versie op uw server geïnstalleerd. Ga als volgt te werk, afhankelijk van uw Windows Server-versie:

* Windows Server 2012R2
  * Microsoft PowerShell is standaard geïnstalleerd. Geen actie vereist.
  * .NET Framework 4.5.1 en latere releases worden aangeboden via Windows Update. Zorg ervoor dat u de nieuwste updates voor Windows Server hebt geïnstalleerd in het Configuratiescherm.
* Windows Server 2012
  * De nieuwste versie van Microsoft PowerShell is beschikbaar in **Windows Management Framework 4.0,** beschikbaar in [Microsoft Download Center.](https://www.microsoft.com/downloads)
  * .NET Framework 4.5.1 en latere releases zijn beschikbaar op [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>TLS 1.2 inschakelen voor Azure AD Connect
Voorafgaand aan versie 1.1.614.0 gebruikt Azure AD Connect standaard TLS 1.0 voor het versleutelen van de communicatie tussen de synchronisatieserver en Azure AD. U dit wijzigen door .NET-toepassingen te configureren om TLS 1.2 standaard op de server te gebruiken. Meer informatie over TLS 1.2 is te vinden in [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Zorg ervoor dat de .NET 4.5.1 hotfix voor uw besturingssysteem is geïnstalleerd, zie [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Mogelijk hebt u deze hotfix of een latere release al op uw server geïnstalleerd.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319 "SchUseStrongCrypto"=dword:0000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
