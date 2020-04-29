---
title: 'Quick Start: Tenant toepassingen weer geven met Azure Active Directory'
description: In deze Quick Start gebruikt u de Azure Portal om de toepassingen in uw Azure Active Directory (Azure AD)-Tenant weer te geven.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74421816"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Snelstartgids: uw Azure Active Directory-Tenant toepassingen weer geven

In deze quickstart wordt de Azure-portal gebruikt om de toepassingen in uw Azure AD-tenant (Azure Active Directory) weer te geven.

## <a name="before-you-begin"></a>Voordat u begint

Uw Azure AD-tenant moet over minstens één toepassing beschikken om resultaten te zien. Raadpleeg de quickstart [Een toepassing toevoegen](add-application-portal.md) om een toepassing toe te voegen.

Meld u bij de [Azure-portal](https://portal.azure.com) aan als globale beheerder van uw Azure AD-tenant, als beheerder van cloudtoepassingen of als toepassingsbeheerder.

## <a name="find-the-list-of-tenant-applications"></a>De lijst met tenanttoepassingen zoeken

Azure AD-tenanttoepassingen worden weergegeven in de sectie **Bedrijfsapps** van de Azure-portal.

Ga als volgt te werk om de tenanttoepassingen te zoeken:

1. In de **[Azure Portal](https://portal.azure.com)**, in het navigatie venster aan de linkerkant, selecteert u **Azure Active Directory**.
1. Selecteer in **Azure Active Directory** het deel venster Azure Active Directory **bedrijfs toepassingen**.
1. Selecteer **alle toepassingen**in het vervolg keuzemenu **toepassings type** en kies **Toep assen**. Er wordt een willekeurige selectie uit uw tenanttoepassingen weergegeven.
1. Als u meer toepassingen wilt weer geven, selecteert u onder aan de lijst de optie **laden** . Afhankelijk van het aantal toepassingen in de tenant is het welllicht gemakkelijker om te [zoeken naar een bepaalde toepassing](#search-for-a-tenant-application) in plaats van door de lijst te schuiven.

## <a name="select-viewing-options"></a>Weergaveopties selecteren

Selecteer opties op basis van wat u zoekt.

1. U kunt de toepassingen weer geven op **toepassings type**, **toepassings status**en **zicht baarheid van toepassingen**.
1. Kies onder **Toepassingstype** een van de volgende opties:

    - **Bedrijfstoepassingen** geeft niet-Microsoft-toepassingen weer.
    - **Microsoft-toepassingen** geeft Microsoft-toepassingen weer.
    - **Alle toepassingen** geeft zowel niet-Microsoft- als Microsoft-toepassingen weer.

1. Kies onder **Toepassingsstatus** een van de volgende opties: **Alle**, **Uitgeschakeld** of **Ingeschakeld**. De optie **Alle** omvat zowel de uitgeschakelde als de ingeschakelde toepassingen.
1. Kies onder **Zichtbaarheid van toepassing** de optie **Alle** of **Verborgen**. De **verborgen** optie toont toepassingen die zich in de Tenant bevinden, maar zijn niet zichtbaar voor gebruikers.
1. Nadat u de gewenste opties hebt gekozen, selecteert u **Toep assen**.

## <a name="search-for-a-tenant-application"></a>Zoeken naar een tenanttoepassing

Zoeken naar een bepaalde toepassing:

1. Selecteer in het menu **toepassings type** **alle toepassingen**en klik **op Toep assen**.
1. Voer de naam in van de toepassing die u zoekt. Als de toepassing is toegevoegd aan uw Azure AD-Tenant, wordt deze weer gegeven in de zoek resultaten. Dit voor beeld laat zien dat GitHub niet is toegevoegd aan de Tenant toepassingen.

    ![Voor beeld ziet u dat een app niet is toegevoegd aan de Tenant](media/view-applications-portal/search-for-tenant-application.png)

1. Voer de eerste paar letters van een toepassingsnaam in. In dit voorbeeld ziet u dat alle toepassingen beginnen met **Verkoop**.

    ![Voor beeld worden alle apps weer gegeven die beginnen met verkopen](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de toepassingen in uw Azure AD-Tenant kunt weer geven. U hebt geleerd hoe u de lijst met toepassingen kunt filteren op toepassings type, status en zicht baarheid. U hebt ook geleerd hoe u kunt zoeken naar een bepaalde toepassing.

Nu u de toepassing hebt gevonden die u zoekt, kunt u nog steeds [meer toepassingen toevoegen aan uw Tenant](add-application-portal.md). U kunt ook de toepassing selecteren om eigenschappen en configuratie opties weer te geven of te bewerken. U kunt bijvoorbeeld ook eenmalige aanmelding configureren.

> [!div class="nextstepaction"]
> [Eenmalige aanmelding configureren](configure-single-sign-on-non-gallery-applications.md)
