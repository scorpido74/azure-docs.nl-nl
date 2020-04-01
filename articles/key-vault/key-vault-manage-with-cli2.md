---
title: Azure Key Vault beheren met CLI - Azure Key Vault | Microsoft Documenten
description: Gebruik dit artikel om veelvoorkomende taken in Key Vault te automatiseren met de Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: f69b65ece09a63c7a1d9e23d5cd488d9659724ad
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79457420"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Sleutelkluis beheren met de Azure CLI 

In dit artikel wordt uitgelegd hoe u aan de slag met Azure Key Vault met de Azure CLI.  U informatie zien over:

- Een geharde container (een kluis) maken in Azure
- Een sleutel, geheim of certificaat toevoegen aan de sleutelkluis
- Een toepassing registreren met Azure Active Directory
- Een toepassing machtigen om een sleutel of geheim te gebruiken
- Beleid voor geavanceerde toegangsbeleid voor belangrijke vaults instellen
- Werken met hardwarebeveiligingsmodules (HSM's)
- De sleutelkluis en bijbehorende sleutels en geheimen verwijderen
- Diverse Azure-opdrachtinterfaceopdrachten voor meerdere azure-clients


Azure Sleutelkluis is beschikbaar in de meeste regio's. Zie de pagina [Prijzen van Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie.

> [!NOTE]
> In dit artikel worden geen instructies opgenomen over het schrijven van de Azure-toepassing die een van de stappen bevat, die laat zien hoe u een toepassing autoriseert om een sleutel of geheim in de sleutelkluis te gebruiken.
>

Zie Wat is Azure Key Vault voor een overzicht van Azure Key [Vault?](key-vault-overview.md)
Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de Azure CLI-opdrachten in dit artikel wilt gebruiken, moet u de volgende items hebben:

* Een abonnement op Microsoft Azure Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefabonnement](https://azure.microsoft.com/pricing/free-trial).
* Azure Command-Line Interface versie 2.0 of hoger. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli)als u de nieuwste versie wilt installeren.
* Een toepassing die is geconfigureerd om de sleutel of het wachtwoord te gebruiken dat u in dit artikel maakt. Er is een voorbeeldtoepassing beschikbaar in het [Microsoft Downloadcentrum](https://www.microsoft.com/download/details.aspx?id=45343). Zie het meegeleverde Readme-bestand voor instructies.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Hulp krijgen met Azure Cross-Platform Command-Line Interface

In dit artikel wordt ervan uitgegaan dat u bekend bent met de command-line interface (Bash, Terminal, Command prompt).

De parameter --help of -h kan worden gebruikt om hulp voor specifieke opdrachten weer te geven. De indeling Azure help [command] [options] kan ook worden gebruikt. Wanneer u twijfelt over de parameters die een opdracht nodig heeft, raadpleegt u hulp. De volgende opdrachten geven bijvoorbeeld dezelfde informatie op:

```azurecli
az account set --help
az account set -h
```

U ook de volgende artikelen lezen om vertrouwd te raken met Azure Resource Manager in azure cross-platform command-line interface:

* [Azure CLI installeren](/cli/azure/install-azure-cli)
* [Aan de slag met de Azure CLI](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Een geharde container (een kluis) maken in Azure

Kluizen zijn beveiligde containers ondersteund door hardware beveiligingsmodules. Kluizen verminderen de kans op onbedoeld verlies van beveiligingsinformatie door de opslag van toepassingsgeheimen te centraliseren. Sleutelkluizen regelen en registreren ook de toegang tot alles wat erin is opgeslagen. Azure Key Vault kan het aanvragen en vernieuwen van Transport Layer Security-certificaten (TLS) verwerken, met behulp van de functies die vereist zijn voor een robuuste oplossing voor het beheren van de levenscyclus van certificaten. In de volgende stappen maakt u een kluis.

### <a name="connect-to-your-subscriptions"></a>Verbinding maken met uw abonnementen

Als u zich interactief wilt aanmelden, gebruikt u de volgende opdracht:

```azurecli
az login
```
Als u zich wilt aanmelden met een organisatieaccount, u uw gebruikersnaam en wachtwoord invoeren.

```azurecli
az login -u username@domain.com -p password
```

Als u meer dan één abonnement hebt en moet opgeven welke u wilt gebruiken, typt u het volgende om de abonnementen voor uw account te bekijken:

```azurecli
az account list
```

Geef een abonnement op met de abonnementsparameter.

```azurecli
az account set --subscription <subscription name or ID>
```

Zie [Azure CLI](/cli/azure/install-azure-cli)installeren voor meer informatie over het configureren van Azure Cross-Platform Command-Line Interface.

### <a name="create-a-new-resource-group"></a>Een nieuwe resourcegroep maken

Bij het gebruik van Azure Resource Manager worden alle gerelateerde resources gemaakt in een resourcegroep. U een sleutelkluis maken in een bestaande resourcegroep. Als u een nieuwe resourcegroep wilt gebruiken, u een nieuwe groep maken.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

De eerste parameter is de naam van de resourcegroep en de tweede parameter is de locatie. Ga als lid van het type met alle mogelijke locaties:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>De Key Vault-resourceprovider registreren

 Mogelijk ziet u de fout 'Het abonnement is niet geregistreerd om naamruimte 'Microsoft.KeyVault' te gebruiken wanneer u een nieuwe sleutelkluis probeert te maken. Als dat bericht wordt weergegeven, controleert u of key vault-resourceprovider is geregistreerd in uw abonnement. Dit is een eenmalige bewerking voor elk abonnement.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

Gebruik `az keyvault create` de opdracht om een sleutelkluis te maken. Dit script heeft drie verplichte parameters: een naam van de resourcegroep, een naam van de sleutelkluis en de geografische locatie.

Als u een nieuwe kluis wilt maken met de naam **ContosoKeyVault**, typt u in de resourcegroep **ContosoResourceGroup,** woonachtig in de locatie **Oost-Azië:** 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

De uitvoer van deze opdracht toont eigenschappen van de sleutelkluis die u hebt gemaakt. De twee belangrijkste eigenschappen zijn:

* **naam:** In het voorbeeld is de naam ContosoKeyVault. Je gebruikt deze naam voor andere Key Vault-opdrachten.
* **vaultUri:** In het voorbeeld https://contosokeyvault.vault.azure.netis de URI . Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Uw Azure-account is nu gemachtigd om alle bewerkingen op deze sleutelkluis uit te voeren. Vooralsnog is niemand anders bevoegd.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Een sleutel, geheim of certificaat toevoegen aan de sleutelkluis

Als u wilt dat Azure Key Vault een softwarebeveiligde `az key create` sleutel voor u maakt, gebruikt u de opdracht.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Als u een bestaande sleutel in een .pem-bestand hebt, u deze uploaden naar Azure Key Vault. U ervoor kiezen om de sleutel te beschermen met software of HSM. In dit voorbeeld importeert u de sleutel uit het .pem-bestand en beschermt u deze met software met behulp van het wachtwoord "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

U nu met de URI verwijzen naar de sleutel die u hebt gemaakt of geüpload naar Azure Key Vault. Gebruik **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** om altijd de huidige versie te krijgen. Gebruik https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] om deze specifieke versie te krijgen. Bijvoorbeeld. **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** 

Voeg een geheim toe aan de kluis, een wachtwoord met de naam SQLPassword, en dat heeft de waarde van "hVFkk965BuUv" aan Azure Key Vaults. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Verwijs naar dit wachtwoord met behulp van de URI. Gebruik **https://ContosoVault.vault.azure.net/secrets/SQLPassword** om altijd de huidige versie te krijgen en https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] om deze specifieke versie te krijgen. Bijvoorbeeld. **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**

Importeer een certificaat in de kluis met een .pem of .pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Laten we de sleutel, het geheim of het certificaat bekijken dat u hebt gemaakt:

* Als u uw toetsen wilt weergeven, typt u: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Als u uw geheimen wilt bekijken, typt u het: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Als u certificaten wilt weergeven, typt u: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Een toepassing registreren met Azure Active Directory

Deze stap wordt doorgaans uitgevoerd door een ontwikkelaar op een afzonderlijke computer. Het is niet specifiek voor Azure Key Vault, maar is hier opgenomen, voor bewustzijn. Als u de app-registratie wilt voltooien, moeten uw account, de kluis en de toepassing zich in dezelfde Azure-map bevinden.

Toepassingen die gebruikmaken van een sleutelkluis moeten een token van Azure Active Directory gebruiken om zich te verifiëren.  De eigenaar van de toepassing moet deze eerst registreren in Azure Active Directory. Aan het eind van het registratieproces ontvangt de eigenaar de volgende waarden:

- Een **toepassings-id** (ook wel aad-client-id of appID genoemd)
- Een **verificatiesleutel** (ook wel het gedeelde geheim genoemd). 

De toepassing moet beide waarden aan Azure Active Directory verstrekken om een token te verkrijgen. Hoe een toepassing is geconfigureerd om een token te krijgen, is afhankelijk van de toepassing. In de [Key Vault-voorbeeldtoepassing](https://www.microsoft.com/download/details.aspx?id=45343) moet de eigenaar van de toepassing deze waarden instellen in het bestand app.config.

Voor gedetailleerde stappen over het registreren van een toepassing met Azure Active Directory moet u de artikelen met de titel [Integratie van toepassingen met Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md)controleren, Portal gebruiken om een Azure Active [Directory-toepassing en serviceprincipal te maken die toegang heeft tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md)en een [Azure-serviceprincipal maken met de Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

Ga als een register voor een toepassing in Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Een toepassing machtigen om een sleutel of geheim te gebruiken

Als u de toepassing wilt autoriseren om `az keyvault set-policy` toegang te krijgen tot de sleutel of het geheim in de kluis, gebruikt u de opdracht.

Als uw kluisnaam bijvoorbeeld ContosoKeyVault is, heeft de toepassing een appID van 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed en u wilt de toepassing autoriseren om te decoderen en ondertekenen met sleutels in uw kluis, gebruik maken van de volgende opdracht:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Als u dezelfde toepassing wilt autoriseren om geheimen in uw kluis te lezen, typt u de volgende opdracht:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a>Beleid voor geavanceerde toegangsbeleid voor belangrijke vaults instellen

Gebruik [de AZ KeyVault-update](/cli/azure/keyvault#az-keyvault-update) om geavanceerd beleid voor de sleutelkluis in te schakelen.

 Key Vault inschakelen voor implementatie: hiermee kunnen virtuele machines certificaten ophalen die zijn opgeslagen als geheimen uit de kluis.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Key Vault inschakelen voor schijfversleuteling: vereist bij het gebruik van de kluis voor Azure Disk-versleuteling.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Key Vault inschakelen voor sjabloonimplementatie: Hiermee kan Resource Manager geheimen uit de kluis ophalen.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Werken met hardwarebeveiligingsmodules (HSM's)

Voor extra zekerheid u sleutels importeren of genereren uit hardwarebeveiligingsmodules (HSM's) die nooit de HSM-grens verlaten. De HSM's zijn FIPS 140-2 Level 2-gevalideerde modules. Als deze vereiste niet van toepassing is op u, kunt u deze sectie overslaan om naar De sleutelkluis en de bijbehorende sleutels en geheimen verwijderen te gaan.

Als u deze met HSM beveiligde sleutels wilt maken, moet u beschikken over een kluisabonnement dat ondersteuning biedt voor sleutels die zijn beveiligd met HSM.

Wanneer u de keyvault maakt, voegt u de parameter 'sku' toe:

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

U kunt softwarematige beveiligde sleutels (zoals hiervoor) en met HSM beveiligde sleutels toevoegen aan deze kluis. Als u een hsm-beveiligde sleutel wilt maken, stelt u de parameter Destination in op 'HSM':

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

U de volgende opdracht gebruiken om een sleutel te importeren uit een .pem-bestand op uw computer. Met deze opdracht wordt de sleutel geïmporteerd in HSM's in de Sleutelkluis-service:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

De volgende opdracht importeert een BYOK-pakket (Bring Your Own Key). Hiermee kunt u de sleutel in uw lokale HSM genereren en overdragen naar HSM's in de Sleutelkluis-service, zonder dat de sleutel de HSM-grens verlaat:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Zie [HSM-beveiligde sleutels gebruiken met Azure Key Vault](key-vault-hsm-protected-keys.md)voor meer gedetailleerde instructies over het genereren van dit BYOK-pakket.

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>De sleutelkluis en bijbehorende sleutels en geheimen verwijderen

Als u de sleutelkluis en de sleutels of geheimen niet meer `az keyvault delete` nodig hebt, u de sleutelkluis verwijderen met behulp van de opdracht:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

U kunt ook een volledige Azure-resourcegroep verwijderen. Deze bevat de sleutelkluis en alle andere resources die u hebt opgenomen in de groep:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Diverse Azure-opdrachtinterfaceopdrachten voor meerdere azure-clients

Andere opdrachten die u mogelijk nuttig vindt voor het beheer van Azure Key Vault.

Met deze opdracht vindt u een tabelweergave van alle toetsen en geselecteerde eigenschappen:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Met deze opdracht wordt een volledige lijst met eigenschappen voor de opgegeven sleutel weergegeven:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Met deze opdracht vindt u een tabelvormige weergave van alle geheime namen en geselecteerde eigenschappen:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Hier is een voorbeeld van het verwijderen van een specifieke sleutel:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Hier is een voorbeeld van het verwijderen van een specifiek geheim:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Key Vault CLI-verwijzing](/cli/azure/keyvault)voor volledige Azure CLI-verwijzing voor belangrijke vault-opdrachten.

- Zie de handleiding [van de Azure Key Vault-ontwikkelaar](key-vault-developers-guide.md) voor het programmeren van verwijzingen

- Zie [HSM-beveiligde sleutels gebruiken met Azure Key Vault](key-vault-hsm-protected-keys.md)voor informatie over Azure Key Vault en HSM's.
