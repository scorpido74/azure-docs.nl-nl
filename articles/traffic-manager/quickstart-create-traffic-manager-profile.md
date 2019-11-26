---
title: Quickstart:Create a profile for HA of applications - Azure portal - Azure Traffic Manager
description: In dit snelstartartikel wordt beschreven hoe u een Traffic Manager-profiel maakt voor het bouwen van webtoepassingen met hoge beschikbaarheid.
services: traffic-manager
author: asudbring
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: allensu
ms.openlocfilehash: b2163b76dc3a301359cf3474789c5b473f9e4552
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483676"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Quickstart: Create a Traffic Manager profile using the Azure portal

In deze quickstart wordt beschreven hoe u een Traffic Manager-profiel maakt die hoge beschikbaarheid van uw webtoepassing biedt.

In deze quickstart leest u meer over twee exemplaren van een webtoepassing. Ze worden elk in een andere Azure-regio uitgevoerd. U maakt een Traffic Manager-profiel op basis van [eindpuntprioriteit](traffic-manager-routing-methods.md#priority-traffic-routing-method). het profiel stuurt gebruikersverkeer door naar de primaire site waar de webtoepassing wordt uitgevoerd. Traffic Manager bewaakt de webtoepassing continu. Als de primaire site niet beschikbaar is, biedt Traffic Manager automatische failover voor de back-upsite.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten

Voor deze quickstart moeten twee exemplaren van een webtoepassing worden geïmplementeerd in twee verschillende Azure-regio's (*US - oost* en *Europa - west*). Elk exemplaar dient als primair en failover-eindpunt voor Traffic Manager.

1. Selecteer **Een resource maken** > **Web** > **Web-app** linksboven in het scherm.

1. In **Create a Web App**, type or select the following values in the **Basics** tab:

   - **Subscription** > **Resource Group**: Select **Create new** and then type **myResourceGroupTM1**.
   - **Instance Details** > **Name**: Type *myWebAppEastUS*.
   - **Instance Details** > **Publish**: Select **Code**.
   - **Instance Details** > **Runtime stack**: Select **ASP.NET V4.7**
   - **Instance Details** > **Operating System**: Select **Windows**.
   - **Instance Details** > **Region**:  Select **East US**.
   - **App Service Plan** > **Windows Plan (East US)** : Select **Create new** and then type **myAppServicePlanEastUS**
   - **App Service Plan** > **Sku and size**: Select **Standard S1**.
   
3. Select the **Monitoring** tab, or select **Next:Monitoring**.  Under **Monitoring**, set **Application Insights** > **Enable Application Insights** to **No**.

4. Select **Review and create**

5. Review the settings, and then click **Create**.  Als de web-app wordt geïmplementeerd, wordt er een standaardwebsite gemaakt.

6. Follow the steps to create a second Web App named *myWebAppWestEurope*, with a **Resource Group** name of *myResourceGroupTM2*, a **Region** of *West Europe*, a **App Service Plan** name of **myAppServicePlanWestEurope**, and all the other settings the same as *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel waarmee gebruikersverkeer wordt doorgestuurd op basis van eindpuntprioriteit.

1. Selecteer links boven in het scherm de optie **Een resource maken** > **Netwerken** > **Traffic Manager-profiel**.
2. Voer in **Traffic Manager-profiel maken** de volgende instellingen in (of selecteer ze):

    | Instelling | Waarde |
    | --------| ----- |
    | Naam | Voer een unieke naam in voor uw Traffic Manager-profiel.|
    | Routeringsmethode | Selecteer **Prioriteit**.|
    | Abonnement | Selecteer het abonnement waarop u het Traffic Manager-profiel wilt toepassen. |
    | Resourcegroep | Selecteer *myResourceGroupTM1*.|
    | Locatie |Deze instelling verwijst naar de locatie van de resourcegroep. De instelling heeft geen gevolgen voor het Traffic Manager-profiel dat globaal wordt geïmplementeerd.|

3. Selecteer **Maken**.

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg de website in *US - oost* toe als primair eindpunt om alle gebruikersverkeer te routeren. Voeg de website in *Europa - west* toe als failover-eindpunt. Als het primaire eindpunt niet beschikbaar is, wordt het verkeer automatisch naar het failover-eindpunt gerouteerd.

1. Voer in de zoekbalk van de portal de naam van het Traffic Manager-profiel in dat u in de vorige sectie hebt gemaakt.
2. Selecteer het profiel in de lijst met zoekresultaten.
3. Selecteer in **Traffic Manager-profiel**, in de sectie **Instellingen**, de optie **Eindpunten** en selecteer **Toevoegen**.
4. Voer de volgende instellingen in (of selecteer ze):

    | Instelling | Waarde |
    | ------- | ------|
    | Type | Selecteer **Azure-eindpunt**. |
    | Naam | Voer *myPrimaryEndpoint* in. |
    | Doelbrontype | Selecteer **App Service**. |
    | Doelbron | Selecteer **Choose an app service** > **US - oost**. |
    | Prioriteit | Selecteer **1**. Alle verkeer gaat naar dit eindpunt indien het in orde is. |

    ![Schermopname van de plaats waar u een eindpunt aan uw Traffic Manager-profiel toevoegt.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Selecteer **OK**.
6. Herhaal stap 3 en 4 met de volgende instellingen als u een failover-eindpunt voor uw tweede Azure-regio wilt maken:

    | Instelling | Waarde |
    | ------- | ------|
    | Type | Selecteer **Azure-eindpunt**. |
    | Naam | Voer*myFailoverEndpoint* in. |
    | Doelbrontype | Selecteer **App Service**. |
    | Doelbron | Selecteer **Choose an app service** > **Europa - west**. |
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
