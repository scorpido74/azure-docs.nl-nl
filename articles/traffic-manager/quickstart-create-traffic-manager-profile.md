---
title: Snelstart:Een profiel maken voor HA-toepassingen - Azure-portal - Azure Traffic Manager
description: In dit snelstartartikel wordt beschreven hoe u een Traffic Manager-profiel maakt voor het bouwen van webtoepassingen met hoge beschikbaarheid.
services: traffic-manager
author: rohinkoul
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: rohink
ms.openlocfilehash: 559ed0a134bb6db78d1e89634138b4025e04152b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76934770"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Snelstart: een Traffic Manager-profiel maken met de Azure-portal

In deze quickstart wordt beschreven hoe u een Traffic Manager-profiel maakt die hoge beschikbaarheid van uw webtoepassing biedt.

In deze quickstart leest u meer over twee exemplaren van een webtoepassing. Ze worden elk in een andere Azure-regio uitgevoerd. U maakt een Traffic Manager-profiel op basis van [eindpuntprioriteit](traffic-manager-routing-methods.md#priority-traffic-routing-method). het profiel stuurt gebruikersverkeer door naar de primaire site waar de webtoepassing wordt uitgevoerd. Traffic Manager bewaakt de webtoepassing continu. Als de primaire site niet beschikbaar is, biedt Traffic Manager automatische failover voor de back-upsite.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten

Voor deze quickstart moeten twee exemplaren van een webtoepassing worden geïmplementeerd in twee verschillende Azure-regio's (*VS - oost* en *Europa - west*). Elk exemplaar dient als primair en failover-eindpunt voor Traffic Manager.

1. Selecteer linksboven in het scherm **de**optie Een > **bronwebweb-app** > **Web**maken .

1. Typ of selecteer in **Een web-app maken**de volgende waarden op het tabblad **Basisbeginselen:**

   - **Subscription** > **Abonnementsbrongroep:** selecteer **Nieuw maken** en typ **myResourceGroupTM1**.
   - **Naam van instantiedetails:** > **Name**Typ *myWebAppEastUS*.
   - **Instantiedetails** > **publiceren**: Code **selecteren**.
   - **Runtime-stack voor instantiedetails** > **Runtime stack**: **selecteer ASP.NET V4.7**
   - **Instance Details** > **Operating System**: Selecteer **Windows**.
   - **Instantiedetails** > **regio**: Selecteer **Oost-VS**.
   - **App Service Plan** > **Windows Plan (Oost-VS)**: Selecteer **Nieuw maken** en typ **vervolgens myAppServicePlanEastUS**
   - **App Service Plan** > **Sku en grootte**: Selecteer Standaard **S1**.
   
3. Selecteer het tabblad **Controle** of selecteer **Volgende:Controle**.  Stel **Application** **Insights** > **In schakelen application insights** in op **Nee.**

4. Selecteer **Controleren en maken**

5. Controleer de instellingen en klik op **Maken**.  Als de web-app wordt geïmplementeerd, wordt er een standaardwebsite gemaakt.

6. Volg de stappen om een tweede web-app genaamd *myWebAppWestEurope*te maken , met een **resourcegroepnaam** van *myResourceGroupTM2,* een **regio** van *West-Europa,* een naam van **het App Service Plan** van **myAppServicePlanWestEurope**en alle andere instellingen hetzelfde als *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel waarmee gebruikersverkeer wordt doorgestuurd op basis van eindpuntprioriteit.

1. Selecteer linksboven in het scherm **de** > optie Een**netwerkverkeersbeheerprofiel**voor bronnen**maken** > .
2. Voer in **Traffic Manager-profiel maken** de volgende instellingen in (of selecteer ze):

    | Instelling | Waarde |
    | --------| ----- |
    | Name | Voer een unieke naam in voor uw Traffic Manager-profiel.|
    | Routeringsmethode | Selecteer **Prioriteit**.|
    | Abonnement | Selecteer het abonnement waarop u het Traffic Manager-profiel wilt toepassen. |
    | Resourcegroep | Selecteer *myResourceGroupTM1*.|
    | Locatie |Deze instelling verwijst naar de locatie van de resourcegroep. De instelling heeft geen gevolgen voor het Traffic Manager-profiel dat globaal wordt geïmplementeerd.|

3. Selecteer **Maken**.

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg de website in *VS - oost* toe als primair eindpunt om alle gebruikersverkeer te routeren. Voeg de website in *Europa - west* toe als failover-eindpunt. Als het primaire eindpunt niet beschikbaar is, wordt het verkeer automatisch naar het failover-eindpunt gerouteerd.

1. Voer in de zoekbalk van de portal de naam van het Traffic Manager-profiel in dat u in de vorige sectie hebt gemaakt.
2. Selecteer het profiel in de lijst met zoekresultaten.
3. Selecteer in **Traffic Manager-profiel**, in de sectie **Instellingen**, de optie **Eindpunten** en selecteer **Toevoegen**.
4. Voer de volgende instellingen in (of selecteer ze):

    | Instelling | Waarde |
    | ------- | ------|
    | Type | Selecteer **Azure-eindpunt**. |
    | Name | Voer *myPrimaryEndpoint* in. |
    | Doelbrontype | Selecteer **App-service**. |
    | Doelbron | Selecteer **Een app-service** > **Oost-VS kiezen**. |
    | Prioriteit | Selecteer **1**. Alle verkeer gaat naar dit eindpunt indien het in orde is. |

    ![Schermopname van de plaats waar u een eindpunt aan uw Traffic Manager-profiel toevoegt.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Selecteer **OK**.
6. Herhaal stap 3 en 4 met de volgende instellingen als u een failover-eindpunt voor uw tweede Azure-regio wilt maken:

    | Instelling | Waarde |
    | ------- | ------|
    | Type | Selecteer **Azure-eindpunt**. |
    | Name | Voer*myFailoverEndpoint* in. |
    | Doelbrontype | Selecteer **App-service**. |
    | Doelbron | Selecteer **Een app-service** > **West-Europa kiezen**. |
    | Prioriteit | Selecteer **2**. Alle verkeer gaat naar dit failover-eindpunt als het primaire eindpunt niet in orde is. |

7. Selecteer **OK**.

Als u de twee eindpunten hebt toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**. De bewakingsstatus is nu **Online**.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In deze sectie controleert u de domeinnaam van uw Traffic Manager-profiel. Tevens configureert u het primaire eindpunt zodanig dat het niet beschikbaar is. Ten slotte kunt u zien dat de web-app nog steeds beschikbaar is. Dat komt omdat Traffic Manager het verkeer naar het failover-eindpunt stuurt.

### <a name="check-the-dns-name"></a>DNS-naam controleren

1. Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in de vorige sectie hebt gemaakt.
2. Selecteer het Traffic Manager-profiel. **Overzicht** wordt weergegeven.
3. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt.
  
   ![Schermopname van de locatie van de DNS-naam van uw Traffic Manager](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien

1. Voer in een webbrowser de DNS-naam van het Traffic Manager-profiel in om de standaardwebsite van uw web-app te bekijken.

    > [!NOTE]
    > In dit quickstartscenario worden alle aanvragen gerouteerd naar het primaire eindpunt. Het is ingesteld op **Priority 1**.

    ![Schermopname van de webpagina ter bevestiging van de beschikbaarheid van het Traffic Manager-profiel](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. Als u de failover van Traffic Manager in werking wilt zien, schakelt u de primaire site uit:
    1. Selecteer op de pagina met het Traffic Manager-profiel, in de sectie **Overzicht**, **myPrimaryEndpoint**.
    2. Selecteer in *myPrimaryEndpoint* de optie **Uitgeschakeld** > **Opslaan**.
    3. Sluit **myPrimaryEndpoint**. Merk op dat de status nu **Uitgeschakeld** is.
3. Kopieer de DNS-naam van het Traffic Manager-profiel uit de vorige stap om de website in een nieuwe sessie van de webbrowser te kunnen bekijken.
4. Controleer of de web-app nog beschikbaar is.

Het primaire eindpunt is niet beschikbaar, dus bent u naar het failover-eindpunt doorgestuurd.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroepen, de webtoepassingen en alle gerelateerde resources als u klaar bent. Selecteer hiertoe elk afzonderlijk item op uw dashboard en selecteer boven aan de pagina de optie **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Traffic Manager-profiel gemaakt. Hiermee kunt u gebruikersverkeer doorsturen voor webtoepassingen van hoge beschikbaarheid. Voor meer informatie over het routeren van verkeer gaat u door naar de zelfstudies voor Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Traffic Manager-zelfstudies)
