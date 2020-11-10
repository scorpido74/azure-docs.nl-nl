---
title: 'Quickstart: De accounttypen wijzigen die door een toepassing worden ondersteund | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart gaat u een toepassing configureren die is geregistreerd bij het Microsoft Identity Platform om te wijzigen wie, of welke accounts, toegang hebben tot de toepassing.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/27/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 2382eedcc14f683d354b88bf2eb8d53b2af40dbd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083266"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Quickstart: De accounts wijzigen die worden ondersteund door een toepassing

Wanneer u uw toepassing hebt geregistreerd bij het identiteitsplatform van Microsoft, hebt u opgegeven wie het kunnen gebruiken (welke accounttypen). U kunt bijvoorbeeld alleen accounts in uw organisatie hebben opgegeven. Dit is een *app met één tenant*. Het is ook mogelijk dat u accounts van elke organisatie (inclusief uw bedrijf) hebt opgegeven. Dit is een *app voor meerdere tenants*.

In deze quickstart leert u hoe u de configuratie van uw toepassing aanpast om te wijzigen wie, of welke accounttypen, toegang hebben tot de toepassing.

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [quickstart: Een toepassing registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>De registratie van de toepassing wijzigen om verschillende accounts te ondersteunen

Ga als volgt te werk om een andere instelling op te geven voor de accounttypen die worden ondersteund door een bestaande app-registratie:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties**. Selecteer vervolgens uw toepassing.
1. Geef nu op wie de toepassing kan gebruiken, ook wel aangeduid als de *doelgroep voor aanmelden*.

    | Ondersteunde accounttypen | Beschrijving |
    |-------------------------|-------------|
    | **Alleen accounts in deze organisatiemap** | Selecteer deze optie als u een toepassing bouwt die alleen is bedoeld voor gebruikers (of gasten) in *uw* tenant.<br><br>Dit wordt vaak een *LOB* -toepassing (Line-of-Business) genoemd. Dit is een toepassing met **één tenant** in het Microsoft Identity Platform. |
    | **Accounts in elke organisatiemap** | Selecteer deze optie als u wilt dat gebruikers in *een willekeurige* Azure AD-tenant uw toepassing kunnen gebruiken. Deze optie is geschikt als u bijvoorbeeld een SaaS-toepassing (Software-as-a-Service) bouwt die u aan meerdere organisaties wilt leveren.<br><br>Dit wordt een toepassing met **meerdere tenants** genoemd in het Microsoft Identity Platform. |
1. Selecteer **Opslaan**.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Waarom het wijzigen van een app voor meerdere tenants kan mislukken

Het overschakelen van een app-registratie van één tenant naar meerdere tenants kan soms mislukken als gevolg van naamconflicten met de URI van app-id's. Een voorbeeld van een URI van de app-id is `https://contoso.onmicrosoft.com/myapp`.

De URI van de app-id is een van de manieren waarop een toepassing wordt geïdentificeerd in protocolberichten. Voor een toepassing met één tenant is het voldoende dat de URI van de app-id uniek is binnen die tenant. Voor een toepassing met meerdere tenants moet deze wereldwijd uniek zijn, zodat Azure Active Directory de app in alle tenants kan vinden. Wereldwijde uniekheid wordt afgedwongen door te vereisen dat de URI van de app-id een hostnaam heeft die overeenkomt met een [geverifieerd uitgeversdomein](howto-configure-publisher-domain.md) van de Azure Active Directory-tenant.

Als de naam van uw tenant bijvoorbeeld *contoso.onmicrosoft.com* is, zou `https://contoso.onmicrosoft.com/myapp` een geldige URI voor de app-id zijn. Als uw tenant een geverifieerd domein van *contoso.com* heeft, zou `https://contoso.com/myapp` ook een geldige URI voor de app-id zijn. Als de URI van de app-id niet het tweede patroon (`https://contoso.com/myapp`) volgt, mislukt het converteren van de app-registratie naar meerdere tenants.

Bekijk [Een geverifieerd domein configureren](quickstart-modify-supported-accounts.md) voor meer informatie over het configureren van een geverifieerd uitgeversdomein.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: Uw app converteren naar meerdere tenants](howto-convert-app-to-be-multi-tenant.md)
