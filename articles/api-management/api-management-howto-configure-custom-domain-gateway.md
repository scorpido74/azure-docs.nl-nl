---
title: Een aangepaste domeinnaam configureren voor uw zelf gehoste Azure API Management-gateway | Microsoft Documenten
description: In dit onderwerp worden de stappen beschreven voor het configureren van een aangepaste domeinnaam voor de azure API Management-gateway die zelf wordt gehost.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1f2184c7c62887a98a76877528b167d173c3d75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335933"
---
# <a name="configure-a-custom-domain-name"></a>Een aangepaste domeinnaam configureren

Wanneer u een [zelf gehoste Azure API Management-gateway indient,](self-hosted-gateway-overview.md) wordt deze niet toegewezen als hostnaam en moet deze worden verwezen naar het IP-adres. In dit artikel ziet u hoe u een bestaande aangepaste DNS-naam (ook wel hostname genoemd) een zelfgehoste gateway in kaart brengt.

> [!NOTE]
> Zelf gehoste gateway-functie is in preview. Tijdens de preview is de zelf gehoste gateway alleen beschikbaar in de lagen Developer en Premium zonder extra kosten. De ontwikkelaarslaag is beperkt tot één zelf gehoste gateway-implementatie.

## <a name="prerequisites"></a>Vereisten

Als u de in dit artikel beschreven stappen wilt uitvoeren, moet u het volgende hebben:

-   Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Een API-beheerexemplaar. Zie [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.
- Een zelfgehoste gateway. Zie Hoe u [zelf gehoste gateway indient voor](api-management-howto-provision-self-hosted-gateway.md) meer informatie
-   Een aangepaste domeinnaam die eigendom is van u of uw organisatie. In dit onderwerp worden geen instructies gegeven over het aanschaffen van een aangepaste domeinnaam.
-   Een DNS-record die wordt gehost op een DNS-server die de aangepaste domeinnaam toeschrijft aan het IP-adres van de zelfgehoste gateway. In dit onderwerp worden geen instructies gegeven over het hosten van een DNS-record.
-   U moet in het bezit zijn van een geldig certificaat met een openbare en privésleutel (. PFX). Onderwerp of onderwerp alternatieve naam (SAN) moet overeenkomen met de domeinnaam (dit stelt API Management instantie om veilig bloot URL's via TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Aangepast domeincertificaat toevoegen aan uw API-beheerservice

1. Selecteer **Certificaten** van onder **Beveiliging**.
2. Selecteer **+ Toevoegen**.
3. Voer een resourcenaam voor het certificaat in het veld **Id** in.
4. Selecteer het bestand met het certificaat (. PFX) door het veld **Certificaat** of het mappictogram ernaast te selecteren.
5. Voer het wachtwoord voor het certificaat in het veld **Wachtwoord** in.
6. Selecteer **Maken** om het certificaat toe te voegen aan uw API Management-service.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>De Azure-portal gebruiken om een aangepaste domeinnaam in te stellen voor uw zelfgehoste gateway

1. Selecteer de **gateways** van onder **Instellingen**.
2. Selecteer de zelf gehoste gateway waarvoor u de domeinnaam wilt configureren.
3. Selecteer **Hostnames** onder **Instellingen**.
4. Selecteren **+ Toevoegen**
5. Voer de resourcenaam voor de hostnaam in het veld **Naam** in.
6. Voer domeinnaam in het veld **Hostname** in.
7. Selecteer een certificaat in de **vervolgkeuzelijst Certificaat.**
8. Schakel het selectievakje **Clientcertificaat onderhandelen** in als een van de API's die via deze gateway worden blootgesteld, clientcertificaatverificatie gebruiken.
    > [!WARNING]
    > Deze instelling wordt gedeeld door alle domeinnamen die voor de gateway zijn geconfigureerd.
9. Selecteer **Toevoegen** om de aangepaste domeinnaam toe te wijzen aan de geselecteerde zelfgehoste gateway.

## <a name="next-steps"></a>Volgende stappen

[Uw service upgraden en schalen](upgrade-and-scale.md)
