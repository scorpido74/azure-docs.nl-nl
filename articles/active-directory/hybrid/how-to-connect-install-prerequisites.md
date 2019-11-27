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
ms.topic: conceptual
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d54ef06fd63a1064962aea6099a2289d04ff658
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462009"
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

* Azure AD Connect moet zijn geïnstalleerd op Windows Server 2008 R2 of later. Deze server moet lid zijn van een domein en kan een domein controller of een lidserver zijn.
* Als u Azure AD Connect installeert op Windows Server 2008 R2, moet u ervoor zorgen dat u de meest recente hotfixes van Windows Update toepast. De installatie kan niet worden gestart met een niet-gepatchde server.
* Als u van plan bent om de functie **wachtwoord synchronisatie**te gebruiken, moet de Azure AD Connect-server op Windows Server 2008 R2 SP1 of hoger zijn.
* Als u van plan bent een **beheerd service account**voor een groep te gebruiken, moet de Azure AD Connect-server op Windows Server 2012 of hoger zijn.
* Op de Azure AD Connect-server moet [.NET Framework 4.5.1](#component-prerequisites) of hoger en [micro soft power Shell 3,0](#component-prerequisites) of hoger zijn geïnstalleerd.
* De Azure AD Connect-server moet Power shell transcriptie niet hebben groepsbeleid ingeschakeld als u Azure AD Connect wizard gebruikt voor het beheren van de ADFS-configuratie. U kunt Power shell transcriptie inschakelen als u Azure AD Connect wizard gebruikt voor het beheren van de synchronisatie configuratie.
* Als Active Directory Federation Services wordt geïmplementeerd, moeten de servers waarop AD FS of Web Application proxy is geïnstalleerd, Windows Server 2012 R2 of later zijn. [Windows Remote Management](#windows-remote-management) moet zijn ingeschakeld op deze servers voor installatie op afstand.
* Als Active Directory Federation Services wordt geïmplementeerd, hebt u [SSL-certificaten](#ssl-certificate-requirements)nodig.
* Als Active Directory Federation Services wordt geïmplementeerd, moet u [naam omzetting](#name-resolution-for-federation-servers)configureren.
* Als voor uw globale beheerders MFA is ingeschakeld, moet de URL **https://secure.aadcdn.microsoftonline-p.com** de lijst met vertrouwde sites zijn. U wordt gevraagd deze site toe te voegen aan de lijst met vertrouwde websites wanneer u wordt gevraagd om een MFA-Challenge en deze niet eerder is toegevoegd. U kunt Internet Explorer gebruiken om het toe te voegen aan uw vertrouwde sites.
* Micro soft raadt aan uw Azure AD Connect-server te beveiligen om het beveiligings risico te verminderen voor dit kritieke onderdeel van uw IT-omgeving.  Als u de onderstaande aanbevelingen volgt, worden de beveiligings Risico's voor uw organisatie verminderd.

* Implementeer Azure AD Connect op een server die lid is van een domein en beperk beheerders toegang tot domein beheerders of andere nauw beheerde beveiligings groepen.

Raadpleeg voor meer informatie: 

* [Beheerders groepen beveiligen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Ingebouwde Administrator-accounts beveiligen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Beveiligings verbetering en-ondersteuning door kwets baarheid te verminderen](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [De Active Directory kwets baarheid verminderen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server gebruikt door Azure AD Connect
* Azure AD Connect vereist een SQL Server-database voor het opslaan van identiteitsgegevens. Er is standaard een SQL Server 2012 Express LocalDB (een lichte versie van SQL Server Express) geïnstalleerd. SQL Server Express heeft een maximale grootte van 10 GB waarmee u ongeveer 100.000 objecten kunt beheren. Als u een groter volume aan Directory objecten wilt beheren, moet u de installatie wizard naar een andere installatie van SQL Server wijzen. Het type SQL Server installatie kan invloed hebben [op de prestaties van Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Als u een andere installatie van SQL Server gebruikt, zijn de volgende vereisten van toepassing:
  * Azure AD Connect ondersteunt alle versies van Microsoft SQL Server van 2008 R2 (met het nieuwste Service Pack) tot SQL Server 2019. Microsoft Azure SQL Database wordt **niet ondersteund** als data base.
  * U moet een niet-hoofdletter gevoelige SQL-sortering gebruiken. Deze sorteringen worden aangeduid met een \_CI_ in hun naam. Het is **niet mogelijk** om een hoofdletter gevoelige collatie te gebruiken, geïdentificeerd door \_CS_ in hun naam.
  * U kunt slechts één synchronisatie-engine per SQL-exemplaar hebben. Het is **niet mogelijk** om een SQL-exemplaar te delen met FIM/MIM Sync, DirSync of Azure AD Sync.

### <a name="accounts"></a>Accounts
* Een Azure AD Global Administrator-account voor de Azure AD-Tenant waarmee u wilt integreren. Dit account moet een **school-of organisatie account** zijn en kan geen **Microsoft-account**zijn.
* Als u snelle instellingen gebruikt of als u een upgrade uitvoert van DirSync, moet u een beheerders account voor de onderneming hebben voor uw on-premises Active Directory.
* [Accounts in Active Directory](reference-connect-accounts-permissions.md) als u het installatiepad voor aangepaste instellingen of een beheerders account voor ondernemingen gebruikt voor uw on-premises Active Directory.

### <a name="connectivity"></a>Connectiviteit
* De Azure AD Connect-server moet de DNS-omzetting voor intranet en Internet hebben. De DNS-server moet namen kunnen omzetten naar uw on-premises Active Directory en de Azure AD-eind punten.
* Als u firewalls op uw intranet hebt en u poorten wilt openen tussen de Azure AD Connect servers en uw domein controllers, raadpleegt u [Azure AD Connect poorten](reference-connect-ports.md) voor meer informatie.
* Als uw proxy of firewall beperkingen heeft voor de toegang tot Url's, moeten de Url's die worden beschreven in [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) adresbereiken worden geopend.
  * Als u de Microsoft Cloud in Duitsland of de Microsoft Azure Government Cloud gebruikt, raadpleegt u [Azure AD Connect Sync Service instances-overwegingen](reference-connect-instances.md) voor url's.
* Azure AD Connect (versie 1.1.614.0 en After) maakt standaard gebruik van TLS 1,2 voor het versleutelen van de communicatie tussen de synchronisatie-engine en Azure AD. Als TLS 1,2 niet beschikbaar is in het onderliggende besturings systeem, wordt Azure AD Connect incrementeel terugvallen op oudere protocollen (TLS 1,1 en TLS 1,0).
* Vóór versie 1.1.614.0 maakt Azure AD Connect standaard gebruik van TLS 1,0 voor het versleutelen van de communicatie tussen de synchronisatie-engine en Azure AD. Als u wilt overschakelen naar TLS 1,2, volgt u de stappen in [tls 1,2 inschakelen voor Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Als u een uitgaande proxy gebruikt om verbinding te maken met internet, moet de volgende instelling in het **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** -bestand worden toegevoegd voor de installatie wizard en kan Azure AD Connect worden gesynchroniseerd om verbinding te kunnen maken met internet en Azure AD. Deze tekst moet aan de onderkant van het bestand worden opgegeven. In deze code bevat &lt;PROXYADDRESS ATTRIBUUT&gt; het werkelijke IP-adres of de hostnaam van de proxy.

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

* Als voor uw proxy server verificatie is vereist, moet het [Service account](reference-connect-accounts-permissions.md#adsync-service-account) zich in het domein bevinden en moet u het installatiepad van aangepaste instellingen gebruiken om een [aangepast Service account](how-to-connect-install-custom.md#install-required-components)op te geven. U hebt ook een andere wijziging nodig voor machine. config. Met deze wijziging in machine. config reageert de installatie wizard en de synchronisatie-engine op verificatie aanvragen van de proxy server. In alle pagina's van de wizard installatie, met uitzonde ring van de pagina **configureren** , worden de referenties van de aangemelde gebruiker gebruikt. Op de pagina **configureren** aan het einde van de installatie wizard wordt de context overgeschakeld naar het [Service account](reference-connect-accounts-permissions.md#adsync-service-account) dat u hebt gemaakt. De sectie machine. config moet er als volgt uitzien.

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

### <a name="other"></a>Overige
* Optioneel: een test gebruikers account om synchronisatie te controleren.

## <a name="component-prerequisites"></a>Onderdeel vereisten
### <a name="powershell-and-net-framework"></a>Power shell en .NET Framework
Azure AD Connect is afhankelijk van micro soft power shell en .NET Framework 4.5.1. U hebt deze versie of een nieuwere versie op uw server nodig. Afhankelijk van de versie van Windows Server gaat u als volgt te werk:

* Windows Server 2012R2
  * Micro soft power shell wordt standaard geïnstalleerd. Geen actie vereist.
  * .NET Framework 4.5.1 en latere versies worden aangeboden via Windows Update. Zorg ervoor dat u de meest recente updates voor Windows Server hebt geïnstalleerd in het configuratie scherm.
* Windows Server 2008 R2 en Windows Server 2012
  * De nieuwste versie van micro soft power shell is beschikbaar in **Windows Management Framework 4,0**, beschikbaar in het [micro soft Download centrum](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 en latere versies zijn beschikbaar in het [micro soft Download centrum](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>TLS 1,2 inschakelen voor Azure AD Connect
Vóór versie 1.1.614.0 maakt Azure AD Connect standaard gebruik van TLS 1,0 voor het versleutelen van de communicatie tussen de synchronisatie engine-server en Azure AD. U kunt dit wijzigen door .NET-toepassingen standaard te configureren voor het gebruik van TLS 1,2 op de server. Meer informatie over TLS 1,2 vindt u in [micro soft-beveiligings advies 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. TLS 1,2 kan niet worden ingeschakeld vóór Windows Server 2008 R2 of eerder. Controleer of u de .NET 4.5.1-hotfix hebt geïnstalleerd voor uw besturings systeem. Zie [micro soft-beveiligings advies 2960358](https://technet.microsoft.com/security/advisory/2960358). Mogelijk hebt u al deze hotfix of een latere versie geïnstalleerd op uw server.
2. Als u Windows Server 2008 R2 gebruikt, moet u ervoor zorgen dat TLS 1,2 is ingeschakeld. Op Windows Server 2012 server en latere versies moet TLS 1,2 al zijn ingeschakeld.
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. Voor alle besturings systemen stelt u deze register sleutel in en start u de server opnieuw op.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. Als u ook TLS 1,2 wilt inschakelen tussen de sync engine-server en een externe SQL Server, moet u ervoor zorgen dat u de vereiste versies hebt geïnstalleerd voor [TLS 1,2-ondersteuning voor Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Vereisten voor Federatie-installatie en-configuratie
### <a name="windows-remote-management"></a>Windows Remote Management
Wanneer u Azure AD Connect gebruikt om Active Directory Federation Services of de webtoepassingsproxy te implementeren, moet u de volgende vereisten controleren:

* Als de doel server lid is van een domein, moet u ervoor zorgen dat Windows Remote Managed is ingeschakeld
  * Gebruik in een PSH opdracht venster met verhoogde bevoegdheid de opdracht `Enable-PSRemoting –force`
* Als de doel server een niet-domein gekoppelde WAP-computer is, zijn er een aantal aanvullende vereisten
  * Op de doel computer (WAP-machine):
    * Zorg ervoor dat de WinRM-service (Windows Remote Management/WS-Management) wordt uitgevoerd via de module Services
    * Gebruik in een PSH opdracht venster met verhoogde bevoegdheid de opdracht `Enable-PSRemoting –force`
  * Op de computer waarop de wizard wordt uitgevoerd (als de doel computer niet lid is van een domein of een niet-vertrouwd domein):
    * Gebruik in een PSH-opdracht venster met verhoogde bevoegdheid de opdracht `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Serverbeheer:
      * DMZ WAP-host toevoegen aan computer groep (Server beheer-> beheren-> servers toevoegen... DNS-tabblad gebruiken)
      * Serverbeheer het tabblad alle servers: Klik met de rechter muisknop op WAP-server en kies beheren als... en geef lokale (niet-domein) referenties op voor de WAP-computer
      * Als u verbinding met externe PSH wilt valideren, klikt u op het tabblad Serverbeheer alle servers: Klik met de rechter muisknop op WAP-server en kies Windows Power shell. Een externe PSH-sessie moet worden geopend om ervoor te zorgen dat externe Power shell-sessies kunnen worden tot stand gebracht.

### <a name="ssl-certificate-requirements"></a>Vereisten voor SSL-certificaat
* Het wordt ten zeerste aanbevolen om hetzelfde SSL-certificaat te gebruiken voor alle knoop punten van uw AD FS-Farm en alle Web Application proxy-servers.
* Het certificaat moet een x509-certificaat zijn.
* U kunt een zelfondertekend certificaat gebruiken op Federatie servers in een test omgeving. Voor een productie omgeving wordt u echter aangeraden het certificaat van een open bare certificerings instantie te verkrijgen.
  * Als u een certificaat gebruikt dat niet openbaar wordt vertrouwd, controleert u of het certificaat dat is geïnstalleerd op elke Web Application proxy-server, wordt vertrouwd op zowel de lokale server als alle Federatie servers
* De identiteit van het certificaat moet overeenkomen met de naam van de Federation service (bijvoorbeeld sts.contoso.com).
  * De identiteit is een SAN-extensie (alternatieve naam voor onderwerp) van het type dNSName of, als er geen SAN-vermeldingen zijn, de naam van het onderwerp opgegeven als een algemene naam.  
  * Er kunnen meerdere SAN-vermeldingen aanwezig zijn in het certificaat, op voor waarde dat een van de items overeenkomt met de naam van de Federation-service.
  * Als u van plan bent Workplace Join te gebruiken, is een extra SAN vereist met de waarde **Enterprise registratie.** gevolgd door het UPN-achtervoegsel (User Principal Name) van uw organisatie, bijvoorbeeld **enterpriseregistration.contoso.com**.
* Certificaten die zijn gebaseerd op CNG-sleutels (CryptoAPI Next Generation) en sleutel opslag providers worden niet ondersteund. Dit betekent dat u een certificaat moet gebruiken dat is gebaseerd op een CSP (Cryptographic Service Provider) en niet een SLEUTELARCHIEFPROVIDER (Key Storage Provider).
* Joker tekens worden ondersteund.

### <a name="name-resolution-for-federation-servers"></a>Naam omzetting voor Federatie servers
* Stel DNS-records in voor de AD FS Federation-service naam (bijvoorbeeld sts.contoso.com) voor zowel het intranet (de interne DNS-server) als het extranet (open bare DNS via uw domein registratie functie). Zorg ervoor dat u een records en niet CNAME-records gebruikt voor de intranet-DNS-record. Dit is vereist voor een juiste werking van Windows-verificatie vanaf de computer die lid is van het domein.
* Als u meer dan één AD FS server of Web Application proxy-server implementeert, moet u ervoor zorgen dat u uw load balancer hebt geconfigureerd en dat de DNS-records voor de AD FS Federation-service naam (bijvoorbeeld sts.contoso.com) verwijzen naar de load balancer.
* Voor geïntegreerde Windows-verificatie voor het werken met browser toepassingen die gebruikmaken van Internet Explorer in uw intranet, moet u ervoor zorgen dat de naam van de AD FS Federation-service (bijvoorbeeld sts.contoso.com) wordt toegevoegd aan de intranet zone in Internet Explorer. Dit kan worden beheerd via groeps beleid en geïmplementeerd op alle computers die lid zijn van het domein.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect ondersteunende onderdelen
Hier volgt een lijst met onderdelen die Azure AD Connect geïnstalleerd op de server waarop Azure AD Connect is geïnstalleerd. Deze lijst is voor een eenvoudige snelle installatie. Als u ervoor kiest om een andere SQL Server te gebruiken op de pagina synchronisatie Services installeren, wordt SQL Express LocalDB niet lokaal geïnstalleerd.

* Azure AD Connect Health
* Microsoft SQL Server 2012-opdracht regel Programma's
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Micro soft C++ Visual 2013-distributie pakket

## <a name="hardware-requirements-for-azure-ad-connect"></a>Hardwarevereisten voor Azure AD Connect
In de volgende tabel ziet u de minimale vereisten voor de Azure AD Connect Sync-computer.

| Aantal objecten in Active Directory | CPU | Geheugen | Grootte van harde schijf |
| --- | --- | --- | --- |
| Minder dan 10.000 |1,6 GHz |4 GB |70 GB |
| 10,000–50,000 |1,6 GHz |4 GB |70 GB |
| 50,000–100,000 |1,6 GHz |16 GB |100 GB |
| Voor 100.000 of meer objecten is de volledige versie van SQL Server vereist | | | |
| 100,000–300,000 |1,6 GHz |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz |32 GB |450 GB |
| Meer dan 600.000 |1,6 GHz |32 GB |500 GB |

De minimale vereisten voor computers waarop AD FS of Web Application proxy-servers worden uitgevoerd, is als volgt:

* CPU: Dual Core 1,6 GHz of hoger
* GEHEUGEN: 2 GB of hoger
* Azure VM: a2-configuratie of hoger

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
