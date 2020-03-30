---
title: Zelfstudie om verbinding te maken met azure data box edge-apparaat in Azure-portal | Microsoft Documenten
description: Zelfstudie voor het implementeren van Data Box Edge geeft u de opdracht om verbinding te maken, het instellen en activeren van uw fysieke apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 144c59c8bc24e8e10584702ec6cd48f7aa8c15c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239054"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Zelfstudie: Azure Data Box Edge verbinden, instellen en activeren 

In deze zelfstudie wordt beschreven hoe u verbinding maken met uw Azure Data Box Edge-apparaat, met behulp van de lokale web-gebruikersinterface.

Het installatie- en activeringsproces kan ongeveer 20 minuten in beslag nemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verbinding maken met een fysiek apparaat
> * Het fysieke apparaat instellen en activeren

## <a name="prerequisites"></a>Vereisten

Voordat u uw Data Box Edge-apparaat configureert en instelt, moet u ervoor zorgen dat:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [De rand van het gegevensvak installeren.](data-box-edge-deploy-install.md)
* U beschikt over de activeringssleutel van de Data Box Edge-service die u hebt gemaakt om het Data Box Edge-apparaat te beheren. Ga voor meer informatie naar [Voorbereiden om Azure Data Box Edge te implementeren.](data-box-edge-deploy-prep.md)

## <a name="connect-to-the-local-web-ui-setup"></a>Verbinding maken met de lokale web-gebruikersinterface-installatie 

1. Configureer de Ethernet-adapter op uw computer om verbinding te maken met het Data Box Edge-apparaat met een statisch IP-adres van 192.168.100.5 en subnet 255.255.255.0.

2. Sluit de computer aan op POORT 1 op uw apparaat. Gebruik de volgende illustratie om POORT 1 op uw apparaat te identificeren.

    ![Achterpaneel van een bekabeld apparaat](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Open een browservenster en krijg toegang tot `https://192.168.100.10`de lokale webgebruikersinterface van het apparaat op .  
    Deze actie kan enkele minuten duren nadat u het apparaat hebt ingeschakeld. 

    U ziet een fout of een waarschuwing die aangeeft dat er een probleem is met het beveiligingscertificaat van de website. 
   
    ![Foutbericht van websitebeveiligingscertificaat](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Selecteer **Doorgaan naar deze webpagina**.  
    Deze stappen kunnen variëren afhankelijk van de browser die u gebruikt.

5. Meld u aan bij de webgebruikersinterface van uw apparaat. Het standaardwachtwoord is *Password1*. 
   
    ![Aanmeldingspagina gegevensvakrandapparaat](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. Wijzig bij de prompt het wachtwoord van de apparaatbeheerder.  
    Het nieuwe wachtwoord moet tussen de 8 en 16 tekens bevatten. Het moet drie van de volgende tekens bevatten: hoofdletters, kleine letters, numerieke en speciale tekens.

U bevindt zich nu op het dashboard van uw apparaat.

## <a name="set-up-and-activate-the-physical-device"></a>Het fysieke apparaat instellen en activeren
 
Op uw dashboard worden de verschillende instellingen weergegeven die nodig zijn om het fysieke apparaat te configureren en te registreren met de Data Box Edge-service. De **instellingen voor apparaatnaam,** **netwerkinstellingen,** **webproxy**en **Tijd** zijn optioneel. De enige vereiste instellingen zijn **Cloud-instellingen.**
   
![Pagina Lokale webgebruikersinterface "Dashboard"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. Selecteer **apparaatnaam**in het linkerdeelvenster en voer vervolgens een vriendelijke naam voor uw apparaat in.  
    De vriendelijke naam moet 1 tot 15 tekens bevatten en letters, cijfers en koppeltekens bevatten.

    ![Pagina Lokale webgebruikersinterface 'Apparaatnaam'](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Optioneel) Selecteer in het linkerdeelvenster **netwerkinstellingen** en configureer de instellingen.  
    Op uw fysieke apparaat zijn er zes netwerkinterfaces. PORT 1 en PORT 2 zijn 1 Gbps netwerkinterfaces. PORT 3, PORT 4, PORT 5 en PORT 6 zijn allemaal 25 Gbps netwerkinterfaces die ook kunnen dienen als 10-Gbps netwerkinterfaces. PORT 1 is automatisch geconfigureerd als een poort met alleen beheeren en PORT 2 naar PORT 6 zijn alle datapoorten. De pagina **Netwerkinstellingen** is zoals hieronder weergegeven.
    
    ![Pagina Lokale webgebruikersinterface 'Netwerkinstellingen'](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Houd er bij het configureren van de netwerkinstellingen rekening mee:

   - Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. Een IP-adres, subnet, gateway en DNS worden automatisch toegewezen.
   - Als DHCP niet is ingeschakeld, u indien nodig statische IP's toewijzen.
   - U uw netwerkinterface configureren als IPv4.

     >[!NOTE] 
     > We raden u aan het lokale IP-adres van de netwerkinterface niet over te schakelen van statisch naar DCHP, tenzij u een ander IP-adres hebt om verbinding te maken met het apparaat. Als u één netwerkinterface gebruikt en u overschakelt naar DHCP, is er geen manier om het DHCP-adres te bepalen. Als u wilt overstappen naar een DHCP-adres, wacht u tot het apparaat zich bij de service heeft geregistreerd en wijzigt u deze. Vervolgens u de IP's van alle adapters in de **eigenschappen van het apparaat** bekijken in de Azure-portal voor uw service.

3. (Optioneel) Selecteer in het linkerdeelvenster **webproxy-instellingen**en configureer vervolgens de webproxyserver. Hoewel webproxyconfiguratie optioneel is, u deze alleen op deze pagina configureren als u een webproxy gebruikt.
   
   ![Pagina Lokale webgebruikersinterface 'Webproxy-instellingen'](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Ga als volgt te werk op de pagina **Webproxy-instellingen:**
   
   a. Voer in het vak **URL van webproxy** de URL in deze indeling in: `http://host-IP address or FQDN:Port number`. HTTPS-URL's worden niet ondersteund.

   b. Selecteer **Onder Verificatie**de optie **Geen** of **NTLM**.

   c. Als u verificatie gebruikt, voert u een gebruikersnaam en wachtwoord in.

   d. Als u de geconfigureerde webproxy-instellingen wilt valideren en toepassen, selecteert u **Instellingen toepassen**.
   
   > [!NOTE]
   > Pac-bestanden (Proxy-auto config) worden niet ondersteund. Een PAC-bestand definieert hoe webbrowsers en andere gebruikersagents automatisch de juiste proxyserver (toegangsmethode) kunnen kiezen voor het ophalen van een bepaalde URL.
   > Proxy's die proberen om al het verkeer te onderscheppen en te lezen (dan alles opnieuw ondertekenen met hun eigen certificering) zijn niet compatibel omdat het cert van de proxy niet wordt vertrouwd.
   > Doorgaans werken transparante proxy's goed met Azure Data Box Edge.

4. (Optioneel) Selecteer in het linkerdeelvenster **Tijdinstellingen**en configureer vervolgens de tijdzone en de primaire en secundaire NTP-servers voor uw apparaat.  
    NTP-servers zijn vereist omdat uw apparaat de tijd moet synchroniseren, zodat het kan verifiëren met uw cloudserviceproviders.
       
    Ga als volgt te werk op de pagina **Tijdinstellingen:**
    
    1. Selecteer **in** de vervolgkeuzelijst Tijdzone de tijdzone die overeenkomt met de geografische locatie waarin het apparaat wordt geïmplementeerd.
        De standaardtijdzone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.

    2. Voer in het vak **Primaire NTP-server** de primaire server voor uw apparaat in of accepteer de standaardwaarde van time.windows.com.  
        Zorg ervoor dat uw netwerk NTP-verkeer kan passeren van uw datacenter naar het internet.

    3. Voer optioneel in het vak **Secundaire NTP-server** een secundaire server voor uw apparaat in.

    4. Als u de geconfigureerde tijdinstellingen wilt valideren en toepassen, selecteert u **Instellingen toepassen**.

        ![Pagina Lokale webgebruikersinterface 'Tijdinstellingen'](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Optioneel) Selecteer in het linkerdeelvenster **de optie Opslaginstellingen** om de opslagtolerantie op uw apparaat te configureren. Deze functie is momenteel beschikbaar als preview-product. Standaard is de opslag op het apparaat niet veerkrachtig en is er gegevensverlies als een gegevensschijf op het apparaat uitvalt. Wanneer u de optie Veerkrachtig inschakelt, wordt de opslag op het apparaat opnieuw geconfigureerd en kan het apparaat bestand zijn tegen het uitvallen van één gegevensschijf zonder gegevensverlies. Als u de opslag als veerkrachtig configureert, vermindert u de bruikbare capaciteit van uw apparaat.

    > [!IMPORTANT] 
    > De tolerantie kan alleen worden geconfigureerd voordat u het apparaat activeert. 

    ![Pagina Lokale webgebruikersinterface 'Opslaginstellingen'](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. Selecteer in het linkerdeelvenster **Cloudinstellingen**en activeer uw apparaat vervolgens met de Data Box Edge-service in de Azure-portal.
    
    1. Voer in het vak **Activeringssleutel** de activeringssleutel in die u hebt in [De activeringssleutel](data-box-edge-deploy-prep.md#get-the-activation-key) voor Gegevensvakrand opvragen.
    2. Selecteer **Toepassen**.
       
        ![Pagina Lokale webgebruikersinterface "Cloud-instellingen"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Eerst wordt het apparaat geactiveerd. Het apparaat wordt vervolgens gescand voor kritieke updates en indien beschikbaar worden de updates automatisch toegepast. U ziet een melding in die zin.

        Het dialoogvenster heeft ook een herstelsleutel die u moet kopiëren en opslaan op een veilige locatie. Deze sleutel wordt gebruikt om uw gegevens te herstellen in het geval het apparaat niet kan opstarten.

        ![Pagina lokale webgebruikersinterface 'Cloud-instellingen' bijgewerkt](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Het kan nodig zijn om enkele minuten te wachten nadat de update is voltooid. De pagina wordt bijgewerkt om aan te geven dat het apparaat is geactiveerd.

        ![Pagina lokale webgebruikersinterface 'Cloud-instellingen' bijgewerkt](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

De installatie van het apparaat is voltooid. Je nu aandelen toevoegen op je apparaat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Verbinding maken met een fysiek apparaat
> * Het fysieke apparaat instellen en activeren

Zie voor meer informatie over het overbrengen van gegevens met uw Data Box Edge-apparaat:

> [!div class="nextstepaction"]
> [Gegevens overbrengen met Data Box Edge](./data-box-edge-deploy-add-shares.md).
