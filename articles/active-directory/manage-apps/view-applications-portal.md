---
title: Snelstart - Tenanttoepassingen weergeven met Azure Active Directory
description: Gebruik in deze Quickstart de Azure-portal om de toepassingen in uw Azure Active Directory-tenant (Azure AD) weer te geven.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74421816"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Snelstart: uw Azure Active Directory-tenanttoepassingen weergeven

In deze quickstart wordt de Azure-portal gebruikt om de toepassingen in uw Azure AD-tenant (Azure Active Directory) weer te geven.

## <a name="before-you-begin"></a>Voordat u begint

Uw Azure AD-tenant moet over minstens één toepassing beschikken om resultaten te zien. Raadpleeg de quickstart [Een toepassing toevoegen](add-application-portal.md) om een toepassing toe te voegen.

Meld u bij de [Azure-portal](https://portal.azure.com) aan als globale beheerder van uw Azure AD-tenant, als beheerder van cloudtoepassingen of als toepassingsbeheerder.

## <a name="find-the-list-of-tenant-applications"></a>De lijst met tenanttoepassingen zoeken

Azure AD-tenanttoepassingen worden weergegeven in de sectie **Bedrijfsapps** van de Azure-portal.

Ga als volgt te werk om de tenanttoepassingen te zoeken:

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .
1. Selecteer **Enterprise-toepassingen**in het deelvenster **Azure Active Directory** .
1. Selecteer **alle toepassingen**in het vervolgkeuzemenu **Toepassingstype** en kies **Toepassen**. Er wordt een willekeurige selectie uit uw tenanttoepassingen weergegeven.
1. Als u meer toepassingen wilt weergeven, selecteert u **Meer laden** onder aan de lijst. Afhankelijk van het aantal toepassingen in de tenant is het welllicht gemakkelijker om te [zoeken naar een bepaalde toepassing](#search-for-a-tenant-application) in plaats van door de lijst te schuiven.

## <a name="select-viewing-options"></a>Weergaveopties selecteren

Selecteer opties op basis van wat u zoekt.

1. U de toepassingen bekijken op **toepassingstype,** **toepassingsstatus**en **zichtbaarheid van toepassingen.**
1. Kies onder **Toepassingstype** een van de volgende opties:

    - **Bedrijfstoepassingen** geeft niet-Microsoft-toepassingen weer.
    - **Microsoft-toepassingen** geeft Microsoft-toepassingen weer.
    - **Alle toepassingen** geeft zowel niet-Microsoft- als Microsoft-toepassingen weer.

1. Kies onder **Toepassingsstatus** een van de volgende opties: **Alle**, **Uitgeschakeld** of **Ingeschakeld**. De optie **Alle** omvat zowel de uitgeschakelde als de ingeschakelde toepassingen.
1. Kies onder **Zichtbaarheid van toepassing** de optie **Alle** of **Verborgen**. Met de optie **Verborgen** worden toepassingen weergegeven die zich in de tenant bevinden, maar die niet zichtbaar zijn voor gebruikers.
1. Nadat u de gewenste opties hebt gekozen, selecteert u **Toepassen**.

## <a name="search-for-a-tenant-application"></a>Zoeken naar een tenanttoepassing

Ga als lid van het volgende naar een bepaalde toepassing:

1. Selecteer In het menu **Toepassingstype** de optie **Alle toepassingen**en kies **Toepassen**.
1. Voer de naam in van de toepassing die u zoekt. Als de toepassing is toegevoegd aan uw Azure AD-tenant, wordt deze weergegeven in de zoekresultaten. In dit voorbeeld wordt weergegeven dat GitHub niet is toegevoegd aan de tenanttoepassingen.

    ![Voorbeeld laat zien dat een app niet is toegevoegd aan de tenant](media/view-applications-portal/search-for-tenant-application.png)

1. Voer de eerste paar letters van een toepassingsnaam in. In dit voorbeeld ziet u dat alle toepassingen beginnen met **Verkoop**.

    ![Voorbeeld toont alle apps die beginnen met Verkoop](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de toepassingen in uw Azure AD-tenant weergeven. U hebt geleerd hoe u de lijst met toepassingen filteren op toepassingstype, status en zichtbaarheid. U hebt ook geleerd hoe u kunt zoeken naar een bepaalde toepassing.

Nu u de toepassing hebt gevonden die u zocht, u [doorgaan met Meer toepassingen toevoegen aan uw tenant.](add-application-portal.md) U ook de toepassing selecteren om eigenschappen en configuratieopties weer te geven of te bewerken. U kunt bijvoorbeeld ook eenmalige aanmelding configureren.

> [!div class="nextstepaction"]
> [Eenmalige aanmelding configureren](configure-single-sign-on-non-gallery-applications.md)
