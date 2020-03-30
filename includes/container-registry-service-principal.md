---
title: bestand opnemen
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9e4f2e355240ba8682cbe9f86f2be94e7dd0d92d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70032349"
---
## <a name="create-a-service-principal"></a>Een service-principal maken

Als u een serviceprincipal wilt maken met toegang tot uw containerregister, voert u het volgende script uit in de [Azure Cloud Shell](../articles/cloud-shell/overview.md) of een lokale installatie van de Azure [CLI.](/cli/azure/install-azure-cli) Het script is opgemaakt voor de Bash-shell.

Werk de `ACR_NAME` variabele bij met de naam van uw containerregister voordat u het script uitvoert. De `SERVICE_PRINCIPAL_NAME` waarde moet uniek zijn binnen uw Azure Active Directory-tenant. Als u een`'http://acr-service-principal' already exists.`fout van " " ontvangt, geeft u een andere naam op voor de serviceprincipal.

U de `--role` waarde in de opdracht [AZ Ad SP create-for-rbac][az-ad-sp-create-for-rbac] optioneel wijzigen als u verschillende machtigingen wilt verlenen. Zie [ACR-rollen en machtigingen](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md)voor een volledige lijst met rollen.

Nadat u het script hebt uitgevoerd, neemt u nota van de **id** en **het wachtwoord**van de serviceprincipal. Zodra u de referenties hebt, u uw toepassingen en services configureren om te verifiëren aan uw containerregister als de serviceprincipal.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Een bestaande serviceprincipal gebruiken

Als u registertoegang wilt verlenen aan een bestaande serviceprincipal, moet u een nieuwe rol toewijzen aan de serviceprincipal. Net als bij het maken van een nieuwe service principal, u pull, push and pull, en eigenaar toegang verlenen, onder anderen.

In het volgende script wordt de opdracht [voor het maken van az-rollen gebruikt][az-role-assignment-create] om *pull-machtigingen* toe te kennen aan een serviceprincipal die u in de `SERVICE_PRINCIPAL_ID` variabele opgeeft. Pas `--role` de waarde aan als u een ander toegangsniveau wilt toekennen.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
