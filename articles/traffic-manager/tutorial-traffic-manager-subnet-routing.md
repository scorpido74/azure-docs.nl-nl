---
title: Routerings verkeer van het subnet configureren-Azure Traffic Manager
description: In dit artikel wordt uitgelegd hoe u Traffic Manager kunt configureren om verkeer van subnetten van gebruikers naar specifieke eindpunten te routeren.
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: allensu
ms.openlocfilehash: 79a8712dbe54446214f3c094a1377efea4defcce
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014572"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Verkeer naar specifieke eindpunten routeren met Traffic Manager op basis van subnets van gebruiker

In dit artikel wordt beschreven hoe u de verkeersrouteringsmethode op basis van subnetten kunt configureren. Met de **Subnet**-verkeersrouteringsmethode kunt u een reeks IP-adresbereiken aan specifieke eindpunten koppelen als een aanvraag door Traffic Manager wordt ontvangen; de methode inspecteert het bron-IP-adres van de aanvraag en retourneert het eindpunt dat daar aan is gekoppeld.

In deze zelfstudie wordt verkeer met behulp van subnetroutering, afhankelijk van het IP-adres van de query van de gebruiker, naar een interne website of productiewebsite gerouteerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele machines maken met daarop een eenvoudige website in IIS
> * Twee virtuele machines voor tests maken om Traffic Manager in actie te zien
> * DNS-naam configureren voor de VM’s met behulp van IIS
> * Een Traffic Manager-profiel maken om verkeer te routeren op basis van het subnet van een gebruiker
> * VM-eindpunten toevoegen aan het Traffic Manager-profiel
> * Traffic Manager in werking zien

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

In deze sectie maakt u twee Vm's *myIISVMEastUS* en *MYIISVMWESTEUROPE* in het **VS-Oost** en **Europa-West** Azure-regio's.

1. Selecteer in de linkerbovenhoek van de Azure Portal **een resource maken** > **Compute** > **Windows Server 2019 Data Center**.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Abonnement** > **resource groep**: Selecteer **nieuwe maken** en typ vervolgens **myResourceGroupTM1**.
   - **Exemplaar Details** > **naam van de virtuele machine**: Typ *myIISVMEastUS*.
   - **Exemplaar Details** > **regio**: Selecteer **VS-Oost**.
   - **Administrator-Account** > **gebruikers**naam: Voer een gebruiker in voor de keuze.
   - **Beheerders Account** > **wacht woord**: Voer een wacht woord van uw keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Binnenkomende poort regels** > **open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Binnenkomende poort regels** > **binnenkomende poorten selecteren**: Selecteer **RDP** en **http** in de vervolg keuzelijst.

3. Selecteer het tabblad **beheer** of selecteer **volgende: schijven**, vervolgens **volgende: netwerken**en vervolgens op **volgende: beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Controleer de instellingen en klik vervolgens op **maken**.  
6. Volg de stappen voor het maken van een tweede VM met de naam *myIISVMWestEurope*, met de naam van de **resource groep** *myResourceGroupTM2*, een **locatie** van *Europa-West*en alle andere instellingen hetzelfde als *myIISVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS installeren en de standaardwebpagina aanpassen

In deze sectie installeert u de IIS-server op de twee Vm's *myIISVMEastUS* & *myIISVMWestEurope*en werkt u vervolgens de pagina standaard website bij. De aangepaste websitepagina geeft de naam weer van de virtuele machine waarmee u verbinding maakt als u de website in een webbrowser bezoekt.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myIISVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
6. Ga op de serverdesktop naar **Windows Systeembeheer**>**Serverbeheer**.
7. Start Windows Power shell op de VM- *myIISVMEastUS*en gebruik de volgende opdrachten om de IIS-server te installeren en het standaard htm-bestand bij te werken.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Sluit de RDP-verbinding met de *myIISVMEastUS* -VM.
9. Herhaal stap 1-6 met door het maken van een RDP-verbinding met de VM- *myIISVMWestEurope* in de resource groep *myResourceGroupTM2* om IIS te installeren en de standaard webpagina aan te passen.
10. Start Windows Power shell op *myIISVMWestEurope* VM en gebruik de volgende opdrachten om de IIS-server te installeren en het standaard htm-bestand bij te werken.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>DNS-namen voor virtuele machines configureren met behulp van IIS

Traffic Manager routeert gebruikersverkeer op basis van de DNS-naam van de service-eindpunten. In deze sectie configureert u de DNS-namen voor de IIS-servers- *myIISVMEastUS* en *myIISVMWestEurope*.

1. Klik in het linkermenu op **Alle resources** en selecteer *myIISVMEastUS* in de lijst met resources, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Selecteer op de pagina **Overzicht** onder **DNS-naam** de optie **Configureren**.
3. Voeg op de pagina **Configuratie** onder het label DNS-naam een unieke naam toe en selecteer vervolgens **Opslaan**.
4. Herhaal stap 1-3 voor de virtuele machine met de naam *myIISVMWestEurope* die zich in de resource groep *myResourceGroupTM2* bevindt.

### <a name="create-test-vms"></a>Test-VM’s maken

In deze sectie maakt u een virtuele machine (*myVMEastUS* en *myVMWestEurope*) in elke Azure-regio (**VS-Oost** en **Europa-West**). U gebruikt deze Vm's om te testen hoe Traffic Manager gebruikers verkeer routeert op basis van het subnet van de query van de gebruiker.

1. Selecteer in de linkerbovenhoek van de Azure Portal **een resource maken** > **Compute** > **Windows Server 2019 Data Center**.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   -  > **resource groep** **abonnement** : Selecteer **myResourceGroupTM1**.
   - **Exemplaar Details** > **naam van de virtuele machine**: Typ *myVMEastUS*.
   - **Exemplaar Details** > **regio**: Selecteer **VS-Oost**.
   - **Administrator-Account** > **gebruikers**naam: Voer een gebruiker in voor de keuze.
   - **Beheerders Account** > **wacht woord**: Voer een wacht woord van uw keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Binnenkomende poort regels** > **open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Binnenkomende poort regels** > **binnenkomende poorten selecteren**: Selecteer **RDP** in de vervolg keuzelijst.

3. Selecteer het tabblad **beheer** of selecteer **volgende: schijven**, vervolgens **volgende: netwerken**en vervolgens op **volgende: beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Controleer de instellingen en klik vervolgens op **maken**.  
6. Volg de stappen voor het maken van een tweede VM met de naam *myVMWestEurope*, met de naam van de **resource groep** *myResourceGroupTM2*, een **locatie** van *Europa-West*en alle andere instellingen hetzelfde als *myVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel waarmee u specifieke eindpunten kunt retourneren op basis van het bron-IP-adres van de aanvraag.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Traffic Manager-profiel** > **Maken**.
2. Voer in  **Traffic Manager-profiel maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Naam                   | Deze naam moet uniek zijn binnen de zone trafficmanager.net en resulteert in de DNS-naam, trafficmanager.net, die wordt gebruikt voor het openen van uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Selecteer de routeringsmethode **Subnet**.                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer **Bestaande** en voer *myResourceGroupTM1* in. |
    | |                              |
    |

    ![Een Traffic Manager-profiel maken](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg de twee virtuele machines met de IIS-servers- *myIISVMEastUS* & *myIISVMWestEurope* toe om gebruikers verkeer te routeren op basis van het subnet van de query van de gebruiker.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt en selecteer het profiel in de weergegeven resultaten.
2. Klik in **Traffic Manager-profiel**, in de sectie **Instellingen**, op **Eindpunten** en vervolgens op **Toevoegen**.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Azure-eindpunt                                   |
    | Naam           | myInternalWebSiteEndpoint                                        |
    | Doelbrontype           | Openbaar IP-adres                          |
    | Doelbron          | **Kies een openbaar IP-adres** om het overzicht van resources met openbare IP-adressen onder hetzelfde abonnement weer te geven. Selecteer in **Resource** het openbare IP-adres met de naam *myIISVMEastUS-ip*. Dit is het openbare IP-adres van de IIS-server VM in US - oost.|
    |  Instellingen voor subnetroutering    |   Voeg het IP-adres van de *myVMEastUS* -test-VM toe. Alle gebruikers query's die afkomstig zijn van deze VM, worden omgeleid naar de *myInternalWebSiteEndpoint*.    |

4. Herhaal stap 2 en 3 om een ander eind punt met de naam *myProdWebsiteEndpoint* toe te voegen voor het open bare IP-adres *myIISVMWestEurope-IP* dat is gekoppeld aan de IIS-Server-VM met de naam *myIISVMWestEurope*. Voeg het IP-adres van de test-VM- *myVMWestEurope*toe voor de instellingen van het **subnet routeren**. Elke gebruikersquery van deze test-VM zal naar het eindpunt *myProdWebsiteEndpoint* worden gerouteerd.
5. Als beide eindpunten zijn toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**, samen met de controlestatus **Online**.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In dit gedeeltetest u hoe de Traffic Manager gebruikersverkeer vanaf een bepaald subnet naar een specifiek eindpunt routeert. Voer de volgende stappen uit om de Traffic Manager in actie te zien:

1. Bepaal de DNS-naam van uw Traffic Manager-profiel.
2. Zie Traffic Manager als volgt in werking:
    - Blader vanuit de test-VM (*myVMEastUS*) die zich in de regio **VS-Oost** bevindt in een webbrowser naar de DNS-naam van uw Traffic Manager-profiel.
    - Blader vanuit de test-VM (*myVMWestEurope*) die zich in de **Europa-West** regio bevindt in een webbrowser naar de DNS-naam van uw Traffic Manager-profiel.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>De DNS-naam van het Traffic Manager-profiel vaststellen

In deze zelfstudie maakt u voor het gemak gebruik van de DNS-naam van het Traffic Manager-profiel om de websites te bezoeken.

U kunt de DNS-naam van het Traffic Manager-profiel als volgt vaststellen:

1. Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in de vorige sectie hebt gemaakt. Klik op het Traffic Manager-profiel in de resultaten die worden weergegeven.
2. Klik op **Overzicht**.
3. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. In productie-implementaties configureert u een aangepaste domeinnaam om met behulp van een DNS CNAME-record naar de Traffic Manager-domeinnaam te verwijzen.

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien

In dit gedeelte kunt u Traffic Manager in werking zien.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
6. Typ in een webbrowser op de VM *myVMEastUS* de DNS-naam van uw Traffic Manager-profiel om uw website weer te geven. Omdat het IP-adres van de VM- *myVMEastUS* is gekoppeld aan het eind punt *myInternalWebsiteEndpoint*, start de webbrowser de test website server- *myIISVMEastUS*.

7. Maak vervolgens verbinding met het VM- *myVMWestEurope* dat zich bevindt in **Europa-West** met behulp van stap 1-5 en blader naar de domein naam van het Traffic Manager profiel van deze VM. Omdat het IP-adres van de VM- *myVMWestEurope* is gekoppeld aan het eind punt *myProductionWebsiteEndpoint*, start de webbrowser de test website server- *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Het Traffic Manager-profiel verwijderen

Verwijder de resourcegroepen (**ResourceGroupTM1** en **ResourceGroupTM2**) als deze niet meer nodig zijn. Selecteer daarvoor de resourcegroep (**ResourceGroupTM1** of **ResourceGroupTM2**) en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gewogen verkeersrouteringsmethode](traffic-manager-configure-weighted-routing-method.md).
- Meer informatie over [de routeringsmethode met prioriteit](traffic-manager-configure-priority-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
