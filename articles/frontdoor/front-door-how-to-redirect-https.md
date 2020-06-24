---
title: Maak een voor deur met HTTP-naar-HTTPS-omleiding met behulp van de Azure Portal
description: Meer informatie over het maken van een voor deur met omgeleid verkeer van HTTP naar HTTPS met behulp van de Azure Portal.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 05699c827af251b890de4c6f195df5872bfbe364
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84743606"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Maak een voor deur met HTTP-naar-HTTPS-omleiding met behulp van de Azure Portal

U kunt de Azure Portal gebruiken om een [voor deur](front-door-overview.md) te maken met een certificaat voor het beëindigen van TLS. Een routerings regel wordt gebruikt om HTTP-verkeer om te leiden naar HTTPS.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een voor deur maken met een bestaande web app-resource
> * Een aangepast domein met TLS/SSL-certificaat toevoegen 
> * HTTPS-omleiding instellen voor het aangepaste domein

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Een voor deur maken met een bestaande web app-resource

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Klik in de linkerbovenhoek van Azure Portal op **Een resource maken**.
3. Zoek naar de voor **deur** met de zoek balk en klik op **maken**zodra u het resource type hebt gevonden.
4. Kies een abonnement en gebruik vervolgens een bestaande resource groep of maak een nieuwe. Opmerking: de locatie die in de gebruikers interface wordt gevraagd, is alleen voor de resource groep. De configuratie van uw front-deur wordt geïmplementeerd op alle POP-locaties van de [Azure front deur](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    ![Basis principes configureren voor nieuwe front-deur](./media/front-door-url-redirect/front-door-create-basics.png)

5. Klik op **volgende** om het tabblad Configuratie in te voeren. De configuratie voor de voor deur gebeurt in drie stappen: het toevoegen van een standaard frontend-host, het toevoegen van back-endservers in een back-end-groep en het maken van routerings regels om het routerings gedrag voor de frontend-host toe te wijzen. 

     ![Configuratie ontwerp voor de voor deur](./media/front-door-url-redirect/front-door-designer.png)

6. Klik op het **+** pictogram op de _frontend-hosts_ om een frontend-host te maken, voer een wereld wijd unieke naam in voor de standaard-frontend-host voor uw front deur ( `\<**name**\>.azurefd.net` ). Klik op **toevoegen** om door te gaan naar de volgende stap.

     ![Een frontend-host toevoegen](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Klik op het **+** pictogram ' ' op de _back-endservers_ om een back-end-groep te maken. Geef een naam op voor de back-end-pool en klik vervolgens op**een back-end toevoegen**.
8. Selecteer het type back-end als _app service_. Selecteer het abonnement waarin uw web-app wordt gehost en selecteer vervolgens de specifieke Web-app in de vervolg keuzelijst voor de **back-end-hostnaam**.
9. Klik op **toevoegen** om de back-end op te slaan en klik nogmaals op **toevoegen** om de back-end-pool configuratie op te slaan.   ![Een back-end toevoegen aan een back-end-groep](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Klik op het **+** pictogram in de _routerings regels_ om een route te maken. Geef een naam op voor de route, zeg ' HttpToHttpsRedirect ' en stel het veld _geaccepteerde protocollen_ vervolgens in op **' http only '**. Zorg ervoor dat de juiste _frontend-host_ is geselecteerd.  
11. Stel in het gedeelte _route Details_ het _route type_ in op **omleiden**, Controleer of _het type omleiding_ is ingesteld op **gevonden (302)** en het _omleidings protocol_ is ingesteld op **alleen https**. 
12. Klik op toevoegen om de routerings regel voor HTTP-naar-HTTPS-omleiding op te slaan.
     ![Een HTTP-naar-HTTPS-omleidings route toevoegen](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Voeg nog een regel voor route ring toe voor het verwerken van het HTTPS-verkeer. Klik op het ' **+** '-aanmeldings _regels_ en geef een naam op voor de route, zeg ' DefaultForwardingRoute ' en stel het veld _geaccepteerde protocollen_ vervolgens in op **' https only '**. Zorg ervoor dat de juiste _frontend-host_ is geselecteerd.
14. Stel in het gedeelte route Details het _route type_ in op **door sturen**, zorg ervoor dat de juiste back-end-pool is geselecteerd en dat het _doorstuur protocol_ alleen is ingesteld op **https**. 
15. Klik op toevoegen om de routerings regel voor het door sturen van aanvragen op te slaan.
     ![Een doorstuur route voor HTTPS-verkeer toevoegen](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Klik op **beoordeling + maken** en vervolgens op **maken**om het profiel voor de voor deur te maken. Ga naar de resource nadat deze is gemaakt.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Een aangepast domein toevoegen aan uw voor deur en HTTPS inschakelen
De volgende stappen laten zien hoe u een aangepast domein kunt toevoegen aan een bestaande front-deur resource en vervolgens HTTP kunt omleiden naar HTTPS-omleiding. 

### <a name="add-a-custom-domain"></a>Een aangepast domein toevoegen

In dit voor beeld voegt u een CNAME-record toe voor het `www` subdomein (bijvoorbeeld `www.contosonews.com` ).

#### <a name="create-the-cname-record"></a>Het CNAME-record maken

Voeg een CNAME-record toe om een subdomein toe te wijzen aan de standaard frontend-host van de front deur ( `<name>.azurefd.net` , waarbij `<name>` de naam is van het voorste deur profiel).

Voeg voor het `www.contoso.com` domein, als voor beeld, een CNAME-record toe die de naam toewijst `www` aan `<name>.azurefd.net` .

Nadat u de CNAME toevoegt, lijkt de pagina DNS-records op het volgende voorbeeld:

![Aangepast domein van CNAME naar voor deur](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Het aangepaste domein onboarden op de voor deur

1. Klik op het tabblad front deur Designer op het pictogram ' + ' op de sectie frontend-hosts om een nieuw aangepast domein toe te voegen. 
2. Voer de volledig gekwalificeerde aangepaste DNS-naam in het veld aangepaste hostnaam in, bijvoorbeeld `www.contosonews.com` . 
3. Zodra de CNAME-toewijzing van het domein naar uw voor deur is gevalideerd, klikt u op **toevoegen** om het aangepaste domein toe te voegen.
4. Klik op **Opslaan** om de wijzigingen te verzenden.

![Menu voor aangepaste domeinen](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>HTTPS inschakelen voor uw aangepaste domein

1. Klik op het aangepaste domein dat is toegevoegd en klik onder de sectie HTTPS van het **aangepaste domein**, wijzig de status in **ingeschakeld**.
2. U kunt het **type certificaat beheer** dat is ingesteld op de _voor deur_ , voor het gratis certificaat dat wordt beheerd, beheerd en automatisch gedraaid door de voor deur, laten staan. U kunt er ook voor kiezen om uw eigen aangepaste TLS/SSL-certificaat te gebruiken dat is opgeslagen met Azure Key Vault. In deze zelf studie wordt ervan uitgegaan dat het gebruik van de voor deur beheerd certificaat.
![HTTPS inschakelen voor een aangepast domein](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Klik op **bijwerken** om de selectie op te slaan en klik vervolgens op **Opslaan**.
4. Klik na een paar minuten op **vernieuwen** en klik vervolgens nogmaals op het aangepaste domein om de voortgang van het inrichten van het certificaat te bekijken. 

> [!WARNING]
> Het inschakelen van HTTPS voor een aangepast domein kan enkele minuten duren en is ook afhankelijk van de validatie van domein eigendom als de CNAME niet rechtstreeks is toegewezen aan de voor deur van de host `<name>.azurefd.net` . Meer informatie over [het inschakelen van HTTPS voor een aangepast domein](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>De routerings regels voor het aangepaste domein configureren

1. Klik op de regel voor omleidings routering die u eerder hebt gemaakt.
2. Klik op de vervolg keuzelijst voor frontend-hosts en selecteer uw aangepaste domein om deze route ook toe te passen voor uw domein.
3. Klik op **Update**.
4. Doe dezelfde bewerking voor de andere routerings regel, en dat wil zeggen, voor uw doorstuur route om het aangepaste domein toe te voegen.
5. Klik op **Opslaan** om uw wijzigingen te verzenden.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
- Meer informatie over [URL-omleiding op de voor deur](front-door-url-redirect.md).
