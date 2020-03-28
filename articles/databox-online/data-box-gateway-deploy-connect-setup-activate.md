---
title: Azure Data Box Gateway maken, configureren en activeren in Azure-portal
description: De derde zelfstudie voor het implementeren van Data Box Gateway geeft u de opdracht om uw virtuele apparaat te verbinden, in te stellen en te activeren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: c2a0dde496d6af66387210ca9b2ebf9cb4bdae7f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79087939"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Zelfstudie: Azure Data Box Gateway verbinden, instellen, activeren

## <a name="introduction"></a>Inleiding

In deze zelfstudie wordt beschreven hoe u verbinding maken met uw Data Box Gateway-apparaat, in deze zelfstudie met behulp van de lokale web-gebruikersinterface. 

Het installatie- en activeringsproces kan ongeveer 10 minuten in beslag nemen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verbinding maken met een virtueel apparaat
> * Het virtuele apparaat instellen en activeren

## <a name="prerequisites"></a>Vereisten

Voordat u uw Data Box Gateway configureert en instelt, moet u ervoor zorgen dat:

* U hebt een virtueel apparaat ingericht en een verbonden URL verkregen zoals beschreven in de [Bepaling van een Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) of Het [inrichten van een Data Box Gateway in VMware.](data-box-gateway-deploy-provision-vmware.md)
* U beschikt over de activeringssleutel van de Data Box Gateway-service die u hebt gemaakt om Data Box Gateway-apparaten te beheren. Ga voor meer informatie naar [Voorbereiden om Azure Data Box Gateway te implementeren.](data-box-gateway-deploy-prep.md)


## <a name="connect-to-the-local-web-ui-setup"></a>Verbinding maken met de lokale web-gebruikersinterface-installatie 

1. Open een browservenster en krijg toegang tot de lokale webgebruikersinterface van het apparaat op:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Gebruik de verbindings-URL die in de vorige zelfstudie is vermeld. U ziet een fout of een waarschuwing die aangeeft dat er een probleem is met het beveiligingscertificaat van de website.

2. Selecteer **Doorgaan naar deze webpagina**. Deze stappen kunnen variëren afhankelijk van de browser die u gebruikt.
   
    ![Foutbericht van websitebeveiligingscertificaat](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Meld u aan bij de webgebruikersinterface van uw virtuele apparaat. Het standaardwachtwoord is *Password1*. 
   
    ![Aanmelden bij lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Wijzig bij de prompt het apparaatwachtwoord. Het nieuwe wachtwoord moet tussen de 8 en 16 tekens bevatten. Het moet 3 van de volgende bevatten: hoofdletters, kleine letters, numerieke en speciale tekens.

    ![Wachtwoord voor apparaat wijzigen](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

U bevindt zich nu op het **dashboard** van uw apparaat.

## <a name="set-up-and-activate-the-virtual-device"></a>Het virtuele apparaat instellen en activeren
 
Op uw dashboard worden de verschillende instellingen weergegeven die nodig zijn om het virtuele apparaat te configureren en te registreren met de Data Box Gateway-service. De **instellingen voor apparaatnaam,** **netwerkinstellingen,** **webproxy**en **Tijd** zijn optioneel. De enige vereiste instellingen zijn **Cloud-instellingen.**
   
![Pagina Lokale webgebruikersinterface "Dashboard"](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. Selecteer in het linkerdeelvenster **de naam apparaat**en voer vervolgens een vriendelijke naam voor uw apparaat in. De vriendelijke naam moet 1 tot 15 tekens lang bevatten en letters, getallen en koppeltekens bevatten.

    ![Pagina Lokale webgebruikersinterface 'Apparaatnaam'](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Optioneel) Selecteer in het linkerdeelvenster **netwerkinstellingen** en configureer de instellingen. Op uw virtuele apparaat ziet u ten minste één netwerkinterface en meer, afhankelijk van het aantal dat u in de onderliggende virtuele machine hebt geconfigureerd. De pagina **Netwerkinstellingen** voor een virtueel apparaat met één netwerkinterface ingeschakeld, is zoals hieronder weergegeven.
    
    ![Pagina Lokale webgebruikersinterface 'Netwerkinstellingen'](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Houd er bij het configureren van netwerkinstellingen rekening mee:

    - Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. Een IP-adres, subnet, gateway en DNS worden automatisch toegewezen.
    - Als DHCP niet is ingeschakeld, u indien nodig statische IP's toewijzen.
    - U uw netwerkinterface configureren als IPv4.

     >[!NOTE] 
     > We raden u aan het lokale IP-adres van de netwerkinterface niet over te schakelen van statisch naar DHCP, tenzij u een ander IP-adres hebt om verbinding te maken met het apparaat. Als u één netwerkinterface gebruikt en u overschakelt naar DHCP, is er geen manier om het DHCP-adres te bepalen. Als u wilt overstappen naar een DHCP-adres, wacht u tot het apparaat zich bij de service heeft geregistreerd en wijzigt u deze. Vervolgens u de IP's van alle adapters in de **eigenschappen van het apparaat** bekijken in de Azure-portal voor uw service.

3. Configureer desgewenst uw webproxyserver. Hoewel webproxyconfiguratie optioneel is, u deze alleen op deze pagina configureren als u een webproxy gebruikt.
   
   ![Pagina Lokale webgebruikersinterface 'Webproxy-instellingen'](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Ga als volgt te werk op de pagina **Webproxy:**
   
   1. Voer in het vak **URL van webproxy** de URL in deze indeling in: `http://&lt;host-IP address or FQDN&gt;:Port number`. HTTPS-URL's worden niet ondersteund.
   2. Selecteer **Onder Verificatie**de optie **Geen** of **NTLM**.
   3. Als u verificatie gebruikt, voert u een **gebruikersnaam** en **wachtwoord**in.
   4. Als u de geconfigureerde webproxy-instellingen wilt valideren en toepassen, selecteert u **Toepassen**.

   > [!NOTE]
   > Pac-bestanden (Proxy-auto config) worden niet ondersteund. Een PAC-bestand definieert hoe webbrowsers en andere gebruikersagents automatisch de juiste proxyserver (toegangsmethode) kunnen kiezen voor het ophalen van een bepaalde URL.
   > Proxy's die proberen om al het verkeer te onderscheppen en te lezen (dan alles opnieuw ondertekenen met hun eigen certificering) zijn niet compatibel omdat het cert van de proxy niet wordt vertrouwd.
   > Doorgaans werken transparante proxy's goed met Azure Data Box Gateway.

4. (Optioneel) Selecteer in het linkerdeelvenster **Tijdinstellingen**en configureer vervolgens de tijdzone en de primaire en secundaire NTP-servers voor uw apparaat. 

    NTP-servers zijn vereist omdat uw apparaat de tijd moet synchroniseren, zodat het kan verifiëren met uw cloudserviceproviders.
    
    ![Pagina Lokale webgebruikersinterface 'Tijdinstellingen'](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Ga op de pagina **Tijdinstellingen** als volgt te werk:
    
    1. Selecteer **in** de vervolgkeuzelijst Tijdzone de tijdzone die overeenkomt met de geografische locatie waarin het apparaat is geïmplementeerd.
        De standaardtijdzone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.

    2. Geef een **primaire NTP-server** op voor `time.windows.com`uw apparaat of accepteer de standaardwaarde van .   
        Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet.

    3. Voer optioneel in het vak **Secundaire NTP-server** een secundaire server voor uw apparaat in.

    4. Als u de geconfigureerde tijdinstellingen wilt valideren en toepassen, selecteert u **Toepassen**.

6. Selecteer in het linkerdeelvenster **Cloudinstellingen**en activeer uw apparaat vervolgens met de Data Box Gateway-service in Azure-portal.
    
    1. Voer in het vak **Activeringssleutel** de **activeringssleutel** in die u hebt in [De activeringssleutel](data-box-gateway-deploy-prep.md#get-the-activation-key) voor Gegevensboxgateway opbrengen.

    2. Selecteer **Activeren**.
       
         ![Pagina Lokale webgebruikersinterface "Cloud-instellingen"](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Het apparaat wordt geactiveerd en kritieke updates, indien beschikbaar, worden automatisch toegepast. U ziet een melding in die zin. Houd de voortgang van de update in de gaten via de Azure-portal.

        ![Pagina Lokale webgebruikersinterface "Cloud-instellingen"](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Het dialoogvenster heeft ook een herstelsleutel die u op een veilige locatie moet kopiëren en opslaan. Deze sleutel wordt gebruikt om uw gegevens te herstellen in het geval het apparaat niet kan opstarten.**


    4. Het kan nodig zijn om enkele minuten te wachten voordat de update is voltooid. Nadat de update is voltooid, meldt u zich aan bij het apparaat. De pagina **Cloud-instellingen** wordt bijgewerkt om aan te geven dat het apparaat is geactiveerd.

        ![Pagina lokale webgebruikersinterface 'Cloud-instellingen' bijgewerkt](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

De installatie van het apparaat is voltooid. Je nu aandelen toevoegen op je apparaat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Verbinding maken met een virtueel apparaat
> * Het virtuele apparaat instellen en activeren

Zie voor meer informatie over het overbrengen van gegevens met uw Data Box Gateway:

> [!div class="nextstepaction"]
> [Gegevens overbrengen met Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
