---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76021287"
---
1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Selecteer in het dialoog venster **publiceren** de optie **Microsoft Azure app service**, kies **nieuwe maken**en selecteer vervolgens **publiceren**.

   ![Publicatie doel selecteren](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Gebruik in het dialoog venster **app service maken** de hosting-instellingen zoals opgegeven in de tabel onder de afbeelding:

    ![Het dialoogvenster Create App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **App-naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app uniek wordt aangeduid. |
    | **Abonnement** | Kies uw abonnement | Het te gebruiken Azure-abonnement. |
    | **[Resourcegroep](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Naam van de resourcegroep waarin uw functie-app moet worden gemaakt. Kies **Nieuw** om een nieuwe resourcegroep te maken.|
    | **[Hosting plan](../articles/app-service/overview-hosting-plans.md)** | App Service-plan | Een [App Service-plan](../articles/app-service/overview-hosting-plans.md) geeft de locatie, de grootte en de functies van de webserverfarm aan die als host fungeert voor uw app. U kunt geld besparen bij het hosten van meerdere apps door de web-apps te configureren voor het delen van één App Service-plan. Met App Service plannen worden de regio, de grootte van het exemplaar, het aantal schalen en de SKU (gratis, gedeeld, basis, standaard of Premium) gedefinieerd. Kies **Nieuw** om een nieuw app service plan te maken. |

1. Klik op **maken** om een Webtaak en gerelateerde resources in azure te maken met deze instellingen en de project code te implementeren.