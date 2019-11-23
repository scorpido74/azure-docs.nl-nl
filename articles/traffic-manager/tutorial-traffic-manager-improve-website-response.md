---
title: Tutorial - Improve website response with Azure Traffic Manager
description: This tutorial article describes how to create a Traffic Manager profile to build a highly responsive website.
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: allensu
ms.openlocfilehash: 3686e9a7d82f8134b44cd40468c5e430eb2e72f3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422854"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Tutorial: Improve website response using Traffic Manager

This tutorial describes how to use Traffic Manager to create a highly responsive website by directing user traffic to the website with the lowest latency. Typically, the datacenter with the lowest latency is the one that is closest in geographic distance.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele machines maken met daarop een eenvoudige website in IIS
> * Twee virtuele machines voor tests maken om Traffic Manager in actie te zien
> * DNS-naam configureren voor de VM’s met behulp van IIS
> * Create a Traffic Manager profile for improved website performance
> * VM-eindpunten toevoegen aan het Traffic Manager-profiel
> * Traffic Manager in werking zien

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet voor deze zelfstudie de volgende zaken implementeren om de Traffic Manager in actie te zien:

- Two instances of basic websites running in different Azure regions - **East US** and **West Europe**.
- Two test VMs for testing the Traffic Manager - one VM in **East US** and the second VM in **West Europe**. The test VMs are used to illustrate how Traffic Manager routes user traffic to the website that is running in the same region as it provides the lowest latency.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

### <a name="create-websites"></a>Websites maken

In dit gedeelte maakt u twee website-instanties die de twee service-eindpunten voor het Traffic Manager-profiel vormen in twee Azure-regio’s. Om de twee websites te maken, moeten de volgende stappen worden uitgevoerd:

1. Maak twee virtuele machines om een eenvoudige website uit te voeren: een in **US - oost** en de andere in **Europa - west**.
2. Installeer IIS-server op elke VM en werk de standaardpagina van de website bij die de naam beschrijft van de virtuele machine waarmee een gebruiker is verbonden als deze de website bezoekt.

#### <a name="create-vms-for-running-websites"></a>Virtuele machines maken voor het uitvoeren van websites

In this section, you create two VMs *myIISVMEastUS* and *myIISVMWestEurope* in the **East US** and **West Europe** Azure regions.

1. On the upper, left corner of the Azure portal, select **Create a resource** > **Compute** > **Windows Server 2019 Datacenter**.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Subscription** > **Resource Group**: Select **Create new** and then type **myResourceGroupTM1**.
   - **Instance Details** > **Virtual machine name**: Type *myIISVMEastUS*.
   - **Instance Details** > **Region**:  Select **East US**.
   - **Administrator Account** > **Username**:  Enter a user name of your choosing.
   - **Administrator Account** > **Password**:  Enter a password of your choosing. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inbound Port Rules** > **Public inbound ports**: Select **Allow selected ports**.
   - **Inbound Port Rules** > **Select inbound ports**: Select **RDP** and **HTTP** in the pull down box.

3. Select the **Management** tab, or select **Next: Disks**, then **Next: Networking**, then **Next: Management**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Review the settings, and then click **Create**.  
6. Follow the steps to create a second VM named *myIISVMWestEurope*, with a **Resource group** name of *myResourceGroupTM2*, a **location** of *West Europe*, and all the other settings the same as *myIISVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

   ![Een VM maken](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS installeren en de standaardwebpagina aanpassen

In this section, you install the IIS server on the two VMs *myIISVMEastUS* and *myIISVMWestEurope*, and then update the default website page. De aangepaste websitepagina geeft de naam weer van de virtuele machine waarmee u verbinding maakt als u de website in een webbrowser bezoekt.

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
9. Repeat steps 1-8 with by creating an RDP connection with the VM *myIISVMWestEurope* within the *myResourceGroupTM2* resource group to install IIS and customize its default web page.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>DNS-namen voor virtuele machines configureren met behulp van IIS

Traffic Manager routeert gebruikersverkeer op basis van de DNS-naam van de service-eindpunten. In this section, you configure the DNS names for the IIS servers - *myIISVMEastUS* and *myIISVMWestEurope*.

1. Klik in het linkermenu op **Alle resources** en selecteer *myIISVMEastUS* in de lijst met resources, die zich in de resourcegroep *myresourceGroupTM1* bevindt.
2. Selecteer op de pagina **Overzicht** onder **DNS-naam** de optie **Configureren**.
3. Voeg op de pagina **Configuratie** onder het label DNS-naam een unieke naam toe en selecteer vervolgens **Opslaan**.
4. Repeat steps 1-3, for the VM named *myIISVMWestEurope* that is located in the *myResourceGroupTM2* resource group.

### <a name="create-test-vms"></a>Test-VM’s maken

In this section, you create a VM (*myVMEastUS* and *myVMWestEurope*) in each Azure region (**East US** and **West Europe**). U zult deze virtuele machines gebruiken om te testen hoe Traffic Manager verkeer naar de dichtstbijzijnde IIS-server routeert als u de website bezoekt.

1. On the upper, left corner of the Azure portal, select **Create a resource** > **Compute** > **Windows Server 2019 Datacenter**.
2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

   - **Subscription** > **Resource Group**: Select **myResourceGroupTM1**.
   - **Instance Details** > **Virtual machine name**: Type *myVMEastUS*.
   - **Instance Details** > **Region**:  Select **East US**.
   - **Administrator Account** > **Username**:  Enter a user name of your choosing.
   - **Administrator Account** > **Password**:  Enter a password of your choosing. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inbound Port Rules** > **Public inbound ports**: Select **Allow selected ports**.
   - **Inbound Port Rules** > **Select inbound ports**: Select **RDP** in the pull down box.

3. Select the **Management** tab, or select **Next: Disks**, then **Next: Networking**, then **Next: Management**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
4. Selecteer **Controleren + maken**.
5. Review the settings, and then click **Create**.  
6. Follow the steps to create a second VM named *myVMWestEurope*, with a **Resource group** name of *myResourceGroupTM2*, a **location** of *West Europe*, and all the other settings the same as *myVMEastUS*.
7. Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Create a Traffic Manager profile that directs user traffic by sending them to the endpoint with lowest latency.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Traffic Manager-profiel** > **Maken**.
2. Voer in  **Traffic Manager-profiel maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Naam                   | Deze naam moet uniek zijn binnen de zone trafficmanager.net en resulteert in de DNS-naam, trafficmanager.net, die wordt gebruikt voor het openen van uw Traffic Manager-profiel.                                   |
    | Routeringsmethode          | Select the **Performance** routing method.                                       |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Select the Resource group *myResourceGroupTM1*. |
    | Locatie                | Selecteer **US - oost**. Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het Traffic Manager-profiel dat wereldwijd wordt geïmplementeerd.                              |
    |

    ![Een Traffic Manager-profiel maken](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Add the two VMs running the IIS servers - *myIISVMEastUS* & *myIISVMWestEurope* to route user traffic to the closest endpoint to the user.

1. Zoek in de zoekbalk van de portal de naam van het Traffic Manager-profiel dat u in de vorige sectie hebt gemaakt en selecteer het profiel in de weergegeven resultaten.
2. Klik in **Traffic Manager-profiel**, in de sectie **Instellingen**, op **Eindpunten** en vervolgens op **Toevoegen**.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Type                    | Azure-eindpunt                                   |
    | Naam           | myEastUSEndpoint                                        |
    | Doelbrontype           | Openbaar IP-adres                          |
    | Doelbron          | **Kies een openbaar IP-adres** om het overzicht van resources met openbare IP-adressen onder hetzelfde abonnement weer te geven. Selecteer in **Resource** het openbare IP-adres met de naam *myIISVMEastUS-ip*. Dit is het openbare IP-adres van de IIS-server VM in US - oost.|
    |        |           |

4. Repeat steps 2 and 3 to add another endpoint named *myWestEuropeEndpoint* for the public IP address *myIISVMWestEurope-ip* that is associated with the IIS server VM named *myIISVMWestEurope*.
5. Als beide eindpunten zijn toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**, samen met de controlestatus **Online**.

    ![Traffic Manager-eindpunt toevoegen](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In this section, you test how the Traffic Manager routes user traffic to the nearest VMs running the website to provide minimum latency. Voer de volgende stappen uit om de Traffic Manager in actie te zien:

1. Bepaal de DNS-naam van uw Traffic Manager-profiel.
2. Zie Traffic Manager als volgt in werking:
    - From the test VM (*myVMEastUS*) that is located in the **East US** region, in a web browser, browse to the DNS name of your Traffic Manager profile.
    - From the test VM (*myVMWestEurope*) that is located in the **West Europe** region, in a web browser, browse to the DNS name of your Traffic Manager profile.

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
1. Typ in een webbrowser op de VM *myVMEastUS* de DNS-naam van uw Traffic Manager-profiel om uw website weer te geven. Since the VM located in **East US**, you are routed to the nearest website hosted on the nearest IIS server *myIISVMEastUS* that is located in **East US**.

   ![Traffic Manager-profiel testen](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Next, connect to the VM *myVMWestEurope* located in **West Europe** using steps 1-5 and browse to the Traffic Manager profile domain name from this VM. Since the VM located in **West Europe**, you are now routed to the website hosted on nearest the IIS server *myIISVMWestEurope* that is located in **West Europe**.

   ![Traffic Manager-profiel testen](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Het Traffic Manager-profiel verwijderen

Verwijder de resourcegroepen (**ResourceGroupTM1** en **ResourceGroupTM2**) als deze niet meer nodig zijn. Selecteer daarvoor de resourcegroep (**ResourceGroupTM1** of **ResourceGroupTM2**) en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verkeer naar een set eindpunten distribueren](traffic-manager-configure-weighted-routing-method.md)
