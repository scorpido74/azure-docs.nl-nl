---
title: 'Azure AD Connect: vereisten en hardware | Microsoft Docs'
description: In dit onderwerp worden de vereisten en hardwarevereisten voor Azure AD Connect beschreven
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
ms.topic: how-to
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bcf7b5b8791b813a28133d8a662d1736aacf35a
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85358715"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Vereisten voor Azure AD Connect
In dit onderwerp worden de vereisten en hardwarevereisten voor Azure AD Connect beschreven.

## <a name="before-you-install-azure-ad-connect"></a>Voordat u Azure AD Connect installeert
Voordat u Azure AD Connect installeert, zijn er enkele dingen die u nodig hebt.

### <a name="azure-ad"></a>Azure AD
* Een Azure AD-tenant. U krijgt er een met een [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/). U kunt een van de volgende portals gebruiken voor het beheren van Azure AD Connect:
  * Het [Azure Portal](https://portal.azure.com).
  * De [Office-Portal](https://portal.office.com).  
* [Voeg en controleer het domein](../active-directory-domains-add-azure-portal.md) dat u wilt gebruiken in azure AD. Als u bijvoorbeeld van plan bent om contoso.com te gebruiken voor uw gebruikers, moet u ervoor zorgen dat dit domein is geverifieerd en dat u niet alleen gebruikmaakt van het standaard domein contoso.onmicrosoft.com.
* Een Azure AD-Tenant maakt standaard 50.000-objecten mogelijk. Wanneer u uw domein verifieert, wordt de limiet verhoogd naar 300 KB-objecten. Als u nog meer objecten in azure AD nodig hebt, moet u een ondersteunings aanvraag openen om de limiet nog verder te verg root. Als u meer dan 500.000-objecten nodig hebt, hebt u een licentie nodig, zoals Office 365, Azure AD Basic, Azure AD Premium of ENTER prise Mobility en Security.

### <a name="prepare-your-on-premises-data"></a>Uw on-premises gegevens voorbereiden
* Gebruik [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) om fouten te identificeren, zoals duplicaten en opmaak problemen in uw Directory voordat u synchroniseert met Azure AD en Office 365.
* Bekijk de [optionele synchronisatie functies die u in azure AD kunt inschakelen](how-to-connect-syncservice-features.md) en bepaal welke functies u moet inschakelen.

### <a name="on-premises-active-directory"></a>On-premises Active Directory
* De AD-schema versie en het functionele forest-functionaliteits niveau moeten Windows Server 2003 of hoger zijn. Op de domein controllers kan elke versie worden uitgevoerd zolang aan de vereisten voor schema-en forestniveau is voldaan.
* Als u het **terugschrijven**van het functie wachtwoord wilt gebruiken, moeten de domein controllers zich op Windows Server 2008 R2 of later bevallen.
* De domein controller die door Azure AD wordt gebruikt, moet beschrijfbaar zijn. Het gebruik van een RODC (alleen-lezen domein controller) wordt **niet ondersteund** en Azure AD Connect geen schrijf omleidingen volgen.
* Het is **niet mogelijk** om on-premises forests/domeinen te gebruiken met behulp van ' dotted ' (naam bevat een punt '. ') NetBios-namen.
* Het is raadzaam om [de Prullenbak van Active Directory in te scha kelen](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Azure AD Connect server
>[!IMPORTANT]
>De Azure AD Connect-server bevat essentiële identiteits gegevens en moet worden behandeld als een onderdeel van de laag 0 zoals beschreven in [het Active Directory beheer laag model](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

* Azure AD Connect kan niet worden geïnstalleerd op Small Business Server of Windows Server Essentials vóór 2019 (Windows Server Essentials 2019 wordt ondersteund). De server moet Windows Server Standard of hoger gebruiken.
* Het installeren van Azure AD Connect op een domein controller wordt niet aanbevolen vanwege beveiligings procedures en meer beperkende instellingen waarmee wordt voor komen dat Azure AD Connect op de juiste manier worden geïnstalleerd.
* Op de Azure AD Connect-server moet een volledige GUI zijn geïnstalleerd. Het wordt **niet ondersteund** voor installatie op Server Core.
>[!IMPORTANT]
>Het installeren van Azure AD Connect op Small Business Server, Server Essentials of Server Core wordt niet ondersteund.

* Azure AD Connect moet zijn geïnstalleerd op Windows Server 2012 of hoger. Deze server moet lid zijn van een domein en kan een domein controller of een lidserver zijn.
* De Azure AD Connect-server moet Power shell transcriptie niet hebben groepsbeleid ingeschakeld als u Azure AD Connect wizard gebruikt voor het beheren van de ADFS-configuratie. U kunt Power shell transcriptie inschakelen als u Azure AD Connect wizard gebruikt voor het beheren van de synchronisatie configuratie.
* Als Active Directory Federation Services wordt geïmplementeerd, moeten de servers waarop AD FS of Web Application proxy is geïnstalleerd, Windows Server 2012 R2 of later zijn. [Windows Remote Management](#windows-remote-management) moet zijn ingeschakeld op deze servers voor installatie op afstand.
* Als Active Directory Federation Services wordt geïmplementeerd, hebt u [TLS/SSL-certificaten](#tlsssl-certificate-requirements)nodig.
* Als Active Directory Federation Services wordt geïmplementeerd, moet u [naam omzetting](#name-resolution-for-federation-servers)configureren.
* Als voor uw globale beheerders MFA is ingeschakeld, moet de URL **https://secure.aadcdn.microsoftonline-p.com** zich in de lijst met vertrouwde sites bekomen. U wordt gevraagd deze site toe te voegen aan de lijst met vertrouwde websites wanneer u wordt gevraagd om een MFA-Challenge en deze niet eerder is toegevoegd. U kunt Internet Explorer gebruiken om het toe te voegen aan uw vertrouwde sites.
* Micro soft raadt aan uw Azure AD Connect-server te beveiligen om het beveiligings risico te verminderen voor dit kritieke onderdeel van uw IT-omgeving.  Als u de onderstaande aanbevelingen volgt, worden de beveiligings Risico's voor uw organisatie verminderd.

* Implementeer Azure AD Connect op een server die lid is van een domein en beperk beheerders toegang tot domein beheerders of andere nauw beheerde beveiligings groepen.

Voor meer informatie zie: 

* [Beheerders groepen beveiligen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Ingebouwde Administrator-accounts beveiligen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Beveiligings verbetering en-ondersteuning door kwets baarheid te verminderen](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [De Active Directory kwets baarheid verminderen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server gebruikt door Azure AD Connect
* Azure AD Connect vereist een SQL Server-database voor het opslaan van identiteitsgegevens. Er is standaard een SQL Server 2012 Express LocalDB (een lichte versie van SQL Server Express) geïnstalleerd. SQL Server Express heeft een maximale grootte van 10 GB waarmee u ongeveer 100.000 objecten kunt beheren. Als u een groter volume aan Directory objecten wilt beheren, moet u de installatie wizard naar een andere installatie van SQL Server wijzen. Het type SQL Server installatie kan invloed hebben [op de prestaties van Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Als u een andere installatie van SQL Server gebruikt, zijn de volgende vereisten van toepassing:
  * Azure AD Connect ondersteunt alle versies van Microsoft SQL Server van 2012 (met het nieuwste Service Pack) tot SQL Server 2019. Microsoft Azure SQL Database wordt **niet ondersteund** als data base.
  * U moet een niet-hoofdletter gevoelige SQL-sortering gebruiken. Deze sorteringen worden aangeduid met een \_ CI_ in hun naam. Het gebruik van een hoofdletter gevoelige sortering wordt **niet ondersteund** , aangeduid door \_ CS_ in hun naam.
  * U kunt slechts één synchronisatie-engine per SQL-exemplaar hebben. Het is **niet mogelijk** om een SQL-exemplaar te delen met FIM/MIM Sync, DirSync of Azure AD Sync.

### <a name="accounts"></a>Accounts
* Een Azure AD Global Administrator-account voor de Azure AD-Tenant waarmee u wilt integreren. Dit account moet een **school-of organisatie account** zijn en kan geen **Microsoft-account**zijn.
* Als u [snelle instellingen](reference-connect-accounts-permissions.md#express-settings-installation) gebruikt of als u een upgrade uitvoert van DirSync, moet u een beheerders account voor de onderneming hebben voor uw on-premises Active Directory.
* Als u het installatiepad van aangepaste instellingen gebruikt, hebt u meer opties. Zie [aangepaste installatie-instellingen](reference-connect-accounts-permissions.md#custom-installation-settings)voor meer informatie.

### <a name="connectivity"></a>Connectiviteit
* De Azure AD Connect-server moet de DNS-omzetting voor intranet en Internet hebben. De DNS-server moet namen kunnen omzetten naar uw on-premises Active Directory en de Azure AD-eind punten.
* Als u firewalls op uw intranet hebt en u poorten wilt openen tussen de Azure AD Connect servers en uw domein controllers, raadpleegt u [Azure AD Connect poorten](reference-connect-ports.md) voor meer informatie.
* Als uw proxy of firewall beperkingen heeft voor de toegang tot Url's, moeten de Url's die worden beschreven in [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) adresbereiken worden geopend.
  * Als u de Microsoft Cloud in Duitsland of de Microsoft Azure Government Cloud gebruikt, raadpleegt u [Azure AD Connect Sync Service instances-overwegingen](reference-connect-instances.md) voor url's.
* Azure AD Connect (versie 1.1.614.0 en After) maakt standaard gebruik van TLS 1,2 voor het versleutelen van de communicatie tussen de synchronisatie-engine en Azure AD. Als TLS 1,2 niet beschikbaar is in het onderliggende besturings systeem, wordt Azure AD Connect incrementeel terugvallen op oudere protocollen (TLS 1,1 en TLS 1,0).
* Vóór versie 1.1.614.0 maakt Azure AD Connect standaard gebruik van TLS 1,0 voor het versleutelen van de communicatie tussen de synchronisatie-engine en Azure AD. Als u wilt overschakelen naar TLS 1,2, volgt u de stappen in [tls 1,2 inschakelen voor Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Als u een uitgaande proxy gebruikt om verbinding te maken met internet, moet u de volgende instelling in het **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** -bestand toevoegen aan de installatie wizard en Azure AD Connect synchroniseren om verbinding te kunnen maken met internet en Azure AD. Deze tekst moet aan de onderkant van het bestand worden opgegeven. In deze code &lt; vertegenwoordigt proxyAddress attribuut &gt; het werkelijke IP-adres of de hostnaam van de proxy.

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

* Als voor uw proxy server verificatie is vereist, moet het [Service account](reference-connect-accounts-permissions.md#adsync-service-account) zich in het domein bevinden en moet u het installatiepad van aangepaste instellingen gebruiken om een [aangepast Service account](how-to-connect-install-custom.md#install-required-components)op te geven. U hebt ook een andere wijziging in machine.config. Met deze wijziging in machine.config reageert de installatie wizard en de synchronisatie-engine op verificatie aanvragen van de proxy server. In alle pagina's van de wizard installatie, met uitzonde ring van de pagina **configureren** , worden de referenties van de aangemelde gebruiker gebruikt. Op de pagina **configureren** aan het einde van de installatie wizard wordt de context overgeschakeld naar het [Service account](reference-connect-accounts-permissions.md#adsync-service-account) dat u hebt gemaakt. De sectie machine.config moet er als volgt uitzien.

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

* Wanneer Azure AD Connect een webverzoek verzendt naar Azure AD als onderdeel van adreslijst synchronisatie, kan het tot vijf minuten duren voordat Azure AD reageert. Het is gebruikelijk dat proxy servers een time-outconfiguratie voor verbinding inactief hebben. Controleer of de configuratie is ingesteld op ten minste 6 minuten of langer.

Raadpleeg MSDN over het [standaard proxy-element](https://msdn.microsoft.com/library/kd3cf2ex.aspx)voor meer informatie.  
Zie [verbindings problemen oplossen](tshoot-connect-connectivity.md)voor meer informatie over problemen met de connectiviteit.

### <a name="other"></a>Anders
* Optioneel: een test gebruikers account om synchronisatie te controleren.

## <a name="component-prerequisites"></a>Onderdeel vereisten
### <a name="powershell-and-net-framework"></a>Power shell en .NET Framework
Azure AD Connect is afhankelijk van micro soft power shell en .NET Framework 4.5.1. U hebt deze versie of een nieuwere versie op uw server nodig. Afhankelijk van de versie van Windows Server gaat u als volgt te werk:

* Windows Server-2012R2
  * Micro soft power shell wordt standaard geïnstalleerd. Geen actie vereist.
  * .NET Framework 4.5.1 en latere versies worden aangeboden via Windows Update. Zorg ervoor dat u de meest recente updates voor Windows Server hebt geïnstalleerd in het configuratie scherm.
* Windows Server 2012
  * De nieuwste versie van micro soft power shell is beschikbaar in **Windows Management Framework 4,0**, beschikbaar in het [micro soft Download centrum](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 en latere versies zijn beschikbaar in het [micro soft Download centrum](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>TLS 1,2 inschakelen voor Azure AD Connect
Vóór versie 1.1.614.0 maakt Azure AD Connect standaard gebruik van TLS 1,0 voor het versleutelen van de communicatie tussen de synchronisatie engine-server en Azure AD. U kunt dit wijzigen door .NET-toepassingen standaard te configureren voor het gebruik van TLS 1,2 op de server. Meer informatie over TLS 1,2 vindt u in [micro soft-beveiligings advies 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Controleer of u de .NET 4.5.1-hotfix hebt geïnstalleerd voor uw besturings systeem. Zie [micro soft-beveiligings advies 2960358](https://technet.microsoft.com/security/advisory/2960358). Mogelijk hebt u al deze hotfix of een latere versie geïnstalleerd op uw server.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \. NETFramework\v4.0.30319 "schusestrongcrypto toe" = dword: 00000001
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
