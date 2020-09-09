---
title: 'Quickstart: Een beheergroep maken met REST API'
description: In deze quickstart gebruikt u de REST API om een beheergroep te maken om uw resources in een resource-hiërarchie in te delen.
ms.date: 08/31/2020
ms.topic: quickstart
ms.openlocfilehash: b19fddf8215a1b133254c2a31bbea568a315f721
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237000"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>Quickstart: Een beheergroep maken met REST API

Managementgroepen zijn containers die u helpen bij het beheren van toegang, beleid en naleving voor meerdere abonnementen. Maak deze containers om een doeltreffende en efficiënte hiërarchie te maken die kan worden gebruikt met [Azure Policy](../policy/overview.md) en [op rollen gebaseerd toegangsbeheer van Azure](../../role-based-access-control/overview.md). Zie [Uw resources indelen met Azure-beheergroepen](overview.md) voor meer informatie over beheergroepen.

Het kan tot vijftien minuten duren voordat de eerste beheergroep die in de map is gemaakt, is voltooid. Er zijn processen die de eerste keer worden uitgevoerd om de service voor beheergroepen in Azure in te stellen voor uw map. U ontvangt een melding wanneer het proces is voltooid. Zie [Eerste configuratie van beheergroepen](./overview.md#initial-setup-of-management-groups) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Installeer [ARMClient](https://github.com/projectkudu/ARMClient) als u dit nog niet hebt gedaan. Dit is een hulpprogramma waarmee HTTP-aanvragen worden verzonden naar Azure Resource Manager-REST API’s. U kunt ook de functie ‘Probeer het’ in REST-documentatie gebruiken of hulpprogramma’s zoals [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) of [Postman](https://www.postman.com) in PowerShell.

- Elke Azure AD-gebruiker in de tenant kan een beheergroep maken zonder dat de schrijfmachtiging voor beheergroepen is toegewezen als [Hiërarchie beschermen](./how-to/protect-resource-hierarchy.md#setting---require-authorization) niet is ingeschakeld. Deze nieuwe beheergroep wordt een onderliggend element van de hoofdbeheergroep of de [standaard beheergroep](./how-to/protect-resource-hierarchy.md#setting---default-management-group) en de maker krijgt de roltoewijzing "Eigenaar". Met de beheergroep-service kan deze functie worden toegewezen, zodat roltoewijzingen niet nodig zijn op hoofdmapniveau. Gebruikers hebben geen toegang tot de hoofdbeheergroep wanneer deze wordt gemaakt. Om te voorkomen dat de drempel van het vinden van de Azure AD Global Admins om beheergroepen te kunnen gebruiken te hoog is, wordt het maken van de eerste beheergroepen op hoofdmapniveau toegestaan.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>Maken in REST API

Gebruik voor REST API het eindpunt [Beheergroepen - eindpunt maken of bijwerken](/rest/api/resources/managementgroups/createorupdate) om een nieuwe beheergroep te maken. In dit voorbeeld is de **groupId** van de beheergroep _Contoso_.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Geen aanvraagbody

De **groupId** is een unieke ID die wordt gemaakt. Deze ID wordt gebruikt door andere opdrachten om te verwijzen naar deze groep en kan later niet worden gewijzigd.

Als u wilt dat de beheergroep een andere naam weergeeft in de Azure-portal, voegt u de eigenschap **properties.displayName** toe in de hoofdtekst van de aanvraag. Gebruik bijvoorbeeld het volgende eindpunt en de hoofdtekst van de aanvraag om een beheergroep te maken met de **groupId** _Contoso_ en de weergavenaam _Contoso Group_:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Aanvraagtekst

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

In de bovenstaande voorbeelden wordt de nieuwe beheergroep gemaakt in de hoofdbeheergroep. Als u een andere beheergroep als de bovenliggende wilt opgeven, gebruikt u de eigenschap **properties.parent.id** .

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Aanvraagtekst

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de eerder gemaakte beheergroep wilt verwijderen, gebruikt u het eindpunt [Beheergroepen - verwijderen](/rest/api/resources/managementgroups/delete):

- REST API-URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Geen aanvraagbody

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een beheergroep gemaakt om uw resource-hiërarchie te organiseren. De beheergroep kan abonnementen of andere beheergroepen bevatten.

Ga verder met het volgende voor meer informatie over beheergroepen en het beheren van uw resource-hiërarchie:

> [!div class="nextstepaction"]
> [Uw resources beheren met beheergroepen](./manage.md)