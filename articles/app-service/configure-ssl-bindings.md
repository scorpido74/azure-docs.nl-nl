---
title: Een aangepaste DNS beveiligen met een SSL-binding
description: Beveiligde HTTPS-toegang tot uw aangepaste domein door een TLS/SSL-binding met een certificaat te maken. Verbeter de beveiliging van uw website door HTTPS of TLS 1,2 af te dwingen.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 263b4e76d334aab82f6bbac9aa268a50f4dd3784
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239705"
---
# <a name="secure-a-custom-dns-name-with-an-ssl-binding-in-azure-app-service"></a>Een aangepaste DNS-naam beveiligen met een SSL-binding in Azure App Service

In dit artikel wordt beschreven hoe u het [aangepaste domein](app-service-web-tutorial-custom-domain.md) in uw [app service app](https://docs.microsoft.com/azure/app-service/) of [functie-app](https://docs.microsoft.com/azure/azure-functions/) kunt beveiligen door een certificaat binding te maken. Wanneer u klaar bent, hebt u toegang tot uw App Service-app op het `https://`-eind punt voor uw aangepaste DNS-naam (bijvoorbeeld `https://www.contoso.com`). 

![Web-app met aangepast SSL-certificaat](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Het beveiligen van een [aangepast domein](app-service-web-tutorial-custom-domain.md) met een certificaat bestaat uit twee stappen:

- [Voeg een persoonlijk certificaat toe aan app service](configure-ssl-certificate.md) dat voldoet aan alle [vereisten voor SSL-bindingen](configure-ssl-certificate.md#private-certificate-requirements).
-  Maak een SSL-binding met het bijbehorende aangepaste domein. Deze tweede stap is opgenomen in dit artikel.

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

Selecteer in het menu links in het <a href="https://portal.azure.com" target="_blank">Azure Portal</a> **app Services** > \<naam van de **app >** .

Open vanuit de linkernavigatiebalk van uw app het dialoog venster **TLS/SSL binding** door:

- **Aangepaste domeinen** selecteren > **binding toe te voegen**
- **TLS/SSL-instellingen** selecteren > **TLS/SSL-binding toevoegen**

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

Gebruik de volgende tabel om u te helpen bij het configureren van de SSL-binding in het dialoog venster **TLS/SSL-binding** en klik vervolgens op **binding toevoegen**.

| Instelling | Beschrijving |
|-|-|
| Aangepast domein | De domein naam voor het toevoegen van de SSL-binding voor. |
| Vinger afdruk van persoonlijk certificaat | Het certificaat dat moet worden gebonden. |
| Type TLS/SSL | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** : er kunnen meerdere SNI SSL bindingen worden toegevoegd. Met deze optie kunnen meerdere SSL-certificaten verschillende domeinen beveiligen op hetzelfde IP-adres. De meeste moderne browsers (waaronder Internet Explorer, Chrome, Firefox en Opera) ondersteunen SNI (Zie [Servernaamindicatie](https://wikipedia.org/wiki/Server_Name_Indication)) voor meer informatie.</li><li>**IP SSL** : er kan slechts één IP SSL binding worden toegevoegd. Met deze optie kan slechts één SSL-certificaat een specifiek openbaar IP-adres beveiligen. Nadat u de binding hebt geconfigureerd, volgt u de stappen in [een record opnieuw toewijzen voor IP SSL](#remap-a-record-for-ip-ssl).<br/>IP SSL wordt alleen ondersteund in productie-of geïsoleerde lagen. </li></ul> |

Zodra de bewerking is voltooid, wordt de SSL-status van het aangepaste domein gewijzigd in **beveiligd**.

![SSL-binding geslaagd](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Een **veilige** status in de **aangepaste domeinen** houdt in dat deze is beveiligd met een certificaat, maar app service controleert niet of het certificaat zelf is ondertekend of is verlopen, bijvoorbeeld, waardoor browsers ook een fout of waarschuwing kunnen weer geven.

## <a name="remap-a-record-for-ip-ssl"></a>Een record voor IP SSL opnieuw toewijzen

Als u IP SSL niet gebruikt in uw app, gaat u door met het [testen van HTTPS voor uw aangepaste domein](#test-https).

Uw app maakt standaard gebruik van een gedeeld openbaar IP-adres. Wanneer u een certificaat verbindt met IP SSL, maakt App Service een nieuw, toegewijd IP-adres voor uw app.

Als u een A-record aan uw app hebt toegewezen, werkt u het domein register bij met dit nieuwe, toegewezen IP-adres.

De pagina **Aangepast domein** van uw app wordt bijgewerkt met het nieuwe, specifieke IP-adres. [Kopieer dit IP-adres](app-service-web-tutorial-custom-domain.md#info) en [wijs de A-record opnieuw toe](app-service-web-tutorial-custom-domain.md#map-an-a-record) aan dit nieuwe IP-adres.

## <a name="test-https"></a>HTTPS testen

Blader in verschillende browsers naar `https://<your.custom.domain>` om te controleren of het uw app is.

![Navigatie naar Azure-app in de portal](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Uw toepassings code kan het protocol controleren via de header ' x-appservice-proto '. De header heeft de waarde `http` of `https`. 

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

## <a name="handle-ssl-termination"></a>SSL-beëindiging afhandelen

In App Service vindt [SSL-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats in de load balancers voor het netwerk, zodat alle HTTPS-aanvragen uw app bereiken als niet-versleutelde HTTP-aanvragen. Inspecteer de header `X-Forwarded-Proto` als de app-logica moet controleren of de aanvragen van gebruikers al dan niet zijn versleuteld.

Taalspecifieke configuratie handleidingen, zoals de [configuratie gids Linux node. js](containers/configure-language-nodejs.md#detect-https-session) , laat zien hoe u een HTTPS-sessie in uw toepassings code kunt detecteren.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Meer bronnen

* [Een SSL-certificaat gebruiken in uw toepassings code](configure-ssl-certificate-in-code.md)
* [Veelgestelde vragen: App Service certificaten](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
