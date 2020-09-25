---
title: Azure-Service Fabric-Service Fabric toepassing sleutel kluis verwijzingen gebruiken
description: In dit artikel wordt uitgelegd hoe u de KeyVaultReference-ondersteuning van service Fabric gebruikt voor toepassings geheimen.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: c4de6ae17ae728e1dbadbd6d6e2d94c0e1471112
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261138"
---
# <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>KeyVaultReference-ondersteuning voor Service Fabric toepassingen (preview)

Een veelvoorkomende uitdaging bij het bouwen van Cloud toepassingen is het veilig opslaan van geheimen dat vereist is voor uw toepassing. U kunt bijvoorbeeld de referenties van de container opslagplaats opslaan in de sleutel kluis en hiernaar verwijzen in het toepassings manifest. Service Fabric KeyVaultReference gebruikt Service Fabric beheerde identiteit en maakt het eenvoudig om te verwijzen naar sleutel kluis geheimen. In de rest van dit artikel vindt u informatie over het gebruik van Service Fabric KeyVaultReference en bevat een typisch gebruik.

> [!IMPORTANT]
> Het gebruik van deze preview-functie wordt niet aanbevolen in productie omgevingen.

> [!NOTE]
> De preview-functie voor de sleutel kluis referentie ondersteunt alleen [versie](https://docs.microsoft.com/azure/key-vault/general/about-keys-secrets-certificates#objects-identifiers-and-versioning) -geheimen. Geheimen zonder versie worden niet ondersteund.

## <a name="prerequisites"></a>Vereisten

- Beheerde identiteit voor toepassing (MIT)
    
    Service Fabric KeyVaultReference-ondersteuning maakt gebruik van de beheerde identiteit van de toepassing en daarom moeten toepassingen die KeyVaultReferences gebruiken, gebruikmaken van beheerde identiteit. Volg dit [document](concepts-managed-identity.md) om beheerde identiteit in te scha kelen voor uw toepassing.

- Archief met centrale geheimen (CSS).

    Central geheimen Store (CSS) is de versleutelde lokale geheimen cache van Service Fabric. CSS is een lokale archief cache voor het opslaan van gevoelige gegevens, zoals een wacht woord, tokens en sleutels, versleuteld in het geheugen. KeyVaultReference, zodra het is opgehaald, worden in de cache opgeslagen in CSS.

    Voeg het onderstaande toe aan uw cluster configuratie onder `fabricSettings` om alle vereiste functies voor KeyVaultReference-ondersteuning in te scha kelen.

    ```json
    "fabricSettings": 
    [
        ...
    {
                "name":  "CentralSecretService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
                ]
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > Het is raadzaam om een afzonderlijk versleutelings certificaat voor CSS te gebruiken. U kunt deze toevoegen onder de sectie ' CentralSecretService '.
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Om de wijzigingen van kracht te laten worden, moet u ook het upgrade beleid wijzigen om een geforceerde opnieuw op te geven van de Service Fabric runtime op elk knoop punt wanneer de upgrade wordt uitgevoerd via het cluster. Opnieuw opstarten zorgt ervoor dat de nieuw ingeschakelde systeem service wordt gestart en wordt uitgevoerd op elk knoop punt. In het onderstaande code fragment is forceRestart de essentiële instelling. Gebruik uw bestaande waarden voor de overige instellingen.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- Machtigingen voor beheerde identiteits toegang verlenen aan de sleutel kluis

    Raadpleeg dit [document](how-to-grant-access-other-resources.md) voor meer informatie over het verlenen van beheerde identiteits toegang tot de sleutel kluis. Opmerking Als u een door het systeem toegewezen beheerde identiteit gebruikt, wordt de beheerde identiteit alleen gemaakt na de implementatie van de toepassing.

## <a name="keyvault-secret-as-application-parameter"></a>Sleutel kluis geheim als toepassings parameter
Stel dat de toepassing het wacht woord voor de back-enddatabase moet lezen dat is opgeslagen in de sleutel kluis. Service Fabric KeyVaultReference-ondersteuning maakt het eenvoudig. In het onderstaande voor beeld wordt geheim gelezen uit de sleutel `DBPassword` kluis met behulp van service Fabric KeyVaultReference-ondersteuning.

- Een sectie toevoegen aan settings.xml

    `DBPassword`Para meter met het `KeyVaultReference` type en de waarde definiëren`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Raadpleeg de nieuwe sectie in ApplicationManifest.xml in `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- KeyVaultReference in uw toepassing gebruiken

    Service Fabric op service-instantiëring wordt de KeyVaultReference-para meter omgezet met de beheerde identiteit van de toepassing. Elke para meter die onder wordt weer gegeven `<Section  Name=dbsecrets>` , is een bestand in de map waarnaar wordt aangegeven door EnvironmentVariable SecretPath. Onder C#-code fragment ziet u hoe u DBPassword in uw toepassing kunt lezen.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Voor het container scenario kunt u de koppel punt gebruiken om te bepalen waar de `secrets` wordt gekoppeld.

## <a name="keyvault-secret-as-environment-variable"></a>Sleutel kluis geheim als omgevings variabele

Service Fabric omgevings variabelen bieden nu ondersteuning voor het type KeyVaultReference, zoals in het onderstaande voor beeld ziet u hoe u een omgevings variabele verbindt met een geheim dat is opgeslagen in de sleutel kluis.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Sleutel kluis geheim als container opslagplaats wachtwoord
KeyVaultReference is een ondersteund type voor container RepositoryCredentials. in het onderstaande voor beeld ziet u hoe u een sleutel kluis referentie gebruikt als container opslagplaats wachtwoord.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>Veelgestelde vragen
- De beheerde identiteit moet worden ingeschakeld voor KeyVaultReference-ondersteuning. de activering van de toepassing mislukt als KeyVaultReference wordt gebruikt zonder beheerde identiteit in te scha kelen.

- Als u een systeem toegewezen identiteit gebruikt, wordt deze alleen gemaakt nadat de toepassing is geïmplementeerd en wordt er een circulaire afhankelijkheid gemaakt. Zodra uw toepassing is geïmplementeerd, kunt u het systeem toegewezen identiteits toegangs machtiging verlenen aan de sleutel kluis. U kunt de door het systeem toegewezen identiteit vinden op naam {cluster}/{Application name}/{ServiceName}

- De sleutel kluis moet zich in hetzelfde abonnement benemen als uw service Fabric-cluster. 

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor Azure-sleutel kluis](../key-vault/index.yml)
