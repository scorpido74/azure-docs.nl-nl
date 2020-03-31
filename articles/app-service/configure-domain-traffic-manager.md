---
title: DNS-namen configureren met Traffic Manager
description: Meer informatie over het configureren van een aangepast domein voor een Azure App Service-app die integreert met Traffic Manager voor taakverdeling.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944138"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Een aangepaste domeinnaam configureren in Azure App Service met Traffic Manager-integratie

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Zie [Een aangepaste domeinnaam configureren voor een Azure-cloudservice voor](../cloud-services/cloud-services-custom-domain-name.md)Cloud Services.

Wanneer u [Azure Traffic Manager](/azure/traffic-manager/) gebruikt om saldoverkeer te laden naar Azure App [Service,](overview.md)kan de App Service-app worden geopend met behulp van ** \<verkeersbeheer-eindpunt>.trafficmanager.net**. U een aangepaste domeinnaam,\.zoals www contoso.com, toewijzen met uw App Service-app om uw gebruikers een meer herkenbare domeinnaam te bieden.

In dit artikel ziet u hoe u een aangepaste domeinnaam configureert met een App Service-app die is geïntegreerd met [Traffic Manager.](../traffic-manager/traffic-manager-overview.md)

> [!NOTE]
> Alleen [CNAME-records](https://en.wikipedia.org/wiki/CNAME_record) worden ondersteund wanneer u een domeinnaam configureert met het eindpunt Traffic Manager. Omdat A-records niet worden ondersteund, wordt een hoofddomeintoewijzing, zoals contoso.com ook niet ondersteund.
> 

## <a name="prepare-the-app"></a>De app voorbereiden

Als u een aangepaste DNS-naam wilt toewijzen aan een app die is geïntegreerd met Azure Traffic Manager, moet het [App Service-abonnement](https://azure.microsoft.com/pricing/details/app-service/) van de web-app in **de standaardlaag** of hoger zijn. In deze stap zorgt u ervoor dat de App Service-app zich in de ondersteunde prijscategorie bevindt.

### <a name="check-the-pricing-tier"></a>Controleer de prijscategorie

Zoek in de [Azure-portal](https://portal.azure.com)naar en selecteer **App Services**.

Selecteer op de pagina **App Services** de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/select-app.png)

Selecteer op de linkernavigatie van de app-pagina **Opschalen (App Service-abonnement).**

![Menu Opschalen](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

De huidige laag van de app wordt gemarkeerd door een blauwe rand. Controleer of de app zich in **de standaardlaag** of hoger bevindt (een laag in de categorie **Productie** of **Geïsoleerd).** Zo ja, sluit u de pagina **Opschalen** en gaat u naar [De CNAME-toewijzing maken.](#create-the-cname-mapping)

![Controleer prijscategorie](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Het App Service-plan opschalen

Als u uw app wilt opschalen, selecteert u een van de prijsniveaus in de categorie **Productie.** Klik op **Aanvullende opties bekijken** voor aanvullende opties.

Klik op **Toepassen**.

## <a name="create-traffic-manager-endpoint"></a>Eindpunt van Traffic Manager maken

Als u de stappen bij [Eindpunt toevoegen of verwijderen volgt,](../traffic-manager/traffic-manager-endpoints.md)voegt u uw App Service-app toe als eindpunt in uw Traffic Manager-profiel.

Zodra uw App Service-app zich in een ondersteunde prijscategorie bevindt, wordt deze weergegeven in de lijst met beschikbare App-servicedoelen wanneer u het eindpunt toevoegt. Als uw app niet wordt weergegeven, [controleert u de prijscategorie van uw app](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>De CNAME-toewijzing maken
> [!NOTE]
> Als u een [App Service-domein wilt](manage-custom-dns-buy-domain.md)configureren dat u hebt gekocht, slaat u deze sectie over en gaat u naar [Aangepast domein inschakelen.](#enable-custom-domain)
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Hoewel de specifieke kenmerken van elke domeinprovider variëren, brengt u de *aangepaste* domeinnaam (zoals **contoso.com)** in kaart *met* de Traffic Manager-domeinnaam **(contoso.trafficmanager.net)** die is geïntegreerd met uw app.

> [!NOTE]
> Als er al een record in gebruik is en u uw apps preventief moet binden, u een extra CNAME-record maken. Als u bijvoorbeeld **www-contoso.com\.** aan uw app wilt binden, maakt u een CNAME-record van **awverify.www** tot **contoso.trafficmanager.net**. U vervolgens\.www contoso.com toevoegen aan uw app zonder dat u de CNAME-record www hoeft te wijzigen. Zie [Een actieve DNS-naam migreren naar Azure App Service](manage-custom-dns-migrate-domain.md)voor meer informatie.

Sla de wijzigingen op zodra u klaar bent met het toevoegen of wijzigen van DNS-records bij uw domeinprovider.

## <a name="enable-custom-domain"></a>Aangepast domein inschakelen
Nadat de records voor uw domeinnaam zijn gepropageerd, gebruikt u de browser om te controleren of uw aangepaste domeinnaam wordt opgelost in uw App Service-app.

> [!NOTE]
> Het kan even duren voordat uw CNAME zich voortplant via het DNS-systeem. U een service <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> gebruiken om te controleren of de CNAME beschikbaar is.
> 
> 

1. Zodra de domeinresolutie is geslaagd, terug naar uw app-pagina in de [Azure Portal](https://portal.azure.com)
2. Selecteer **aangepaste domeinen** > **Hostname toevoegen**aan de linkernavigatie.
4. Typ de aangepaste domeinnaam die u eerder hebt toegewezen en selecteer **Valideren**.
5. Controleer of **het recordtype Hostname** is ingesteld op **CNAME (www\.example.com of een subdomein).**

6. Aangezien de App Service-app nu is geïntegreerd met een Traffic Manager-eindpunt, moet u de domeinnaam Traffic Manager onder **CNAME-configuratie zien.** Selecteer het en klik op **Aangepast domein toevoegen**.

    ![DNS-naam toevoegen aan de app](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een aangepaste DNS-naam beveiligen met een SSL-binding in Azure App Service](configure-ssl-bindings.md)
