---
title: Zelfstudie - Websiterespons verbeteren met Azure Traffic Manager
description: In dit zelfstudieartikel wordt beschreven hoe u een Traffic Manager-profiel maakt om een zeer responsieve website te bouwen.
services: traffic-manager
author: rohinkoul
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: rohink
ms.openlocfilehash: f7778b60a5e84f5d8900b8e37bfa655a7915d403
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77136389"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Zelfstudie: Websiterespons verbeteren met Traffic Manager

In deze zelfstudie wordt beschreven hoe u Traffic Manager gebruiken om een zeer responsieve website te maken door gebruikersverkeer naar de website te leiden met de laagste latentie. Meestal is het datacenter met de laagste latentie het datacenter dat het dichtst bij geografische afstand staat.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele machines maken met daarop een eenvoudige website in IIS
> * Twee virtuele machines voor tests maken om Traffic Manager in actie te zien
> * DNS-naam configureren voor de VM’s met behulp van IIS
> * Een Traffic Manager-profiel maken voor betere websiteprestaties
> * VM-eindpunten toevoegen aan het Traffic Manager-profiel
> * Traffic Manager in werking zien

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet voor deze zelfstudie de volgende zaken implementeren om de Traffic Manager in actie te zien:

- Twee exemplaren van basiswebsites die worden uitgevoerd in verschillende Azure-regio's - **Oost-VS** en **West-Europa**.
- Twee test VM's voor het testen van de Traffic Manager - een VM in **Oost-VS** en de tweede VM in **West-Europa**. De test VM's worden gebruikt om te illustreren hoe Traffic Manager gebruikersverkeer doorstuurt naar de website die wordt uitgevoerd in dezelfde regio als de laagste latentie.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

### <a name="create-websites"></a>Websites maken

In dit gedeelte maakt u twee website-instanties die de twee service-eindpunten voor het Traffic Manager-profiel vormen in twee Azure-regio’s. Om de twee websites te maken, moeten de volgende stappen worden uitgevoerd:

1. Maak twee virtuele machines om een eenvoudige website uit te voeren: een in **VS - oost** en de andere in **Europa - west**.
2. Installeer IIS-server op elke VM en werk de standaardpagina van de website bij die de naam beschrijft van de virtuele machine waarmee een gebruiker is verbonden als deze de website bezoekt.

#### <a name="create-vms-for-running-websites"></a>Virtuele machines maken voor het uitvoeren van websites

In deze sectie maakt u twee VM's *myIISVMEastUS* en *myIISVMWestEurope* in de **Azure-regio's Oost-VS** en **West-Europa.**

1. Selecteer in de linkerbovenhoek van de Azure-portal de optie **Een resource** > **Compute** > **Windows Server 2019-datacenter maken**.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Subscription** > **Abonnementsbrongroep:** selecteer **Nieuw maken** en typ **myResourceGroupTM1**.
   - **Instantiedetails** > **Virtuele machinenaam:** Typ *myIISVMEastUS*.
   - **Instantiedetails** > **regio**: Selecteer **Oost-VS**.
   - **Administrator Account** > Gebruikersnaam van het**beheerdersaccount:** voer een gebruikersnaam van uw keuze in.
   - **Administrator Account** > **Beheerdersaccountwachtwoord:** voer een wachtwoord in naar keuze. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inkomende poortregels** > **Openbare binnenkomende poorten:** selecteer **Geselecteerde poorten toestaan**.
   - **Binnenkomende poortregels** > **Selecteer binnenkomende poorten:** Selecteer **RDP** en **HTTP** in het vak naar beneden trekken.

3. Selecteer het tabblad **Beheer** of selecteer **Volgende: schijven**en vervolgens **Volgende: Netwerken**en vervolgens **Volgende: Beheer**. Stel bij **Bewaking****Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Controleer de instellingen en klik op **Maken**.  
6. Volg de stappen om een tweede VM met de naam *myIISVMWestEurope*te maken , met een **resourcegroepnaam** van *myResourceGroupTM2*, een **locatie** in *West-Europa*, en alle andere instellingen hetzelfde als *myIISVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

   ![Een virtuele machine maken](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS installeren en de standaardwebpagina aanpassen

In deze sectie installeert u de IIS-server op de twee VM's *myIISVMEastUS* en *myIISVMWestEurope*en werkt u de standaardwebsitepagina bij. De aangepaste websitepagina geeft de naam weer van de virtuele machine waarmee u verbinding maakt als u de website in een webbrowser bezoekt.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myIISVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
6. Navigeer op het serverbureaublad naar **Windows Beheertools**>**Serverbeheer**.
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
9. Herhaal stap 1-8 met door een RDP-verbinding te maken met de VM *myIISVMWestEurope* binnen de *myResourceGroupTM2-brongroep* om IIS te installeren en de standaardwebpagina aan te passen.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>DNS-namen voor virtuele machines configureren met behulp van IIS

Traffic Manager routeert gebruikersverkeer op basis van de DNS-naam van de service-eindpunten. In deze sectie configureert u de DNS-namen voor de IIS-servers - *myIISVMEastUS* en *myIISVMWestEurope*.

1. Klik in het linkermenu op **Alle resources** en selecteer *myIISVMEastUS* in de lijst met resources, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Selecteer op de pagina **Overzicht** onder **DNS-naam** de optie **Configureren**.
3. Voeg op de pagina **Configuratie** onder het label DNS-naam een unieke naam toe en selecteer vervolgens **Opslaan**.
4. Herhaal stap 1-3 voor de VM met de naam *myIISVMWestEurope* die zich in de *brongroep myResourceGroupTM2* bevindt.

### <a name="create-test-vms"></a>Test-VM’s maken

In deze sectie maakt u een VM (*myVMEastUS* en *myVMWestEurope*) in elke Azure-regio **(Oost-VS** en **West-Europa).** U zult deze virtuele machines gebruiken om te testen hoe Traffic Manager verkeer naar de dichtstbijzijnde IIS-server routeert als u de website bezoekt.

1. Selecteer in de linkerbovenhoek van de Azure-portal de optie **Een resource** > **Compute** > **Windows Server 2019-datacenter maken**.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Abonnementsbrongroep** > **Resource Group**: Selecteer **myResourceGroupTM1**.
   - **Instantie details** > **Virtuele machine naam:** Typ *myVMEastUS*.
   - **Instantiedetails** > **regio**: Selecteer **Oost-VS**.
   - **Administrator Account** > Gebruikersnaam van het**beheerdersaccount:** voer een gebruikersnaam van uw keuze in.
   - **Administrator Account** > **Beheerdersaccountwachtwoord:** voer een wachtwoord in naar keuze. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inkomende poortregels** > **Openbare binnenkomende poorten:** selecteer **Geselecteerde poorten toestaan**.
   - **Binnenkomende poortregels** > **Selecteer binnenkomende poorten:** Selecteer **RDP** in het vak naar beneden trekken.

3. Selecteer het tabblad **Beheer** of selecteer **Volgende: schijven**en vervolgens **Volgende: Netwerken**en vervolgens **Volgende: Beheer**. Stel bij **Bewaking****Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Controleer de instellingen en klik op **Maken**.  
6. Volg de stappen om een tweede VM met de naam *myVMWestEurope*te maken , met een **resourcegroepnaam** van *myResourceGroupTM2,* een **locatie** in *West-Europa*en alle andere instellingen hetzelfde als *myVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel dat gebruikersverkeer leidt door ze naar het eindpunt met de laagste latentie te sturen.

1. Selecteer linksboven in het scherm de optie Een**netwerkverkeersbeheerprofiel** > maken voor een > **resourcenetwerkverkeer** > **maken**. **Create a resource**
2. Voer in ** Traffic Manager-profiel maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Name                   | Deze naam moet uniek zijn binnen de zone trafficmanager.net en resulteert in de DNS-naam, trafficmanager.net, die wordt gebruikt voor het openen van uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Selecteer de **routeringsmethode Prestaties.**                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer de resourcegroep *myResourceGroupTM1*. |
    | Locatie                | Selecteer **Oost-VS**. Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het Traffic Manager-profiel dat wereldwijd wordt geïmplementeerd.                              |
    |

    ![Een Traffic Manager-profiel maken](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg de twee VM's met de IIS-servers toe - *myIISVMEastUS* & *myIISVMWestEurope* om gebruikersverkeer naar het dichtstbijzijnde eindpunt naar de gebruiker te leiden.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt en selecteer het profiel in de weergegeven resultaten.
2. Klik in **Traffic Manager-profiel**, in de sectie **Instellingen**, op **Eindpunten** en vervolgens op **Toevoegen**.
3. Voer de volgende gegevens in of selecteer de volgende gegevens, accepteer de standaardinstellingen voor de overige instellingen en selecteer **OK:**

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Azure-eindpunt                                   |
    | Name           | myEastUSEndpoint                                        |
    | Doelbrontype           | Openbaar IP-adres                          |
    | Doelbron          | **Kies een openbaar IP-adres** om het overzicht van resources met openbare IP-adressen onder hetzelfde abonnement weer te geven. Selecteer in **Resource** het openbare IP-adres met de naam *myIISVMEastUS-ip*. Dit is het openbare IP-adres van de IIS-server VM in VS - oost.|
    |        |           |

4. Herhaal stap 2 en 3 om een ander eindpunt met de naam *myWestEuropeEndpoint* toe te voegen voor het openbare IP-adres *myIISVMWestEurope-ip* dat is gekoppeld aan de IIS-server VM genaamd *myIISVMWestEurope*.
5. Als beide eindpunten zijn toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**, samen met de controlestatus **Online**.

    ![Traffic Manager-eindpunt toevoegen](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In deze sectie test u hoe de verkeersbeheerder het gebruikersverkeer doorstuurt naar de dichtstbijzijnde VM's waarop de website wordt uitgevoerd, om minimale latentie te bieden. Voer de volgende stappen uit om de Traffic Manager in actie te zien:

1. Bepaal de DNS-naam van uw Traffic Manager-profiel.
2. Zie Traffic Manager als volgt in werking:
    - Blader vanaf de testVM *(myVMEastUS)* in de regio **Oost-VS** in een webbrowser naar de DNS-naam van uw Traffic Manager-profiel.
    - Blader vanaf de testVM *(myVMWestEurope)* in de regio **West-Europa** in een webbrowser naar de DNS-naam van uw Traffic Manager-profiel.

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
1. Typ in een webbrowser op de VM *myVMEastUS* de DNS-naam van uw Traffic Manager-profiel om uw website weer te geven. Sinds de VM in **Oost-VS,** wordt u doorgestuurd naar de dichtstbijzijnde website gehost op de dichtstbijzijnde IIS-server *myIISVMEastUS* die zich in **Oost-VS**.

   ![Traffic Manager-profiel testen](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Maak vervolgens verbinding met de VM *myVMWestEurope* in **West-Europa** met stap 1-5 en blader naar de domeinnaam Traffic Manager-profiel van deze VM. Sinds de VM in **West-Europa,** bent u nu doorgestuurd naar de website gehost op de dichtstbijzijnde IIS-server *myIISVMWestEurope* die is gevestigd in **West-Europa**.

   ![Traffic Manager-profiel testen](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Het Traffic Manager-profiel verwijderen

Verwijder de resourcegroepen (**ResourceGroupTM1** en **ResourceGroupTM2**) als deze niet meer nodig zijn. Selecteer daarvoor de resourcegroep (**ResourceGroupTM1** of **ResourceGroupTM2**) en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verkeer naar een set eindpunten distribueren](traffic-manager-configure-weighted-routing-method.md)
