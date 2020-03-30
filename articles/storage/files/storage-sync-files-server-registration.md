---
title: Geregistreerde servers beheren met Azure File Sync | Microsoft Documenten
description: Meer informatie over het registreren en uitschrijven van een Windows Server met een Azure File Sync Storage Sync Service.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2656716560b981481273c3032fc0c7b1a06be8a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255091"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Geregistreerde servers beheren met Azure File Sync
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Dit gebeurt door uw Windows-servers om te zetten in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als waar ook ter wereld u nodig hebt.

In het volgende artikel wordt uitgelegd hoe u een server registreert en beheert met een Storage Sync-service. Zie [Azure File Sync implementeren](storage-sync-files-deployment-guide.md) voor informatie over het end-to-end implementeren van Azure File Sync.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Een server registreren/uitschrijven met Storage Sync Service
Als u een server registreert met Azure File Sync, wordt een vertrouwensrelatie tussen Windows Server en Azure tot gevolg. Deze relatie kan vervolgens worden gebruikt om *servereindpunten* op de server te maken, die specifieke mappen vertegenwoordigen die moeten worden gesynchroniseerd met een Azure-bestandsshare (ook wel een eindpunt in de *cloud genoemd).* 

### <a name="prerequisites"></a>Vereisten
Als u een server wilt registreren bij een Storage Sync Service, moet u uw server eerst voorbereiden met de vereiste vereisten:

* Op uw server moet een ondersteunde versie van Windows Server worden uitgevoerd. Zie Azure [File Sync-systeemvereisten en interoperabiliteit](storage-sync-files-planning.md#windows-file-server-considerations)voor meer informatie.
* Controleer of er een Storage Sync-service is geïmplementeerd. Zie [Azure File Sync implementeren](storage-sync-files-deployment-guide.md)voor meer informatie over het implementeren van een Opslagsynchronisatieservice.
* Controleer of de server is verbonden met internet en dat Azure toegankelijk is.
* Schakel de IE Enhanced Security Configuration voor beheerders uit met de gebruikersinterface serverbeheer.
    
    ![Gebruikersinterface serverbeheer met de iE-verbeterde beveiligingsconfiguratie gemarkeerd](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Controleer of de Azure PowerShell-module op uw server is geïnstalleerd. Als uw server lid is van een Failovercluster, is voor elk knooppunt in het cluster de Az-module vereist. Meer informatie over het installeren van de Az-module vindt u op de [Installatie en configureer Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-Az-ps)

    > [!Note]  
    > We raden u aan de nieuwste versie van de Az PowerShell-module te gebruiken om een server te registreren/uitte registreren. Als het Az-pakket eerder op deze server is geïnstalleerd (en de PowerShell-versie op `Update-Module` deze server 5.* of meer is), u de cmdlet gebruiken om dit pakket bij te werken. 
* Als u een netwerkproxyserver in uw omgeving gebruikt, configureert u proxy-instellingen op uw server voor het gebruik van de synchronisatieagent.
    1. Uw proxy-IP-adres en poortnummer bepalen
    2. Bewerk deze twee bestanden:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Voeg de regels in figuur 1 (onder deze sectie) onder /System.ServiceModel in de bovenstaande twee bestanden die 127.0.0.1:8888 wijzigen, toe aan het juiste IP-adres (vervang 127.0.0.1) en het juiste poortnummer (vervang 8888):
    4. Stel de winhttp-proxy-instellingen in via de opdrachtregel:
        * De proxy weergeven: netsh winhttp show proxy
        * De proxy instellen: netsh winhttp set proxy 127.0.0.1:8888
        * De proxy opnieuw instellen: netsh winhttp reset proxy
        * als dit is ingesteld nadat de agent is geïnstalleerd, start u onze synchronisatieagent opnieuw op: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Een server registreren met Storage Sync Service
Voordat een server kan worden gebruikt als *een servereindpunt* in een Azure File *Sync-groep,* moet deze zijn geregistreerd bij een *Storage Sync Service*. Een server kan slechts bij één Storage Sync Service tegelijk worden geregistreerd.

#### <a name="install-the-azure-file-sync-agent"></a>Azure File Sync-agent installeren
1. [Download de Azure File Sync-agent](https://go.microsoft.com/fwlink/?linkid=858257).
2. Start het installatieprogramma van de Azure File Sync-agent.
    
    ![Het eerste deelvenster van het installatieprogramma van de Azure File Sync-agent](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Zorg ervoor dat u updates voor de Azure File Sync-agent inschakelt met Microsoft Update. Het is belangrijk omdat kritieke beveiligingsoplossingen en functieverbeteringen voor het serverpakket worden verzonden via Microsoft Update.

    ![Controleren of Microsoft Update is ingeschakeld in het deelvenster Microsoft Update van het installatieprogramma van de Azure File Sync-agent](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Als de server nog niet eerder is geregistreerd, verschijnt de gebruikersinterface voor serverregistratie onmiddellijk na het voltooien van de installatie.

> [!Important]  
> Als de server lid is van een Failovercluster, moet de Azure File Sync-agent op elk knooppunt in het cluster worden geïnstalleerd.

#### <a name="register-the-server-using-the-server-registration-ui"></a>De server registreren met de gebruikersinterface voor serverregistratie
> [!Important]  
> CSP-abonnementen (Cloud Solution Provider) kunnen geen gebruik maken van de gebruikersinterface voor serverregistratie. Gebruik in plaats daarvan PowerShell (onder deze sectie).

1. Als de serverregistratie-gebruikersinterface niet onmiddellijk na het voltooien van de installatie van de Azure `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`File Sync-agent is gestart, kan deze handmatig worden gestart door het uitvoeren van .
2. Klik op Aanmelden om toegang te krijgen tot uw *Azure-abonnement.* 

    ![Dialoogvenster openen van de gebruikersinterface voor serverregistratie](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Kies de juiste abonnements-, brongroep- en opslagsynchronisatieservice in het dialoogvenster.

    ![Informatie over opslagsynchronisatie](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. In preview is nog een aanmelding vereist om het proces te voltooien. 

    ![Het dialoogvenster Aanmelden](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Als de server lid is van een failovercluster, moet elke server serverregistratie uitvoeren. Wanneer u de geregistreerde servers in de Azure Portal bekijkt, herkent Azure File Sync automatisch elk knooppunt als lid van hetzelfde Failovercluster en groepeert u deze op de juiste manier.

#### <a name="register-the-server-with-powershell"></a>De server registreren met PowerShell
U ook serverregistratie uitvoeren via PowerShell. Dit is de enige ondersteunde manier van serverregistratie voor CSP-abonnementen (Cloud Solution Provider):

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>De server uitschrijven met Storage Sync Service
Er zijn verschillende stappen vereist om een server uit te schrijven bij een Storage Sync Service. Laten we eens kijken hoe je een server goed uitschrijven.

> [!Warning]  
> Probeer geen problemen met synchronisatie, cloudtiering of enig ander aspect van Azure File Sync op te lossen door het registreren en registreren van een server niet uit te doen, of de servereindpunten te verwijderen en opnieuw te maken, tenzij dit expliciet is geïnstrueerd door een Microsoft-technicus. Het verwijderen van een server en het verwijderen van servereindpunten is een destructieve bewerking en gelaagde bestanden op de volumes met servereindpunten worden niet 'opnieuw verbonden' met hun locaties in het Azure-bestandsshare nadat de geregistreerde server- en servereindpunten zijn opnieuw gemaakt, wat resulteert in synchronisatiefouten. Houd er ook rekening mee dat gelaagde bestanden die buiten de naamruimte van een servereindpunt bestaan, permanent verloren kunnen gaan. Er kunnen gelaagde bestanden bestaan binnen servereindpunten, zelfs als cloudtiering nooit is ingeschakeld.

#### <a name="optional-recall-all-tiered-data"></a>(Optioneel) Alle gelaagde gegevens terugroepen
Als u wilt dat bestanden die momenteel gelaagd zijn, beschikbaar zijn na het verwijderen van Azure File Sync (d.w.z. dit is een productie, geen test, omgeving), roept u alle bestanden op elk volume met servereindpunten terug. Schakel cloudlagen voor alle servereindpunten uit en voer vervolgens de volgende PowerShell-cmdlet uit:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Als het lokale volume dat het servereindpunt host niet genoeg vrije ruimte `Invoke-StorageSyncFileRecall` heeft om alle gelaagde gegevens terug te roepen, mislukt de cmdlet.  

#### <a name="remove-the-server-from-all-sync-groups"></a>De server uit alle synchronisatiegroepen verwijderen
Voordat u de server uitschrijft in de Storage Sync Service, moeten alle servereindpunten op die server worden verwijderd. Dit kan via de Azure-portal:

1. Navigeer naar de opslagsynchronisatieservice waar uw server is geregistreerd.
2. Verwijder alle servereindpunten voor deze server in elke synchronisatiegroep in de opslagsynchronisatieservice. Dit kan worden bereikt door met de rechtermuisknop op het desbetreffende servereindpunt in het deelvenster synchronisatiegroep te klikken.

    ![Een servereindpunt uit een synchronisatiegroep verwijderen](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Dit kan ook worden bereikt met een eenvoudig PowerShell-script:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>Het registreren van de server uitschrijven
Nu alle gegevens zijn teruggeroepen en de server uit alle synchronisatiegroepen is verwijderd, kan de server worden losgekoppeld. 

1. Navigeer in de Azure-portal naar het gedeelte *Geregistreerde servers* van de Opslagsynchronisatieservice.
2. Klik met de rechtermuisknop op de server die u wilt uitschrijven en klik op 'Server uitschrijven'.

    ![Server uitschrijven](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zorgen voor Azure File Sync is een goede buur in uw datacenter 
Aangezien Azure File Sync zelden de enige service is die in uw datacenter wordt uitgevoerd, u het netwerk- en opslaggebruik van Azure File Sync beperken.

> [!Important]  
> Het instellen van te lage limieten heeft invloed op de prestaties van Azure File Sync-synchronisatie en terugroepen.

### <a name="set-azure-file-sync-network-limits"></a>Azure File Sync-netwerklimieten instellen
U het netwerkgebruik van Azure `StorageSyncNetworkLimit` File Sync beperken met behulp van de cmdlets.

> [!Note]  
> Netwerklimieten zijn niet van toepassing wanneer een gelaagd bestand wordt geopend of de cmdlet Invoke-StorageSyncFileRecall wordt gebruikt.

U bijvoorbeeld een nieuwe gaskleplimiet maken om ervoor te zorgen dat Azure File Sync tijdens de werkweek niet meer dan 10 Mbps gebruikt tussen 9.00 en 17.00 uur(17:00u): 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

U uw limiet zien met behulp van de volgende cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Als u netwerklimieten `Remove-StorageSyncNetworkLimit`wilt verwijderen, gebruikt u . Met de volgende opdracht worden bijvoorbeeld alle netwerklimieten verwijderd:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>QoS van Windows Server-opslag gebruiken 
Wanneer Azure File Sync wordt gehost in een virtuele machine die wordt uitgevoerd op een Windows Server-virtualisatiehost, u Storage QoS (opslagkwaliteit van de service) gebruiken om het IO-verbruik van opslag te regelen. Het QoS-beleid voor opslag kan worden ingesteld als een maximum (of limiet, zoals hoe de StorageSyncNetwork-limiet hierboven wordt afgedwongen) of als minimum (of reservering). Als u een minimum in plaats van een maximum instelt, kan Azure File Sync barsten om beschikbare opslagbandbreedte te gebruiken als andere workloads deze niet gebruiken. Zie [De kwaliteit van de service voor opslag voor](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview)meer informatie.

## <a name="see-also"></a>Zie ook
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
- [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
- [Problemen met Azure Files Sync oplossen](storage-sync-files-troubleshoot.md)
