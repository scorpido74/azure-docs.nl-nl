---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013093"
---
De methode DefaultAzureCredential in de toepassing is afhankelijk van drie omgevingsvariabelen: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` en `AZURE_TENANT_ID`. Stel deze variabelen in op de clientId-, clientSecret- en tenantId-waarden die zijn geretourneerd in de stap Een service-principal maken, met de indeling `export VARNAME=VALUE`. (Met deze methode worden alleen de variabelen ingesteld voor uw huidige shell en processen gemaakt vanuit de shell. Als u deze variabelen permanent wilt toevoegen aan uw omgeving, moet u het bestand `/etc/environment ` bewerken.) 

U moet de naam van de sleutelkluis ook opslaan als een omgevingsvariabele met de naam `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
