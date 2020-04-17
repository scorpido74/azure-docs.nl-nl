---
title: Azure-portaldashboards delen met behulp van toegangsbeheer op basis van rollen
description: In dit artikel wordt uitgelegd hoe u een dashboard in de Azure-portal delen met behulp van Toegangsbeheer op basis van rollen.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 4eef5a9e3f010e19871471d007ff2a0cc24d3834
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461375"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Azure-dashboards delen met behulp van op rollen gebaseerd toegangsbeheer

Nadat u een dashboard hebt geconfigureerd, u het publiceren en delen met andere gebruikers in uw organisatie. U anderen uw dashboard bekijken met Behulp van RBAC [(Azure Role-Based Access Control).](../role-based-access-control/role-assignments-portal.md) Wijs een gebruiker of groep gebruikers toe aan een rol. In die rol wordt bepaald of deze gebruikers het gepubliceerde dashboard kunnen bekijken of wijzigen.

Alle gepubliceerde dashboards worden geïmplementeerd als Azure-bronnen. Ze bestaan als beheerbare items binnen uw abonnement en zijn opgenomen in een resourcegroep. Vanuit het oogpunt van toegangscontrole zijn dashboards niet anders dan andere bronnen, zoals een virtuele machine of een opslagaccount.

> [!TIP]
> Afzonderlijke tegels op het dashboard dwingen hun eigen vereisten voor toegangscontrole af op basis van de resources die ze weergeven. U een dashboard breed delen terwijl u de gegevens op afzonderlijke tegels beschermt.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Toegangsbeheer voor dashboards begrijpen

Met RBAC (Role-Based Access Control) u gebruikers toewijzen aan rollen op drie verschillende scopeniveaus:

* abonnement
* resourcegroep
* resource

De machtigingen die u toewijst, nemen over van het abonnement tot aan de bron. Het gepubliceerde dashboard is een bron. Mogelijk hebben gebruikers al toegewezen aan rollen voor het abonnement dat van toepassing is op het gepubliceerde dashboard.

Stel dat u een Azure-abonnement hebt en dat verschillende leden van uw team de rollen van *eigenaar,* *bijdrager*of *lezer* voor het abonnement hebben toegewezen. Gebruikers die eigenaar of bijdragers zijn, kunnen dashboards in het abonnement aanbieden, bekijken, maken, wijzigen of verwijderen. Gebruikers die lezers zijn, kunnen dashboards aanbieden en bekijken, maar kunnen deze niet wijzigen of verwijderen. Gebruikers met lezerstoegang kunnen lokale bewerkingen aanbrengen in een gepubliceerd dashboard, bijvoorbeeld bij het oplossen van een probleem, maar ze kunnen deze wijzigingen niet opnieuw naar de server publiceren. Ze kunnen zelf een privékopie van het dashboard maken.

U ook machtigingen toewijzen aan de resourcegroep die meerdere dashboards of een afzonderlijk dashboard bevat. U bijvoorbeeld besluiten dat een groep gebruikers beperkte machtigingen voor het abonnement moet hebben, maar een betere toegang tot een bepaald dashboard. Wijs deze gebruikers toe aan een rol voor dat dashboard.

## <a name="publish-dashboard"></a>Dashboard publiceren

Stel dat u een dashboard configureert dat u wilt delen met een groep gebruikers in uw abonnement. In de volgende stappen wordt uitgelegd hoe u een dashboard deelt met een groep met de naam Storage Managers. Je je groep een naam geven wat je wilt. Zie [Groepen beheren in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie.

Voordat u toegang toegeeft, moet u het dashboard publiceren.

1. Selecteer **Delen**in het dashboard .

    ![selecteren delen voor uw dashboard](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. Selecteer **Publiceren**in **Delen + toegangscontrole**.

    ![uw dashboard publiceren](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Het delen van het dashboard publiceert standaard naar een resourcegroep met de naam **dashboards**. Schakel het selectievakje uit als u een andere resourcegroep wilt selecteren.

Uw dashboard is nu gepubliceerd. Als de machtigingen die van het abonnement zijn overgenomen geschikt zijn, hoeft u niets meer te doen. Andere gebruikers in uw organisatie hebben toegang tot het dashboard en kunnen deze wijzigen op basis van hun rol op abonnementsniveau.

## <a name="assign-access-to-a-dashboard"></a>Toegang tot een dashboard toewijzen

U een groep gebruikers toewijzen aan een rol voor dat dashboard.

1. Nadat u het dashboard hebt gepubliceerd, selecteert u de optie **Delen** of **Delen opheffen** om toegang te krijgen tot Delen **+ toegangscontrole**.

1. Selecteer **Gebruikers beheren**in Delen **+ toegangscontrole**.

    ![gebruikers beheren voor een dashboard](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Selecteer **Roltoewijzingen** om bestaande gebruikers te zien die al een rol voor dit dashboard hebben toegewezen.

1. Als u een nieuwe gebruiker of groep wilt toevoegen, selecteert u **Toevoegen** en **roltoewijzing toevoegen**.

    ![een gebruiker toevoegen voor toegang tot het dashboard](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Selecteer de rol die de machtigingen vertegenwoordigt die moeten worden verleend. Selecteer in dit voorbeeld **Bijdragers**.

1. Selecteer de gebruiker of groep die aan de rol moet worden toegewezen. Als u de gebruiker of groep die u zoekt niet ziet in de lijst, gebruikt u het zoekvak. Je lijst met beschikbare groepen is afhankelijk van de groepen die je hebt gemaakt in Active Directory.

1. Wanneer u klaar bent met het toevoegen van gebruikers of groepen, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

* Zie [Ingebouwde rollen voor Azure-resources voor](../role-based-access-control/built-in-roles.md)een lijst met rollen.
* Zie [Azure-resources beheren met behulp van de Azure-portal](resource-group-portal.md)voor meer informatie over het beheren van resources.

