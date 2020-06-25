---
title: Problemen met een zelf-hostende Integration runtime in Azure Data Factory oplossen
description: Meer informatie over het oplossen van problemen met zelf-hostende Integration runtime in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 06/24/2020
ms.author: abnarain
ms.openlocfilehash: e77d621d5699c434e691de0a523e58e49166d8d6
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85315091"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Problemen met zelf-hostende Integration runtime oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor zelf-hostende Integration runtime in Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Veelvoorkomende fouten en oplossingen

### <a name="error-message"></a>Foutbericht: 

`Self-hosted integration runtime can't connect to cloud service`

![Probleem met de zelf-hosted IR-verbinding](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Oorzaak 

De zelf-hostende Integration runtime kan geen verbinding maken met de Data Factory Service (back-end). Dit probleem wordt meestal veroorzaakt door netwerk instellingen in de firewall.

#### <a name="resolution"></a>Oplossing

1. Controleer of de service Integration runtime actief is.
    
   ![Zelf-hostende status van de IR-service](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Als de service wordt uitgevoerd, gaat u naar stap 3.

1. Als er geen proxy is geconfigureerd voor de zelf-hostende Integration runtime (dit is de standaard instelling), voert u de volgende Power shell-opdracht uit op de computer waarop de zelf-hostende Integration runtime is geïnstalleerd:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > De service-URL kan variëren, afhankelijk van de locatie van uw Data Factory. U kunt de service-URL vinden onder **ADF UI**  >  **Connections**  >  **Integration Runtimes**  >  **bewerken zelf-hostende IR**-  >  **knoop punten**  >  **bekijken service-url's**.
            
    Hier volgt de verwachte reactie:
            
    ![Power shell-opdracht antwoord](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Als u het verwachte antwoord niet ontvangt, gebruikt u een van de volgende methoden, afhankelijk van uw situatie:
            
    * Als er een bericht wordt weer gegeven dat de externe naam niet kan worden omgezet, is er een probleem met de Domain Name System (DNS). Neem contact op met uw netwerk team om dit probleem op te lossen.
    * Als er een bericht wordt weer gegeven dat het SSL/TLS-certificaat niet wordt vertrouwd, controleert u of het certificaat voor https://wu2.frontend.clouddatahub.net/ wordt vertrouwd op de computer en installeert u vervolgens het open bare certificaat met behulp van certificaat beheer. Deze actie moet het probleem verminderen.
    * Ga naar **Windows**logboeken  >  toepassingen en services van Windows **(Logboeken)**  >  **Applications and Services Logs**  >  **Integration runtime** en controleer op fouten die worden veroorzaakt door DNS, een firewall regel of instellingen van het bedrijfs netwerk. (Als u een dergelijke fout vindt, sluit u de verbinding af.) Omdat elk bedrijf aangepaste netwerk instellingen heeft, neemt u contact op met uw netwerk team om deze problemen op te lossen.

1. Als "proxy" is geconfigureerd op de zelf-hostende Integration runtime, controleert u of de proxy server toegang heeft tot het service-eind punt. Zie [Power shell, webaanvragen en proxy's](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)voor een voor beeld van een opdracht.    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Hier volgt de verwachte reactie:
            
![Power shell-opdracht antwoord 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Overwegingen voor de proxy:
> *    Controleer of de proxy server moet worden geplaatst in de lijst met veilige geadresseerden. Als dit het geval is, moet u ervoor zorgen dat [deze domeinen](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) zich in de lijst met veilige ontvangers bevinden.
> *    Controleer of het TLS/SSL-certificaat "wu2.frontend.clouddatahub.net/" wordt vertrouwd op de proxy server.
> *    Als u Active Directory verificatie gebruikt op de proxy, wijzigt u het service account in het gebruikers account dat toegang heeft tot de proxy als ' Integration Runtime-service.

### <a name="error-message"></a>Foutbericht: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>Oorzaak 

Het zelf-hostende geïntegreerde runtime knooppunt heeft mogelijk een **inactieve** status, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Niet-actief zelf-Hostend IR-knoop punt](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Dit gedrag treedt op wanneer knoop punten niet met elkaar kunnen communiceren.

#### <a name="resolution"></a>Oplossing

1. Meld u aan bij de door het knoop punt gehoste VM. Open in de **Logboeken toepassingen en services**  >  **Integration runtime**logboeken en filter alle fouten Logboeken.

1. Controleer of een fouten logboek de volgende fout bevat: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Als u deze fout ziet, voert u het volgende uit op de opdracht regel: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Als het volgende fout bericht wordt weer gegeven, neemt u contact op met uw IT-afdeling voor hulp bij het oplossen van dit probleem. Wanneer u met succes kunt Telnet, neemt u contact op met Microsoft Ondersteuning als u nog steeds problemen ondervindt met de status van het integrale runtime-knoop punt.
        
   ![Fout met opdracht regel](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Controleer of het fouten logboek het volgende bevat:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Probeer een of beide van de volgende methoden om het probleem op te lossen:
    - Plaats alle knoop punten in hetzelfde domein.
    - Voeg het IP-adres toe aan de host toewijzing in alle host-bestanden van de gehoste VM.


## <a name="troubleshoot-connectivity-issue"></a>Connectiviteits probleem oplossen

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Verbindings problemen oplossen tussen zelf-hostende IR en Data Factory of zelf-hostende IR en gegevens bron/Sink

Als u het probleem met de netwerk verbinding wilt oplossen, moet u weten hoe u [de netwerk tracering kunt verzamelen](#how-to-collect-netmon-trace), hoe u deze kunt gebruiken en hoe u [de netmon-tracering moet analyseren](#how-to-analyze-netmon-trace) voordat u de hulpprogram ma's voor netmon in real cases van zelf-hostende IR toepast.

Soms kunnen de verbindings problemen, zoals hieronder, worden opgelost tussen een zelf-hostende IR en Data Factory: 

![HTTP-aanvraag is mislukt](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Of de ene tussen zelf-hostende IR en gegevens bron/sink, zal de volgende fouten tegen komen:

**Fout bericht:**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**Fout bericht:**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**Oplossing:** Raadpleeg de volgende instructies als u meer problemen ondervindt:

Neem de netmon-tracering en analyseer verder.
- Ten eerste kunt u het filter zo instellen dat er een opnieuw instellen wordt weer geven van de server naar de client zijde. In het onderstaande voor beeld ziet u dat de server is Data Factory server.

    ![Data Factory-server](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Wanneer u het pakket opnieuw instellen krijgt, kunt u het gesprek vinden door TCP te volgen.

    ![Gesprek zoeken](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Daarna kunt u de conversie tussen client en Data Factory server hieronder ophalen door het filter te verwijderen.

    ![Gesprek ontvangen](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Op basis van de verzamelde netmon-tracering kunnen we zien dat het TTL-totaal (TimeToLive) 64 is. Volgens de **standaard waarden voor TTL en hops** die in [dit artikel](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) worden genoemd (zoals hieronder geëxtraheerd), kunnen we zien dat het het Linux-systeem is dat het pakket opnieuw instelt, waardoor de verbinding wordt verbroken.

    De waarden voor de standaard-TTL en-hop variëren tussen de verschillende besturings systemen, en dit zijn de standaard instellingen voor enkele:
    - Linux kernel 2,4 (circa 2001): 255 voor TCP, UDP en ICMP
    - Linux kernel 4,10 (2015): 64 voor TCP, UDP en ICMP
    - Windows XP (2001): 128 voor TCP, UDP en ICMP
    - Windows 10 (2015): 128 voor TCP, UDP en ICMP
    - Windows Server 2008:128 voor TCP, UDP en ICMP
    - Windows Server 2019 (2018): 128 voor TCP, UDP en ICMP
    - macOS (2001): 64 voor TCP, UDP en ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Het wordt echter weer gegeven als 61 in plaats van 64 in het bovenstaande voor beeld, omdat wanneer het netwerk pakket naar de bestemming bereikt, de verschillende hops moeten door lopen, zoals routers/netwerk apparaten. Het aantal routers/netwerk apparaten wordt in mindering gebracht in de laatste TTL.
    In dit geval kunnen we zien dat opnieuw instellen kan worden verzonden vanuit Linux-systeem met TTL 64.

- We moeten de vierde hop van de zelf-hostende IR controleren om te bevestigen dat het apparaat opnieuw wordt ingesteld.
 
    *Netwerk pakket van Linux-systeem A met TTL 64-> B TTL 64 min 1 = 63-> C TTL 63 min 1 = 62-> TTL 62 min 1 = 61 zelf-hostende IR*

- In ideale situatie is de TTL 128, wat betekent dat Windows System de Data Factory uitvoert. Zoals u in het onderstaande voor beeld ziet, *128 – 107 = 21 hops*, wat betekent dat er 21 hops voor het pakket zijn verzonden van Data Factory naar een zelf-hostende IR tijdens de TCP 3-handshake.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Daarom moet u het netwerk team om te controleren wat de vierde hop is van zelf-hostende IR. Als dit de firewall is als Linux-systeem, controleert u de logboeken op de reden dat het apparaat het pakket opnieuw instelt na de TCP 3-handshake. Als u echter niet zeker weet waar u moet onderzoeken, probeert u de netmon-tracering van de zelf-hostende IR en firewall samen te halen tijdens de problematische tijd om te achterhalen op welk apparaat dit pakket kan worden teruggezet en wordt de verbinding verbroken. In dit geval moet u ook uw netwerk team laten door lopen.

### <a name="how-to-collect-netmon-trace"></a>Netmon-tracering verzamelen

1.  Down load de Hulpprogram Ma's voor netmon van [deze website](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)en installeer deze op uw server computer (ongeacht welke server het probleem heeft) en de client (zoals zelf-hostende IR).

2.  Maak een map, bijvoorbeeld in het volgende pad: *D:\netmon*. Zorg ervoor dat er voldoende ruimte is om het logboek op te slaan.

3.  Leg de IP-en poort gegevens vast. 
    1. Start een opdracht prompt.
    2. Selecteer als administrator uitvoeren en voer de volgende opdracht uit:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Leg de netmon-tracering (netwerk pakket) vast.
    1. Start een opdracht prompt.
    2. Selecteer als administrator uitvoeren en voer de volgende opdracht uit:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. U kunt drie verschillende opdrachten gebruiken om de netwerk pagina vast te leggen:
        - Optie A: RoundRobin-bestands opdracht (Hiermee wordt slechts één bestand vastgelegd en worden oude logboeken overschreven).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Optie B: geketende bestands opdracht (Hiermee wordt een nieuw bestand gemaakt als 200 MB is bereikt).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Optie C: opdracht gepland bestand.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Druk op **CTRL + C** om te stoppen met het vastleggen van de netmon-tracering.
 
> [!NOTE]
> Als u de netmon-tracering op de client computer alleen kunt verzamelen, moet u het IP-adres van de server ophalen om de tracering te analyseren.

### <a name="how-to-analyze-netmon-trace"></a>Netmon-tracering analyseren

Wanneer u probeert Telnet **8.8.8.8 888** te maken met de bovenstaande netmon-tracering, moet u de onderstaande tracering zien:

![netmon-tracering 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon-tracering 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Dit betekent dat u geen TCP-verbinding met de **8.8.8.8** -server kunt maken op basis van de poort **888**, zodat u daar twee extra pakketten van **SynReTransmit** ziet. Omdat de **Self-HOST2** van de bron geen verbinding kan maken met **8.8.8.8** bij het eerste pakket, blijft deze verbinding maken.

> [!TIP]
> - U kunt klikken op standaard filter voor **Load filter**  ->  **Standard Filter**  ->  **adressen**  ->  **IPv4-adressen**.
> - Voer **IPv4. Address = = 8.8.8.8** in als filter en klik op **Toep assen**. Daarna ziet u alleen de communicatie van de lokale computer naar de doel- **8.8.8.8**.

![filter adressen 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![filter adressen 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

In het onderstaande voor beeld ziet u hoe een geschikt scenario eruit zou zien. 

- Als Telnet **8.8.8.8 53** zonder problemen werkt, kunt u de TCP 3-Handshake bekijken en vervolgens de sessie met TCP 4-Handshake volt ooien.

    ![goed scenario-voor beeld 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![goed scenario-voor beeld 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Op basis van de bovenstaande TCP 3-Handshake kunt u de onderstaande werk stroom zien:

    ![TCP 3 Handshake-werk stroom](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- De TCP 4-Handshake voor het volt ooien van de sessie en de werk stroom worden als volgt weer gegeven:

    ![TCP 4-Handshake](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4-Handshake-werk stroom](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen kunt u de volgende bronnen proberen:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A-vragenpagina](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack overflow-forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestatie gids gegevens stromen toewijzen](concepts-data-flow-performance.md)
