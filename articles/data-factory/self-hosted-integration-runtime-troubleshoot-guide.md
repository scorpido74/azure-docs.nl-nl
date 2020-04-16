---
title: Problemen met zelfgehoste runtime voor integratie in Azure Data Factory oplossen
description: Meer informatie over het oplossen van zelf gehoste problemen met de runtime van de integratie in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f298b331d53eb8bab67a6f99194065dc5f889236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414880"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Problemen met zelf gehoste runtime voor integratie oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden veelvoorkomende probleemoplossingsmethoden voor zelfgehoste gebruiksruntime voor zelfgehoste integratie in Azure Data Factory onderzocht.

## <a name="common-errors-and-resolutions"></a>Veelvoorkomende fouten en oplossingen

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Foutbericht: Runtime voor zelfgehoste integratie kan geen verbinding maken met cloudservice

![Zelf gehoste IR-verbindingsprobleem](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Oorzaak 

De self-hosted integration runtime kan geen verbinding maken met de Data Factory-service (backend). Dit probleem wordt meestal veroorzaakt door netwerkinstellingen in de firewall.

#### <a name="resolution"></a>Oplossing

1. Controleer of de runtime-service voor integratie wordt uitgevoerd.
    
   ![Zelf gehoste IR-service- status](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Als de service wordt uitgevoerd, gaat u verder met stap 3.

1. Als er geen proxy is geconfigureerd op de runtime voor zelfgehoste integratie (de standaardinstelling), voert u de volgende PowerShell-opdracht uit op de machine waar de zelfgehoste runtime voor integratie is geïnstalleerd:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > De URL van de service kan variëren, afhankelijk van de locatie van uw gegevensfabriek. U de url van de service vinden onder de runtimes voor integratie van **ADF-gebruikersinterface's** > **Connections** > **Integration runtimes** > Zelf gehoste**URL's**van**de IR-knooppuntenweergave** > **Nodes** > bewerken.
            
    Het volgende is het verwachte antwoord:
            
    ![PowerShell-opdrachtrespons](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Als u het verwachte antwoord niet ontvangt, gebruikt u een van de volgende methoden die past bij uw situatie:
            
    * Als u een bericht 'Externe naam kan niet worden opgelost' ontvangt, is er een DNS-probleem (Domain Name System). Neem contact op met uw netwerkteam om dit probleem op te lossen.
    * Als u een bericht 'ssl/tls-cert wordt niet vertrouwd' ontvangt, controleert u of het certificaat https://wu2.frontend.clouddatahub.net/ op de machine wordt vertrouwd en installeert u het openbare certificaat met Behulp van Certificaatbeheer. Deze actie moet het probleem beperken.
    * Ga naar **De** > **runtime** van Windows**Event viewer (logboeken)** > Applications and Services**Logs** > Integration en controleer op een storing die wordt veroorzaakt door DNS, een firewallregel of bedrijfsnetwerkinstellingen. (Als u een dergelijke fout vindt, sluit u de verbinding met geweld.) Omdat elk bedrijf aangepaste netwerkinstellingen heeft, neemt u contact op met uw netwerkteam om deze problemen op te lossen.

1. Als 'proxy' is geconfigureerd op de runtime voor zelfgehoste integratie, controleert u of uw proxyserver toegang heeft tot het eindpunt van de service. Zie [PowerShell, webaanvragen en proxy's voor](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)een voorbeeldopdracht.    
                
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

Het volgende is het verwachte antwoord:
            
![Powershell-opdrachtrespons 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxy overwegingen:
> *    Controleer of de proxyserver op de lijst Veilige geadresseerden moet worden geplaatst. Zorg er dan voor dat [deze domeinen](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) in de lijst Veilige geadresseerden staan.
> *    Controleer of het TLS/SSL-certificaat "wu2.frontend.clouddatahub.net/" wordt vertrouwd op de proxyserver.
> *    Als u Active Directory-verificatie op de proxy gebruikt, wijzigt u het serviceaccount in het gebruikersaccount dat toegang heeft tot de proxy als 'Integratieruntimeservice'.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Foutbericht: Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" status

#### <a name="cause"></a>Oorzaak 

Het zelf gehoste geïntegreerde runtime-knooppunt heeft mogelijk een **inactieve** status, zoals in de volgende schermafbeelding wordt weergegeven:

![Inactief zelfgehost IR-knooppunt](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Dit gedrag treedt op wanneer knooppunten niet met elkaar kunnen communiceren.

#### <a name="resolution"></a>Oplossing

1. Meld u aan bij de vm met knooppuntgehoste virtuele gegevens. Open Logboeken voor **logboeken en** > services**voor logboeken**integratie en filter alle foutlogboeken.

1. Controleer of een foutlogboek de volgende fout bevat: 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.    Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
