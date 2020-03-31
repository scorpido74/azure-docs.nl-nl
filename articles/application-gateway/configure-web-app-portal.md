---
title: Verkeer naar apps met meerdere tenant's beheren met behulp van de portal
titleSuffix: Azure Application Gateway
description: In dit artikel vindt u richtlijnen voor het configureren van Web-apps voor Azure App-service als leden in backendpool op een bestaande of nieuwe toepassingsgateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075166"
---
# <a name="configure-app-service-with-application-gateway"></a>App-service configureren met Application Gateway

Aangezien app-service een multi-tenantservice is in plaats van een implementatie, gebruikt deze hostheader in het binnenkomende verzoek om het verzoek op te lossen naar het juiste eindpunt van de app-service. Meestal is de DNS-naam van de toepassing, die op zijn beurt de DNS-naam is die is gekoppeld aan de toepassingsgateway voor de app-service, anders dan de domeinnaam van de backend-app-service. Daarom is de hostheader in de oorspronkelijke aanvraag die door de toepassingsgateway is ontvangen, niet dezelfde als de hostnaam van de backendservice. Hierdoor kunnen de back-ends van de multitenant back-ends het verzoek naar het juiste eindpunt niet oplossen, tenzij de hostheader in de aanvraag van de toepassingsgateway naar de backend wordt gewijzigd in de hostnaam van de backendservice.

Application Gateway biedt `Pick host name from backend address` een schakelaar die de hostheader in de aanvraag overschrijft met de hostnaam van de back-end wanneer het verzoek wordt doorgestuurd van de Application Gateway naar de backend. Met deze mogelijkheid u ondersteuning bieden voor back-ends met meerdere tenant's, zoals Azure-app-service en API-beheer. 

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> - Een backendpool maken en er een app-service aan toevoegen
> - HTTP-instellingen en aangepaste sonde maken met de switches Hostname kiezen ingeschakeld

## <a name="prerequisites"></a>Vereisten

- Toepassingsgateway: als u geen bestaande toepassingsgateway hebt, raadpleegt u hoe [u een toepassingsgateway maakt](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- App-service: Als u geen bestaande App-service hebt, raadpleegt u [App-servicedocumentatie](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>App-service toevoegen als backendpool

1. Open in de Azure-portal de configuratieweergave van uw toepassingsgateway.

2. Klik onder **Backend-groepen**op **Toevoegen** om een nieuwe backendpool te maken.

3. Geef een geschikte naam aan de backend pool. 

4. Klik **onder Doelen**op de vervolgkeuzelijst en kies App **Services** als optie.

5. Er verschijnt een vervolgkeuzelijst direct onder de vervolgkeuzelijst **Doelen** die een lijst met uw App-services bevat. Kies in deze vervolgkeuzelijst de appservice die u als backendpoollid wilt toevoegen en klik op Toevoegen.

   ![Backend van app-service](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > De vervolgkeuzelijst vult alleen de app-services die zich in hetzelfde abonnement bevinden als uw Application Gateway. Als u een app-service wilt gebruiken die een ander abonnement heeft dan de app-gateway, kiest u in plaats van **App-services** te kiezen in de **vervolgkeuzelijst Doelen,** kiest u **IP-adres of hostnaam** optie en voert u de hostnaam in (bijvoorbeeld. azurewebsites.net) van de app-service.

## <a name="create-http-settings-for-app-service"></a>HTTP-instellingen voor app-service maken

1. Klik **onder HTTP-instellingen**op **Toevoegen** om een nieuwe HTTP-instelling te maken.

2. Voer een naam in voor de HTTP-instelling en u cookiegebaseerde affiniteit in- of uitschakelen volgens uw vereiste.

3. Kies het protocol als HTTP of HTTPS volgens uw use case. 

   > [!NOTE]
   > Als u HTTPS selecteert, hoeft u geen verificatiecertificaat of vertrouwd rootcertificaat te uploaden om de back-end van de app-service op de witte lijst te krijgen, omdat app-service een vertrouwde Azure-service is.

4. Schakel het selectievakje voor **Gebruik voor App-service** in. Houd er `Create a probe with pick host name from backend address` rekening `Pick host name from backend address` mee dat de schakelaars automatisch worden ingeschakeld.`Pick host name from backend address` overschrijft de hostheader in de aanvraag met de hostnaam van de back-end wanneer het verzoek wordt doorgestuurd van de Application Gateway naar de backend.  

   `Create a probe with pick host name from backend address`maakt automatisch een statussonde en koppelt deze aan deze HTTP-instelling. U hoeft geen andere statussonde sonde voor deze HTTP-instelling te maken. U controleren of een <HTTP Setting name> <Unique GUID> nieuwe sonde met de naam is toegevoegd `Pick host name from backend http settings enabled`in de lijst van gezondheid sondes en het heeft al de schakelaar.

   Als u al een of meer HTTP-instellingen hebt die worden gebruikt voor de App-service en als deze HTTP-instellingen `Create a probe with pick host name from backend address` hetzelfde protocol gebruiken als het protocol dat u gebruikt in de instelling die u maakt, krijgt u in plaats van de switch een vervolgkeuzelijst om een van de aangepaste sondes te selecteren. Dit komt omdat er al een HTTP-instelling met app-service bestaat, zou `Pick host name from backend http settings enabled` er dus ook een gezondheidssonde bestaan die de schakelaar heeft. Kies die aangepaste sonde in de vervolgkeuzelijst.

5. Klik op **OK** om de HTTP-instelling te maken.

   ![HTTP-instelling1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-instelling2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Regel maken om de instelling Listener, Backend Pool en HTTP vast te binden

1. Klik **onder Regels**op **Basisom** een nieuwe basisregel te maken.

2. Geef een geschikte naam op en selecteer de luisteraar die de inkomende aanvragen voor de App-service accepteert.

3. Kies in de vervolgkeuzelijst **Backend** pool de backendpool die u hierboven hebt gemaakt.

4. Kies in de vervolgkeuzelijst **HTTP-instelling** de http-instelling die u hierboven hebt gemaakt.

5. Klik op **OK** om deze regel op te slaan.

   ![Regel](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Aanvullende configuratie in geval van omleiding naar het relatieve pad van de app-service

Wanneer de app-service een omleidingsreactie naar de client stuurt om om te leiden naar het relatieve pad (bijvoorbeeld een omleiding van contoso.azurewebsites.net/path1 naar contoso.azurewebsites.net/path2), gebruikt deze dezelfde hostnaam in de locatiekop van de reactie als die in het verzoek dat is ontvangen van de toepassingsgateway. Dus de client zal het verzoek rechtstreeks aan contoso.azurewebsites.net/path2 in plaats van te gaan via de applicatie gateway (contoso.com/path2). Het omzeilen van de toepassingsgateway is niet wenselijk.

Als er in uw gebruiksvoorbeeld scenario's zijn waarin de app-service een omleidingsreactie naar de client moet verzenden, de extra stappen uitvoeren [om de locatiekoptekst te herschrijven.](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)

## <a name="restrict-access"></a>Toegang beperken

De web-apps die in deze voorbeelden worden geïmplementeerd, gebruiken openbare IP-adressen die rechtstreeks vanaf internet toegankelijk zijn. Dit helpt bij het oplossen van problemen wanneer u meer te weten komt over een nieuwe functie en nieuwe dingen probeert. Maar als u van plan bent een functie in productie te nemen, wilt u meer beperkingen toevoegen.

Een manier waarop u de toegang tot uw web-apps beperken, is door [statische IP-beperkingen](../app-service/app-service-ip-restrictions.md)van Azure App Service te gebruiken. U bijvoorbeeld de web-app beperken zodat deze alleen verkeer van de toepassingsgateway ontvangt. Gebruik de IP-beperkingsfunctie voor app-service om de VIP-gateway van de toepassing als het enige adres met toegang te vermelden.

## <a name="next-steps"></a>Volgende stappen

Zie ondersteuning voor ondersteuning met [meerdere tenant-toepassingen met toepassingsgateway voor](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)meer informatie over de app-service en andere ondersteuning met meerdere tenants.
