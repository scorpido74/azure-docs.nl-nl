---
title: 'Quick Start: profiel voor voor deur maken voor hoge Beschik baarheid van toepassingen'
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
ms.openlocfilehash: 67a4f9eb3290ba09a2c19325464cf7ad224856e7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184520"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Snelstart: een Front Door maken voor een webtoepassing met hoge beschikbaarheid

In deze snelstart wordt beschreven hoe u een Front Door-profiel maakt dat hoge beschikbaarheid en uitstekende prestaties biedt voor uw webtoepassing. 

Het scenario dat in deze snelstart wordt beschreven, beschrijft twee exemplaren van een webtoepassing die in verschillende Azure-regio's wordt uitgevoerd. Er wordt een configuratie van Front Door gemaakt op basis van [back-ends met dezelfde weging en prioriteit](front-door-routing-methods.md) die helpt om verkeer van gebruikers om te leiden naar de dichtstbijzijnde set site-back-ends waarop de toepassing wordt uitgevoerd. De weboepassing wordt continu gecontroleerd door Front Door en de service biedt automatische failover naar de eerstvolgende beschikbare back-end als de dichtstbijzijnde site niet beschikbaar is.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 
Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="prerequisites"></a>Vereisten
Voor deze snelstart moeten twee exemplaren van een webtoepassing worden geïmplementeerd, die in verschillende Azure-regio's wordt uitgevoerd (*US - oost* en *Europa - west*). Beide exemplaren van de webtoepassing worden uitgevoerd in de modus Active/Active, wat inhoudt dat een van beide exemplaren op elk gewenst moment verkeer kan accepteren, in tegenstelling tot een configuratie Active/Stand-By waarbij het ene exemplaar fungeert als een failover.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Web** > **Web-app** > **Maken**.
2. Voer in **Web-app** de volgende gegevens in (of selecteer ze) en voer standaardinstellingen in waar ze niet zijn opgegeven:

     | Instelling         | Waarde     |
     | ---              | ---  |
     | Naam           | Voer een unieke naam voor de web-app in  |
     | Resourcegroep          | Selecteer **Nieuw** en typ *myResourceGroupFD1* |
     | App Service-plan/-locatie         | Selecteer **Nieuw**.  Voer in het App Service-plan *myAppServicePlanEastUS* in en selecteer **OK**. 
     |      Locatie  |   VS - oost        |
    |||

3. Selecteer **Maken**.
4. Er wordt een standaardwebsite gemaakt als de web-app is geïmplementeerd.
5. Herhaal stap 1 tot en met 3 om een tweede website te maken in een andere Azure-regio met de volgende instellingen:

     | Instelling         | Waarde     |
     | ---              | ---  |
     | Naam           | Voer een unieke naam voor de web-app in  |
     | Resourcegroep          | Selecteer **Nieuw** en typ *myResourceGroupFD2* |
     | App Service-plan/-locatie         | Selecteer **Nieuw**.  Voer in het App Service-plan *myAppServicePlanWestEurope* in en selecteer **OK**. 
     |      Locatie  |   Europa - west      |
    |||


## <a name="create-a-front-door-for-your-application"></a>Een Front Door maken voor uw toepassing
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Een front-end-host toevoegen voor Front Door
Maak een Front Door-configuratie die verkeer van gebruikers omleid op basis van de laagste latentie tussen de twee back-ends.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Front Door** > **Maken**.
2. Begin in het scherm **Een Front Door maken** met het toevoegen van de basisgegevens en geef een abonnement op waarvoor u de Front Door wilt configureren. Net als bij andere Azure-resources moet u een resourcegroep opgeven, plus een regio als u een nieuwe resourcegroep maakt. Ten slotte moet u een naam voor de Front Door opgeven.
3. Zodra de basisgegevens zijn ingevuld, is de volgende stap het definiëren van de **front-endhost** voor de configuratie. Het resultaat moet een geldige domeinnaam zijn, zoals `myappfrontend.azurefd.net`. Deze hostnaam moet globaal uniek zijn. Deze controle wordt echter uitgevoerd door Front Door. 

### <a name="b-add-application-backend-and-backend-pools"></a>B. Toepassings-back-end en back-endpools toevoegen

Vervolgens moet u uw toepassings-back-end(s) configureren in een back-endpool, zodat Front Door weet waar uw toepassing zich bevindt. 

1. Klik op het pictogram '+' voor het toevoegen van een back-endpool en geef vervolgens een **naam** op voor uw back-endpool, bijvoorbeeld `myBackendPool`.
2. Klik vervolgens op Back-ends toevoegen om de websites toe te voegen die u eerder hebt gemaakt.
3. Stel **Type doelhost** in op 'App Service', selecteer het abonnement waarin u de website hebt gemaakt en kies vervolgens de eerste website bij **Naam doelhost**, dus *myAppServicePlanEastUS.azurewebsites.net*.
4. Laat de overige velden voorlopig ongewijzigd en klik op **Toevoegen**.
5. Herhaal stappen 2 - 4 om de andere website toe te voegen, namelijk *myAppServicePlanWestEurope.azurewebsites.net*
6. U kunt eventueel de instellingen voor tests en load balancing bijwerken voor de back-endpool, maar de standaardwaarden moeten voldoen. Klik op **Add**.


### <a name="c-add-a-routing-rule"></a>C. Een routeringsregel toevoegen
Klik als laatste op het pictogram '+' voor Routeringsregels om van een regel voor doorsturen te configureren. Dit is nodig om de front-end-host te koppelen aan de back-endpool. Wat u doet, is instellen dat een aanvraag afkomstig van `myappfrontend.azurefd.net` wordt doorgestuurd naar de back-endpool `myBackendPool`. Klik op **Toevoegen** om de regel voor doorsturen toe te voegen voor de Front Door. U kunt de Front Door nu maken als het goed is, dus klik op **Beoordelen en maken**.

>[!WARNING]
> U **moet** ervoor zorgen dat elk van de front-endhosts in de Front Door is geconfigureerd met een regel voor doorsturen waaraan een standaardpad ('/\*') is gekoppeld. Dat wil zeggen dat er ten minste één regel voor doorsturen moet zijn voor elk van de front-endhosts die is gedefinieerd in het standaardpad ('/\*'). Als u dit niet doet, wordt uw eindgebruikersverkeer mogelijk niet goed gerouteerd.

## <a name="view-front-door-in-action"></a>Front Door in actie zien
Als u een Front Door hebt gemaakt, duurt het een paar minuten voordat de configuratie wereldwijd is geïmplementeerd. Als dat is gebeurd, gaat u naar de front-endhost die u hebt gemaakt. Dit doet u door in een webbrowser de URL `myappfrontend.azurefd.net` op te geven. Uw aanvraag wordt automatisch gerouteerd naar de back-end uit de opgegeven back-ends in de back-endpool die zich het dichtst bij u in de buurt bevindt. 

### <a name="view-front-door-handle-application-failover"></a>Failover van toepassing door Front Door
Als u de direct wereldwijde failover van Front Door in actie wilt zien, gaat u naar een van de websites die u hebt gemaakt en stopt u deze. Op basis van de testinstelling die is gedefinieerd voor de back-endpool, wordt het verkeer direct omgeleid naar de andere website-implementatie. U kunt dit gedrag ook testen door de back-end uit te schakelen in de configuratie van de back-endpool voor uw Front Door. 

## <a name="clean-up-resources"></a>Resources opschonen
Verwijder de resourcegroepen, de webtoepassingen en alle gerelateerde resources als u ze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen
In deze snelstart hebt u een Front Door-profiel gemaakt waarmee u verkeer van gebruikers kunt omleiden voor webtoepassingen die een hoge beschikbaarheid en goede prestaties vereisen. Zie [Routeringsmethoden van Front Door](front-door-routing-methods.md) voor meer informatie over het routeren van verkeer.