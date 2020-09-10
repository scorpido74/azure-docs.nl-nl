---
title: Zelfstudie voor configureren van netwerkinstellingen voor een Azure Stack Edge-apparaat met GPU in de Azure-portal | Microsoft Docs
description: In deze zelfstudie voor het implementeren van Azure Stack Edge GPU krijgt u instructies om instellingen voor het netwerk, het rekennetwerk en de webproxy voor uw fysieke apparaat te configureren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 6e7dbc2b96a53d220554e07228a5e30857d12d9c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89262971"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-with-gpu"></a>Zelfstudie: netwerk configureren voor Azure Stack Edge met GPU

In deze zelfstudie wordt beschreven hoe u het netwerk voor uw Azure Stack Edge-apparaat met een onboard GPU kunt configureren door de lokale webinterface te gebruiken.

Het verbindingsproces kan ongeveer twintig minuten duren.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Netwerk configureren
> * Rekennetwerk inschakelen
> * Webproxy configureren


## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge-apparaat met GPU configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge installeren](azure-stack-edge-gpu-deploy-install.md).
* U hebt verbinding gemaakt met de lokale webinterface van het apparaat, zoals beschreven in [Verbinding maken met Azure Stack Edge](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Netwerk configureren

Op de pagina **Aan de slag** worden de verschillende instellingen weergegeven die nodig zijn voor het configureren en registreren van het fysieke apparaat bij de Azure Stack Edge-service. 

Volg deze stappen voor het configureren van het netwerk voor uw apparaat.

1. Ga in de lokale webinterface van uw apparaat naar de pagina **Aan de slag**. 

2. Selecteer op de tegel **Netwerk** de optie **Configureren**.  
    
    ![Tegel Netwerkinstellingen van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    Er zijn zes netwerkinterfaces op het fysieke apparaat. Poort 1 en poort 2 zijn 1-Gbps-netwerkinterfaces. Poort 3, poort 4, poort 5 en poort 6 zijn allemaal netwerkinterfaces van 25 Gbps die ook kunnen dienen als netwerkinterfaces van 10 Gbps. Poort 1 wordt automatisch geconfigureerd als een beheerpoort en poort 2 tot poort 6 zijn allemaal gegevenspoorten. Voor een nieuw apparaat ziet de pagina **Netwerkinstellingen** eruit zoals hieronder wordt weergegeven.
    
    ![Pagina 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. Als u de netwerkinstellingen wilt wijzigen, selecteert u een poort en wijzigt u in het rechterdeelvenster dat verschijnt, het IP-adres, het subnet, de gateway, de primaire DNS en de secundaire DNS. 

    - Als u poort 1 selecteert, ziet u dat deze vooraf is geconfigureerd als statisch. 

        ![Netwerkinstellingen van poort 1 van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - Als u poort 2, poort 3, poort 4 of poort 5 selecteert, worden al deze poorten standaard geconfigureerd als DHCP.

        ![Netwerkinstellingen van poort 3 van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    Houd bij het configureren van de netwerkinstellingen rekening met het volgende:

   * Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. Er wordt automatisch een IP-adres, subnet, gateway en DNS toegewezen.
   * Als DHCP niet is ingeschakeld, kunt u indien nodig statische IP-adressen toewijzen.
   * U kunt uw netwerkinterface configureren als IPv4.
   * Op de 25 Gbps-interfaces kunt u de RDMA-modus (Remote Direct Access Memory) instellen op iWarp of RoCE (RDMA over Converged Ethernet). Wanneer lage latenties de primaire vereiste zijn en schaalbaarheid er niet toe doet, gebruikt u RoCE. Wanneer latentie een belangrijke vereiste is, maar gebruiksgemak en schaalbaarheid ook een hoge prioriteit kennen, is iWARP de beste kandidaat.
   * Het serienummer van een poort komt overeen met het serienummer van het knooppunt.

    Zodra het apparaatnetwerk is geconfigureerd, wordt de pagina bijgewerkt, zoals hieronder weergegeven.

    ![Pagina 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * Het is raadzaam om het lokale IP-adres van de netwerkinterface niet te wijzigen van statisch naar DHCP, tenzij u een ander IP-adres hebt om verbinding te maken met het apparaat. Als u één netwerkinterface gebruikt en u overschakelt naar DHCP, is er geen manier om het DHCP-adres te bepalen. Als u wilt overstappen op een DHCP-adres, wacht u totdat het apparaat bij de service is geactiveerd en brengt u vervolgens de wijziging aan. U kunt vervolgens de IP-adressen van alle adapters in de **Apparaateigenschappen** weergeven in de Azure Portal voor uw service.


    Nadat u de netwerkinstellingen hebt geconfigureerd en toegepast, gaat u terug naar **Aan de slag**.

## <a name="enable-compute-network"></a>Rekennetwerk inschakelen

Voer de volgende stappen uit om het rekenproces in te schakelen en het rekennetwerk te configureren.

1. Ga in de lokale webinterface van uw apparaat naar de pagina **Aan de slag**. Selecteer op de tegel **Netwerk** de optie **Rekennetwerk**.  

    ![Pagina Berekening in lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)

2. Selecteer op de pagina **Berekening** een netwerkinterface die u voor rekenprocessen wilt inschakelen. 

    ![Pagina Berekening in lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

2. Selecteer in het dialoogvenster **Netwerkinstellingen** de optie **Inschakelen**. Wanneer u een rekenproces inschakelt, wordt er op die netwerkinterface een virtuele switch op het apparaat gemaakt. De virtuele switch wordt gebruikt voor de rekeninfrastructuur op het apparaat. 
    
3. Wijs **IP-adressen aan Kubernetes-knooppunten** toe. Deze vaste IP-adressen zijn voor de reken-VM. 

    Voor een apparaat met *n* knooppunten wordt met behulp van de begin- en eind-IP-adressen een aansluitend bereik van minimaal *n + 1* IPv4-adressen (of meer) voor de reken-VM verstrekt. Aangezien Azure Stack Edge een apparaat met één knooppunt is, worden er minimaal twee aansluitende IPv4-adressen opgegeven. 

4. Wijs **externe IP-adressen voor Kubernetes-services** toe. Dit zijn ook de IP-adressen voor de taakverdeling. Deze aansluitende IP-adressen zijn voor services die u buiten het Kubernetes-cluster zichtbaar wilt maken. U geeft het statische IP-bereik op, afhankelijk van het aantal weergegeven services. 
    
    > [!IMPORTANT]
    > U wordt ten sterkste aangeraden minimaal één IP-adres voor de Azure Stack Edge Hub-service op te geven om toegang te krijgen tot de rekenmodules. U kunt desgewenst extra IP-adressen opgeven voor andere services/IoT Edge-modules (één per service/module). Deze moeten toegankelijk zijn van buiten het cluster. De IP-adressen van de service kunnen later worden bijgewerkt. 
    
5. Selecteer **Toepassen**.

    ![Pagina Berekening in lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)


## <a name="configure-web-proxy"></a>Webproxy configureren

Dit is een optionele configuratie.

> [!IMPORTANT]
> * Als u berekening inschakelt en een IoT Edge-module op uw Azure Stack Edge-apparaat gebruikt, wordt u aangeraden de verificatie van de webproxy in te stellen op **Geen**. NTLM wordt niet ondersteund.
>* De bestanden voor het automatisch configureren van de proxy (PAC-bestanden) worden niet ondersteund. Een PAC-bestand definieert hoe webbrowsers en andere gebruikersagenten automatisch de juiste proxyserver (toegangsmethode) kunnen kiezen voor het ophalen van een bepaalde URL. Proxy's die al het verkeer proberen te onderscheppen en lezen (en vervolgens alles opnieuw ondertekenen met hun eigen certificering) zijn niet compatibel, omdat het certificaat van de proxy niet wordt vertrouwd. Normaal gesproken werken transparante proxy's goed met Azure Stack Edge. Niet-transparante webproxy's worden niet ondersteund.

1. Ga in de lokale webinterface van uw apparaat naar de pagina **Aan de slag**.
2. Configureer de instellingen voor de webproxyserver op de tegel **Netwerk**. De configuratie van uw webproxy is weliswaar optioneel, maar als u een webproxy gebruikt, kan deze alleen op deze pagina worden geconfigureerd.

   ![Pagina 'Webproxy-instellingen' voor lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

3. Op de pagina **Webproxyinstellingen** voert u de volgende stappen uit:

    1. Voer in het vak **Webproxy-URL** de URL in deze indeling in: `http://host-IP address or FQDN:Port number`. HTTPS-URL's worden niet ondersteund.

    2. Selecteer onder **Verificatie** **Geen** of **NTLM**. Als u berekening inschakelt en IoT Edge-module op uw Azure Stack Edge-apparaat gebruikt, raden we u aan om de verificatie van de webproxy in te stellen op **Geen**. **NTLM** wordt niet ondersteund.

    3. Als u verificatie gebruikt, voert u een gebruikersnaam en wachtwoord in.

    4. Selecteer **Toepassen** om de geconfigureerde webproxyinstellingen te valideren en toe te passen.
    
   ![Pagina 'Webproxy-instellingen' voor lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

4. Nadat de instellingen zijn toegepast, gaat u terug naar **Aan de slag**.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Netwerk configureren
> * Rekennetwerk inschakelen
> * Webproxy configureren


Als u wilt weten hoe u uw Azure Stack Edge-apparaat kunt instellen, gaat u naar:

> [!div class="nextstepaction"]
> [Apparaatinstellingen configureren](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
