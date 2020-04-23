---
title: Azure IoT Hub-ondersteuning voor virtuele netwerken
description: Het gebruik van een verbindings patroon voor virtuele netwerken met IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501435"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Ondersteuning voor virtuele netwerken IoT Hub

In dit artikel wordt het VNET-verbindings patroon geïntroduceerd en wordt uitgelegd hoe u een persoonlijke connectiviteits ervaring kunt instellen voor een IoT-hub via een Azure-VNET dat eigendom is van de klant.

> [!NOTE]
> De IoT Hub functies die in dit artikel worden beschreven, zijn momenteel beschikbaar voor IoT-hubs die zijn [gemaakt met een beheerde service-identiteit](#create-an-iot-hub-with-managed-service-identity) in de volgende REGIO'S: VS-Oost, Zuid-Centraal VS en VS-West 2.


## <a name="introduction"></a>Inleiding

IoT Hub hostnamen worden standaard toegewezen aan een openbaar eind punt met een openbaar routeerbaar IP-adres via internet. Zoals in de onderstaande afbeelding wordt weer gegeven, wordt dit IoT Hub open bare eind punt gedeeld tussen hubs die eigendom zijn van verschillende klanten en kunnen ze toegankelijk zijn voor IoT-apparaten via Wide Area-netwerken, evenals on-premises netwerken.

Verschillende IoT Hub functies, waaronder [bericht routering](./iot-hub-devguide-messages-d2c.md), het [uploaden van bestanden](./iot-hub-devguide-file-upload.md)en het [importeren/exporteren van bulk apparaten](./iot-hub-bulk-identity-mgmt.md) , vereisen een verbinding van IOT hub naar een Azure-resource van een klant in het open bare eind punt. Zoals hieronder wordt beschreven, vormen deze connectiviteits paden gezamenlijk het uitgaande verkeer van IoT Hub naar klant resources.
![IoT Hub openbaar eind punt](./media/virtual-network-support/public-endpoint.png)


Om verschillende redenen kunnen klanten de connectiviteit met hun Azure-resources (inclusief IoT Hub) beperken via een VNET waarvan ze eigenaar zijn en werken. Dit zijn de volgende redenen:

* Introductie van extra beveiligings lagen via isolatie op netwerk niveau voor uw IoT-hub door te voor komen dat de verbinding met uw hub via het open bare Internet wordt blootgesteld.

* Het inschakelen van een persoonlijke connectiviteits ervaring van uw on-premises netwerk assets zorgt ervoor dat uw gegevens en verkeer rechtstreeks naar het Azure-backbone netwerk worden verzonden.

* Exfiltration-aanvallen van gevoelige on-premises netwerken voor komen. 

* Hieronder zijn de Azure-brede connectiviteits patronen ingesteld met behulp van [privé-eind punten](../private-link/private-endpoint-overview.md).


In dit artikel wordt beschreven hoe u deze doelen kunt bereiken met behulp van [persoonlijke eind punten](../private-link/private-endpoint-overview.md) voor ingangs connectiviteit met IOT hub, zoals het gebruik van Azure Trusted First partij Services-uitzonde ring voor het afleiden van connectiviteit vanuit IOT hub met andere Azure-resources.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Ingangs connectiviteit met IoT Hub persoonlijke eind punten

Een persoonlijk eind punt is een privé-IP-adres dat is toegewezen in een VNET van de klant via welke een Azure-resource bereikbaar is. Door een persoonlijk eind punt voor uw IoT-hub te hebben, kunt u ervoor zorgen dat services in uw VNET IoT Hub bereiken zonder dat er verkeer naar het open bare eind punt van IoT Hub hoeft te worden verzonden. Op dezelfde manier kunnen apparaten die in uw on-premises werken, gebruikmaken van [VPN (virtueel particulier netwerk)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) of [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privé-peering om verbinding te krijgen met uw VNET in Azure en vervolgens naar uw IOT hub (via het persoonlijke eind punt). Als gevolg hiervan kunnen klanten die de connectiviteit met hun open bare eind punten van de IoT-hub willen beperken (of deze mogelijk volledig blok keren) dit doel halen met behulp van [IOT hub firewall regels](./iot-hub-ip-filtering.md) , terwijl de verbinding met hun hub met behulp van het privé-eind punt wordt behouden.

> [!NOTE]
> De hoofd focus van deze installatie is voor apparaten in een on-premises netwerk. Deze installatie wordt niet aanbevolen voor apparaten die zijn geïmplementeerd in een Wide Area-netwerk.

![IoT Hub openbaar eind punt](./media/virtual-network-support/virtual-network-ingress.png)

Voordat u doorgaat, controleert u of aan de volgende vereisten wordt voldaan:

* Uw IoT-hub moet worden ingericht met een [beheerde service-identiteit](#create-an-iot-hub-with-managed-service-identity).

* Uw IoT-hub moet worden ingericht in een van de [ondersteunde regio's](#regional-availability-private-endpoints).

* U hebt een Azure VNET ingericht met een subnet waarin het persoonlijke eind punt wordt gemaakt. Zie [een virtueel netwerk maken met behulp van Azure cli](../virtual-network/quick-create-cli.md) voor meer informatie.

* Voor apparaten die in een on-premises netwerk werken, stelt u [VPN (virtueel particulier netwerk)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) of [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privé-peering in op uw Azure VNET.


### <a name="regional-availability-private-endpoints"></a>Regionale Beschik baarheid (privé-eind punten)

Privé-eind punten die worden ondersteund in de IoT Hub zijn gemaakt in de volgende regio's:

* VS - oost

* VS - zuid-centraal

* VS - west 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Een persoonlijk eind punt voor IoT Hub ingang instellen

Voer de volgende stappen uit om een persoonlijk eind punt in te stellen:

1. Voer de volgende Azure CLI-opdracht uit om Azure IoT Hub provider opnieuw te registreren bij uw abonnement:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Ga naar het tabblad verbindingen met het **persoonlijke eind punt** in uw IOT hub Portal (dit tabblad is alleen beschikbaar voor IOT-hubs in de [ondersteunde regio's](#regional-availability-private-endpoints)) en **+** Klik op het teken om een nieuw persoonlijk eind punt toe te voegen.

3. Geef het abonnement, de resource groep, de naam en de regio voor het maken van het nieuwe persoonlijke eind punt op (in het ideale geval moet het persoonlijke eind punt worden gemaakt in dezelfde regio als uw hub; Zie de [sectie regionale Beschik baarheid](#regional-availability-private-endpoints) voor meer informatie).

4. Klik op **volgende: resource**, geef het abonnement voor uw IOT hub resource op en selecteer **' micro soft. devices/IotHubs '** als resource type, uw IOT hub naam als **resource**en **iotHub** als doel-subresource.

5. Klik op **volgende: Configuratie** en geef uw virtuele netwerk en subnet op om het persoonlijke eind punt in te maken. Selecteer de optie voor de integratie met de persoonlijke DNS-zone van Azure, indien gewenst.

6. Klik op **volgende: Tags**en geef eventueel labels voor uw resource op.

7. Klik op **beoordeling + maken** om uw persoonlijke eindpunt resource te maken.


### <a name="pricing-private-endpoints"></a>Prijzen (privé-eind punten)

Zie [prijzen voor persoonlijke Azure-koppelingen](https://azure.microsoft.com/pricing/details/private-link)voor prijs informatie.


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>De connectiviteit van IoT Hub met andere Azure-resources afbreken

IoT Hub moet toegang hebben tot uw Azure Blob-opslag, Event hubs, service bus-resources voor [bericht routering](./iot-hub-devguide-messages-d2c.md), het [uploaden van bestanden](./iot-hub-devguide-file-upload.md)en het [importeren/exporteren van bulk apparaten](./iot-hub-bulk-identity-mgmt.md), wat doorgaans plaatsvindt via het open bare eind punt van de resource. In het geval dat u uw opslag account, Event hubs of service bus-bron verbindt met een VNET, blokkeert de aanbevolen configuratie standaard de connectiviteit met de bron. Hierdoor wordt de functionaliteit van IoT Hub waarvoor toegang tot de resources nodig is, belemmerd.

Om deze situatie op te lossen, moet u de connectiviteit van uw IoT Hub-bron naar uw opslag account, Event hubs of service bus-resources inschakelen via de optie **voor vertrouwde services van Azure** .

De vereisten zijn als volgt:

* Uw IoT-hub moet worden ingericht in een van de [ondersteunde regio's](#regional-availability-trusted-microsoft-first-party-services).

* Aan uw IoT Hub moet een beheerde service-identiteit worden toegewezen bij de inrichtings tijd van de hub. Volg de instructies voor het [maken van een hub met een beheerde service-identiteit](#create-an-iot-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Regionale Beschik baarheid (vertrouwde micro soft-Services voor de eerste partij)

Azure-uitzonde ring voor services van de eerste partij om firewall beperkingen te omzeilen voor Azure Storage, Event hubs en service bus-bronnen worden alleen ondersteund voor IoT-hubs in de volgende regio's:

* VS - oost

* VS - zuid-centraal

* VS - west 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Prijzen (vertrouwde micro soft-Services voor de eerste partij)

De betrouw bare micro soft-functie voor de eerste partij Services-uitzonde ring is gratis in IoT-hubs in de [ondersteunde regio's](#regional-availability-trusted-microsoft-first-party-services). Kosten voor de ingerichte opslag accounts, Event hubs of service bus-resources worden afzonderlijk toegepast.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Een IoT-hub maken met een beheerde service-identiteit

Een beheerde service-identiteit kan worden toegewezen aan uw hub op het tijdstip van de inrichting van resources (deze functie wordt momenteel niet ondersteund voor bestaande hubs), waarvoor de IoT-hub TLS 1,2 moet gebruiken als de minimale versie. Voor dit doel moet u de ARM-resource sjabloon hieronder gebruiken:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

Nadat u de waarden voor uw `name`resource hebt vervangen `location`, `SKU.name` en `SKU.tier`, kunt u Azure CLI gebruiken om de resource in een bestaande resource groep te implementeren met behulp van:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Nadat de resource is gemaakt, kunt u de beheerde service-identiteit ophalen die aan uw hub is toegewezen met behulp van Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Als IoT Hub met een beheerde service-identiteit is ingericht, volgt u de bijbehorende sectie om routerings eindpunten in te stellen voor [opslag accounts](#egress-connectivity-to-storage-account-endpoints-for-routing), [Event hubs](#egress-connectivity-to-event-hubs-endpoints-for-routing)en [Service Bus](#egress-connectivity-to-service-bus-endpoints-for-routing) -resources, of om het [importeren en exporteren](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)van [bestanden](#egress-connectivity-to-storage-accounts-for-file-upload) te configureren.


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>De connectiviteit met de eind punten van het opslag account voor route ring

IoT Hub kunnen worden geconfigureerd voor het routeren van berichten naar een opslag account dat eigendom is van een klant. Als u wilt dat de routerings functionaliteit toegang krijgt tot een opslag account terwijl er firewall beperkingen zijn ingesteld, moet uw IoT Hub een beheerde service-identiteit hebben (Zie [een hub maken met een beheerde service-identiteit](#create-an-iot-hub-with-managed-service-identity)) voor meer informatie. Nadat een beheerde service-identiteit is ingericht, volgt u de onderstaande stappen om RBAC-machtigingen te geven aan de resource-identiteit van uw hub om toegang te krijgen tot uw opslag account.

1. Navigeer in het Azure Portal naar het tabblad **toegangs beheer (IAM)** van uw opslag account en klik op **toevoegen** onder de sectie **een roltoewijzing toevoegen** .

2. Selecteer **gegevens Inzender voor opslag-BLOB** als **rol**, **Azure AD-gebruiker,-groep of Service-Principal** als **toegangs toewijzing aan** en selecteer de resource naam van uw IOT hub in de vervolg keuzelijst. Klik op de knop **Opslaan**.

3. Ga naar het tabblad **firewalls en virtuele netwerken** in uw opslag account en schakel de optie **toegang via geselecteerde netwerken toestaan** in. Schakel onder de lijst **uitzonde ringen** het selectie vakje in voor **vertrouwde micro soft-Services toegang geven tot dit opslag account**. Klik op de knop **Opslaan**.

4. Ga op de pagina resource van uw IoT Hub naar het tabblad **bericht routering** .

5. Navigeer naar het gedeelte **aangepaste eind punten** en klik op **toevoegen**. Selecteer **opslag** als het type eind punt.

6. Op de pagina die wordt weer gegeven, geeft u een naam voor het eind punt op, selecteert u de container die u wilt gebruiken in uw Blob-opslag, geeft u code ring en indeling van de bestands naam op. Selecteer **systeem toegewezen** als **verificatie type** voor uw opslag eindpunt. Klik op de knop **Maken**.

Nu is uw aangepaste opslag eindpunt ingesteld voor het gebruik van de door het systeem toegewezen identiteit van uw hub en heeft deze toegang tot uw opslag Resource ondanks de firewall beperkingen. U kunt dit eind punt nu gebruiken om een routerings regel in te stellen.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Connectiviteit met Event hubs-eind punten voor route ring

IoT Hub kunnen worden geconfigureerd voor het routeren van berichten naar een event hubs-naam ruimte die eigendom is van een klant. Als u wilt dat de routerings functionaliteit toegang krijgt tot een event hubs-resource terwijl er firewall beperkingen zijn ingesteld, moet uw IoT Hub een beheerde service-identiteit hebben (Zie [een hub maken met een beheerde service-identiteit](#create-an-iot-hub-with-managed-service-identity)) voor meer informatie. Nadat een beheerde service-identiteit is ingericht, volgt u de onderstaande stappen om RBAC-machtigingen te geven aan de resource-identiteit van uw hub om toegang te krijgen tot uw event hubs.

1. Ga in het Azure Portal naar uw **iam-tabblad (Event hubs Access Control)** en klik op **toevoegen** onder de sectie **een roltoewijzing toevoegen** .

2. Selecteer **Event hubs gegevens afzender** als **rol**, **Azure AD-gebruiker,-groep of Service-Principal** als **toegangs toewijzing aan** en selecteer de resource naam van uw IOT hub in de vervolg keuzelijst. Klik op de knop **Opslaan**.

3. Ga naar het tabblad **firewalls en virtuele netwerken** in uw event hubs en schakel de optie **toegang via geselecteerde netwerken toestaan** in. Schakel onder de lijst **uitzonde ringen** het selectie vakje in om **vertrouwde micro soft-Services toegang te geven tot Event hubs**. Klik op de knop **Opslaan**.

4. Ga op de pagina resource van uw IoT Hub naar het tabblad **bericht routering** .

5. Navigeer naar het gedeelte **aangepaste eind punten** en klik op **toevoegen**. Selecteer **Event hubs** als het type eind punt.

6. Op de pagina die wordt weer gegeven, geeft u een naam op voor uw eind punt, selecteert u uw event hubs-naam ruimte en-exemplaar en klikt u op de knop **maken** .

Uw aangepaste Event hubs-eind punt is ingesteld voor het gebruik van de door het systeem toegewezen identiteit van uw hub en heeft toegang tot uw event hubs-resource ondanks de firewall beperkingen. U kunt dit eind punt nu gebruiken om een routerings regel in te stellen.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Connectiviteit met Service Bus-eind punten voor route ring

IoT Hub kunnen worden geconfigureerd voor het routeren van berichten naar een service bus-naam ruimte die eigendom is van een klant. Als u wilt dat de routerings functionaliteit toegang krijgt tot een service bus-resource terwijl er firewall beperkingen zijn ingesteld, moet uw IoT Hub een beheerde service-identiteit hebben (Zie [een hub maken met een beheerde service-identiteit](#create-an-iot-hub-with-managed-service-identity)) voor meer informatie. Nadat een beheerde service-identiteit is ingericht, volgt u de onderstaande stappen om RBAC-machtigingen te geven aan de resource-identiteit van uw hub om toegang te krijgen tot uw service bus.

1. Navigeer in het Azure Portal naar het tabblad **toegangs beheer (IAM)** van de service bus en klik op **toevoegen** onder de sectie **een roltoewijzing toevoegen** .

2. Selecteer **Service Bus-gegevens afzender** als **rol**, **Azure AD-gebruiker,-groep of Service-Principal** als **toegangs toewijzing aan** en selecteer de resource naam van uw IOT hub in de vervolg keuzelijst. Klik op de knop **Opslaan**.

3. Ga naar het tabblad **firewalls en virtuele netwerken** in uw service bus en schakel de optie **toegang via geselecteerde netwerken toestaan** in. Schakel onder de lijst **uitzonde ringen** het selectie vakje **vertrouwde micro soft-Services toestaan voor toegang tot deze service bus**in. Klik op de knop **Opslaan**.

4. Ga op de pagina resource van uw IoT Hub naar het tabblad **bericht routering** .

5. Navigeer naar het gedeelte **aangepaste eind punten** en klik op **toevoegen**. Selecteer **Service Bus-wachtrij** of **Service Bus-onderwerp** (indien van toepassing) als type eind punt.

6. Op de pagina die wordt weer gegeven, geeft u een naam op voor uw eind punt, selecteert u de naam ruimte van de service bus en de wachtrij of het onderwerp (indien van toepassing). Klik op de knop **Maken**.

Nu het aangepaste service bus-eind punt is ingesteld voor het gebruik van de door het systeem toegewezen identiteit van uw hub en de toegang heeft tot uw service bus-resource ondanks de firewall beperkingen. U kunt dit eind punt nu gebruiken om een routerings regel in te stellen.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>De connectiviteit met opslag accounts voor het uploaden van bestanden

Met de functie voor het uploaden van bestanden van IoT Hub kunnen apparaten bestanden uploaden naar een opslag account van de klant. Als u wilt toestaan dat het bestand kan worden geüpload, moeten zowel apparaten als IoT Hub verbinding hebben met het opslag account. Als er firewall beperkingen zijn ingesteld op het opslag account, moeten uw apparaten gebruikmaken van het mechanisme van het ondersteunde opslag account (inclusief [particuliere eind punten](../private-link/create-private-endpoint-storage-portal.md), [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) of [directe firewall configuratie](../storage/common/storage-network-security.md)) om verbinding te kunnen maken. Als er firewall beperkingen zijn ingesteld op het opslag account, moet IoT Hub worden geconfigureerd voor toegang tot de opslag bron via de uitzonde ring vertrouwde micro soft-Services. Voor dit doel moet uw IoT Hub een beheerde service-identiteit hebben (Zie How to [Create a hub with Managed Service Identity](#create-an-iot-hub-with-managed-service-identity)) (Engelstalig) voor meer informatie. Nadat een beheerde service-identiteit is ingericht, volgt u de onderstaande stappen om RBAC-machtigingen te geven aan de resource-identiteit van uw hub om toegang te krijgen tot uw opslag account.

1. Navigeer in het Azure Portal naar het tabblad **toegangs beheer (IAM)** van uw opslag account en klik op **toevoegen** onder de sectie **een roltoewijzing toevoegen** .

2. Selecteer **gegevens Inzender voor opslag-BLOB** als **rol**, **Azure AD-gebruiker,-groep of Service-Principal** als **toegangs toewijzing aan** en selecteer de resource naam van uw IOT hub in de vervolg keuzelijst. Klik op de knop **Opslaan**.

3. Ga naar het tabblad **firewalls en virtuele netwerken** in uw opslag account en schakel de optie **toegang via geselecteerde netwerken toestaan** in. Schakel onder de lijst **uitzonde ringen** het selectie vakje in voor **vertrouwde micro soft-Services toegang geven tot dit opslag account**. Klik op de knop **Opslaan**.

4. Ga op de pagina resource van uw IoT Hub naar het tabblad **bestand uploaden** .

5. Op de pagina die wordt weer gegeven, selecteert u de container die u in uw Blob-opslag wilt gebruiken, configureert u de **instellingen voor bestands meldingen**, **SAS TTL**, **standaard-TTL** en **maximum aantal levering** . Selecteer **systeem toegewezen** als **verificatie type** voor uw opslag eindpunt. Klik op de knop **Maken**.

Nu uw opslag eindpunt voor het uploaden van bestanden is ingesteld voor het gebruik van de door het systeem toegewezen identiteit van uw hub en de toegang heeft tot uw opslag Resource ondanks de firewall beperkingen.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>De connectiviteit met opslag accounts voor bulk import/export

IoT Hub ondersteunt de functionaliteit voor het [importeren/exporteren](./iot-hub-bulk-identity-mgmt.md) van informatie over apparaten in bulk van/naar een door de klant verschafte opslag-blob. Als u de functie voor bulk import/export wilt gebruiken, moeten zowel apparaten als IoT Hub verbinding hebben met het opslag account.

Deze functionaliteit vereist connectiviteit van IoT Hub naar het opslag account. Als u toegang wilt krijgen tot een service bus-resource terwijl er firewall beperkingen zijn ingesteld, moet uw IoT Hub een beheerde service-identiteit hebben (Zie [een hub maken met een beheerde service-identiteit](#create-an-iot-hub-with-managed-service-identity)) voor meer informatie. Nadat een beheerde service-identiteit is ingericht, volgt u de onderstaande stappen om RBAC-machtigingen te geven aan de resource-identiteit van uw hub om toegang te krijgen tot uw service bus.

1. Navigeer in het Azure Portal naar het tabblad **toegangs beheer (IAM)** van uw opslag account en klik op **toevoegen** onder de sectie **een roltoewijzing toevoegen** .

2. Selecteer **gegevens Inzender voor opslag-BLOB** als **rol**, **Azure AD-gebruiker,-groep of Service-Principal** als **toegangs toewijzing aan** en selecteer de resource naam van uw IOT hub in de vervolg keuzelijst. Klik op de knop **Opslaan**.

3. Ga naar het tabblad **firewalls en virtuele netwerken** in uw opslag account en schakel de optie **toegang via geselecteerde netwerken toestaan** in. Schakel onder de lijst **uitzonde ringen** het selectie vakje in voor **vertrouwde micro soft-Services toegang geven tot dit opslag account**. Klik op de knop **Opslaan**.

U kunt nu de Azure IoT-REST API gebruiken voor het [maken van import export-taken](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) voor informatie over het gebruik van de functie voor bulksgewijs importeren/exporteren. Houd er rekening mee dat u de `storageAuthenticationType="identityBased"` in de hoofd tekst van de aanvraag `inputBlobContainerUri="https://..."` moet `outputBlobContainerUri="https://..."` opgeven en dat u als de invoer-en uitvoer-URL van uw opslag account.


Azure IoT Hub SDK biedt ook ondersteuning voor deze functionaliteit in het register beheer van de service-client. Het volgende code fragment laat zien hoe u een import-of export taak initieert in met behulp van de C#-SDK.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


Voor het gebruik van deze regio-beperkte versie van de Azure IoT Sdk's met ondersteuning voor virtuele netwerken voor C#, Java en node. js:

1. Maak een omgevings variabele `EnableStorageIdentity` met de naam en stel `1`de waarde in op.

2. De SDK downloaden: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [node. js](https://aka.ms/vnetnodesdk)
 
Voor python downloadt u onze beperkte versie van GitHub.

1. Navigeer naar de [pagina release van github](https://aka.ms/vnetpythonsdk).

2. Down load het volgende bestand, dat u onder aan de pagina release onder de koptekst met de naam **assets**kunt vinden.
    > *azure_iot_hub-2.2.0_limited-py2. py3-none-any. WHL*

3. Open een Terminal en navigeer naar de map met het gedownloade bestand.

4. Voer de volgende opdracht uit om de python Service-SDK te installeren met ondersteuning voor virtuele netwerken:
    > PIP install./azure_iot_hub-2.2.0_limited-py2. py3-none-any. WHL


## <a name="next-steps"></a>Volgende stappen

Gebruik de onderstaande koppelingen voor meer informatie over IoT Hub-functies:

* [Berichtroutering](./iot-hub-devguide-messages-d2c.md)
* [Bestand uploaden](./iot-hub-devguide-file-upload.md)
* [Bulksgewijs importeren/exporteren van apparaten](./iot-hub-bulk-identity-mgmt.md) 
