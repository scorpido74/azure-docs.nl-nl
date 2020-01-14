---
title: Controle rapport voor Azure-resource rollen weer geven in PIM-Azure AD | Microsoft Docs
description: Bekijk de activiteiten-en controle geschiedenis voor Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 905acd206ba574e092f41707c9a5625bcaed7f8d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932381"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Activiteiten en controle geschiedenis voor Azure-resource rollen in Privileged Identity Management weer geven

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u activiteiten, activeringen en controle geschiedenis voor Azure-resource rollen in uw organisatie weer geven. Dit omvat abonnementen, resource groepen en zelfs virtuele machines. Resources in de Azure Portal die gebruikmaken van de functionaliteit voor het beheer van op rollen gebaseerde toegang (RBAC) van Azure, kunnen profiteren van de mogelijkheden voor beveiliging en levens cyclus in Privileged Identity Management.

> [!NOTE]
> Als uw organisatie uitbestede beheer functies heeft voor een service provider die gebruikmaakt van [Azure delegated resource management](../../lighthouse/concepts/azure-delegated-resource-management.md), worden roltoewijzingen die door die service provider worden toegestaan, hier niet weer gegeven.

## <a name="view-activity-and-activations"></a>Activiteiten en activeringen weer geven

Als u wilt zien welke acties een specifieke gebruiker heeft uitgevoerd in verschillende resources, kunt u de Azure-resource activiteit bekijken die is gekoppeld aan een bepaalde activerings periode.

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure-resources**.

1. Klik op de resource waarvoor u activiteiten en activeringen wilt weer geven.

1. Klik op **functies** of **leden**.

1. Klik op een gebruiker.

    U ziet een grafische weer gave van de acties van de gebruiker in azure-resources op datum. U ziet ook de recente functie activeringen over diezelfde periode.

    ![Gebruikers gegevens met overzicht van resource activiteiten en rollen activeringen](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Klik op een specifieke functie activering om details en bijbehorende Azure-resource activiteit te bekijken die is opgetreden terwijl de gebruiker actief was.

    ![Activering van functie geselecteerd en Details van activiteit op datum weer geven](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Roltoewijzingen met onderliggende items exporteren

Mogelijk hebt u een nalevings vereiste waarbij u een volledige lijst met roltoewijzingen aan Audi tors moet geven. Met Privileged Identity Management kunt u een query uitvoeren op Roltoewijzingen voor een specifieke resource, die roltoewijzingen bevat voor alle onderliggende resources. Voorheen was het moeilijk voor beheerders om een volledige lijst met roltoewijzingen voor een abonnement te krijgen en ze moest roltoewijzingen voor elke specifieke resource exporteren. Met Privileged Identity Management kunt u een query uitvoeren voor alle actieve en in aanmerking komende roltoewijzingen in een abonnement, inclusief roltoewijzingen voor alle resource groepen en resources.

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure-resources**.

1. Klik op de resource waarvoor u roltoewijzingen wilt exporteren, zoals een abonnement.

1. Klik op **leden**.

1. Klik op **exporteren** om het deel venster lidmaatschap exporteren te openen.

    ![Deel venster lidmaatschap exporteren om alle leden te exporteren](media/azure-pim-resource-rbac/export-membership.png)

1. Klik op **alle leden exporteren** om alle roltoewijzingen in een CSV-bestand te exporteren.

    ![Geëxporteerde roltoewijzingen in een CSV-bestand als weer gave in Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Controle geschiedenis van resources weer geven

Resource controle geeft u een overzicht van alle functie activiteiten voor een resource.

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure-resources**.

1. Klik op de resource waarvoor u de controle geschiedenis wilt weer geven.

1. Klik op **resource audit**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    ![Resource audit lijst met filters](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Selecteer **activeren (toegewezen + geactiveerd)** bij **controle type**.

    ![Resource controle lijst die wordt gefilterd door audit type activeren](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Klik onder **actie**op **(activiteit)** voor een gebruiker om de activiteit Details van die gebruiker in azure-resources te bekijken.

    ![Details van de gebruikers activiteit voor een bepaalde actie](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Mijn controle weer geven

Met mijn controle kunt u uw activiteiten voor persoonlijke rollen weer geven.

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure-resources**.

1. Klik op de resource waarvoor u de controle geschiedenis wilt weer geven.

1. Klik op **mijn controle**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    ![Controle lijst voor de huidige gebruiker](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure-resource rollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Aanvragen voor Azure-resource rollen in Privileged Identity Management goed keuren of weigeren](pim-resource-roles-approval-workflow.md)
- [De controle geschiedenis voor Azure AD-rollen in Privileged Identity Management weer geven](pim-how-to-use-audit-log.md)
