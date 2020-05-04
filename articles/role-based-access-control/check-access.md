---
title: 'Snelstartgids: de toegang van een gebruiker tot Azure-resources weer geven-Azure RBAC'
description: In deze Quick Start leert u hoe u de toegang tot een gebruiker of een andere beveiligingsprincipal kunt weer geven met Azure-resources met behulp van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9be53aa964e75bab0b90495640537fe927a5af0e
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734158"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Snelstartgids: de toegang van een gebruiker tot Azure-resources weer geven

U kunt de Blade **toegangs beheer (IAM)** in [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](overview.md) gebruiken om de toegang van een gebruiker of een andere beveiligings-principal te bekijken voor Azure-resources. Maar soms wilt u alleen snel de toegang van één gebruiker of een andere beveiligings-principal bekijken. De eenvoudigste manier om dit te doen, is met de functie **Toegang controleren** in de Azure-portal.

## <a name="view-role-assignments"></a>Roltoewijzingen weergeven

 U kunt de toegang van een gebruiker weergeven door zijn roltoewijzingen te bekijken. Volg deze stappen om de roltoewijzingen weer te geven voor één gebruiker, groep, service-principal of beheerde identiteit in het bereik van het abonnement.

1. Klik in de Azure-portal op de optie **Alle services** en vervolgens op **Abonnementen**.

1. Klik op uw abonnement.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Toegang controleren**.

    ![Toegangsbeheer - Tabblad Toegang controleren](./media/check-access/access-control-check-access.png)

1. Selecteer in de lijst **Zoeken** het type beveiligings-principal waarvoor u de toegang wilt controleren.

1. Voer in het zoekvak een tekenreeks in om de map te doorzoeken op weergavenamen, e-mailadressen of object-id's.

    ![De toegangsselectielijst controleren](./media/check-access/check-access-select.png)

1. Klik op de beveiligings-principal om het deelvenster **Toewijzingen** te openen.

    ![Deelvenster Toewijzingen](./media/check-access/check-access-assignments.png)

    In dit deelvenster ziet u de rollen die zijn toegewezen aan de geselecteerde beveiligings-principal en het bereik. Als er een in dit bereik toewijzingen zijn geweigerd of zijn overgenomen, worden deze weergegeven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een gebruiker toegang verlenen tot Azure-resources met behulp van de Azure Portal](quickstart-assign-role-user-portal.md)
