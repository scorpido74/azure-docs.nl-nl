---
title: Verbinding maken met Azure Data Box Gateway, configureren en activeren in Azure Portal
description: De derde zelf studie voor het implementeren van Data Box Gateway geeft u de opdracht om verbinding te maken, uw virtuele apparaat in te stellen en te activeren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 33333f8df1e4809a330815e34074d1bca556cd14
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561830"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Zelf studie: verbinding maken, instellen, Azure Data Box Gateway activeren

## <a name="introduction"></a>Inleiding

In deze zelf studie wordt beschreven hoe u met behulp van de lokale web-UI verbinding maakt met uw Data Box Gateway-apparaat en hoe u deze kunt instellen en activeert. 

Het installatie-en activerings proces kan ongeveer tien minuten duren. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verbinding maken met een virtueel apparaat
> * Het virtuele apparaat instellen en activeren

## <a name="prerequisites"></a>Vereisten

Voordat u uw Data Box Gateway configureert en instelt, moet u ervoor zorgen dat:

* U hebt een virtueel apparaat ingericht en daar een verbonden URL aan toegewezen, zoals beschreven in het [inrichten van een Data Box gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) of het [inrichten van een Data Box gateway in VMware](data-box-gateway-deploy-provision-vmware.md).
* U hebt de activerings sleutel van de Data Box Gateway-Service die u hebt gemaakt voor het beheren van Data Box Gateway apparaten. Ga voor meer informatie naar voor [bereiding voor het implementeren van Azure data Box gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Verbinding maken met de lokale web-UI instellen 

1. Open een browser venster en ga naar de lokale web-UI van het apparaat op:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Gebruik de verbindings-URL die u in de vorige zelf studie hebt genoteerd. Er wordt een fout bericht of een waarschuwing weer gegeven waarin wordt aangegeven dat er een probleem is met het beveiligings certificaat van de website.

2. Selecteer **door gaan naar deze webpagina**. Deze stappen kunnen variëren, afhankelijk van de browser die u gebruikt.
   
    ![Fout bericht van beveiligings certificaat voor website](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Meld u aan bij de Web-UI van uw virtuele apparaat. Het standaard wachtwoord is *Wachtwoord1*. 
   
    ![Aanmelden bij de lokale web-UI](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Wijzig bij de prompt het wacht woord van het apparaat. Het nieuwe wacht woord moet tussen 8 en 16 tekens bevatten. De naam moet drie van de volgende tekens bevatten: hoofd letters, kleine letters, cijfers en speciale letters.

    ![Wachtwoord voor apparaat wijzigen](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

U bent nu klaar met het **dash board** van uw apparaat.

## <a name="set-up-and-activate-the-virtual-device"></a>Het virtuele apparaat instellen en activeren
 
Uw dash board geeft de verschillende instellingen weer die nodig zijn om het virtuele apparaat te configureren en registreren bij de Data Box Gateway-Service. De **apparaatnaam**, **netwerk instellingen**, **webproxy-instellingen**en **tijd instellingen** zijn optioneel. De enige vereiste instellingen zijn **Cloud instellingen**.
   
![De pagina dash board van lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. Selecteer **apparaatnaam**in het linkerdeel venster en geef een beschrijvende naam op voor het apparaat. De beschrijvende naam moet tussen 1 en 15 tekens lang zijn en mag alleen bestaan uit letters, cijfers en afbreek streepjes.

    ![Pagina apparaatnaam van lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. Beschrijving Selecteer in het linkerdeel venster **netwerk instellingen** en configureer vervolgens de instellingen. Op het virtuele apparaat ziet u ten minste één netwerk interface en meer, afhankelijk van het aantal dat u hebt geconfigureerd op de onderliggende virtuele machine. De pagina **netwerk instellingen** voor een virtueel apparaat met één netwerk interface is ingeschakeld, zoals hieronder wordt weer gegeven.
    
    ![Pagina netwerk instellingen van lokale webinterface](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Houd bij het configureren van netwerk instellingen het volgende in acht:

    - Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. Er wordt automatisch een IP-adres, subnet, gateway en DNS toegewezen.
    - Als DHCP niet is ingeschakeld, kunt u indien nodig statische IP-adressen toewijzen.
    - U kunt uw netwerk interface configureren als IPv4.

     >[!NOTE] 
     > Het is raadzaam om het lokale IP-adres van de netwerk interface niet te wijzigen van statisch naar DHCP, tenzij u een ander IP-adres hebt om verbinding te maken met het apparaat. Als u één netwerk interface gebruikt en u overschakelt naar DHCP, is er geen manier om het DHCP-adres te bepalen. Als u een DHCP-adres wilt wijzigen, wacht u totdat het apparaat is geregistreerd bij de service en wijzigt u vervolgens. U kunt vervolgens de IP-adressen van alle adapters weer geven in de eigenschappen van het **apparaat** in de Azure portal voor uw service.

3. Configureer desgewenst uw webproxyserver. Hoewel de configuratie van de webproxy optioneel is, kunt u deze alleen op deze pagina configureren als u een webproxy gebruikt.
   
   ![Pagina Web Proxy-instellingen voor lokale webinterfaces](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Ga als volgt te werk op de pagina **Web Proxy** :
   
   1. Voer in het vak **webproxy-URL** de URL in deze indeling in: `http://&lt;host-IP address or FQDN&gt;:Port number`. HTTPS-Url's worden niet ondersteund.
   2. Onder **verificatie**selecteert u **geen** of **NTLM**.
   3. Als u verificatie gebruikt, voert u een **gebruikers naam** en **wacht woord**in.
   4. Selecteer **Toep assen**om de geconfigureerde web proxy-instellingen te valideren en toe te passen.

4. Beschrijving Selecteer in het linkerdeel venster **tijd instellingen**en configureer vervolgens de tijd zone en de primaire en secundaire NTP-servers voor uw apparaat. 

    NTP-servers zijn vereist omdat uw apparaat tijd moet synchroniseren zodat het kan worden geverifieerd bij uw Cloud serviceproviders.
    
    ![Pagina tijd instellingen van lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Ga als volgt te werk op de pagina **tijd instellingen** :
    
    1. Selecteer in de vervolg keuzelijst **tijd zone** de tijd zone die overeenkomt met de geografische locatie waar het apparaat wordt geïmplementeerd.
        De standaard tijd zone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.

    2. Geef een **primaire NTP-server** voor uw apparaat op of accepteer de standaard waarde van `time.windows.com`.   
        Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet.

    3. Voer desgewenst in het vak **secundaire NTP-server** een secundaire server in voor uw apparaat.

    4. Selecteer **Toep assen**om de geconfigureerde tijd instellingen te valideren en toe te passen.

6. Selecteer in het linkerdeel venster **Cloud instellingen**en activeer vervolgens uw apparaat met de data Box Gateway-service in azure Portal.
    
    1. Voer in het vak **activerings sleutel** de **activerings sleutel** in die u hebt ontvangen voor [de activerings sleutel](data-box-gateway-deploy-prep.md#get-the-activation-key) voor data Box gateway.

    2. Selecteer **activeren**.
       
         ![Pagina Cloud instellingen van de lokale webinterface](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Het apparaat wordt geactiveerd en essentiële updates, indien beschikbaar, worden automatisch toegepast. U ziet een melding voor dat effect. Controleer de voortgang van de update via de Azure Portal.

        ![Pagina Cloud instellingen van de lokale webinterface](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Het dialoog venster bevat ook een herstel sleutel die u moet kopiëren en opslaan op een veilige locatie. Deze sleutel wordt gebruikt om uw gegevens te herstellen in het geval dat het apparaat niet kan worden opgestart.**


    4. Mogelijk moet u enkele minuten wachten totdat de update is voltooid. Nadat de update is voltooid, meldt u zich aan bij het apparaat. De pagina **Cloud instellingen** wordt bijgewerkt om aan te geven dat het apparaat is geactiveerd.

        ![De pagina Cloud instellingen van de lokale webinterface is bijgewerkt](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

De installatie van het apparaat is voltooid. U kunt nu shares toevoegen aan uw apparaat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Verbinding maken met een virtueel apparaat
> * Het virtuele apparaat instellen en activeren

Zie voor meer informatie over het overdragen van gegevens met uw Data Box Gateway:

> [!div class="nextstepaction"]
> [Gegevens overdragen met data Box gateway](./data-box-gateway-deploy-add-shares.md).
