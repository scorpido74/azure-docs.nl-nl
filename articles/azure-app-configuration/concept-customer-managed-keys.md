---
title: Door de klant beheerde sleutels gebruiken om uw configuratiegegevens te versleutelen
description: Uw configuratiegegevens versleutelen met door de klant beheerde sleutels
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 5749b2fc58c4e1c5c75142f85a5132946714e25b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472633"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Door de klant beheerde sleutels gebruiken om uw app-configuratiegegevens te versleutelen
Azure App Configuration [versleutelt gevoelige informatie in rust.](../security/fundamentals/encryption-atrest.md) Het gebruik van door de klant beheerde sleutels biedt verbeterde gegevensbescherming doordat u uw versleutelingssleutels beheren.  Wanneer beheerde sleutelversleuteling wordt gebruikt, wordt alle gevoelige informatie in app-configuratie versleuteld met een door de gebruiker geleverde Azure Key Vault-sleutel.  Dit biedt de mogelijkheid om de encryptiesleutel op aanvraag te roteren.  Het biedt ook de mogelijkheid om de toegang van Azure App Configuration tot gevoelige informatie in te trekken door de toegang van het app-configuratie-exemplaar tot de sleutel in te trekken.

## <a name="overview"></a>Overzicht 
Azure App Configuration versleutelt gevoelige informatie in rust met behulp van een 256-bits AES-versleutelingssleutel van Microsoft. Elke app-configuratie-instantie heeft zijn eigen encryptiesleutel beheerd door de service en wordt gebruikt om gevoelige informatie te versleutelen. Gevoelige informatie bevat de waarden die in sleutelwaardeparen worden gevonden.  Wanneer de sleutelmogelijkheid voor de klant is ingeschakeld, gebruikt app-configuratie een beheerde identiteit die is toegewezen aan de instantie App-configuratie om te verifiëren met Azure Active Directory. De beheerde identiteit roept vervolgens Azure Key Vault aan en omsluit de versleutelingssleutel van het app-configuratie-exemplaar. De ingepakte encryptiesleutel wordt vervolgens opgeslagen en de uitgepakte encryptiesleutel wordt gedurende een uur in de cache opgeslagen in de app-configuratie. App-configuratie vernieuwt de uitgepakte versie van de versleutelingssleutel van het app-configuratie-exemplaar elk uur. Dit zorgt voor beschikbaarheid onder normale bedrijfsomstandigheden. 

>[!IMPORTANT]
> Als de identiteit die is toegewezen aan de instantie App-configuratie niet langer is toegestaan om de versleutelingssleutel van de instantie uit te pakken of als de beheerde sleutel permanent wordt verwijderd, is het niet langer mogelijk om gevoelige informatie die is opgeslagen in de App te decoderen Configuratie-instantie. Als u de [soft delete-functie](../key-vault/key-vault-ovw-soft-delete.md) van Azure Key Vault gebruikt, wordt de kans beperkt dat uw versleutelingssleutel per ongeluk wordt verwijderd.

Wanneer gebruikers de sleutelmogelijkheid van de klant inschakelen op hun Azure App-configuratie-exemplaar, bepalen ze de mogelijkheid van de service om toegang te krijgen tot hun gevoelige informatie. De beheerde sleutel dient als een root-encryptiesleutel. Een gebruiker kan de toegang van de app-configuratie-instantie tot zijn beheerde sleutel intrekken door het toegangsbeleid voor sleutelkluizen te wijzigen. Wanneer deze toegang wordt ingetrokken, verliest app-configuratie de mogelijkheid om gebruikersgegevens binnen een uur te decoderen. Op dit moment verbiedt de instantie App-configuratie alle toegangspogingen. Deze situatie kan worden hersteld door de service opnieuw toegang te verlenen tot de beheerde sleutel.  Binnen een uur kan app-configuratie gebruikersgegevens decoderen en werken onder normale omstandigheden.

>[!NOTE]
>Alle configuratiegegevens van Azure App worden maximaal 24 uur bewaard in een geïsoleerde back-up. Dit geldt ook voor de onverpakte encryptiesleutel. Deze gegevens zijn niet onmiddellijk beschikbaar voor het service- of serviceteam. In het geval van een noodherstel trekt azure-appconfiguratie zich opnieuw terug uit de beheerde sleutelgegevens.

## <a name="requirements"></a>Vereisten
De volgende onderdelen zijn vereist om de door de klant beheerde sleutelmogelijkheid voor Azure App-configuratie in te schakelen:
- Instantie Azure App-configuratie met standaardlaag
- Azure Key Vault met functies voor soft-delete en purge-protection ingeschakeld
- Een RSA- of RSA-HSM-sleutel in de Key Vault
    - De sleutel mag niet worden verlopen, moet worden ingeschakeld, en het moet zowel wrap en uitpakken mogelijkheden ingeschakeld

Zodra deze bronnen zijn geconfigureerd, blijven er twee stappen over om Azure App-configuratie toe te staan de Key Vault-sleutel te gebruiken:
1. Een beheerde identiteit toewijzen aan de instantie Azure App-configuratie
2. Geef de `GET` `WRAP`identiteit `UNWRAP` en machtigingen in het toegangsbeleid van de doelgroep Key Vault.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Door de klant beheerde sleutelversleuteling inschakelen voor uw Azure App-configuratie-exemplaar
Om te beginnen hebt u een goed geconfigureerde Azure App-configuratie-instantie nodig. Als u nog geen app-configuratie-exemplaar beschikbaar hebt, volgt u een van deze snelstarts om er een in te stellen:
- [Een ASP.NET Core-app maken met Azure-app-configuratie](quickstart-aspnet-core-app.md)
- [Een .NET Core-app maken met Azure-appconfiguratie](quickstart-dotnet-core-app.md)
- [Een .NET Framework-app maken met Azure-appconfiguratie](quickstart-dotnet-app.md)
- [Een Java Spring-app maken met Azure-appconfiguratie](quickstart-java-spring-app.md)

>[!TIP]
> De Azure Cloud Shell is een gratis interactieve shell die u gebruiken om de instructies voor de opdrachtregel in dit artikel uit te voeren.  Er zijn veelvoorkomende Azure-hulpprogramma's vooraf geïnstalleerd, waaronder de .NET Core SDK. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud Shell](https://shell.azure.com) vanaf shell.azure.com.  U meer informatie over Azure Cloud Shell lezen door [onze documentatie te lezen](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Een Azure Key Vault maken en configureren
1. Maak een Azure Key Vault met de Azure CLI.  Houd er `vault-name` `resource-group-name` rekening mee dat beide en zijn door de gebruiker verstrekt en moet uniek zijn.  We `contoso-vault` gebruiken `contoso-resource-group` en in deze voorbeelden.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Schakel soft-delete en purge-bescherming in voor de Key Vault. Vervang de namen van`contoso-vault`de Key Vault`contoso-resource-group`( ) en Resource Group ( ) gemaakt in stap 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Maak een Key Vault-sleutel. Geef een `key-name` unieke voor deze sleutel, en vervang`contoso-vault`de namen van de Key Vault ( ) gemaakt in stap 1. Geef op `RSA` of `RSA-HSM` u de voorkeur geeft aan of versleuteling.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    De uitvoer van deze opdracht toont de sleutel-ID ("kind") voor de gegenereerde sleutel.  Noteer de sleutel-ID die u later in deze oefening wilt gebruiken.  De sleutel-ID heeft `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`het formulier: .  De sleutel-ID heeft drie belangrijke onderdelen:
    1. Key Vault URI: 'https://{mijn sleutelkluis}.vault.azure.net
    1. Key Vault-sleutelnaam: {Sleutelnaam}
    1. Key Vault-sleutelversie: {Key-versie}

1. Maak een beheerde identiteit met systeemtoegewezen met behulp van de Azure CLI, waarbij de naam van de instantie en resourcegroep van uw app-configuratie die in de vorige stappen is gebruikt, wordt vervangen. De beheerde identiteit wordt gebruikt om toegang te krijgen tot de beheerde sleutel. We `contoso-app-config` gebruiken om de naam van een app-configuratie-exemplaar te illustreren:
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    De uitvoer van deze opdracht omvat de hoofd-ID ("principalId") en tenant-ID ("tenandId") van de toegewezen identiteit van het systeem.  Dit zal worden gebruikt om de identiteit toegang te verlenen tot de beheerde sleutel.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. De beheerde identiteit van het azure-app-configuratie-exemplaar heeft toegang tot de sleutel nodig om sleutelvalidatie, versleuteling en decryptie uit te voeren. De specifieke set van acties waartoe `GET` `WRAP`het `UNWRAP` toegang nodig heeft omvat: , en voor sleutels.  Voor het verlenen van de toegang is de hoofd-ID van de beheerde identiteit van het app-configuratie-exemplaar vereist. Deze waarde werd verkregen in de vorige stap. Het wordt hieronder `contoso-principalId`weergegeven als . Toestemming verlenen aan de beheerde sleutel met behulp van de opdrachtregel:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Zodra de instantie Azure App Configuration toegang heeft tot de beheerde sleutel, kunnen we de door de klant beheerde sleutelmogelijkheid in de service inschakelen met behulp van de Azure CLI. De volgende eigenschappen terugroepen die `key name` `key vault URI`zijn geregistreerd tijdens de stappen voor het maken van sleutels: .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Uw azure-app-configuratie-exemplaar is nu geconfigureerd voor het gebruik van een door de klant beheerde sleutel die is opgeslagen in Azure Key Vault.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u de instantie Azure App-configuratie geconfigureerd om een door de klant beheerde sleutel voor versleuteling te gebruiken.  Meer informatie over het [integreren van uw service met Azure Managed Identities](howto-integrate-azure-managed-service-identity.md).