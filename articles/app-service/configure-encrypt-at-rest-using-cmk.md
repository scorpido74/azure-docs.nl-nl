---
title: Uw toepassingsbron in rust versleutelen
description: Versleutel uw toepassingsgegevens in Azure Storage en implementeer deze als een pakketbestand.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 7e5e809fe8b670ae6ec5bfd15e54f9a8019e76d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408740"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Versleuteling in rust met door de klant beheerde sleutels

Voor het versleutelen van de toepassingsgegevens van uw web-app in rust is een Azure Storage-account en een Azure Key Vault vereist. Deze services worden gebruikt wanneer u uw app uitvoert vanuit een implementatiepakket.

  - [Azure Storage biedt versleuteling in rust.](../storage/common/storage-service-encryption.md) U systeemsleutels of uw eigen, door de klant beheerde sleutels gebruiken. Dit is waar uw toepassingsgegevens worden opgeslagen wanneer deze niet worden uitgevoerd in een web-app in Azure.
  - [Uitvoeren vanuit een implementatiepakket](deploy-run-package.md) is een implementatiefunctie van App Service. Hiermee u uw site-inhoud implementeren vanuit een Azure Storage-account met behulp van een URL (Shared Access Signature) (SAS).
  - [Key Vault-referenties](app-service-key-vault-references.md) zijn een beveiligingsfunctie van App Service. Hiermee u geheimen importeren tijdens runtime als toepassingsinstellingen. Gebruik dit om de SAS-URL van uw Azure Storage-account te versleutelen.

## <a name="set-up-encryption-at-rest"></a>Versleuteling in rust instellen

### <a name="create-an-azure-storage-account"></a>Een Azure Storage-account maken

Maak eerst [een Azure Storage-account](../storage/common/storage-account-create.md) aan en [versleutel het met door de klant beheerde sleutels.](../storage/common/encryption-customer-managed-keys.md) Zodra het opslagaccount is gemaakt, gebruikt u de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) om pakketbestanden te uploaden.

Gebruik vervolgens de Storage Explorer om [een SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)te genereren. 

> [!NOTE]
> Sla deze SAS URL op, dit wordt later gebruikt om veilige toegang tot het implementatiepakket bij runtime mogelijk te maken.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Uitvoeren configureren vanuit een pakket vanuit uw opslagaccount
  
Zodra u uw bestand hebt geüpload naar blob-opslag `WEBSITE_RUN_FROM_PACKAGE` en een SAS-URL voor het bestand hebt, stelt u de toepassingsinstelling in op de SAS-URL. In het volgende voorbeeld wordt Azure CLI gebruikt:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Als u deze toepassingsinstelling toevoegt, wordt uw web-app opnieuw opgestart. Nadat de app opnieuw is opgestart, bladert u ernaar en controleert u of de app correct is gestart met het implementatiepakket. Als de toepassing niet correct is gestart, raadpleegt u de [handleiding Voor het oplossen van pakketten uitvoeren.](deploy-run-package.md#troubleshooting)

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>De toepassingsinstelling versleutelen met key vault-verwijzingen

Nu u de `WEBSITE_RUN_FROM_PACKAGE` waarde van de toepassingsinstelling vervangen door een Key Vault-verwijzing naar de sas-gecodeerde URL. Dit houdt de SAS URL versleuteld in Key Vault, wat een extra beveiligingslaag biedt.

1. Gebruik de [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) volgende opdracht om een Key Vault-instantie te maken.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Volg [deze instructies om uw app toegang](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) te verlenen tot uw sleutelkluis:

1. Gebruik de [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) volgende opdracht om uw externe URL als geheim toe te voegen in uw sleutelkluis:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Gebruik de [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) volgende opdracht `WEBSITE_RUN_FROM_PACKAGE` om de toepassingsinstelling met de waarde te maken als verwijzing naar de externe URL:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    De `<secret-version>` zal in de uitvoer `az keyvault secret set` van de vorige opdracht.

Als u deze toepassingsinstelling bijwerkt, wordt uw web-app opnieuw opgestart. Nadat de app opnieuw is opgestart, bladert u ernaar om te zoeken of deze correct is gestart met behulp van de verwijzing Key Vault.

## <a name="how-to-rotate-the-access-token"></a>Het toegangstoken roteren

Het is de beste gewoonte om de SAS-sleutel van uw opslagaccount periodiek te roteren. Om ervoor te zorgen dat de web-app niet per ongeluk toegang verliest, moet u ook de SAS-URL in Key Vault bijwerken.

1. Draai de SAS-sleutel door te navigeren naar uw opslagaccount in de Azure-portal. Klik onder De sleutels van **Instellingentoegang** > **Access keys**op het pictogram om de SAS-toets te draaien.

1. Kopieer de nieuwe SAS-URL en gebruik de volgende opdracht om de bijgewerkte SAS-URL in uw sleutelkluis in te stellen:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Werk de verwijzing naar de sleutelkluis in de toepassingsinstelling bij naar de nieuwe geheime versie:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    De `<secret-version>` zal in de uitvoer `az keyvault secret set` van de vorige opdracht.

## <a name="how-to-revoke-the-web-apps-data-access"></a>De gegevenstoegang van de web-app intrekken

Er zijn twee methoden om de toegang van de web-app tot het opslagaccount in te trekken. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>De SAS-sleutel voor het Azure Storage-account roteren

Als de SAS-sleutel voor het opslagaccount wordt gedraaid, heeft de web-app geen toegang meer tot het opslagaccount, maar blijft deze wel worden uitgevoerd met de laatst gedownloade versie van het pakketbestand. Start de web-app opnieuw op om de laatst gedownloade versie te wissen.

### <a name="remove-the-web-apps-access-to-key-vault"></a>De toegang van de web-app tot Key Vault verwijderen

U de toegang van de web-app tot de sitegegevens intrekken door de toegang van de web-app tot Key Vault uit te schakelen. Verwijder hiervoor het toegangsbeleid voor de identiteit van de web-app. Dit is dezelfde identiteit die u eerder hebt gemaakt tijdens het configureren van referentiereferenties voor belangrijke kluizen.

## <a name="summary"></a>Samenvatting

Uw toepassingsbestanden worden nu in rust versleuteld in uw opslagaccount. Wanneer uw web-app wordt gestart, wordt de SAS-URL opgehaald uit uw sleutelkluis. Ten slotte laadt de web-app de toepassingsbestanden van het opslagaccount. 

Als u de toegang van de web-app tot uw opslagaccount moet intrekken, u de toegang tot de sleutelkluis intrekken of de opslagaccountsleutels roteren, waardoor de SAS-URL ongeldig wordt.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Zijn er extra kosten verbonden aan het uitvoeren van mijn web-app vanuit het implementatiepakket?

Alleen de kosten die zijn gekoppeld aan het Azure Storage-account en eventuele toepasselijke uitweinemen.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Hoe beïnvloedt het uitvoeren van het implementatiepakket mijn web-app?

- Als u uw app `wwwroot/` uitvoert vanuit het implementatiepakket, wordt alleen-lezen. Uw app ontvangt een foutmelding wanneer deze naar deze map probeert te schrijven.
- TAR- en GZIP-formaten worden niet ondersteund.
- Deze functie is niet compatibel met lokale cache.

## <a name="next-steps"></a>Volgende stappen

- [Key Vault-referenties voor App-service](app-service-key-vault-references.md)
- [Azure Storage-versleuteling voor gegevens in rust](../storage/common/storage-service-encryption.md)
