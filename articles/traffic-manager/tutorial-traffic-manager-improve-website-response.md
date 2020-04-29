---
title: Zelf studie-reacties op websites verbeteren met Azure Traffic Manager
description: In deze zelf studie wordt beschreven hoe u een Traffic Manager profiel maakt om een zeer responsieve website te bouwen.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77136389"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Zelf studie: het antwoord van websites verbeteren met behulp van Traffic Manager

In deze zelf studie wordt beschreven hoe u Traffic Manager kunt gebruiken om een zeer responsieve website te maken door gebruikers verkeer naar de website met de laagste latentie te sturen. Normaal gesp roken is het Data Center met de laagste latentie hetzelfde als het dichtstbijzijnde geografische afstand.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele machines maken met daarop een eenvoudige website in IIS
> * Twee virtuele machines voor tests maken om Traffic Manager in actie te zien
> * DNS-naam configureren voor de VM’s met behulp van IIS
> * Een Traffic Manager profiel maken voor verbeterde website prestaties
> * VM-eindpunten toevoegen aan het Traffic Manager-profiel
> * Traffic Manager in werking zien

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet voor deze zelfstudie de volgende zaken implementeren om de Traffic Manager in actie te zien:

- Twee exemplaren van basis websites die worden uitgevoerd in verschillende Azure-regio's, **VS-Oost** en **Europa-West**.
- Twee virtuele machines testen voor het testen van de Traffic Manager-één VM in **VS-Oost** en de tweede vm in **Europa-West**. De test-Vm's worden gebruikt om te illustreren hoe Traffic Manager verkeer van gebruikers naar de website die wordt uitgevoerd in dezelfde regio als de laagste latentie biedt.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Websites maken

In dit gedeelte maakt u twee website-instanties die de twee service-eindpunten voor het Traffic Manager-profiel vormen in twee Azure-regio’s. Om de twee websites te maken, moeten de volgende stappen worden uitgevoerd:

1. Maak twee virtuele machines om een eenvoudige website uit te voeren: een in **VS - oost** en de andere in **Europa - west**.
2. Installeer IIS-server op elke VM en werk de standaardpagina van de website bij die de naam beschrijft van de virtuele machine waarmee een gebruiker is verbonden als deze de website bezoekt.

#### <a name="create-vms-for-running-websites"></a>Virtuele machines maken voor het uitvoeren van websites

In deze sectie maakt u twee Vm's *myIISVMEastUS* en *MYIISVMWESTEUROPE* in het **VS-Oost** en **Europa-West** Azure-regio's.

1. Selecteer in de linkerbovenhoek van de Azure Portal **een resource** > **maken Compute** > **Windows Server 2019 Data Center**.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Abonnements** > **resource groep**: Selecteer **nieuwe maken** en typ vervolgens **myResourceGroupTM1**.
   - **Details** > van de naam van de**virtuele machine**: Typ *myIISVMEastUS*.
   - **Exemplaar Details** > **regio**: Selecteer **VS-Oost**.
   - **Administrator Account** > **Gebruikers**naam van Administrator-account: Voer een door u gekozen gebruikernaam in.
   - **Administrator Account** > **Wacht woord**voor beheerders account: Voer een wacht woord van uw keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Binnenkomende poort regels** > **open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Binnenkomende poort regels** > **Selecteer binnenkomende poorten**: Selecteer **RDP** en **http** in de vervolg keuzelijst.

3. Selecteer het tabblad **beheer** of selecteer **volgende: schijven**, vervolgens **volgende: netwerken**en vervolgens op **volgende: beheer**. Stel bij **Bewaking****Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **controleren + maken**.
5. Controleer de instellingen en klik vervolgens op **maken**.  
6. Volg de stappen voor het maken van een tweede VM met de naam *myIISVMWestEurope*, met de naam van de **resource groep** *myResourceGroupTM2*, een **locatie** van *Europa-West*en alle andere instellingen hetzelfde als *myIISVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

   ![Een virtuele machine maken](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS installeren en de standaardwebpagina aanpassen

In deze sectie installeert u de IIS-server op de twee Vm's *myIISVMEastUS* en *myIISVMWestEurope*, en werkt u vervolgens de pagina standaard website bij. De aangepaste websitepagina geeft de naam weer van de virtuele machine waarmee u verbinding maakt als u de website in een webbrowser bezoekt.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myIISVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
6. Navigeer op het bureau blad van de server naar **beheer hulpprogramma's**>van Windows**Serverbeheer**.
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
9. Herhaal stap 1-8 met door het maken van een RDP-verbinding met de VM- *myIISVMWestEurope* in de resource groep *myResourceGroupTM2* om IIS te installeren en de standaard webpagina aan te passen.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>DNS-namen voor virtuele machines configureren met behulp van IIS

Traffic Manager routeert gebruikersverkeer op basis van de DNS-naam van de service-eindpunten. In deze sectie configureert u de DNS-namen voor de IIS-servers- *myIISVMEastUS* en *myIISVMWestEurope*.

1. Klik in het linkermenu op **Alle resources** en selecteer *myIISVMEastUS* in de lijst met resources, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Selecteer op de pagina **Overzicht** onder **DNS-naam** de optie **Configureren**.
3. Voeg op de pagina **Configuratie** onder het label DNS-naam een unieke naam toe en selecteer vervolgens **Opslaan**.
4. Herhaal stap 1-3 voor de virtuele machine met de naam *myIISVMWestEurope* die zich in de resource groep *myResourceGroupTM2* bevindt.

### <a name="create-test-vms"></a>Test-VM’s maken

In deze sectie maakt u een virtuele machine (*myVMEastUS* en *myVMWestEurope*) in elke Azure-regio (**VS-Oost** en **Europa-West**). U zult deze virtuele machines gebruiken om te testen hoe Traffic Manager verkeer naar de dichtstbijzijnde IIS-server routeert als u de website bezoekt.

1. Selecteer in de linkerbovenhoek van de Azure Portal **een resource** > **maken Compute** > **Windows Server 2019 Data Center**.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Abonnements** > **resource groep**: Selecteer **myResourceGroupTM1**.
   - **Details** > van de naam van de**virtuele machine**: Typ *myVMEastUS*.
   - **Exemplaar Details** > **regio**: Selecteer **VS-Oost**.
   - **Administrator Account** > **Gebruikers**naam van Administrator-account: Voer een door u gekozen gebruikernaam in.
   - **Administrator Account** > **Wacht woord**voor beheerders account: Voer een wacht woord van uw keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Binnenkomende poort regels** > **open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Binnenkomende poort regels** > **Selecteer binnenkomende poorten**: Selecteer **RDP** in de vervolg keuzelijst.

3. Selecteer het tabblad **beheer** of selecteer **volgende: schijven**, vervolgens **volgende: netwerken**en vervolgens op **volgende: beheer**. Stel bij **Bewaking****Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **controleren + maken**.
5. Controleer de instellingen en klik vervolgens op **maken**.  
6. Volg de stappen voor het maken van een tweede VM met de naam *myVMWestEurope*, met de naam van de **resource groep** *myResourceGroupTM2*, een **locatie** van *Europa-West*en alle andere instellingen hetzelfde als *myVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager profiel dat gebruikers verkeer verstuurt door het naar het eind punt te verzenden met de laagste latentie.

1. Selecteer in de linkerbovenhoek van het scherm **een resource** > **netwerk** > **Traffic Manager profiel** > **maken**.
2. Voer in ** Traffic Manager-profiel maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Naam                   | Deze naam moet uniek zijn binnen de zone trafficmanager.net en resulteert in de DNS-naam, trafficmanager.net, die wordt gebruikt voor het openen van uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Selecteer de methode voor de **prestaties** van de route ring.                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer de resource groep *myResourceGroupTM1*. |
    | Locatie                | Selecteer **VS - oost**. Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het Traffic Manager-profiel dat wereldwijd wordt geïmplementeerd.                              |
    |

    ![Een Traffic Manager-profiel maken](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg de twee virtuele machines met de IIS-servers- *myIISVMEastUS* & *myIISVMWestEurope* toe om gebruikers verkeer naar het dichtstbijzijnde eind punt te routeren naar de gebruiker.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt en selecteer het profiel in de weergegeven resultaten.
2. Klik in **Traffic Manager-profiel**, in de sectie **Instellingen**, op **Eindpunten** en vervolgens op **Toevoegen**.
3. Voer de volgende informatie in of Selecteer deze, accepteer de standaard waarden voor de overige instellingen en selecteer **OK**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Azure-eindpunt                                   |
    | Naam           | myEastUSEndpoint                                        |
    | Doelbrontype           | Openbaar IP-adres                          |
    | Doelbron          | **Kies een openbaar IP-adres** om het overzicht van resources met openbare IP-adressen onder hetzelfde abonnement weer te geven. Selecteer in **Resource** het openbare IP-adres met de naam *myIISVMEastUS-ip*. Dit is het openbare IP-adres van de IIS-server VM in VS - oost.|
    |        |           |

4. Herhaal stap 2 en 3 om een ander eind punt met de naam *myWestEuropeEndpoint* toe te voegen voor het open bare IP-adres *myIISVMWestEurope-IP* dat is gekoppeld aan de IIS-Server-VM met de naam *myIISVMWestEurope*.
5. Als beide eindpunten zijn toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**, samen met de controlestatus **Online**.

    ![Traffic Manager-eindpunt toevoegen](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In deze sectie test u hoe de Traffic Manager het verkeer van gebruikers naar de dichtstbijzijnde Vm's waarop de website wordt uitgevoerd om minimale latentie te bieden. Voer de volgende stappen uit om de Traffic Manager in actie te zien:

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

   ![DNS-naam van Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien

In dit gedeelte kunt u Traffic Manager in werking zien.

1. Selecteer in het linkermenu **Alle resources** en klik in de lijst met resources op *myVMEastUS*, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** en selecteer vervolgens in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.
1. Typ in een webbrowser op de VM *myVMEastUS* de DNS-naam van uw Traffic Manager-profiel om uw website weer te geven. Sinds de virtuele machine in **VS-Oost**, wordt u doorgestuurd naar de dichtstbijzijnde website die wordt gehost op de dichtstbijzijnde IIS server- *myIISVMEastUS* die zich in **VS-Oost**bevindt.

   ![Traffic Manager-profiel testen](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Maak vervolgens verbinding met het VM- *myVMWestEurope* dat zich bevindt in **Europa-West** met behulp van stap 1-5 en blader naar de domein naam van het Traffic Manager profiel van deze VM. Omdat de virtuele machine zich in **Europa-West**bevindt, wordt u nu doorgestuurd naar de website die als host fungeert voor de *MYIISVMWESTEUROPE* van de IIS-server die zich in **Europa-West**bevindt.

   ![Traffic Manager-profiel testen](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Het Traffic Manager-profiel verwijderen

Verwijder de resourcegroepen (**ResourceGroupTM1** en **ResourceGroupTM2**) als deze niet meer nodig zijn. Selecteer daarvoor de resourcegroep (**ResourceGroupTM1** of **ResourceGroupTM2**) en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verkeer naar een set eindpunten distribueren](traffic-manager-configure-weighted-routing-method.md)
