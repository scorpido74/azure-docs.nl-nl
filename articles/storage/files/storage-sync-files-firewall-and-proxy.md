---
title: Azure File Sync on-premises firewall en proxy-instellingen | Microsoft Docs
description: Azure File Sync on-premises netwerk configuratie
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dcf6160c3650975431bf50fcf5bcba67f833a717
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750445"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Proxy- en firewallinstellingen van Azure File Sync
Azure File Sync maakt verbinding met uw on-premises servers met Azure Files, waarbij functies voor multi-site synchronisatie en Cloud lagen worden ingeschakeld. Als zodanig moet een on-premises server zijn verbonden met internet. Een IT-beheerder moet het beste pad bepalen dat de server kan bereiken in azure Cloud Services.

In dit artikel vindt u inzicht in specifieke vereisten en opties die beschikbaar zijn voor een geslaagde en veilige verbinding van uw server met Azure File Sync.

## <a name="overview"></a>Overzicht
Azure File Sync fungeert als een Orchestration-service tussen uw Windows-Server, uw Azure-bestands share en verschillende andere Azure-Services om gegevens te synchroniseren zoals beschreven in de synchronisatie groep. Azure File Sync om goed te laten werken, moet u uw servers configureren om te communiceren met de volgende Azure-Services:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Verificatie services

> [!Note]  
> De Azure File Sync-agent op Windows server initieert alle aanvragen voor Cloud Services, waardoor het uitgaande verkeer van een firewall perspectief alleen hoeft te worden overwogen. <br /> Geen enkele Azure-service initieert een verbinding met de Azure File Sync-agent.

## <a name="ports"></a>Poorten
Azure File Sync verplaatst bestands gegevens en meta gegevens uitsluitend via HTTPS en vereist dat poort 443 open-uitgaand moet zijn.
Als gevolg hiervan wordt al het verkeer versleuteld.

## <a name="networks-and-special-connections-to-azure"></a>Netwerken en speciale verbindingen met Azure
De Azure File Sync-agent heeft geen vereisten met betrekking tot speciale kanalen zoals [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. naar Azure.

Azure File Sync beschik bare manieren om in azure te kunnen bereiken, automatisch aan verschillende netwerk kenmerken, zoals band breedte, latentie en het bieden van beheer beheer voor het afstemmen. Niet alle functies zijn op dit moment beschikbaar. Als u specifiek gedrag wilt configureren, laat u ons weten via [Azure files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync biedt ondersteuning voor app-specifieke proxy-instellingen voor de hele machine.

Met **app-specifieke proxy-instellingen** kunt u een proxy configureren die specifiek is voor Azure file sync verkeer. App-specifieke proxy-instellingen worden ondersteund op Agent versie 4.0.1.0 of hoger en kunnen worden geconfigureerd tijdens de installatie van de agent of met behulp van de Power shell-cmdlet Set-StorageSyncProxyConfiguration.

Power shell-opdrachten voor het configureren van app-specifieke proxy-instellingen:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Proxy-instellingen** voor alle computers zijn transparant voor de Azure file sync-agent, omdat het hele verkeer van de server via de proxy wordt doorgestuurd.

Volg de onderstaande stappen voor het configureren van proxy-instellingen voor alle computers: 

1. Proxy-instellingen voor .NET-toepassingen configureren 

   - Bewerk deze twee bestanden:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Voeg de sectie < System. net > in de machine. config-bestanden toe (onder het gedeelte < System. service model >).  Wijzig 127.0.01:8888 in het IP-adres en de poort voor de proxy server. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. De WinHTTP-proxy-instellingen instellen 

   - Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid of Power shell om de bestaande proxy-instelling te bekijken:   

     Netsh WinHTTP show proxy

   - Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid of Power shell om de proxy instelling in te stellen (127.0.01:8888 wijzigen in het IP-adres en de poort voor de proxy server):  

     Netsh WinHTTP set proxy 127.0.0.1:8888

3. Start de opslag synchronisatie Agent-service opnieuw door de volgende opdracht uit te voeren vanaf een opdracht prompt met verhoogde bevoegdheid of Power shell: 

      net stop filesyncsvc

      Opmerking: de opslag synchronisatie agent (filesyncsvc) wordt automatisch gestart zodra de service is gestopt.

## <a name="firewall"></a>Firewall
Zoals vermeld in een vorige sectie, moet poort 443 open-uitgaand zijn. Afhankelijk van het beleid in uw Data Center, vertakking of regio kunt u het verkeer dat via deze poort wordt beperkt tot specifieke domeinen, mogelijk gewenst of vereist.

In de volgende tabel worden de vereiste domeinen voor communicatie beschreven:

| Service | Open bare-Cloud eindpunt | Azure Government-eind punt | Gebruik |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Elke gebruikers aanroep (zoals Power shell) gaat naar/via deze URL, met inbegrip van de eerste registratie aanroep van de server. |
| **Azure Active Directory** | https://login.windows.net<br>https://login.microsoftonline.com | https://login.microsoftonline.us | Azure Resource Manager-aanroepen moeten worden uitgevoerd door een geverifieerde gebruiker. Deze URL wordt gebruikt voor gebruikers verificatie. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Als onderdeel van de implementatie van Azure File Sync wordt een Service-Principal in de Azure Active Directory van het abonnement gemaakt. Deze URL wordt gebruikt voor die. Deze principal wordt gebruikt voor het delegeren van een minimale set rechten voor de Azure File Sync-Service. De gebruiker die de eerste installatie van Azure File Sync uitvoert, moet een geverifieerde gebruiker zijn met de bevoegdheid eigenaar van het abonnement. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Wanneer een bestand door de server wordt gedownload, voert de server de gegevens efficiënter uit wanneer deze rechtstreeks met de Azure-bestands share in het opslag account communiceren. De server heeft een SAS-sleutel die alleen toegang tot de doel bestands shares toestaat. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;. afs.azure.net | &ast;.afs.azure.us | Na de eerste registratie van de server ontvangt de server een regionale URL voor het Azure File Sync service-exemplaar in die regio. De-server kan de URL gebruiken om rechtstreeks en efficiënt te communiceren met het exemplaar dat de synchronisatie afhandelt. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Zodra de Azure File Sync-agent is geïnstalleerd, wordt de PKI-URL gebruikt voor het downloaden van tussenliggende certificaten die nodig zijn om te communiceren met de Azure File Sync-Service en de Azure-bestands share. De OCSP-URL wordt gebruikt om de status van een certificaat te controleren. |

> [!Important]
> Als verkeer naar &ast;. one.microsoft.com wordt toegestaan, kan verkeer naar meer dan alleen de synchronisatie service van de server mogelijk zijn. Er zijn veel meer micro soft-services beschikbaar onder subdomeinen.

Als &ast;. one.microsoft.com te breed is, kunt u de communicatie van de server beperken door communicatie toe te staan alleen expliciete regionale instanties van de Azure Files-synchronisatie service. Welk exemplaar (en) u kiest, is afhankelijk van de regio van de opslag synchronisatie service die u hebt geïmplementeerd en de server hebt geregistreerd bij. Deze regio wordt ' primaire eind punt-URL ' genoemd in de volgende tabel.

Voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) kunt u uw Azure-bestands shares opgeven in een globaal redundante (GRS)-opslag account. Als dat het geval is, zal de Azure-bestands shares een failover uitvoeren naar de gekoppelde regio in het geval van een blijvende regionale storing. Azure File Sync gebruikt dezelfde regionale koppeling als opslag. Als u dus GRS-opslag accounts gebruikt, moet u extra Url's inschakelen zodat uw server kan communiceren met de gekoppelde regio voor Azure File Sync. In de volgende tabel wordt deze ' gekoppelde regio ' aangeroepen. Daarnaast is er een Traffic Manager-profiel-URL die ook moet worden ingeschakeld. Zo zorgt u ervoor dat netwerk verkeer naadloos opnieuw kan worden gerouteerd naar de gekoppelde regio in het geval van een failover en wordt "detectie-URL" genoemd in de onderstaande tabel.

| Cloud  | Regio | URL van het primaire eind punt | Gekoppelde regio | Detectie-URL |
|--------|--------|----------------------|---------------|---------------|
| Public |Australië - oost | https:\/-kailani-aue.one.microsoft.com | Australië - zuidoost | https:\/-tm-kailani-aue.one.microsoft.com |
| Public |Australië - zuidoost | https:\/-kailani-aus.one.microsoft.com | Australië - oost | https:\/-tm-kailani-aus.one.microsoft.com |
| Public | Brazilië - Zuid | https:\//brazilsouth01.afs.azure.net | VS - zuid-centraal | https:\/-tm-brazilsouth01.afs.azure.net |
| Public | Canada-Midden | https:\/-kailani-cac.one.microsoft.com | Canada-Oost | https:\/-tm-kailani-cac.one.microsoft.com |
| Public | Canada-Oost | https:\/-kailani-cae.one.microsoft.com | Canada-Midden | https:\/-tm-kailani.cae.one.microsoft.com |
| Public | India - centraal | https:\/-kailani-cin.one.microsoft.com | India - zuid | https:\/-tm-kailani-cin.one.microsoft.com |
| Public | VS - centraal | https:\/-kailani-cus.one.microsoft.com | VS - oost 2 | https:\/-tm-kailani-cus.one.microsoft.com |
| Public | Azië - oost | https:\//kailani11.one.microsoft.com | Azië - zuidoost | https:\/-tm-kailani11.one.microsoft.com |
| Public | VS - oost | https:\//kailani1.one.microsoft.com | VS - west | https:\/-tm-kailani1.one.microsoft.com |
| Public | VS - oost 2 | https:\/-kailani-ess.one.microsoft.com | VS - centraal | https:\/-tm-kailani-ess.one.microsoft.com |
| Public | Japan - Oost | https:\/-japaneast01.afs.azure.net | Japan - West | https:\/-tm-japaneast01.afs.azure.net |
| Public | Japan - West | https:\/-japanwest01.afs.azure.net | Japan - Oost | https:\/-tm-japanwest01.afs.azure.net |
| Public | Korea - centraal | https:\/-koreacentral01.afs.azure.net/ | Korea - zuid | https:\/-tm-koreacentral01.afs.azure.net/ |
| Public | Korea - zuid | https:\//koreasouth01.afs.azure.net/ | Korea - centraal | https:\//tm-koreasouth01.afs.azure.net/ |
| Public | VS - noord-centraal | https:\/-northcentralus01.afs.azure.net | VS - zuid-centraal | https:\/-tm-northcentralus01.afs.azure.net |
| Public | Europa - noord | https:\/-kailani7.one.microsoft.com | Europa - west | https:\/-tm-kailani7.one.microsoft.com |
| Public | VS - zuid-centraal | https:\/-southcentralus01.afs.azure.net | VS - noord-centraal | https:\/-tm-southcentralus01.afs.azure.net |
| Public | India - zuid | https:\/-kailani-sin.one.microsoft.com | India - centraal | https:\/-tm-kailani-sin.one.microsoft.com |
| Public | Azië - zuidoost | https:\/-kailani10.one.microsoft.com | Azië - oost | https:\/-tm-kailani10.one.microsoft.com |
| Public | UK - zuid | https:\/-kailani-uks.one.microsoft.com | UK - west | https:\/-tm-kailani-uks.one.microsoft.com |
| Public | UK - west | https:\/-kailani-ukw.one.microsoft.com | UK - zuid | https:\/-tm-kailani-ukw.one.microsoft.com |
| Public | VS - west-centraal | https:\/-westcentralus01.afs.azure.net | VS - west 2 | https:\/-tm-westcentralus01.afs.azure.net |
| Public | Europa - west | https:\//kailani6.one.microsoft.com | Europa - noord | https:\/-tm-kailani6.one.microsoft.com |
| Public | VS - west | https:\//kailani.one.microsoft.com | VS - oost | https:\/-tm-kailani.one.microsoft.com |
| Public | VS - west 2 | https:\/-westus201.afs.azure.net | VS - west-centraal | https:\//tm-westus201.afs.azure.net |
| Overheid | US Gov - Arizona | https:\/-usgovarizona01.afs.azure.us | US Gov - Texas | https:\/-tm-usgovarizona01.afs.azure.us |
| Overheid | US Gov - Texas | https:\//usgovtexas01.afs.azure.us | US Gov - Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Als u lokaal redundante (LRS) of zone redundante (ZRS) opslag accounts gebruikt, hoeft u alleen de URL in te scha kelen die wordt weer gegeven onder URL van het primaire eind punt.

- Als u wereld wijd redundante opslag accounts (GRS) gebruikt, schakelt u drie Url's in.

**Voor beeld:** U implementeert een opslag synchronisatie service in `"West US"` en registreert de server hieraan. De Url's waarmee de server kan communiceren voor deze aanvraag:

> - https:\//kailani.one.microsoft.com (primair eind punt: VS-West)
> - https:\/-kailani1.one.microsoft.com (gekoppelde failover-over regio: VS-Oost)
> - https:\//tm-kailani.one.microsoft.com (detectie-URL van de primaire regio)

## <a name="test-network-connectivity-to-service-endpoints"></a>De netwerk verbinding met Service-eind punten testen
Zodra een server is geregistreerd bij de Azure File Sync-Service, kan de cmdlet test-StorageSyncNetworkConnectivity en ServerRegistration. exe worden gebruikt om de communicatie met alle eind punten (Url's) die voor deze server gelden, te testen. Met deze cmdlet kunt u problemen oplossen wanneer de server niet volledig met Azure File Sync werkt en kan worden gebruikt voor het afstemmen van proxy-en firewall configuraties.

Als u de test voor de netwerk verbinding wilt uitvoeren, installeert u Azure File Sync agent versie 9,1 of hoger en voert u de volgende Power shell-opdrachten uit:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Samen vatting en risico beperking
De lijsten eerder in dit document bevatten de Url's Azure File Sync momenteel communiceert met. Firewalls moeten uitgaand verkeer naar deze domeinen kunnen toestaan. Micro soft streeft ernaar om deze lijst bijgewerkt te laten worden.

Het instellen van domein beperking van firewall regels kan een maat eenheid zijn voor het verbeteren van de beveiliging. Als deze firewall configuraties worden gebruikt, moet er een of meer Url's worden toegevoegd. Dit kan zelfs na verloop van tijd worden gewijzigd. Controleer dit artikel regel matig.

## <a name="next-steps"></a>Volgende stappen
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
- [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
