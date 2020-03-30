---
title: Aangepaste domeinnaam configureren voor Azure API Management-instantie
titleSuffix: Azure API Management
description: In dit onderwerp wordt beschreven hoe u een aangepaste domeinnaam configureert voor uw Azure API Management-exemplaar.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4587909ad6fca6cdf21d54d11d89f797bbb29833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335838"
---
# <a name="configure-a-custom-domain-name"></a>Een aangepaste domeinnaam configureren

Wanneer u een Azure API Management-serviceinstantie maakt, `azure-api.net` wijst Azure `apim-service-name.azure-api.net`deze een subdomein toe van (bijvoorbeeld). U uw API Management-eindpunten echter blootstellen met uw eigen aangepaste domeinnaam, zoals **contoso.com.** In deze zelfstudie ziet u hoe u een bestaande aangepaste DNS-naam in kaart brengt aan eindpunten die worden blootgesteld door een API-beheerexemplaar.

> [!IMPORTANT]
> API Management accepteert alleen aanvragen met [hostheaderwaarden](https://tools.ietf.org/html/rfc2616#section-14.23) die overeenkomen met de standaarddomeinnaam of een van de geconfigureerde aangepaste domeinnamen.

> [!WARNING]
> Klanten die certificaatpinning willen gebruiken om de beveiliging van hun toepassingen te verbeteren, moeten een aangepaste domeinnaam en certificaat gebruiken dat ze beheren, niet het standaardcertificaat. Klanten die het standaardcertificaat vastmaken, hoeven afhankelijk te zijn van de eigenschappen van het certificaat waarover ze geen controle hebben, wat geen aanbevolen praktijk is.

## <a name="prerequisites"></a>Vereisten

Als u de in dit artikel beschreven stappen wilt uitvoeren, moet u het volgende hebben:

-   Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Een API-beheerexemplaar. Zie [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.
-   Een aangepaste domeinnaam die eigendom is van u of uw organisatie. In dit onderwerp worden geen instructies gegeven over het aanschaffen van een aangepaste domeinnaam.
-   Een CNAME-record die wordt gehost op een DNS-server die de aangepaste domeinnaam toebrengt aan de standaarddomeinnaam van uw API-beheerexemplaar. Dit onderwerp geeft geen instructies over het hosten van een CNAME-record.
-   U moet in het bezit zijn van een geldig certificaat met een openbare en privésleutel (. PFX). Onderwerp of onderwerp alternatieve naam (SAN) moet overeenkomen met de domeinnaam (dit stelt API Management instantie om veilig bloot URL's via TLS).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>De Azure-portal gebruiken om een aangepaste domeinnaam in te stellen

1. Navigeer naar uw api-beheerexemplaar in de [Azure-portal.](https://portal.azure.com/)
1. Selecteer **Aangepaste domeinen**.

    Er zijn een aantal eindpunten waaraan u een aangepaste domeinnaam toewijzen. Momenteel zijn de volgende eindpunten beschikbaar:

    - **Gateway** (standaard `<apim-service-name>.azure-api.net`is: ),
    - **Portal** (standaard `<apim-service-name>.portal.azure-api.net`is: ),
    - **Beheer** (standaard `<apim-service-name>.management.azure-api.net`is: ),
    - **SCM** (standaard `<apim-service-name>.scm.azure-api.net`is: ),
    - **NewPortal** (standaard `<apim-service-name>.developer.azure-api.net`is: ).

    > [!NOTE]
    > Alleen het **Gateway-eindpunt** is beschikbaar voor configuratie in de laag Verbruik.
    > U alle eindpunten of een aantal van deze punten bijwerken. Vaak werken klanten **Gateway** bij (deze URL wordt gebruikt om de API aan te roepen die wordt blootgesteld via API-beheer) en **Portal** (de URL van de ontwikkelaarsportal).
    > **Beheer-** en **SCM-eindpunten** worden alleen intern gebruikt door de api-beheerinstantie-eigenaren en krijgen dus minder vaak een aangepaste domeinnaam toegewezen.
    > De **premiumlaag** ondersteunt het instellen van meerdere hostnamen voor het **Gateway-eindpunt.**

1. Selecteer het eindpunt dat u wilt bijwerken.
1. Klik in het venster rechts op **Aangepast**.

    - Geef in de **aangepaste domeinnaam**de naam op die u wilt gebruiken. Bijvoorbeeld `api.contoso.com`.
    - Selecteer in het **certificaat**een certificaat uit Key Vault. Je ook een geldige uploaden. PFX bestand en geef het **wachtwoord,** als het certificaat is beveiligd met een wachtwoord.

    > [!NOTE]
    > Wildcard-domeinnamen worden bijvoorbeeld `*.contoso.com` in alle lagen ondersteund, behalve in de categorie Verbruik.

    > [!TIP]
    > We raden u aan Azure Key Vault te gebruiken voor het beheren van certificaten en deze in te stellen op automatisch roteren.
    > Als u Azure Key Vault gebruikt om het aangepaste DOMEIN TLS/SSL-certificaat te beheren, controleert u of het certificaat [als _certificaat_in](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)Key Vault wordt ingevoegd, geen _geheim_.
    >
    > Als u een TLS/SSL-certificaat wilt ophalen, moet API-beheer de lijst hebben en geheimenmachtigingen krijgen voor de Azure Key Vault die het certificaat bevat. Bij het gebruik van Azure portal worden alle benodigde configuratiestappen automatisch voltooid. Bij het gebruik van opdrachtregelgereedschappen of beheer-API moeten deze machtigingen handmatig worden verleend. Dit gebeurt in twee stappen. Gebruik eerst de pagina Beheerde identiteiten in uw API-beheerexemplaar om ervoor te zorgen dat Beheerde identiteit is ingeschakeld en maak een notitie van de hoofdnummerid die op die pagina wordt weergegeven. Ten tweede geeft u toestemmingslijst en krijgt u toestemming voor geheimen voor deze hoofd-id op de Azure Key Vault die het certificaat bevat.
    >
    > Als het certificaat is ingesteld op automatisch roteren, haalt API Management automatisch de nieuwste versie op zonder dat de service wordt uitvalbuiten (als uw API-beheerlaag SLA heeft - d.w.z. in alle lagen behalve de projectlaag).

1. Klik op Toepassen.

    > [!NOTE]
    > Het toewijzen van het certificaat kan 15 minuten of langer duren, afhankelijk van de grootte van de implementatie. Ontwikkelaar SKU heeft downtime, Basic en hogere SKU's hebben geen downtime.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS-configuratie

Bij het configureren van DNS voor uw aangepaste domeinnaam hebt u twee opties:

-   Configureer een CNAME-record die verwijst naar het eindpunt van uw geconfigureerde aangepaste domeinnaam.
-   Configureer een A-record die verwijst naar het IP-adres van uw API Management-gateway.

> [!NOTE]
> Hoewel het IP-adres van de API-managment-instantie statisch is, kan dit in een paar scenario's veranderen. Daarom is het raadzaam om CNAME te gebruiken bij het configureren van een aangepast domein. Houd daar rekening mee bij het kiezen van DNS-configuratiemethode. Lees meer in [het IP-documentatieartikel](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) en de [FAQ API Management](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Volgende stappen

[Uw service upgraden en schalen](upgrade-and-scale.md)
