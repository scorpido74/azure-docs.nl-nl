---
title: Azure-functies integreren met een virtueel Azure-netwerk
description: Een stapsgewijze zelfstudie die u laat zien hoe u een functie verbinden met een virtueel Azure-netwerk
author: alexkarcher-msft
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0c70c69f547405eb8ebdcf6dcc6ae597db151e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433216"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Zelfstudie: Azure Functions integreren met een virtueel Azure-netwerk

In deze zelfstudie ziet u hoe u Azure-functies gebruiken om verbinding te maken met bronnen in een virtueel Azure-netwerk. u maakt een functie die toegang heeft tot zowel het internet als tot een VM met WordPress in virtueel netwerk.

> [!div class="checklist"]
> * Een functie-app maken in het Premium-abonnement
> * Een WordPress-site implementeren op VM in een virtueel netwerk
> * De functie-app verbinden met het virtuele netwerk
> * Een functieproxy maken om toegang te krijgen tot WordPress-bronnen
> * Een WordPress-bestand aanvragen vanuit het virtuele netwerk

## <a name="topology"></a>Topologie

In het volgende diagram ziet u de architectuur van de oplossing die u maakt:

 ![Gebruikersinterface voor virtuele netwerkintegratie](./media/functions-create-vnet/topology.png)

Functies die in het Premium-abonnement worden uitgevoerd, hebben dezelfde hostingmogelijkheden als web-apps in Azure App Service, waaronder de VNet-integratiefunctie. Zie [Uw app integreren met een virtueel Azure-netwerk](../app-service/web-sites-integrate-with-vnet.md)voor meer informatie over VNet-integratie, inclusief probleemoplossing en geavanceerde configuratie.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie is het belangrijk dat u IP-adressering en subnetting begrijpt. U beginnen met [dit artikel dat de basisprincipes van het aanpakken en subnetting omvat.](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics) Veel meer artikelen en video's zijn online beschikbaar.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-a-function-app-in-a-premium-plan"></a>Een functie-app maken in een Premium-abonnement

Eerst maakt u een functie-app in het [Premium-abonnement.] Dit plan biedt serverloze schaal en ondersteunt virtuele netwerkintegratie.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

U de functie-app vastmaken aan het dashboard door het pinpictogram in de rechterbovenhoek te selecteren. Vastmaken maakt het eenvoudiger om terug te keren naar deze functie-app nadat u uw VM hebt gemaakt.

## <a name="create-a-vm-inside-a-virtual-network"></a>Een VM maken in een virtueel netwerk

Maak vervolgens een vooraf geconfigureerde VM die WordPress uitvoert in een virtueel netwerk[(WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). Een WordPress VM wordt gebruikt vanwege de lage kosten en gemak. Hetzelfde scenario werkt met elke bron in een virtueel netwerk, zoals REST API's, App Service-omgevingen en andere Azure-services. 

1. Kies in de portal **+ Een resource maken** in het `WordPress LEMP7 Max Performance`linkernavigatiedeelvenster in het zoekveldtype en druk op Enter.

1. Kies **Wordpress LEMP Max Performance** in de zoekresultaten. Selecteer een softwareplan van **Wordpress LEMP Max Performance voor CentOS** als **softwareplan** en selecteer **Maken**.

1. Gebruik op het tabblad **Basisbeginselen** de VM-instellingen zoals opgegeven in de tabel onder de afbeelding:

    ![Tabblad Basisbeginselen voor het maken van een vm](./media/functions-create-vnet/create-vm-1.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waaronder uw resources worden gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Kies `myResourceGroup`of de resourcegroep die u hebt gemaakt met uw functie-app. Met behulp van dezelfde resourcegroep voor de functie-app, WordPress VM en hostingplan, u gemakkelijker resources opschonen wanneer u klaar bent met deze zelfstudie. |
    | **Naam virtuele machine** | VNET-Wordpress | De VM-naam moet uniek zijn in de resourcegroep |
    | **[Regio](https://azure.microsoft.com/regions/)** | (Europa) West-Europa | Kies een gebied bij u in de buurt of in de buurt van de functies die toegang hebben tot de VM. |
    | **Grootte** | B1's | Kies **Grootte wijzigen** en selecteer vervolgens de standaardafbeelding B1's, die 1 vCPU en 1 GB geheugen heeft. |
    | **Verificatietype** | Wachtwoord | Als u wachtwoordverificatie wilt gebruiken, moet u ook een **gebruikersnaam,** een beveiligd **wachtwoord**opgeven en vervolgens **wachtwoord bevestigen.** Voor deze zelfstudie hoeft u zich niet aan te melden bij de virtuele machine, tenzij u problemen moet oplossen. |

1. Kies het tabblad **Netwerken** en selecteer onder Virtuele netwerken configureren de optie **Nieuw maken**.

1. Gebruik **in Virtueel netwerk maken**de instellingen in de tabel onder de afbeelding:

    ![Tabblad Netwerken van VM maken](./media/functions-create-vnet/create-vm-2.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Naam** | myResourceGroup-vnet | U de standaardnaam gebruiken die is gegenereerd voor uw virtuele netwerk. |
    | **Adresbereik** | 10.10.0.0/16 | Gebruik één adresbereik voor het virtuele netwerk. |
    | **Subnetnaam** | Zelfstudie-Net | Naam van het subnet. |
    | **Adresbereik** (subnet) | 10.10.1.0/24   | De subnetgrootte bepaalt hoeveel interfaces aan het subnet kunnen worden toegevoegd. Dit subnet wordt gebruikt door de WordPress site.  Een `/24` subnet bevat 254 hostadressen. |

1. Selecteer **OK** om het virtuele netwerk te maken.

1. Kies **Geen** voor **openbaar IP**op het tabblad **Netwerken.**

1. Kies het tabblad **Beheer** en kies vervolgens in **het opslagaccount Diagnostische gegevens**het opslagaccount dat u met uw functie-app hebt gemaakt.

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **Maken**. Het vm-maakproces duurt enkele minuten. De gemaakte VM heeft alleen toegang tot het virtuele netwerk.

1. Nadat de vm is gemaakt, kiest **u Ga naar resource om** de pagina voor uw nieuwe virtuele machine weer te geven en kiest u **Netwerken** onder **Instellingen**.

1. Controleer of er geen **openbaar IP is.** Noteer het **privé-IP**, waarmee u verbinding maakt met de VM vanuit uw functie-app.

    ![Netwerkinstellingen in de VM](./media/functions-create-vnet/vm-networking.png)

U hebt nu een WordPress-site die volledig binnen uw virtuele netwerk wordt geïmplementeerd. Deze site is niet toegankelijk vanaf het openbare internet.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Uw functie-app verbinden met het virtuele netwerk

Met een WordPress-site die in een VM in een virtueel netwerk wordt uitgevoerd, u uw functie-app nu verbinden met dat virtuele netwerk.

1. Selecteer in de nieuwe functie-app**Netwerkfuncties** **platformfuncties.** > 

    ![Netwerken kiezen in de functie-app](./media/functions-create-vnet/networking-0.png)

1. Selecteer onder **VNet-integratie** **Klik hier om te configureren**.

    ![Status voor het configureren van een netwerkfunctie](./media/functions-create-vnet/Networking-1.png)

1. Selecteer **VNet toevoegen (voorbeeld)** op de pagina voor virtuele netwerkintegratie .

    ![Het voorbeeld vnet-integratie toevoegen](./media/functions-create-vnet/networking-2.png)

1. Gebruik in **de netwerkfunctiestatus**de instellingen in de tabel onder de afbeelding:

    ![Het virtuele netwerk van de functie-app definiëren](./media/functions-create-vnet/networking-3.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Virtueel netwerk** | MyResourceGroup-vnet | Dit virtuele netwerk is degene die u eerder hebt gemaakt. |
    | **Subnet** | Nieuw subnet maken | Maak een subnet in het virtuele netwerk dat uw functie-app kan gebruiken. VNet-integratie moet zijn geconfigureerd om een leeg subnet te gebruiken. Het maakt niet uit dat uw functies een ander subnet gebruiken dan uw VM. Het virtuele netwerk leidt automatisch het verkeer tussen de twee subnetten. |
    | **Subnetnaam** | Functie-Net | Naam van het nieuwe subnet. |
    | **Blok voor virtueel netwerkadres** | 10.10.0.0/16 | Kies hetzelfde adresblok dat wordt gebruikt door de WordPress-site. U moet slechts één adresblok hebben gedefinieerd. |
    | **Adresbereik** | 10.10.2.0/24   | De subnetgrootte beperkt het totale aantal exemplaren waarop de functie-app Premium-abonnement kan worden uitgeschaald. In dit `/24` voorbeeld wordt een subnet gebruikt met 254 beschikbare hostadressen. Dit subnet is over-ingericht, maar gemakkelijk te berekenen. |

1. Selecteer **OK** om het subnet toe te voegen. Sluit de pagina's VNet-integratie en netwerkfunctiestatus om terug te keren naar de pagina met de functie-app.

De functie-app heeft nu toegang tot het virtuele netwerk waar de WordPress-site wordt uitgevoerd. Vervolgens gebruikt u [Azure Functions Proxies](functions-proxies.md) om een bestand van de WordPress-site te retourneren.

## <a name="create-a-proxy-to-access-vm-resources"></a>Een proxy maken om toegang te krijgen tot VM-bronnen

Als VNet-integratie is ingeschakeld, u een proxy maken in uw functie-app om aanvragen door te sturen naar de vm die in het virtuele netwerk wordt uitgevoerd.

1. Selecteer in uw functie-app **Proxy's** > **+** en gebruik vervolgens de proxy-instellingen in de tabel onder de afbeelding:

    ![De proxy-instellingen definiëren](./media/functions-create-vnet/create-proxy.png)

    | Instelling  | Voorgestelde waarde  | Beschrijving      |
    | -------- | ---------------- | ---------------- |
    | **Naam** | Fabriek | De naam kan elke waarde zijn. Het wordt gebruikt om de proxy te identificeren. |
    | **Routesjabloon** | /plant | Route die wordt toegewezen aan een VM-bron. |
    | **URL van back-end** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Vervang `<YOUR_VM_IP>` door het IP-adres van uw WordPress VM dat u eerder hebt gemaakt. Met deze toewijzing wordt één bestand van de site geretourneerd. |

1. Selecteer **Maken** om de proxy toe te voegen aan uw functie-app.

## <a name="try-it-out"></a>Uitproberen

1. Probeer in uw browser toegang te krijgen tot de URL die u als **backend-URL hebt**gebruikt. Zoals verwacht, het verzoek een keer uit. Er treedt een time-out op omdat uw WordPress-site alleen is verbonden met uw virtuele netwerk en niet met internet.

1. Kopieer de **proxy-URL-waarde** van uw nieuwe proxy en plak deze in de adresbalk van uw browser. De geretourneerde afbeelding is van de WordPress-site die in uw virtuele netwerk wordt uitgevoerd.

    ![Installatieafbeeldingsbestand geretourneerd van de WordPress-site](./media/functions-create-vnet/plant.png)

Uw functie-app is verbonden met zowel het internet als uw virtuele netwerk. De proxy ontvangt een verzoek via het openbare internet en fungeert vervolgens als een eenvoudige HTTP-proxy om dat verzoek door te sturen naar het aangesloten virtuele netwerk. De proxy stuurt vervolgens de reactie terug naar u openbaar via het internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie dient de WordPress-site als een API die wordt aangeroepen met behulp van een proxy in de functie-app. Dit scenario maakt een goede tutorial, omdat het gemakkelijk is in te stellen en te visualiseren. U elke andere API gebruiken die binnen een virtueel netwerk wordt geïmplementeerd. U had ook een functie kunnen maken met code die API's aanroept die binnen het virtuele netwerk zijn geïmplementeerd. Een realistischer scenario is een functie die API's van gegevensclient gebruikt om een SQL Server-instantie aan te roepen die in het virtuele netwerk wordt geïmplementeerd.

Functies die worden uitgevoerd in een Premium-abonnement delen dezelfde onderliggende App Service-infrastructuur als web-apps in PremiumV2-abonnementen. Alle documentatie voor [webapps in Azure App Service](../app-service/overview.md) is van toepassing op uw Premium-abonnementsfuncties.

> [!div class="nextstepaction"]
> [Meer informatie over de netwerkopties in Functies](./functions-networking-options.md)

[Premium-abonnement]: functions-scale.md#premium-plan
