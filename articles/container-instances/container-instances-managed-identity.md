---
title: Beheerde identiteit inschakelen in containergroep
description: Meer informatie over het inschakelen van een beheerde identiteit in Azure Container Instances die kan worden geverifieerd met andere Azure-services
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 003055d5021dd8ad7c3bab6d2900298ffd13b222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76901928"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Beheerde identiteiten gebruiken met Azure Container Instances

Gebruik [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) om code uit te voeren in Azure Container Instances die met andere Azure-services communiceren - zonder geheimen of referenties in code te bewaren. De functie biedt een Azure Container Instances-implementatie met een automatisch beheerde identiteit in Azure Active Directory.

In dit artikel vindt u meer informatie over beheerde identiteiten in Azure Container Instances en:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit in een containergroep inschakelen
> * De identiteit toegang verlenen tot een Azure-sleutelkluis
> * De beheerde identiteit gebruiken om toegang te krijgen tot een sleutelkluis vanuit een lopende container

Pas de voorbeelden aan om identiteiten in Azure Container Instances in te schakelen en te gebruiken om toegang te krijgen tot andere Azure-services. Deze voorbeelden zijn interactief. In de praktijk worden uw containerafbeeldingen echter code uitgevoerd om toegang te krijgen tot Azure-services.

> [!NOTE]
> Momenteel u een beheerde identiteit niet gebruiken in een containergroep die is geïmplementeerd in een virtueel netwerk.

## <a name="why-use-a-managed-identity"></a>Waarom een beheerde identiteit gebruiken?

Gebruik een beheerde identiteit in een lopende container om te verifiëren voor een [service die Azure AD-verificatie ondersteunt](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) zonder referenties in uw containercode te beheren. Voor services die geen AD-verificatie ondersteunen, u geheimen opslaan in een Azure-sleutelkluis en de beheerde identiteit gebruiken om toegang te krijgen tot de sleutelkluis om referenties op te halen. Zie [Wat zijn beheerde identiteiten voor Azure-bronnen voor](../active-directory/managed-identities-azure-resources/overview.md) meer informatie over het gebruik van een beheerde identiteit?

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt. Momenteel worden beheerde identiteiten op Azure Container Instances alleen ondersteund met Linux-containers en nog niet met Windows-containers.
>  

### <a name="enable-a-managed-identity"></a>Een beheerde identiteit inschakelen

 In Azure Container Instances worden beheerde identiteiten voor Azure-resources ondersteund vanaf REST API-versie 2018-10-01 en bijbehorende SDK's en hulpprogramma's. Wanneer u een containergroep maakt, schakelt u een of meer beheerde identiteiten in door een eigenschap [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) in te stellen. U beheerde identiteiten ook in- of bijwerken nadat een containergroep wordt uitgevoerd, omdat de containergroep opnieuw wordt opgestart. Als u de identiteiten wilt instellen op een nieuwe of bestaande containergroep, gebruikt u de Azure CLI, een resourcemanagersjabloon of een YAML-bestand. 

Azure Container Instances ondersteunt beide typen beheerde Azure-identiteiten: door de gebruiker toegewezen en systeemtoegewezen. In een containergroep u een door het systeem toegewezen identiteit, een of meer door de gebruiker toegewezen identiteiten of beide typen identiteiten inschakelen. 

* Een door de gebruiker toegewezen beheerde identiteit wordt gemaakt als een zelfstandige Azure-bron in de Azure **AD-tenant** die wordt vertrouwd door het abonnement dat in gebruik is. Nadat de identiteit is gemaakt, kan de identiteit worden toegewezen aan een of meer Azure-bronnen (in Azure Container Instances of andere Azure-services). De levenscyclus van een door de gebruiker toegewezen identiteit wordt afzonderlijk beheerd vanaf de levenscyclus van de containergroepen of andere serviceresources waaraan de identiteit is toegewezen. Dit gedrag is vooral handig in Azure Container Instances. Omdat de identiteit verder gaat dan de levensduur van een containergroep, u deze opnieuw gebruiken, samen met andere standaardinstellingen om de implementaties van uw containergroep zeer herhaalbaar te maken.

* Een **door het systeem toegewezen beheerde** identiteit is rechtstreeks ingeschakeld op een containergroep in Azure Container Instances. Wanneer dit is ingeschakeld, maakt Azure een identiteit voor de groep in de Azure AD-tenant die wordt vertrouwd door het abonnement van de instantie. Nadat de identiteit is gemaakt, worden de referenties in elke container in de containergroep ingericht. De levenscyclus van een door het systeem toegewezen identiteit is rechtstreeks gekoppeld aan de containergroep waarop deze is ingeschakeld. Wanneer de groep wordt verwijderd, ruimt Azure automatisch de referenties en de identiteit op in Azure AD.

### <a name="use-a-managed-identity"></a>Een beheerde identiteit gebruiken

Als u een beheerde identiteit wilt gebruiken, moet de identiteit in eerste instantie toegang krijgen tot een of meer Azure-servicebronnen (zoals een web-app, een sleutelkluis of een opslagaccount) in het abonnement. Als u de Azure-bronnen vanuit een lopende container wilt openen, moet uw code een *toegangstoken* verkrijgen van een Azure AD-eindpunt. Vervolgens verzendt uw code het toegangstoken op een gesprek naar een service die Azure AD-verificatie ondersteunt. 

Het gebruik van een beheerde identiteit in een lopende container is in wezen hetzelfde als het gebruik van een identiteit in een Azure VM. Zie de VM-richtlijnen voor het gebruik van een [token,](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) [Azure PowerShell of Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)of de Azure [SDKs](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit artikel de Azure CLI-versie 2.0.49 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Een Azure-sleutelkluis maken

De voorbeelden in dit artikel gebruiken een beheerde identiteit in Azure Container Instances om toegang te krijgen tot een Azure-sleutelkluisgeheim. 

Maak eerst een resourcegroep met de naam *myResourceGroup* op de locatie *eastus* met behulp van de volgende [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create)-opdracht:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Gebruik de opdracht [AZ Keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) om een sleutelkluis te maken. Zorg ervoor dat u een unieke naam van de sleutelkluis opgeeft. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Bewaar een voorbeeldgeheim in de sleutelkluis met de opdracht [az keyvault secret set:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Ga verder met de volgende voorbeelden om toegang te krijgen tot de sleutelkluis met behulp van een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit in Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Voorbeeld 1: Een door de gebruiker toegewezen identiteit gebruiken om toegang te krijgen tot Azure-sleutelkluis

### <a name="create-an-identity"></a>Een identiteit maken

Maak eerst een identiteit in uw abonnement met behulp van de opdracht [AZ-identiteit maken.](/cli/azure/identity?view=azure-cli-latest#az-identity-create) U dezelfde resourcegroep gebruiken die wordt gebruikt om de sleutelkluis te maken of een andere groep te gebruiken.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Als u de identiteit in de volgende stappen wilt gebruiken, gebruikt u de opdracht [AZ-identiteitsshow](/cli/azure/identity?view=azure-cli-latest#az-identity-show) om de serviceprincipal-id en resource-id van de identiteit op te slaan in variabelen.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Een door de gebruiker toegewezen identiteit inschakelen voor een containergroep

Voer de volgende opdracht [voor het maken van AZ-containers uit](/cli/azure/container?view=azure-cli-latest#az-container-create) om een containerexemplaar te maken op basis van de afbeelding van `azure-cli` Microsoft. In dit voorbeeld wordt een groep met één container die u interactief gebruiken om de Azure CLI uit te voeren om toegang te krijgen tot andere Azure-services. In deze sectie wordt alleen het basis Ubuntu-besturingssysteem gebruikt. 

De `--assign-identity` parameter geeft uw door de gebruiker toegewezen beheerde identiteit door aan de groep. De langlopende opdracht houdt de container draaiende. In dit voorbeeld wordt dezelfde resourcegroep gebruikt die wordt gebruikt om de sleutelkluis te maken, maar u een andere groep opgeven.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Binnen enkele seconden krijgt u een reactie van de Azure-CLI die aangeeft dat de implementatie is voltooid. Controleer de status met de opdracht [AZ Container Show.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

De `identity` sectie in de uitvoer ziet er vergelijkbaar uit als het volgende, waarbij de identiteit wordt weergegeven in de containergroep. Het `principalID` `userAssignedIdentities` onder is het serviceprincipal van de identiteit die u hebt gemaakt in Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Gebruikerstoegewezen identiteitstoegang verlenen tot de sleutelkluis

Voer de volgende opdracht [voor het sleutelkluisbeleid van AZ](/cli/azure/keyvault?view=azure-cli-latest) uit om een toegangsbeleid in te stellen voor de sleutelkluis. In het volgende voorbeeld kan de door de gebruiker toegewezen identiteit geheimen uit de sleutelkluis krijgen:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Gebruik door de gebruiker toegewezen identiteit om geheim te halen uit sleutelkluis

Nu u de beheerde identiteit in de lopende containerinstantie gebruiken om toegang te krijgen tot de sleutelkluis. Start eerst een bash shell in de container:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Voer de volgende opdrachten uit in de bashshell in de container. Voer de volgende opdracht uit om een toegangstoken te gebruiken dat Azure Active Directory gebruikt om te verifiëren voor sleutelkluis:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Uitvoer:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Voer de volgende opdracht uit om het toegangstoken in een variabele op te slaan dat u in volgende opdrachten wilt gebruiken om te verifiëren:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Gebruik nu het toegangstoken om te verifiëren voor sleutelkluis en lees een geheim. Zorg ervoor dat u de naam van*https://mykeyvault.vault.azure.net/..* uw sleutelkluis vervangt in de URL ( . ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Het antwoord lijkt op het volgende, met het geheim. In uw code, zou je ontindirect deze output om het geheim te verkrijgen. Gebruik het geheim vervolgens in een volgende bewerking om toegang te krijgen tot een andere Azure-bron.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Voorbeeld 2: Een door het systeem toegewezen identiteit gebruiken om toegang te krijgen tot Azure-sleutelkluis

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Een systeemtoegewezen identiteit inschakelen voor een containergroep

Voer de volgende opdracht [voor het maken van AZ-containers uit](/cli/azure/container?view=azure-cli-latest#az-container-create) om een containerexemplaar te maken op basis van de afbeelding van `azure-cli` Microsoft. In dit voorbeeld wordt een groep met één container die u interactief gebruiken om de Azure CLI uit te voeren om toegang te krijgen tot andere Azure-services. 

De `--assign-identity` parameter zonder extra waarde maakt een door het systeem toegewezen beheerde identiteit op de groep mogelijk. De identiteit is beperkt tot de resourcegroep van de containergroep. De langlopende opdracht houdt de container draaiende. In dit voorbeeld wordt dezelfde resourcegroep gebruikt die wordt gebruikt om de sleutelkluis te maken, maar u een andere groep opgeven.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Binnen enkele seconden krijgt u een reactie van de Azure-CLI die aangeeft dat de implementatie is voltooid. Controleer de status met de opdracht [AZ Container Show.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

De `identity` sectie in de uitvoer lijkt op het volgende, waaruit blijkt dat een door het systeem toegewezen identiteit is gemaakt in Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Stel een variabele in `principalId` op de waarde van (de serviceprincipal-id) van de identiteit, die u in latere stappen gebruiken.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Containergroep toegang verlenen tot de sleutelkluis

Voer de volgende opdracht [voor het sleutelkluisbeleid van AZ](/cli/azure/keyvault?view=azure-cli-latest) uit om een toegangsbeleid in te stellen voor de sleutelkluis. In het volgende voorbeeld kan de door het systeem beheerde identiteit geheimen uit de sleutelkluis krijgen:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>De identiteit van de containergroep gebruiken om geheim te halen uit sleutelkluis

Nu u de beheerde identiteit gebruiken om toegang te krijgen tot de sleutelkluis binnen de lopende containerinstantie. Start eerst een bash shell in de container:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Voer de volgende opdrachten uit in de bashshell in de container. Log eerst in bij de Azure CLI met de beheerde identiteit:

```bash
az login --identity
```

Haal uit de lopende container het geheim uit de sleutelkluis:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

De waarde van het geheim wordt opgehaald:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Beheerde identiteit inschakelen met de sjabloon Resourcebeheer

Als u een beheerde identiteit in een containergroep `identity` wilt `Microsoft.ContainerInstance/containerGroups` inschakelen met `ContainerGroupIdentity` behulp van een [resourcemanagersjabloon,](container-instances-multi-container-group.md)stelt u de eigenschap van het object in met een object. In de volgende `identity` fragmenten wordt de eigenschap weergegeven die is geconfigureerd voor verschillende scenario's. Zie de verwijzing naar de [sjabloon ResourceManager](/azure/templates/microsoft.containerinstance/containergroups). Geef een `apiVersion` `2018-10-01`minimum van .

### <a name="user-assigned-identity"></a>Door de gebruiker toegewezen identiteit

Een door de gebruiker toegewezen identiteit is een bron-id van het formulier:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

U een of meer door gebruikers toegewezen identiteiten inschakelen.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Systeemtoegewezen identiteit

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Door het systeem en de gebruiker toegewezen identiteiten

In een containergroep u zowel een door het systeem toegewezen identiteit als een of meer door de gebruiker toegewezen identiteiten inschakelen.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Beheerde identiteit inschakelen met YAML-bestand

Als u een beheerde identiteit wilt inschakelen in een containergroep die is geïmplementeerd met een [YAML-bestand,](container-instances-multi-container-yaml.md)bevat u de volgende YAML.
Geef een `apiVersion` `2018-10-01`minimum van .

### <a name="user-assigned-identity"></a>Door de gebruiker toegewezen identiteit

Een door de gebruiker toegewezen identiteit is een bron-id van het formulier 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

U een of meer door gebruikers toegewezen identiteiten inschakelen.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Systeemtoegewezen identiteit

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Door het systeem en de gebruiker toegewezen identiteiten

In een containergroep u zowel een door het systeem toegewezen identiteit als een of meer door de gebruiker toegewezen identiteiten inschakelen.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over beheerde identiteiten in Azure Container Instances en hoe u:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit in een containergroep inschakelen
> * De identiteit toegang verlenen tot een Azure-sleutelkluis
> * De beheerde identiteit gebruiken om toegang te krijgen tot een sleutelkluis vanuit een lopende container

* Meer informatie over [beheerde identiteiten voor Azure-bronnen](/azure/active-directory/managed-identities-azure-resources/).

* Bekijk een [Azure Go SDK-voorbeeld](https://medium.com/@samkreter/c98911206328) van het gebruik van een beheerde identiteit om toegang te krijgen tot een sleutelkluis vanuit Azure Container Instances.
