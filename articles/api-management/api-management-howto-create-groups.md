---
title: Ontwikkelaarsaccounts beheren met groepen in Azure API Management
titleSuffix: Azure API Management
description: Meer informatie over het beheren van ontwikkelaarsaccounts met groepen in Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 182f0ea93ddfb2ab64de1c15b36958537fa5431f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430711"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Groepen maken en gebruiken voor beheer van ontwikkelaarsaccounts in Azure API Management

In API Management worden groepen gebruikt voor het beheren van de zichtbaarheid van producten voor ontwikkelaars. Producten worden eerst zichtbaar gemaakt voor groepen en vervolgens kunnen ontwikkelaars in die groepen de producten bekijken en erop abonneren die aan de groepen zijn gekoppeld. 

API Management heeft de volgende onveranderbare systeemgroepen:

* **Beheerders** - Azure-abonnementbeheerders zijn lid van deze groep. Beheerders beheren service-exemplaren van API Management, door het maken van de API's, bewerkingen en producten die door ontwikkelaars worden gebruikt.
* **Ontwikkelaars** - Geverifieerde gebruikers van de ontwikkelaarsportal vallen in deze groep. Ontwikkelaars zijn de klanten die toepassingen bouwen met uw API's. Ontwikkelaars krijgen toegang tot de ontwikkelaarsportal en bouwen toepassingen waarmee de bewerkingen van een API worden aangeroepen.
* **Gasten** - Niet-geverifieerde gebruikers van de ontwikkelaarsportal, zoals potentiële klanten die de ontwikkelaarsportal van een API Management-exemplaar bezoeken, vallen in deze groep. Ze kunnen bepaalde alleen-lezentoegang krijgen, zoals de mogelijkheid om API's te bekijken maar niet om ze aan te roepen.

Naast deze systeemgroepen kunnen beheerders aangepaste groepen maken of [gebruikmaken van externe groepen in gekoppelde Azure Active Directory-tenants][leverage external groups in associated Azure Active Directory tenants]. Aangepaste en externe groepen kunnen naast systeemgroepen worden gebruikt om ontwikkelaars zichtbaarheid van en toegang tot API-producten te geven. U kunt bijvoorbeeld één aangepaste groep maken voor ontwikkelaars die zijn gekoppeld aan een specifieke partnerorganisatie en hen toegang geven tot de API's vanuit een product dat alleen relevante API's bevat. Een gebruiker kan lid zijn van meerdere groepen.

In deze handleiding ziet u hoe beheerders van een API-beheerexemplaar nieuwe groepen kunnen toevoegen en deze kunnen koppelen aan producten en ontwikkelaars.

Naast het maken en beheren van groepen in de uitgeversportal, u uw groepen maken en beheren met behulp van de entiteit API Management REST API [Group.](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Vereisten

Taken voltooien in dit artikel: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-group"></a><a name="create-group"> </a>Een groep maken

In dit gedeelte ziet u hoe u een nieuwe groep toevoegt aan uw API Management-account.

1. Selecteer het tabblad **Groepen** links van het scherm.
2. Klik **op +Toevoegen**.
3. Voer een unieke naam voor de groep en een optionele beschrijving in.
4. Kies **Maken**.

    ![Een nieuwe groep maken](./media/api-management-howto-create-groups/groups001.png)

Zodra de groep is gemaakt, wordt deze toegevoegd aan de lijst **Groepen.** <br/>Als u de **naam** of beschrijving van de groep **wilt** bewerken, klikt u op de naam van de groep en **Instellingen**.<br/>Als u de groep wilt verwijderen, klikt u op de naam van de groep en drukt u op **Verwijderen**.

Nu de groep is gemaakt, kan deze worden gekoppeld aan producten en ontwikkelaars.

## <a name="associate-a-group-with-a-product"></a><a name="associate-group-product"> </a>Een groep koppelen aan een product

1. Selecteer het tabblad **Producten** aan de linkerkant.
2. Klik op de naam van het gewenste product.
3. Druk op **Toegangsbesturingselement**.
4. Klik **op + Groep toevoegen**.

    ![Een groep koppelen aan een product](./media/api-management-howto-create-groups/groups002.png)
5. Selecteer de groep die u wilt toevoegen.

    ![Een groep koppelen aan een product](./media/api-management-howto-create-groups/groups003.png)

    Als u een groep uit het product wilt verwijderen, klikt u op **Verwijderen**.

    ![Een groep verwijderen](./media/api-management-howto-create-groups/groups004.png)

Zodra een product is gekoppeld aan een groep, kunnen ontwikkelaars in die groep het product bekijken en erop abonneren.

> [!NOTE]
> Zie [Voor het autoriseren van ontwikkelaarsaccounts met Azure Active Directory in Azure API-beheer](api-management-howto-aad.md)als u Azure Active Directory-groepen wilt toevoegen.

## <a name="associate-groups-with-developers"></a><a name="associate-group-developer"> </a>Groepen koppelen aan ontwikkelaars

In deze sectie ziet u hoe u groepen aan leden koppelt.

1. Selecteer het tabblad **Groepen** links van het scherm.
2. Selecteer **Leden**.

    ![Lid toevoegen](./media/api-management-howto-create-groups/groups005.png)
3. Druk **op +Toevoegen** en selecteer een lid.

    ![Lid toevoegen](./media/api-management-howto-create-groups/groups006.png)
4. Druk op **Selecteren**.

Zodra de koppeling tussen de ontwikkelaar en de groep is toegevoegd, u deze weergeven op het tabblad **Gebruikers.**

## <a name="next-steps"></a><a name="next-steps"> </a>Volgende stappen

* Zodra een ontwikkelaar aan een groep is toegevoegd, kan deze zich abonneren op de producten die aan die groep zijn gekoppeld. Zie [Hoe een product maken en publiceren in Azure API Management][How create and publish a product in Azure API Management]voor meer informatie.
* Naast het maken en beheren van groepen in de uitgeversportal, u uw groepen maken en beheren met behulp van de entiteit API Management REST API [Group.](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
