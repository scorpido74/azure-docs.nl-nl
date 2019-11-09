---
title: Bestaande aangepaste DNS-naam toewijzen - Azure App Service | Microsoft Docs
description: Informatie over het toevoegen van een bestaande aangepaste DNS-domeinnaam (vanity-domein) aan een web-app, back-end voor een mobiele app of een API-app in Azure App Service.
keywords: app service, azure app service, domeintoewijzing, domeinnaam, bestaand domein, hostnaam
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fa8acbab8179eea752607c4410851d74ae4e9444
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835860"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Zelf studie: een bestaande aangepaste DNS-naam toewijzen aan Azure App Service

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In deze zelfstudie ziet u hoe u een bestaande aangepaste DNS-naam toewijst aan Azure App Service.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een subdomein toewijzen (bijvoorbeeld `www.contoso.com`) met behulp van een CNAME-record
> * Een hoofddomein toewijzen (bijvoorbeeld `contoso.com`) met behulp van een A-record
> * Een wildcard-domein toewijzen (bijvoorbeeld `*.contoso.com`) met behulp van een CNAME-record
> * Standaard-URL omleiden naar een aangepaste map
> * Domeintoewijzing met scripts automatiseren

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

* [Maak een App Service-app](/azure/app-service/), of gebruik een app die u hebt gemaakt voor een andere zelfstudie.
* Koop een domeinnaam en controleer of u toegang hebt tot het DNS-register voor uw domeinprovider (zoals GoDaddy).

  Als u bijvoorbeeld DNS-vermeldingen voor `contoso.com` en `www.contoso.com` wilt toevoegen, moet u de DNS-instellingen voor het hoofddomein van `contoso.com` kunnen configureren.

  > [!NOTE]
  > Als u geen bestaande domeinnaam heeft, denk dan na over het [aanschaffen van een domein met de Azure portal](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>De app voorbereiden

Om een aangepaste DNS-naam toe te wijzen aan een web-app, moet het [App Service-plan](https://azure.microsoft.com/pricing/details/app-service/) van de web-app een betaalde categorie zijn (**Shared**, **Basic**, **Standard**,  **Premium** of **Consumption** voor Azure Functions). In deze stap zorgt u ervoor dat de App Service-app zich in de ondersteunde prijscategorie bevindt.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open de [Azure-portal](https://portal.azure.com) en meld u aan met uw Azure-account.

### <a name="select-the-app-in-the-azure-portal"></a>Selecteer de app in de Azure Portal

Zoek en selecteer **app Services**.

![App Services selecteren](./media/app-service-web-tutorial-custom-domain/app-services.png)

Selecteer op de pagina **app Services** de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/select-app.png)

U ziet de beheerpagina van de App Service-app.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Controleer de prijscategorie

Schuif in de navigatiebalk links van de app-pagina naar de sectie **Instellingen** en selecteer **Opschalen (App Service-plan)** .

![Menu Opschalen](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

De huidige laag van de app wordt gemarkeerd door een blauwe rand. Controleer of de app zich niet in de **F1**-laag bevindt. Aangepaste DNS wordt niet ondersteund in de **F1**-laag. 

![Controleer prijscategorie](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Als het App Service-plan zich niet in de **F1**-laag bevindt, sluit u de pagina **Omhoog schalen** en gaat u door met [Een CNAME-record toewijzen](#cname).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Het App Service-plan opschalen

Selecteer een van de lagen die niet gratis zijn (**D1**, **B1**, **B2**, **B3** of een laag in de categorie **Productie**). Klik op **Aanvullende opties bekijken** voor aanvullende opties.

Klik op **Toepassen**.

![Controleer prijscategorie](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Wanneer u de volgende melding ziet, is de schaalbewerking voltooid.

![Schaalbewerking bevestigen](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="map-your-domain"></a>Uw domein toewijzen

U kunt ofwel een **CNAME-record** of een **A-record** gebruiken voor het toewijzen van een aangepaste DNS-naam aan App Service. Volg de bijbehorende stappen:

- [Een CNAME-record toewijzen](#map-a-cname-record)
- [Een A-record toewijzen](#map-an-a-record)
- [Een wildcard-domein toewijzen (met behulp van een CNAME-record)](#map-a-wildcard-domain)

> [!NOTE]
> U moet CNAME-records gebruiken voor alle aangepaste DNS-namen, behalve voor het hoofddomein (bijvoorbeeld `contoso.com`). Voor hoofddomeinen gebruikt u A-records.

### <a name="map-a-cname-record"></a>Een CNAME-record toewijzen

In het voorbeeld van de zelfstudie voegt u toe een CNAME-record toe voor het subdomein `www` (bijvoorbeeld `www.contoso.com`).

#### <a name="access-dns-records-with-domain-provider"></a>Toegang tot DNS-records via domeinprovider

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Het CNAME-record maken

Voeg een CNAME-record toe om een subdomein toe te wijzen aan de standaard domein naam van de app (`<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam van uw app is).

Voeg voor het voorbeelddomein `www.contoso.com` een CNAME-record, dat is toegewezen aan de naam `www`, toe aan `<app_name>.azurewebsites.net`.

Nadat u de CNAME toevoegt, lijkt de pagina DNS-records op het volgende voorbeeld:

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>De toewijzing van het CNAME-record in Azure inschakelen

Selecteer in het linkernavigatievenster van de app-pagina in de Azure portal **Aangepaste domeinen**.

![Menu voor aangepaste domeinen](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Voeg op de pagina **Aangepaste domeinen** van de app de volledig gekwalificeerde aangepaste DNS-naam (`www.contoso.com`) toe aan de lijst.

Selecteer het **+** pictogram naast **aangepast domein toevoegen**.

![Hostnaam toevoegen](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Typ de volledig gekwalificeerde domeinnaam in waarvoor u een CNAME-record zoals `www.contoso.com` heeft toegevoegd.

Selecteer **Valideren**.

De pagina **aangepast domein toevoegen** wordt weer gegeven.

Zorg ervoor dat het **hostnaam-record type** is ingesteld op **CNAME (www\.example.com of een ander subdomein)** .

Selecteer **Aangepast domein toevoegen**.

![DNS-naam toevoegen aan de app](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Het kan enige tijd duren voordat het nieuwe aangepaste domein wordt weer gegeven op de pagina **aangepaste domeinen** van de app. Vernieuw de browser voor om de gegevens bij te werken.

![CNAME-record toegevoegd](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> Een **niet-beveiligd** label voor uw aangepaste domein betekent dat het nog niet is gebonden aan een SSL-certificaat en dat de HTTPS-aanvraag van een browser naar uw aangepaste domein, afhankelijk van de browser, wordt ontvangen en fout of waarschuwing is. Als u een SSL-binding wilt toevoegen, raadpleegt u [een aangepaste DNS-naam beveiligen met een SSL-binding in azure app service](configure-ssl-bindings.md).

Als u een stap hebt gemist of eerder ergens een typefout hebt gemaakt, ziet u een verificatie-foutmelding aan de onderkant van de pagina.

![Verificatiefout](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Toewijzen van een A-record

In het voorbeeld van de zelfstudie voegt u toe een A-record toe voor het hoofddomein (bijvoorbeeld `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Het IP-adres van de app kopiëren

Als u een A-record wilt toewijzen, heeft u het externe IP-adres van de app nodig. U vindt dit IP-adres op pagina **Aangepaste domeinen** van de app in de Azure-portal.

Selecteer in het linkernavigatievenster van de app-pagina in de Azure portal **Aangepaste domeinen**.

![Menu voor aangepaste domeinen](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Op de pagina **Aangepaste domeinen** kopieert u het IP-adres van de app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>Toegang tot DNS-records via domeinprovider

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Een A-record maken

Als u een A-record wilt toewijzen aan een app, vereist App Service **twee** DNS-records:

- Een **A**-record toewijzen aan het IP-adres van de app.
- Een **txt** -record die wordt toegewezen aan de standaard domein naam van de app `<app_name>.azurewebsites.net`. App Service gebruikt dit record alleen tijdens de configuratie, om te controleren of u de eigenaar bent van het aangepaste domein. Nadat uw aangepaste domein is gevalideerd en geconfigureerd in App Service, kunt u dit TXT-record verwijderen.

Maak voor het domeinvoorbeeld `contoso.com` de A- en TXT-records overeenkomstig de volgende tabel (`@` vertegenwoordigt doorgaans het hoofddomein).

| Recordtype | Host | Waarde |
| - | - | - |
| A | `@` | IP-adres uit [Het IP-adres van de app kopiëren](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

> [!NOTE]
> Als u een subdomein (zoals `www.contoso.com`) wilt toevoegen met behulp van een A-record in plaats van een aanbevolen [CNAME-record](#map-a-cname-record), moeten uw A-record en TXT-record eruitzien zoals in de volgende tabel:
>
> | Recordtype | Host | Waarde |
> | - | - | - |
> | A | `www` | IP-adres uit [Het IP-adres van de app kopiëren](#info) |
> | TXT | `www` | `<app_name>.azurewebsites.net` |
>

Wanneer de records worden toegevoegd, lijkt de pagina met DNS-records op het volgende voorbeeld:

![Pagina met DNS-records](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>De toewijzing van de A-record in de app inschakelen

Voeg op de pagina **Aangepaste domeinen** van de app in de Azure portal de volledig gekwalificeerde aangepaste DNS-naam (bijvoorbeeld `contoso.com`) toe aan de lijst.

Selecteer het **+** pictogram naast **aangepast domein toevoegen**.

![Hostnaam toevoegen](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Typ de volledig gekwalificeerde domeinnaam in waarvoor u een A-record zoals `contoso.com` heeft toegevoegd.

Selecteer **Valideren**.

De pagina **aangepast domein toevoegen** wordt weer gegeven.

Zorg ervoor dat **Hostnaam recordtype** is ingesteld op **A-record (voorbeeld.com)** .

Selecteer **Aangepast domein toevoegen**.

![DNS-naam toevoegen aan de app](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Het kan enige tijd duren voordat het nieuwe aangepaste domein wordt weer gegeven op de pagina **aangepaste domeinen** van de app. Vernieuw de browser voor om de gegevens bij te werken.

![A-record toegevoegd](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> Een **niet-beveiligd** label voor uw aangepaste domein betekent dat het nog niet is gebonden aan een SSL-certificaat en dat de HTTPS-aanvraag van een browser naar uw aangepaste domein, afhankelijk van de browser, wordt ontvangen en fout of waarschuwing is. Als u een SSL-binding wilt toevoegen, raadpleegt u [een aangepaste DNS-naam beveiligen met een SSL-binding in azure app service](configure-ssl-bindings.md).

Als u een stap hebt gemist of eerder ergens een typefout hebt gemaakt, ziet u een verificatie-foutmelding aan de onderkant van de pagina.

![Verificatiefout](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Een wildcard-domein toewijzen

In het voorbeeld van de zelfstudie, wijst u een [wildcard-DNS-naam](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (bijvoorbeeld `*.contoso.com`) toe aan de App Service-app door een CNAME-record toe te voegen.

#### <a name="access-dns-records-with-domain-provider"></a>Toegang tot DNS-records via domeinprovider

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Het CNAME-record maken

Voeg een CNAME-record toe om een naam van een Joker teken toe te wijzen aan de standaard domein naam van de app (`<app_name>.azurewebsites.net`).

Voor het `*.contoso.com` domeinvoorbeeld zal het CNAME-record de naam `*` toewijzen naar `<app_name>.azurewebsites.net`.

Wanneer de CNAME wordt toegevoegd, lijkt de pagina met DNS-records op het volgende voorbeeld:

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>De toewijzing van het CNAME-record in de app inschakelen

U kunt nu elk subdomein dat overeenkomt met de wildcard-naam aan de app toevoegen (bijvoorbeeld `sub1.contoso.com` en `sub2.contoso.com` komen overeen met `*.contoso.com`).

Selecteer in het linkernavigatievenster van de app-pagina in de Azure portal **Aangepaste domeinen**.

![Menu voor aangepaste domeinen](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Selecteer het **+** pictogram naast **aangepast domein toevoegen**.

![Hostnaam toevoegen](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Typ een FQDN-naam die overeenkomt met het wildcard-domein (bijvoorbeeld `sub1.contoso.com`), en selecteer vervolgens **Valideren**.

De knop **aangepast domein toevoegen** wordt geactiveerd.

Zorg ervoor dat het **hostnaam-record type** is ingesteld op **CNAME-record (www\.example.com of een wille keurig subdomein)** .

Selecteer **Aangepast domein toevoegen**.

![DNS-naam toevoegen aan de app](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Het kan enige tijd duren voordat het nieuwe aangepaste domein wordt weer gegeven op de pagina **aangepaste domeinen** van de app. Vernieuw de browser voor om de gegevens bij te werken.

Selecteer nogmaals het **+** pictogram om een ander aangepast domein toe te voegen dat overeenkomt met het Joker teken domein. Bijvoorbeeld: voeg `sub2.contoso.com` toe.

![CNAME-record toegevoegd](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> Een **beveiligde notitie** label voor uw aangepaste domein betekent dat het nog niet is gebonden aan een SSL-certificaat en dat alle HTTPS-aanvragen van een browser naar uw aangepaste domein worden ontvangen en fout of waarschuwing, afhankelijk van de browser. Als u een SSL-binding wilt toevoegen, raadpleegt u [een aangepaste DNS-naam beveiligen met een SSL-binding in azure app service](configure-ssl-bindings.md).

## <a name="test-in-browser"></a>Testen in browser

Blader naar de DNS-namen die u eerder hebt geconfigureerd (bijvoorbeeld `contoso.com`, `www.contoso.com`, `sub1.contoso.com`, en `sub2.contoso.com`).

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>404-fout 'Niet gevonden' oplossen

Als u een HTTP 404 (niet gevonden)-fout ontvangt bij het bladeren naar de URL van uw aangepaste domein, moet u controleren of uw domein wordt omgezet naar uw app IP-adres met <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Als dit niet gebeurt, kan dit een van de volgende oorzaken hebben:

- Bij het geconfigureerde aangepaste domein ontbreekt een A-record en/of een CNAME-record.
- De browserclient heeft het oude IP-adres van uw domein in de cache. Leeg de cache en test DNS-omzetting opnieuw. Op een Windows-computer, leegt u de cache met `ipconfig /flushdns`.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>Een actief domein migreren

Zie voor het zonder downtime migreren van een live site en de DNS-domeinnaam naar App Service [Een actieve DNS-naam migreren naar Azure App Service](manage-custom-dns-migrate-domain.md).

## <a name="redirect-to-a-custom-directory"></a>Een aangepaste map omleiden

Standaard stuurt App Service webaanvragen naar de hoofdmap van uw app-code. Bepaalde web-frameworks starten echter niet in de hoofdmap. Bijvoorbeeld: [Laravel](https://laravel.com/) start in de submap `public`. Om door te gaan met het DNS-voorbeeld `contoso.com`, is een dergelijke app toegankelijk op `http://contoso.com/public`, maar u moet in plaats daarvan eigenlijk `http://contoso.com` naar de map `public` sturen. Deze stap heeft geen betrekking op DNS-omzetting, maar het aanpassen van de virtuele map.

Om dit te doen, selecteert u **Toepassingsinstellingen** in de linkernavigatiebalk van uw web-app-pagina. 

Aan de onderkant van de pagina verwijst de virtuele hoofdmap `/` standaard naar `site\wwwroot`. Dit is de hoofdmap van uw app-code. Wijzig deze om in plaats daarvan bijvoorbeeld te verwijzen naar de `site\wwwroot\public` en sla de wijzigingen op.

![Virtuele map aanpassen](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Zodra de bewerking is voltooid, moet uw app de juiste pagina op het hoofdpad (bijvoorbeeld `http://contoso.com`) retour neren.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U kunt het beheer van aangepaste domeinen met scripts automatiseren met behulp van de [Azure CLI](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>Azure CLI 

De volgende opdracht voegt een geconfigureerde aangepaste DNS-naam toe aan een App Service-app. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Zie voor meer informatie [Een aangepast domein toewijzen aan een web-app](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende opdracht voegt een geconfigureerde aangepaste DNS-naam toe aan een App Service-app.

```powershell  
Set-AzWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net")
```

Zie voor meer informatie [Een aangepast domein toewijzen aan een web-app](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een subdomein toewijzen met behulp van een CNAME-record
> * Een subdomein toewijzen met behulp van een A-record
> * Een wildcard-domein toewijzen met behulp van een CNAME-record
> * Standaard-URL omleiden naar een aangepaste map
> * Domeintoewijzing met scripts automatiseren

Ga door naar de volgende zelfstudie om te leren hoe u een aangepast SSL-certificaat aan een web-app kunt toewijzen.

> [!div class="nextstepaction"]
> [Een aangepaste DNS-naam beveiligen met een SSL-binding in Azure App Service](configure-ssl-bindings.md)
