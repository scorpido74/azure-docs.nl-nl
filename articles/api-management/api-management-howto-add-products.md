---
title: Een product maken en publiceren in Azure API Management
description: Leer hoe u producten kunt maken en publiceren in Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: d90b212286b555bf5b858990fd5dcb062348be8d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86254721"
---
# <a name="create-and-publish-a-product"></a>Een product maken en publiceren  

In Azure API Management bevat een product een of meer API's, evenals een gebruiksquotum en de gebruiksvoorwaarden. Zodra een product is gepubliceerd, kunnen ontwikkelaars zich abonneren op het product en de API's van het product gaan gebruiken.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een product maken en publiceren
> * Een API toevoegen aan het product

![Zelfstudie over product toevoegen](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Vereisten

+ Informatie over de [terminologie van Azure API Management](api-management-terminology.md).
+ Voltooi de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi ook de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Een product maken en publiceren

![Product toevoegen](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. Klik op **Producten** in het menu aan de linkerkant om de pagina **Producten** weer te geven.
2. Klik op **+ Toevoegen**.

    Wanneer u een product toevoegt, moet u de volgende informatie opgeven: 

    | Naam                     | Beschrijving                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Weergavenaam             | De naam zoals u wilt dat deze wordt weergegeven in de **ontwikkelaarsportal**.                                                                                                                                                                                                                                                        |
    | Naam                     | Een beschrijvende naam van het product.                                                                                                                                                                                                                                                                                      |
    | Beschrijving              | In het veld **Beschrijving** kunt u gedetailleerde informatie over het product opgeven, zoals het doel, de API's waartoe het product toegang biedt, en andere nuttige informatie.                                                                                                                                               |
    | Status                    | Druk op **Gepubliceerd** als u het product wilt publiceren. Voordat de API's in een product kunnen worden aangeroepen, moet het product worden gepubliceerd. Nieuwe producten zijn standaard niet-gepubliceerd, en alleen zichtbaar voor gebruikers in de groep **Beheerders**.                                                                                      |
    | Abonnement is vereist    | Schakel **Abonnement vereisen**in als een gebruiker een abonnement moet hebben om het product te kunnen gebruiken.                                                                                                                                                                                                                                   |
    | Goedkeuring vereist        | Schakel **Goedkeuring vereisen** in als u wilt dat een beheerder abonnementspogingen voor dit product beoordeelt en accepteert of weigert. Als het selectievakje uitgeschakeld is, worden abonnementspogingen automatisch goedgekeurd.                                                                                                                         |
    | Limiet voor het aantal abonnementen | Als u het aantal gelijktijdige abonnementen wilt beperken, voert u de limiet voor het aantal abonnementen in.                                                                                                                                                                                                                                |
    | Juridische voorwaarden              | U kunt ook de gebruiksvoorwaarden voor het product opnemen, die abonnees moeten accepteren om het product te kunnen gebruiken.                                                                                                                                                                                                             |
    | API's                     | Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. <br/> U kunt een bestaande API toevoegen tijdens het maken van het product. U kunt een API later aan het product toevoegen, hetzij vanuit de pagina van de product **Instellingen** of tijdens het maken van een API. |

3. Klik op **Maken** om een nieuw product te maken.

### <a name="add-more-configurations"></a>Meer configuraties toevoegen

U kunt doorgaan met het configureren van het product nadat het is opgeslagen door het tabblad **Instellingen** te kiezen. 

Op het tabblad **Abonnementen** kunt u de abonnees van het product bekijken of abonnees aan het product toevoegen.

Op het tabblad **Toegangsbeheer** kunt u de zichtbaarheid voor ontwikkelaars of gastgebruikers van een product instellen.

## <a name="add-apis-to-a-product"></a><a name="add-apis"> </a>API's toevoegen aan een product

Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. U kunt een bestaande API toevoegen tijdens het maken van het product. U kunt ook later een API aan het product toevoegen, via de pagina met **instellingen** van het product of tijdens het maken van een API.

Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. Als u de APIM-abonnementssleutel hebt gemaakt, bent u al een beheerder en bent u standaard geabonneerd op elk product.

### <a name="add-an-api-to-an-existing-product"></a>Een API toevoegen aan een bestaand product

![product-API toevoegen](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. Selecteer op het tabblad **Producten** een product.
2. Navigeer naar het tabblad **API's**.
3. Klik op **+ Toevoegen**.
4. Kies een API en klik op **Selecteren**.

> [!TIP]
> U kunt het abonnement van een gebruiker maken of bijwerken naar een *Product* met aangepaste abonnementssleutels [via een REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) of een PowerShell-opdracht.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een product maken en publiceren
> * Een API toevoegen aan het product

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Create blank API and mock API responses](mock-api-responses.md) (Lege API en mock-API-reacties maken)
