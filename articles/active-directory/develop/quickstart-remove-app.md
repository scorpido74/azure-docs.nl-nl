---
title: App verwijderen die is geregistreerd bij het Microsoft-identiteitsplatform | Azure
description: Leer hoe u een toepassing verwijdert die bij het Microsoft Identity Platform is geregistreerd.
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
ms.openlocfilehash: 3cc9e4458f14a63bad7f484bc16683248895ede9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240847"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Snelstart: een toepassing verwijderen die is geregistreerd bij het Microsoft-identiteitsplatform

Bedrijfsontwikkelaars en SaaS-providers (software als een dienst) die toepassingen hebben geregistreerd bij het Microsoft Identity Platform moeten mogelijk de registratie van een toepassing verwijderen.

In deze snelstart leert u het volgende:

* Een toepassing verwijderen die is geschreven door u of uw organisatie
* Een toepassing verwijderen die is geschreven door een andere organisatie

## <a name="prerequisites"></a>Vereisten

U moet een tenant hebben die er aanvragen op heeft geregistreerd. Zie [Een toepassing registreren met het Microsoft Identity Platform](quickstart-register-app.md) voor informatie over het toevoegen en registeren van apps.

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Een toepassing verwijderen die is geschreven door u of uw organisatie

Toepassingen die u of uw organisatie hebben geregistreerd, worden vertegenwoordigd door een toepassingsobject en een service-principalobject in uw tenant. Zie [Toepassingsobjecten en service-principalobjecten](active-directory-application-objects.md) voor meer informatie.

### <a name="to-remove-an-application"></a>Toepassing verwijderen

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
2. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
3. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service** en selecteer **vervolgens App-registraties**. Zoek en selecteer de toepassing die u wilt configureren. Wanneer u de app hebt geselecteerd, ziet u de pagina **Overzicht**.
4. Selecteer **Verwijderen** op de pagina **Overzicht**.
5. Selecteer **Ja** om te bevestigen dat u de app wilt verwijderen.

   > [!NOTE]
   > Als u een toepassing wilt verwijderen, moet u vermeld staan als de eigenaar van de toepassing of over beheerdersrechten beschikken.

## <a name="remove-an-application-authored-by-another-organization"></a>Een toepassing verwijderen die is geschreven door een andere organisatie

Als u **App-registraties** bekijkt binnen de context van een tenant, dan is een subset van de toepassingen die onder **Alle apps** wordt weergegeven van een andere tenant en zijn deze tijdens het toestemmingsproces bij uw tenant geregistreerd. Om precies te zijn, worden deze toepassingen alleen vertegenwoordigd door een service-principal-object in uw tenant, zonder bijbehorend toepassingsobject. Zie [Toepassings- en service-principal-objecten in Azure Active Directory](active-directory-application-objects.md) voor meer informatie over de verschillen tussen toepassings- en service-principal-objecten.

Als u de toegang van een toepassing tot uw directory wilt verwijderen (nadat u toestemming hebt verleend), moet de bedrijfsbeheerder de service-principal van de toepassing verwijderen. De beheerder moet globale beheerderstoegang hebben en kan de toepassing verwijderen via de Azure-portal of de [Azure Active Directory PowerShell-cmdlets](https://go.microsoft.com/fwlink/?LinkId=294151) gebruiken om de toegang op te heffen.

## <a name="next-steps"></a>Volgende stappen

Lees meer over andere gerelateerde snelstarts voor app-beheer:

* [Een toepassing registreren met het Microsoft Identity Platform](quickstart-register-app.md)
* [Een clienttoepassing configureren voor toegang tot web-API's](quickstart-configure-app-access-web-apis.md)
* [Een toepassing configureren voor het beschikbaar maken van web-API's](quickstart-configure-app-expose-web-apis.md)
* [De accounts wijzigen die worden ondersteund door een toepassing](quickstart-modify-supported-accounts.md)
