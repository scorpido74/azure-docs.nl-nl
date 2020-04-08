---
title: Een aangepaste DNS beveiligen met een TLS/SSL-binding
description: Beveilig HTTPS-toegang tot uw aangepaste domein door een TLS/SSL-binding te maken met een certificaat. Verbeter de beveiliging van uw website door HTTPS of TLS 1.2 af te dwingen.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 9792181379bfa6f9e0337bf14208fe853c16b745
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811745"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service

In dit artikel ziet u hoe u het [aangepaste domein](app-service-web-tutorial-custom-domain.md) in uw [App Service-app](https://docs.microsoft.com/azure/app-service/) of [functie-app](https://docs.microsoft.com/azure/azure-functions/) beveiligen door een certificaatbinding te maken. Wanneer u klaar bent, hebt u toegang `https://` tot uw App Service-app op `https://www.contoso.com`het eindpunt voor uw aangepaste DNS-naam (bijvoorbeeld). 

![Web-app met aangepast TLS/SSL-certificaat](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Het beveiligen van een [aangepast domein](app-service-web-tutorial-custom-domain.md) met een certificaat omvat twee stappen:

- [Voeg een privécertificaat toe aan App Service](configure-ssl-certificate.md) dat voldoet aan alle vereisten voor [privécertificaten.](configure-ssl-certificate.md#private-certificate-requirements)
-  Maak een TLS-binding voor het bijbehorende aangepaste domein. Deze tweede stap wordt behandeld door dit artikel.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De prijscategorie van uw app upgraden
> * Een aangepast domein beveiligen met een certificaat
> * HTTPS afdwingen
> * TLS 1.1/1.2 afdwingen
> * TLS-beheer automatiseren met scripts

## <a name="prerequisites"></a>Vereisten

Volg deze handleiding:

- [Een App Service-app maken](/azure/app-service/)
- [Een domeinnaam](app-service-web-tutorial-custom-domain.md) toewijzen aan uw app of [deze kopen en configureren in Azure](manage-custom-dns-buy-domain.md)
- [Een privécertificaat toevoegen aan uw app](configure-ssl-certificate.md)

> [!NOTE]
> De eenvoudigste manier om een privécertificaat toe te voegen, is door een gratis App Service Managed Certificate (Preview) te [maken.](configure-ssl-certificate.md#create-a-free-certificate-preview)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Een aangepast domein beveiligen

Ga als volgt te werk:

Selecteer in de <a href="https://portal.azure.com" target="_blank">Azure-portal</a>in het linkermenu de>**\<app-naam app-naam ** **van App Services.** > 

Start vanuit de linkernavigatie van uw app het dialoogvenster **TLS/SSL-binding** op:

- **Aangepaste domeinen** > **selecteren Binding toevoegen**
- **TLS/SSL-instellingen** > **selecteren Voeg TLS/SSL-binding toe**

![Binding toevoegen aan domein](./media/configure-ssl-bindings/secure-domain-launch.png)

Selecteer **in Aangepast domein**het aangepaste domein waarvoor u een binding wilt toevoegen.

Als uw app al een certificaat heeft voor het geselecteerde aangepaste domein, gaat u rechtstreeks naar [Binding maken.](#create-binding) Anders ga je door.

### <a name="add-a-certificate-for-custom-domain"></a>Een certificaat toevoegen voor aangepast domein

Als uw app geen certificaat heeft voor het geselecteerde aangepaste domein, hebt u twee opties:

- **PFX-certificaat uploaden** - Volg de workflow bij [Een privécertificaat uploaden](configure-ssl-certificate.md#upload-a-private-certificate)en selecteer deze optie hier.
- **App Servicecertificaat importeren** - Volg de werkstroom bij [Een App Service-certificaat importeren](configure-ssl-certificate.md#import-an-app-service-certificate)en selecteer deze optie hier.

> [!NOTE]
> U ook [een gratis certificaat maken](configure-ssl-certificate.md#create-a-free-certificate-preview) (Voorbeeld) of Een Key [Vault-certificaat importeren,](configure-ssl-certificate.md#import-a-certificate-from-key-vault)maar u moet dit afzonderlijk doen en vervolgens terugkeren naar het dialoogvenster **TLS/SSL-binding.**

### <a name="create-binding"></a>Binding maken

Gebruik de volgende tabel om de TLS-binding in het dialoogvenster **TLS/SSL-binding** te configureren en klik vervolgens op **Binding toevoegen**.

| Instelling | Beschrijving |
|-|-|
| Aangepast domein | De domeinnaam om de TLS/SSL-binding voor toe te voegen. |
| Duimafdruk privécertificaat | Het certificaat te binden. |
| TLS/SSL-type | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** - Er kunnen meerdere SNI SSL-bindingen worden toegevoegd. Met deze optie kunnen meerdere TLS/SSL-certificaten meerdere domeinen op hetzelfde IP-adres beveiligen. De meeste moderne browsers (waaronder Internet Explorer, Chrome, Firefox en Opera) ondersteunen SNI (zie [Servernaamaanduiding](https://wikipedia.org/wiki/Server_Name_Indication)voor meer informatie).</li><li>**IP SSL** - Er mag slechts één IP SSL-binding worden toegevoegd. Met deze optie kan slechts één TLS/SSL-certificaat een speciaal openbaar IP-adres beveiligen. Nadat u de binding hebt geconfigureerd, volgt u de stappen in [Remap A-record voor IP SSL.](#remap-a-record-for-ip-ssl)<br/>IP SSL wordt alleen ondersteund in productie- of geïsoleerde lagen. </li></ul> |

Zodra de bewerking is voltooid, wordt de TLS/SSL-status van het aangepaste domein gewijzigd in **Secure.**

![TLS/SSL-binding geslaagd](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Een **veilige** status in de **aangepaste domeinen** betekent dat deze is beveiligd met een certificaat, maar App Service controleert niet of het certificaat bijvoorbeeld zelf is ondertekend of verlopen, waardoor browsers ook een fout of waarschuwing kunnen weergeven.

## <a name="remap-a-record-for-ip-ssl"></a>Een record voor IP SSL opnieuw toewijzen

Als u GEEN IP SSL in uw app gebruikt, gaat u naar [HTTPS testen voor uw aangepaste domein.](#test-https)

Uw app maakt standaard gebruik van een gedeeld openbaar IP-adres. Wanneer u een certificaat bindt met IP SSL, maakt App Service een nieuw, speciaal IP-adres voor uw app.

Als u een A-record aan uw app hebt toegewezen, werkt u uw domeinregister bij met dit nieuwe, specifieke IP-adres.

De pagina **Aangepast domein** van uw app wordt bijgewerkt met het nieuwe, specifieke IP-adres. [Kopieer dit IP-adres](app-service-web-tutorial-custom-domain.md#info) en [wijs de A-record opnieuw toe](app-service-web-tutorial-custom-domain.md#map-an-a-record) aan dit nieuwe IP-adres.

## <a name="test-https"></a>HTTPS testen

Blader in verschillende `https://<your.custom.domain>` browsers om te controleren of deze uw app opdient.

![Navigatie naar Azure-app in de portal](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Uw toepassingscode kan het protocol inspecteren via de "x-appservice-proto" header. De koptekst heeft `http` een `https`waarde van of . 

> [!NOTE]
> Als uw app certificaatvalidatiefouten geeft, gebruikt u waarschijnlijk een zelfondertekend certificaat.
>
> Als dit niet het geval is, hebt u mogelijk tussenliggende certificaten weggelaten toen u uw certificaat naar het PFX-bestand exporteerde.

## <a name="prevent-ip-changes"></a>IP-wijzigingen voorkomen

Uw binnenkomende IP-adres kan veranderen wanneer u een binding verwijdert, zelfs als die binding IP SSL is. Dit is vooral belangrijk wanneer u een certificaat verlengt dat al in een IP SSL-binding staat. Als u een wijziging in het IP-adres van uw app wilt voorkomen, volgt u in volgorde de volgende stappen:

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

## <a name="handle-tls-termination"></a>TLS-beëindiging afhandelen

In App Service vindt [TLS-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats bij de netwerklastsbalansen, zodat alle HTTPS-verzoeken uw app bereiken als onversleutelde HTTP-verzoeken. Inspecteer de header `X-Forwarded-Proto` als de app-logica moet controleren of de aanvragen van gebruikers al dan niet zijn versleuteld.

Taalspecifieke configuratiehandleidingen, zoals de [Configuratiegids Linux Node.js,](containers/configure-language-nodejs.md#detect-https-session) laten u zien hoe u een HTTPS-sessie in uw toepassingscode detecteren.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Meer bronnen

* [Een TLS/SSL-certificaat gebruiken in uw code in Azure App Service](configure-ssl-certificate-in-code.md)
* [Veelgestelde vragen: App-servicecertificaten](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
