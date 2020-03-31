---
title: Implementatiegegevens versleutelen
description: Meer informatie over versleuteling van gegevens die zijn gebruikt voor uw containerinstantiebronnen en hoe u de gegevens versleutelen met een door de klant beheerde sleutel
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: ad232c5d9df9f6bfae3a79dbd72e2c68143be949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080357"
---
# <a name="encrypt-deployment-data"></a>Implementatiegegevens versleutelen

Wanneer azure containerinstances (ACI)-resources in de cloud worden uitgevoerd, verzamelt en blijft de ACI-service gegevens met betrekking tot uw containers verzamelen en onderhouden. ACI versleutelt deze gegevens automatisch wanneer deze in de cloud worden weergegeven. Deze versleuteling beschermt uw gegevens om te voldoen aan de beveiligings- en nalevingsverplichtingen van uw organisatie. ACI geeft u ook de mogelijkheid om deze gegevens te versleutelen met uw eigen sleutel, zodat u meer controle hebt over de gegevens met betrekking tot uw ACI-implementaties.

## <a name="about-aci-data-encryption"></a>Over ACI-gegevensversleuteling 

Gegevens in ACI worden versleuteld en gedecodeerd met behulp van 256-bits AES-versleuteling. Het is ingeschakeld voor alle ACI-implementaties en u hoeft uw implementatie of containers niet te wijzigen om gebruik te maken van deze versleuteling. Dit omvat metagegevens over de implementatie, omgevingsvariabelen, sleutels die worden doorgegeven in uw containers en logboeken die zijn blijven bestaan nadat uw containers zijn gestopt, zodat u ze nog steeds zien. Versleuteling heeft geen invloed op de prestaties van uw containergroep en er zijn geen extra kosten voor versleuteling.

## <a name="encryption-key-management"></a>Beheer van versleutelingssleutels

U vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw containergegevens, of u de versleuteling beheren met uw eigen sleutels. In de volgende tabel worden de volgende opties vergeleken: 

|    |    Door Microsoft beheerde sleutels     |     Door klant beheerde sleutels     |
|----|----|----|
|    Versleutelings-/decryptiebewerkingen    |    Azure    |    Azure    |
|    Sleutelopslag    |    Microsoft-sleutelarchief    |    Azure Key Vault    |
|    Belangrijkste rotatieverantwoordelijkheid    |    Microsoft    |    Klant    |
|    Toegang tot sleutels    |    Alleen Microsoft    |    Microsoft, Klant    |

De rest van het document heeft betrekking op de stappen die nodig zijn om uw ACI-implementatiegegevens te versleutelen met uw sleutel (door de klant beheerde sleutel). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Gegevens versleutelen met een door de klant beheerde sleutel

### <a name="create-service-principal-for-aci"></a>Serviceprincipal maken voor ACI

De eerste stap is ervoor te zorgen dat uw [Azure-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) een serviceprincipal heeft toegewezen voor het verlenen van machtigingen voor de Azure Container Instances-service. 

> [!IMPORTANT]
> Als u de volgende opdracht wilt uitvoeren en een serviceprincipal wilt maken, bevestigt u dat u machtigingen hebt om serviceprincipals in uw tenant te maken.
>

Met de volgende opdracht CLI stelt u de ACI SP in uw Azure-omgeving in:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

De uitvoer van het uitvoeren van deze opdracht moet u een serviceprincipal weergeven die is ingesteld met 'displayName': 'Azure Container Instance Service'.

Als u de serviceprincipal niet maken:
* bevestigen dat u machtigingen hebt om dit te doen in uw tenant
* controleer of er al een serviceprincipal in uw tenant aanwezig is voor implementatie naar ACI. U dat `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` doen door die serviceprincipal uit te voeren en te gebruiken

### <a name="create-a-key-vault-resource"></a>Een Key Vault-bron maken

Maak een Azure Key Vault met [Azure portal,](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)of [PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell). 

Gebruik de volgende richtlijnen voor de eigenschappen van uw sleutelkluis: 
* Naam: geef een unieke naam op. 
* Abonnement: kies een abonnement.
* Kies onder Resourcegroep een bestaande resourcegroep of maak nieuwe en voer een naam van de resourcegroep in.
* Kies een locatie in de vervolgkeuzelijst Locatie.
* U de andere opties aan hun standaardinstellingen overlaten of kiezen op basis van aanvullende vereisten.

> [!IMPORTANT]
> Wanneer u door de klant beheerde sleutels gebruikt om een ACI-implementatiesjabloon te versleutelen, wordt aanbevolen de volgende twee eigenschappen in te stellen op de sleutelkluis, Soft Delete en Do Not Purge. Deze eigenschappen zijn standaard niet ingeschakeld, maar kunnen worden ingeschakeld met PowerShell of Azure CLI op een nieuwe of bestaande sleutelkluis.

### <a name="generate-a-new-key"></a>Een nieuwe sleutel genereren 

Zodra uw sleutelkluis is gemaakt, navigeert u naar de bron in azure-portal. Klik in het linkernavigatiemenu van het resourceblad onder Instellingen op **Toetsen**. Klik in de weergave 'Sleutels' op Genereren/importeren om een nieuwe sleutel te genereren. Gebruik een unieke naam voor deze sleutel en eventuele andere voorkeuren op basis van uw vereisten. 

![Een nieuwe sleutel genereren](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Toegangsbeleid instellen

Maak een nieuw toegangsbeleid om de ACI-service toegang te geven tot uw sleutel.

* Zodra uw sleutel is gegenereerd, klikt u onder Instellingen op **Toegangsbeleid**onder Instellingen.
* Klik op de pagina Toegangsbeleid voor uw sleutelkluis op **Toegangsbeleid toevoegen**.
* De *sleutelmachtigingen* instellen om sleutelmachtigingen **voor het aanvragen** en uitpakken van **sleutelsets** ![op te nemen](./media/container-instances-encrypt-data/set-key-permissions.png)
* Selecteer Azure **Container Instance Service** voor Principal *selecteren*
* Klik onderaan **op Toevoegen** 

Het toegangsbeleid moet nu worden weergegeven in het toegangsbeleid van uw sleutelkluis.

![Nieuw toegangsbeleid](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Uw JSON-implementatiesjabloon wijzigen

> [!IMPORTANT]
> Het versleutelen van implementatiegegevens met een door de klant beheerde sleutel is beschikbaar in de nieuwste API-versie (2019-12-01) die momenteel wordt uitgerold. Geef deze API-versie op in uw implementatiesjabloon. Als u hier problemen mee hebt, neem dan contact op met Azure Support.

Zodra de sleutelkluissleutel en het toegangsbeleid zijn ingesteld, voegt u de volgende eigenschappen toe aan uw ACI-implementatiesjabloon. Meer informatie over het implementeren van ACI-resources met een sjabloon in de [zelfstudie: een groep met meerdere containers implementeren met behulp van een resourcemanagersjabloon](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 
* Onder `resources`, `apiVersion` `2019-12-01`ingesteld op .
* Voeg onder de sectie eigenschappen van de `encryptionProperties`containergroep van de implementatiesjabloon een , die de volgende waarden bevat, toe:
  * `vaultBaseUrl`: de DNS-naam van uw sleutelkluis, is te vinden op het overzichtsblad van de sleutelkluisbron in Portal
  * `keyName`: de naam van de eerder gegenereerde sleutel
  * `keyVersion`: de huidige versie van de sleutel. Dit kan worden gevonden door te klikken in de sleutel zelf (onder "Sleutels" in de sectie Instellingen van uw sleutelkluis bron)
* Voeg onder de eigenschappen `sku` van de `Standard`containergroep een eigenschap met waarde toe . De `sku` eigenschap is vereist in API-versie 2019-12-01.

In het volgende sjabloonfragment worden de volgende eigenschappen weergegeven om implementatiegegevens te versleutelen:

```json
[...]
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
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Hieronder volgt een volledige sjabloon, aangepast aan de sjabloon in [Zelfstudie: Een groep met meerdere containers implementeren met behulp van een resourcemanagersjabloon](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
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
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Uw resources implementeren

Als u het sjabloonbestand op uw bureaublad hebt gemaakt en bewerkt, u het uploaden naar uw Cloud Shell-map door het bestand erin te slepen. 

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de [opdracht Implementatie van az-groep.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

U ontvangt binnen enkele seconden een eerste reactie van Azure. Zodra de implementatie is voltooid, worden alle gegevens die ermee verbonden zijn door de ACI-service versleuteld met de door u opgegeven sleutel.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
