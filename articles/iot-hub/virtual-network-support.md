---
title: Azure IoT Hub-ondersteuning voor virtuele netwerken
description: Het connectiviteitspatroon van virtuele netwerken gebruiken met IoT Hub
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
# <a name="iot-hub-support-for-virtual-networks"></a>IoT Hub-ondersteuning voor virtuele netwerken

In dit artikel wordt het VNET-connectiviteitspatroon geïntroduceerd en wordt uitgelegd hoe u een privé-connectiviteitservaring instellen voor een IoT-hub via een Azure VNET, die eigendom is van een klant.

> [!NOTE]
> De IoT Hub-functies die in dit artikel worden beschreven, zijn momenteel beschikbaar voor IoT-hubs die zijn [gemaakt met een beheerde service-identiteit](#create-an-iot-hub-with-managed-service-identity) in de volgende regio's: Oost-VS, Zuid-Centraal-VS en West-VS 2.


## <a name="introduction"></a>Inleiding

Standaard worden IoT Hub-hostnames toegewezen aan een openbaar eindpunt met een openbaar routeerbaar IP-adres via internet. Zoals in de onderstaande afbeelding wordt dit openbare eindpunt van IoT Hub gedeeld tussen hubs die eigendom zijn van verschillende klanten en toegankelijk zijn voor IoT-apparaten via netwerken in het brede gebied en on-premises netwerken.

Verschillende IoT Hub-functies, waaronder [berichtroutering,](./iot-hub-devguide-messages-d2c.md) [bestandsupload](./iot-hub-devguide-file-upload.md)en [het importeren/exporteren](./iot-hub-bulk-identity-mgmt.md) van bulkapparaten vereisen op dezelfde manier connectiviteit van IoT Hub met een Azure-bron die eigendom is van de klant via het openbare eindpunt. Zoals hieronder geïllustreerd, vormen deze connectiviteitspaden gezamenlijk het uitgaande verkeer van IoT Hub naar klantbronnen.
![Openbaar eindpunt van IoT Hub](./media/virtual-network-support/public-endpoint.png)


Om verschillende redenen willen klanten de connectiviteit met hun Azure-bronnen (inclusief IoT Hub) beperken via een VNET waarvan ze eigenaar zijn en die ze exploiteren. Deze redenen zijn onder meer:

* Maak gebruik van extra beveiligingslagen via isolatie op netwerkniveau voor uw IoT-hub door blootstelling aan connectiviteit aan uw hub via het openbare internet te voorkomen.

* Het inschakelen van een privé-connectiviteitservaring van uw on-premises netwerkassets die ervoor zorgen dat uw gegevens en verkeer rechtstreeks naar het Azure-backbonenetwerk worden verzonden.

* Het voorkomen van exfiltratie-aanvallen van gevoelige on-premises netwerken. 

* Het volgen van gevestigde Azure-brede connectiviteitspatronen met behulp van [privéeindpunten.](../private-link/private-endpoint-overview.md)


In dit artikel wordt beschreven hoe u deze doelen bereiken met behulp van [privéeindpunten](../private-link/private-endpoint-overview.md) voor ingress-connectiviteit met IoT Hub, zoals het gebruik van Azure trusted first party services uitzondering voor het uitdeinen van de connectiviteit van IoT Hub naar andere Azure-bronnen.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Ingress-connectiviteit met IoT Hub met privéeindpunten

Een privéeindpunt is een privé-IP-adres dat is toegewezen in een VNET in handen van een klant waarmee een Azure-bron bereikbaar is. Door een privéeindpunt voor uw IoT-hub te hebben, u services die binnen uw VNET werken, iot-hub bereiken zonder dat verkeer naar het openbare eindpunt van IoT Hub hoeft te worden verzonden. Op dezelfde manier kunnen apparaten die in uw on-premises [werken, Virtual Private Network (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) of [ExpressRoute](https://azure.microsoft.com/services/expressroute/) Private Peering gebruiken om verbinding te maken met uw VNET in Azure en vervolgens met uw IoT Hub (via het privéeindpunt). Als gevolg hiervan kunnen klanten die de connectiviteit met de openbare eindpunten van hun IoT-hub willen beperken (of deze mogelijk volledig blokkeren) dit doel bereiken door [iot-hubfirewallregels](./iot-hub-ip-filtering.md) te gebruiken met behoud van connectiviteit met hun Hub met behulp van het privéeindpunt.

> [!NOTE]
> De belangrijkste focus van deze setup is voor apparaten binnen een on-premises netwerk. Deze instelling wordt niet aangeraden voor apparaten die in een breed netwerk worden geïmplementeerd.

![Openbaar eindpunt van IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

Alvorens te gaan ervoor te zorgen dat aan de volgende voorwaarden wordt voldaan:

* Uw IoT-hub moet zijn ingericht met [een beheerde service-identiteit.](#create-an-iot-hub-with-managed-service-identity)

* Uw IoT-hub moet worden ingericht in een van de [ondersteunde regio's.](#regional-availability-private-endpoints)

* U hebt een Azure VNET ingericht met een subnet waarin het privéeindpunt wordt gemaakt. Zie [een virtueel netwerk maken met Azure CLI](../virtual-network/quick-create-cli.md) voor meer informatie.

* Voor apparaten die binnen on-premises netwerken werken, stelt u [VPN (Virtual Private Network)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) of [ExpressRoute](https://azure.microsoft.com/services/expressroute/) private peering in uw Azure VNET in.


### <a name="regional-availability-private-endpoints"></a>Regionale beschikbaarheid (privéeindpunten)

Privéeindpunten die worden ondersteund in IoT Hub's die zijn gemaakt in de volgende regio's:

* VS - oost

* VS - zuid-centraal

* VS - west 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Een privéeindpunt instellen voor IoT Hub-ingress

Voer de volgende stappen uit om een privéeindpunt in te stellen:

1. Voer de volgende opdracht Azure CLI uit om azure IoT Hub-provider opnieuw te registreren met uw abonnement:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Navigeer naar het tabblad **Privéeindpuntverbindingen** in uw IoT Hub-portal (dit tabblad is alleen beschikbaar **+** voor in IoT-hubs in de [ondersteunde regio's)](#regional-availability-private-endpoints)en klik op het bord om een nieuw privéeindpunt toe te voegen.

3. Geef het abonnement, de resourcegroep, de naam en het gebied op om het nieuwe privéeindpunt in te maken (idealiter moet privéeindpunt worden gemaakt in dezelfde regio als uw hub; zie [sectie regionale beschikbaarheid](#regional-availability-private-endpoints) voor meer details).

4. Klik op **Volgende: Resource**en geef het abonnement op uw IoT Hub-bron op en selecteer **'Microsoft.Devices/IotHubs'** als resourcetype, uw IoT Hub-naam als **bron**en **iotHub** als doelsubbron.

5. Klik op **Volgende: Configuratie** en geef uw virtuele netwerk en subnet om het privéeindpunt in te maken. Selecteer de optie om te integreren met azure private DNS-zone, indien gewenst.

6. Klik op **Volgende: Tags**en geef optioneel tags voor uw bron op.

7. Klik **op Controleren + maken** om uw privéeindpuntbron te maken.


### <a name="pricing-private-endpoints"></a>Prijzen (privéeindpunten)

Zie [Azure Private Link-prijzen](https://azure.microsoft.com/pricing/details/private-link)voor prijsdetails.


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Egress-connectiviteit van IoT-hub naar andere Azure-bronnen

IoT Hub heeft toegang nodig tot uw Azure blob-opslag, gebeurtenishubs, servicebusbronnen voor [berichtroutering,](./iot-hub-devguide-messages-d2c.md) [het uploaden](./iot-hub-devguide-file-upload.md)van bestanden en [het importeren/exporteren van bulkapparaten,](./iot-hub-bulk-identity-mgmt.md)wat meestal plaatsvindt via het openbare eindpunt van de resources. In het geval dat u uw opslagaccount, gebeurtenishubs of servicebusbron aan een VNET bindt, blokkeert de geadviseerde configuratie standaard de verbinding met de bron. Dit zal de functionaliteit van IoT Hub belemmeren die toegang tot deze bronnen vereist.

Om deze situatie te verlichten, moet u connectiviteit inschakelen van uw IoT Hub-bron naar uw opslagaccount, gebeurtenishubs of servicebusbronnen via de optie **vertrouwde services van Azure first party.**

De vereisten zijn als volgt:

* Uw IoT-hub moet worden ingericht in een van de [ondersteunde regio's.](#regional-availability-trusted-microsoft-first-party-services)

* Uw IoT-hub moet een beheerde service-identiteit krijgen toegewezen op het inrichten van hubs. Volg instructies voor het [maken van een hub met beheerde service-identiteit.](#create-an-iot-hub-with-managed-service-identity)


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Regionale beschikbaarheid (vertrouwde services van Microsoft first party)

Azure trusted first party services exception to bypass firewall restrictions to Azure storage, event hubs and service bus resources is only supported for IoT Hubs in the following regions: Azure trusted first party services exception to bypass firewall restrictions to Azure storage, event hubs and service bus resources is only supported for IoT Hubs in the following regions: Azure trusted first party services exception to bypass firewall restrictions to Azure storage, event hubs and service bus resources is only supported for IoT Hubs in the following regions: Azure trusted

* VS - oost

* VS - zuid-centraal

* VS - west 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Prijzen (vertrouwde Services van Microsoft first party)

Vertrouwde microsoft first party services uitzondering functie is gratis in IoT Hubs in de [ondersteunde regio's](#regional-availability-trusted-microsoft-first-party-services). Kosten voor de ingerichte opslagaccounts, gebeurtenishubs of servicebusbronnen zijn afzonderlijk van toepassing.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Een IoT-hub maken met een beheerde service-identiteit

Een beheerde service-identiteit kan worden toegewezen aan uw hub op tijd voor het inrichten van resources (deze functie wordt momenteel niet ondersteund voor bestaande hubs), waarvoor de IoT-hub TLS 1.2 als minimale versie moet gebruiken. Hiervoor moet u de arm-resourcesjabloon hieronder gebruiken:

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

Nadat u de waarden `name`voor `location` `SKU.name` uw `SKU.tier`resource hebt vervangen en u Azure CLI gebruiken om de bron in een bestaande resourcegroep te implementeren met behulp van:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Nadat de bron is gemaakt, u de beheerde service-identiteit die aan uw hub is toegewezen, ophalen met Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Zodra IoT Hub met een beheerde service-identiteit is ingericht, volgt u de bijbehorende sectie om routeringseindpunten in te stellen naar [opslagaccounts,](#egress-connectivity-to-storage-account-endpoints-for-routing) [gebeurtenishubs](#egress-connectivity-to-event-hubs-endpoints-for-routing)en [servicebusbronnen,](#egress-connectivity-to-service-bus-endpoints-for-routing) of om het [importeren/exporteren/exporteren van](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport) [bestanden](#egress-connectivity-to-storage-accounts-for-file-upload) en bulkapparaten te configureren.


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Egress-connectiviteit met opslagaccounteindpunten voor routering

IoT Hub kan worden geconfigureerd om berichten door te sturen naar een opslagaccount dat eigendom is van een klant. Om de routeringsfunctionaliteit toegang te geven tot een opslagaccount terwijl firewallbeperkingen zijn ingevoerd, moet uw IoT Hub een beheerde service-identiteit hebben (zie hoe [u een hub met beheerde service-identiteit maakt).](#create-an-iot-hub-with-managed-service-identity) Zodra een beheerde service-identiteit is ingericht, volgt u de onderstaande stappen om RBAC toestemming te geven voor de bronidentiteit van uw hub om toegang te krijgen tot uw opslagaccount.

1. Navigeer in de Azure-portal naar het tabblad **Toegangsbeheer (IAM) van** uw opslagaccount en klik op **Toevoegen** onder de sectie **Een roltoewijzing toevoegen.**

2. Selecteer **Opslagblobgegevensopdragers** als **rol**, **Azure AD-gebruiker, groep of serviceprincipal,** als **het toewijzen van toegang tot** en selecteer de bronnaam van uw IoT Hub in de vervolgkeuzelijst. Klik op de knop **Opslaan**.

3. Navigeer naar het tabblad **Firewalls en virtuele netwerken** in uw opslagaccount en schakel de optie **Toegang toestaan vanuit geselecteerde netwerken in.** Schakel onder de lijst **Uitzonderingen** het selectievakje **Vertrouwde Microsoft-services toestaan om toegang te krijgen tot dit opslagaccount**in. Klik op de knop **Opslaan**.

4. Navigeer op de resourcepagina van uw IoT Hub naar het tabblad **Berichtroutering.**

5. Navigeer naar de sectie **Aangepaste eindpunten** en klik op **Toevoegen**. Selecteer **Opslag** als eindpunttype.

6. Geef op de pagina die wordt weergegeven een naam op voor uw eindpunt en selecteer de container die u wilt gebruiken in uw blobopslag, geef codering en bestandsnaamindeling op. Selecteer **Systeem toegewezen** als het **verificatietype** aan uw opslageindpunt. Klik op de knop **Maken**.

Nu is uw aangepaste opslageindpunt ingesteld om de door uw hub toegewezen identiteit te gebruiken en heeft het toestemming om toegang te krijgen tot uw opslagbron, ondanks de firewallbeperkingen. U dit eindpunt nu gebruiken om een routeringsregel in te stellen.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Egress-connectiviteit met eindpunten voor gebeurtenishubs voor routering

IoT Hub kan worden geconfigureerd om berichten door te sturen naar de naamruimte van een klantbeheervan gebeurtenishubs. Als u wilt dat de routeringsfunctionaliteit toegang krijgt tot een bron voor gebeurtenishubs terwijl firewallbeperkingen zijn ingevoerd, moet uw IoT Hub een beheerde service-identiteit hebben (zie hoe [u een hub met beheerde service-identiteit maakt).](#create-an-iot-hub-with-managed-service-identity) Zodra een beheerde service-identiteit is ingericht, volgt u de onderstaande stappen om RBAC toestemming te geven voor de bronidentiteit van uw hub om toegang te krijgen tot uw gebeurtenishubs.

1. Navigeer in de Azure-portal naar het tabblad **Access Control (IAM)** naar uw gebeurtenishubs en klik op **Toevoegen** onder de sectie **Een roltoewijzing toevoegen.**

2. Selecteer **De afzender van gebeurtenishubsgegevens** als **rol**, **Azure AD-gebruiker, groep of serviceprincipal,** als **het toewijzen van toegang tot** en selecteer de bronnaam van uw IoT Hub in de vervolgkeuzelijst. Klik op de knop **Opslaan**.

3. Navigeer naar het tabblad **Firewalls en virtuele netwerken** in uw gebeurtenishubs en schakel de optie **Toegang toestaan vanuit geselecteerde netwerken in.** Schakel onder de lijst **Uitzonderingen** het selectievakje **Vertrouwde Microsoft-services toestaan toegang te krijgen tot gebeurtenishubs**. Klik op de knop **Opslaan**.

4. Navigeer op de resourcepagina van uw IoT Hub naar het tabblad **Berichtroutering.**

5. Navigeer naar de sectie **Aangepaste eindpunten** en klik op **Toevoegen**. Selecteer **Gebeurtenishubs** als eindpunttype.

6. Geef op de pagina die wordt weergegeven een naam op voor uw eindpunt, selecteer de naamruimte en instantie van uw gebeurtenishubs en klik op de knop **Maken.**

Nu is het eindpunt van uw aangepaste gebeurtenishubs ingesteld om de door uw hub toegewezen identiteit te gebruiken en heeft het toestemming om toegang te krijgen tot de bron van uw gebeurtenishubs, ondanks de firewallbeperkingen. U dit eindpunt nu gebruiken om een routeringsregel in te stellen.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Egress-connectiviteit met servicebuseindpunten voor routering

IoT Hub kan worden geconfigureerd om berichten door te sturen naar de naamruimte van een servicebus die eigendom is van de klant. Als u wilt dat de routeringsfunctionaliteit toegang krijgt tot een servicebusbron terwijl firewallbeperkingen zijn ingevoerd, moet uw IoT Hub een beheerde service-identiteit hebben (zie hoe [u een hub met beheerde service-identiteit maakt).](#create-an-iot-hub-with-managed-service-identity) Zodra een beheerde service-identiteit is ingericht, volgt u de onderstaande stappen om RBAC toestemming te geven voor de bronidentiteit van uw hub om toegang te krijgen tot uw servicebus.

1. Navigeer in de Azure-portal naar het tabblad **Toegangsbeheer (IAM) van** uw servicebus en **klik** onder de sectie Een **roltoewijzing toevoegen.**

2. Selecteer **Afzender servicebusgegevens** als **rol**, **Azure AD-gebruiker, groep of serviceprincipal,** als **het toewijzen van toegang tot** en selecteer de bronnaam van uw IoT Hub in de vervolgkeuzelijst. Klik op de knop **Opslaan**.

3. Navigeer naar het tabblad **Firewalls en virtuele netwerken** in uw servicebus en schakel de optie **Toegang toestaan vanuit geselecteerde netwerken in.** Schakel onder de lijst **Uitzonderingen** het selectievakje **Vertrouwde Microsoft-services toestaan om toegang te krijgen tot deze servicebus**in. Klik op de knop **Opslaan**.

4. Navigeer op de resourcepagina van uw IoT Hub naar het tabblad **Berichtroutering.**

5. Navigeer naar de sectie **Aangepaste eindpunten** en klik op **Toevoegen**. Selecteer **Servicebuswachtrij** of **Servicebusonderwerp** (indien van toepassing) als eindpunttype.

6. Geef op de pagina die wordt weergegeven een naam op voor uw eindpunt, selecteer de naamruimte van uw servicebus en wachtrij of onderwerp (indien van toepassing). Klik op de knop **Maken**.

Nu is uw aangepaste servicebuseindpunt ingesteld om de door uw hub toegewezen identiteit te gebruiken en heeft het toestemming om toegang te krijgen tot uw servicebusbron, ondanks de firewallbeperkingen. U dit eindpunt nu gebruiken om een routeringsregel in te stellen.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Egress-connectiviteit met opslagaccounts voor het uploaden van bestanden

Met de functie voor het uploaden van bestanden van IoT Hub kunnen apparaten bestanden uploaden naar een opslagaccount dat eigendom is van een klant. Om het uploaden van bestanden te laten functioneren, moeten zowel apparaten als IoT Hub verbinding hebben met het opslagaccount. Als er firewallbeperkingen zijn voor het opslagaccount, moeten uw apparaten een van het mechanisme van het ondersteunde opslagaccount (inclusief [privéeindpunten,](../private-link/create-private-endpoint-storage-portal.md) [serviceeindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) of [directe firewallconfiguratie)](../storage/common/storage-network-security.md)gebruiken om connectiviteit te verkrijgen. Als er firewallbeperkingen zijn voor het opslagaccount, moet IoT Hub ook worden geconfigureerd om toegang te krijgen tot de opslagbron via de uitzondering voor vertrouwde Microsoft-services. Hiervoor moet uw IoT Hub een beheerde service-identiteit hebben (zie hoe [u een hub met beheerde service-identiteit maakt).](#create-an-iot-hub-with-managed-service-identity) Zodra een beheerde service-identiteit is ingericht, volgt u de onderstaande stappen om RBAC toestemming te geven voor de bronidentiteit van uw hub om toegang te krijgen tot uw opslagaccount.

1. Navigeer in de Azure-portal naar het tabblad **Toegangsbeheer (IAM) van** uw opslagaccount en klik op **Toevoegen** onder de sectie **Een roltoewijzing toevoegen.**

2. Selecteer **Opslagblobgegevensopdragers** als **rol**, **Azure AD-gebruiker, groep of serviceprincipal,** als **het toewijzen van toegang tot** en selecteer de bronnaam van uw IoT Hub in de vervolgkeuzelijst. Klik op de knop **Opslaan**.

3. Navigeer naar het tabblad **Firewalls en virtuele netwerken** in uw opslagaccount en schakel de optie **Toegang toestaan vanuit geselecteerde netwerken in.** Schakel onder de lijst **Uitzonderingen** het selectievakje **Vertrouwde Microsoft-services toestaan om toegang te krijgen tot dit opslagaccount**in. Klik op de knop **Opslaan**.

4. Navigeer op de bronpagina van uw IoT Hub naar **het tabblad Uploaden van bestanden.**

5. Selecteer op de pagina die wordt weergegeven de container die u wilt gebruiken in uw blob-opslag, configureer de **instellingen voor bestandsmeldingen**, **SAS TTL,** **StandaardTTL** en **Maximaal aantal leveringen** zoals gewenst. Selecteer **Systeem toegewezen** als het **verificatietype** aan uw opslageindpunt. Klik op de knop **Maken**.

Nu is uw opslageindpunt voor het uploaden van bestanden ingesteld om de door uw hub toegewezen identiteit te gebruiken en heeft het toestemming om toegang te krijgen tot uw opslagbron, ondanks de firewallbeperkingen.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Egress-connectiviteit met opslagaccounts voor het importeren/exporteren van bulkapparaten

IoT Hub ondersteunt de functionaliteit voor [het importeren/exporteren](./iot-hub-bulk-identity-mgmt.md) van informatie van apparaten in bulk van/naar een door de klant geleverde opslagblob. Om de functie voor bulkimport/export te laten functioneren, moeten zowel apparaten als IoT Hub verbinding hebben met het opslagaccount.

Deze functionaliteit vereist connectiviteit van IoT Hub naar het opslagaccount. Als u toegang wilt krijgen tot een servicebusbron terwijl firewallbeperkingen zijn ingevoerd, moet uw IoT Hub een beheerde service-identiteit hebben (zie hoe [u een hub met beheerde service-identiteit maakt).](#create-an-iot-hub-with-managed-service-identity) Zodra een beheerde service-identiteit is ingericht, volgt u de onderstaande stappen om RBAC toestemming te geven voor de bronidentiteit van uw hub om toegang te krijgen tot uw servicebus.

1. Navigeer in de Azure-portal naar het tabblad **Toegangsbeheer (IAM) van** uw opslagaccount en klik op **Toevoegen** onder de sectie **Een roltoewijzing toevoegen.**

2. Selecteer **Opslagblobgegevensopdragers** als **rol**, **Azure AD-gebruiker, groep of serviceprincipal,** als **het toewijzen van toegang tot** en selecteer de bronnaam van uw IoT Hub in de vervolgkeuzelijst. Klik op de knop **Opslaan**.

3. Navigeer naar het tabblad **Firewalls en virtuele netwerken** in uw opslagaccount en schakel de optie **Toegang toestaan vanuit geselecteerde netwerken in.** Schakel onder de lijst **Uitzonderingen** het selectievakje **Vertrouwde Microsoft-services toestaan om toegang te krijgen tot dit opslagaccount**in. Klik op de knop **Opslaan**.

U nu de Azure IoT REST API's gebruiken voor [het maken van importexporttaken](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) voor informatie over het gebruik van de bulkimport/exportfunctionaliteit. Houd er rekening mee `storageAuthenticationType="identityBased"` dat u de `inputBlobContainerUri="https://..."` in `outputBlobContainerUri="https://..."` uw aanvraaglichaam moet opgeven en het gebruik en het gebruik en de url's van respectievelijk uw opslagaccount invoeren en uitvoer.


Azure IoT Hub SDK's ondersteunen deze functionaliteit ook in de registermanager van de serviceclient. In het volgende codefragment ziet u hoe u een importtaak initieert of een taak exporteert in het gebruik van de C# SDK.

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


Deze regiobeperkte versie van de Azure IoT SDK's gebruiken met virtuele netwerkondersteuning voor C#, Java en Node.js:

1. Maak een omgevingsvariabele met de naam `EnableStorageIdentity` en stel de waarde ervan in op `1`.

2. Download de SDK: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Download voor Python onze beperkte versie van GitHub.

1. Navigeer naar de [github-releasepagina](https://aka.ms/vnetpythonsdk).

2. Download het volgende bestand, dat u onderaan de releasepagina vindt onder de koptekst met de naam **assets**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-any-any.whl*

3. Open een terminal en navigeer naar de map met het gedownloade bestand.

4. Voer de volgende opdracht uit om de Python Service SDK te installeren met ondersteuning voor virtuele netwerken:
    > pip installeren ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Volgende stappen

Gebruik de onderstaande koppelingen voor meer informatie over IoT Hub-functies:

* [Berichtroutering](./iot-hub-devguide-messages-d2c.md)
* [Bestand uploaden](./iot-hub-devguide-file-upload.md)
* [Importeren/exporteren van bulkapparaten](./iot-hub-bulk-identity-mgmt.md) 
