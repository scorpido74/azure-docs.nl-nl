---
title: 'Snelstart: een geregistreerde app verwijderen uit Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart leert u hoe u een toepassing kunt verwijderen die bij Microsoft Identity Platform is geregistreerd.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 0a0150112602cd34168f64132785faf1f8c33f62
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91612384"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Quickstart: Een geregistreerde toepassing verwijderen uit het Microsoft Identity Platform

Bedrijfsontwikkelaars en SaaS-providers (software als een dienst) die toepassingen hebben geregistreerd bij het Microsoft Identity Platform moeten mogelijk de registratie van een toepassing verwijderen.

In deze snelstart leert u het volgende:

* Een toepassing verwijderen die is geschreven door u of uw organisatie
* Een toepassing verwijderen die is geschreven door een andere organisatie

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [quickstart: Een toepassing registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Een toepassing verwijderen die is geschreven door u of uw organisatie

Toepassingen die u of uw organisatie hebben geregistreerd, worden vertegenwoordigd door een toepassingsobject en een service-principalobject in uw tenant. Zie [Toepassingsobjecten en service-principalobjecten](./app-objects-and-service-principals.md) voor meer informatie.

Als u een toepassing wilt verwijderen, moet u vermeld staan als de eigenaar van de toepassing of over beheerdersrechten beschikken.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u de portalsessie in op de gewenste Azure Active Directory-tenant.
1. Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service en selecteer vervolgens **App-registraties**. Zoek en selecteer de toepassing die u wilt configureren. Wanneer u de app hebt geselecteerd, ziet u de pagina **Overzicht**.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.
1. Selecteer **Ja** om te bevestigen dat u de app wilt verwijderen.

## <a name="remove-an-application-authored-by-another-organization"></a>Een toepassing verwijderen die is geschreven door een andere organisatie

Als u **App-registraties** bekijkt binnen de context van een tenant, dan is een subset van de toepassingen die onder **Alle apps** wordt weergegeven van een andere tenant en zijn deze tijdens het toestemmingsproces bij uw tenant geregistreerd. Om precies te zijn, worden deze toepassingen alleen vertegenwoordigd door een service-principal-object in uw tenant, zonder bijbehorend toepassingsobject. Zie [Toepassings- en service-principal-objecten in Azure Active Directory](./app-objects-and-service-principals.md) voor meer informatie over de verschillen tussen toepassings- en service-principal-objecten.

Als u de toegang van een toepassing tot uw directory wilt verwijderen (nadat u toestemming hebt verleend), moet de bedrijfsbeheerder de service-principal van de toepassing verwijderen. De beheerder moet globale beheerderstoegang hebben en kan de toepassing verwijderen via de Azure-portal of de [Azure Active Directory PowerShell-cmdlets](https://go.microsoft.com/fwlink/?LinkId=294151) gebruiken om de toegang op te heffen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over toepassings- en service-principalobjecten in Microsoft Identity Platform:

> [!div class="nextstepaction"]
> [Toepassings- en service-principal-objecten in Azure Active Directory](app-objects-and-service-principals.md)