---
title: Een aangepaste domein naam configureren voor uw zelf-hostende Azure API Management-Gateway | Microsoft Docs
description: In dit onderwerp worden de stappen beschreven voor het configureren van een aangepaste domein naam voor zelf-hostende Azure API Management Gateway.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: dacf1329d35117c65bcc48a82ac27a767ebd2b3b
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203179"
---
# <a name="configure-a-custom-domain-name"></a>Een aangepaste domeinnaam configureren

Wanneer u een [zelf-hostende Azure API Management Gateway](self-hosted-gateway-overview.md) inricht, wordt er geen hostnaam toegewezen en waarnaar wordt verwezen door het bijbehorende IP-adres. In dit artikel wordt beschreven hoe u een bestaande aangepaste DNS-naam (ook wel hostname genoemd) toewijst aan een zelf-hostende gateway.

## <a name="prerequisites"></a>Vereisten

Voor het uitvoeren van de stappen die in dit artikel worden beschreven, hebt u het volgende nodig:

-   Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Een API Management-exemplaar. Zie [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.
- Een zelf-hostende gateway. Zie [zelf-hostende gateway inrichten](api-management-howto-provision-self-hosted-gateway.md) voor meer informatie
-   Een aangepaste domein naam die eigendom is van u of uw organisatie. In dit onderwerp vindt u geen instructies voor het aanschaffen van een aangepaste domein naam.
-   Een DNS-record die wordt gehost op een DNS-server die de aangepaste domein naam toewijst aan het IP-adres van de zelf-hostende gateway. In dit onderwerp vindt u geen instructies voor het hosten van een DNS-record.
-   U moet een geldig certificaat met een open bare en persoonlijke sleutel hebben (. PFX). Het onderwerp of de alternatieve naam voor het onderwerp (SAN) moet overeenkomen met de domein naam (dit maakt het mogelijk dat API Management exemplaar veilig Url's weergeeft via TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Aangepast domein certificaat toevoegen aan uw API Management-service

1. Selecteer **certificaten** uit de **beveiliging**.
2. Selecteer **+ Toevoegen**.
3. Voer een resource naam in voor het certificaat in het veld **id** .
4. Selecteer het bestand met het certificaat (. PFX) door het veld **certificaat** of het mappictogram ernaast te selecteren.
5. Voer het wacht woord voor het certificaat in het veld **wacht woord** in.
6. Selecteer **maken** om het certificaat toe te voegen aan uw API Management-service.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>De Azure Portal gebruiken om een aangepaste domein naam voor uw zelf-hostende gateway in te stellen

1. Selecteer de **gateways** onder **instellingen**.
2. Selecteer de zelf-hostende gateway waarvoor u de domein naam wilt configureren.
3. Selecteer **hostnamen** onder **instellingen**.
4. Selecteer **+ toevoegen**
5. Voer de resource naam voor de hostnaam in het veld **naam** in.
6. Voer de domein naam in het veld **hostnaam** in.
7. Selecteer een certificaat in de vervolg keuzelijst **certificaat** .
8. Schakel het selectie vakje **onderhandelen client certificaat** in als een van de api's die via deze gateway worden weer gegeven, client certificaat authenticatie gebruikt.
    > [!WARNING]
    > Deze instelling wordt gedeeld door alle domein namen die zijn geconfigureerd voor de gateway.
9. Selecteer **toevoegen** om de aangepaste domein naam toe te wijzen aan de geselecteerde zelf-hostende gateway.

## <a name="next-steps"></a>Volgende stappen

[Uw service bijwerken en schalen](upgrade-and-scale.md)
