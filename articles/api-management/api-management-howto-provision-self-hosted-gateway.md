---
title: Een zelf-hostende gateway inrichten in azure API Management | Microsoft Docs
description: Meer informatie over het inrichten van een zelf-hostende gateway in azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74075294"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Een zelf-hostende gateway inrichten in azure API Management

Het inrichten van een gateway bron in uw Azure API Management-exemplaar is een vereiste voor het implementeren van een zelf-hostende gateway. In dit artikel worden de stappen beschreven voor het inrichten van een gateway bron in API Management.

> [!NOTE]
> De zelf-hostende gateway functie is beschikbaar als preview-versie. Tijdens de preview-periode is de zelf-hostende gateway alleen beschikbaar in de lagen ontwikkelaar en Premium zonder extra kosten. De Developer-laag is beperkt tot één zelf-hostende gateway-implementatie.

## <a name="prerequisites"></a>Vereisten

Voer de volgende Snelstartgids uit: [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Een zelf-hostende gateway inrichten

1. Selecteer de **gateways** onder **instellingen**.
2. Klik op **+ Toevoegen**.
3. Voer de **naam** en de **regio** van de gateway in.
> [!TIP]
> **Regio** specificeert de beoogde locatie van de gateway knooppunten die aan deze gateway bron worden gekoppeld. Het is semantisch gelijk aan een vergelijk bare eigenschap die is gekoppeld aan een Azure-resource, maar er kan een wille keurige teken reeks waarde aan worden toegewezen.

4. Voer desgewenst een **Beschrijving** in van de gateway resource.
5. Selecteer **+** desgewenst onder **api's** om een of meer api's aan deze gateway bron te koppelen.
> [!TIP]
> U kunt een API koppelen aan en verwijderen uit een gateway op het tabblad **instellingen** van de API.

> [!IMPORTANT]
> Standaard wordt geen van de bestaande Api's gekoppeld aan de nieuwe gateway resource. Daarom zullen pogingen om deze via de nieuwe gateway aan te roepen, `404 Resource Not Found` leiden tot antwoorden.

6. Klik op **Add**.

De gateway bron is nu ingericht in uw API Management-exemplaar. U kunt door gaan met het implementeren van de gateway.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de zelf-hostende Gateway vindt u in [Azure API Management zelf-hostende gateway-overzicht](self-hosted-gateway-overview.md)
* Meer informatie over het [implementeren van een zelf-hostende gateway naar Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Meer informatie over het [implementeren van een zelf-hostende gateway naar docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
