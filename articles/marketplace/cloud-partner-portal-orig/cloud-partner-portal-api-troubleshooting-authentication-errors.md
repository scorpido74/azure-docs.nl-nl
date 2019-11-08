---
title: Veelvoorkomende verificatie fouten oplossen | Azure Marketplace
description: Biedt hulp bij veelvoorkomende verificatie fouten bij het gebruik van de Cloud Partner-portal-Api's.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 0990e9aedf17f6d4ad01e4911e47efd60001f3d7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827369"
---
# <a name="troubleshooting-common-authentication-errors"></a>Veelvoorkomende verificatie fouten oplossen

Dit artikel biedt hulp bij veelvoorkomende verificatie fouten bij het gebruik van de Cloud Partner-portal-Api's.

## <a name="unauthorized-error"></a>Niet-geautoriseerde fout

Als u `401 unauthorized` fouten consistent krijgt, controleert u of u een geldig toegangs token hebt.  Als u dit nog niet hebt gedaan, maakt u een Basic Azure Active Directory-toepassing (Azure AD) en een Service-Principal zoals beschreven in [Portal gebruiken om een Azure Active Directory toepassing en Service-Principal te maken die toegang hebben tot resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Gebruik vervolgens de toepassing of een eenvoudige HTTP POST-aanvraag om uw toegang te controleren.  U neemt de Tenant-ID, toepassings-ID, object-ID en de geheime sleutel op om het toegangs token op te halen, zoals wordt weer gegeven in de volgende afbeelding:

![Problemen met de 401-fout oplossen](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Verboden fout

Als er een `403 forbidden` fout optreedt, moet u ervoor zorgen dat de juiste Service-Principal is toegevoegd aan uw uitgevers account in de Cloud Partner-portal.
Volg de stappen op de pagina [vereisten](./cloud-partner-portal-api-prerequisites.md) om uw Service-Principal toe te voegen aan de portal.

Als de juiste Service-Principal is toegevoegd, controleert u alle andere gegevens. Let op de object-ID die is ingevoerd op de portal. De Azure Active Directory app-registratie pagina bevat twee object-Id's en u moet de lokale object-ID gebruiken. U kunt de juiste waarde vinden door naar de pagina **app-registraties** voor uw app te gaan en te klikken op de naam van de app onder **beheerde toepassing in de lokale map**. Hiermee gaat u naar de lokale eigenschappen voor de app, waar u de juiste object-ID kunt vinden op de pagina **Eigenschappen** , zoals wordt weer gegeven in de volgende afbeelding. Zorg er ook voor dat u de juiste uitgever-ID gebruikt om de Service-Principal toe te voegen en de API-aanroep te maken.

![Problemen met de 403-fout oplossen](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
