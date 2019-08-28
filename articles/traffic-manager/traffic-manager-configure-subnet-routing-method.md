---
title: Methode voor routering van subnetverkeer configureren met behulp van Azure Traffic Manager
description: In dit artikel wordt uitgelegd hoe u Traffic Manager configureren om verkeer te routeren van specifieke subnetten.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: allensu
ms.openlocfilehash: 1a7bc38a91dc7621a3b09d7901c70eecb21101d6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060961"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Verkeer naar specifieke eindpunten routeren met Traffic Manager op basis van subnets van gebruiker

In dit artikel wordt beschreven hoe u de verkeersrouteringsmethode op basis van subnetten kunt configureren. Met de **Subnet**-verkeersrouteringsmethode kunt u een reeks IP-adresbereiken aan specifieke eindpunten koppelen als een aanvraag door Traffic Manager wordt ontvangen; de methode inspecteert het bron-IP-adres van de aanvraag en retourneert het eindpunt dat daar aan is gekoppeld.

In het scenario beschreven in dit artikel, met behulp van subnet routering, afhankelijk van het IP-adres van de query van de gebruiker, wordt een verkeer doorgestuurd naar een interne of een productiewebsite.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
U moet voor deze zelfstudie de volgende zaken implementeren om de Traffic Manager in actie te zien:
- twee basiswebsites in verschillende Azure-regio’s - **US - oost** (fungeert als interne website) en **Europa - west** (fungeert als productiewebsite).
- twee virtuele machines voor het testen van de Traffic Manager - Een VM in **US - oost** en de tweede VM in **Europa - west**.

De virtuele machines voor de tests worden gebruikt om te laten zien hoe Traffic Manager verkeer van gebruikers naar de interne website of de productiewebsite routeert op basis van het subnet waar de query van de gebruiker vandaan komt.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

### <a name="create-websites"></a>Websites maken

In dit gedeelte maakt u twee website-instanties die de twee service-eindpunten voor het Traffic Manager-profiel vormen in twee Azure-regio’s. Om de twee websites te maken, moeten de volgende stappen worden uitgevoerd:
1. Maak twee virtuele machines om een eenvoudige website uit te voeren: een in **US - oost** en de andere in **Europa - west**.
2. Installeer IIS-server op elke VM en werk de standaardpagina van de website bij die de naam beschrijft van de virtuele machine waarmee een gebruiker is verbonden als deze de website bezoekt.

#### <a name="create-vms-for-running-websites"></a>Virtuele machines maken voor het uitvoeren van websites
In deze sectie maakt u twee virtuele machines maken *myEndpointVMEastUS* en *myEndpointVMWEurope* in de **VS-Oost** en **West-Europa** Azure de regio's.

1. Selecteer **Een resource maken** > **Compute** > **Virtuele machine met Windows Server 2016** in de linkerbovenhoek van Azure Portal.
2. Voer de volgende informatie voor **Basisinformatie** in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Maken**:

    |Instelling|Waarde|
    |---|---|
    |Name|myIISVMEastUS|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resourcegroep| Selecteer **Nieuw** en typ *myResourceGroupTM1*.|
    |Locatie| Selecteer **US - oost**.|
    |||

4. Selecteer een VM-grootte onder **Kies een grootte**.
5. Selecteer de volgende waarden voor **Instellingen** en selecteer **OK**:
    
    |Instelling|Waarde|
    |---|---|
    |Virtueel netwerk| Selecteer **Virtueel netwerk** in **Virtueel netwerk maken** en voer voor **Naam** *myVNet1* en voor het subnet  *mySubnet* in.|
    |Netwerkbeveiligingsgroep|Selecteer **Basic** en selecteer in de vervolgkeuzelijst **Openbare binnenkomende poorten selecteren** de opties **HTTP** en **RDP** |
    |Diagnostische gegevens over opstarten|Selecteer **Uitgeschakeld**.|
    |||

6. Selecteer onder **Maken** in het **Overzicht** **Maken** om de implementatie van de VM te starten.

7. Volg de stappen 1-6 nogmaals, met de volgende wijzigingen:

    |Instelling|Waarde|
    |---|---|
    |Resourcegroep | Selecteer **Nieuw** en typ *myResourceGroupTM2*|
    |Locatie|Europa -west|
    |VM-naam | myIISVMWEurope|
    |Virtueel netwerk | Selecteer **Virtueel netwerk** in **Virtueel netwerk maken** en voer voor **Naam** *myVNet2* en voor het subnet  *mySubnet* in.|
    |||

8. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

   ![Een virtuele machine maken](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS installeren en de standaardwebpagina aanpassen

In dit gedeelte installeert u de IIS-server op de twee virtuele machines - *myIISVMEastUS*  & *myIISVMWEurope* en werkt u daarna de standaardpagina van de website bij. De aangepaste websitepagina geeft de naam weer van de virtuele machine waarmee u verbinding maakt als u de website in een webbrowser bezoekt.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myIISVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
6. Ga op de serverdesktop naar **Windows Systeembeheer**>**Serverbeheer**.
7. Start Windows PowerShell op *myIISVMEastUS* en IIS-server installeren en bijwerken van de standaard htm-bestand met de volgende opdrachten.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. Sluit de RDP-verbinding met *myIISVMEastUS*.
9. Herhaal stap 1-6 met door het maken van een RDP-verbinding met de virtuele machine *myIISVMWEurope* binnen de *myResourceGroupTM2* resourcegroep IIS installeren en aanpassen van de standaard-webpagina.
10. Start Windows PowerShell op *myIISVMWEurope* en IIS-server installeren en bijwerken van de standaard htm-bestand met de volgende opdrachten.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>DNS-namen voor virtuele machines configureren met behulp van IIS

Traffic Manager routeert gebruikersverkeer op basis van de DNS-naam van de service-eindpunten. In deze sectie configureert u de DNS-namen voor de IIS-servers - *myIISVMEastUS* en *myIISVMWEurope*.

1. Klik in het linkermenu op **Alle resources** en selecteer *myIISVMEastUS* in de lijst met resources, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Selecteer op de pagina **Overzicht** onder **DNS-naam** de optie **Configureren**.
3. Voeg op de pagina **Configuratie** onder het label DNS-naam een unieke naam toe en selecteer vervolgens **Opslaan**.
4. Herhaal stap 1 tot en met 3 voor de VM met de naam *myIISVMWEurope* die zich in de resourcegroep *myResourceGroupTM1* bevindt.

### <a name="create-test-vms"></a>Test-VM’s maken

In deze sectie maakt u een virtuele machine maken (*mVMEastUS* en *myVMWestEurope*) in elke Azure-regio (**VS-Oost** en **West-Europa**. U zult deze virtuele machines gebruiken om te testen hoe Traffic Manager verkeer naar de dichtstbijzijnde IIS-server routeert als u de website bezoekt.

1. Selecteer **Een resource maken** > **Compute** > **Virtuele machine met Windows Server 2016** in de linkerbovenhoek van Azure Portal.
2. Voer de volgende informatie voor **Basisinformatie** in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Maken**:

    |Instelling|Waarde|
    |---|---|
    |Name|myVMEastUS|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resourcegroep| Selecteer **Bestaande** en selecteer vervolgens *myResourceGroupTM1*.|
    |||

4. Selecteer een VM-grootte onder **Kies een grootte**.
5. Selecteer de volgende waarden voor **Instellingen** en selecteer **OK**:

    |Instelling|Waarde|
    |---|---|
    |Virtueel netwerk| Selecteer **Virtueel netwerk** in **Virtueel netwerk maken** en voer voor **Naam** *myVNet3* en voor het subnet *mySubnet3* in.|
    |Netwerkbeveiligingsgroep|Selecteer **Basic** en selecteer in de vervolgkeuzelijst **Openbare binnenkomende poorten selecteren** de opties **HTTP** en **RDP** |
    |Diagnostische gegevens over opstarten|Selecteer **Uitgeschakeld**.|
    |||

6. Selecteer onder **Maken** in het **Overzicht** **Maken** om de implementatie van de VM te starten.

7. Volg de stappen 1-5 nogmaals, maar dan met de volgende wijzigingen:

    |Instelling|Value|
    |---|---|
    |VM-naam | *myVMWEurope*|
    |Resourcegroep | Selecteer **Bestaande** en typ vervolgens *myResourceGroupTM2*|
    |Virtueel netwerk | Selecteer **Virtueel netwerk** in **Virtueel netwerk maken** en voer voor **Naam** *myVNet4* en voor het subnet *mySubnet4* in.|
    |||

8. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken
Maak een Traffic Manager-profiel waarmee u specifieke eindpunten kunt retourneren op basis van het bron-IP-adres van de aanvraag.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Traffic Manager-profiel** > **Maken**.
2. Voer in  **Traffic Manager-profiel maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Name                   | Deze naam moet uniek zijn binnen de zone trafficmanager.net en resulteert in de DNS-naam, trafficmanager.net, die wordt gebruikt voor het openen van uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Selecteer de routeringsmethode **Subnet**.                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer **Bestaande** en voer *myResourceGroupTM1* in. |
    | |                              |
    |

    ![Een Traffic Manager-profiel maken](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Toevoegen van de twee virtuele machines met de IIS-servers - *myIISVMEastUS* & *myIISVMWEurope* gebruiker om verkeer te routeren op basis van het subnet van de query van de gebruiker.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt en selecteer het profiel in de weergegeven resultaten.
2. Klik in **Traffic Manager-profiel**, in de sectie **Instellingen**, op **Eindpunten** en vervolgens op **Toevoegen**.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Azure-eindpunt                                   |
    | Name           | myTestWebSiteEndpoint                                        |
    | Doelbrontype           | Openbaar IP-adres                          |
    | Doelbron          | **Kies een openbaar IP-adres** om het overzicht van resources met openbare IP-adressen onder hetzelfde abonnement weer te geven. Selecteer in **Resource** het openbare IP-adres met de naam *myIISVMEastUS-ip*. Dit is het openbare IP-adres van de IIS-server VM in US - oost.|
    |  Instellingen voor subnetroutering    |   Voeg de IP-adres van *myVMEastUS* testen van de virtuele machine. Elke gebruikersquery die afkomstig zijn van deze virtuele machine worden omgeleid naar de *myTestWebSiteEndpoint*.    |

4. Herhaal stappen 2 en 3 om toe te voegen een ander eindpunt met de naam *myProductionEndpoint* voor het openbare IP-adres *myIISVMWEurope-IP-* dat is gekoppeld aan de virtuele machine met de naam van de IIS-server *myIISVMWEurope* . Voor **routering subnetinstellingen**, voeg de IP-adres van de test-VM - *myVMWestEurope*. Elke gebruikersquery van deze virtuele machine worden doorgestuurd naar het eindpunt - test *myProductionWebsiteEndpoint*.
5. Als beide eindpunten zijn toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**, samen met de controlestatus **Online**.

    ![Traffic Manager-eindpunt toevoegen](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen
In dit gedeeltetest u hoe de Traffic Manager gebruikersverkeer vanaf een bepaald subnet naar een specifiek eindpunt routeert. Voer de volgende stappen uit om de Traffic Manager in actie te zien:
1. Bepaal de DNS-naam van uw Traffic Manager-profiel.
2. Zie Traffic Manager als volgt in werking:
    - Van de virtuele testmachine (*myVMEastUS*) dat zich bevindt in de **VS-Oost** regio, in een webbrowser, blader naar de DNS-naam van uw Traffic Manager-profiel.
    - Van de virtuele testmachine (*myVMEastUS*) dat zich bevindt in de **West-Europa** regio, in een webbrowser, blader naar de DNS-naam van uw Traffic Manager-profiel.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>De DNS-naam van het Traffic Manager-profiel vaststellen
In deze zelfstudie maakt u voor het gemak gebruik van de DNS-naam van het Traffic Manager-profiel om de websites te bezoeken.

U kunt de DNS-naam van het Traffic Manager-profiel als volgt vaststellen:

1. Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in de vorige sectie hebt gemaakt. Klik op het Traffic Manager-profiel in de resultaten die worden weergegeven.
1. Klik op **Overzicht**.
2. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. In productie-implementaties configureert u een aangepaste domeinnaam om met behulp van een DNS CNAME-record naar de Traffic Manager-domeinnaam te verwijzen.

   ![DNS-naam van Traffic Manager](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien
In dit gedeelte kunt u Traffic Manager in werking zien.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
1. Typ in een webbrowser op de VM *myVMEastUS* de DNS-naam van uw Traffic Manager-profiel om uw website weer te geven. Nadat de virtuele machine *myVMEastUS* IP-adres is gekoppeld aan het eindpunt *myIISVMEastUS*, de webbrowser wordt gestart voor de website testserver - *myIISVMEastUS*.

   ![Traffic Manager-profiel testen](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Vervolgens maakt u verbinding met de virtuele machine *myVMWestEurope* zich in **West-Europa** met behulp van de stappen 1-5 en blader naar de domeinnaam van het Traffic Manager-profiel van deze virtuele machine. Nadat de virtuele machine *myVMWestEurope* IP-adres is gekoppeld aan het eindpunt *myIISVMEastUS*, de webbrowser wordt gestart voor de website testserver - *myIISVMWEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Het Traffic Manager-profiel verwijderen
Verwijder de resourcegroepen (**ResourceGroupTM1** en **ResourceGroupTM2**) als deze niet meer nodig zijn. Selecteer daarvoor de resourcegroep (**ResourceGroupTM1** of **ResourceGroupTM2**) en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gewogen verkeersrouteringsmethode](traffic-manager-configure-weighted-routing-method.md).
- Meer informatie over [de routeringsmethode met prioriteit](traffic-manager-configure-priority-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
