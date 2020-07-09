---
title: Op rollen gebaseerde toegang verlenen voor gebruikers
titleSuffix: Azure Maps
description: Op rollen gebaseerd toegangs beheer gebruiken om gebruikers toestemming te geven Azure Maps
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 86f89397f3685443071788580253ee11ce4b70be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988569"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Op rollen gebaseerde toegang verlenen aan gebruikers Azure Maps

U verleent op *rollen gebaseerd toegangs beheer* (RBAC) door een Azure AD-groep of beveiligings-principals toe te wijzen aan een of meer Azure Maps Access Control-rollen definities. Ga naar **toegangs beheer (IAM)** om de RBAC-functie definities weer te geven die beschikbaar zijn voor Azure Maps. Selecteer **rollen**en zoek vervolgens naar rollen die beginnen met *Azure Maps*.

* Als u een grote hoeveelheid gebruikers toegang tot Azure Maps efficiënt wilt beheren, raadpleegt u [Azure ad-groepen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).
* Gebruikers kunnen zich alleen bij de toepassing verifiëren als ze zijn gemaakt in azure AD. Zie [gebruikers toevoegen of verwijderen met Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

Meer informatie vindt u in [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/) om een directory voor gebruikers effectief te beheren.

1. Ga naar uw **Azure Maps-account**. Selecteer de roltoewijzing van **toegangs beheer (IAM)**  >  **Role assignment**.

    ![RBAC toekennen](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Selecteer op **het tabblad roltoewijzingen,** onder **rol**, een ingebouwde Azure Maps roldefinitie, zoals **Azure Maps gegevens lezer** of **Azure Maps gegevensinzender**. Onder **toegang toewijzen aan**selecteert u **Azure AD-gebruiker,-groep of Service-Principal**. Selecteer de principal op naam. Selecteer vervolgens **Opslaan**.

   * Zie de Details voor het [toevoegen of verwijderen van roltoewijzingen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

> [!WARNING]
> Azure Maps ingebouwde roldefinities bieden een zeer grote autorisatie toegang tot veel Azure Maps REST-Api's. Zie [een aangepaste roldefinitie maken en gebruikers toewijzen](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) aan de definitie van de aangepaste rol om api's voor gebruikers tot een minimum te beperken. Hiermee kunnen gebruikers de mini maal benodigde bevoegdheden voor de toepassing hebben.