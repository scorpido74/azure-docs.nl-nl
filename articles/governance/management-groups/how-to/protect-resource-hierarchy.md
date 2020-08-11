---
title: Uw resource hiërarchie beveiligen-Azure governance
description: Meer informatie over het beveiligen van uw resource hiërarchie met hiërarchie-instellingen, zoals het instellen van de standaard beheer groep.
ms.date: 08/10/2020
ms.topic: conceptual
ms.openlocfilehash: 2dd6791e152ba3ef02f6e6f710589cbe7d3442bc
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056615"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Uw resource hiërarchie beveiligen

Uw resources, resource groepen, abonnementen, beheer groepen en Tenant vormen samen de resource hiërarchie. Instellingen in de hoofd beheer groep, zoals aangepaste Azure-rollen of Azure Policy-beleids toewijzingen, kunnen van invloed zijn op elke resource in de resource hiërarchie. Het is belang rijk om de resource hiërarchie te beschermen tegen wijzigingen die een negatieve invloed kunnen hebben op alle resources.

Beheer groepen hebben nu hiërarchie-instellingen waarmee de Tenant beheerder dit gedrag kan beheren. In dit artikel worden alle beschik bare hiërarchie-instellingen beschreven en wordt uitgelegd hoe u deze kunt instellen.

## <a name="rbac-permissions-for-hierarchy-settings"></a>RBAC-machtigingen voor hiërarchie-instellingen

Voor het configureren van een van de hiërarchie-instellingen zijn de volgende twee RBAC-bewerkingen vereist voor de hoofd beheer groep:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Met deze bewerkingen kan een gebruiker de hiërarchie-instellingen niet lezen en bijwerken. De bewerkingen bieden geen andere toegang tot de hiërarchie van de beheer groep of resources in de hiërarchie. Beide bewerkingen zijn beschikbaar in de beheerder van de ingebouwde rol hiërarchie- **instellingen**van Azure.

## <a name="setting---default-management-group"></a>Instelling: standaard beheer groep

Een nieuw abonnement dat in een Tenant wordt toegevoegd, wordt standaard toegevoegd als lid van de hoofd beheer groep. Als beleids toewijzingen, op rollen gebaseerd toegangs beheer (RBAC) en andere governance-constructies zijn toegewezen aan de hoofd beheer groep, hebben ze direct invloed op deze nieuwe abonnementen. Daarom passen veel organisaties deze constructs niet toe aan de hoofd beheer groep, zelfs als dat wel de gewenste plaats is om ze toe te wijzen. In andere gevallen is een meer beperkende set besturings elementen gewenst voor nieuwe abonnementen, maar niet aan alle abonnementen. Deze instelling ondersteunt beide use-cases.

Door toe te staan dat de standaard beheer groep voor nieuwe abonnementen kan worden gedefinieerd, kunnen bedrijfsbrede governance-constructies worden toegepast in de hoofd beheer groep en een afzonderlijke beheer groep met beleids toewijzingen of Azure-roltoewijzingen die geschikter zijn voor een nieuw abonnement, kan worden gedefinieerd.

Als u deze instelling wilt configureren, worden de [hiërarchie-instellingen](/rest/api/resources/hierarchysettings) rest API eind punt genoemd. Gebruik hiervoor de volgende REST API URI en de indeling van de hoofd tekst. Vervang door `{rootMgID}` de id van uw hoofd beheer groep en `{defaultGroupID}` met de id van de beheer groep om de standaard beheer groep te worden:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Aanvraagtekst

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Gebruik hetzelfde eind punt en stel **defaultManagementGroup** in op een waarde van om de standaard beheer groep weer in te stellen op de hoofd beheer groep `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Instelling: autorisatie vereisen

Elke gebruiker kan standaard nieuwe beheer groepen maken binnen een Tenant. Beheerders van een Tenant kunnen deze machtigingen alleen aanbieden aan specifieke gebruikers om consistentie en overeenstemming in de beheer groeps hiërarchie te behouden. Als deze functie is ingeschakeld, moet de gebruiker de `Microsoft.Management/managementGroups/write` bewerking in de hoofd beheer groep uitvoeren om nieuwe onderliggende beheer groepen te maken.

Als u deze instelling wilt configureren, worden de [hiërarchie-instellingen](/rest/api/resources/hierarchysettings) rest API eind punt genoemd. Gebruik hiervoor de volgende REST API URI en de indeling van de hoofd tekst. Deze waarde is een _Boolean_, dus geef **waar** of **Onwaar** op voor de waarde. Met de waarde **True** wordt deze methode voor het beveiligen van uw beheer groeps hiërarchie ingeschakeld:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Aanvraagtekst

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Als u de instelling wilt uitschakelen, gebruikt u hetzelfde eind punt en stelt u **requireAuthorizationForGroupCreation** in op de waarde **False**.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over beheergroepen gaat u naar:

- [Beheergroepen maken om Azure-resources te ordenen](../create.md)
- [Uw beheergroepen wijzigen, verwijderen of beheren](../manage.md)
