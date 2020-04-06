---
title: Veelgestelde vragen over configuratie- en beheerproblemen
titleSuffix: Azure Cloud Services
description: In dit artikel worden de veelgestelde vragen over configuratie en beheer voor Microsoft Azure Cloud Services weergegeven.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 5821c72ae1be4759cf5aa76ff1f5af43337749c0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668581"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Configuratie- en beheerproblemen voor Azure Cloud Services: veelgestelde vragen (veelgestelde vragen)

Dit artikel bevat veelgestelde vragen over configuratie- en beheerproblemen voor [Microsoft Azure Cloud Services.](https://azure.microsoft.com/services/cloud-services) U ook de [pagina VM-grootte van Cloud Services](cloud-services-sizes-specs.md) raadplegen voor informatie over de grootte.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificaten**

- [Waarom is de certificaatketen van mijn Cloud Service TLS/SSL-certificaat onvolledig?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Wat is het doel van het versleutelingscertificaat voor Windows Azure Tools voor extensies?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Hoe kan ik een Csr (Certificate Signing Request) genereren zonder 'RDP-ing' in de instantie?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Mijn Cloud Service Management Certificate verloopt. Hoe te vernieuwen?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Hoe automatiseer je de installatie van het hoofdTLS/SSL-certificaat(.pfx) en intermediate certificaat(.p7b)?](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Wat is het doel van het certificaat 'Microsoft Azure Service Management for MachineKey'?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Bewaking en registratie**

- [Wat zijn de komende Cloud Service-mogelijkheden in de Azure-portal die kunnen helpen bij het beheren en bewaken van toepassingen?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Waarom schrijft IIS niet meer naar de logboekmap?](#why-does-iis-stop-writing-to-the-log-directory)
- [Hoe schakel ik WAD-logboekregistratie in voor Cloud Services?](#how-do-i-enable-wad-logging-for-cloud-services)

**Netwerkconfiguratie**

- [Hoe stel ik de niet-actieve time-out in voor Azure load balancer?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Hoe koppel ik een statisch IP-adres aan mijn Cloud Service?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Wat zijn de functies en mogelijkheden die Azure basic IPS/IDS en DDOS biedt?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Http/2 inschakelen op VM voor Cloud Services?](#how-to-enable-http2-on-cloud-services-vm)

**Machtigingen**

- [Kunnen microsoft interne engineers remote desktop naar Cloud Service-exemplaren zonder toestemming?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Ik kan geen extern bureaublad naar Cloud Service VM met behulp van het RDP-bestand. Ik krijg volgende fout: Er is een verificatiefout opgetreden (Code: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Schalen**

- [Ik kan niet verder schalen dan X-exemplaren](#i-cannot-scale-beyond-x-instances)
- [Hoe kan ik Automatisch schalen configureren op basis van geheugenstatistieken?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Generieke**

- [Hoe voeg ik 'nosniff' toe aan mijn website?](#how-do-i-add-nosniff-to-my-website)
- [Hoe pas ik IIS aan voor een webrol?](#how-do-i-customize-iis-for-a-web-role)
- [Wat is de quotumlimiet voor mijn Cloud Service?](#what-is-the-quota-limit-for-my-cloud-service)
- [Waarom toont het station op mijn Cloud Service VM zeer weinig vrije schijfruimte?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Hoe kan ik een Antimalware-extensie voor mijn Cloud Services op een geautomatiseerde manier toevoegen?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Hoe schakel je Server Name Indication (SNI) in voor Cloud Services?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Hoe kan ik tags toevoegen aan mijn Azure Cloud Service?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [De Azure-portal geeft niet de SDK-versie van mijn Cloud Service weer. Hoe kan ik dat krijgen?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Ik wil de Cloud Service enkele maanden afsluiten. Hoe u de factureringskosten van Cloud Service verlagen zonder het IP-adres te verliezen?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certificaten

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Waarom is de certificaatketen van mijn Cloud Service TLS/SSL-certificaat onvolledig?
    
We raden klanten aan om de volledige certificaatketen (leaf cert, intermediate certs en root cert) te installeren in plaats van alleen het bladcertificaat. Wanneer u alleen het bladcertificaat installeert, vertrouwt u op Windows om de certificaatketen op te bouwen door de CTL te lopen. Als er intermitterende netwerk- of DNS-problemen optreden in Azure of Windows Update wanneer Windows het certificaat probeert te valideren, kan het certificaat als ongeldig worden beschouwd. Door de volledige certificaatketen te installeren, kan dit probleem worden voorkomen. De blog op [Hoe een geketend SSL-certificaat te installeren](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) laat zien hoe dit te doen.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Wat is het doel van het versleutelingscertificaat voor Windows Azure Tools voor extensies?

Deze certificaten worden automatisch gemaakt wanneer een extensie wordt toegevoegd aan de Cloud Service. Meestal is dit de WAD-extensie of de RDP-extensie, maar het kunnen anderen zijn, zoals de Antimalware- of Log Collector-extensie. Deze certificaten worden alleen gebruikt voor het versleutelen en decoderen van de privéconfiguratie voor de extensie. De vervaldatum wordt nooit gecontroleerd, dus het maakt niet uit of het certificaat is verlopen. 

U deze certificaten negeren. Als u de certificaten wilt opschonen, u ze allemaal verwijderen. Azure gooit een fout als u een certificaat probeert te verwijderen dat in gebruik is.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Hoe kan ik een Csr (Certificate Signing Request) genereren zonder 'RDP-ing' in de instantie?

Zie het volgende begeleidingsdocument:

[Een certificaat verkrijgen voor gebruik met Windows Azure-websites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

De CSR is slechts een tekstbestand. Het hoeft niet te worden gemaakt van de machine waar het certificaat uiteindelijk zal worden gebruikt.Hoewel dit document is geschreven voor een App-service, is de csr-creatie generiek en geldt deze ook voor Cloud Services.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Mijn Cloud Service Management Certificate verloopt. Hoe te vernieuwen?

U de volgende PowerShell-opdrachten gebruiken om uw beheercertificaten te vernieuwen:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

Met **het Get-AzurePublishSettingsFile** wordt een nieuw beheercertificaat gemaakt in **certificaten voor abonnementsbeheer** > **Management Certificates** in de Azure-portal. De naam van het nieuwe certificaat ziet eruit als "YourSubscriptionNam]-[CurrentDate]-credentials".

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>Hoe automatiseer je de installatie van het hoofdTLS/SSL-certificaat(.pfx) en intermediate certificaat(.p7b)?

U deze taak automatiseren met een opstartscript (batch/cmd/PowerShell) en dat opstartscript registreren in het servicedefinitiebestand. Voeg zowel het opstartscript als het certificaat(.p7b-bestand) toe in de projectmap van dezelfde map van het opstartscript.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Wat is het doel van het certificaat 'Microsoft Azure Service Management for MachineKey'?

Dit certificaat wordt gebruikt om machinesleutels te versleutelen in Azure WebRollen. Voor meer informatie, check [out dit advies](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731).

Raadpleeg voor meer informatie de volgende artikelen:
- [Opstarttaken configureren en uitvoeren voor een Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Opstarttaken van Common Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Bewaking en registratie

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Wat zijn de komende Cloud Service-mogelijkheden in de Azure-portal die kunnen helpen bij het beheren en bewaken van toepassingen?

De mogelijkheid om een nieuw certificaat voor Extern bureaublad-protocol (RDP) te genereren, is binnenkort beschikbaar. U dit script ook uitvoeren:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Mogelijkheid om blob of lokaal te kiezen voor uw csdef en cscfg upload locatie is binnenkort beschikbaar. Met [Nieuw-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0)u elke locatiewaarde instellen.

Mogelijkheid om statistieken op instantieniveau te controleren. Aanvullende bewakingsmogelijkheden zijn beschikbaar in [Hoe cloudservices te controleren.](cloud-services-how-to-monitor.md)

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Waarom schrijft IIS niet meer naar de logboekmap?
U hebt het lokale opslagquotum voor het schrijven naar de logboekmap opgebruikt.Om dit te corrigeren, u een van de drie dingen doen:
* Schakel diagnostische gegevens in voor IIS en laat de diagnose periodiek worden verplaatst naar blobopslag.
* Logbestanden handmatig verwijderen uit de logboekmap.
* Verhoog de quotumlimiet voor lokale resources.

Raadpleeg de volgende documenten voor meer informatie:
* [Diagnostische gegevens opslaan en weergeven in Azure Storage](/azure/storage/common/storage-introduction)
* [IIS-logboeken stoppen met schrijven in Cloud Service](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Hoe schakel ik WAD-logboekregistratie in voor Cloud Services?
U Logboekregistratie (Wad) van Windows Azure Diagnostics inschakelen via de volgende opties:
1. [Inschakelen vanuit Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Inschakelen via .NET-code](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Inschakelen via Powershell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Om de huidige WAD-instellingen van uw Cloud Service te krijgen, u [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd gebruiken of bekijken via portal van het blade 'Cloud Services --> Extensions'.


## <a name="network-configuration"></a>Netwerkconfiguratie

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Hoe stel ik de niet-actieve time-out in voor Azure load balancer?
U de time-out in uw servicedefinitiebestand (csdef) als volgt opgeven:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Zie [Nieuw: Configureerbare idle time-out voor Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) voor meer informatie.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Hoe koppel ik een statisch IP-adres aan mijn Cloud Service?
Als u een statisch IP-adres wilt instellen, moet u een gereserveerd IP-adres maken. Dit gereserveerde IP kan worden gekoppeld aan een nieuwe Cloud Service of aan een bestaande implementatie. Zie de volgende documenten voor meer informatie:
* [Een gereserveerd IP-adres maken](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Het IP-adres van een bestaande cloudservice reserveren](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Een gereserveerd IP koppelen aan een nieuwe CloudService](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Een gereserveerd IP koppelen aan een lopende implementatie](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Een gereserveerd IP koppelen aan een CloudService met behulp van een serviceconfiguratiebestand](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Wat zijn de functies en mogelijkheden die Azure basic IPS/IDS en DDOS biedt?
Azure heeft IPS/IDS in fysieke datacenterservers om zich te verdedigen tegen bedreigingen. Daarnaast kunnen klanten beveiligingsoplossingen van derden implementeren, zoals firewalls van webapplicaties, netwerkfirewalls, antimalware, inbraakdetectie, preventiesystemen (IDS/IPS) en meer. Zie [Uw gegevens en assets beveiligen en voldoen aan wereldwijde beveiligingsstandaarden](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)voor meer informatie.

Microsoft controleert continu servers, netwerken en toepassingen om bedreigingen te detecteren. Azure's multipronged threat-management aanpak maakt gebruik van inbraakdetectie, distributed denial-of-service (DDoS) aanvalspreventie, penetratietesten, gedragsanalyse, anomaliedetectie en machine learning om de verdediging voortdurend te versterken en risico's te verminderen. Microsoft Antimalware voor Azure beschermt Azure Cloud Services en virtuele machines. Daarnaast u beveiligingsoplossingen van derden implementeren, zoals brandwanden van webapplicaties, netwerkfirewalls, antimalware, inbraakdetectie- en preventiesystemen (IDS/IPS) en meer.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Http/2 inschakelen op VM voor Cloud Services?

Windows 10 en Windows Server 2016 worden geleverd met ondersteuning voor HTTP/2 aan zowel client- als serverzijde. Als uw client (browser) verbinding maakt met de IIS-server via TLS die via TLS-extensies over HTTP/2 onderhandelt, hoeft u geen wijzigingen aan te brengen aan de serverzijde. Dit komt omdat via TLS de h2-14-header die het gebruik van HTTP/2 opgeeft, standaard wordt verzonden. Als uw client daarentegen een upgrade-header stuurt om te upgraden naar HTTP/2, moet u de onderstaande wijziging aan de serverzijde aanbrengen om ervoor te zorgen dat de upgrade werkt en u een HTTP/2-verbinding krijgt. 

1. Voer regedit.exe uit.
2. Blader naar de registersleutel: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Maak een nieuwe DWORD-waarde met de naam **DuoEnabled**.
4. Stel de waarde in op 1.
5. Start de server opnieuw op.
6. Ga naar uw **standaardwebsite** en maak onder **Bindingen**een nieuwe TLS-binding met het zojuist ondertekende certificaat dat is gemaakt. 

Zie voor meer informatie:

- [HTTP/2 op IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP/2 in Windows 10: browser, apps en webserver](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Deze stappen kunnen worden geautomatiseerd via een opstarttaak, zodat wanneer een nieuwe PaaS-instantie wordt gemaakt, het de bovenstaande wijzigingen in het systeemregister kan uitvoeren. Zie [Opstarttaken configureren en uitvoeren voor een Cloud Service voor](cloud-services-startup-tasks.md)meer informatie.

 
Zodra dit is gebeurd, u controleren of de HTTP/2 is ingeschakeld of niet met behulp van een van de volgende methoden:

- Schakel de protocolversie in IIS-logboeken in en kijk in de IIS-logboeken. Het toont HTTP/2 in de logboeken. 
- Schakel F12 Developer Tool in Internet Explorer of Microsoft Edge in en schakel over naar het tabblad Netwerk om het protocol te verifiëren. 

Zie [HTTP/2 op IIS voor](https://blogs.iis.net/davidso/http2)meer informatie .

## <a name="permissions"></a>Machtigingen

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Hoe kan ik op rollen gebaseerde toegang voor Cloud Services implementeren?
Cloud Services biedt geen ondersteuning voor het RBAC-model (Role-based access control), omdat het geen op Azure Resource Manager gebaseerde service is.

Zie [De verschillende rollen in Azure begrijpen](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Extern bureaublad

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Kunnen microsoft interne engineers remote desktop naar Cloud Service-exemplaren zonder toestemming?
Microsoft volgt een strikt proces waarbij interne engineers geen extern bureaublad in uw Cloud Service kunnen opnemen zonder schriftelijke toestemming (e-mail of andere schriftelijke communicatie) van de eigenaar of hun ontwerp.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Ik kan geen extern bureaublad naar Cloud Service VM met behulp van het RDP-bestand. Ik krijg volgende fout: Er is een verificatiefout opgetreden (Code: 0x80004005)

Deze fout kan optreden als u het RDP-bestand gebruikt van een machine die is verbonden met Azure Active Directory. Volg deze stappen om dit probleem op te lossen:

1. Klik met de rechtermuisknop op het RDP-bestand dat u hebt gedownload en selecteer **Vervolgens Bewerken**.
2. Voeg '&#92;' toe als voorvoegsel voor de gebruikersnaam. Gebruik bijvoorbeeld **.\gebruikersnaam** in plaats van **gebruikersnaam**.

## <a name="scaling"></a>Schalen

### <a name="i-cannot-scale-beyond-x-instances"></a>Ik kan niet verder schalen dan X-exemplaren
Uw Azure-abonnement heeft een limiet voor het aantal cores dat u gebruiken. Schalen werkt niet als u alle beschikbare cores hebt gebruikt. Als u bijvoorbeeld een limiet van 100 cores hebt, betekent dit dat u 100 A1-formaat virtuele machine-exemplaren voor uw Cloud Service of 50 A2-formaat virtuele machine-exemplaren hebben.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Hoe kan ik Automatisch schalen configureren op basis van geheugenstatistieken?

Automatische schaal op basis van geheugenstatistieken voor een Cloud Services wordt momenteel niet ondersteund. 

Om dit probleem op te lossen, u Application Insights gebruiken. Auto-scale ondersteunt Application Insights als een metrics bron en kan het aantal rolinstanties schalen op basis van gaststatistiek zoals 'Geheugen'.  U moet Application Insights configureren in uw Cloud Service-projectpakketbestand (*.cspkg) en azure diagnostics-extensie op de service inschakelen om deze prestatie te implementeren.

Zie [Aan de slag met automatische schaal op basis van aangepaste statistiek in Azure](../azure-monitor/platform/autoscale-custom-metric.md) voor meer informatie over het gebruik van een aangepaste statistiek via Application Insights voor het configureren van automatische schaal op Cloud Services.

Zie Diagnostische gegevens voor [Cloud Service, Virtual Machine of Service Fabric verzenden naar Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md) voor meer informatie over het integreren van Azure Diagnostics met Application Insights voor cloudservices

Zie Application Insights voor Azure Cloud Services voor meer informatie over het inschakelen van Application Insights for Cloud [Services.](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Zie Diagnostische gegevens instellen voor Azure Cloud Services en virtuele machines voor meer informatie over het inschakelen van Azure Diagnostics Logging voor Cloud [Services.](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Algemeen

### <a name="how-do-i-add-nosniff-to-my-website"></a>Hoe voeg ik 'nosniff' toe aan mijn website?
Als u wilt voorkomen dat clients de MIME-typen snuiven, voegt u een instelling toe in uw *web.config-bestand.*

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

U dit ook toevoegen als een instelling in IIS. Gebruik de volgende opdracht met het algemene artikel [opstarttaken.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Hoe pas ik IIS aan voor een webrol?
Gebruik het iIS-opstartscript in het algemene [opstarttakenartikel.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Wat is de quotumlimiet voor mijn Cloud Service?
Zie [Servicespecifieke limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Waarom toont het station op mijn Cloud Service VM zeer weinig vrije schijfruimte?
Dit wordt verwacht gedrag, en het mag geen probleem veroorzaken aan uw toepassing. Journaling is ingeschakeld voor het %approot%-station in Azure PaaS VM's, dat in wezen het dubbele van de hoeveelheid ruimte verbruikt die bestanden normaal gesproken in beslag nemen. Er zijn echter verschillende dingen om zich bewust van te zijn dat in wezen zet dit in een non-issue.

De schijfgrootte %approot% \<wordt berekend als grootte van .cspkg + maximale journaalgrootte + een marge van vrije ruimte> of 1,5 GB, naar gelang van het jaar groter. De grootte van uw VM heeft geen invloed op deze berekening. (De VM-grootte heeft alleen invloed op de grootte van de tijdelijke C: station.) 

Het wordt niet ondersteund om naar het station %approot% te schrijven. Als u naar de Azure VM schrijft, moet u dit doen in een tijdelijke LocalStorage-bron (of een andere optie, zoals Blob-opslag, Azure-bestanden, enz.). Dus de hoeveelheid vrije ruimte op de map %approot% is niet zinvol. Als u niet zeker weet of uw toepassing naar het station %approot% schrijft, u uw service altijd een paar dagen laten draaien en vervolgens de grootte 'voor' en 'na' vergelijken. 

Azure schrijft niets naar het station %approot%. Zodra de VHD is gemaakt van uw .cspkg en gemonteerd in de Azure VM, het enige dat zou kunnen schrijven naar dit station is uw toepassing. 

De logboekinstellingen zijn niet configureerbaar, dus u deze niet uitschakelen.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Hoe kan ik een Antimalware-extensie voor mijn Cloud Services op een geautomatiseerde manier toevoegen?

U antimalware-extensie inschakelen met PowerShell-script in de opstarttaak. Volg de stappen in deze artikelen om het te implementeren: 
 
- [Een PowerShell-opstarttaak maken](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Zie [Antimalware Deployment Scenarios](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)voor meer informatie over antimalware-implementatiescenario's en hoe u deze inschakelen via de portal.

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Hoe schakel je Server Name Indication (SNI) in voor Cloud Services?

U SNI in Cloud Services inschakelen met een van de volgende methoden:

**Methode 1: PowerShell gebruiken**

De SNI-binding kan worden geconfigureerd met de PowerShell-cmdlet **New-WebBinding** in een opstarttaak voor een functie-instantie CloudService zoals hieronder:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Zoals [hier](https://technet.microsoft.com/library/ee790567.aspx)beschreven, kan de $sslFlags een van de waarden als volgt zijn:

|Waarde|Betekenis|
------|------
|0|Geen SNI|
|1|SNI ingeschakeld|
|2|Niet SNI-binding die het Centrale Certificaatarchief gebruikt|
|3|SNI-binding die het centrale certificaatarchief gebruikt|
 
**Methode 2: Code gebruiken**

De SNI-binding kan ook worden geconfigureerd via code in de rol opstarten zoals beschreven in deze [blogpost:](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/)

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                    binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Met behulp van een van de bovenstaande benaderingen moeten de respectieve certificaten (*.pfx) voor de specifieke hostnamen eerst worden geïnstalleerd op de rolinstanties die een opstarttaak of via code gebruiken om de SNI-binding effectief te laten zijn.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Hoe kan ik tags toevoegen aan mijn Azure Cloud Service? 

Cloud Service is een klassieke bron. Alleen resources die zijn gemaakt via Azure Resource Manager-ondersteuningstags. U geen tags toepassen op Klassieke bronnen zoals Cloud Service. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>De Azure-portal geeft niet de SDK-versie van mijn Cloud Service weer. Hoe kan ik dat krijgen?

We werken eraan om deze functie op de Azure-portal te brengen. Ondertussen u volgende PowerShell-opdrachten gebruiken om de SDK-versie te krijgen:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Ik wil de Cloud Service enkele maanden afsluiten. Hoe u de factureringskosten van Cloud Service verlagen zonder het IP-adres te verliezen?

Een reeds geïmplementeerde Cloud Service wordt gefactureerd voor de Compute en Storage die wordt gebruikt. Dus zelfs als u de Azure VM afsluit, wordt u nog steeds gefactureerd voor de opslag. 

Dit is wat u doen om uw facturering te verminderen zonder het IP-adres voor uw service te verliezen:

1. [Reserveer het IP-adres](../virtual-network/virtual-networks-reserved-public-ip.md) voordat u de implementaties verwijdert.  Dit IP-adres wordt alleen in rekening gebracht. Zie Prijzen voor IP-adressen voor meer informatie over facturering via [IP-adres.](https://azure.microsoft.com/pricing/details/ip-addresses/)
2. Verwijder de implementaties. Verwijder de xxx.cloudapp.net niet, zodat u deze in de toekomst gebruiken.
3. Zie [Gereserveerde IP-adressen voor Cloud Services en Virtuele Machines](https://azure.microsoft.com/blog/reserved-ip-addresses/)als u de cloudservice opnieuw wilt implementeren met hetzelfde reserve-IP-ip-adres dat u in uw abonnement hebt gereserveerd.

