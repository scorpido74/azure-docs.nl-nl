---
title: Een actieve DNS-naam migreren-Azure App Service | Microsoft Docs
description: Meer informatie over het migreren van een aangepaste DNS-domein naam die al aan een live-site is toegewezen, Azure App Service zonder uitval tijd.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 703a151f801f65b968ecf93eaa97640c22a71bd2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073095"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Een actieve DNS-naam migreren naar Azure App Service

In dit artikel wordt beschreven hoe u een actieve DNS-naam migreert naar [Azure app service](../app-service/overview.md) zonder uitval tijd.

Wanneer u een live-site en de DNS-domein naam naar App Service migreert, is die DNS-naam al bezig met live verkeer. U kunt voor komen dat de DNS-omzetting tijdens de migratie wordt versneld door de actieve DNS-naam te koppelen aan uw App Service app preventief.

Zie [een bestaande aangepaste DNS-naam toewijzen aan Azure app service](app-service-web-tutorial-custom-domain.md)als u zich niet zorgen maakt over uitval tijd in DNS-omzetting.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze procedure:

- [Zorg ervoor dat uw app service-app zich niet in de laag gratis](app-service-web-tutorial-custom-domain.md#checkpricing)bevindt.

## <a name="bind-the-domain-name-preemptively"></a>Bind de domein naam preventief

Wanneer u een aangepast domein preventief bindt, voert u de volgende handelingen uit voordat u wijzigingen aanbrengt in uw DNS-records:

- Domeineigendom controleren
- De domein naam voor uw app inschakelen

Wanneer u ten slotte uw aangepaste DNS-naam migreert van de oude-site naar de App App Service, wordt er geen downtime in DNS-omzetting.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Domein verificatie record maken

Voeg een TXT-record toe om het domein eigendom te controleren. De TXT-record wordt toegewezen vanuit _awverify.&lt; subdomein >_ op  _&lt;appName >. azurewebsites. net_. 

De TXT-record die u nodig hebt, is afhankelijk van de DNS-record die u wilt migreren. Zie de volgende tabel (`@` dit is meestal het hoofd domein) voor voor beelden:

| Voor beeld van DNS-record | TXT-host | TXT-waarde |
| - | - | - |
| \@basis | _awverify_ | _&lt;appName >. azurewebsites. net_ |
| www (sub) | _awverify.www_ | _&lt;appName >. azurewebsites. net_ |
| \*vervanging | _awverify.\*_ | _&lt;appName >. azurewebsites. net_ |

Noteer op de pagina DNS-records het record type van de DNS-naam die u wilt migreren. App Service ondersteunt toewijzingen van CNAME-en A-records.

> [!NOTE]
> Voor bepaalde providers, zoals CloudFlare, `awverify.*` is geen geldige record. Gebruik `*` in plaats daarvan.

> [!NOTE]
> Met `*` Joker records worden subdomeinen met een bestaande CNAME-record niet gevalideerd. U moet mogelijk expliciet een TXT-record maken voor elk subdomein.


### <a name="enable-the-domain-for-your-app"></a>Het domein voor uw app inschakelen

Selecteer in de [Azure Portal](https://portal.azure.com), in de linkernavigatiebalk van de app-pagina, de optie **aangepaste domeinen**. 

![Menu voor aangepaste domeinen](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Selecteer op de pagina **aangepaste domeinen** het **+** pictogram naast **hostnaam toevoegen**.

![Hostnaam toevoegen](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Typ de Fully Qualified Domain Name waarvoor u de TXT-record hebt toegevoegd, zoals `www.contoso.com`. Voor een Joker teken domein ( \*zoals. contoso.com) kunt u een DNS-naam gebruiken die overeenkomt met het Joker teken domein. 

Selecteer **Valideren**.

De knop **Hostnaam toevoegen** wordt geactiveerd. 

Zorg ervoor dat het **hostnaam-record type** is ingesteld op het DNS-record type dat u wilt migreren.

Selecteer **Hostnaam toevoegen**.

![DNS-naam toevoegen aan de app](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Het kan even duren voor de nieuwe hostnaam wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser voor om de gegevens bij te werken.

![CNAME-record toegevoegd](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Uw aangepaste DNS-naam is nu ingeschakeld in uw Azure-app. 

## <a name="remap-the-active-dns-name"></a>De actieve DNS-naam opnieuw toewijzen

Het enige wat u nog moet doen, is het opnieuw toewijzen van uw actieve DNS-record om naar App Service te verwijzen. Op dit moment wijst het nog steeds naar uw oude site.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Het IP-adres van de app kopiëren (alleen een record)

Als u een CNAME-record opnieuw wilt toewijzen, kunt u deze sectie overs Laan. 

Als u een A-record opnieuw wilt toewijzen, hebt u het externe IP-adres van de App Service-app nodig. dit wordt weer gegeven op de pagina **aangepaste domeinen** .

Sluit de pagina **hostname toevoegen** door **X** in de rechter bovenhoek te selecteren. 

Op de pagina **Aangepaste domeinen** kopieert u het IP-adres van de app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>De DNS-record bijwerken

Selecteer op de pagina DNS-records van uw domein provider de DNS-record die u opnieuw wilt toewijzen.

Voor het `contoso.com` voor beeld van het hoofd domein wijst u de A-of CNAME-record toe zoals de voor beelden in de volgende tabel: 

| FQDN-voor beeld | Recordtype | Host | Value |
| - | - | - | - |
| contoso.com (root) | G | `@` | IP-adres uit [Het IP-adres van de app kopiëren](#info) |
| www\.-contoso.com (sub) | CNAME | `www` | _&lt;appName >. azurewebsites. net_ |
| \*. contoso.com (Joker teken) | CNAME | _\*_ | _&lt;appName >. azurewebsites. net_ |

Sla de instellingen op.

DNS-query's moeten meteen beginnen met het oplossen van problemen met de App Service-app nadat de DNS-doorgifte is uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u een aangepast SSL-certificaat verbindt met App Service.

> [!div class="nextstepaction"]
> [Een bestaand, aangepast SSL-certificaat met Azure App Service verbinden](app-service-web-tutorial-custom-ssl.md)
