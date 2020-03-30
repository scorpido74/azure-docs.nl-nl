---
title: Een aangepaste domeinnaam kopen
description: Meer informatie over het kopen van een App Service-domein en het gebruiken als een aangepast domein voor uw app Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: afb40d0f3681bc02351e43166fccfaafe7741128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259277"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Aangepaste domeinnaam voor Azure App Service kopen

App Service-domeinen zijn domeinen op het hoogste niveau die rechtstreeks in Azure worden beheerd. Hiermee u eenvoudig aangepaste domeinen beheren voor [Azure App Service.](overview.md) In deze zelfstudie ziet u hoe u een App Service-domein koopt en DNS-namen toewijst aan Azure App Service.

Zie [App Service-domein toewijzen aan Azure VM of Azure Storage voor](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage)Azure VM of Azure Storage. Zie [Een aangepaste domeinnaam configureren voor een Azure-cloudservice voor](../cloud-services/cloud-services-custom-domain-name-portal.md)Cloud Services.

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* [Maak een App Service-app](/azure/app-service/), of gebruik een app die u hebt gemaakt voor een andere zelfstudie.
* [Verwijder de bestedingslimiet voor uw abonnement.](../cost-management-billing/manage/spending-limit.md#remove) U geen App Service-domeinen kopen met gratis abonnementskredieten.

## <a name="prepare-the-app"></a>De app voorbereiden

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Als u aangepaste domeinen wilt gebruiken in Azure App Service, moet het [App Service-abonnement](https://azure.microsoft.com/pricing/details/app-service/) van uw app een betaalde laag zijn **(Gedeeld**, **Basic,** **Standaard**of **Premium).** In deze stap zorgt u ervoor dat de app zich in de ondersteunde prijscategorie bevindt.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open de [Azure-portal](https://portal.azure.com) en meld u aan met uw Azure-account.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navigeer naar de app in de Azure portal

Selecteer in het linkermenu **App Services** en selecteer de naam van uw app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/select-app.png)

U ziet de beheerpagina van de App Service-app.  

### <a name="check-the-pricing-tier"></a>Controleer de prijscategorie

Schuif in de navigatiebalk links van de app-pagina naar de sectie **Instellingen** en selecteer **Opschalen (App Service-plan)**.

![Menu Opschalen](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

De huidige laag van de app wordt gemarkeerd door een blauwe rand. Controleer of de app zich niet in de **F1**-laag bevindt. Aangepaste DNS wordt niet ondersteund in de **F1**-laag. 

![Controleer prijscategorie](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Als het App-serviceplan zich niet in de **F1-laag** bevindt, sluit u de pagina **Opschalen** en gaat u naar [Het domein kopen.](#buy-the-domain)

### <a name="scale-up-the-app-service-plan"></a>Het App Service-plan opschalen

Selecteer een van de lagen die niet gratis zijn (**D1**, **B1**, **B2**, **B3** of een laag in de categorie **Productie**). Klik op **Aanvullende opties bekijken** voor aanvullende opties.

Klik op **Toepassen**.

![Controleer prijscategorie](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Wanneer u de volgende melding ziet, is de schaalbewerking voltooid.

![Schaalbewerking bevestigen](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Het domein kopen

### <a name="pricing-information"></a>Prijsinformatie
Ga naar de pagina App Service Pricing naar het domein van de [app-service](https://azure.microsoft.com/pricing/details/app-service/windows/) voor prijsinformatie over Azure App Service-domeinen en blader naar het App Service-domein.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Open de [Azure-portal](https://portal.azure.com/) en meld u aan met uw Azure-account.

### <a name="launch-buy-domains"></a>Domeinen Kopen starten
Klik op het tabblad **App Services** op de naam van uw app, selecteer **Instellingen**en selecteer **Aangepaste domeinen**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Klik op de pagina **Aangepaste domeinen** op **Domein kopen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Als u de sectie **App-servicedomeinen** niet zien, moet u de bestedingslimiet voor uw Azure-account verwijderen [(zie Vereisten).](#prerequisites)
>
>

### <a name="configure-the-domain-purchase"></a>De domeinaankoop configureren

Typ op de pagina **App Service-domein** in het vak **Zoeken naar** `Enter`domein de domeinnaam die u wilt kopen en typ . De voorgestelde beschikbare domeinen worden getoond enkel onder het tekstvak. Selecteer een of meer domeinen die u wilt kopen.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> De volgende [topdomeinen](https://wikipedia.org/wiki/Top-level_domain) worden ondersteund door App Service domeinen: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_en _co.in_.
>
>

Klik op de **contactgegevens** en vul het contactgegevensformulier van het domein in. Klik op **OK** om terug te keren naar de pagina App Service-domein als u klaar bent.

Het is belangrijk dat u alle vereiste velden met zoveel mogelijk nauwkeurigheid invult. Onjuiste gegevens voor contactgegevens kunnen leiden tot het niet kopen van domeinen.

Selecteer vervolgens de gewenste opties voor uw domein. Zie de volgende tabel voor uitleg:

| Instelling | Voorgestelde waarde | Beschrijving |
|-|-|-|
|Privacybescherming | Inschakelen | U zich _gratis_aanmelden voor Privacybescherming, die is inbegrepen in de aankoopprijs. Sommige domeinen op het hoogste niveau worden beheerd door registrars die geen privacybescherming ondersteunen en worden vermeld op de pagina **Privacybescherming.** |
| Standaardhostnamen toewijzen | **www** en**\@** | Selecteer desgewenst de gewenste hostnamebindingen. Wanneer de bewerking voor de aankoop van het domein is voltooid, kan uw app worden geopend op de geselecteerde hostnamen. Als de app zich achter [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)bevindt, ziet u niet de optie om het hoofddomein toe te wijzen (@), omdat Traffic Manager geen A-records ondersteunt. U wijzigingen aanbrengen in de toewijzingen voor hostname nadat de domeinaankoop is voltooid. |

### <a name="accept-terms-and-purchase"></a>Accepteer voorwaarden en aankoop

Klik **op Juridische voorwaarden** om de voorwaarden en de kosten te bekijken en klik vervolgens op **Kopen**.

> [!NOTE]
> App Service-domeinen gebruiken GoDaddy voor domeinregistratie en Azure DNS om de domeinen te hosten. Naast de kosten voor domeinregistratie zijn er gebruikskosten voor Azure DNS van toepassing. Zie Azure [DNS Pricing](https://azure.microsoft.com/pricing/details/dns/)voor meer informatie.
>
>

Klik op de pagina **App Service-domein** op **OK**. Terwijl de bewerking aan de gang is, ziet u de volgende meldingen:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>De hostnamen testen

Als u standaardhostnamen aan uw app hebt toegewezen, ziet u ook een succesmelding voor elke geselecteerde hostnaam.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

U ziet ook de geselecteerde hostnamen op de pagina **Aangepaste domeinen** in de sectie **Aangepaste hostnamen.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Een **Niet Veilig** label voor uw aangepaste domein betekent dat het nog niet is gekoppeld aan een SSL-certificaat, en elk HTTPS-verzoek van een browser naar uw aangepaste domein ontvangt een fout of waarschuwing, afhankelijk van de browser. Zie Een aangepaste [DNS-naam beveiligen met een SSL-binding in Azure App Service](configure-ssl-bindings.md)als u SSL-binding wilt configureren.
>

Als u de hostnamen wilt testen, navigeert u naar de vermelde hostnamen in de browser. Probeer in het voorbeeld in de voorgaande schermafbeelding naar _kontoso.net_ en _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Hostnamen toewijzen aan app

Als u ervoor kiest om tijdens het aankoopproces geen of meer standaardhostnamen aan uw app toe te wijzen of als u een hostnaam moet toewijzen die niet wordt vermeld, u op elk gewenst moment een hostnaam toewijzen.

U hostnamen in het App-servicedomein ook toewijzen aan een andere app. De stappen zijn afhankelijk van de vraag of het App Service-domein en de app tot hetzelfde abonnement behoren.

- Ander abonnement: Breng aangepaste DNS-records van het App Service-domein naar de app toe, zoals een extern aangeschaft domein. Zie [Aangepaste DNS-records beheren](#custom)voor informatie over het toevoegen van aangepaste DNS-namen aan een App Service-domein. Zie [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service als](app-service-web-tutorial-custom-domain.md)u een extern gekocht domein wilt toewijzen aan een app. 
- Zelfde abonnement: Gebruik de volgende stappen.

### <a name="launch-add-hostname"></a>Hostnaam toevoegen
Selecteer **op** de pagina App Services de naam van uw app waaraan u hostnamen wilt toewijzen, selecteer **Instellingen**en selecteer **aangepaste domeinen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Zorg ervoor dat uw gekochte domein wordt weergegeven in de sectie **App-servicedomeinen,** maar selecteer het niet. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Alle App Service-domeinen in hetzelfde abonnement worden weergegeven op de pagina **Aangepaste domeinen** van de app. Als uw domein zich in het abonnement van de app bevindt, maar u het niet zien op de pagina **Aangepaste domeinen** van de app, probeert u de pagina **Aangepaste domeinen opnieuw te** openen of de webpagina te vernieuwen. Controleer ook de meldingsbel boven aan de Azure-portal op voortgangs- of creatiefouten.
>
>

Selecteer **Hostnaam toevoegen**.

### <a name="configure-hostname"></a>Hostnaam configureren
Typ in het dialoogvenster **Hostname toevoegen** de volledig gekwalificeerde domeinnaam van uw App Service-domein of een subdomein. Bijvoorbeeld:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Als u klaar bent, selecteert u **Valideren**. Het type hostnamerecord wordt automatisch voor u geselecteerd.

Selecteer **Hostnaam toevoegen**.

Wanneer de bewerking is voltooid, ziet u een melding voor succes voor de toegewezen hostnaam.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Hostnaam toevoegen sluiten
Wijs op de pagina **Hostname toevoegen** de gewenste andere hostnaam toe aan uw app. Als u klaar bent, sluit u de pagina **Hostname toevoegen.**

U ziet nu de nieuw toegewezen hostname(s) op de pagina **Aangepaste domeinen** van uw app.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>De hostnamen testen

Navigeer naar de vermelde hostnamen in de browser. Probeer in het voorbeeld in de vorige schermafbeelding te navigeren naar _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Het domein vernieuwen

Het app-servicedomein dat u hebt gekocht, is één jaar geldig vanaf het moment van aankoop. Standaard is het domein geconfigureerd om automatisch te verlengen door uw betalingsmethode voor het volgende jaar in rekening te brengen. U uw domeinnaam handmatig vernieuwen.

Als u automatische verlenging wilt uitschakelen of als u uw domein handmatig wilt vernieuwen, volgt u de stappen hier.

Klik op het tabblad **App Services** op de naam van uw app, selecteer **Instellingen**en selecteer **Aangepaste domeinen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Selecteer in de sectie **App-servicedomeinen** het domein dat u wilt configureren.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Selecteer **Domeinvernieuwing**in de linkernavigatie van het domein . Als u wilt stoppen met het automatisch vernieuwen van uw domein, selecteert u **Uit,** en vervolgens **Opslaan**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Als u uw domein handmatig wilt vernieuwen, selecteert u **Domein vernieuwen**. Deze knop is echter pas [90 dagen voor het verstrijken van het domein](#when-domain-expires)actief.

Als uw domeinvernieuwing succesvol is, ontvangt u binnen 24 uur een e-mailmelding.

## <a name="when-domain-expires"></a>Wanneer domein verloopt

Azure houdt zich als volgt bezig met verlopen of verlopen App Service-domeinen:

* Als automatische verlenging is uitgeschakeld: 90 dagen voor het verlopen van het domein, wordt een e-mail met verlengingsmelding naar u verzonden en wordt de knop **Domein vernieuwen** geactiveerd in de portal.
* Als automatische verlenging is ingeschakeld: op de dag na de vervaldatum van uw domein probeert Azure u te factureren voor de verlenging van de domeinnaam.
* Als er een fout optreedt tijdens automatische verlenging (bijvoorbeeld uw kaart in het bestand is verlopen) of als automatische verlenging is uitgeschakeld en u toestaat dat het domein verloopt, waarschuwt Azure u over de vervaldatum van het domein en parkeert u uw domeinnaam. U uw domein [handmatig vernieuwen.](#renew-the-domain)
* Op de 4e en 12e dag na het verstrijken van de dag stuurt Azure u aanvullende e-mails met meldingen. U uw domein [handmatig vernieuwen.](#renew-the-domain)
* Op de 19e dag na het verstrijken van het verval, uw domein blijft in de wacht, maar wordt onderworpen aan een inwisselkosten. U de klantenservice bellen om uw domeinnaam te vernieuwen, onder voorbehoud van toepasselijke verlengings- en inwisselkosten.
* Op de 25e dag na het verstrijken van de looptijd zet Azure uw domein te veilen met een veilingservice voor de domeinnaamindustrie. U de klantenservice bellen om uw domeinnaam te vernieuwen, onder voorbehoud van toepasselijke verlengings- en inwisselkosten.
* Op de 30e dag na afloop u uw domein niet meer inwisselen.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Aangepaste DNS-records beheren

In Azure worden DNS-records voor een App Service-domein beheerd met [Azure DNS.](https://azure.microsoft.com/services/dns/) U DNS-records toevoegen, verwijderen en bijwerken, net als voor een extern aangeschaft domein.

### <a name="open-app-service-domain"></a>App-servicedomein openen

Selecteer in de Azure-portal in het linkermenu **alle services** > **App Service-domeinen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecteer het domein dat u wilt beheren. 

### <a name="access-dns-zone"></a>DNS-zone access

Selecteer **DNS-zone**in het linkermenu van het domein.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Met deze actie wordt de [PAGINA DNS-zone](../dns/dns-zones-records.md) van uw App Service-domein geopend in Azure DNS. Zie [DNS-zones beheren in de Azure-portal](../dns/dns-operations-dnszones-portal.md)voor informatie over het bewerken van DNS-records.

## <a name="cancel-purchase-delete-domain"></a>Aankoop annuleren (domein verwijderen)

Nadat u het App Service-domein hebt gekocht, hebt u vijf dagen de tijd om uw aankoop te annuleren voor een volledige terugbetaling. Na vijf dagen u het app-servicedomein verwijderen, maar u geen terugbetaling ontvangen.

### <a name="open-app-service-domain"></a>App-servicedomein openen

Selecteer in de Azure-portal in het linkermenu **alle services** > **App Service-domeinen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecteer het domein dat u wilt annuleren of verwijderen. 

### <a name="delete-hostname-bindings"></a>Bindingen met hostnaam verwijderen

Selecteer **hostnaambindingen**in het linkermenu van het domein. De hostnamebindingen van alle Azure-services worden hier weergegeven.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

U het App Service-domein niet verwijderen totdat alle hostnamebindingen zijn verwijderd.

Verwijder elke hostname binding door te selecteren **...**  >  **Verwijderen**. Nadat alle bindingen zijn verwijderd, selecteert u **Opslaan**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Annuleren of verwijderen

Selecteer **Overzicht**in het linkermenu van het domein. 

Als de annuleringsperiode op het gekochte domein niet is verstreken, selecteert u **Aankoop annuleren**. Anders ziet u in plaats daarvan een knop **Verwijderen.** Als u het domein wilt verwijderen zonder terugbetaling, selecteert u **Verwijderen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Als u de bewerking wilt bevestigen, selecteert u **Ja**.

Nadat de bewerking is voltooid, wordt het domein vrijgegeven van uw abonnement en beschikbaar voor iedereen om opnieuw te kopen. 

## <a name="direct-default-url-to-a-custom-directory"></a>Standaard-URL naar een aangepaste map omleiden

Standaard stuurt App Service webaanvragen naar de hoofdmap van uw app-code. Als u ze wilt doorverwijzen `public`naar een submap, zoals , zie [Standaard-URL naar een aangepaste map](app-service-web-tutorial-custom-domain.md#virtualdir).
