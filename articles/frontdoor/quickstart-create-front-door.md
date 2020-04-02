---
title: 'Quickstart: Voordeurprofiel maken voor hoge beschikbaarheid van toepassingen'
description: In dit snelstartartikel wordt beschreven hoe u een Front Door-profiel maakt voor een webtoepassing met hoge beschikbaarheid en uitstekende prestaties.
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
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521454"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Snelstart: een Front Door maken voor een webtoepassing met hoge beschikbaarheid

In deze snelstart wordt beschreven hoe u een Front Door-profiel maakt dat hoge beschikbaarheid en uitstekende prestaties biedt voor uw webtoepassing. 

Het scenario dat in deze snelstart wordt beschreven, beschrijft twee exemplaren van een webtoepassing die in verschillende Azure-regio's wordt uitgevoerd. Er wordt een configuratie van Front Door gemaakt op basis van [back-ends met dezelfde weging en prioriteit](front-door-routing-methods.md) die helpt om verkeer van gebruikers om te leiden naar de dichtstbijzijnde set site-back-ends waarop de toepassing wordt uitgevoerd. De weboepassing wordt continu gecontroleerd door Front Door en de service biedt automatische failover naar de eerstvolgende beschikbare back-end als de dichtstbijzijnde site niet beschikbaar is.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 
Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="prerequisites"></a>Vereisten
Voor deze snelstart moeten twee exemplaren van een webtoepassing worden geïmplementeerd, die in verschillende Azure-regio's wordt uitgevoerd (*VS - oost* en *Europa - west*). Beide exemplaren van de webtoepassing worden uitgevoerd in de modus Active/Active, wat inhoudt dat een van beide exemplaren op elk gewenst moment verkeer kan accepteren, in tegenstelling tot een configuratie Active/Stand-By waarbij het ene exemplaar fungeert als een failover.

1. Selecteer linksboven in het scherm de optie > Een > **bronweb-webapp****Web** **maken**.
2. Voer in **Web-app** de volgende gegevens in (of selecteer ze) en voer standaardinstellingen in waar ze niet zijn opgegeven:

     | Instelling         | Waarde     |
     | ---              | ---  |
     | Resourcegroep          | Selecteer **Nieuw** en typ *myResourceGroupFD1* |
     | Name           | Voer een unieke naam voor de web-app in  |
     | Runtimestack          | Een Runtime-stack voor uw app selecteren |
     |      Regio  |   VS - west        |
     | App Service-plan/-locatie         | Selecteer **Nieuw**.  Voer in het App Service-plan *myAppServicePlanEastUS* in en selecteer **OK**.| 
     |SKU en grootte  | Selecteer **Standaard** **S1 100 totaal ACU, 1,75 GB geheugen selecteren** |
     
3. Selecteer **Controleren + maken**.
4. Bekijk de overzichtsgegevens voor de web-app. Selecteer **Maken**.
5. Na ongeveer 5 minuten wordt een standaardwebsite gemaakt wanneer de web-app is geïmplementeerd.
6. Herhaal stap 1 tot en met 3 om een tweede website te maken in een andere Azure-regio met de volgende instellingen:

     | Instelling         | Waarde     |
     | ---              | ---  |
     | Resourcegroep          | Selecteer **Nieuw** en typ *myResourceGroupFD2* |
     | Name           | Voer een unieke naam voor de web-app in  |
     | Runtimestack          | Een Runtime-stack voor uw app selecteren |
     |      Regio  |   Europa -west      |
     | App Service-plan/-locatie         | Selecteer **Nieuw**.  Voer in het App Service-plan *myAppServicePlanWestEurope* in en selecteer **OK**.|   
     |SKU en grootte  | Selecteer **Standaard** **S1 100 totaal ACU, 1,75 GB geheugen selecteren** |
    
## <a name="create-a-front-door-for-your-application"></a>Een Front Door maken voor uw toepassing
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Een front-end-host toevoegen voor Front Door
Maak een Front Door-configuratie die verkeer van gebruikers omleid op basis van de laagste latentie tussen de twee back-ends.

1. Selecteer linksboven in het scherm de optie **Een bron** > **netwerkvoordeur****maken** > .
2. Voer **in Een voordeur maken**de volgende gegevens in of selecteer ze en voer standaardinstellingen in waarin geen enkele is opgegeven:

     | Instelling         | Waarde     |
     | ---              | ---  |
     |Abonnement  | Selecteer het abonnement waarin u Voordeur wilt maken.|
     | Resourcegroep          | Nieuw **selecteren**en vervolgens *myResourceGroupFD0* typen |
     | Resourcegroeplocatie  |   VS - centraal        |
     
     > [!NOTE]
     > U hoeft geen nieuwe resourcegroep te maken om Front Door in te zetten.  Als u ook een bestaande resourcegroep selecteren.
     
3. Klik **op Volgende: Configuratie**.
4. Klik op het pictogram '+' op de Frontends/domeinenkaart.  Voer voor `<Your Initials>frontend` **Hostnaam** in . Deze hostnaam moet wereldwijd uniek zijn, Front Door zorgt voor validatie.
5. Klik op**toevoegen**.

### <a name="b-add-application-backend-and-backend-pools"></a>B. Toepassings-back-end en back-endpools toevoegen

Vervolgens moet u uw Frontends/domeinen en toepassingsbackend(s) configureren in een backendpool voor Front Door om te weten waar uw toepassing zich bevindt. 

1. Klik op het pictogram '+' op de backend-poolkaart om een backendpool voor **naam** voor uw backendpool toe te voegen, voer . `myBackendPool`
2. Klik vervolgens op **Een backend toevoegen** om uw eerder gemaakte websites toe te voegen.
3. Selecteer **Backend-hosttype** als 'App-service', selecteer het abonnement waarin u de website hebt gemaakt en kies vervolgens de eerste website in de vervolgkeuzelijst **Backend-hostnaam.**
4. Laat de overige velden voorlopig ongewijzigd en klik op **Toevoegen**.
5. Selecteer **Backend-hosttype** als 'App-service', selecteer het abonnement waarin u de website hebt gemaakt en kies vervolgens de **tweede** website in de vervolgkeuzelijst **Backend-hostnaam.**
6. Laat de overige velden voorlopig ongewijzigd en klik op **Toevoegen**.
7. U er optioneel voor kiezen om de instellingen voor statussondes en load-balancing voor de backendpool bij te werken, maar de standaardwaarden moeten ook werken. Klik in beide gevallen op **Toevoegen**.


### <a name="c-add-a-routing-rule"></a>C. Een routeringsregel toevoegen
1. Klik ten slotte op het pictogram '+' op de kaart routeringsregels om een routeringsregel te configureren. Dit is nodig om de front-end-host te koppelen aan de back-endpool. Wat u doet, is instellen dat een aanvraag afkomstig van `myappfrontend.azurefd.net` wordt doorgestuurd naar de back-endpool `myBackendPool`. 
2. Voer voor **Naam** 'Locatieregel' in.
3. Klik op **Toevoegen** om de regel voor doorsturen toe te voegen voor de Front Door. 
4. Klik op **Controleren en maken**.
5. Bekijk de instellingen voor de creatie van de voordeur. Klik **op Maken**

>[!WARNING]
> U **moet** ervoor zorgen dat elk van de front-endhosts in de Front Door is geconfigureerd met een regel voor doorsturen waaraan een standaardpad ('/\*') is gekoppeld. Dat wil zeggen dat er ten minste één regel voor doorsturen moet zijn voor elk van de front-endhosts die is gedefinieerd in het standaardpad ('/\*'). Als u dit niet doet, wordt uw eindgebruikersverkeer mogelijk niet goed gerouteerd.

## <a name="view-front-door-in-action"></a>Front Door in actie zien
Als u een Front Door hebt gemaakt, duurt het een paar minuten voordat de configuratie wereldwijd is geïmplementeerd. Als dat is gebeurd, gaat u naar de front-endhost die u hebt gemaakt. Dit doet u door in een webbrowser de URL `myappfrontend.azurefd.net` op te geven. Uw aanvraag wordt automatisch gerouteerd naar de back-end uit de opgegeven back-ends in de back-endpool die zich het dichtst bij u in de buurt bevindt. 

### <a name="view-front-door-handle-application-failover"></a>Failover van toepassing door Front Door
Als u de direct wereldwijde failover van Front Door in actie wilt zien, gaat u naar een van de websites die u hebt gemaakt en stopt u deze. Op basis van de testinstelling die is gedefinieerd voor de back-endpool, wordt het verkeer direct omgeleid naar de andere website-implementatie. U kunt dit gedrag ook testen door de back-end uit te schakelen in de configuratie van de back-endpool voor uw Front Door. 

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u niet langer nodig bent, verwijdert u de resourcegroepen **myResourceGroupFD1,** **myResourceGroupFD2**en **myResourceGroupFD0:**

## <a name="next-steps"></a>Volgende stappen
In deze snelstart hebt u een Front Door-profiel gemaakt waarmee u verkeer van gebruikers kunt omleiden voor webtoepassingen die een hoge beschikbaarheid en goede prestaties vereisen. Zie [Routeringsmethoden van Front Door](front-door-routing-methods.md) voor meer informatie over het routeren van verkeer.
