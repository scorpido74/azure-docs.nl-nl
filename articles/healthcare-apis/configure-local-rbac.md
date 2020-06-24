---
title: Op lokale rollen gebaseerde Access Control (RBAC) voor Azure API configureren voor FHIR
description: In dit artikel wordt beschreven hoe u de Azure-API voor FHIR configureert voor het gebruik van een externe Azure AD-Tenant voor gegevens vlak
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: mihansen
ms.openlocfilehash: a8c1b36d6a439297dfb0bbcb34efe059349fc5a2
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "84871908"
---
# <a name="configure-local-rbac-for-fhir"></a>Lokale RBAC configureren voor FHIR 

In dit artikel wordt uitgelegd hoe u de Azure-API voor FHIR configureert om een externe, secundaire Azure Active Directory Tenant te gebruiken voor het beheren van toegang tot gegevens vlak. Gebruik deze modus alleen als het niet mogelijk is om de Azure Active Directory Tenant te gebruiken die aan uw abonnement is gekoppeld.

> [!NOTE]
> Als uw FHIR-service gegevens vlak is geconfigureerd voor het gebruik van uw primaire Azure Active Directory Tenant die is gekoppeld aan uw abonnement, [gebruikt u Azure RBAC om gegevenslaag rollen toe te wijzen](configure-azure-rbac.md).

## <a name="add-service-principal"></a>Service-principal toevoegen

Met lokale RBAC kunt u een externe Azure Active Directory Tenant met uw FHIR-server gebruiken. Om het RBAC-systeem in staat te stellen groepslid maatschappen in deze Tenant te controleren, moet de Azure API voor FHIR een Service-Principal in de Tenant hebben. Deze service-principal wordt automatisch gemaakt in tenants die zijn gekoppeld aan abonnementen die de Azure-API voor FHIR hebben geïmplementeerd. als uw Tenant echter geen abonnement heeft, moet een Tenant beheerder deze service-principal maken met een van de volgende opdrachten:

De `Az` Power shell-module gebruiken:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

u kunt ook de `AzureAd` Power shell-module gebruiken:

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

u kunt ook Azure CLI gebruiken:

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>Lokale RBAC configureren

U kunt de Azure-API voor FHIR configureren voor het gebruik van een externe of secundaire Azure Active Directory Tenant op de Blade **authenticatie** :

![Lokale RBAC-toewijzingen](media/rbac/local-rbac-guids.png).

Voer in het vak machtiging een geldige Azure Active Directory-Tenant in. Zodra de Tenant is gevalideerd, moet het vakje **toegestane object-id's** worden geactiveerd en kunt u een lijst met id-object-id's invoeren. Deze Id's kunnen de identiteits object-Id's zijn van:

* Een Azure Active Directory gebruiker.
* Een Azure Active Directory Service-Principal.
* Een Azure Active Directory-beveiligings groep.

U kunt het artikel lezen over het [vinden van identiteits object-id's](find-identity-object-ids.md) voor meer informatie.

Nadat u de vereiste object-Id's hebt ingevoerd, klikt u op **Opslaan** en wacht u totdat de wijzigingen zijn opgeslagen voordat u probeert toegang te krijgen tot het gegevens vlak met behulp van de toegewezen gebruikers, service-principals of groepen.

## <a name="caching-behavior"></a>Cache gedrag

De Azure API voor FHIR neemt beslissingen tot vijf minuten in de cache. Als u een gebruiker toegang verleent tot de FHIR-server door deze toe te voegen aan de lijst met toegestane object-Id's, of als u ze uit de lijst verwijdert, moet u er rekening mee houden dat het tot vijf minuten duurt voordat wijzigingen in de machtigingen worden door gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u FHIR kunt toewijzen met behulp van een extern (secundair) Azure Active Directory Tenant. Meer informatie over aanvullende instellingen voor de Azure API voor FHIR:
 
>[!div class="nextstepaction"]
>[Aanvullende instellingen Azure API voor FHIR](azure-api-for-fhir-additional-settings.md)

