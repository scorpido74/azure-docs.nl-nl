---
title: Uw eigen sleutel configureren voor het versleutelen van Azure Service Bus gegevens in rust
description: Dit artikel bevat informatie over het configureren van uw eigen sleutel voor het versleutelen van Azure Service Bus gegevens rest.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: aschhab
ms.openlocfilehash: 356f825524192c3b6cf7df7f0460975f23ea4f7c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852287"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal-preview"></a>Door de klant beheerde sleutels voor het versleutelen van Azure Service Bus gegevens op rest configureren met behulp van de Azure Portal (preview)
Azure Service Bus Premium zorgt voor versleuteling van gegevens in rust met Azure Storage-service versleuteling (Azure SSE). Service Bus Premium is afhankelijk van Azure Storage om de gegevens op te slaan en de standaard instelling is dat alle gegevens die zijn opgeslagen met Azure Storage, worden versleuteld met door micro soft beheerde sleutels. 

## <a name="overview"></a>Overzicht
Azure Service Bus ondersteunt nu de optie voor het versleutelen van gegevens in rust met door micro soft beheerde sleutels of door de klant beheerde sleutels (Bring Your Own Key-BYOK). met deze functie kunt u toegang tot de door de klant beheerde sleutels maken, draaien, uitschakelen en intrekken die worden gebruikt voor het versleutelen van Azure Service Bus op rest.

Het inschakelen van de functie BYOK is een eenmalige installatie procedure voor uw naam ruimte.

> [!NOTE]
> Er zijn enkele voor behoud van de door de klant beheerde sleutel voor versleuteling aan de service zijde. 
>   * Deze functie wordt ondersteund door [Azure service bus Premium](service-bus-premium-messaging.md) -laag. Het kan niet worden ingeschakeld voor de standaardlaag Service Bus naam ruimten.
>   * De versleuteling kan alleen worden ingeschakeld voor nieuwe of lege naam ruimten. Als de naam ruimte gegevens bevat, mislukt de versleutelings bewerking.
>   * Als de [service-eind punten van Virtual Network (VNet)](service-bus-service-endpoints.md) zijn geconfigureerd op Azure Key Vault voor uw service bus naam ruimte, wordt BYOK niet ondersteund. 

U kunt Azure Key Vault gebruiken voor het beheren van uw sleutels en het controleren van uw sleutel gebruik. U kunt zelf sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren. Zie [Wat is Azure Key Vault?](../key-vault/key-vault-overview.md) voor meer informatie over Azure Key Vault.

In dit artikel wordt beschreven hoe u een sleutel kluis kunt configureren met door de klant beheerde sleutels met behulp van de Azure Portal. Voor informatie over het maken van een sleutel kluis met behulp van de Azure Portal, raadpleegt u [Quick Start: een geheim instellen en ophalen van Azure Key Vault met behulp van de Azure Portal](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Voor het gebruik van door de klant beheerde sleutels met Azure Service Bus is vereist dat er twee vereiste eigenschappen zijn geconfigureerd voor de sleutel kluis. Dit zijn: **voorlopig verwijderen** en **niet opschonen**. Deze eigenschappen zijn standaard ingeschakeld wanneer u een nieuwe sleutel kluis maakt in de Azure Portal. Als u deze eigenschappen echter wilt inschakelen voor een bestaande sleutel kluis, moet u Power shell of Azure CLI gebruiken.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen
Voer de volgende stappen uit om door de klant beheerde sleutels in te scha kelen in de Azure Portal:

1. Navigeer naar uw Service Bus Premium-naam ruimte.
2. Selecteer op de pagina **instellingen** van uw service bus-naam ruimte de optie **versleuteling (preview-versie)** .
3. Selecteer de door de **klant beheerde sleutel versleuteling bij rest** , zoals wordt weer gegeven in de volgende afbeelding.

    ![Door de klant beheerde sleutel inschakelen](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Een sleutel kluis met sleutels instellen

Nadat u door de klant beheerde sleutels hebt ingeschakeld, moet u de door de klant beheerde sleutel koppelen aan uw Azure Service Bus naam ruimte. Service Bus ondersteunt alleen Azure Key Vault. Als u de optie **versleuteling met door de klant beheerde sleutel** in de vorige sectie inschakelt, moet u de sleutel in azure Key Vault importeren. De sleutels moeten ook **zacht verwijderen** hebben en **niet leeg** zijn geconfigureerd voor de sleutel. Deze instellingen kunnen worden geconfigureerd met [Power shell](../key-vault/key-vault-soft-delete-powershell.md) of [cli](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Als u een nieuwe sleutel kluis wilt maken, volgt u de Azure Key Vault [Snelstartgids](../key-vault/key-vault-overview.md). Zie [over sleutels, geheimen en certificaten](../key-vault/about-keys-secrets-and-certificates.md)voor meer informatie over het importeren van bestaande sleutels.
1. Gebruik de opdracht AZ-sleutel [kluis maken](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) om zowel zacht verwijderen als beveiliging opschonen in te scha kelen bij het maken van een kluis.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Gebruik de opdracht AZ-sleutel [kluis bijwerken](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) om een schone beveiliging toe te voegen aan een bestaande kluis (waarvoor al zacht verwijderen is ingeschakeld).

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Maak sleutels door de volgende stappen uit te voeren:
    1. Als u een nieuwe sleutel wilt maken, selecteert u **genereren/importeren** in het menu **sleutels** onder **instellingen**.
        
        ![Selecteer de knop genereren/importeren](./media/configure-customer-managed-key/select-generate-import.png)

    1. Stel **Opties** in voor het **genereren** en geven van de sleutel een naam.

        ![Een sleutel maken](./media/configure-customer-managed-key/create-key.png) 

    1. U kunt nu deze sleutel selecteren om te koppelen aan de Service Bus naam ruimte voor het versleutelen van de vervolg keuzelijst. 

        ![Selecteer een sleutel in de sleutel kluis](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > U kunt Maxi maal drie sleutels toevoegen voor redundantie. In het geval dat een van de sleutels is verlopen of niet toegankelijk is, worden de andere sleutels gebruikt voor versleuteling.
        
    1. Vul de Details voor de sleutel in en klik op **selecteren**. Dit zorgt ervoor dat de versleuteling van gegevens in rust in de naam ruimte met een door de klant beheerde sleutel. 


> [!IMPORTANT]
> Als u een door de klant beheerde sleutel en geo-nood herstel wilt gebruiken, raadpleegt u de onderstaande- 
>
> Voor het inschakelen van versleuteling in rust met door de klant beheerde sleutel, wordt een [toegangs beleid](../key-vault/key-vault-secure-your-key-vault.md) ingesteld voor de service bus beheerde identiteit op de opgegeven Azure-hoofd kluis. Dit zorgt voor gecontroleerde toegang tot de Azure-sleutel kluis vanuit de Azure Service Bus naam ruimte.
>
> Vanwege dit:
> 
>   * Als [geo-nood herstel](service-bus-geo-dr.md) al is ingeschakeld voor de service bus naam ruimte en u de door de klant beheerde sleutel wilt inschakelen, 
>     * Koppeling verbreekt
>     * [Stel het toegangs beleid](../key-vault/managed-identity.md) voor de beheerde identiteit voor de primaire en secundaire naam ruimten in op de sleutel kluis.
>     * Stel versleuteling in voor de primaire naam ruimte.
>     * De primaire en secundaire naam ruimten opnieuw koppelen.
> 
>   * Als u geo-DR wilt inschakelen voor een Service Bus naam ruimte waarin de door de klant beheerde sleutel al is ingesteld,
>     * [Stel het toegangs beleid](../key-vault/managed-identity.md) voor de beheerde identiteit voor de secundaire naam ruimte in op de sleutel kluis.
>     * Koppel de primaire en secundaire naam ruimte.


## <a name="rotate-your-encryption-keys"></a>Uw versleutelings sleutels draaien

U kunt de sleutel in de sleutel kluis draaien met behulp van het rotatie mechanisme voor Azure-sleutel kluizen. Zie voor meer informatie [sleutel rotatie en controle instellen](../key-vault/key-vault-key-rotation-log-monitoring.md). Activerings-en verloop datums kunnen ook worden ingesteld op het automatiseren van sleutel rotatie. Met de Service Bus-service worden nieuwe sleutel versies gedetecteerd en worden ze automatisch gestart.

## <a name="revoke-access-to-keys"></a>Toegang tot sleutels intrekken

Als u de toegang tot de versleutelings sleutels intrekt, worden de gegevens niet verwijderd uit Service Bus. De gegevens kunnen echter niet worden geopend vanuit de naam ruimte Service Bus. U kunt de versleutelings sleutel intrekken via toegangs beleid of door de sleutel te verwijderen. Meer informatie over toegangs beleid en het beveiligen van uw sleutel kluis van [beveiligde toegang tot een sleutel kluis](../key-vault/key-vault-secure-your-key-vault.md).

Zodra de versleutelings sleutel is ingetrokken, wordt de Service Bus-service op de versleutelde naam ruimte niet meer bruikbaar. Als de toegang tot de sleutel is ingeschakeld of de verwijderde sleutel is hersteld, wordt de sleutel door Service Bus service gekozen, zodat u toegang hebt tot de gegevens van de versleutelde Service Bus naam ruimte.

> [!NOTE]
> Als u een bestaande versleutelings sleutel uit de sleutel kluis verwijdert en deze vervangt door een nieuwe sleutel in de naam ruimte Service Bus, omdat de sleutel delete nog steeds geldig is (omdat deze in de cache wordt opgeslagen) tot een uur, zijn de oude gegevens (die zijn versleuteld met de oude sleutel) mogelijk nog steeds toegankelijk Alon g met de nieuwe gegevens, die nu alleen toegankelijk zijn met de nieuwe sleutel. Dit gedrag is inherent aan het ontwerp van de preview-versie van de functie. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:
- [Overzicht van Service Bus](service-bus-messaging-overview.md)
- [Overzicht van Key Vault](../key-vault/key-vault-overview.md)


