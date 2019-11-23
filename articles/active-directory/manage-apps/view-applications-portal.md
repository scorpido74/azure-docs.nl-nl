---
title: Quickstart - View tenant applications using Azure Active Directory
description: In this Quickstart, use the Azure portal to view the applications in your Azure Active Directory (Azure AD) tenant.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d40e968bb7079d50e3fa18889ae996c9b59c90f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421816"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Quickstart: View your Azure Active Directory tenant applications

In deze quickstart wordt de Azure-portal gebruikt om de toepassingen in uw Azure AD-tenant (Azure Active Directory) weer te geven.

## <a name="before-you-begin"></a>Voordat u begint

Uw Azure AD-tenant moet over minstens één toepassing beschikken om resultaten te zien. Raadpleeg de quickstart [Een toepassing toevoegen](add-application-portal.md) om een toepassing toe te voegen.

Meld u bij de [Azure-portal](https://portal.azure.com) aan als globale beheerder van uw Azure AD-tenant, als beheerder van cloudtoepassingen of als toepassingsbeheerder.

## <a name="find-the-list-of-tenant-applications"></a>De lijst met tenanttoepassingen zoeken

Azure AD-tenanttoepassingen worden weergegeven in de sectie **Bedrijfsapps** van de Azure-portal.

Ga als volgt te werk om de tenanttoepassingen te zoeken:

1. In the **[Azure portal](https://portal.azure.com)** , on the left navigation panel, select **Azure Active Directory**.
1. In the **Azure Active Directory** pane, select **Enterprise applications**.
1. From the **Application Type** drop-down menu, select **All Applications**, and choose **Apply**. Er wordt een willekeurige selectie uit uw tenanttoepassingen weergegeven.
1. To view more applications, select **Load more** at the bottom of the list. Afhankelijk van het aantal toepassingen in de tenant is het welllicht gemakkelijker om te [zoeken naar een bepaalde toepassing](#search-for-a-tenant-application) in plaats van door de lijst te schuiven.

## <a name="select-viewing-options"></a>Weergaveopties selecteren

Select options according to what you're looking for.

1. You can view the applications by **Application Type**, **Application Status**, and **Application visibility**.
1. Kies onder **Toepassingstype** een van de volgende opties:

    - **Bedrijfstoepassingen** geeft niet-Microsoft-toepassingen weer.
    - **Microsoft-toepassingen** geeft Microsoft-toepassingen weer.
    - **Alle toepassingen** geeft zowel niet-Microsoft- als Microsoft-toepassingen weer.

1. Kies onder **Toepassingsstatus** een van de volgende opties: **Alle**, **Uitgeschakeld** of **Ingeschakeld**. De optie **Alle** omvat zowel de uitgeschakelde als de ingeschakelde toepassingen.
1. Kies onder **Zichtbaarheid van toepassing** de optie **Alle** of **Verborgen**. The **Hidden** option shows applications that are in the tenant, but aren't visible to users.
1. After choosing the options you want, select **Apply**.

## <a name="search-for-a-tenant-application"></a>Zoeken naar een tenanttoepassing

To search for a particular application:

1. In the **Application Type** menu, select **All applications**, and choose **Apply**.
1. Voer de naam in van de toepassing die u zoekt. If the application has been added to your Azure AD tenant, it appears in the search results. This example shows that GitHub hasn't been added to the tenant applications.

    ![Example shows an app hasn't been added to the tenant](media/view-applications-portal/search-for-tenant-application.png)

1. Voer de eerste paar letters van een toepassingsnaam in. In dit voorbeeld ziet u dat alle toepassingen beginnen met **Verkoop**.

    ![Example shows all apps that start with Sales](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Volgende stappen

In this quickstart, you learned how to view the applications in your Azure AD tenant. You learned how to filter the list of applications by application type, status, and visibility. U hebt ook geleerd hoe u kunt zoeken naar een bepaalde toepassing.

Now that you've found the application you were looking for, you can continue to [Add more applications to your tenant](add-application-portal.md). Or, you can select the application to view or edit properties and configuration options. U kunt bijvoorbeeld ook eenmalige aanmelding configureren.

> [!div class="nextstepaction"]
> [Eenmalige aanmelding configureren](configure-single-sign-on-non-gallery-applications.md)
