---
title: Definitie van door Azure beheerde toepassing maken | Microsoft Docs
description: In dit artikel leest u hoe u een beheerde Azure-toepassing maakt die is bedoeld voor leden van uw organisatie.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: b8c5a99a74446fcd126606b34135bba315ca1473
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995414"
---
# <a name="publish-an-azure-managed-application-definition"></a>De definitie van een door Azure beheerde toepassing publiceren

In deze snelstart vindt u een inleiding tot het werken met beheerde toepassingen. U voegt de definitie van een beheerde toepassing toe aan een interne catalogus voor gebruikers in uw organisatie. Om de inleiding te vereenvoudigen, hebben we de bestanden voor uw beheerde toepassing al gemaakt. Deze bestanden zijn beschikbaar via GitHub. U krijgt informatie over het bouwen van die bestanden in de zelfstudie [Servicecatalogustoepassing maken](publish-service-catalog-app.md).

Wanneer u klaar bent, hebt u een resourcegroep met de naam **appDefinitionGroup** die de definitie van de beheerde toepassing bevat.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Een resourcegroep voor de definitie maken

De definitie van uw beheerde toepassing bevindt zich in een resourcegroep. De resourcegroep is een logische verzameling waarin Azure-resources worden geïmplementeerd en beheerd.

Gebruik de volgende opdracht om een resourcegroep te maken:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>De definitie van de beheerde toepassing maken

Bij het definiëren van de beheerde toepassing selecteert u een gebruiker, groep of toepassing die de resources voor de gebruiker beheert. Deze identiteit heeft machtigingen voor de beheerde resourcegroep die overeenkomen met de toegewezen rol. Meestal maakt u een Azure Active Directory-groep om resources te beheren. In dit artikel gebruikt u echter uw eigen identiteit.

Als u de object-id van uw identiteit wilt ophalen, geeft u uw user principal name op in de volgende opdracht:

```azurecli-interactive
userid=$(az ad user show --id example@contoso.org --query objectId --output tsv)
```

Vervolgens hebt u de roldefinitie-id nodig van de ingebouwde RBAC-rol waarmee u toegang wilt verlenen aan de gebruiker. In de volgende opdracht ziet u hoe u de roldefinitie-id ophaalt voor de rol van eigenaar:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Maak nu de definitieresource van de beheerde toepassing. De beheerde toepassing bevat alleen een opslagaccount.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://github.com/Azure/azure-managedapp-samples/raw/master/Managed%20Application%20Sample%20Packages/201-managed-storage-account/managedstorage.zip"
```

Wanneer de opdracht is voltooid, hebt u de definitie van een beheerde toepassing in de resourcegroep. 

Enkele parameters die in het voorgaande voorbeeld worden gebruikt, zijn:

* **resource-group**: De naam van de resource groep waarin de definitie van de beheerde toepassing wordt gemaakt.
* **vergrendelings niveau**: Het type vergren deling dat wordt geplaatst op de beheerde resource groep. Hiermee voorkomt u dat de klant ongewenste bewerkingen uitvoert op deze resourcegroep. ReadOnly is momenteel het enige ondersteunde vergrendelingsniveau. Als ReadOnly is opgegeven, kan de klant alleen de resources die aanwezig zijn in de beheerde resourcegroep lezen. De uitgeversidentiteiten die toegang tot de beheerde resourcegroep krijgen, zijn uitgesloten van de vergrendeling.
* **autorisaties**: Hierin worden de principal-ID en de roldefinitie-ID beschreven die worden gebruikt voor het verlenen van machtigingen aan de beheerde resource groep. Wordt opgegeven in de `<principalId>:<roleDefinitionId>`-indeling. Als meer dan één waarde is vereist, geeft u deze op in de vorm `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. De waarden worden gescheiden door een spatie.
* **package-file-uri**: De locatie van een zip-pakket dat de vereiste bestanden bevat. Het pakket moet de bestanden **mainTemplate.json** en **createUiDefinition.json** bevatten. **mainTemplate.json** definieert de Azure-resources die zijn gemaakt als onderdeel van de beheerde toepassing. De sjabloon is niet anders dan een reguliere Resource Manager-sjabloon. **createUiDefinition.json** genereert de gebruikersinterface voor gebruikers die de beheerde toepassing via de portal maken.

## <a name="next-steps"></a>Volgende stappen

U hebt de definitie van de beheerde toepassing gepubliceerd. Nu krijgt u informatie over het implementeren van een exemplaar van deze definitie.

> [!div class="nextstepaction"]
> [Snelstart: Service catalogus-app implementeren](deploy-service-catalog-quickstart.md)