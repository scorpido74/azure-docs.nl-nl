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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76021287"
---
1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Selecteer in het dialoogvenster **Publiceren** de optie **Microsoft Azure App Service**, kies Nieuw **maken**en selecteer **Vervolgens Publiceren**.

   ![Publicatiedoel kiezen](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Gebruik in het dialoogvenster **App-service maken** de hostinginstellingen zoals opgegeven in de tabel onder de afbeelding:

    ![Het dialoogvenster Create App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam van app** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app uniek wordt aangeduid. |
    | **Abonnement** | Kies uw abonnement | Het te gebruiken Azure-abonnement. |
    | **[Resourcegroep](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Naam van de resourcegroep waarin uw functie-app moet worden gemaakt. Kies **Nieuw** om een nieuwe resourcegroep te maken.|
    | **[Hostingplan](../articles/app-service/overview-hosting-plans.md)** | App Service-plan | Een [App Service-plan](../articles/app-service/overview-hosting-plans.md) geeft de locatie, de grootte en de functies van de webserverfarm aan die als host fungeert voor uw app. U kunt geld besparen bij het hosten van meerdere apps door de web-apps te configureren voor het delen van één App Service-plan. App Service-abonnementen definiëren de regio, instantiegrootte, schaaltelling en SKU (Gratis, Gedeeld, Basis, Standaard of Premium). Kies **Nieuw** om een nieuw App Service-abonnement te maken. |

1. Klik **op Maken** om een WebJob en gerelateerde resources in Azure te maken met deze instellingen en implementeer uw projectcode.