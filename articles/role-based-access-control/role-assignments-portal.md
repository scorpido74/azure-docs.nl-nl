---
title: Toegang tot Azure-resources beheren met RBAC en de Azure Portal | Microsoft Docs
description: Meer informatie over het beheren van de toegang tot Azure-resources voor gebruikers, groepen, service-principals en beheerde identiteiten met behulp van RBAC (op rollen gebaseerd toegangs beheer) en de Azure Portal. U vindt hier instructies voor het weergeven van toegang, het verlenen van toegang en het intrekken van toegang.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e9a53c41535c17de2d56227012160c7f6eb25c6
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337624"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Toegang tot Azure-resources beheren met RBAC en de Azure Portal

[Op rollen gebaseerd toegangsbeheer (RBAC)](overview.md) is de manier waarop u de toegang tot Azure-resources beheert. In dit artikel wordt beschreven hoe u toegang beheert met behulp van de Azure Portal. Zie [beheerders rollen weer geven en toewijzen in azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md)als u de toegang tot Azure Active Directory wilt beheren.

## <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt toevoegen en verwijderen, hebt u het volgende nodig:

- `Microsoft.Authorization/roleAssignments/write` en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals beheerder of [eigenaar](built-in-roles.md#owner) van [gebruikers toegang](built-in-roles.md#user-access-administrator)

## <a name="overview-of-access-control-iam"></a>Overzicht van toegangs beheer (IAM)

**Toegangs beheer (IAM)** is de Blade die u gebruikt om de toegang tot Azure-resources te beheren. Het is ook bekend als identiteits-en toegangs beheer en wordt weer gegeven op verschillende locaties in de Azure Portal. Hieronder ziet u een voor beeld van de Blade toegangs beheer (IAM) voor een abonnement.

![Blade toegangs beheer (IAM) voor een abonnement](./media/role-assignments-portal/access-control-numbers.png)

In de volgende tabel wordt beschreven waarvoor sommige elementen worden gebruikt voor:

| # | Element | Wat u gebruikt voor |
| --- | --- | --- |
| 1 | Resource waar toegangs beheer (IAM) is geopend | Bereik identificeren (abonnement in dit voor beeld) |
| 2 | Knop **toevoegen** | Roltoewijzingen toevoegen |
| 3 | Tabblad **toegang controleren** | De roltoewijzingen voor één gebruiker weer geven |
| 4 | **Tabblad roltoewijzingen** | De roltoewijzingen in het huidige bereik weer geven |
| 5 | Tabblad **rollen** | Alle rollen en machtigingen weer geven |

Als u de Blade toegangs beheer (IAM) het meest effectief wilt maken, is het handig als u de volgende drie vragen kunt beantwoorden wanneer u toegang probeert te beheren:

1. **Wie moet er toegang toe hebben?**

    Wie verwijst naar een gebruiker, groep, Service-Principal of beheerde identiteit. Dit wordt ook wel een *beveiligingsprincipal*genoemd.

1. **Welke machtigingen moeten er worden toegewezen?**

    Machtigingen worden samen in rollen gegroepeerd. U kunt kiezen uit een lijst met verschillende ingebouwde rollen.

1. **Waar hebben ze toegang nodig?**

    Waar verwijst naar de set resources waarop de toegang van toepassing is. Waar kan een beheer groep, een abonnement, een resource groep of een enkele resource, zoals een opslag account, zijn. Dit wordt het *bereik*genoemd.

## <a name="open-access-control-iam"></a>Toegangs beheer openen (IAM)

Het eerste wat u moet beslissen, is waar u de Blade toegangs beheer (IAM) kunt openen. Dit is afhankelijk van de resources waarvoor u de toegang wilt beheren. Wilt u de toegang beheren voor alles in een beheer groep, alles in een abonnement, alles in een resource groep of een enkele resource?

1. Klik in de Azure Portal op **alle services** en selecteer vervolgens het bereik. U kunt bijvoorbeeld **beheer groepen**, **abonnementen**, **resource groepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **toegangsbeheer (IAM)** .

    Hieronder ziet u een voor beeld van de Blade toegangs beheer (IAM) voor een abonnement. Als u hier wijzigingen voor toegangs beheer aanbrengt, worden deze toegepast op het hele abonnement.

    ![Blade toegangs beheer (IAM) voor een abonnement](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Rollen en machtigingen weergeven

Een roldefinitie is een verzameling machtigingen die u gebruikt voor roltoewijzingen. Azure heeft meer dan 70 [ingebouwde rollen voor Azure-resources](built-in-roles.md). Volg deze stappen om de beschik bare rollen en machtigingen weer te geven.

1. Open **toegangs beheer (IAM)** in elk bereik.

1. Klik op het tabblad **rollen** om een lijst met alle ingebouwde en aangepaste rollen weer te geven.

   U kunt het aantal gebruikers en groepen zien dat is toegewezen aan elke rol in het huidige bereik.

   ![Lijst met rollen](./media/role-assignments-portal/roles-list.png)

1. Klik op een afzonderlijke rol om te zien wie deze rol heeft toegewezen en Bekijk ook de machtigingen voor de rol.

   ![Rollen toewijzingen](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Roltoewijzingen weergeven

Bij het beheren van de toegang wilt u weten wie er toegang heeft, wat zijn de machtigingen en wat het bereik is. Als u de toegang voor een gebruiker, groep, Service-Principal of beheerde identiteit wilt weer geven, bekijkt u de roltoewijzingen.

### <a name="view-role-assignments-for-a-single-user"></a>Roltoewijzingen voor één gebruiker weer geven

Volg deze stappen om de toegang voor één gebruiker, groep, Service-Principal of beheerde identiteit voor een bepaald bereik weer te geven.

1. Open **toegangs beheer (IAM)** in een bereik, zoals beheer groep, abonnement, resource groep of resource, waar u de toegang wilt weer geven.

1. Klik op het tabblad **Toegang controleren**.

    ![Toegangsbeheer - Tabblad Toegang controleren](./media/role-assignments-portal/access-control-check-access.png)

1. Selecteer in de lijst **Zoeken** het type beveiligings-principal waarvoor u de toegang wilt controleren.

1. Voer in het zoekvak een tekenreeks in om de map te doorzoeken op weergavenamen, e-mailadressen of object-id's.

    ![De toegangsselectielijst controleren](./media/role-assignments-portal/check-access-select.png)

1. Klik op de beveiligings-principal om het deelvenster **Toewijzingen** te openen.

    ![Deelvenster Toewijzingen](./media/role-assignments-portal/check-access-assignments.png)

    In dit deelvenster ziet u de rollen die zijn toegewezen aan de geselecteerde beveiligings-principal en het bereik. Als er een in dit bereik toewijzingen zijn geweigerd of zijn overgenomen, worden deze weergegeven.

### <a name="view-all-role-assignments-at-a-scope"></a>Alle roltoewijzingen in een bereik weer geven

1. Open **toegangs beheer (IAM)** in een bereik, zoals beheer groep, abonnement, resource groep of resource, waar u de toegang wilt weer geven.

1. Klik op **het tabblad roltoewijzingen om** alle roltoewijzingen in dit bereik weer te geven.

   ![Toegangs beheer-tabblad roltoewijzingen](./media/role-assignments-portal/access-control-role-assignments.png)

   Op het tabblad roltoewijzingen kunt u zien wie toegang heeft tot dit bereik. U ziet dat voor sommige rollen het bereik is ingesteld op **Deze resource**, terwijl andere zijn **(Overgenomen)** uit een ander bereik. De toegang wordt specifiek aan deze resource toegewezen of overgenomen van een toewijzing aan het bovenliggende bereik.

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

Als u in RBAC toegang wilt verlenen, wijst u een rol toe aan een gebruiker, groep, Service-Principal of beheerde identiteit. Volg deze stappen om toegang te verlenen aan verschillende bereiken.

### <a name="assign-a-role-at-a-scope"></a>Een rol aan een bereik toewijzen

1. Open **toegangs beheer (IAM)** in een bereik, zoals de beheer groep, het abonnement, de resource groep of de resource, waar u toegang wilt verlenen.

1. Klik op **het tabblad roltoewijzingen om** alle roltoewijzingen in dit bereik weer te geven.

1. Klik op **Toevoegen** > **Roltoewijzing toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Toevoegen](./media/role-assignments-portal/add-menu.png)

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

1. Selecteer een gebruiker, groep, Service-Principal of beheerde identiteit in de **selectie** lijst. Als u de beveiligings-principal niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om te zoeken naar weergavenamen, e-mailadressen en object-id's.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken wordt de rol bij de geselecteerde scope toegewezen aan de beveiligingsprincipal.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Een gebruiker toewijzen als beheerder van een abonnement

Als u een gebruiker beheerder van een Azure-abonnement wilt maken, wijst u deze toe aan de rol van [eigenaar](built-in-roles.md#owner) op het abonnements bereik. De rol van eigenaar geeft de gebruiker volledige toegang tot alle resources in het abonnement, waaronder het recht om toegang aan anderen te delegeren. Deze stappen zijn hetzelfde als die voor elke andere functietoewijzing.

1. Klik in de Azure-portal op de optie **Alle services** en vervolgens op **Abonnementen**.

1. Klik op het abonnement waartoe u toegang wilt verlenen.

1. Klik op **toegangsbeheer (IAM)** .

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.

1. Klik op **Toevoegen** > **Roltoewijzing toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Toevoegen](./media/role-assignments-portal/add-menu.png)

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** de rol **Eigenaar**.

1. Selecteer een gebruiker in de lijst **Selecteren**. Als u de gebruiker niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om in de map te zoeken naar weergavenamen en e-mailadressen.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogenblikken wordt de rol van eigenaar op abonnementsniveau toegewezen aan de gebruiker.

## <a name="remove-role-assignments"></a>Roltoewijzingen verwijderen

Als u in RBAC de toegang wilt intrekken voor een rol, verwijdert u de roltoewijzing. Volg deze stappen om de toegang te verwijderen.

1. Open **toegangs beheer (IAM)** in een bereik, zoals de beheer groep, het abonnement, de resource groep of de resource, waar u de toegang wilt verwijderen.

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.

1. Schakel in de lijst met roltoewijzingen het selectievakje in naast de beveiligings-principal met de roltoewijzing die u wilt verwijderen.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klik op **Verwijderen**.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment.png)

1. Klik op **Ja** om te bevestigen dat u de roltoewijzing inderdaad wilt verwijderen.

    Overgenomen toewijzingen kunnen niet worden verwijderd. Als u een overgenomen roltoewijzing wilt verwijderen, moet u dit te doen voor het bereik waarvoor de roltoewijzing is gemaakt. In de kolom **bereik** vindt u naast **(overgenomen)** een koppeling waarmee u naar het bereik gaat waar deze rol is toegewezen. Ga naar het bereik dat hier wordt weergegeven om de roltoewijzing te verwijderen.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Toegang tot Azure-resources verlenen aan een gebruiker met behulp van RBAC en de Azure-portal](quickstart-assign-role-user-portal.md)
* [Zelfstudie: Toegang tot Azure-resources verlenen aan een gebruiker met behulp van RBAC en Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Problemen met RBAC voor Azure-resources oplossen](troubleshooting.md)
* [Resources organiseren met beheergroepen voor Azure](../governance/management-groups/overview.md)
