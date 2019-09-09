---
title: Activiteiten en controle geschiedenis voor Azure-resource rollen weer geven in PIM-Azure Active Directory | Microsoft Docs
description: Bekijk de activiteiten-en controle geschiedenis voor Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e1ed018f66d4f5eefd02d587504cf64969f47b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804044"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Activiteiten en controle geschiedenis voor Azure-resource rollen weer geven in PIM

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u activiteiten, activeringen en controle geschiedenis voor Azure-resource rollen in uw organisatie weer geven. Dit omvat abonnementen, resource groepen en zelfs virtuele machines. Alle resources binnen de Azure Portal die gebruikmaken van de functionaliteit voor het beheer van op rollen gebaseerde toegangs beheer (RBAC), kunnen profiteren van de mogelijkheden voor beveiliging en levens cyclus in PIM.

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

Mogelijk hebt u een nalevings vereiste waarbij u een volledige lijst met roltoewijzingen aan Audi tors moet geven. Met PIM kunt u een query uitvoeren op Roltoewijzingen voor een specifieke resource, die roltoewijzingen bevat voor alle onderliggende resources. Voorheen was het moeilijk voor beheerders om een volledige lijst met roltoewijzingen voor een abonnement te krijgen en ze moest roltoewijzingen voor elke specifieke resource exporteren. Met behulp van PIM kunt u een query uitvoeren voor alle actieve en in aanmerking komende roltoewijzingen in een abonnement, inclusief roltoewijzingen voor alle resource groepen en resources.

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure-resources**.

1. Klik op de resource waarvoor u roltoewijzingen wilt exporteren, zoals een abonnement.

1. Klik op **leden**.

1. Klik op **exporteren** om het deel venster lidmaatschap exporteren te openen.

    ![Deel venster lidmaatschap exporteren om alle leden te exporteren](media/azure-pim-resource-rbac/export-membership.png)

1. Klik op **alle leden exporteren** om alle roltoewijzingen in een CSV-bestand te exporteren.

    ![Geëxporteerde roltoewijzingen in CSV-fil als weer gave in Excel](media/azure-pim-resource-rbac/export-csv.png)

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

- [Azure-resource rollen toewijzen in PIM](pim-resource-roles-assign-roles.md)
- [Aanvragen voor Azure-resource rollen in PIM goed keuren of weigeren](pim-resource-roles-approval-workflow.md)
- [De controle geschiedenis voor Azure AD-rollen in PIM weer geven](pim-how-to-use-audit-log.md)
