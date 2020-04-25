---
title: 'Snelstartgids: hoge Beschik baarheid instellen met de Azure front-deur service'
description: In deze Quick Start wordt beschreven hoe u de Azure front-deur service gebruikt voor uw Maxi maal beschik bare, wereld wijde webtoepassing met hoge prestaties.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2020
ms.author: sharadag
ms.openlocfilehash: c1ce34bb7fc851d3f763241c9e92371b43ed1861
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133449"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Snelstart: een Front Door maken voor een webtoepassing met hoge beschikbaarheid

Ga aan de slag met de voor deur van Azure met behulp van de Azure Portal om hoge Beschik baarheid in te stellen voor een webtoepassing.

In deze Snelstartgids worden twee exemplaren van een webtoepassing die in verschillende Azure-regio's worden uitgevoerd, in azure front-deur gegroepeerd. U maakt een front-deur configuratie op basis van een of meer achterliggende back-ends. Met deze configuratie wordt het verkeer omgeleid naar de dichtstbijzijnde site waarop de toepassing wordt uitgevoerd. De webtoepassing wordt voortdurend bewaakt door de front-deur van Azure. De service biedt automatische failover naar de volgende beschik bare site wanneer de dichtstbijzijnde site niet beschikbaar is.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-two-instances-of-a-web-app"></a>Twee exemplaren van een web-app maken

Deze Snelstartgids vereist twee exemplaren van een webtoepassing die in verschillende Azure-regio's worden uitgevoerd. De exemplaren van de webtoepassing worden uitgevoerd in de modus *actief/actief* , waardoor er verkeer kan plaatsvinden. Deze configuratie wijkt af van een configuratie van *actief/stand-by* , waarbij één fungeert als een failover.

Als u nog geen web-app hebt, gebruikt u de volgende stappen voor het instellen van voor beeld-web-apps.

1. Meld u aan bij Azure Portal op https://portal.azure.com.

1. Selecteer op de start pagina of in het Azure-menu de optie **een resource maken**.

1. **Web** > -**Web-app**selecteren.

   ![Een web-app maken in Azure Portal](media/quickstart-create-front-door/create-web-app-azure-front-door.png)

1. In **Web-app**selecteert u het **abonnement** dat u wilt gebruiken.

1. Selecteer voor **resource groep**de optie **nieuwe maken**. Voer *FrontDoorQS_rg1* in voor de **naam** en selecteer **OK**.

1. Voer onder **Details van exemplaar**een unieke **naam** in voor de web-app. In dit voor beeld wordt *WebAppContoso-1*gebruikt.

1. Selecteer een **runtime stack**in dit voor beeld *.net Core 2,1 (LTS)*.

1. Selecteer een regio, zoals *VS-centraal*.

1. Voor **Windows-abonnement**selecteert u **nieuwe maken**. Voer *myAppServicePlanCentralUS* in als **naam** en selecteer **OK**.

1. Zorg ervoor dat de **SKU en** de grootte **Standard S1 100 totale ACU, 1,75 GB geheugen**.

1. Selecteer **controleren + maken**, Bekijk de **samen vatting**en selecteer vervolgens **maken**. Het kan enkele minuten duren voordat de implementatie is voltooid.

   ![Overzicht van de web-app controleren](media/quickstart-create-front-door/web-app-summary-azure-front-door.png)

Nadat de implementatie is voltooid, maakt u een tweede web-app. Gebruik dezelfde procedure met dezelfde waarden, met uitzonde ring van de volgende waarden:

| Instelling          | Waarde     |
| ---              | ---  |
| **Resourcegroep**   | Selecteer **Nieuw** en voer *FrontDoorQS_rg2* in |
| **Naam**             | Voer een unieke naam in voor uw web-app, in dit voor beeld *WebAppContoso-2*  |
| **Regio**           | Een andere regio, in dit voor beeld, *Zuid-Centraal VS* |
| **App Service plan** > **Windows-abonnement** app service plan         | Selecteer **Nieuw** en voer *myAppServicePlanSouthCentralUS*in en selecteer **OK** . |

## <a name="create-a-front-door-for-your-application"></a>Een Front Door maken voor uw toepassing

Configureer Azure front-deur om gebruikers verkeer te leiden op basis van de laagste latentie tussen de twee web apps-servers. Als u wilt beginnen, voegt u een frontend-host voor Azure front deur toe.

1. Selecteer op de start pagina of in het Azure-menu de optie **een resource maken**. Selecteer **netwerk** > **front-deur**.

1. Selecteer een **abonnement**in **een front deur maken**.

1. Voor **resource groep**selecteert u **Nieuw**, voert u *FrontDoorQS_rg0* in en selecteert u **OK**.  In plaats daarvan kunt u een bestaande resource groep gebruiken.

1. Als u een resource groep hebt gemaakt, selecteert u een locatie voor de **resource groep**en selecteert u **volgende: Configuratie**.

1. Selecteer in **frontends/domeinen**de **+** optie **een frontend-host toevoegen**openen.

1. Voer bij **hostnaam**een wereld wijd unieke hostnaam in. In dit voor beeld wordt *Contoso-front-end*gebruikt. Selecteer **Toevoegen**.

   ![Een frontend-host toevoegen voor Azure front deur](media/quickstart-create-front-door/add-frontend-host-azure-front-door.png)

Maak vervolgens een back-end-groep die uw twee web-apps bevat.

1. Als u nog steeds **een voor deur maakt**, **+** selecteert u in **back**-endservers **een back-end-groep toevoegen**.

1. Voer bij **naam** *myBackendPool*in.

1. Selecteer **een back-end toevoegen**. Selecteer *app service*voor het **type back-end**.

1. Selecteer uw abonnement en kies vervolgens de eerste web-app die u hebt gemaakt op basis van de naam van de **back-end**. In dit voor beeld is de web *-app WebAppContoso-1*. Selecteer **Toevoegen**.

1. Selecteer opnieuw **een back-end toevoegen** . Selecteer *app service*voor het **type back-end**.

1. Selecteer uw abonnement, en kies de tweede web-app die u hebt gemaakt op basis van de **back-end-hostnaam**. Selecteer **Toevoegen**.

   ![Een backend-host toevoegen aan de voor deur](media/quickstart-create-front-door/add-backend-host-pool-azure-front-door.png)

Voeg ten slotte een routerings regel toe. Met een regel voor door sturen wordt uw frontend-host toegewezen aan de back-end-groep. De regel stuurt een aanvraag door `contoso-frontend.azurefd.net` naar **myBackendPool**.

1. Selecteer nog steeds **een voor deur maken**, in **routerings regels**, selecteren **+** om een routerings regel te configureren.

1. Voer in **een regel toevoegen**voor **naam** *LocationRule*in. Accepteer alle standaard waarden en selecteer **toevoegen** om de routerings regel toe te voegen.

   >[!WARNING]
   > U **moet** ervoor zorgen dat elk van de frontend-hosts in de voor deur een regel voor door sturen met een`\*`standaardpad () is gekoppeld. Dat wil zeggen dat voor al uw routerings regels ten minste één routerings regel moet zijn voor elk van de frontend-hosts die zijn gedefinieerd op`\*`het standaardpad (). Als u dit niet doet, kan het verkeer van eind gebruikers niet goed worden gerouteerd.

1. Selecteer **controleren + maken**en vervolgens **maken**.

   ![Geconfigureerde Azure-voor deur](media/quickstart-create-front-door/configuration-azure-front-door.png)

## <a name="view-azure-front-door-in-action"></a>De voor deur van Azure in actie weer geven

Wanneer u een voor deur hebt gemaakt, duurt het enkele minuten voordat de configuratie wereld wijd wordt geïmplementeerd. Zodra het proces is voltooid, opent u de frontend-host die u hebt gemaakt. Ga in een browser naar `contoso-frontend.azurefd.net`. Uw aanvraag wordt automatisch naar de dichtstbijzijnde server gerouteerd vanaf de opgegeven servers in de back-endadresgroep.

Als u deze apps in deze Quick Start hebt gemaakt, wordt er een informatie pagina weer geven.

Voer de volgende stappen uit om direct globale failover in actie te testen:

1. Open een browser, zoals hierboven beschreven, en ga naar het frontend-adres `contoso-frontend.azurefd.net`:.

1. Zoek en selecteer in de Azure Portal app- *Services*. Schuif omlaag om een van uw web-apps te zoeken, **WebAppContoso-1** in dit voor beeld.

1. Selecteer uw web-app en selecteer vervolgens **stoppen**en **Ja** om te controleren.

1. Vernieuw de browser. U ziet nu dezelfde informatie pagina.

   >[!TIP]
   >Er is een beetje vertraging voor deze acties. Mogelijk moet u opnieuw vernieuwen.

1. Zoek de andere web-app en stop deze ook.

1. Vernieuw de browser. Deze keer wordt een fout bericht weer gegeven.

   ![Beide exemplaren van de web-app zijn gestopt](media/quickstart-create-front-door/web-app-stopped-message.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent, kunt u alle items verwijderen die u hebt gemaakt. Als u een resource groep verwijdert, wordt ook de inhoud ervan verwijderd. Als u deze voor deur niet wilt gebruiken, moet u resources verwijderen om onnodige kosten te voor komen.

1. Zoek en selecteer **resource groepen**In de Azure Portal, of selecteer **resource groepen** in het menu Azure Portal.

1. Filter of schuif omlaag om een resource groep te zoeken, zoals **FrontDoorQS_rg0**.

1. Selecteer de resource groep en selecteer vervolgens **resource groep verwijderen**.

   >[!WARNING]
   >Deze actie is irreversable.

1. Typ de naam van de resource groep die u wilt controleren en selecteer vervolgens **verwijderen**.

Herhaal de procedure voor de andere twee groepen.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het toevoegen van een aangepast domein aan uw voor deur.
> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen](front-door-custom-domain.md)

Voor meer informatie over routerings verkeer, Zie [voor deur routerings methoden](front-door-routing-methods.md).
