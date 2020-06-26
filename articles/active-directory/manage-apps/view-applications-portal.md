---
title: 'Snelstart: tenanttoepassingen weergeven met Azure Active Directory'
description: In deze snelstart wordt Azure Portal gebruikt om de toepassingen in uw Azure AD-tenant (Azure Active Directory) weer te geven.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce3a1a55683b0882984a4986cf59853933954274
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763003"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Quickstart: Azure Active Directory-tenanttoepassingen weergeven

In deze quickstart wordt de Azure-portal gebruikt om de toepassingen in uw Azure AD-tenant (Azure Active Directory) weer te geven.

## <a name="before-you-begin"></a>Voordat u begint

Uw Azure AD-tenant moet over minstens één toepassing beschikken om resultaten te zien. Raadpleeg de quickstart [Een toepassing toevoegen](add-application-portal.md) om een toepassing toe te voegen.

Meld u bij de [Azure-portal](https://portal.azure.com) aan als globale beheerder van uw Azure AD-tenant, als beheerder van cloudtoepassingen of als toepassingsbeheerder.

## <a name="find-the-list-of-tenant-applications"></a>De lijst met tenanttoepassingen zoeken

Azure AD-tenanttoepassingen worden weergegeven in de sectie **Bedrijfsapps** van de Azure-portal.

Ga als volgt te werk om de tenanttoepassingen te zoeken:

1. Selecteer **Azure Active Directory** in **[Azure Portal](https://portal.azure.com)** in het navigatiepaneel aan de linkerkant.
1. Selecteer **Bedrijfstoepassingen** in het deelvenster **Azure Active Directory**.
1. Selecteer in de vervolgkeuzelijst **Toepassingstype** de optie **Alle toepassingen** en kies **Toepassen**. Er wordt een willekeurige selectie uit uw tenanttoepassingen weergegeven.
1. Als u meer toepassingen wilt zien, selecteert u onderaan de lijst **Meer laden**. Afhankelijk van het aantal toepassingen in de tenant is het welllicht gemakkelijker om te [zoeken naar een bepaalde toepassing](#search-for-a-tenant-application) in plaats van door de lijst te schuiven.

## <a name="select-viewing-options"></a>Weergaveopties selecteren

Selecteer opties op basis van wat u zoekt.

1. U kunt de toepassingen weergeven op **Toepassingstype**, **Toepassingsstatus** en **Zichtbaarheid van toepassing**.
1. Kies onder **Toepassingstype** een van de volgende opties:

    - **Bedrijfstoepassingen** geeft niet-Microsoft-toepassingen weer.
    - **Microsoft-toepassingen** geeft Microsoft-toepassingen weer.
    - **Alle toepassingen** geeft zowel niet-Microsoft- als Microsoft-toepassingen weer.

1. Kies onder **Toepassingsstatus** een van de volgende opties: **Alle**, **Uitgeschakeld** of **Ingeschakeld**. De optie **Alle** omvat zowel de uitgeschakelde als de ingeschakelde toepassingen.
1. Kies onder **Zichtbaarheid van toepassing** de optie **Alle** of **Verborgen**. De optie **Verborgen** geeft toepassingen weer die zich in de tenant bevinden maar die niet zichtbaar zijn voor gebruikers.
1. Als u de gewenste opties hebt gekozen, selecteert u **Toepassen**.

## <a name="search-for-a-tenant-application"></a>Zoeken naar een tenanttoepassing

Ga als volgt te werk om naar een bepaalde toepassing te zoeken:

1. Selecteer in het menu **Toepassingstype** de optie **Alle toepassingen** en kies **Toepassen**.
1. Voer de naam in van de toepassing die u zoekt. Als de toepassing is toegevoegd aan uw Azure AD-tenant, wordt deze weergegeven in de zoekresultaten. In dit voorbeeld ziet u dat GitHub niet is toegevoegd aan de tenanttoepassingen.

    ![Voorbeeld toont een app die niet is toegevoegd aan de tenant](media/view-applications-portal/search-for-tenant-application.png)

1. Voer de eerste paar letters van een toepassingsnaam in. In dit voorbeeld ziet u dat alle toepassingen beginnen met **Verkoop**.

    ![Voorbeeld toont alle apps die beginnen met Sales](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u geleerd hoe u de toepassingen kunt weergeven in uw Azure AD-tenant. U hebt geleerd hoe u de lijst met toepassingen kunt filteren op toepassingstype, status en zichtbaarheid. U hebt ook geleerd hoe u kunt zoeken naar een bepaalde toepassing.

Nu u de toepassing hebt gevonden die u zoekt, kunt u doorgaan en [Meer toepassingen toevoegen aan uw tenant](add-application-portal.md). U kunt ook de toepassing selecteren om eigenschappen en configuratieopties weer te geven of te bewerken. U kunt bijvoorbeeld ook eenmalige aanmelding configureren.

> [!div class="nextstepaction"]
> [Eenmalige aanmelding configureren](configure-single-sign-on-non-gallery-applications.md)
