---
title: Implementatie gegevens versleutelen
description: Meer informatie over het versleutelen van gegevens die zijn opgeslagen voor de resources van de container instantie en hoe u de gegevens versleutelt met een door de klant beheerde sleutel
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 146effd7f1a7ad1ddd94886d1a79e2914bd1c94b
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904209"
---
# <a name="encrypt-deployment-data"></a>Implementatie gegevens versleutelen

Bij het uitvoeren van Azure Container Instances (ACI)-resources in de cloud worden met de ACI-service gegevens verzameld en persistent gemaakt met betrekking tot uw containers. Met ACI worden deze gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Deze versleuteling beveiligt uw gegevens om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. ACI biedt u ook de mogelijkheid om deze gegevens te versleutelen met uw eigen sleutel, zodat u meer controle hebt over de gegevens met betrekking tot uw ACI-implementaties.

## <a name="about-aci-data-encryption"></a>Over ACI-gegevens versleuteling 

Gegevens in ACI worden versleuteld en ontsleuteld met 256-bits AES-versleuteling. Deze functie is ingeschakeld voor alle ACI-implementaties en u hoeft uw implementatie of containers niet te wijzigen om te profiteren van deze versleuteling. Dit omvat meta gegevens over de implementatie, omgevings variabelen, sleutels die worden door gegeven aan uw containers en logboeken die persistent zijn gemaakt nadat de containers zijn gestopt, zodat u ze nog steeds kunt zien. Versleuteling heeft geen invloed op de prestaties van de container groep en er zijn geen extra kosten voor versleuteling.

## <a name="encryption-key-management"></a>Versleutelings sleutel beheer

U kunt gebruikmaken van door micro soft beheerde sleutels voor het versleutelen van de container gegevens of u kunt de versleuteling beheren met uw eigen sleutels. De volgende tabel vergelijkt deze opties: 

|    |    Door micro soft beheerde sleutels     |     Door de klant beheerde sleutels     |
|----|----|----|
|    Bewerkingen voor versleuteling/ontsleuteling    |    Azure    |    Azure    |
|    Sleutel opslag    |    Micro soft-sleutel archief    |    Azure Key Vault    |
|    Verantwoordelijkheid voor sleutel rotatie    |    Microsoft    |    Klant    |
|    Sleutel toegang    |    Alleen micro soft    |    Micro soft, klant    |

In de rest van het document worden de stappen beschreven die nodig zijn voor het versleutelen van uw ACI-implementatie gegevens met uw sleutel (door de klant beheerde sleutel). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Gegevens versleutelen met een door de klant beheerde sleutel

### <a name="create-service-principal-for-aci"></a>Een service-principal maken voor ACI

De eerste stap is om ervoor te zorgen dat uw [Azure-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) beschikt over een service-principal die is toegewezen voor het verlenen van machtigingen aan de Azure container instances-service. 

Met de volgende CLI-opdracht wordt de ACI-SP in uw Azure-omgeving ingesteld:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Bij de uitvoer van deze opdracht moet u een Service-Principal weer geven die is ingesteld met ' displayName ': ' Azure container instance-service '.

### <a name="create-a-key-vault-resource"></a>Een Key Vault resource maken

Maak een Azure Key Vault met behulp van [Azure Portal](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault), [cli](https://docs.microsoft.com/azure/key-vault/quick-create-cli)of [Power shell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell). 

Gebruik de volgende richt lijnen voor de eigenschappen van uw sleutel kluis: 
* Naam: een unieke naam is vereist. 
* Abonnement: Kies een abonnement.
* Onder resource groep kiest u een bestaande resource groep of maakt u een nieuwe en voert u de naam van een resource groep in.
* Kies een locatie in de vervolg keuzelijst locatie.
* U kunt de standaard waarden van de andere opties wijzigen of kiezen op basis van aanvullende vereisten.

> [!IMPORTANT]
> Wanneer door de klant beheerde sleutels worden gebruikt voor het versleutelen van een ACI-implementatie sjabloon, wordt aangeraden de volgende twee eigenschappen in te stellen op de sleutel kluis, zacht verwijderen en niet leeg te maken. Deze eigenschappen zijn niet standaard ingeschakeld, maar kunnen worden ingeschakeld met Power shell of Azure CLI in een nieuwe of bestaande sleutel kluis.

### <a name="generate-a-new-key"></a>Een nieuwe sleutel genereren 

Als uw sleutel kluis is gemaakt, gaat u naar de resource in Azure Portal. Klik in het navigatie menu aan de linkerkant van de Blade resource onder instellingen op **sleutels**. Klik in de weer gave voor ' sleutels ' op genereren/importeren ' om een nieuwe sleutel te genereren. Gebruik een unieke naam voor deze sleutel en andere voor keuren op basis van uw vereisten. 

![Een nieuwe sleutel genereren](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Toegangs beleid instellen

Maak een nieuw toegangs beleid om de ACI-service toegang tot uw sleutel te geven.

* Als uw sleutel is gegenereerd, klikt u op de Blade sleutel kluis resource onder instellingen op **toegangs beleid**.
* Klik op **toegangs beleid toevoegen**op de pagina toegangs beleid voor uw sleutel kluis.
* Stel de *sleutel machtigingen* in om de sleutel **Get** en **unwrap** op te geven ![sleutel machtigingen instellen](./media/container-instances-encrypt-data/set-key-permissions.png)
* Selecteer voor *Select Principal*de **service Azure container instance**
* Klik onderaan op **toevoegen** 

Het toegangs beleid wordt nu weer gegeven in het toegangs beleid van uw sleutel kluis.

![Nieuw toegangs beleid](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>De JSON-implementatie sjabloon wijzigen

> [!IMPORTANT]
> Het versleutelen van implementatie gegevens met een door de klant beheerde sleutel is beschikbaar in de nieuwste API-versie (2019-12-01) die momenteel wordt geïmplementeerd. Geef deze API-versie op in uw implementatie sjabloon. Neem contact op met de ondersteuning van Azure als u problemen ondervindt.

Wanneer de sleutel kluis sleutel en het toegangs beleid zijn ingesteld, voegt u de volgende eigenschap toe aan uw ACI-implementatie sjabloon. Meer informatie over het implementeren van ACI-resources met een sjabloon vindt u in de [zelf studie: een groep met meerdere containers implementeren met behulp van een resource manager-sjabloon](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Voeg, onder de sectie eigenschappen van container groep van de implementatie sjabloon, een ' encryptionProperties ' toe, die de volgende waarden bevat:
* vaultBaseUrl: de DNS-naam van uw sleutel kluis kunt u vinden op de Blade overzicht van de sleutel kluis bron in de portal
* sleutel naam: de naam van de code die u eerder hebt gegenereerd
* Versie: de huidige versie van de sleutel. U kunt dit vinden door te klikken op de sleutel zelf (onder sleutels in de sectie instellingen van uw sleutel kluis resource).


```json
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "containers": {
                [...]
            }
        }
    }
]
```

### <a name="deploy-your-resources"></a>Uw resources implementeren

Als u het sjabloon bestand op uw bureau blad hebt gemaakt en bewerkt, kunt u het uploaden naar uw Cloud Shell Directory door het bestand naar de map te slepen. 

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de opdracht [AZ Group Deployment Create][az-group-deployment-create] .

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

U ontvangt binnen enkele seconden een eerste reactie van Azure. Zodra de implementatie is voltooid, worden alle gegevens die betrekking hebben op deze door de ACI-service bewaard, versleuteld met de sleutel die u hebt ingevoerd.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create