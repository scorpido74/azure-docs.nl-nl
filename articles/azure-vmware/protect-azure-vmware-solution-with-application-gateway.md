---
title: Azure-toepassing gateway gebruiken voor het beveiligen van uw web-apps op de Azure VMware-oplossing
description: Configureer Azure-toepassing gateway zo dat uw web-apps die worden uitgevoerd op een Azure VMware-oplossing, veilig zichtbaar zijn.
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: ad7cd36d77da41d75ea9dcc18a51d0ffc5540d2a
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579817"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Azure-toepassing gateway gebruiken voor het beveiligen van uw web-apps op de Azure VMware-oplossing

[Azure-toepassing gateway](https://azure.microsoft.com/services/application-gateway/) is een laag 7-webverkeer Load Balancer waarmee u verkeer naar uw webtoepassingen kunt beheren. Het biedt veel mogelijkheden: sessie affiniteit op basis van cookies, route ring op basis van URL en WAF (Web Application firewall). (Zie [Azure-toepassing gateway functies](../application-gateway/features.md)(Engelstalig) voor een volledige lijst met functies. Het wordt aangeboden in twee versies, v1 en v2. Beide zijn getest met web-apps die worden uitgevoerd op de Azure VMware-oplossing.

In dit artikel wordt gebruikgemaakt van een gemeen schappelijk scenario met Application Gateway voor een webserver farm met een reeks configuraties en aanbevelingen voor het beveiligen van een web-app die wordt uitgevoerd op een Azure VMware-oplossing. 

## <a name="topology"></a>Topologie
Zoals in de volgende afbeelding wordt weer gegeven, kunt Application Gateway worden gebruikt voor het beveiligen van virtuele Azure IaaS-machines, virtuele-machine schaal sets van Azure of on-premises servers. Virtuele Azure VMware-oplossingen worden beschouwd als on-premises servers door Application Gateway.

![Application Gateway beschermt Vm's van Azure VMware-oplossing.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure-toepassing gateway is momenteel de enige ondersteunde methode om web-apps weer te geven die worden uitgevoerd op virtuele machines van Azure VMware-oplossingen.

In het volgende diagram ziet u het test scenario dat wordt gebruikt om Application Gateway te valideren met Azure VMware Solution-webtoepassingen.

![Application Gateway integratie met de Azure VMware-oplossing die web-apps uitvoert.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-avs-scenario.png)

Het Application Gateway exemplaar wordt geïmplementeerd op de hub in een toegewezen subnet. Het heeft een openbaar IP-adres van Azure; het is raadzaam om de standaard DDoS-beveiliging voor het virtuele netwerk te activeren. De webserver wordt gehost op een Azure VMware-oplossing privécloud achter NSX t0 en T1-routers. De Azure VMware-oplossing maakt gebruik van [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) om de communicatie met de hub en on-premises systemen mogelijk te maken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement.
- Een Azure VMware-oplossing privécloud geïmplementeerd en wordt uitgevoerd.

## <a name="deployment-and-configuration"></a>Implementatie en configuratie

1. Zoek in het Azure Portal naar **Application Gateway** en selecteer **toepassings gateway maken**.

2. Geef de basis gegevens op zoals in de volgende afbeelding. Selecteer vervolgens **volgende: front-end>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Application Gateway maken":::

3. Kies het type van het frontend-IP-adres. Kies voor openbaar een bestaand openbaar IP-adres of maak een nieuw account. Selecteer **volgende: back **-ends>.

    > [!NOTE]
    > Alleen standaard-en WAF-Sku's (Web Application firewall) worden ondersteund voor privé-front-end.

4. Voeg vervolgens een back-end-pool toe, waarmee een set exemplaren wordt beschreven die deel uitmaken van de toepassing of service (in dit geval virtuele machines die worden uitgevoerd op de infra structuur van de Azure VMware-oplossing). Geef de details op van webservers die worden uitgevoerd op de Azure VMware-oplossing privécloud en selecteer **toevoegen**; Selecteer vervolgens **volgende: configuratie>**.

1. Selecteer op het tabblad **configuratie** **een regel voor het toevoegen van een route ring**.

6. Geef op het tabblad **listener** de details op voor de listener. Als HTTPS is geselecteerd, moet een certificaat worden opgegeven, hetzij vanuit een PFX-bestand of een bestaand certificaat van Azure Key Vault. 

7. Selecteer het tabblad **backend-doelen** en selecteer de back-end-groep die u eerder hebt gemaakt. Selecteer voor het veld **http-instellingen** de optie **nieuwe toevoegen**.

8. Configureer de para meters voor de HTTP-instellingen. Selecteer **Toevoegen**.

9. Als u op paden gebaseerde regels wilt configureren, selecteert u **meerdere doelen toevoegen om een op pad gebaseerde regel te maken**. 

10. Voeg een op een pad gebaseerde regel toe en selecteer **toevoegen**. Herhaal dit om aanvullende regels op basis van pad toe te voegen. 

11. Wanneer u klaar bent met het toevoegen van op het pad gebaseerde regels, selecteert u opnieuw **toevoegen** . Selecteer vervolgens **volgende: labels>**. 

12. Voeg de gewenste labels toe. Selecteer **volgende: controleren +>maken **.

13. Er wordt een validatie uitgevoerd op uw Application Gateway. Als dit lukt, selecteert u **maken** om te implementeren.

## <a name="configuration-examples"></a>Configuratie voorbeelden

In deze sectie leert u hoe u Application Gateway kunt configureren met virtuele machines van Azure VMware-oplossingen als back-endservers voor de volgende use-cases: 

- [Meerdere sites hosten](#hosting-multiple-sites)
- [Route ring op URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Meerdere sites hosten

U kunt de Azure Portal gebruiken om meerdere websites te configureren wanneer u een toepassings gateway maakt. In deze zelf studie definieert u de back-end-adres groepen met virtuele machines die worden uitgevoerd op een privécloud van Azure VMware-oplossing op een bestaande toepassings gateway. De toepassings gateway maakt deel uit van een virtuele hub-netwerk zoals beschreven in [Azure VMware-oplossing integreren in een hub-en spoke-architectuur](concepts-hub-and-spoke.md). In deze zelf studie wordt ervan uitgegaan dat u beschikt over meerdere domeinen en maakt gebruik van voor beelden van www.contoso.com en www.fabrikam.com.

1. Maak de virtuele machines. Maak in de privécloud van de Azure VMware-oplossing twee verschillende groepen met virtuele machines. de ene vertegenwoordigt Contoso en de tweede fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="Application Gateway maken":::

    Ter illustratie van deze zelf studie hebben we Windows Server 2016 met Internet Information Services (IIS)-rol geïnstalleerd. Wanneer de virtuele machines zijn geïnstalleerd, voert u de volgende Power shell-opdrachten uit om IIS op elke virtuele machine te configureren. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Voeg de back-end-Pools toe. Selecteer in een bestaand toepassings gateway-exemplaar **Back-upgroepen** in het linkermenu, selecteer  **toevoegen**en voer de details van de nieuwe groepen in. Selecteer **toevoegen** in het rechterdeel venster.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="Application Gateway maken" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. Maak in de sectie **listeners** een nieuwe listener voor elke website. Geef de Details voor elke listener op en selecteer **toevoegen**.

4. Selecteer in het linkernavigatievenster de optie **http-instellingen** en selecteer **toevoegen** in het linkerdeel venster. Vul de details in om een nieuwe HTTP-instelling te maken en selecteer **Opslaan**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="Application Gateway maken" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. Maak de regels in het gedeelte **regels** van het menu links. Koppel elke regel aan de bijbehorende listener. Selecteer **Toevoegen**.

6. Configureer de bijbehorende back-end-en HTTP-instellingen. Selecteer **Toevoegen**.

7. Test de verbinding. Open uw favoriete browser en navigeer naar de verschillende websites die worden gehost op uw Azure VMware-oplossings omgeving http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="Application Gateway maken":::

### <a name="routing-by-url"></a>Route ring op URL

U kunt Azure-toepassing gateway gebruiken om op URL-pad gebaseerde routerings regels te configureren. In deze zelf studie definieert u de back-end-adres groepen met virtuele machines die worden uitgevoerd op een privécloud van Azure VMware-oplossing op een bestaande toepassings gateway. De toepassings gateway maakt deel uit van een virtuele hub-netwerk zoals beschreven in [Azure VMware-oplossing Azure native Integration-documentatie](concepts-hub-and-spoke.md). Vervolgens maakt u routeringsregels die ervoor zorgen dat webverkeer wordt omgeleid naar de servers in de pools.

1. Maak de virtuele machines. Maak in de privécloud van de Azure VMware-oplossing een pool met virtuele machines die de webfarm vertegenwoordigen. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="Application Gateway maken"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Voeg de back-end-Pools toe. U moet drie nieuwe back-endservers toevoegen in een bestaand toepassings gateway-exemplaar. Selecteer **Back-endpools** in het linkermenu. Selecteer **toevoegen** en voer de details in van de eerste groep **Contoso-Web**. Voeg één virtuele machine toe als doel. Selecteer **Toevoegen**. Herhaal dit proces voor **Contoso-afbeeldingen** en **Contoso-video**en voeg één unieke VM toe aan elk als doel. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Application Gateway maken" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. Maak in de sectie **listeners** een nieuwe listener van het type Basic met behulp van poort 8080.

4. Selecteer in het linkernavigatievenster de optie **http-instellingen** en selecteer **toevoegen** in het linkerdeel venster. Vul de details in om een nieuwe HTTP-instelling te maken en selecteer **Opslaan**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Application Gateway maken":::

5. Maak de regels in het gedeelte **regels** van het menu links. Koppel elke regel aan de eerder gemaakte listener. Configureer vervolgens de belangrijkste back-end-en HTTP-instellingen. Selecteer **Toevoegen**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Application Gateway maken":::

6. Test de configuratie. Open de toepassings gateway op het Azure Portal en kopieer in het gedeelte **overzicht** het openbaar IP-adres. Open vervolgens een nieuw browser venster en voer de URL in `http://<app-gw-ip-address>:8080` . 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Application Gateway maken":::

    Wijzig de URL in `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Application Gateway maken":::

    Wijzig de URL opnieuw in `http://<app-gw-ip-address>:8080/video/test.htm` .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Application Gateway maken":::

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie over Azure-toepassing gateway](https://docs.microsoft.com/azure/application-gateway/) voor meer configuratie voorbeelden.
