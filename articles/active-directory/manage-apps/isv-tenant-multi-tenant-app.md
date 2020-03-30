---
title: Een Azure-tenant maken voor een multi-tenanttoepassing
description: Richtlijnen voor onafhankelijke softwareleveranciers over de integratie met Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812608"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Een Azure-tenant maken voor een multi-tenanttoepassing  

Als u toegang wilt bieden tot uw multi-tenanttoepassing, moet u een Azure Active Directory-tenant maken om de toepassing te registreren en de federatie van de identiteiten van uw klant in te schakelen. Zie [Het juiste federatieprotocol voor uw multi-tenanttoepassing kiezen.](isv-choose-multi-tenant-federation.md) Met deze tenant u uw toepassing en de federatie testen in een omgeving die vergelijkbaar is met uw Azure AD-omgevingen voor klanten.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Kosten voor het hosten van een multi-tenant applicatie

Azure Active Directory is beschikbaar in meerdere edities. [Zie de gedetailleerde functievergelijking](https://azure.microsoft.com/pricing/details/active-directory/).

U uw Azure-abonnement en Azure active directory gratis maken en basisfuncties gebruiken.

## <a name="create-your-tenant"></a>Uw tenant maken

1. Maak uw tenant. Zie [Een dev-omgeving instellen](../develop/quickstart-create-new-tenant.md).

2. Toegang tot één aanmelding tot uw toepassing inschakelen en testen,

   a. **Voor OIDC- of Oath-toepassingen** [registreer u uw aanvraag](../develop/quickstart-register-app.md) als een multi-tenant applicatie. Selecteer de optie Accounts in een organisatiemap en persoonlijke Microsoft-accounts in ondersteunde accounttypen

   b. **Voor SAML- en WS-Fed-gebaseerde toepassingen** [configureert u SAML-gebaseerde toepassingen voor één aanmelding](configure-single-sign-on-non-gallery-applications.md) met behulp van een generieke SAML-sjabloon in Azure AD.

U ook [een toepassing met één tenant converteren naar multi-tenant](../develop/howto-convert-app-to-be-multi-tenant.md) indien nodig.

## <a name="next-steps"></a>Volgende stappen

[SSO integreren in uw applicatie](isv-sso-content.md)
