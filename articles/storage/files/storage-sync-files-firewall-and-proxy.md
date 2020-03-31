---
title: On-premises firewall- en proxy-instellingen voor Azure File Sync | Microsoft Documenten
description: On-premises netwerkconfiguratie azure file sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f398012edc25ba6a04e230fa8049e7264f857bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294527"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Proxy- en firewallinstellingen van Azure File Sync
Azure File Sync verbindt uw on-premises servers met Azure Files, waardoor functies voor synchronisatie met meerdere locaties en cloudtiering mogelijk zijn. Als zodanig moet een on-premises server zijn aangesloten op het internet. Een IT-beheerder moet beslissen welk pad de server het beste kan bereiken in Azure-cloudservices.

In dit artikel vindt u inzicht in specifieke vereisten en opties die beschikbaar zijn om uw server succesvol en veilig te verbinden met Azure File Sync.

## <a name="overview"></a>Overzicht
Azure File Sync fungeert als een orchestration-service tussen uw Windows Server, uw Azure-bestandsshare en verschillende andere Azure-services om gegevens te synchroniseren zoals beschreven in uw synchronisatiegroep. Als Azure File Sync correct werkt, moet u uw servers configureren om te communiceren met de volgende Azure-services:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Verificatieservices

> [!Note]  
> De Azure File Sync-agent op Windows Server initieert alle aanvragen voor cloudservices, waardoor u alleen uitgaand verkeer vanuit een firewallperspectief hoeft te overwegen. <br /> Geen Azure-service start een verbinding met de Azure File Sync-agent.

## <a name="ports"></a>Poorten
Azure File Sync verplaatst bestandsgegevens en metagegevens uitsluitend via HTTPS en vereist dat poort 443 outbound is geopend.
Hierdoor wordt al het verkeer versleuteld.

## <a name="networks-and-special-connections-to-azure"></a>Netwerken en speciale verbindingen met Azure
De Azure File Sync-agent heeft geen vereisten met betrekking tot speciale kanalen zoals [ExpressRoute](../../expressroute/expressroute-introduction.md), enz.

Azure File Sync werkt met alle beschikbare middelen die bereik in Azure mogelijk maken, automatisch aanpassen aan verschillende netwerkkenmerken zoals bandbreedte, latentie en het bieden van beheerbeheer voor fine-tuning. Niet alle functies zijn beschikbaar op dit moment. Als u specifiek gedrag wilt configureren, laat het ons dan weten via [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync ondersteunt app-specifieke en machinebrede proxy-instellingen.

**App-specifieke proxy-instellingen** maken configuratie van een proxy specifiek voor Azure File Sync-verkeer mogelijk. App-specifieke proxy-instellingen worden ondersteund op agentversie 4.0.1.0 of nieuwer en kunnen worden geconfigureerd tijdens de installatie van de agent of met behulp van de PowerShell-cmdlet set-StorageSyncConfiguration.

PowerShell-opdrachten om app-specifieke proxy-instellingen te configureren:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Machinebrede proxy-instellingen** zijn transparant voor de Azure File Sync-agent, omdat het volledige verkeer van de server wordt doorgestuurd via de proxy.

Volg de onderstaande stappen om proxy-instellingen voor de hele machine te configureren: 

1. Proxy-instellingen configureren voor .NET-toepassingen 

   - Bewerk deze twee bestanden:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Voeg de sectie <system.net> toe in de machine.config-bestanden (onder de sectie <system.serviceModel>).  Wijzig 127.0.01:8888 naar het IP-adres en de poort voor de proxyserver. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. De winhttp-proxy-instellingen instellen 

   - Voer de volgende opdracht uit vanuit een opdrachtprompt met verhoogde bevoegdheid of PowerShell om de bestaande proxy-instelling te bekijken:   

     netsh winhttp show proxy

   - Voer de volgende opdracht uit vanaf een opdrachtprompt met verhoogde bevoegdheid of PowerShell om de proxy-instelling in te stellen (wijzig 127.0.01:8888 naar het IP-adres en de poort voor de proxyserver):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Start de service Storage Sync Agent opnieuw door de volgende opdracht uit te voeren vanuit een opdrachtprompt met verhoogde bevoegdheid of PowerShell: 

      net stop filesyncsvc

      Opmerking: de service Storage Sync Agent (filesyncsvc) wordt automatisch gestart nadat deze is gestopt.

## <a name="firewall"></a>Firewall
Zoals in een vorige sectie vermeld, moet poort 443 outbound open zijn. Op basis van beleid in uw datacenter, branche of regio kan het gewenst of vereist zijn om het verkeer over deze poort verder te beperken tot specifieke domeinen.

In de volgende tabel worden de vereiste domeinen voor communicatie beschreven:

| Service | Eindpunt openbare cloud | Eindpunt azure-overheid | Gebruik |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Elke gebruikersoproep (zoals PowerShell) gaat naar/via deze URL, inclusief de eerste serverregistratieoproep. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager-aanroepen moeten worden uitgevoerd door een geverifieerde gebruiker. Om te slagen, wordt deze URL gebruikt voor gebruikersverificatie. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Als onderdeel van het implementeren van Azure File Sync wordt een serviceprincipal in de Azure Active Directory van het abonnement gemaakt. Deze URL wordt daarvoor gebruikt. Deze principal wordt gebruikt voor het delegeren van een minimale set rechten op de Azure File Sync-service. De gebruiker die de eerste installatie van Azure File Sync uitvoert, moet een geverifieerde gebruiker zijn met bevoegdheden voor de eigenaar van het abonnement. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Wanneer de server een bestand downloadt, voert de server die gegevensbeweging efficiënter uit wanneer deze rechtstreeks met het Azure-bestandsaandeel in het opslagaccount wordt gesproken. De server heeft een SAS-sleutel die alleen gerichte toegang tot bestandsdelen mogelijk maakt. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | Na de eerste serverregistratie ontvangt de server een regionale URL voor het azure file sync-serviceexemplaar in die regio. De server kan de URL gebruiken om direct en efficiënt te communiceren met de instantie die de synchronisatie ervan verwerkt. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Zodra de Azure File Sync-agent is geïnstalleerd, wordt de PKI-URL gebruikt om tussenliggende certificaten te downloaden die nodig zijn om te communiceren met de Azure File Sync-service en Azure-bestandsshare. De OCSP-URL wordt gebruikt om de status van een certificaat te controleren. |

> [!Important]
> Wanneer verkeer wordt &ast;toegestaan op .one.microsoft.com, is verkeer naar meer dan alleen de synchronisatieservice mogelijk vanaf de server. Er zijn veel meer Microsoft-services beschikbaar onder subdomeinen.

Als &ast;.one.microsoft.com te breed is, u de communicatie van de server beperken door communicatie toe te staan tot alleen expliciete regionale exemplaren van de Azure Files Sync-service. Welke instantie(s) u wilt kiezen, is afhankelijk van het gebied van de opslagsynchronisatieservice waarop u de server hebt geïmplementeerd en geregistreerd. Dat gebied wordt in de onderstaande tabel 'URL voor primair eindpunt' genoemd.

Om redenen van bedrijfscontinuïteit en disaster recovery (BCDR) hebt u mogelijk uw Azure-bestandsshares opgegeven in een GRS-opslagaccount (globally redundant). Als dat het geval is, worden uw Azure-bestandsshares niet meer overgeslagen naar de gekoppelde regio in het geval van een langdurige regionale storing. Azure File Sync gebruikt dezelfde regionale koppelingen als opslag. Als u DUS GRS-opslagaccounts gebruikt, moet u extra URL's inschakelen om uw server in staat te stellen met het gekoppelde gebied te praten voor Azure File Sync. De onderstaande tabel noemt dit "Gekoppelde gebied". Daarnaast is er een URL van het verkeersmanagerprofiel die ook moet worden ingeschakeld. Dit zorgt ervoor dat netwerkverkeer naadloos kan worden omgeleid naar het gekoppelde gebied in het geval van een fail-over en wordt in de onderstaande tabel 'Discovery URL' genoemd.

| Cloud  | Regio | URL voor primair eindpunt | Gekoppelde regio | Url van detectie |
|--------|--------|----------------------|---------------|---------------|
| Public |Australië - oost | https:\//kailani-aue.one.microsoft.com | Australië - zuidoost | https:\//tm-kailani-aue.one.microsoft.com |
| Public |Australië - zuidoost | https:\//kailani-aus.one.microsoft.com | Australië - oost | https:\//tm-kailani-aus.one.microsoft.com |
| Public | Brazilië - zuid | https:\//brazilsouth01.afs.azure.net | VS - zuid-centraal | https:\//tm-brazilsouth01.afs.azure.net |
| Public | Canada - midden | https:\//kailani-cac.one.microsoft.com | Canada - oost | https:\//tm-kailani-cac.one.microsoft.com |
| Public | Canada - oost | https:\//kailani-cae.one.microsoft.com | Canada - midden | https:\//tm-kailani.cae.one.microsoft.com |
| Public | India - centraal | https:\//kailani-cin.one.microsoft.com | India - zuid | https:\//tm-kailani-cin.one.microsoft.com |
| Public | VS - centraal | https:\//kailani-cus.one.microsoft.com | VS - oost 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Public | Azië - oost | https:\//kailani11.one.microsoft.com | Azië - zuidoost | https:\//tm-kailani11.one.microsoft.com |
| Public | VS - oost | https:\//kailani1.one.microsoft.com | VS - west | https:\//tm-kailani1.one.microsoft.com |
| Public | VS - oost 2 | https:\//kailani-ess.one.microsoft.com | VS - centraal | https:\//tm-kailani-ess.one.microsoft.com |
| Public | Japan - oost | https:\//japaneast01.afs.azure.net | Japan - west | https:\//tm-japaneast01.afs.azure.net |
| Public | Japan - west | https:\//japanwest01.afs.azure.net | Japan - oost | https:\//tm-japanwest01.afs.azure.net |
| Public | Korea - centraal | https:\//koreacentral01.afs.azure.net/ | Korea - zuid | https:\//tm-koreacentral01.afs.azure.net/ |
| Public | Korea - zuid | https:\//koreasouth01.afs.azure.net/ | Korea - centraal | https:\//tm-koreasouth01.afs.azure.net/ |
| Public | VS - noord-centraal | https:\//northcentralus01.afs.azure.net | VS - zuid-centraal | https:\//tm-northcentralus01.afs.azure.net |
| Public | Europa - noord | https:\//kailani7.one.microsoft.com | Europa -west | https:\//tm-kailani7.one.microsoft.com |
| Public | VS - zuid-centraal | https:\//southcentralus01.afs.azure.net | VS - noord-centraal | https:\//tm-southcentralus01.afs.azure.net |
| Public | India - zuid | https:\//kailani-sin.one.microsoft.com | India - centraal | https:\//tm-kailani-sin.one.microsoft.com |
| Public | Azië - zuidoost | https:\//kailani10.one.microsoft.com | Azië - oost | https:\//tm-kailani10.one.microsoft.com |
| Public | Verenigd Koninkrijk Zuid | https:\//kailani-uks.one.microsoft.com | Verenigd Koninkrijk West | https:\//tm-kailani-uks.one.microsoft.com |
| Public | Verenigd Koninkrijk West | https:\//kailani-ukw.one.microsoft.com | Verenigd Koninkrijk Zuid | https:\//tm-kailani-ukw.one.microsoft.com |
| Public | VS - west-centraal | https:\//westcentralus01.afs.azure.net | VS - west 2 | https:\//tm-westcentralus01.afs.azure.net |
| Public | Europa -west | https:\//kailani6.one.microsoft.com | Europa - noord | https:\//tm-kailani6.one.microsoft.com |
| Public | VS - west | https:\//kailani.one.microsoft.com | VS - oost | https:\//tm-kailani.one.microsoft.com |
| Public | VS - west 2 | https:\//westus201.afs.azure.net | VS - west-centraal | https:\//tm-westus201.afs.azure.net |
| Overheid | VS (overheid) - Arizona | https:\//usgovarizona01.afs.azure.us | VS (overheid) - Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Overheid | VS (overheid) - Texas | https:\//usgovtexas01.afs.azure.us | VS (overheid) - Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Als u lokaal redundante (LRS) of zone redundante (ZRS)-opslagaccounts gebruikt, hoeft u alleen de URL in te schakelen die wordt vermeld onder 'Url van primair eindpunt'.

- Als u grs-opslagaccounts (globally redundante) gebruikt, schakelt u drie URL's in.

**Voorbeeld:** U implementeert een `"West US"` opslagsynchronisatieservice in en registreert uw server ermee. De URL's waarmee de server in dit geval kan communiceren, zijn:

> - https:\//kailani.one.microsoft.com (primair eindpunt: West US)
> - https:\//kailani1.one.microsoft.com (gekoppelde fail-over regio: Oost-VS)
> - https:\//tm-kailani.one.microsoft.com (detectie-URL van het primaire gebied)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Lijst toestaan voor AZURE File Sync IP-adressen
Azure File Sync ondersteunt het gebruik van [servicetags,](../../virtual-network/service-tags-overview.md)die een groep IP-adresvoorvoegsels voor een bepaalde Azure-service vertegenwoordigen. U servicetags gebruiken om firewallregels te maken die communicatie met de Azure File Sync-service mogelijk maken. De servicetag voor Azure `StorageSyncService`File Sync is .

Als u Azure File Sync binnen Azure gebruikt, u de naam van de servicetag rechtstreeks in uw netwerkbeveiligingsgroep gebruiken om verkeer toe te staan. Zie [Netwerkbeveiligingsgroepen](../../virtual-network/security-overview.md)voor meer informatie over hoe u dit doen.

Als u on-premises Azure File Sync gebruikt, u de API voor servicetags gebruiken om specifieke IP-adresbereiken te krijgen voor de lijst met toestaan van uw firewall. Er zijn twee methoden voor het verkrijgen van deze informatie:

- De huidige lijst met IP-adresbereiken voor alle Azure-services die servicetags ondersteunen, wordt wekelijks gepubliceerd in het Microsoft Download Center in de vorm van een JSON-document. Elke Azure-cloud heeft zijn eigen JSON-document met de IP-adresbereiken die relevant zijn voor die cloud:
    - [Azure-openbaar](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure Amerikaanse overheid](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)
- De API voor het ontdekken van servicetags (preview) maakt het programmeerbaar ophalen van de huidige lijst met servicetags mogelijk. In preview kan de API voor het ontdekken van servicetags informatie retourneren die minder actueel is dan informatie die is geretourneerd uit de JSON-documenten die zijn gepubliceerd in het Microsoft DownloadCenter. U het API-oppervlak gebruiken op basis van uw automatiseringsvoorkeur:
    - [REST-API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure-CLI](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Omdat de API voor het ontdekken van servicetags niet zo vaak wordt bijgewerkt als de JSON-documenten die zijn gepubliceerd in het Microsoft Download Center, raden we u aan het JSON-document te gebruiken om de lijst met on-premises firewalls bij te werken. Dit kan als volgt:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

U vervolgens de IP-adresbereiken gebruiken `$ipAddressRanges` om uw firewall bij te werken. Controleer de website van uw firewall/netwerktoestel voor informatie over het bijwerken van uw firewall.

## <a name="test-network-connectivity-to-service-endpoints"></a>Netwerkconnectiviteit testen met serviceeindpunten
Zodra een server is geregistreerd bij de Azure File Sync-service, kunnen de cmdlet Test-StorageSyncNetworkConnectivity en ServerRegistration.exe worden gebruikt om communicatie te testen met alle eindpunten (URL's) die specifiek zijn voor deze server. Deze cmdlet kan helpen problemen op te lossen wanneer onvolledige communicatie voorkomt dat de server volledig werkt met Azure File Sync en kan worden gebruikt om proxy- en firewallconfiguraties te verfijnen.

Als u de netwerkverbindingstest wilt uitvoeren, installeert u Azure File Sync agent versie 9.1 of hoger en voert u de volgende PowerShell-opdrachten uit:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Samenvatting en risicobeperking
De lijsten eerder in dit document bevatten de URL's waarmee Azure File Sync momenteel communiceert. Firewalls moeten verkeer naar deze domeinen kunnen toestaan. Microsoft streeft ernaar deze lijst bijgewerkt te houden.

Het instellen van domeinbeperkende firewallregels kan een maatregel zijn om de beveiliging te verbeteren. Als deze firewallconfiguraties worden gebruikt, moet men er rekening mee houden dat URL's worden toegevoegd en in de loop van de tijd zelfs kunnen veranderen. Controleer dit artikel regelmatig.

## <a name="next-steps"></a>Volgende stappen
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
- [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
