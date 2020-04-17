---
title: Een actieve DNS-naam migreren
description: Meer informatie over het migreren van een aangepaste DNS-domeinnaam die al is toegewezen aan een live-site naar Azure App Service zonder downtime.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 5c1760c746aca439e19ab5727e5be02f6dbad3cb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535686"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Een actieve DNS-naam migreren naar Azure App Service

In dit artikel ziet u hoe u een actieve DNS-naam migreert naar [Azure App Service](../app-service/overview.md) zonder enige downtime.

Wanneer u een live-site en de DNS-domeinnaam migreert naar App Service, wordt die DNS-naam al actief voor live verkeer. U downtime in DNS-resolutie tijdens de migratie voorkomen door de actieve DNS-naam preventief aan uw App Service-app te binden.

Zie [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](app-service-web-tutorial-custom-domain.md)als u zich geen zorgen maakt over downtime in DNS-resolutie.

## <a name="prerequisites"></a>Vereisten

Om deze how-to te voltooien:

- [Zorg ervoor dat uw App Service-app niet in DE VRIJE laag staat.](app-service-web-tutorial-custom-domain.md#checkpricing)

## <a name="bind-the-domain-name-preemptively"></a>De domeinnaam preventief binden

Wanneer u een aangepast domein preventief bindt, voert u beide volgende handelingen uit voordat u wijzigingen aanbrengt in uw DNS-records:

- Domeineigendom verifiëren
- De domeinnaam voor uw app inschakelen

Wanneer u eindelijk uw aangepaste DNS-naam migreert van de oude site naar de App Service-app, is er geen downtime in DNS-resolutie.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Domeinverificatierecord maken

Als u het eigendom van het domein wilt verifiëren, voegt u een TXT-record toe. De TXT-recordkaarten van _awverify.&lt; subdomein>_ _ &lt;naar appname>.azurewebsites.net_. 

De TXT-record die u nodig hebt, is afhankelijk van de DNS-record die u wilt migreren. Zie bijvoorbeeld de volgende tabel`@` (vertegenwoordigt meestal het hoofddomein):

| Voorbeeld van DNS-record | TXT-host | TXT-waarde |
| - | - | - |
| \@(wortel) | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| www (sub) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \*(wildcard) | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

Noteer op de pagina DNS-records het recordtype van de DNS-naam die u wilt migreren. App Service ondersteunt toewijzingen van CNAME- en A-records.

> [!NOTE]
> Voor bepaalde providers, zoals `awverify.*` CloudFlare, is geen geldige record. Gebruik `*` alleen in plaats daarvan.

> [!NOTE]
> Jokertekens `*` valideren geen subdomeinen met de record van een bestaande CNAME. Mogelijk moet u voor elk subdomein expliciet een TXT-record maken.


### <a name="enable-the-domain-for-your-app"></a>Het domein voor uw app inschakelen

Selecteer **aangepaste domeinen**in de [Azure-portal](https://portal.azure.com)in de linkernavigatie van de app-pagina . 

![Menu voor aangepaste domeinen](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Selecteer **op** de pagina **+** Aangepaste domeinen het pictogram naast **Hostname toevoegen**.

![Hostnaam toevoegen](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Typ de volledig gekwalificeerde domeinnaam waarvoor u de TXT-record hebt toegevoegd, zoals `www.contoso.com`. Voor een wildcarddomein \*(zoals .contoso.com) u elke DNS-naam gebruiken die overeenkomt met het wildcarddomein. 

Selecteer **Valideren**.

De knop **Hostnaam toevoegen** wordt geactiveerd. 

Controleer of **het recordtype Hostname** is ingesteld op het DNS-recordtype dat u wilt migreren.

Selecteer **Hostnaam toevoegen**.

![DNS-naam toevoegen aan de app](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Het kan even duren voor de nieuwe hostnaam wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser voor om de gegevens bij te werken.

![CNAME-record toegevoegd](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Uw aangepaste DNS-naam is nu ingeschakeld in uw Azure-app. 

## <a name="remap-the-active-dns-name"></a>De actieve DNS-naam opnieuw toewijzen

Het enige wat u nog moet doen, is het opnieuw toewijzen van uw actieve DNS-record om naar App Service te wijzen. Op dit moment, het wijst nog steeds naar uw oude site.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Het IP-adres van de app kopiëren (alleen een record)

Als u een CNAME-record opnieuw in kaart brengt, slaat u deze sectie over. 

Als u een A-record opnieuw wilt toewijzen, hebt u het externe IP-adres van de App Service-app nodig, dat wordt weergegeven op de pagina **Aangepaste domeinen.**

Sluit de pagina **Hostname toevoegen** door **X** in de rechterbovenhoek te selecteren. 

Op de pagina **Aangepaste domeinen** kopieert u het IP-adres van de app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>De DNS-record bijwerken

Selecteer terug op de pagina DNS-records van uw domeinprovider de DNS-record die u opnieuw wilt toewijzen.

Breng `contoso.com` voor het voorbeeld van het hoofddomein de A- of CNAME-record opnieuw in kaart, zoals de voorbeelden in de volgende tabel: 

| FQDN voorbeeld | Recordtype | Host | Waarde |
| - | - | - | - |
| contoso.com (wortel) | A | `@` | IP-adres uit [Het IP-adres van de app kopiëren](#info) |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*.contoso.com (wildcard) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

Sla uw wijzigingen op.

DNS-query's moeten onmiddellijk na dns-propagatie worden opgelost in uw App Service-app.

## <a name="active-domain-in-azure"></a>Actief domein in Azure

U een actief aangepast domein migreren in Azure, tussen abonnementen of binnen hetzelfde abonnement. Een dergelijke migratie zonder downtime vereist echter de bron-app en de doel-app krijgt op een bepaald moment hetzelfde aangepaste domein toegewezen. Daarom moet u ervoor zorgen dat de twee apps niet worden geïmplementeerd op dezelfde implementatie-eenheid (intern bekend als een webspace). Een domeinnaam kan worden toegewezen aan slechts één app in elke implementatie-eenheid.

U de implementatie-eenheid voor uw app vinden door te `<deployment-unit>.ftp.azurewebsites.windows.net`kijken naar de domeinnaam van de FTP/S-URL. Controleer en controleer of de implementatie-eenheid verschilt tussen de bron-app en de doel-app. De implementatie-eenheid van een app wordt bepaald door het [App Service-abonnement](overview-hosting-plans.md) waarin deze zich bevindt. Azure selecteert het willekeurig wanneer u het abonnement maakt en kan niet worden gewijzigd. Azure zorgt er alleen voor dat twee abonnementen zich in dezelfde implementatie-eenheid bevinden wanneer u [ze maakt in dezelfde resourcegroep *en* dezelfde regio,](app-service-plan-manage.md#create-an-app-service-plan)maar het heeft geen logica om ervoor te zorgen dat plannen zich in verschillende implementatie-eenheden bevinden. De enige manier waarop u een plan in een andere implementatie-eenheid maken, is door een plan te blijven maken in een nieuwe resourcegroep of -regio totdat u een andere implementatie-eenheid hebt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het binden van een aangepast TLS/SSL-certificaat aan App Service.

> [!div class="nextstepaction"]
> [Een aangepaste DNS-naam beveiligen met een TLS-binding in Azure App Service](configure-ssl-bindings.md)
