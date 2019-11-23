---
title: Toegangs beheer voor het maken van ondersteunings aanvragen in azure
description: Azure Role Access Control (RBAC) voor het beheren van de toegangs rechten voor het maken en beheren van ondersteunings aanvragen
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure-supportability
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 328f4928aff6a892849ce3e08fe794578b5e03a4
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299258"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure Role Access Control (RBAC) voor het beheren van de toegangs rechten voor het maken en beheren van ondersteunings aanvragen

> [!IMPORTANT]
> Deze hand matige aanpak voor het beheren van de toegangs rechten voor de ondersteunings aanvraag in Azure is vervangen door een ingebouwde rol. Zie voor meer informatie [Inzender voor ondersteunings aanvragen](../role-based-access-control/built-in-roles.md#support-request-contributor). 

[Met Access Control op basis van rollen (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) hebt u verfijnd toegangs beheer voor Azure.
Ondersteuningsaanvraag maken in de Azure Portal, [Portal.Azure.com](https://portal.azure.com), gebruikt het RBAC-model van Azure om te bepalen wie ondersteunings aanvragen kan maken en beheren.
Toegang wordt verleend door de juiste RBAC-rol toe te wijzen aan gebruikers, groepen en toepassingen bij een bepaald bereik, die een abonnement, resource groep of resource kunnen zijn.

We gaan een voor beeld bekijken: als eigenaar van een resource groep met lees machtigingen voor het abonnements bereik, kunt u alle resources in de resource groep, zoals websites, virtuele machines en subnetten, beheren.
Wanneer u echter probeert een ondersteunings aanvraag te maken op basis van de virtuele-machine bron, treedt de volgende fout op

![Abonnements fout](./media/create-manage-support-requests-using-access-control/subscription-error.png)

In ondersteunings aanvraag beheer hebt u schrijf machtiging of een rol met de ondersteunings actie micro soft. support/* bij het abonnements bereik nodig om ondersteunings aanvragen te kunnen maken en beheren.

In het volgende artikel wordt uitgelegd hoe u de aangepaste op rollen gebaseerde Access Control (RBAC) van Azure kunt gebruiken om ondersteunings aanvragen te maken en te beheren in de Azure Portal.

## <a name="getting-started"></a>Aan de slag

Met het bovenstaande voor beeld zou u een ondersteunings aanvraag voor uw resource kunnen maken als u een aangepaste RBAC-rol aan het abonnement hebt toegewezen door de eigenaar van het abonnement.
[Aangepaste RBAC-rollen](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) kunnen worden gemaakt met behulp van Azure PowerShell, de Azure-opdracht regel interface (CLI) en de rest API.

De eigenschap Actions van een aangepaste rol specificeert de Azure-bewerkingen waartoe de rol toegang verleent.
Als u een aangepaste rol wilt maken voor het beheer van ondersteunings aanvragen, moet de rol micro soft. support/* hebben.

Hier volgt een voor beeld van een aangepaste rol die u kunt gebruiken om ondersteunings aanvragen te maken en te beheren.
We hebben de rol ' Inzender voor ondersteunings aanvragen ' genoemd, en dat is de manier waarop we in dit artikel naar de aangepaste rol verwijzen.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Volg de stappen in [deze video](https://www.youtube.com/watch?v=-PaBaDmfwKI) voor meer informatie over het maken van een aangepaste rol voor uw abonnement.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Ondersteunings aanvragen maken en beheren in de Azure Portal

We gaan een voor beeld bekijken: u bent de eigenaar van het abonnement ' Visual Studio MSDN Subscription '.
Joe is uw peer die een resource-eigenaar is van een aantal resource groepen in dit abonnement en lees machtigingen heeft voor het abonnement.
U wilt toegang verlenen aan uw peer, Joe, de mogelijkheid om ondersteunings tickets te maken en te beheren voor de resources in dit abonnement.

1. De eerste stap is om naar het abonnement te gaan. Onder **instellingen**ziet u een lijst met gebruikers. Selecteer de gebruiker Joe, die lezers toegang heeft voor het abonnement. We gaan een nieuwe aangepaste rol toewijzen aan Joe.

    ![Rol toevoegen](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Klik op toevoegen onder de Blade gebruikers. Selecteer de aangepaste rol ' Inzender voor ondersteunings aanvragen ' in de lijst met rollen

    ![Rol voor ondersteunings bijdrage toevoegen](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Nadat u de rolnaam hebt geselecteerd, klikt u op gebruikers toevoegen en voert u de e-mail referenties van de Joe in. Klik op Selecteren.

    ![Add users (Gebruikers toevoegen)](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Klik op OK om door te gaan

    ![Toegang toevoegen](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Nu ziet u de gebruiker met de zojuist toegevoegde aangepaste rol ' mede werker ondersteunings aanvraag ' onder het abonnement waarvoor u de eigenaar bent

    ![Gebruiker toegevoegd](./media/create-manage-support-requests-using-access-control/user-added.png)

    Als Jan zich bij de portal aanmeldt, ziet Joe het abonnement waaraan Jan is toegevoegd.

7. Joe klikt op ' New Ondersteuningsaanvraag ' op de Blade Help en ondersteuning en kan ondersteunings aanvragen voor ' Visual Studio Ultimate met MSDN ' maken

    ![Nieuw ondersteuningsverzoek](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Als u op alle ondersteunings aanvragen hebt geklikt, ziet u de lijst met ondersteunings aanvragen die voor dit abonnement zijn gemaakt ![weer gave Details van de case](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Toegang tot ondersteunings aanvragen verwijderen in de Azure Portal

Net zoals het mogelijk is om toegang te verlenen aan een gebruiker om ondersteunings aanvragen te maken en te beheren, is het mogelijk om de toegang voor de gebruiker te verwijderen.

Als u de mogelijkheid wilt om ondersteunings aanvragen te maken en te beheren, gaat u naar het abonnement, klikt u op instellingen en klikt u op de gebruiker (in dit geval Joe). Klik met de rechter muisknop op de naam van de rol, ' Inzender voor ondersteunings aanvragen ' en klik op verwijderen.

![Toegang tot ondersteunings aanvragen verwijderen](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Als Joe zich aanmeldt bij de portal en probeert een ondersteunings aanvraag te maken, wordt de volgende fout aangetroffen:

![Abonnements fout-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Joe kan geen ondersteunings aanvragen zien als Jan alle ondersteunings aanvragen selecteert

![Case-details weer geven-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
