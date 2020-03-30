---
title: Azure Service Fabric - KeyVault-verwijzingen voor servicefabric-toepassingen gebruiken
description: In dit artikel wordt uitgelegd hoe u KeyVaultReference-ondersteuning voor toepassingsgeheimen gebruiken.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545480"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>KeyVaultReference-ondersteuning voor Service Fabric-toepassingen (voorbeeld)

Een veelvoorkomende uitdaging bij het bouwen van cloudtoepassingen is hoe u veilig geheimen opslaan die door uw toepassing vereist zijn. U bijvoorbeeld de referenties van de containeropslagplaats opslaan in keyvault en ernaar verwijzen in het toepassingsmanifest. Service Fabric KeyVaultReference maakt gebruik van Service Fabric Managed Identity en maakt het eenvoudig om keyvault-geheimen te verwijzen. In de rest van dit artikel wordt beschreven hoe u Service Fabric KeyVaultReference gebruikt en bevat u een typisch gebruik.

## <a name="prerequisites"></a>Vereisten

- Beheerde identiteit voor toepassing (MIT)
    
    Service Fabric KeyVaultReference-ondersteuning maakt gebruik van de beheerde identiteit van de toepassing en daarom moeten toepassingen die KeyVaultReferences willen gebruiken, Managed Identity gebruiken. Volg dit [document](concepts-managed-identity.md) om beheerde identiteit voor uw toepassing in te schakelen.

- Central Secrets Store (CSS).

    Central Secrets Store (CSS) is de versleutelde cache van Service Fabric voor lokale geheimen. CSS is een lokale geheime winkelcache die gevoelige gegevens, zoals een wachtwoord, tokens en sleutels, versleuteld in het geheugen bewaart. KeyVaultReference, eenmaal opgehaald, worden in de cache opgeslagen in CSS.

    Voeg hieronder de onderstaande `fabricSettings` toevoegen aan uw clusterconfiguratie om alle vereiste functies voor KeyVaultReference-ondersteuning in te schakelen.

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
    > Het wordt aanbevolen om een apart versleutelingscertificaat voor CSS te gebruiken. U het toevoegen onder de sectie CentralSecretService.
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Als u wilt dat de wijzigingen van kracht worden, moet u ook het upgradebeleid wijzigen om een krachtige herstart van de runtime van de ServiceFabric op elk knooppunt op te geven naarmate de upgrade door het cluster vordert. Deze herstart zorgt ervoor dat de nieuw ingeschakelde systeemservice wordt gestart en op elk knooppunt wordt uitgevoerd. In het onderstaande fragment is forceRestart de essentiële instelling; gebruik uw bestaande waarden voor de rest van de instellingen.
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
- De beheerde identiteitstoegangstoestemming van de toepassing verlenen aan de keyvault

    Raadpleeg dit [document](how-to-grant-access-other-resources.md) om te zien hoe u beheerde identiteitstoegang verleent tot keyvault. Let ook op als u system assigned managed identity gebruikt, wordt de beheerde identiteit pas gemaakt na implementatie van de toepassing.

## <a name="keyvault-secret-as-application-parameter"></a>Keyvault geheim als toepassingsparameter
Stel dat de toepassing het wachtwoord van de backenddatabase moet lezen dat is opgeslagen in keyvault, servicefabric KeyVaultReference-ondersteuning maakt het eenvoudig. Onderstaand voorbeeld `DBPassword` leest geheim van keyvault met behulp van Service Fabric KeyVaultReference ondersteuning.

- Een sectie toevoegen aan settings.xml

    Parameter `DBPassword` definiëren `KeyVaultReference` met Tekst en waarde`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Verwijzen naar de nieuwe sectie in ApplicationManifest.xml in`<ConfigPackagePolicies>`

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

- KeyVaultReference gebruiken in uw toepassing

    Service Fabric op serviceinstantiatie lost de KeyVaultReference-parameter op met behulp van de beheerde identiteit van de toepassing. Elke parameter die `<Section  Name=dbsecrets>` hieronder wordt vermeld, is een bestand onder de map waarnaar wordt aangegaan door EnvironmentVariable SecretPath. Hieronder C# code fragment laten zien hoe DBPassword te lezen in uw aanvraag.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Voor het containerscenario u de MountPoint `secrets` gebruiken om te bepalen waar de houder wordt gemonteerd.

## <a name="keyvault-secret-as-environment-variable"></a>Keyvault geheim als omgevingsvariabele

Service Fabric-omgevingsvariabelen ondersteunen nu het Type KeyVaultReference, hieronder ziet u hoe u een omgevingsvariabele binden aan een geheim dat is opgeslagen in KeyVault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Keyvault geheim als container repository wachtwoord
KeyVaultReference is een ondersteund type voor containerRepositoryCredentials, hieronder ziet u hoe u een keyvault-verwijzing gebruikt als wachtwoord voor containeropslagplaatsen.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>Veelgestelde vragen
- Beheerde identiteit moet zijn ingeschakeld voor KeyVaultReference-ondersteuning, uw activering van uw toepassing mislukt als KeyVaultReference wordt gebruikt zonder Managed Identity in te schakelen.

- Als u een door het systeem toegewezen identiteit gebruikt, wordt deze pas gemaakt nadat de toepassing is geïmplementeerd en wordt een cirkelvormige afhankelijkheid gecreëerd. Zodra uw toepassing is geïmplementeerd, u het systeem met toestemming voor identiteitstoegang verlenen aan keyvault. U de toegewezen identiteit van het systeem vinden op naam {cluster}/{toepassingsnaam}/{servicename}

- De keyvault moet in hetzelfde abonnement zitten als uw servicefabriccluster. 

## <a name="next-steps"></a>Volgende stappen

* [Azure KeyVault-documentatie](https://docs.microsoft.com/azure/key-vault/)
