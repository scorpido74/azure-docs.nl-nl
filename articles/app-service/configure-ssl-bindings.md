---
title: Een aangepaste DNS beveiligen met een TLS/SSL-binding
description: Beveiligde HTTPS-toegang tot uw aangepaste domein door een TLS/SSL-binding met een certificaat te maken. Verbeter de beveiliging van uw website door HTTPS of TLS 1,2 af te dwingen.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 04/30/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: c93938db4632f6509e386d440c9be75596ea254f
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597892"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service

In dit artikel wordt beschreven hoe u het [aangepaste domein](app-service-web-tutorial-custom-domain.md) in uw [app service app](https://docs.microsoft.com/azure/app-service/) of [functie-app](https://docs.microsoft.com/azure/azure-functions/) kunt beveiligen door een certificaat binding te maken. Wanneer u klaar bent, kunt u toegang krijgen tot uw App Service- `https://` app op het eind punt voor uw aangepaste DNS- `https://www.contoso.com`naam (bijvoorbeeld). 

![Web-app met aangepast TLS/SSL-certificaat](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Het beveiligen van een [aangepast domein](app-service-web-tutorial-custom-domain.md) met een certificaat bestaat uit twee stappen:

- [Voeg een persoonlijk certificaat toe aan app service](configure-ssl-certificate.md) dat voldoet aan alle [vereisten voor persoonlijke certificaten](configure-ssl-certificate.md#private-certificate-requirements).
-  Maak een TLS-binding met het bijbehorende aangepaste domein. Deze tweede stap is opgenomen in dit artikel.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De prijscategorie van uw app upgraden
> * Een aangepast domein beveiligen met een certificaat
> * HTTPS afdwingen
> * TLS 1.1/1.2 afdwingen
> * TLS-beheer automatiseren met scripts

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt volgen:

- [Een App Service-app maken](/azure/app-service/)
- [Een domein naam toewijzen aan uw app](app-service-web-tutorial-custom-domain.md) of [kopen en configureren in azure](manage-custom-dns-buy-domain.md)
- [Een persoonlijk certificaat toevoegen aan uw app](configure-ssl-certificate.md)

> [!NOTE]
> De eenvoudigste manier om een persoonlijk certificaat toe te voegen, is door [een gratis app service beheerd certificaat te maken](configure-ssl-certificate.md#create-a-free-certificate-preview) (preview).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Een aangepast domein beveiligen

Voer de volgende stappen uit:

Selecteer **App Services** > **app Services\<app-name>** in het menu links in het <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

Open vanuit de linkernavigatiebalk van uw app het dialoog venster **TLS/SSL binding** door:

- **Aangepaste domeinen** > selecteren**binding toevoegen**
- **TLS/SSL-instellingen** > selecteren**TLS/SSL-binding toevoegen**

![Binding aan domein toevoegen](./media/configure-ssl-bindings/secure-domain-launch.png)

Selecteer in **aangepast domein**het aangepaste domein waarvoor u een binding wilt toevoegen.

Als uw app al een certificaat voor het geselecteerde aangepaste domein heeft, gaat u naar [binding rechtstreeks maken](#create-binding) . Ga anders verder.

### <a name="add-a-certificate-for-custom-domain"></a>Een certificaat voor een aangepast domein toevoegen

Als uw app geen certificaat heeft voor het geselecteerde aangepaste domein, hebt u twee opties:

- **PFX-certificaat uploaden** : Volg de werk stroom bij het [uploaden van een persoonlijk certificaat](configure-ssl-certificate.md#upload-a-private-certificate)en selecteer deze optie hier.
- **App service Certificate importeren** : Volg de werk stroom bij het [importeren van een app service certificaat](configure-ssl-certificate.md#import-an-app-service-certificate)en selecteer deze optie hier.

> [!NOTE]
> U kunt ook [een gratis certificaat](configure-ssl-certificate.md#create-a-free-certificate-preview) (preview) maken of [een Key Vault certificaat importeren](configure-ssl-certificate.md#import-a-certificate-from-key-vault), maar u moet dit afzonderlijk doen en vervolgens terugkeren naar het dialoog venster **TLS/SSL-binding** .

### <a name="create-binding"></a>Binding maken

Gebruik de volgende tabel om u te helpen de TLS-binding te configureren in het dialoog venster **TLS/SSL-binding** en klik vervolgens op **binding toevoegen**.

| Instelling | Beschrijving |
|-|-|
| Aangepast domein | De domein naam waaraan u de TLS/SSL-binding wilt toevoegen. |
| Vinger afdruk van persoonlijk certificaat | Het certificaat dat moet worden gebonden. |
| Type TLS/SSL | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** : er kunnen meerdere SNI SSL bindingen worden toegevoegd. Met deze optie kunnen meerdere TLS/SSL-certificaten meerdere domeinen op hetzelfde IP-adres beveiligen. De meeste moderne browsers (waaronder Internet Explorer, Chrome, Firefox en Opera) ondersteunen SNI (Zie [Servernaamindicatie](https://wikipedia.org/wiki/Server_Name_Indication)) voor meer informatie.</li><li>**IP SSL** : er kan slechts één IP SSL binding worden toegevoegd. Met deze optie kan slechts één TLS/SSL-certificaat een specifiek openbaar IP-adres beveiligen. Nadat u de binding hebt geconfigureerd, volgt u de stappen in [records opnieuw toewijzen voor IP SSL](#remap-records-for-ip-ssl).<br/>IP SSL wordt alleen ondersteund in de **Standard** -laag of hoger. </li></ul> |

Zodra de bewerking is voltooid, wordt de TLS/SSL-status van het aangepaste domein gewijzigd in **beveiligd**.

![TLS/SSL-binding geslaagd](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Een **veilige** status in de **aangepaste domeinen** houdt in dat deze is beveiligd met een certificaat, maar app service controleert niet of het certificaat zelf is ondertekend of is verlopen, bijvoorbeeld, waardoor browsers ook een fout of waarschuwing kunnen weer geven.

## <a name="remap-records-for-ip-ssl"></a>Records opnieuw toewijzen voor IP SSL

Als u IP SSL niet gebruikt in uw app, gaat u door met het [testen van HTTPS voor uw aangepaste domein](#test-https).

Er zijn twee wijzigingen die u moet aanbrengen, mogelijk:

- Uw app maakt standaard gebruik van een gedeeld openbaar IP-adres. Wanneer u een certificaat verbindt met IP SSL, maakt App Service een nieuw, toegewijd IP-adres voor uw app. Als u een A-record aan uw app hebt toegewezen, werkt u het domein register bij met dit nieuwe, toegewezen IP-adres.

    De pagina **Aangepast domein** van uw app wordt bijgewerkt met het nieuwe, specifieke IP-adres. [Kopieer dit IP-adres](app-service-web-tutorial-custom-domain.md#info) en [wijs de A-record opnieuw toe](app-service-web-tutorial-custom-domain.md#map-an-a-record) aan dit nieuwe IP-adres.

- Als u een SNI SSL binding hebt met `<app-name>.azurewebsites.net`, moet u [een CNAME-toewijzing](app-service-web-tutorial-custom-domain.md#map-a-cname-record) zo `sni.<app-name>.azurewebsites.net` toewijzen dat ze in `sni` plaats daarvan verwijzen (Voeg het voor voegsel toe).

## <a name="test-https"></a>HTTPS testen

In verschillende browsers, bladert `https://<your.custom.domain>` u naar om te controleren of het uw app in beslag voorziet.

![Navigatie naar Azure-app in de portal](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Uw toepassings code kan het protocol controleren via de header ' x-appservice-proto '. De header heeft de waarde `http` of. `https` 

> [!NOTE]
> Als uw app certificaatvalidatiefouten geeft, gebruikt u waarschijnlijk een zelfondertekend certificaat.
>
> Als dit niet het geval is, hebt u mogelijk tussenliggende certificaten weggelaten toen u uw certificaat naar het PFX-bestand exporteerde.

## <a name="prevent-ip-changes"></a>IP-wijzigingen voor komen

Uw inkomende IP-adres kan veranderen wanneer u een binding verwijdert, zelfs als deze binding is IP SSL. Dit is vooral belang rijk bij het vernieuwen van een certificaat dat al in een IP SSL binding is. Als u een wijziging in het IP-adres van uw app wilt voorkomen, volgt u in volgorde de volgende stappen:

1. Upload het nieuwe certificaat.
2. Verbind het nieuwe certificaat aan het aangepaste domein dat u wilt, zonder het oude certificaat te verwijderen. Met deze actie wordt de oude binding vervangen en niet verwijderd.
3. Verwijder het oude certificaat. 

## <a name="enforce-https"></a>HTTPS afdwingen

Standaard heeft iedereen nog steeds toegang tot uw app via HTTP. U kunt alle HTTP-aanvragen omleiden naar de HTTPS-poort.

Selecteer in het linkernavigatievenster van de app-pagina **SSL-instellingen**. Klik op **Alleen HTTPS** en selecteer **Aan**.

![HTTPS afdwingen](./media/configure-ssl-bindings/enforce-https.png)

Wanneer de bewerking is voltooid, gaat u naar een van de HTTP-URL's die naar uw app verwijzen. Bijvoorbeeld:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS-versies afdwingen

Voor uw app is standaard [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 toegestaan, wat het aanbevolen TLS-niveau is volgens industrienormen zoals [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Als u andere TLS-versies wilt afdwingen, volgt u deze stappen:

Selecteer in het linkernavigatievenster van de app-pagina **SSL-instellingen**. Selecteer vervolgens bij **TLS-versie** de gewenste minimale TLS-versie. Met deze instelling controleert u alleen de inkomende oproepen. 

![TLS 1.1 of 1.2 afdwingen](./media/configure-ssl-bindings/enforce-tls1-2.png)

Als de bewerking is voltooid, worden in de app alle verbindingen met lagere TLS-versies geweigerd.

## <a name="handle-tls-termination"></a>TLS-beëindiging verwerken

In App Service wordt [TLS-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) uitgevoerd op netwerk taak verdelers, dus alle HTTPS-aanvragen bereiken uw app als niet-versleutelde HTTP-aanvragen. Inspecteer de header `X-Forwarded-Proto` als de app-logica moet controleren of de aanvragen van gebruikers al dan niet zijn versleuteld.

Taalspecifieke configuratie handleidingen, zoals de [configuratie gids Linux node. js](containers/configure-language-nodejs.md#detect-https-session) , laat zien hoe u een HTTPS-sessie in uw toepassings code kunt detecteren.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Meer bronnen

* [Gebruik een TLS/SSL-certificaat in uw code in Azure App Service](configure-ssl-certificate-in-code.md)
* [Veelgestelde vragen: App Service certificaten](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
