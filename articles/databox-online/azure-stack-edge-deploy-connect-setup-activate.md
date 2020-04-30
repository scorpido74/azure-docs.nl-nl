---
title: Zelf studie voor het maken van verbinding met, configureren en activeren van Azure Stack edge-apparaat in Azure Portal | Microsoft Docs
description: Zelf studie voor het implementeren van Azure Stack Edge geeft u de mogelijkheid om uw fysieke apparaat te verbinden, in te stellen en te activeren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 7c8a35b2699035b3ce4f96a94ca970da2cf343c4
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570606"
---
# <a name="tutorial-connect-set-up-and-activate-azure-stack-edge"></a>Zelf studie: Azure Stack Edge verbinden, instellen en activeren 

In deze zelf studie wordt beschreven hoe u met behulp van de lokale webinterface verbinding kunt maken met uw Azure Stack edge-apparaat en hoe u deze activeert.

Het installatie-en activerings proces kan ongeveer 20 minuten duren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verbinding maken met een fysiek apparaat
> * Het fysieke apparaat instellen en activeren

## <a name="prerequisites"></a>Vereisten

Voordat u uw Azure Stack edge-apparaat configureert en instelt, moet u het volgende doen:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure stack Edge installeren](azure-stack-edge-deploy-install.md).
* U hebt de activerings sleutel van de Azure Stack Edge-service die u hebt gemaakt om het Azure Stack edge-apparaat te beheren. Ga naar [voorbereiden voor de implementatie van Azure stack Edge](azure-stack-edge-deploy-prep.md)voor meer informatie.

## <a name="connect-to-the-local-web-ui-setup"></a>Verbinding maken met de lokale web-UI instellen 

1. Configureer de Ethernet-adapter op uw computer om verbinding te maken met het Azure Stack edge-apparaat met een statisch IP-adres van 192.168.100.5 en subnet 255.255.255.0.

2. Verbind de computer met poort 1 op het apparaat. Gebruik de volgende afbeelding om poort 1 op uw apparaat te identificeren.

    ![Achterpaneel van een bekabeld apparaat](./media/azure-stack-edge-deploy-install/backplane-cabled.png)


3. Open een browser venster en ga naar de lokale web-UI van het `https://192.168.100.10`apparaat op.  
    Deze actie kan enkele minuten duren nadat u het apparaat hebt ingeschakeld. 

    Er wordt een fout bericht of een waarschuwing weer gegeven waarin wordt aangegeven dat er een probleem is met het beveiligings certificaat van de website. 
   
    ![Fout bericht van beveiligings certificaat voor website](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Selecteer **door gaan naar deze webpagina**.  
    Deze stappen kunnen variëren, afhankelijk van de browser die u gebruikt.

5. Meld u aan bij de webgebruikersinterface van uw apparaat. Het standaard wachtwoord is *Wachtwoord1*. 
   
    ![Aanmeldings pagina van Azure Stack edge-apparaat](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. Wijzig bij de prompt het beheerders wachtwoord van het apparaat.  
    Het nieuwe wacht woord moet tussen 8 en 16 tekens bevatten. De naam moet drie van de volgende tekens bevatten: hoofd letters, kleine letters, cijfers en speciale tekens.

U bent nu klaar met het dash board van uw apparaat.

## <a name="set-up-and-activate-the-physical-device"></a>Het fysieke apparaat instellen en activeren
 
Uw dash board geeft de verschillende instellingen weer die nodig zijn voor het configureren en registreren van het fysieke apparaat bij de service Azure Stack Edge. De **apparaatnaam**, **netwerk instellingen**, **webproxy-instellingen**en **tijd instellingen** zijn optioneel. De enige vereiste instellingen zijn **Cloud instellingen**.
   
![De pagina dash board van lokale webgebruikersinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. Selecteer **apparaatnaam**in het linkerdeel venster en geef een beschrijvende naam op voor het apparaat.  
    De beschrijvende naam moet tussen 1 en 15 tekens lang zijn en mag alleen letters, cijfers en afbreek streepjes bevatten.

    ![Pagina apparaatnaam van lokale webgebruikersinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. Beschrijving Selecteer in het linkerdeel venster **netwerk instellingen** en configureer vervolgens de instellingen.  
    Op het fysieke apparaat zijn er zes netwerk interfaces. POORT 1 en poort 2 zijn 1 Gbps netwerk interfaces. POORT 3, poort 4, poort 5 en poort 6 zijn alle netwerk interfaces van 25 Gbps die ook kunnen dienen als 10-Gbps-netwerk interfaces. POORT 1 wordt automatisch geconfigureerd als een beheer poort en poort 2 tot poort 6 zijn alle gegevens poorten. De pagina **netwerk instellingen** is zoals hieronder wordt weer gegeven.
    
    ![Pagina netwerk instellingen van lokale webinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Houd bij het configureren van de netwerk instellingen het volgende in acht:

   - Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. Er wordt automatisch een IP-adres, subnet, gateway en DNS toegewezen.
   - Als DHCP niet is ingeschakeld, kunt u indien nodig statische IP-adressen toewijzen.
   - U kunt uw netwerk interface configureren als IPv4.

     >[!NOTE] 
     > Het is raadzaam om het lokale IP-adres van de netwerk interface niet te wijzigen van statisch naar DHCP, tenzij u een ander IP-adres hebt om verbinding te maken met het apparaat. Als u één netwerk interface gebruikt en u overschakelt naar DHCP, is er geen manier om het DHCP-adres te bepalen. Als u een DHCP-adres wilt wijzigen, wacht u totdat het apparaat is geregistreerd bij de service en wijzigt u vervolgens. U kunt vervolgens de IP-adressen van alle adapters weer geven in de eigenschappen van het **apparaat** in de Azure portal voor uw service.

3. Beschrijving Selecteer in het linkerdeel venster **web proxy-instellingen**en configureer vervolgens uw webproxyserver. Hoewel de configuratie van de webproxy optioneel is, kunt u deze alleen op deze pagina configureren als u een webproxy gebruikt.
   
   ![Pagina Web Proxy-instellingen voor lokale webinterfaces](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Ga als volgt te werk op de pagina **web proxy-instellingen** :
   
   a. Voer in het vak **webproxy-URL** de URL in deze indeling in `http://host-IP address or FQDN:Port number`:. HTTPS-Url's worden niet ondersteund.

   b. Onder **verificatie**selecteert u **geen** of **NTLM**.

   c. Als u verificatie gebruikt, voert u een gebruikers naam en wacht woord in.

   d. Selecteer **instellingen Toep assen**om de geconfigureerde web proxy-instellingen te valideren en toe te passen.
   
   > [!NOTE]
   > De bestanden voor het automatisch configureren van de proxy (PAC) worden niet ondersteund. Een PAC-bestand definieert hoe webbrowsers en andere gebruikers agenten automatisch de juiste proxy server (toegangs methode) kunnen kiezen voor het ophalen van een bepaalde URL.
   > Proxy's die al het verkeer proberen te onderscheppen en lezen (en vervolgens alles opnieuw ondertekenen met hun eigen certificering) zijn niet compatibel, omdat het certificaat van de proxy niet wordt vertrouwd.
   > Normaal gesp roken werken transparante proxy's goed met Azure Stack Edge.

4. Beschrijving Selecteer in het linkerdeel venster **tijd instellingen**en configureer vervolgens de tijd zone en de primaire en secundaire NTP-servers voor uw apparaat.  
    NTP-servers zijn vereist omdat uw apparaat tijd moet synchroniseren zodat het kan worden geverifieerd bij uw Cloud serviceproviders.
       
    Ga als volgt te werk op de pagina **tijd instellingen** :
    
    1. Selecteer in de vervolg keuzelijst **tijd zone** de tijd zone die overeenkomt met de geografische locatie waar het apparaat wordt geïmplementeerd.
        De standaard tijd zone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.

    2. Voer in het vak **primaire NTP-server** de primaire server voor uw apparaat in of accepteer de standaard waarde van time.Windows.com.  
        Zorg ervoor dat uw netwerk NTP-verkeer kan door geven van uw Data Center naar Internet.

    3. Voer desgewenst in het vak **secundaire NTP-server** een secundaire server in voor uw apparaat.

    4. Selecteer **instellingen Toep assen**om de geconfigureerde tijd instellingen te valideren en toe te passen.

        ![Pagina tijd instellingen van lokale webgebruikersinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. Beschrijving Selecteer **opslag instellingen** in het linkerdeel venster om de opslag tolerantie op uw apparaat te configureren. Deze functie is momenteel beschikbaar als preview-product. De opslag op het apparaat is standaard niet robuust en er zijn gegevens verlies als een gegevens schijf op het apparaat mislukt. Wanneer u de optie voor robuuste gegevens inschakelt, wordt de opslag op het apparaat opnieuw geconfigureerd en kan het apparaat de storing van één gegevens schijf zonder gegevens verlies tot gevolg hebben. Als u de opslag configureert als een robuuste schijf, vermindert u de bruikbare capaciteit van uw apparaat.

    > [!IMPORTANT] 
    > De tolerantie kan alleen worden geconfigureerd voordat u het apparaat activeert. 

    ![Pagina opslag instellingen van de lokale webinterface](./media/azure-stack-edge-deploy-connect-setup-activate/storage-settings.png)

6. Selecteer in het linkerdeel venster **Cloud instellingen**en activeer vervolgens uw apparaat met de Azure stack Edge-service in de Azure Portal.
    
    1. Voer in het vak **activerings sleutel** de activerings sleutel in die u hebt ontvangen voor [de activerings sleutel](azure-stack-edge-deploy-prep.md#get-the-activation-key) voor Azure stack Edge.
    2. Selecteer **Toepassen**.
       
        ![Pagina Cloud instellingen van de lokale webinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. De eerste keer dat het apparaat wordt geactiveerd. Het apparaat wordt vervolgens gescand op essentiële updates en, indien beschikbaar, worden de updates automatisch toegepast. U ziet een melding voor dat effect.

        Het dialoog venster bevat ook een herstel sleutel die u moet kopiëren en opslaan op een veilige locatie. Deze sleutel wordt gebruikt om uw gegevens te herstellen in het geval dat het apparaat niet kan worden opgestart.

        ![De pagina Cloud instellingen van de lokale webinterface is bijgewerkt](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Mogelijk moet u enkele minuten wachten nadat de update is voltooid. De pagina wordt bijgewerkt om aan te geven dat het apparaat is geactiveerd.

        ![De pagina Cloud instellingen van de lokale webinterface is bijgewerkt](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-8.png)

De installatie van het apparaat is voltooid. U kunt nu shares toevoegen aan uw apparaat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Verbinding maken met een fysiek apparaat
> * Het fysieke apparaat instellen en activeren

Zie voor meer informatie over het overdragen van gegevens met uw Azure Stack edge-apparaat:

> [!div class="nextstepaction"]
> [Gegevens overdragen met Azure stack Edge](./azure-stack-edge-deploy-add-shares.md).
