---
title: Een voordeur maken met HTTP naar HTTPS-omleiding via de Azure-portal
description: Meer informatie over het maken van een voordeur met omgeleid verkeer van HTTP naar HTTPS met behulp van de Azure-portal.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: b7385ef27cd17705f2c86b6f57d4780511b6935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246854"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Een voordeur maken met HTTP naar HTTPS-omleiding via de Azure-portal

U de Azure-portal gebruiken om een [voordeur](front-door-overview.md) te maken met een certificaat voor SSL-beëindiging. Een routeringsregel wordt gebruikt om HTTP-verkeer om te leiden naar HTTPS.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een voordeur maken met een bestaande Web App-bron
> * Een aangepast domein toevoegen met SSL-certificaat 
> * HTTPS-omleiding instellen op het aangepaste domein

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Een voordeur maken met een bestaande Web App-bron

1. Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .
2. Klik in de linkerbovenhoek van Azure Portal op **Een resource maken**.
3. Zoek naar **voordeur** met de zoekbalk en klik op Maken zodra u het brontype hebt **gevonden.**
4. Kies een abonnement en gebruik vervolgens een bestaande brongroep of maak een nieuwe groep. Opmerking, de locatie die in de gebruikersinterface wordt gevraagd, is alleen voor de resourcegroep. Uw frontdoor-configuratie wordt geïmplementeerd op alle [POP-locaties](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)van Azure Front Door.

    ![Basisbeginselen configureren voor nieuwe voordeur](./media/front-door-url-redirect/front-door-create-basics.png)

5. Klik **op Volgende** om het tabblad configuratie in te voeren. De configuratie voor Front Door gebeurt in drie stappen : het toevoegen van een standaard frontendhost, het toevoegen van backends in een backend-groep en vervolgens routeringsregels maken om het routeringsgedrag voor frontendhost in kaart te brengen. 

     ![Voordeur configuratie ontwerper](./media/front-door-url-redirect/front-door-designer.png)

6. Klik op**+** het pictogram ' ' op de _Frontend-hosts_ om een frontendhost te maken,`\<**name**\>.azurefd.net`voer een wereldwijd unieke naam in voor uw standaard frontendhost voor uw voordeur ( ). Klik **op Toevoegen** om door te gaan naar de volgende stap.

     ![Een frontendhost toevoegen](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Klik op**+** het pictogram ' ' op de _backend-zwembaden_ om een backendpool te maken. Geef een naam op voor de backendpool en klik op **'Voeg een backend toe'.**
8. Selecteer het hosttype backend als _app-service_. Selecteer het abonnement waarbij uw web-app wordt gehost en selecteer vervolgens de specifieke web-app in de vervolgkeuzelijst voor **Backend-hostnaam**.
9. Klik **op Toevoegen** om de backend op te slaan en klik nogmaals om de backendpoolconfig op te slaan. **Add**   ![Een backend toevoegen in een backendpool](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Klik op**+** het pictogram ' ' op de _routeringsregels_ om een route te maken. Geef een naam op voor de route, zeg 'HttpToHttpsRedirect' en stel het veld _Geaccepteerde protocollen_ in op **'alleen HTTP'.** Controleer of de juiste _frontendhost_ is geselecteerd.  
11. Stel in de sectie _Routedetails_ het _routetype_ in op **Omleiding**, controleer of het _type Omleiding_ is ingesteld op **Gevonden (302)** en _het protocol Redirect_ is ingesteld op **alleen HTTPS**. 
12. Klik op Toevoegen om de routeringsregel voor HTTP op te slaan in HTTPS-omleiding.
     ![Een HTTP-omleidingsroute toevoegen aan HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Voeg een andere routeringsregel toe voor het verwerken van het HTTPS-verkeer. Klik op**+** het ' teken op de _routeringsregels_ en geef een naam op voor de route, zeg 'DefaultForwardingRoute' en stel het veld _Geaccepteerde protocollen_ in op **'alleen HTTPS'.** Controleer of de juiste _frontendhost_ is geselecteerd.
14. Stel in de sectie Routedetails het _routetype_ in **op Doorsturen,** zorg ervoor dat de backendpool rechts is geselecteerd en het _doorsturenprotocol_ is **ingesteld op alleen HTTPS**. 
15. Klik op Toevoegen om de routeringsregel op te slaan voor het doorsturen van aanvragen.
     ![Een doorstuurroute toevoegen voor HTTPS-verkeer](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Klik **op Controleren + maken** en vervolgens **maken**om uw voordeurprofiel te maken. Ga naar de bron die is gemaakt.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Een aangepast domein toevoegen aan uw voordeur en HTTPS inschakelen
In de volgende stappen wordt uitgelegd hoe u een aangepast domein toevoegen aan een bestaande Front Door-bron en vervolgens HTTP inschakelen voor HTTPS-omleiding. 

### <a name="add-a-custom-domain"></a>Een aangepast domein toevoegen

In dit voorbeeld voegt u een `www` CNAME-record toe `www.contosonews.com`voor het subdomein (bijvoorbeeld ).

#### <a name="create-the-cname-record"></a>Het CNAME-record maken

Voeg een CNAME-record toe om een subdomein in kaart`<name>.azurefd.net`te `<name>` brengen aan de standaard frontendhost van uw voordeur ( , waar is de naam van uw voordeurprofiel).

Voeg `www.contoso.com` bijvoorbeeld voor het domein een CNAME-record `www` toe `<name>.azurefd.net`waaraan de naam wordt toegewezen aan .

Nadat u de CNAME toevoegt, lijkt de pagina DNS-records op het volgende voorbeeld:

![CNAME aangepast domein naar Voordeur](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Aan boord van het aangepaste domein op uw voordeur

1. Klik op het tabblad Voordeurontwerper op het pictogram '+' in de sectie Frontend hosts om een nieuw aangepast domein toe te voegen. 
2. Voer de volledig gekwalificeerde aangepaste DNS-naam in `www.contosonews.com`het veld aangepaste hostnaam in, bijvoorbeeld . 
3. Zodra de CNAME-toewijzing van het domein naar uw voordeur is gevalideerd, klikt u op **Toevoegen** om het aangepaste domein toe te voegen.
4. Klik **op Opslaan** om de wijzigingen in te dienen.

![Menu voor aangepaste domeinen](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>HTTPS inschakelen op uw aangepaste domein

1. Klik op het aangepaste domein dat is toegevoegd en wijzig onder de sectie **Aangepast domein HTTPS**de status in **Ingeschakeld**.
2. U het **type certificaatbeheer** op _Front Door_ laten beheren voor het gratis certificaat dat door De voordeur wordt onderhouden, beheerd en automatisch wordt geroteerd. U er ook voor kiezen om uw eigen aangepaste SSL-certificaat te gebruiken dat is opgeslagen met Azure Key Vault. In deze zelfstudie wordt ervan uitgegaan dat het gebruik van door de voordeur beheerd certificaat.
![HTTPS inschakelen voor aangepast domein](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Klik op **Bijwerken** om de selectie op te slaan en klik vervolgens op **Opslaan**.
4. Klik na een paar minuten op **Vernieuwen** en klik vervolgens opnieuw op het aangepaste domein om de voortgang van certificaatinrichting te bekijken. 

> [!WARNING]
> Het inschakelen van HTTPS voor een aangepast domein kan enkele minuten duren en is ook afhankelijk `<name>.azurefd.net`van de validatie van het domeineigendom als de CNAME niet rechtstreeks is toegewezen aan uw Front Door-host. Meer informatie over [het inschakelen van HTTPS voor een aangepast domein](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>De routeringsregels voor het aangepaste domein configureren

1. Klik op de eerder gemaakte omleidingsregel.
2. Klik op de vervolgkeuzelijst voor Frontend-hosts en selecteer uw aangepaste domein om deze route ook voor uw domein toe te passen.
3. Klik op **Update**.
4. Doe dezelfde bewerking ook voor de andere routeringsregel, dat wil zeggen dat uw doorstuurroute het aangepaste domein toevoegt.
5. Klik **op Opslaan** om uw wijzigingen in te dienen.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
- Meer informatie over [URL-omleiding op Voordeur](front-door-url-redirect.md).
