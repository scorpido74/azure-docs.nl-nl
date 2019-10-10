---
title: Een SSL-certificaat kopen en configureren vanuit Azure-App Service | Microsoft Docs
description: Meer informatie over het kopen van een App Service certificaat en het koppelen aan uw App Service-app
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cephalin
ms.reviewer: apurvajo
ms.custom: seodec18
ms.openlocfilehash: 9569928882bcaaa1d2406c9af1b2197c2ba6e93d
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177523"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Een SSL-certificaat voor Azure App Service kopen en configureren

In deze zelf studie ziet u hoe u uw [app service app](https://docs.microsoft.com/azure/app-service/) of [functie-app](https://docs.microsoft.com/azure/azure-functions/) kunt beveiligen door een app service certificaat te maken (te kopen) in [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) en het vervolgens te binden aan een app service-app.

> [!TIP]
> App Service certificaten kunnen worden gebruikt voor alle Azure-of niet-Azure-Services en is niet beperkt tot App Services. Als u dit wilt doen, moet u een lokaal PFX-exemplaar maken van een App Service certificaat dat u overal kunt gebruiken. Zie [een lokale pfx-kopie maken van een app service Certificate](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/)voor meer informatie.
>

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt volgen:

- [Een App Service-app maken](/azure/app-service/)
- [Een domein naam toewijzen aan uw app](app-service-web-tutorial-custom-domain.md) of [kopen en configureren in azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Certificaat volgorde starten

Start een App Service-certificaat volgorde op de <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">pagina app service Certificate maken</a>.

![Certificaat maken](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Gebruik de volgende tabel om u te helpen bij het configureren van het certificaat. Klik op **Create** als u klaar bent.

| Instelling | Beschrijving |
|-|-|
| Naam | Een beschrijvende naam voor uw App Service certificaat. |
| Naam van het Blot-domein | Geef hier het hoofd domein op. Het verleende certificaat beveiligt *zowel* het hoofd domein als het subdomein `www`. In het verleende certificaat bevat het veld algemene naam het hoofd domein en het veld alternatieve naam voor onderwerp bevat het domein `www`. Als u alleen een subdomein wilt beveiligen, geeft u de Fully Qualified Domain Name van het subdomein hier op (bijvoorbeeld `mysubdomain.contoso.com`).|
| Abonnement | Het datacenter waar de web-app wordt gehost. |
| Resourcegroep | De resource groep die het certificaat bevat. U kunt een nieuwe resource groep gebruiken of dezelfde resource groep selecteren als uw App Service-app, bijvoorbeeld. |
| Certificaat-SKU | Hiermee wordt het type certificaat bepaald dat moet worden gemaakt, of het een standaard certificaat of een [certificaat voor joker tekens](https://wikipedia.org/wiki/Wildcard_certificate)is. |
| Juridische voor waarden | Klik om te bevestigen dat u akkoord gaat met de juridische voor waarden. De certificaten worden opgehaald van GoDaddy. |

## <a name="store-in-azure-key-vault"></a>Opslaan in Azure Key Vault

Zodra het aankoop proces van het certificaat is voltooid, zijn er nog enkele stappen die u moet volt ooien voordat u dit certificaat kunt gaan gebruiken. 

Selecteer het certificaat op de pagina [app service certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en klik vervolgens op **certificaat configuratie** > **stap 1: opslaan**.

![afbeelding van gereed voor Store invoegen in KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) is een Azure-service die helpt bij het beveiligen van cryptografische sleutels en geheimen die worden gebruikt door Cloud toepassingen en-services. Het is de opslag van de keuze voor App Service certificaten.

Klik op de pagina **Key Vault status** op **Key Vault opslag plaats** om een nieuwe kluis te maken of kies een bestaande kluis. Als u ervoor kiest om een nieuwe kluis te maken, gebruikt u de volgende tabel om u te helpen de kluis te configureren en klikt u op **maken**.

| Instelling | Beschrijving |
|-|-|
| Naam | Een unieke naam die bestaat uit alfanumerieke tekens en afbreek streepjes. |
| Resourcegroep | Als aanbeveling selecteert u dezelfde resource groep als uw App Service certificaat. |
| Locatie | Selecteer dezelfde locatie als uw App Service-app. |
| Prijscategorie | Zie [Azure Key Vault prijs informatie](https://azure.microsoft.com/pricing/details/key-vault/)voor meer informatie. |
| Toegangs beleid| Hiermee worden de toepassingen en de toegestane toegang tot de kluis resources gedefinieerd. U kunt deze later configureren, door de stappen [te volgen om verschillende toepassingen toegang te verlenen tot een sleutel kluis](../key-vault/key-vault-group-permissions-for-apps.md). |
| Toegang Virtual Network | Beperk de toegang tot de kluis tot bepaalde virtuele netwerken van Azure. U kunt dit later configureren door de stappen te volgen op [Azure Key Vault firewalls en virtuele netwerken configureren](../key-vault/key-vault-network-security.md) |

Wanneer u de kluis hebt geselecteerd, sluit u de pagina **Key Vault opslag plaats** . De optie **Archief** moet een groen vinkje weer geven voor geslaagde pogingen. Laat de pagina voor de volgende stap geopend.

## <a name="verify-domain-ownership"></a>Domein eigendom verifiëren

Klik op de pagina **certificaat configuratie** die u in de laatste stap hebt gebruikt, op **stap 2: controleren**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Selecteer **app service verificatie**. Omdat u het domein al aan uw web-app hebt toegewezen (Zie [vereisten](#prerequisites)), is het al gecontroleerd. Klik op **verifiëren** om deze stap te volt ooien. Klik op de knop **vernieuwen** totdat het bericht **certificaat is geverifieerd op domein** wordt weer gegeven.

> [!NOTE]
> Er worden vier typen domein verificatie methoden ondersteund: 
> 
> - **App service** : de handigste optie wanneer het domein al is toegewezen aan een app service-app in hetzelfde abonnement. Er wordt gebruikgemaakt van het feit dat de App Service-app al het eigendom van het domein heeft gecontroleerd.
> - **Domein** : een [app service domein controleren dat u hebt aangeschaft vanuit Azure](manage-custom-dns-buy-domain.md). Azure voegt de bevestigings-TXT-record automatisch voor u toe en voltooit het proces.
> - **E-mail** : Controleer het domein door een e-mail te verzenden naar de domein beheerder. Er worden instructies gegeven wanneer u de optie selecteert.
> - **Hand matig** : Verifieer het domein met behulp van een HTML-pagina (alleen**standaard** certificaat) of een DNS TXT-record. Er worden instructies gegeven wanneer u de optie selecteert.

## <a name="bind-certificate-to-app"></a>Certificaat binden aan de app

Selecteer in het menu links in het **[Azure Portal](https://portal.azure.com/)** **app Services** >  **\<your_ app->** .

Selecteer in de linkernavigatiebalk van uw app SSL- **instellingen** > **Private certificaten (. pfx)**  > **import app service Certificate**.

![afbeelding van import certificaat invoegen](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Selecteer het certificaat dat u zojuist hebt aangeschaft.

Nu het certificaat is geïmporteerd, moet u het binden aan een toegewezen domein naam in uw app. Selecteer **bindingen** > **SSL-binding toevoegen**. 

![afbeelding van import certificaat invoegen](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Gebruik de volgende tabel om u te helpen de binding te configureren in het dialoog venster **SSL-bindingen** en klik vervolgens op **binding toevoegen**.

| Instelling | Beschrijving |
|-|-|
| Hostnaam | De domein naam waaraan u SSL-binding wilt toevoegen. |
| Vinger afdruk van persoonlijk certificaat | Het certificaat dat moet worden gebonden. |
| SSL-type | <ul><li>**SNI SSL** -meerdere op SNI gebaseerde SSL-bindingen kunnen worden toegevoegd. Met deze optie kunnen meerdere SSL-certificaten verschillende domeinen beveiligen op hetzelfde IP-adres. De meeste moderne browsers (waaronder Internet Explorer, Chrome, Firefox en Opera) ondersteunen SNI. Ga voor uitgebreidere informatie over browserondersteuning naar [Servernaamindicatie](https://wikipedia.org/wiki/Server_Name_Indication).</li><li>**Op IP gebaseerde SSL**: er kan slechts één op IP gebaseerde SSL-binding worden toegevoegd. Met deze optie kan slechts één SSL-certificaat een specifiek openbaar IP-adres beveiligen. Nadat u de binding hebt geconfigureerd, volgt u de stappen in [een record opnieuw toewijzen voor IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>HTTPS-toegang controleren

Ga naar uw app met `HTTPS://<domain_name>` in plaats van `HTTP://<domain_name>` om te controleren of het certificaat juist is geconfigureerd.

## <a name="rekey-certificate"></a>Certificaat opnieuw genereren

Als u denkt dat de persoonlijke sleutel van uw certificaat is aangetast, kunt u uw certificaat opnieuw genereren. Selecteer het certificaat op de pagina [app service certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en selecteer vervolgens opnieuw **genereren en synchroniseren** vanuit de linkernavigatiebalk.

Klik op opnieuw **genereren** om het proces te starten. Dit proces kan 1-10 minuten duren.

![afbeelding van het SSL-bestand voor opnieuw genereren invoegen](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Als u het certificaat opnieuw keying, wordt het certificaat gedistribueerd met een nieuw certificaat dat is uitgegeven door de certificerings instantie.

Zodra de bewerking voor opnieuw genereren is voltooid, klikt u op **synchroniseren**. Met de synchronisatie bewerking worden de hostname-bindingen voor het certificaat in App Service automatisch bijgewerkt zonder dat er uitval tijd voor uw apps wordt veroorzaakt.

> [!NOTE]
> Als u niet op **synchroniseren**klikt, wordt app service uw certificaat automatisch gesynchroniseerd binnen 48 uur.

## <a name="renew-certificate"></a>Certificaat vernieuwen

Als u de automatische verlenging van uw certificaat op elk gewenst moment wilt inschakelen, selecteert u het certificaat op de pagina [app service certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en klikt u vervolgens op **instellingen voor automatisch verlengen** in het linkernavigatievenster. App Service certificaten hebben standaard een geldigheids periode van 1 jaar.

Selecteer **aan** en klik op **Opslaan**. Certificaten kunnen 60 dagen vóór de verval datum automatisch worden vernieuwd als u automatische verlenging hebt ingeschakeld.

![certificaat automatisch vernieuwen](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Als u het certificaat hand matig wilt vernieuwen, klikt u op **hand matig verlengen**. U kunt aanvragen om uw certificaat 60 dagen voor de verval datum hand matig te verlengen.

Zodra de vernieuwings bewerking is voltooid, klikt u op **synchroniseren**. Met de synchronisatie bewerking worden de hostname-bindingen voor het certificaat in App Service automatisch bijgewerkt zonder dat er uitval tijd voor uw apps wordt veroorzaakt.

> [!NOTE]
> Als u niet op **synchroniseren**klikt, wordt app service uw certificaat automatisch gesynchroniseerd binnen 48 uur.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Meer informatiebronnen

* [HTTPS afdwingen](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [TLS 1.1/1.2 afdwingen](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Gebruik een SSL-certificaat in de code van uw toepassing in Azure App Service](app-service-web-ssl-cert-load.md)
* [Veelgestelde vragen: App Service certificaten](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
