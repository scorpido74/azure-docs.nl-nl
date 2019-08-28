---
title: Zelfstudie - verkeer routeren voor het verbeteren van de website-antwoord met behulp van Azure Traffic Manager
description: Deze zelfstudie artikel wordt beschreven hoe u een Traffic Manager-profiel voor het bouwen van een zeer responsieve website te maken.
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: allensu
ms.openlocfilehash: 304beeae02da5836ba88a56d7166fc681e263501
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258358"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Zelfstudie: Website-antwoord met Traffic Manager te verbeteren

In deze zelfstudie wordt beschreven hoe u Traffic Manager gebruiken om te maken van een zeer responsieve website door te leiden van gebruikersverkeer naar de website met de laagste latentie. Het datacenter met de laagste latentie is normaal gesproken degene die zich het dichtst in de geografische afstand.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele machines maken met daarop een eenvoudige website in IIS
> * Twee virtuele machines voor tests maken om Traffic Manager in actie te zien
> * DNS-naam configureren voor de VM’s met behulp van IIS
> * Voor de prestaties verbeterd website een Traffic Manager-profiel maken
> * VM-eindpunten toevoegen aan het Traffic Manager-profiel
> * Traffic Manager in werking zien

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet voor deze zelfstudie de volgende zaken implementeren om de Traffic Manager in actie te zien:

- twee exemplaren van basic websites die worden uitgevoerd in verschillende regio's Azure - **VS-Oost** en **West-Europa**.
- twee test-VM's voor het testen van het Traffic Manager - een virtuele machine in **VS-Oost** en de tweede virtuele machine in **West-Europa**. De test-VM's worden gebruikt om te laten zien hoe Traffic Manager gebruikersverkeer routeert naar de website die wordt uitgevoerd in dezelfde regio bevinden als het biedt de laagste latentie.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

### <a name="create-websites"></a>Websites maken

In dit gedeelte maakt u twee website-instanties die de twee service-eindpunten voor het Traffic Manager-profiel vormen in twee Azure-regio’s. Om de twee websites te maken, moeten de volgende stappen worden uitgevoerd:

1. Maak twee virtuele machines om een eenvoudige website uit te voeren: een in **US - oost** en de andere in **Europa - west**.
2. Installeer IIS-server op elke VM en werk de standaardpagina van de website bij die de naam beschrijft van de virtuele machine waarmee een gebruiker is verbonden als deze de website bezoekt.

#### <a name="create-vms-for-running-websites"></a>Virtuele machines maken voor het uitvoeren van websites

In deze sectie maakt u twee virtuele machines maken *myIISVMEastUS* en *myIISVMWestEurope* in de **VS-Oost** en **West-Europa** Azure-regio's.

1. In de rechterbovenhoek linkerbovenhoek van de Azure portal, selecteer **een resource maken** > **Compute** > **Windows Server 2019 Datacenter**.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Abonnement** > **Resourcegroep**: Selecteer **nieuw** en typ vervolgens **myResourceGroupTM1**.
   - **Instantiedetails** > **Naam van virtuele machine**: Type *myIISVMEastUS*.
   - **Details van exemplaar** > **regio**:  Selecteer **US - oost**.
   - **Administrator-Account** > **gebruikersnaam**:  Voer een gebruikersnaam naar keuze in.
   - **Administrator-Account** > **wachtwoord**:  Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regels voor binnenkomende poort** > **openbare binnenkomende poorten**: Selecteer **Geselecteerde poorten toestaan**.
   - **Regels voor binnenkomende poort** > **binnenkomende poorten selecteren**: Selecteer **RDP** en **HTTP** in de vervolgkeuzelijst vak.

3. Selecteer de **Management** tabblad of selecteer **volgende: Schijven** en vervolgens **Volgende: Netwerken**, klikt u vervolgens **volgende: Beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Controleer de instellingen en klik vervolgens op **maken**.  
6. Volg de stappen voor het maken van een tweede virtuele machine met de naam *myIISVMWestEurope*, met een **resourcegroep** naam van het *myResourceGroupTM2*, een **locatie**van *West-Europa*, en alle andere instellingen hetzelfde zijn als *myIISVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

   ![Een virtuele machine maken](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS installeren en de standaardwebpagina aanpassen

In deze sectie maakt u de IIS-server installeren op de twee virtuele machines *myIISVMEastUS* en *myIISVMWestEurope*, en werk vervolgens de standaardpagina van de website. De aangepaste websitepagina geeft de naam weer van de virtuele machine waarmee u verbinding maakt als u de website in een webbrowser bezoekt.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myIISVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
6. Ga op de serverdesktop naar **Windows Systeembeheer**>**Serverbeheer**.
7. Start Windows PowerShell op VM1 en gebruik de volgende opdrachten om de IIS-server te installeren en het standaard htm-bestand bij te werken.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![IIS installeren en de webpagina aanpassen](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Sluit de RDP-verbinding met *myIISVMEastUS*.
9. Herhaal stap 1-8 met door het maken van een RDP-verbinding met de virtuele machine *myIISVMWestEurope* binnen de *myResourceGroupTM2* resourcegroep IIS installeren en aanpassen van de standaard-webpagina.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>DNS-namen voor virtuele machines configureren met behulp van IIS

Traffic Manager routeert gebruikersverkeer op basis van de DNS-naam van de service-eindpunten. In deze sectie configureert u de DNS-namen voor de IIS-servers - *myIISVMEastUS* en *myIISVMWestEurope*.

1. Klik in het linkermenu op **Alle resources** en selecteer *myIISVMEastUS* in de lijst met resources, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Selecteer op de pagina **Overzicht** onder **DNS-naam** de optie **Configureren**.
3. Voeg op de pagina **Configuratie** onder het label DNS-naam een unieke naam toe en selecteer vervolgens **Opslaan**.
4. Herhaal stap 1-3 voor de virtuele machine met de naam *myIISVMWestEurope* die bevindt zich in de *myResourceGroupTM2* resourcegroep.

### <a name="create-test-vms"></a>Test-VM’s maken

In deze sectie maakt u een virtuele machine maken (*myVMEastUS* en *myVMWestEurope*) in elke Azure-regio (**VS-Oost** en **West-Europa**). U zult deze virtuele machines gebruiken om te testen hoe Traffic Manager verkeer naar de dichtstbijzijnde IIS-server routeert als u de website bezoekt.

1. In de rechterbovenhoek linkerbovenhoek van de Azure portal, selecteer **een resource maken** > **Compute** > **Windows Server 2019 Datacenter**.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupTM1**.
   - **Instantiedetails** > **Naam van virtuele machine**: Type *myVMEastUS*.
   - **Details van exemplaar** > **regio**:  Selecteer **US - oost**.
   - **Administrator-Account** > **gebruikersnaam**:  Voer een gebruikersnaam naar keuze in.
   - **Administrator-Account** > **wachtwoord**:  Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regels voor binnenkomende poort** > **openbare binnenkomende poorten**: Selecteer **Geselecteerde poorten toestaan**.
   - **Regels voor binnenkomende poort** > **binnenkomende poorten selecteren**: Selecteer **RDP** in de vervolgkeuzelijst vak.

3. Selecteer de **Management** tabblad of selecteer **volgende: Schijven** en vervolgens **Volgende: Netwerken**, klikt u vervolgens **volgende: Beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Controleer de instellingen en klik vervolgens op **maken**.  
6. Volg de stappen voor het maken van een tweede virtuele machine met de naam *myVMWestEurope*, met een **resourcegroep** naam van het *myResourceGroupTM2*, een **locatie** van *West-Europa*, en alle andere instellingen hetzelfde zijn als *myVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel met gebruikersverkeer verwezen door deze te verzenden naar het eindpunt met de laagste latentie.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Traffic Manager-profiel** > **Maken**.
2. Voer in  **Traffic Manager-profiel maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Name                   | Deze naam moet uniek zijn binnen de zone trafficmanager.net en resulteert in de DNS-naam, trafficmanager.net, die wordt gebruikt voor het openen van uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Selecteer de **prestaties** routeringsmethode.                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer de resourcegroep *myResourceGroupTM1*. |
    | Locatie                | Selecteer **US - oost**. Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het Traffic Manager-profiel dat wereldwijd wordt geïmplementeerd.                              |
    |

    ![Een Traffic Manager-profiel maken](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Toevoegen van de twee virtuele machines met de IIS-servers - *myIISVMEastUS* & *myIISVMWestEurope* gebruiker om verkeer te routeren naar de dichtstbijzijnde eindpunt voor de gebruiker.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt en selecteer het profiel in de weergegeven resultaten.
2. Klik in **Traffic Manager-profiel**, in de sectie **Instellingen**, op **Eindpunten** en vervolgens op **Toevoegen**.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Azure-eindpunt                                   |
    | Name           | myEastUSEndpoint                                        |
    | Doelbrontype           | Openbaar IP-adres                          |
    | Doelbron          | **Kies een openbaar IP-adres** om het overzicht van resources met openbare IP-adressen onder hetzelfde abonnement weer te geven. Selecteer in **Resource** het openbare IP-adres met de naam *myIISVMEastUS-ip*. Dit is het openbare IP-adres van de IIS-server VM in US - oost.|
    |        |           |

4. Herhaal stappen 2 en 3 om toe te voegen een ander eindpunt met de naam *myWestEuropeEndpoint* voor het openbare IP-adres *myIISVMWestEurope-IP-* dat is gekoppeld aan de virtuele machine met de naam van de IIS-server  *myIISVMWestEurope*.
5. Als beide eindpunten zijn toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**, samen met de controlestatus **Online**.

    ![Traffic Manager-eindpunt toevoegen](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In deze sectie maakt testen u hoe Traffic Manager gebruikersverkeer routeert naar de dichtstbijzijnde virtuele machines met de website voor minimale latentie. Voer de volgende stappen uit om de Traffic Manager in actie te zien:

1. Bepaal de DNS-naam van uw Traffic Manager-profiel.
2. Zie Traffic Manager als volgt in werking:
    - Van de virtuele testmachine (*myVMEastUS*) dat zich bevindt in de **VS-Oost** regio, in een webbrowser, blader naar de DNS-naam van uw Traffic Manager-profiel.
    - Van de virtuele testmachine (*myVMWestEurope*) dat zich bevindt in de **West-Europa** regio, in een webbrowser, blader naar de DNS-naam van uw Traffic Manager-profiel.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>De DNS-naam van het Traffic Manager-profiel vaststellen

In deze zelfstudie maakt u voor het gemak gebruik van de DNS-naam van het Traffic Manager-profiel om de websites te bezoeken.

U kunt de DNS-naam van het Traffic Manager-profiel als volgt vaststellen:

1. Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in de vorige sectie hebt gemaakt. Klik op het Traffic Manager-profiel in de resultaten die worden weergegeven.
2. Klik op **Overzicht**.
3. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. In productie-implementaties configureert u een aangepaste domeinnaam om met behulp van een DNS CNAME-record naar de Traffic Manager-domeinnaam te verwijzen.

   ![DNS-naam van Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien

In dit gedeelte kunt u Traffic Manager in werking zien.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
1. Typ in een webbrowser op de VM *myVMEastUS* de DNS-naam van uw Traffic Manager-profiel om uw website weer te geven. Omdat de virtuele machine zich in **VS-Oost**, u worden doorgestuurd naar de dichtstbijzijnde website die wordt gehost op de dichtstbijzijnde IIS-server *myIISVMEastUS* die bevindt zich in **VS-Oost**.

   ![Traffic Manager-profiel testen](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Vervolgens maakt u verbinding met de virtuele machine *myVMWestEurope* zich in **West-Europa** met behulp van de stappen 1-5 en blader naar de domeinnaam van het Traffic Manager-profiel van deze virtuele machine. Omdat de virtuele machine zich in **West-Europa**, u worden nu doorgestuurd naar de website die wordt gehost op dichtst bij de IIS-server *myIISVMWestEurope* die bevindt zich in **West-Europa**.

   ![Traffic Manager-profiel testen](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Het Traffic Manager-profiel verwijderen

Verwijder de resourcegroepen (**ResourceGroupTM1** en **ResourceGroupTM2**) als deze niet meer nodig zijn. Selecteer daarvoor de resourcegroep (**ResourceGroupTM1** of **ResourceGroupTM2**) en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verkeer naar een set eindpunten distribueren](traffic-manager-configure-weighted-routing-method.md)
