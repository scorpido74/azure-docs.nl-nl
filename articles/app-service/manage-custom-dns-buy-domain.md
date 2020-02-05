---
title: Een aangepaste domein naam kopen
description: Meer informatie over het kopen van een App Service domein en het gebruiken als aangepast domein voor uw app Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: afb40d0f3681bc02351e43166fccfaafe7741128
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023409"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Een aangepaste domein naam voor Azure App Service kopen

App Service domeinen zijn domeinen op het hoogste niveau die rechtstreeks in Azure worden beheerd. Ze maken het eenvoudig om aangepaste domeinen voor [Azure app service](overview.md)te beheren. Deze zelf studie laat zien hoe u een App Service domein kunt kopen en DNS-namen kunt toewijzen aan Azure App Service.

Zie [app service domein toewijzen aan Azure VM of Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage)voor Azure vm of Azure Storage. Zie voor Cloud Services [een aangepaste domein naam configureren voor een Azure-Cloud service](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* [Maak een App Service-app](/azure/app-service/), of gebruik een app die u hebt gemaakt voor een andere zelfstudie.
* [Verwijder de bestedings limiet voor uw abonnement](../cost-management-billing/manage/spending-limit.md#remove). U kunt geen App Service domeinen kopen met gratis abonnements tegoeden.

## <a name="prepare-the-app"></a>De app voorbereiden

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Als u aangepaste domeinen in Azure App Service wilt gebruiken, moet het [app service plan](https://azure.microsoft.com/pricing/details/app-service/) van uw app een betaalde laag zijn (**gedeeld**, **Basic**, **Standard**of **Premium**). In deze stap zorgt u ervoor dat de app zich in de ondersteunde prijs categorie bevindt.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open de [Azure-portal](https://portal.azure.com) en meld u aan met uw Azure-account.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navigeer naar de app in de Azure portal

Selecteer in het linkermenu **App Services** en selecteer de naam van uw app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/select-app.png)

U ziet de beheerpagina van de App Service-app.  

### <a name="check-the-pricing-tier"></a>Controleer de prijscategorie

Schuif in de navigatiebalk links van de app-pagina naar de sectie **Instellingen** en selecteer **Opschalen (App Service-plan)** .

![Menu Opschalen](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

De huidige laag van de app wordt gemarkeerd door een blauwe rand. Controleer of de app zich niet in de **F1**-laag bevindt. Aangepaste DNS wordt niet ondersteund in de **F1**-laag. 

![Controleer prijscategorie](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Als het App Service plan zich niet in de **F1** -laag bevindt, sluit u de pagina **opschalen** en gaat u verder met [het kopen van het domein](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Het App Service-plan opschalen

Selecteer een van de lagen die niet gratis zijn (**D1**, **B1**, **B2**, **B3** of een laag in de categorie **Productie**). Klik op **Aanvullende opties bekijken** voor aanvullende opties.

Klik op **Toepassen**.

![Controleer prijscategorie](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Wanneer u de volgende melding ziet, is de schaalbewerking voltooid.

![Schaalbewerking bevestigen](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Het domein kopen

### <a name="pricing-information"></a>Prijs informatie
Ga voor prijs informatie over Azure App Service domeinen naar de [pagina met app service prijzen](https://azure.microsoft.com/pricing/details/app-service/windows/) en schuif omlaag naar app service domein.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Open de [Azure-portal](https://portal.azure.com/) en meld u aan met uw Azure-account.

### <a name="launch-buy-domains"></a>Kopen van domeinen starten
Klik op het tabblad **app Services** op de naam van uw app, selecteer **instellingen**en selecteer vervolgens **aangepaste domeinen**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Klik op de pagina **aangepaste domeinen** op **domein kopen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Als u de sectie **app service domeinen** niet kunt zien, moet u de bestedings limiet voor uw Azure-account verwijderen (Zie [vereisten](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>De domein aankoop configureren

Typ op de pagina **app service domein** in het vak **zoeken naar domein** de naam van het domein dat u wilt kopen en typ `Enter`. De voorgestelde beschik bare domeinen worden precies onder het tekstvak weer gegeven. Selecteer een of meer domeinen die u wilt kopen.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> De volgende [domeinen op het hoogste niveau](https://wikipedia.org/wiki/Top-level_domain) worden ondersteund door app service domeinen: _com_, _net_, _co.uk_, _org_, _nl_, in _,_ _org.uk_en _co.in_.
>
>

Klik op de **contact gegevens** en vul het formulier contact gegevens van het domein in. Wanneer u klaar bent, klikt u op **OK** om terug te keren naar de pagina app service domein.

Het is belang rijk dat u alle verplichte velden zo nauw keurig mogelijk invult. Onjuiste gegevens voor contact gegevens kunnen ertoe leiden dat het kopen van domeinen mislukt.

Selecteer vervolgens de gewenste opties voor uw domein. Zie de volgende tabel voor uitleg:

| Instelling | Voorgestelde waarde | Beschrijving |
|-|-|-|
|Privacy-beveiliging | Inschakelen | Meld u aan als privacy-beveiliging, die in de _gratis_aankoop prijs is opgenomen. Sommige domeinen op het hoogste niveau worden beheerd door registraties die geen ondersteuning bieden voor privacy-bescherming en ze worden vermeld op de pagina **privacy-beveiliging** . |
| Standaard hostnamen toewijzen | **www** en **\@** | Selecteer de gewenste hostnamen bindingen, indien gewenst. Wanneer de aankoop bewerking van het domein is voltooid, kan uw app worden geopend op de geselecteerde hostnamen. Als de app zich achter [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)bevindt, ziet u de optie voor het toewijzen van het hoofd domein (@) niet, omdat Traffic Manager geen records ondersteunt. U kunt wijzigingen aanbrengen in de hostname-toewijzingen nadat de domein aankoop is voltooid. |

### <a name="accept-terms-and-purchase"></a>Voor waarden accepteren en kopen

Klik op **juridische voor waarden** om de voor waarden en kosten te controleren en klik vervolgens op **kopen**.

> [!NOTE]
> App Service domeinen gebruiken GoDaddy voor domein registratie en Azure DNS om de domeinen te hosten. Naast de kosten voor het registreren van het domein, gelden de gebruiks kosten voor Azure DNS. Zie [Azure DNS prijzen](https://azure.microsoft.com/pricing/details/dns/)voor meer informatie.
>
>

Klik op de pagina **app service domein** op **OK**. Terwijl de bewerking wordt uitgevoerd, ziet u de volgende meldingen:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>De hostnamen testen

Als u standaard hostnamen aan uw app hebt toegewezen, ziet u ook een geslaagde melding voor elke geselecteerde hostnaam.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

U ziet ook de geselecteerde hostnamen op de pagina **aangepaste domeinen** , in de sectie **aangepaste hostnamen** .

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Een **niet-beveiligd** label voor uw aangepaste domein betekent dat het nog niet is gebonden aan een SSL-certificaat en dat de HTTPS-aanvraag van een browser naar uw aangepaste domein een fout of waarschuwing krijgt, afhankelijk van de browser. Als u SSL-binding wilt configureren, raadpleegt u [een aangepaste DNS-naam beveiligen met een SSL-binding in azure app service](configure-ssl-bindings.md).
>

Als u de hostnamen wilt testen, gaat u naar de vermelde hostnamen in de browser. In het voor beeld in de vorige scherm afbeelding gaat u naar _kontoso.net_ en _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Hostnamen toewijzen aan de app

Als u ervoor kiest om een of meer standaard-hostnamen niet aan uw app toe te wijzen tijdens het aankoop proces, of als u een hostnaam die niet wordt vermeld, wilt toewijzen, kunt u op elk gewenst moment een hostnaam toewijzen.

U kunt ook hostnamen in het App Service domein toewijzen aan een andere app. De stappen zijn afhankelijk van het feit of het App Service domein en de app tot hetzelfde abonnement behoren.

- Ander abonnement: wijs aangepaste DNS-records uit het App Service domein toe aan de app, zoals een extern aangeschaft domein. Zie [aangepaste DNS-records beheren](#custom)voor meer informatie over het toevoegen van aangepaste DNS-namen aan een app service domein. Als u een extern aangekocht domein wilt toewijzen aan een app, raadpleegt u [een bestaande aangepaste DNS-naam toewijzen aan Azure app service](app-service-web-tutorial-custom-domain.md). 
- Hetzelfde abonnement: gebruik de volgende stappen.

### <a name="launch-add-hostname"></a>Hostnaam van toevoegen starten
Selecteer op de pagina **app Services** de naam van uw app waaraan u hostnamen wilt toewijzen, selecteer **instellingen**en selecteer vervolgens **aangepaste domeinen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Zorg ervoor dat uw aangeschafte domein wordt weer gegeven in de sectie **app service domeinen** , maar Selecteer deze niet. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Alle App Service domeinen in hetzelfde abonnement worden weer gegeven op de pagina **aangepaste domeinen** van de app. Als uw domein zich in het abonnement van de app bevindt, maar u het niet ziet op de pagina **aangepaste domeinen** van de app, kunt u de pagina met **aangepaste domeinen** opnieuw openen of de webpagina vernieuwen. U kunt ook de meldings Bell boven aan het Azure Portal controleren op voortgang of fouten bij het maken.
>
>

Selecteer **Hostnaam toevoegen**.

### <a name="configure-hostname"></a>Hostnaam configureren
Typ in het dialoog venster **hostname toevoegen** de Fully Qualified Domain name van uw app service domein of een subdomein. Bijvoorbeeld:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Wanneer u klaar bent, selecteert u **valideren**. Het hostname-record type wordt automatisch voor u geselecteerd.

Selecteer **Hostnaam toevoegen**.

Wanneer de bewerking is voltooid, ziet u een melding over de successen van de toegewezen hostnaam.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Hostnaam toevoegen sluiten
Wijs op de pagina **hostname toevoegen** een andere hostnaam toe aan uw app, indien gewenst. Wanneer u klaar bent, sluit u de pagina **hostnaam toevoegen** .

U ziet nu de zojuist toegewezen hostnaam (en) op de pagina **aangepaste domeinen** van uw app.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>De hostnamen testen

Navigeer naar de vermelde hostnamen in de browser. In het voor beeld in de vorige scherm afbeelding gaat u naar _ABC.kontoso.net_.

## <a name="renew-the-domain"></a>Het domein vernieuwen

Het App Service domein dat u hebt gekocht, is gedurende één jaar geldig vanaf het moment van aankoop. Het domein is standaard geconfigureerd om automatisch te worden vernieuwd door uw betalings wijze voor het volgende jaar te laden. U kunt de domein naam hand matig verlengen.

Als u automatische verlenging wilt uitschakelen of als u uw domein hand matig wilt verlengen, volgt u de stappen hier.

Klik op het tabblad **app Services** op de naam van uw app, selecteer **instellingen**en selecteer vervolgens **aangepaste domeinen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Selecteer in de sectie **app service domeinen** het domein dat u wilt configureren.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Selecteer **domein vernieuwen**vanuit de linkernavigatiebalk van het domein. Als u het automatisch vernieuwen van uw domein wilt stoppen, selecteert u **uit**en **slaat**u vervolgens op.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Als u uw domein hand matig wilt verlengen, selecteert u **domein vernieuwen**. Deze knop is echter niet actief tot [90 dagen vóór de verval datum van het domein](#when-domain-expires).

Als uw domein is vernieuwd, ontvangt u binnen 24 uur een e-mail melding.

## <a name="when-domain-expires"></a>Wanneer het domein verloopt

Azure behandelt App Service domeinen die verlopen of verlopen zijn als volgt:

* Als automatische verlenging is uitgeschakeld: 90 dagen vóór het verlopen van het domein, wordt er een e-mail bericht voor het vernieuwen van de e-mail naar u verzonden en wordt de knop voor het vernieuwen van het **domein** geactiveerd in de portal.
* Als automatische verlenging is ingeschakeld: op de dag na de verval datum van uw domein probeert Azure u te factureren voor het vernieuwen van de domein naam.
* Als er een fout optreedt tijdens het automatisch verlengen (bijvoorbeeld als uw kaart in het bestand is verlopen) of als automatische verlenging is uitgeschakeld en u toestaat dat het domein verloopt, ontvangt Azure u een melding van het domein verloop en Parken uw domein naam. U kunt uw domein [hand matig verlengen](#renew-the-domain) .
* Op de dag van de vierde en twaalfde dagen na verloop van tijd stuurt Azure u extra e-mail berichten. U kunt uw domein [hand matig verlengen](#renew-the-domain) .
* Op de 19e dag na de verval datum blijft uw domein in de wacht staan, maar wordt het onderhouds tarief in rekening gebracht. U kunt klant ondersteuning aanroepen om uw domein naam te vernieuwen, afhankelijk van eventuele verlengings-en activerings kosten.
* Op de 25e dag na de verval datum brengt Azure uw domein in op veiling met een domeinnaam veiling service. U kunt klant ondersteuning aanroepen om uw domein naam te vernieuwen, afhankelijk van eventuele verlengings-en activerings kosten.
* Op de 30e dag na de verval datum kunt u uw domein niet meer inwisselen.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Aangepaste DNS-records beheren

In Azure worden DNS-records voor een App Service domein beheerd met behulp van [Azure DNS](https://azure.microsoft.com/services/dns/). U kunt DNS-records toevoegen, verwijderen en bijwerken, net als bij een extern aangeschaft domein.

### <a name="open-app-service-domain"></a>App Service domein openen

Selecteer in het menu links in het Azure Portal **alle services** > **app service domeinen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecteer het domein dat u wilt beheren. 

### <a name="access-dns-zone"></a>Toegang tot de DNS-zone

Selecteer **DNS-zone**in het menu links van het domein.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Met deze actie wordt de pagina [DNS-zone](../dns/dns-zones-records.md) van uw app service domein in azure DNS geopend. Zie [DNS-zones beheren in de Azure Portal](../dns/dns-operations-dnszones-portal.md)voor meer informatie over het bewerken van DNS-records.

## <a name="cancel-purchase-delete-domain"></a>Aankoop annuleren (domein verwijderen)

Nadat u het App Service-domein hebt aangeschaft, hebt u vijf dagen de aankoop voor een volledige terugbetaling te annuleren. Na vijf dagen kunt u het App Service domein verwijderen, maar kan er geen restitutie worden ontvangen.

### <a name="open-app-service-domain"></a>App Service domein openen

Selecteer in het menu links in het Azure Portal **alle services** > **app service domeinen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecteer het domein dat u wilt annuleren of verwijderen. 

### <a name="delete-hostname-bindings"></a>Hostname-bindingen verwijderen

Selecteer in het menu links **hostnamen bindingen**. De hostname-bindingen van alle Azure-Services worden hier weer gegeven.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

U kunt het App Service domein pas verwijderen als alle hostnamen bindingen zijn verwijderd.

Verwijder elke hostnaam binding door **...**  > **verwijderen**te selecteren. Nadat alle bindingen zijn verwijderd, selecteert u **Opslaan**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Annuleren of verwijderen

Selecteer **overzicht**in het menu links van het domein. 

Als de annulerings periode op het aangeschafte domein niet is verstreken, selecteert u **aankoop annuleren**. Als dat niet het geval is, ziet u in plaats daarvan de knop **verwijderen** . Als u het domein zonder restitutie wilt verwijderen, selecteert u **verwijderen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Selecteer **Ja**om de bewerking te bevestigen.

Nadat de bewerking is voltooid, wordt het domein vrijgegeven uit uw abonnement en kan iedereen het opnieuw aanschaffen. 

## <a name="direct-default-url-to-a-custom-directory"></a>Standaard-URL naar een aangepaste map omleiden

Standaard stuurt App Service webaanvragen naar de hoofdmap van uw app-code. Als u ze wilt omleiden naar een submap, zoals `public`, raadpleegt u [de standaard-URL voor een aangepaste map](app-service-web-tutorial-custom-domain.md#virtualdir).
