---
title: Zelfstudie:Verkeer routeren naar gewogen eindpunten - Azure Traffic Manager
description: In dit artikel wordt beschreven hoe u verkeer routeert naar gewogen eindpunten met behulp van Traffic Manager.
services: traffic-manager
author: rohinkoul
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: rohink
ms.openlocfilehash: a4738b2e36786cd627f53af3e36bd8f1e3fbc375
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76939486"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Zelfstudie: Verkeersroutering met gewogen eindpunten beheren met behulp van Traffic Manager

In deze zelfstudie wordt beschreven hoe u Azure Traffic Manager gebruikt om de routering van gebruikersverkeer tussen eindpunten te beheren via de methode voor gewogen routering. Met deze routeringsmethode kunt u het gewicht toewijzen aan elk eindpunt in de profielconfiguratie van Traffic Manager. Gebruikersverkeer wordt vervolgens gerouteerd op basis van het gewicht dat is toegewezen aan elk eindpunt. Het gewicht is een geheel getal tussen 1 en 1.000. Hoe hoger het gewicht dat is toegewezen aan een eindpunt, hoe hoger de prioriteit van het eindpunt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele machines maken met daarop een eenvoudige website in IIS.
> * Twee virtuele machines voor tests maken om Traffic Manager in actie te zien.
> * Een DNS-naam configureren voor de VM's met behulp van IIS.
> * Een Traffic Manager-profiel maken.
> * VM-eindpunten toevoegen aan het Traffic Manager-profiel.
> * Zie Traffic Manager in werking.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Traffic Manager in actie wilt zien, implementeert u het volgende voor deze zelfstudie:

- Twee exemplaren van basiswebsites die worden uitgevoerd in verschillende Azure-regio's: VS - oost en Europa - west.
- Twee virtuele machines voor het testen van de Traffic Manager: één in VS - oost en de andere in Europa - west. De test-VM’s worden gebruikt om te illustreren hoe gebruikersverkeer via Traffic Manager naar een website wordt gerouteerd met een bijbehorend eindpunt waaraan een hoger gewicht is toegewezen.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

### <a name="create-websites"></a>Websites maken

In dit gedeelte maakt u twee website-instanties die de twee service-eindpunten voor het Traffic Manager-profiel vormen in twee Azure-regio’s. Voltooi de volgende stappen om de twee websites te maken:

1. Maak twee virtuele machines om een eenvoudige website uit te voeren: een in VS - oost en de andere in Europa - west.
2. Installeer een IIS-server op elke virtuele machine. Werk de standaardwebpagina bij die de naam beschrijft van de virtuele machine waarmee een gebruiker is verbonden als deze de website bezoekt.

#### <a name="create-vms-for-running-websites"></a>Virtuele machines maken voor het uitvoeren van websites

In deze sectie maakt u twee VM's *(myIISVMEastUS* en *myIISVMWestEurope)* in de Azure-regio's Oost-VS en West-Europa.

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

#### <a name="install-iis-and-customize-the-default-webpage"></a>IIS installeren en de standaardwebpagina aanpassen

In deze sectie installeert u de IIS-server op de twee VM's myIISVMEastUS en myIISVMWestEurope en werkt u de standaardwebpagina bij. De aangepaste webpagina geeft de naam weer van de virtuele machine waarmee u verbinding maakt als u de website in een webbrowser bezoekt.

1. Selecteer **Alle resources** in het menu aan de linkerkant. Selecteer in de lijst met resources **myIISVMEastUS** in de resourcegroep **myResourceGroupTM1**.
2. Selecteer op de pagina **Overzicht** de optie **Verbinding maken**. Selecteer **RDP-bestand downloaden**in **Verbinding maken met virtuele machine**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer u hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven tijdens het maken van de virtuele machine. Mogelijk moet u **Meer opties** > selecteren**Gebruik een ander account**om de referenties op te geven die u hebt ingevoerd toen u de vm maakte.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** met de verbinding.
6. Blader op het serverbureaublad naar **Windows Systeembeheer** > **Serverbeheer**.
7. Open Windows PowerShell op VM1. Gebruik de volgende opdrachten om de IIS-server te installeren en het standaard htm-bestand bij te werken.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![IIS installeren en de webpagina aanpassen](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Sluit de RDP-verbinding met **myIISVMEastUS**.
9. Herhaal de stappen 1-8. Maak een RDP-verbinding met de VM **myIISVMWestEurope** binnen de **myResourceGroupTM2-brongroep** om IIS te installeren en de standaardwebpagina aan te passen.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>DNS-namen voor virtuele machines configureren met behulp van IIS

Traffic Manager routeert gebruikersverkeer op basis van de DNS-naam van de service-eindpunten. In deze sectie configureert u de DNS-namen voor de IIS-servers myIISVMEastUS en myIISVMWestEurope.

1. Selecteer **Alle resources** in het menu aan de linkerkant. Selecteer in de lijst met resources **myIISVMEastUS** in de resourcegroep **myResourceGroupTM1**.
2. Selecteer op de pagina **Overzicht** onder **DNS-naam** de optie **Configureren**.
3. Voeg op de pagina **Configuratie** onder het label DNS-naam een unieke naam toe. Selecteer vervolgens **Opslaan**.
4. Herhaal stap 1-3 voor de VM met de naam **myIISVMWestEurope** in de **myResourceGroupTM2-resourcegroep.**

### <a name="create-a-test-vm"></a>Een test-VM maken

In deze sectie maakt u een VM (*myVMEastUS* en *myVMWestEurope*) in elke Azure-regio **(Oost-VS** en **West-Europa).** U gebruikt deze VM's om te testen hoe Traffic Manager verkeer doorstuurt naar het eindpunt van de website met de hogere gewichtswaarde.

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

Maak een Traffic Manager-profiel op basis van de routeringsmethode **Gewogen**.

1. Selecteer linksboven in het scherm de optie Een**netwerkbeheerprofiel** > maken voor een > **resourcenetwerkverkeer** > **maken**. **Create a resource**
2. Typ of selecteer de volgende informatie in **Traffic Manager-profiel**. Accepteer de standaardinstellingen voor de andere instellingen en selecteer **Vervolgens Maken**.

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Name                   | Voer een unieke naam binnen de trafficmanager.net-zone in. Dit resulteert in de DNS-naam trafficmanager.net, die wordt gebruikt voor toegang tot uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Selecteer de routeringsmethode **Gewogen**.                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer **Bestaande gebruiken** en selecteer vervolgens **myResourceGroupTM1**. |
    |        |   |

    ![Een Traffic Manager-profiel maken](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg de twee VM's met de IIS-servers myIISVMEastUS en myIISVMWestEurope toe om gebruikersverkeer naar hen te leiden.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt. Selecteer het profiel in de resultaten die worden weergegeven.
2. Selecteer in **Traffic Manager-profiel** in de sectie **Instellingen** de optie **Eindpunten** > **Toevoegen**.
3. Voer de volgende informatie in of selecteer deze. Accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**.

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Voer het Azure-eindpunt in.                                   |
    | Name           | Voer **myEastUSEndpoint** in.                                        |
    | Doelbrontype           | Selecteer **Openbaar IP-adres**.                          |
    | Doelbron          | Kies een openbaar IP-adres om het overzicht van resources met openbare IP-adressen onder hetzelfde abonnement weer te geven. Selecteer in **Resource** het openbare IP-adres met de naam **myIISVMEastUS-ip**. Dit is het openbare IP-adres van de IIS-server VM in VS - oost.|
    |  Gewicht      | Voer **100** in.        |
    |        |           |

4. Herhaal stap 2 en 3 om een ander eindpunt met de naam **myWestEuropeEndpoint** toe te voegen voor het openbare IP-adres **myIISVMWestEurope-ip.** Dit adres is gekoppeld aan de IIS-server VM met de naam myIISVMWestEurope. Voer voor **Gewicht****25** in.
5. Als beide eindpunten zijn toegevoegd, worden ze weergegeven in het Traffic Manager-profiel, samen met de controlestatus **Online**.

## <a name="test-the-traffic-manager-profile"></a>Het Traffic Manager-profiel testen

Voer de volgende stappen uit om Traffic Manager in actie te zien:

1. Bepaal de DNS-naam van uw Traffic Manager-profiel.
2. Zie Traffic Manager in werking.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>De DNS-naam van het Traffic Manager-profiel vaststellen

In deze zelfstudie maakt u voor het gemak gebruik van de DNS-naam van het Traffic Manager-profiel om de websites te bezoeken.

U kunt de DNS-naam van het Traffic Manager-profiel als volgt vaststellen:

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt. Selecteer het Traffic Manager-profiel in de resultaten die worden weergegeven.
2. Selecteer **Overzicht**.
3. Het Traffic Manager-profiel geeft de bijbehorende DNS-naam weer. In productie-implementaties configureert u een aangepaste domeinnaam om met behulp van een DNS CNAME-record naar de Traffic Manager-domeinnaam te verwijzen.

   ![DNS-naam van Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien

In dit gedeelte kunt u Traffic Manager in actie zien.

1. Selecteer **Alle resources** in het menu aan de linkerkant. Selecteer in de lijst met resources **myVMEastUS** in de resourcegroep **myResourceGroupTM1**.
2. Selecteer op de pagina **Overzicht** de optie **Verbinding maken**. Selecteer **RDP-bestand downloaden**in **Verbinding maken met virtuele machine**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer u hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** > selecteren**Gebruik een ander account**om de referenties op te geven die u hebt ingevoerd toen u de vm maakte.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** met de verbinding.
6. Voer in een webbrowser op de VM myVMEastUS de DNS-naam van uw Traffic Manager-profiel in om uw website weer te geven. U wordt doorgestuurd naar de website die wordt gehost op de IIS-server myIISVMEastUS omdat hieraan een hoger gewicht van **100** is toegewezen. De IIS-server myIISVMWestEurope krijgt een lagere eindpuntwaarde van **25**.

   ![Traffic Manager-profiel testen](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Herhaal stap 1-6 op de VM myVMWestEurope om de gewogen website reactie te zien.

## <a name="delete-the-traffic-manager-profile"></a>Het Traffic Manager-profiel verwijderen

Wanneer u de resourcegroepen die u in deze zelfstudie hebt gemaakt niet meer nodig hebt, kunt u ze verwijderen. Selecteer daarvoor de resourcegroep (**ResourceGroupTM1** of **ResourceGroupTM2**) en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verkeer routeren naar specifieke eindpunten op basis van de geografische locatie van de gebruiker](traffic-manager-configure-geographic-routing-method.md)
