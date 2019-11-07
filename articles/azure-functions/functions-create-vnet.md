---
title: Azure Functions integreren met een virtueel Azure-netwerk
description: Een stapsgewijze zelf studie waarin wordt uitgelegd hoe u een functie verbindt met een virtueel Azure-netwerk
author: alexkarcher-msft
manager: gwallace
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: bc6c87a28078d25a212a681206258d6d369f2867
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575546"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Zelf studie: functies integreren met een virtueel Azure-netwerk

Deze zelf studie laat zien hoe u Azure Functions kunt gebruiken om verbinding te maken met resources in een virtueel Azure-netwerk. u maakt een functie die toegang heeft tot internet en naar een virtuele machine met WordPress in het virtuele netwerk.

> [!div class="checklist"]
> * Een functie-app maken in het Premium-abonnement
> * Een WordPress-site implementeren naar VM in een virtueel netwerk
> * De functie-app verbinden met het virtuele netwerk
> * Een functie proxy maken voor toegang tot WordPress-bronnen
> * Een WordPress-bestand van binnen het virtuele netwerk aanvragen

## <a name="topology"></a>Topologie

In het volgende diagram ziet u de architectuur van de oplossing die u maakt:

 ![Gebruikers interface voor integratie van virtueel netwerk](./media/functions-create-vnet/topology.png)

Functies die worden uitgevoerd in het Premium-abonnement hebben dezelfde hosting mogelijkheden als web-apps in Azure App Service, waaronder de functie VNet-integratie. Zie [uw app integreren met een virtueel Azure-netwerk](../app-service/web-sites-integrate-with-vnet.md)voor meer informatie over VNet-integratie, met inbegrip van probleem oplossing en geavanceerde configuratie.

## <a name="prerequisites"></a>Vereisten

Voor deze zelf studie is het belang rijk dat u inzicht hebt in IP-adres sering en-subnetten. U kunt beginnen met [dit artikel voor informatie over de basis principes van adres sering en subnetten](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Er zijn veel meer artikelen en Video's online beschikbaar.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-function-app-in-a-premium-plan"></a>Een functie-app maken in een Premium-abonnement

Eerst maakt u een functie-app in het [Premium-abonnement]. Dit abonnement biedt serverloze schalen en ondersteunt de integratie van virtuele netwerken.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

U kunt de functie-app vastmaken aan het dash board door het speld pictogram in de rechter bovenhoek te selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app nadat u uw virtuele machine hebt gemaakt.

## <a name="create-a-vm-inside-a-virtual-network"></a>Een virtuele machine maken in een virtueel netwerk

Maak vervolgens een vooraf geconfigureerde virtuele machine die WordPress in een virtueel netwerk uitvoert ([LEMP7 Max. prestaties van WordPress](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) -Jetware). Een WordPress-VM wordt gebruikt vanwege de lage kosten en het gebruiks gemak. Dit zelfde scenario werkt met alle resources in een virtueel netwerk, zoals REST Api's, App Service omgevingen en andere Azure-Services. 

1. Kies in de portal **+ een resource maken** in het navigatie deel venster aan de linkerkant in het zoek veld type `WordPress LEMP7 Max Performance`en druk op ENTER.

1. Kies **WordPress LEMP max performance** in de zoek resultaten. Selecteer een software abonnement van **WordPress LEMP max performance voor CentOS** als het **Software abonnement** en selecteer **maken**.

1. Op het tabblad **basis beginselen** gebruikt u de VM-instellingen zoals opgegeven in de tabel onder de installatie kopie:

    ![Tabblad basis voor het maken van een VM](./media/functions-create-vnet/create-vm-1.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee uw resources worden gemaakt. | 
    | **[Resource groep](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | Kies `myResourceGroup`, of de resource groep die u hebt gemaakt met uw functie-app. Als u dezelfde resource groep gebruikt voor de functie-app, WordPress VM en hosting plan, is het eenvoudiger om resources op te schonen wanneer u met deze zelf studie klaar bent. |
    | **Naam van de virtuele machine** | VNET-WordPress | De naam van de virtuele machine moet uniek zijn in de resource groep |
    | **[Deel](https://azure.microsoft.com/regions/)** | (Europa) Europa - west | Kies een regio bij u in de buurt of in de buurt van de functies die toegang hebben tot de virtuele machine. |
    | **Grootte** | B1s | Kies **grootte wijzigen** en selecteer vervolgens de B1s standaard installatie kopie met 1 vCPU en 1 GB aan geheugen. |
    | **Verificatie type** | Wachtwoord | Als u wachtwoord verificatie wilt gebruiken, moet u ook een **gebruikers naam**, een veilig **wacht woord**opgeven en vervolgens het **wacht woord bevestigen**. Voor deze zelf studie hoeft u zich niet aan te melden bij de VM, tenzij u problemen moet oplossen. |

1. Kies het tabblad **netwerken** en selecteer onder virtuele netwerken configureren de optie **nieuwe maken**.

1. In **virtueel netwerk maken**gebruikt u de instellingen in de tabel onder de installatie kopie:

    ![Tabblad netwerk van virtuele machine maken](./media/functions-create-vnet/create-vm-2.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Naam** | MyResourceGroup-vnet | U kunt de standaard naam die voor het virtuele netwerk is gegenereerd, gebruiken. |
    | **Adresbereik** | 10.10.0.0/16 | Gebruik één adres bereik voor het virtuele netwerk. |
    | **Subnetnaam** | Zelf studie-net | De naam van het subnet. |
    | **Adres bereik** (subnet) | 10.10.1.0/24   | De subnet grootte definieert het aantal interfaces dat aan het subnet kan worden toegevoegd. Dit subnet wordt gebruikt door de WordPress-site.  Een `/24` subnet biedt 254 hostadressen. |

1. Selecteer **OK** om het virtuele netwerk te maken.

1. Klik op het tabblad **netwerken** op **geen** voor het **open bare IP-adres**.

1. Kies het tabblad **beheer** en kies vervolgens in het **opslag account voor diagnostische gegevens**het opslag account dat u hebt gemaakt met uw functie-app.

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **maken**. Het proces voor het maken van de virtuele machine duurt een paar minuten. De gemaakte VM heeft alleen toegang tot het virtuele netwerk.

1. Nadat de VM is gemaakt, kiest u **naar resource gaan** om de pagina voor de nieuwe virtuele machine weer te geven. Kies vervolgens **netwerken** onder **instellingen**.

1. Controleer of er geen **openbaar IP-adres**is. Noteer het **privé-IP-adres**dat u gebruikt om verbinding te maken met de virtuele machine vanuit uw functie-app.

    ![Netwerk instellingen in de VM](./media/functions-create-vnet/vm-networking.png)

U hebt nu volledig in uw virtuele netwerk een WordPress-site geïmplementeerd. Deze site is niet toegankelijk via het open bare Internet.

## <a name="connect-your-function-app-to-the-virtual-network"></a>De functie-app verbinden met het virtuele netwerk

Met een WordPress-site die wordt uitgevoerd op een virtuele machine in een virtueel netwerk, kunt u nu verbinding maken met uw functie-app voor dat virtuele netwerk.

1. Selecteer in de nieuwe functie-app **platform functies** > **netwerk**.

    ![Kies netwerken in de functie-app](./media/functions-create-vnet/networking-0.png)

1. Selecteer onder **VNet-integratie**de optie **Klik hier om te configureren**.

    ![Status voor het configureren van een netwerk functie](./media/functions-create-vnet/Networking-1.png)

1. Selecteer op de pagina integratie van virtueel netwerk de optie **VNet toevoegen (preview)** .

    ![De preview-versie van het VNet-integratie toevoegen](./media/functions-create-vnet/networking-2.png)

1. In de status van de **netwerk functie**gebruikt u de instellingen in de tabel onder de installatie kopie:

    ![Het virtuele netwerk van de functie-app definiëren](./media/functions-create-vnet/networking-3.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Virtueel netwerk** | MyResourceGroup-vnet | Dit virtuele netwerk is de versie die u eerder hebt gemaakt. |
    | **Subnet** | Nieuw subnet maken | Maak een subnet in het virtuele netwerk voor de functie-app die u wilt gebruiken. VNet-integratie moet worden geconfigureerd voor het gebruik van een leeg subnet. Het maakt niet uit of uw functies een ander subnet dan uw VM gebruiken. Het virtuele netwerk routeert automatisch verkeer tussen de twee subnetten. |
    | **Subnetnaam** | Function-net | Naam van het nieuwe subnet. |
    | **Adres blok van virtueel netwerk** | 10.10.0.0/16 | Kies hetzelfde adres blok dat wordt gebruikt door de WordPress-site. Er mag slechts één adres blok zijn gedefinieerd. |
    | **Adresbereik** | 10.10.2.0/24   | De grootte van het subnet beperkt het totale aantal exemplaren dat kan worden uitgeschaald door uw Premium-plan functie-app. In dit voor beeld wordt een `/24`-subnet met 254 beschik bare hostadressen gebruikt. Dit subnet is te veel ingericht, maar kan eenvoudig worden berekend. |

1. Selecteer **OK** om het subnet toe te voegen. Sluit de pagina's van de VNet-integratie en de status van de netwerk functie om terug te gaan naar uw functie-app-pagina.

De functie-app heeft nu toegang tot het virtuele netwerk waarin de WordPress-site wordt uitgevoerd. Vervolgens gebruikt u [Azure functions-proxy's](functions-proxies.md) om een bestand te retour neren van de WordPress-site.

## <a name="create-a-proxy-to-access-vm-resources"></a>Een proxy maken voor toegang tot VM-resources

Als VNet-integratie is ingeschakeld, kunt u een proxy in uw functie-app maken om aanvragen door te sturen naar de virtuele machine die in het virtueel netwerk wordt uitgevoerd.

1. Selecteer in de functie-app **proxy's** >  **+** en gebruik vervolgens de proxy instellingen in de tabel onder de installatie kopie:

    ![De proxy-instellingen definiëren](./media/functions-create-vnet/create-proxy.png)

    | Instelling  | Voorgestelde waarde  | Beschrijving      |
    | -------- | ---------------- | ---------------- |
    | **Naam** | Brand | De naam kan een wille keurige waarde zijn. Dit wordt gebruikt om de proxy te identificeren. |
    | **Route sjabloon** | /plant | Route die is toegewezen aan een VM-resource. |
    | **Back-end-URL** | http://< YOUR_VM_IP >/wp-content/themes/twentyseventeen/assets/images/header.jpg | Vervang `<YOUR_VM_IP>` door het IP-adres van de WordPress-VM die u eerder hebt gemaakt. Deze toewijzing retourneert één bestand van de site. |

1. Selecteer **maken** om de proxy toe te voegen aan uw functie-app.

## <a name="try-it-out"></a>Uitproberen

1. Probeer in uw browser toegang te krijgen tot de URL die u hebt gebruikt als de **back-end-URL**. Zoals verwacht, is er een time-out voor de aanvraag. Er treedt een time-out op omdat uw WordPress-site alleen is verbonden met uw virtuele netwerk en niet via internet.

1. Kopieer de waarde van de **proxy-URL** van uw nieuwe proxy en plak deze in de adres balk van uw browser. De geretourneerde afbeelding is van de WordPress-site die in het virtuele netwerk wordt uitgevoerd.

    ![Installatie kopie bestand van de WordPress-site geretourneerd](./media/functions-create-vnet/plant.png)

De functie-app is verbonden met internet en uw virtuele netwerk. De proxy ontvangt een aanvraag via het open bare Internet en fungeert vervolgens als een eenvoudige HTTP-proxy om die aanvraag door te sturen naar het verbonden virtuele netwerk. De proxy stuurt het antwoord vervolgens via internet terug naar uw publiek.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie fungeert de WordPress-site als een API die wordt aangeroepen met behulp van een proxy in de functie-app. Dit scenario maakt u een goede zelf studie, omdat u deze eenvoudig kunt instellen en visualiseren. U kunt elke andere API gebruiken die is geïmplementeerd in een virtueel netwerk. U kunt ook een functie hebben gemaakt met code die Api's aanroept die in het virtuele netwerk zijn geïmplementeerd. Een realistischer scenario is een functie die gebruikmaakt van data client-Api's voor het aanroepen van een SQL Server exemplaar dat in het virtuele netwerk is geïmplementeerd.

Functies die worden uitgevoerd in een Premium-abonnement, delen dezelfde onderliggende App Service-infra structuur als web-apps in PremiumV2-abonnementen. Alle documentatie voor [Web-apps in azure app service](../app-service/overview.md) is van toepassing op de functies van uw Premium-abonnement.

> [!div class="nextstepaction"]
> [Meer informatie over de netwerk opties in functions](./functions-networking-options.md)

[Premium-abonnement]: functions-scale.md#premium-plan
