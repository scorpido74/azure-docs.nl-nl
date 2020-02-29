---
title: Azure Key Vault beheren met CLI-Azure Key Vault | Microsoft Docs
description: Gebruik dit artikel voor het automatiseren van algemene taken in Key Vault met behulp van de Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 642cc42a9853fe0a93a40ca65652b6dc5fcd8d40
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195274"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Key Vault beheren met de Azure CLI 

In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Key Vault met behulp van de Azure CLI.  U kunt informatie weer geven over:

- Een beveiligde container (een kluis) maken in azure
- Een sleutel, geheim of certificaat toevoegen aan de sleutel kluis
- Een toepassing met Azure Active Directory registreren
- Een toepassing machtigen voor het gebruik van een sleutel of geheim
- Geavanceerd toegangs beleid voor sleutel kluis instellen
- Werken met Hardware Security modules (Hsm's)
- De sleutel kluis en de bijbehorende sleutels en geheimen verwijderen
- Diverse opdracht regel opdrachten voor meerdere platformen van Azure


Azure Sleutelkluis is beschikbaar in de meeste regio's. Zie de pagina [Prijzen van Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie.

> [!NOTE]
> Dit artikel bevat geen instructies voor het schrijven van de Azure-toepassing die deel uitmaakt van een van de stappen, die laat zien hoe u een toepassing kunt autoriseren voor het gebruik van een sleutel of geheim in de sleutel kluis.
>

Zie [Wat is Azure Key Vault?](key-vault-overview.md) voor een overzicht van Azure Key Vault.
Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de Azure CLI-opdrachten in dit artikel wilt gebruiken, hebt u de volgende items nodig:

* Een abonnement op Microsoft Azure Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefabonnement](https://azure.microsoft.com/pricing/free-trial).
* Azure-opdracht regel interface versie 2,0 of hoger. Zie [de Azure cli installeren](/cli/azure/install-azure-cli)voor informatie over het installeren van de meest recente versie.
* Een toepassing die wordt geconfigureerd voor het gebruik van de sleutel of het wacht woord dat u in dit artikel hebt gemaakt. Er is een voorbeeldtoepassing beschikbaar in het [Microsoft Downloadcentrum](https://www.microsoft.com/download/details.aspx?id=45343). Zie het meegeleverde Leesmij-bestand voor instructies.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Hulp krijgen met de platformoverschrijdende opdracht regel interface van Azure

In dit artikel wordt ervan uitgegaan dat u bekend bent met de opdracht regel interface (bash, Terminal, opdracht prompt).

De para meter--Help of-h kan worden gebruikt om de Help voor specifieke opdrachten weer te geven. U kunt ook de Azure Help [opdracht] [opties] Format gebruiken. Raadpleeg de Help als u twijfelt over de para meters die nodig zijn voor een opdracht. De volgende opdrachten geven bijvoorbeeld dezelfde informatie weer:

```azurecli
az account set --help
az account set -h
```

U kunt ook de volgende artikelen lezen om vertrouwd te raken met Azure Resource Manager in de Azure-opdracht regel interface voor meerdere platforms:

* [Azure CLI installeren](/cli/azure/install-azure-cli)
* [Aan de slag met de Azure CLI](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Een beveiligde container (een kluis) maken in azure

Kluizen zijn beveiligde containers die worden ondersteund door Hardware Security modules. Kluizen verminderen de kans op onbedoeld verlies van beveiligingsinformatie door de opslag van toepassingsgeheimen te centraliseren. Sleutelkluizen regelen en registreren ook de toegang tot alles wat erin is opgeslagen. Azure Key Vault kan het aanvragen en vernieuwen van Transport Layer Security-certificaten (TLS) verwerken, met behulp van de functies die vereist zijn voor een robuuste oplossing voor het beheren van de levenscyclus van certificaten. In de volgende stappen maakt u een kluis.

### <a name="connect-to-your-subscriptions"></a>Verbinding maken met uw abonnementen

Als u zich interactief wilt aanmelden, gebruikt u de volgende opdracht:

```azurecli
az login
```
Als u zich wilt aanmelden met een organisatie account, kunt u uw gebruikers naam en wacht woord door geven.

```azurecli
az login -u username@domain.com -p password
```

Als u meer dan één abonnement hebt en u wilt opgeven welke u wilt gebruiken, typt u het volgende om de abonnementen voor uw account weer te geven:

```azurecli
az account list
```

Geef een abonnement op met de para meter abonnement.

```azurecli
az account set --subscription <subscription name or ID>
```

Zie [Azure cli installeren](/cli/azure/install-azure-cli)voor meer informatie over het configureren van de platformoverschrijdende opdracht regel interface van Azure.

### <a name="create-a-new-resource-group"></a>Een nieuwe resourcegroep maken

Wanneer u Azure Resource Manager gebruikt, worden alle gerelateerde resources in een resource groep gemaakt. U kunt een sleutel kluis maken in een bestaande resource groep. Als u een nieuwe resource groep wilt gebruiken, kunt u een nieuwe maken.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

De eerste para meter is de naam van de resource groep en de tweede para meter is de locatie. Een lijst met alle mogelijke locatie typen ophalen:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>De Key Vault resource provider registreren

 U ziet mogelijk de fout "het abonnement is niet geregistreerd voor het gebruik van de naam ruimte micro soft. sleutel kluis" wanneer u een nieuwe sleutel kluis probeert te maken. Als dat bericht wordt weer gegeven, controleert u of Key Vault resource provider is geregistreerd in uw abonnement. Dit is een eenmalige bewerking voor elk abonnement.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

Gebruik de opdracht `az keyvault create` om een sleutel kluis te maken. Dit script heeft drie verplichte para meters: een naam van een resource groep, een sleutel kluis naam en de geografische locatie.

Als u een nieuwe kluis met de naam **ContosoKeyVault**wilt maken, typt u in de resource groep **ContosoResourceGroup**op de locatie van de **Azië-Oost** : 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

De uitvoer van deze opdracht bevat eigenschappen van de sleutel kluis die u hebt gemaakt. De twee belangrijkste eigenschappen zijn:

* **naam**: in het voor beeld is de naam ContosoKeyVault. U gebruikt deze naam voor andere Key Vault-opdrachten.
* **vaultUri**: in het voor beeld is de URI https://contosokeyvault.vault.azure.net. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Uw Azure-account is nu gemachtigd om alle bewerkingen op deze sleutelkluis uit te voeren. Vanaf nog, is niemand anders gemachtigd.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Een sleutel, geheim of certificaat toevoegen aan de sleutel kluis

Als u wilt dat Azure Key Vault een met software beschermde sleutel voor u maakt, gebruikt u de `az key create` opdracht.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Als u een bestaande sleutel in een. pem-bestand hebt, kunt u deze uploaden naar Azure Key Vault. U kunt ervoor kiezen om de sleutel te beveiligen met software of HSM. In dit voor beeld wordt de sleutel uit het. pem-bestand geïmporteerd en beschermd met software, met het wacht woord ' hVFkk965BuUv ':

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

U kunt nu verwijzen naar de sleutel die u hebt gemaakt of geüpload naar Azure Key Vault, met behulp van de bijbehorende URI. Gebruik **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** om altijd de huidige versie op te halen. Gebruik https://[de naam van de sleutel kluis]. kluis. Azure. net/Keys/[computer naam]/[key-unique-id] om deze specifieke versie op te halen. Bijvoorbeeld **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** . 

Voeg een geheim toe aan de kluis, een wacht woord met de naam SQLPassword en met de waarde ' hVFkk965BuUv ' voor Azure-sleutel kluizen. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Verwijs dit wacht woord met behulp van de bijbehorende URI. Gebruik **https://ContosoVault.vault.azure.net/secrets/SQLPassword** om altijd de huidige versie op te halen en https://[de naam van de sleutel kluis-name]. kluis. Azure. net/Secret/[geheime naam]/[geheim-unieke id] om deze specifieke versie op te halen. Bijvoorbeeld **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** .

Importeer met behulp van een. pem-of. pfx een certificaat naar de kluis.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Laten we de sleutel, het geheim of het certificaat dat u hebt gemaakt, bekijken:

* Als u uw sleutels wilt weer geven, typt u: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Als u uw geheimen wilt weer geven, typt u: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Als u certificaten wilt weer geven, typt u: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Een toepassing met Azure Active Directory registreren

Deze stap wordt doorgaans uitgevoerd door een ontwikkelaar op een afzonderlijke computer. Het is niet specifiek voor Azure Key Vault, maar is wel opgenomen in het geval van bewustzijn. Voor het volt ooien van de app-registratie, uw account, de kluis en de toepassing moeten zich in dezelfde Azure-map bestaan.

Toepassingen die gebruikmaken van een sleutelkluis moeten een token van Azure Active Directory gebruiken om zich te verifiëren.  De eigenaar van de toepassing moet deze eerst registreren in Azure Active Directory. Aan het eind van het registratieproces ontvangt de eigenaar de volgende waarden:

- Een **toepassings-id** (ook bekend als de Aad-client-id of appID)
- Een **verificatiesleutel** (ook wel het gedeelde geheim genoemd). 

De toepassing moet beide waarden aan Azure Active Directory verstrekken om een token te verkrijgen. Hoe een toepassing is geconfigureerd om een token op te halen, is afhankelijk van de toepassing. In de [Key Vault-voorbeeldtoepassing](https://www.microsoft.com/download/details.aspx?id=45343) moet de eigenaar van de toepassing deze waarden instellen in het bestand app.config.

Voor gedetailleerde stappen voor het registreren van een toepassing met Azure Active Directory moet u de artikelen met de titel [toepassingen integreren met Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), de [Portal gebruiken om een Azure Active Directory toepassing en Service-Principal te maken die toegang heeft tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md)en [een Azure-Service-Principal te maken met de Azure cli](/cli/azure/create-an-azure-service-principal-azure-cli).

Een toepassing registreren in Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Een toepassing machtigen voor het gebruik van een sleutel of geheim

Gebruik de opdracht `az keyvault set-policy` om de toepassing toegang te geven tot de sleutel of het geheim in de kluis.

Als uw kluis naam bijvoorbeeld ContosoKeyVault is, is de toepassing voorzien van een appID van 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed en wilt u de toepassing autoriseren voor het ontsleutelen en ondertekenen van sleutels in uw kluis, dan gebruikt u de volgende opdracht:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Als u dezelfde toepassing wilt machtigen voor het lezen van geheimen in uw kluis, typt u de volgende opdracht:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a>Geavanceerd toegangs beleid voor sleutel kluis instellen

Gebruik [AZ Key kluis update](/cli/azure/keyvault#az-keyvault-update) om geavanceerde beleids regels in te scha kelen voor de sleutel kluis.

 Key Vault voor implementatie inschakelen: Hiermee kunnen virtuele machines certificaten ophalen die zijn opgeslagen als geheimen van de kluis.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Schakel Key Vault in voor schijf versleuteling: vereist voor het gebruik van de kluis voor Azure Disk Encryption.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Key Vault voor sjabloon implementatie inschakelen: Hiermee kan Resource Manager geheimen uit de kluis ophalen.

```azurecli 
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>Werken met Hardware Security modules (Hsm's)

Voor extra zekerheid kunt u sleutels importeren of genereren op basis van Hsm's (Hardware Security modules) die nooit de HSM-grens verlaten. De HSM's zijn FIPS 140-2 Level 2-gevalideerde modules. Als deze vereiste niet op u van toepassing is, slaat u deze sectie over en gaat u naar de sleutel kluis en de bijbehorende sleutels en geheimen verwijderen.

Als u deze met HSM beveiligde sleutels wilt maken, moet u een kluis abonnement hebben dat ondersteuning biedt voor met HSM beschermde sleutels.

Wanneer u de sleutel kluis maakt, voegt u de para meter ' SKU ' toe:

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

U kunt softwarematige beveiligde sleutels (zoals hiervoor) en met HSM beveiligde sleutels toevoegen aan deze kluis. Als u een met HSM beschermde sleutel wilt maken, stelt u de para meter Destination in op HSM:

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

U kunt de volgende opdracht gebruiken om een sleutel te importeren uit een. pem-bestand op uw computer. Met deze opdracht wordt de sleutel geïmporteerd in HSM's in de Sleutelkluis-service:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Met de volgende opdracht wordt een BYOK-pakket (Bring Your Own Key) geïmporteerd. Hiermee kunt u de sleutel in uw lokale HSM genereren en overdragen naar HSM's in de Sleutelkluis-service, zonder dat de sleutel de HSM-grens verlaat:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Zie [How-Protected Keys gebruiken with Azure Key Vault](key-vault-hsm-protected-keys.md)voor meer gedetailleerde instructies voor het genereren van dit BYOK-pakket.

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>De sleutel kluis en de bijbehorende sleutels en geheimen verwijderen

Als u de sleutel kluis en de bijbehorende sleutels of geheimen niet meer nodig hebt, kunt u de sleutel kluis verwijderen met behulp van de `az keyvault delete` opdracht:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

U kunt ook een volledige Azure-resourcegroep verwijderen. Deze bevat de sleutelkluis en alle andere resources die u hebt opgenomen in de groep:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Diverse opdracht regel opdrachten voor meerdere platformen van Azure

Andere opdrachten die nuttig kunnen zijn voor het beheren van Azure Key Vault.

Met deze opdracht worden alle sleutels en geselecteerde eigenschappen weer gegeven in tabel vorm:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Met deze opdracht wordt een volledige lijst met eigenschappen voor de opgegeven sleutel weer gegeven:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Met deze opdracht worden alle geheime namen en geselecteerde eigenschappen weer gegeven in tabel vorm:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Hier volgt een voor beeld van hoe u een specifieke sleutel kunt verwijderen:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Hier volgt een voor beeld van het verwijderen van een specifiek geheim:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Key Vault cli-referentie](/cli/azure/keyvault)voor de volledige naslag informatie voor Azure CLI voor sleutel kluis-opdrachten.

- Raadpleeg [de Azure Key Vault hand leiding voor ontwikkel aars](key-vault-developers-guide.md) voor meer informatie over het Program meren

- Zie [How-Protected Keys gebruiken with Azure Key Vault](key-vault-hsm-protected-keys.md)(Engelstalig) voor meer informatie over Azure Key Vault en hsm's.
