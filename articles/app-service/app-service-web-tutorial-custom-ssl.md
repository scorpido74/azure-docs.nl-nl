---
title: SSL-certificaat uploaden en binden-Azure App Service | Microsoft Docs
description: Meer informatie over het verbinden van een aangepast SSL-certificaat met een web-app, de back-end voor een mobiele app of een API-app in Azure App Service.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c5095bc8c274ef0985b00459b0d088371ab24d88
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177032"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Zelfstudie: SSL-certificaten uploaden en binden aan Azure App Service

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In deze zelf studie wordt uitgelegd hoe u een aangepast domein in App Service kunt beveiligen met een certificaat dat u hebt aangeschaft bij een vertrouwde certificerings instantie. U ziet ook hoe u alle persoonlijke en open bare certificaten uploadt die uw app nodig heeft. Wanneer u klaar bent, kunt u uw app openen op het HTTPS-eindpunt van uw aangepaste DNS-domein.

![Web-app met aangepast SSL-certificaat](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De prijscategorie van uw app upgraden
> * Een aangepast domein beveiligen met een certificaat
> * Een persoonlijk certificaat uploaden
> * Een openbaar certificaat uploaden
> * Certificaten vernieuwen
> * HTTPS afdwingen
> * TLS 1.1/1.2 afdwingen
> * TLS-beheer automatiseren met scripts

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- [Een App Service-app maken](/azure/app-service/)
- [Een aangepaste DNS-naam toewijzen aan uw app service-app](app-service-web-tutorial-custom-domain.md) (bij het beveiligen van een aangepast domein)
- Een certificaat van een vertrouwde certificerings instantie verkrijgen
- De persoonlijke sleutel gebruiken voor het ondertekenen van de certificaat aanvraag (voor persoonlijke certificaten)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Een persoonlijk certificaat voorbereiden

Als u een domein wilt beveiligen, moet het certificaat voldoen aan de volgende vereisten:

* Geconfigureerd voor Server verificatie
* Ondertekend door een vertrouwde certificeringsinstantie
* Geëxporteerd als een PFX-bestand met wachtwoordbeveiliging
* Bevat een persoonlijke sleutel van minstens 2048 bits
* Bevat alle tussenliggende certificaten in de certificaatketen

> [!TIP]
> Als u een aangepast SSL-certificaat nodig hebt, kunt u het rechtstreeks in de Azure Portal ophalen en dit importeren in uw app. Volg de [zelfstudie voor App Service Certificates](web-sites-purchase-ssl-web-site.md).

> [!NOTE]
> **ECC-certificaten (cryptografie met behulp van elliptische krommen)** kunnen met App Service worden gebruikt, maar worden niet in dit artikel beschreven. Werk samen met uw certificeringsinstantie aan de exacte stappen voor het maken van ECC-certificaten.

Zodra u een certificaat van uw certificaat provider hebt ontvangen, volgt u de stappen in deze sectie om de app gereed te maken voor App Service.

### <a name="merge-intermediate-certificates"></a>Tussenliggende certificaten samenvoegen

Als uw certificeringsinstantie meerdere certificaten in de certificaatketen geeft, moet u de certificaten in de juiste volgorde samenvoegen.

U doet dit door elk certificaat dat u hebt ontvangen in een teksteditor te openen.

Maak een bestand voor het samengevoegde certificaat met de naam _mergedcertificate.crt_. Kopieer de inhoud van elk certificaat in dit bestand in een teksteditor. De volgorde van uw certificaten moet de volgorde in de certificaatketen volgen, beginnend met uw certificaat en eindigend met het hoofdcertificaat. Het lijkt op het volgende voorbeeld:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Certificaat naar PFX exporteren

Exporteer uw samengevoegde SSL-certificaat met de persoonlijke sleutel die met uw certificaataanvraag is gegenereerd.

Als u de certificaataanvraag met OpenSSL hebt gegenereerd, hebt u een bestand met een persoonlijke sleutel gemaakt. Voer de volgende opdracht uit om uw certificaat naar PFX te exporteren. Vervang de tijdelijke aanduidingen _&lt;private-key-file>_ en _&lt;merged-certificate-file>_ door de paden naar uw persoonlijke sleutel en uw bestand met samengevoegde certificaten.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Wanneer u daarom wordt gevraagd, geeft u een wachtwoord voor export op. U gebruikt dit wachtwoord later wanneer u uw SSL-certificaat naar App Service uploadt.

Als u IIS of _Certreq.exe_ hebt gebruikt voor het genereren van uw certificaataanvraag, installeert u het certificaat op uw lokale computer en [exporteert u het certificaat naar PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

U bent nu klaar om het certificaat te uploaden naar App Service.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Een aangepast domein beveiligen

> [!TIP]
> Als u een aangepast SSL-certificaat nodig hebt, kunt u er een rechtstreeks in Azure Portal ophalen en met uw app verbinden. Volg de [zelfstudie voor App Service Certificates](web-sites-purchase-ssl-web-site.md).

Als u een [aangepast domein](app-service-web-tutorial-custom-domain.md) met een certificaat van een derde partij wilt beveiligen, uploadt u het [voor bereide persoonlijke certificaat](#prepare-a-private-certificate) en koppelt u dit aan het aangepaste domein, maar app Service vereenvoudigt het proces voor u. Voer de volgende stappen uit:

Klik op **aangepaste domeinen** in het linkernavigatievenster van uw app en klik vervolgens op **binding toevoegen** voor het domein dat u wilt beveiligen. Als u **binding toevoegen** niet ziet voor een domein, is deze al beveiligd en moet er een **beveiligde** SSL-status zijn.

![Binding aan domein toevoegen](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Klik op **Certificaat uploaden**.

In **PFX-certificaatbestand** selecteert u uw PFX-bestand. Typ in **Certificaatwachtwoord** het wachtwoord dat u hebt gemaakt toen u het PFX-bestand exporteerde.

Klik op **Uploaden**.

![Certificaat voor domein uploaden](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Wacht tot Azure uw certificaat heeft geüpload en start het dialoog venster SSL-bindingen.

Selecteer in het dialoog venster SSL-bindingen het certificaat dat u hebt geüpload en het SSL-type en klik vervolgens op **binding toevoegen**.

> [!NOTE]
> De volgende SSL-typen worden ondersteund:
>
> - **[Op SNI gebaseerde SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** : meerdere SSL-bindingen op basis van SNI kunnen worden toegevoegd. Met deze optie kunnen meerdere SSL-certificaten verschillende domeinen beveiligen op hetzelfde IP-adres. De meeste moderne browsers (waaronder Internet Explorer, Chrome, Firefox en Opera) ondersteunen SNI. Ga voor uitgebreidere informatie over browserondersteuning naar [Servernaamindicatie](https://wikipedia.org/wiki/Server_Name_Indication).
> - **Op IP gebaseerde SSL**: er kan slechts één op IP gebaseerde SSL-binding worden toegevoegd. Met deze optie kan slechts één SSL-certificaat een specifiek openbaar IP-adres beveiligen. Als u meerdere domeinen wilt beveiligen, moet u ze allemaal met hetzelfde SSL-certificaat beveiligen. Dit is de traditionele optie voor SSL-binding.

![SSL binden aan het domein](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

De SSL-status van het domein moet nu worden gewijzigd in **beveiligd**.

![Beveiligd domein](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> Een **veilige** status in de **aangepaste domeinen** houdt in dat deze is beveiligd met een certificaat, maar app service controleert niet of het certificaat zelf is ondertekend of is verlopen, bijvoorbeeld, waardoor browsers ook een fout of waarschuwing kunnen weer geven.

## <a name="remap-a-record-for-ip-ssl"></a>Een record voor IP SSL opnieuw toewijzen

Als u geen op IP gebaseerde SSL in uw app gebruikt, gaat u naar [Test HTTPS for your custom domain](#test) (HTTPS voor uw aangepaste domein testen).

Uw app maakt standaard gebruik van een gedeeld openbaar IP-adres. Wanneer u een certificaat met op IP gebaseerde SSL verbindt, maakt App Service een nieuw, specifiek IP-adres voor uw app.

Als u een A-record aan uw app hebt toegewezen, werkt u uw domeinregister bij met dit nieuwe, specifieke IP-adres.

De pagina **Aangepast domein** van uw app wordt bijgewerkt met het nieuwe, specifieke IP-adres. [Kopieer dit IP-adres](app-service-web-tutorial-custom-domain.md#info) en [wijs de A-record opnieuw toe](app-service-web-tutorial-custom-domain.md#map-an-a-record) aan dit nieuwe IP-adres.

<a name="test"></a>

## <a name="test-https"></a>HTTPS testen

Nu hoeft u alleen nog maar te controleren of HTTPS werkt voor uw aangepaste domein. Browse in verschillende browsers naar `https://<your.custom.domain>` om te controleren of het naar uw app leidt.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Als uw app certificaatvalidatiefouten geeft, gebruikt u waarschijnlijk een zelfondertekend certificaat.
>
> Als dit niet het geval is, hebt u mogelijk tussenliggende certificaten weggelaten toen u uw certificaat naar het PFX-bestand exporteerde.

## <a name="renew-certificates"></a>Certificaten vernieuwen

Uw inkomende IP-adres kan wijzigen wanneer u een binding verwijdert, zelfs als die binding IP-gebaseerd is. Dit is vooral belangrijk wanneer u een certificaat vernieuwt dat zich al in een IP-gebaseerde binding bevindt. Als u een wijziging in het IP-adres van uw app wilt voorkomen, volgt u in volgorde de volgende stappen:

1. Upload het nieuwe certificaat.
2. Verbind het nieuwe certificaat aan het aangepaste domein dat u wilt, zonder het oude certificaat te verwijderen. Met deze actie wordt de oude binding vervangen en niet verwijderd.
3. Verwijder het oude certificaat. 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>HTTPS afdwingen

Standaard heeft iedereen nog steeds toegang tot uw app via HTTP. U kunt alle HTTP-aanvragen omleiden naar de HTTPS-poort.

Selecteer in het linkernavigatievenster van de app-pagina **SSL-instellingen**. Klik op **Alleen HTTPS** en selecteer **Aan**.

![HTTPS afdwingen](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Wanneer de bewerking is voltooid, gaat u naar een van de HTTP-URL's die naar uw app verwijzen. Bijvoorbeeld:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS-versies afdwingen

Voor uw app is standaard [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 toegestaan, wat het aanbevolen TLS-niveau is volgens industrienormen zoals [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Als u andere TLS-versies wilt afdwingen, volgt u deze stappen:

Selecteer in het linkernavigatievenster van de app-pagina **SSL-instellingen**. Selecteer vervolgens bij **TLS-versie** de gewenste minimale TLS-versie. Met deze instelling controleert u alleen de inkomende oproepen. 

![TLS 1.1 of 1.2 afdwingen](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Als de bewerking is voltooid, worden in de app alle verbindingen met lagere TLS-versies geweigerd.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U kunt SSL-bindingen voor uw app met scripts automatiseren met behulp van de [Azure CLI](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure-CLI

Met de volgende opdracht wordt een geëxporteerd PFX-bestand geüpload en de vingerafdruk opgehaald.

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

Met de volgende opdracht wordt een op SNI gebaseerde SSL-binding toegevoegd met behulp van de vingerafdruk van de vorige opdracht.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Met de volgende opdracht wordt de app gedwongen HTTPS te gebruiken.

```azurecli-interactive
az webapp update \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --https-only true
```

Met de volgende opdracht wordt een minimale TLS-versie van TLS 1.2 afgedwongen.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met de volgende opdracht wordt een geëxporteerd PFX-bestand geüpload en een op SNI gebaseerde SSL-binding toegevoegd.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>Certificaten gebruiken in uw code

Als uw app verbinding moet maken met externe bronnen en de externe bron verificatie van certificaten vereist, kunt u open bare of persoonlijke certificaten uploaden naar uw app. U hoeft deze certificaten niet te binden aan een aangepast domein in uw app. Zie [Use an SSL certificate in your application code in Azure App Service](app-service-web-ssl-cert-load.md) (Een SSL-certificaat gebruiken in uw toepassingscode in Azure App Service) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * De prijscategorie van uw app upgraden
> * Uw aangepaste certificaat met App Service binden
> * Certificaten vernieuwen
> * HTTPS afdwingen
> * TLS 1.1/1.2 afdwingen
> * TLS-beheer automatiseren met scripts

Ga naar de volgende zelfstudie voor meer informatie over het gebruik van Azure Content Delivery Network.

> [!div class="nextstepaction"]
> [Een netwerk voor contentlevering toevoegen aan een Azure App Service](../cdn/cdn-add-to-web-app.md)

Zie [Use an SSL certificate in your application code in Azure App Service](app-service-web-ssl-cert-load.md) (Een SSL-certificaat gebruiken in uw toepassingscode in Azure App Service) voor meer informatie.
