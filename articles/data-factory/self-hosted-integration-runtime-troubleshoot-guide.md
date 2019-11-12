---
title: Problemen met een zelf-hostende Integration runtime in Azure Data Factory oplossen
description: Meer informatie over het oplossen van problemen met zelf-hostende Integration runtime in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907280"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Problemen met zelf-hostende Integration runtime oplossen

In dit artikel worden algemene probleemoplossings methoden besproken voor zelf-hostende Integration runtime in Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Veelvoorkomende fouten en oplossingen

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>Fout bericht: zelf-hostende Integration runtime kan geen verbinding maken met de Cloud service.

- **Symptoom**: 

    ![Probleem met de zelf-hosted IR-verbinding](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **Oorzaak**: de zelf-hostende Integration runtime kan geen verbinding maken met de Data Factory Service (back-end). Dit wordt meestal veroorzaakt door netwerk instellingen in firewall.

- **Oplossing**: 

    1. Controleer of de Windows-Service Integration Runtime service wordt uitgevoerd.
    
        ![Zelf-Hostende status van de IR-service](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. Als de Windows-service, zoals weer gegeven in [1], wordt uitgevoerd, volgt u de onderstaande instructies:

        1. Als "proxy" niet is geconfigureerd op zelf-hostende Integration runtime (standaard instellingen is geen proxy configuratie), voert u de onderstaande Power shell-opdracht uit op de computer waarop zelf-hostende Integration runtime is geïnstalleerd: 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > De service-URL kan variëren op basis van uw data factory locatie. U kunt de URL van de service vinden onder ADF-gebruikers interface-> verbindingen-> Integration Runtimes-> zelf-hostende IR-> knooppunten bewerken-> Service-Url's weer geven.
            
            Hieronder ziet u de verwachte reactie:
            
            ![Power shell-opdracht antwoord](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            Als het antwoord anders is, volgt u de onderstaande instructies:
            
            * Als er een fout bericht wordt weer geven dat de externe naam niet kan worden omgezet, is er een probleem met DNS. Neem contact op met het netwerk team om het probleem met de DNS-omzetting vast te stellen. 
            * Als er een fout bericht wordt weer geven dat het SSL/TLS-certificaat niet wordt vertrouwd, controleert u of het certificaat voor 'https://wu2.frontend.clouddatahub.net/' wordt vertrouwd op de computer. Installeer het open bare certificaat met CERT Manager, waardoor dit probleem kan worden verholpen.
            * Controleer Windows-> Logboeken (Logboeken): > toepassingen en services-Logboeken > Integration Runtime voor elke fout, meestal veroorzaakt door DNS, firewall regel en netwerk instellingen van het bedrijf (sluit de verbinding geforceerd af). Voor dit probleem moet u uw netwerk team benaderen voor verdere troubleshot, omdat elk bedrijf aangepaste netwerk instellingen heeft.

        2. Als "proxy" is geconfigureerd op de zelf-hostende Integration runtime, controleert u of de proxy server toegang heeft tot onze service-eind punten. [Zie voor](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)een voor beeld van een opdracht.    
                
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

            Hieronder ziet u de verwachte reactie:
            
            ![Power shell-opdracht antwoord 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > Overwegingen voor de proxy:
            > * Controleer of de proxy server White List vereist. Als dat het geval is, laat u [deze domeinen](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) white list.
            > * Controleer het TLS/SSL-certificaat voor ' wu2.frontend.clouddatahub.net/' wordt vertrouwd op de proxy server.
            > * Als u Active Directory-verificatie gebruikt in proxy, wijzigt u het service account in het gebruikers account dat toegang heeft tot de proxy als "Integration Runtime-service".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Fout bericht: zelf-hostende Integration runtime-knoop punt/logische SHIR is in inactief/' wordt uitgevoerd (beperkt) '

- **Oorzaak**: mogelijk ziet u een zelf-HOSTend IR-knoop punt in inactieve status, zoals wordt weer gegeven in de onderstaande scherm afbeelding:

    ![Niet-actief zelf-Hostend IR-knoop punt](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    Het gebeurt zo dat de knoop punten niet met elkaar kunnen communiceren. 

- **Oplossing**: 

    Meld u aan bij het knoop punt gehoste VM en open de gebeurtenis weergave, onder de logboeken toepassingen en Services-> Integration Runtime alle fouten logboeken worden gefilterd. 

     1. Als het fouten logboek de volgende bevat: 
    
        **Fouten logboek**: System. service model. EndpointNotFoundException: kan geen verbinding maken met net. TCP://xxxxxxx.bwld.com: 8060/ExternalService. SVC/WorkerManager. De verbindings poging is voor een tijds panne van 00:00:00.9940994. TCP-fout code 10061: er kan geen verbinding worden gemaakt, omdat de doel computer deze actief heeft geweigerd 10.2.4.10:8060.  ---> System .net. sockets. SocketException: er kan geen verbinding worden gemaakt omdat de doel machine deze actief heeft geweigerd 10.2.4.10:8060
    
           op System .net. sockets. socket. DoConnect (eind punt endPointSnapshot, SocketAddress socketAddress)
           
           op het systeem .net. sockets. socket. Connect (eind punt remoteEP)
           
           op System. service model. channels. SocketConnectionInitiator. Connect (URI-URI, time-out van time span)
    
        **Oplossing:** start de opdracht regel: telnet 10.2.4.10 8060
        
        Als u een fout melding krijgt, neemt u contact op met uw IT-guys voor hulp bij het oplossen van dit probleem. Neem contact op met micro soft ondersteuning als u nog steeds problemen hebt met de status van het IR-knoop punt nadat u een Telnet-verbinding hebt gemaakt.
        
        ![Fout met opdracht regel](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. Als het fouten logboek de volgende bevat:
     
        **Fouten logboek:** Kan geen verbinding maken met worker-Manager: net. TCP://xxxxxx: 8060/ExternalService. SVC/er zijn geen DNS-vermeldingen voor de host azranlcir01r1. Er is geen bekende Details van de uitzonde ring: System. service model. EndpointNotFoundException: er zijn geen DNS-vermeldingen voor host xxxxx. ---> System .net. sockets. SocketException: deze host is bekend op het systeem .net. DNS. GetAddrInfo (String name) op het systeem .net. DNS. InternalGetHostByName (String hostName, Boolean includeIPv6) op het systeem .net. DNS. GetHostEntry (teken reeks hostNameOrAddress ) op System. service model. channels. DnsCache. Resolve (URI URI)---einde van de stack tracering binnen de interne uitzonde ring---server stack trace: at System. service model. channels. DnsCache. Resolve (URI URI) 
    
        **Oplossing:** Een van de onderstaande twee acties kan helpen bij het oplossen van het probleem:
         1. Plaats alle knoop punten in hetzelfde domein.
         2. Voeg IP toe aan host toewijzing in het hosts-bestand van de gehoste VM.


## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory-blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestatie gids voor gegevens stromen van ADF-toewijzing](concepts-data-flow-performance.md)
