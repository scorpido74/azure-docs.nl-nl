---
title: Azure Monitor door de klant beheerde sleutel
description: Informatie en stappen voor het configureren van door de klant beheerde sleutel (CMK) voor het versleutelen van gegevens in uw Log Analytics-werk ruimten met behulp van een Azure Key Vault sleutel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 06/11/2020
ms.openlocfilehash: 6e3a4b61c86d476a9e5c5a0392c51a72f06f048d
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84761328"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor door de klant beheerde sleutel 

Dit artikel bevat achtergrond informatie en stappen voor het configureren van door de klant beheerde sleutels (CMK) voor uw Log Analytics-werk ruimten. Eenmaal geconfigureerd, worden alle gegevens die naar uw werk ruimten worden verzonden, versleuteld met uw Azure Key Vault sleutel.

U wordt aangeraden [beperkingen en beperkingen](#limitationsandconstraints) hieronder vóór de configuratie te bekijken.

## <a name="customer-managed-key-cmk-overview"></a>Overzicht van door de klant beheerde sleutel (CMK)

[Versleuteling bij rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)   is een veelvoorkomende privacy-en beveiligings vereiste in organisaties.U kunt de versleuteling op de rest van Azure volledig beheren, terwijl u verschillende opties hebt om versleutelings-of versleutelings sleutels nauw keurig te beheren.

Azure Monitor zorgt ervoor dat alle gegevens in rust worden versleuteld met door Azure beheerde sleutels.Azure Monitor biedt ook een optie voor gegevens versleuteling met behulp van uw eigen sleutel die is opgeslagen in uw [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)   en toegankelijk is via opslag met door het systeem toegewezen [beheerde identiteits](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)   verificatie.Deze sleutel kan [software of hardware-HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview)zijn die is beveiligd.

Azure Monitor versleuteling is hetzelfde als de manier waarop [Azure Storage versleuteling](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)   werkt.

Met CMK kunt u de toegang tot uw gegevens beheren en deze op elk gewenst moment intrekken. Azure Monitor Storage respecteert altijd wijzigingen in de sleutel machtigingen binnen een uur. De gegevens die in de afgelopen 14 dagen zijn opgenomen, worden ook opgeslagen in de Hot-cache (met SSD-back-ups) voor een efficiënte query-engine bewerking. Deze gegevens blijven versleuteld met micro soft-sleutels, ongeacht de CMK-configuratie, maar uw controle over SSD-gegevens voldoet aan de [sleutel intrekking](#cmk-kek-revocation). Er wordt gewerkt aan SSD-gegevens die zijn versleuteld met CMK in de tweede helft van 2020.

De CMK-mogelijkheid wordt geleverd op toegewezen Log Analytics clusters. Om te controleren of we de vereiste capaciteit hebben in uw regio, moet u ervoor zorgen dat uw abonnement vooraf is toegestaan. Gebruik uw micro soft-contact persoon om uw abonnement op te halen voordat u begint met het configureren van CMK.

Het [prijs model van log Analytics clusters](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)   maakt gebruik van capaciteits reserveringen vanaf een niveau van 1000 GB per dag.

## <a name="how-cmk-works-in-azure-monitor"></a>Hoe CMK werkt in Azure Monitor

Azure Monitor maakt gebruik van door het systeem toegewezen beheerde identiteit om toegang tot uw Azure Key Vault te verlenen. Door het systeem toegewezen beheerde identiteit kan alleen worden gekoppeld aan één Azure-resource, terwijl de identiteit van het Log Analytics cluster wordt ondersteund op cluster niveau. Dit bepaalt of de CMK-mogelijkheid wordt geleverd op een toegewezen Log Analytics cluster. Ter ondersteuning van CMK op meerdere werk ruimten, een nieuwe Log Analytics *cluster* resource wordt uitgevoerd als een tussenliggende identiteits verbinding tussen uw Key Vault en uw log Analytics-werk ruimten. De Log Analytics cluster opslag maakt gebruik van de beheerde identiteit die \' aan de *cluster* bron is gekoppeld om de Azure Key Vault via Azure Active Directory te verifiëren. 

Na de configuratie van CMK worden gegevens die zijn opgenomen in werk ruimten die zijn gekoppeld aan uw *cluster* bron, versleuteld met uw sleutel in Key Vault. U kunt werk ruimten op elk gewenst moment loskoppelen van de *cluster* resource. Nieuwe gegevens worden opgenomen in Log Analytics opslag en versleuteld met de micro soft-sleutel, terwijl u uw nieuwe en oude gegevens naadloos kunt opvragen.


![Overzicht van CMK](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics *cluster* bron met beheerde identiteit met machtigingen voor Key Vault--de identiteit wordt door gegeven aan de toegewezen log Analytics-cluster opslag van aan
3. Toegewezen Log Analytics cluster
4. Werk ruimten die zijn gekoppeld aan *cluster* bron voor CMK-versleuteling

## <a name="encryption-keys-operation"></a>Versleutelings sleutel bewerking

Er zijn drie soorten sleutels betrokken bij het versleutelen van opslag gegevens:

- **Kek** -sleutel versleutelings sleutel (CMK)
- **AEK** -account versleutelings sleutel
- **Dek** -gegevens versleutelings sleutel

De volgende regels zijn van toepassing:

- De Log Analytics cluster-opslag accounts genereren een unieke versleutelings sleutel voor elk opslag account, dat wordt aangeduid als de AEK.

- De AEK wordt gebruikt om DEKs af te leiden. Dit zijn de sleutels die worden gebruikt voor het versleutelen van elk gegevens blok dat naar de schijf wordt geschreven.

- Wanneer u de sleutel in Key Vault configureert en ernaar verwijst in de *cluster* bron, verzendt de Azure Storage aanvragen naar uw Azure Key Vault om de AEK in te pakken en het decoderen van de gegevens voor het versleutelen en ontsleutelen van bewerkingen uit te voeren.

- Uw KEK verlaat uw Key Vault nooit en in het geval van een HSM-sleutel verlaat het nooit de hardware.

- Azure Storage gebruikt de beheerde identiteit die is gekoppeld aan de *cluster* bron om te verifiëren en toegang te krijgen tot Azure Key Vault via Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>CMK-inrichtings procedure

1. Abonnement toestaan: de CMK-mogelijkheid wordt geleverd op toegewezen Log Analytics clusters. Om te controleren of we de vereiste capaciteit hebben in uw regio, moet u ervoor zorgen dat uw abonnement vooraf is toegestaan. Gebruik uw micro soft-contact persoon om uw abonnement te laten zijn toegestaan.
2. Azure Key Vault maken en de sleutel opslaan
3. Een *cluster* bron maken
4. Machtigingen verlenen aan uw Key Vault
5. Log Analytics werk ruimten koppelen

De procedure wordt niet ondersteund in Azure Portal en het inrichten wordt uitgevoerd via Power shell of REST-aanvragen.

> [!IMPORTANT]
> Alle REST-aanvragen moeten een Bearer-autorisatie token in de aanvraag header bevatten.

Bijvoorbeeld:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Waarbij *eyJ0eXAiO....* het volledige verificatie token vertegenwoordigt. 

U kunt het token verkrijgen met een van de volgende methoden:

1. Gebruik [app-registraties](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) methode.
2. In de Azure Portal
    1. Navigeer naar Azure Portal in het hulp programma voor ontwikkel aars (F12)
    1. Zoek naar een autorisatie reeks onder ' aanvraag headers ' in een van de exemplaren van ' batch? API-Version '. Het ziet er als volgt uit: ' autorisatie: Bearer eyJ0eXAiO.... '. 
    1. Kopieer en voeg deze toe aan uw API-oproep volgens de onderstaande voor beelden.
3. Navigeer naar de site Azure REST-documentatie. Druk op ' Probeer het ' op een API en kopieer het Bearer-token.

### <a name="asynchronous-operations-and-status-check"></a>Asynchrone bewerkingen en status controle

Sommige bewerkingen in deze configuratie procedure worden asynchroon uitgevoerd, omdat deze niet snel kunnen worden voltooid. Wanneer REST-aanvragen in de configuratie worden gebruikt, retourneert de reactie in eerste instantie een HTTP-status code 200 (OK) en koptekst met de eigenschap *Azure-AsyncOperation* wanneer deze wordt geaccepteerd:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Vervolgens kunt u de status van de asynchrone bewerking controleren door een GET-aanvraag te verzenden naar de waarde van de *Azure-AsyncOperation-* header:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

Het antwoord bevat informatie over de bewerking en de *status*ervan. Dit kan een van de volgende zijn:

Bewerking wordt uitgevoerd
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

De update bewerking voor de sleutel-id wordt uitgevoerd
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

*Cluster* bron verwijderen wordt uitgevoerd: wanneer u een *cluster* resource verwijdert die werk ruimten aan werk ruimten heeft gekoppeld, wordt er een ontkoppelings bewerking uitgevoerd voor elk van de werk ruimten in asynchrone bewerkingen die even kunnen duren.
Dit is niet relevant wanneer u een *cluster* verwijdert zonder gekoppelde werk ruimte. in dit geval wordt de *cluster* bron onmiddellijk verwijderd.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Bewerking is voltooid
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Bewerking is mislukt
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>Het abonnement voor de implementatie van CMK toestaan

De CMK-mogelijkheid wordt geleverd op toegewezen Log Analytics clusters.Om te controleren of we de vereiste capaciteit hebben in uw regio, moet u ervoor zorgen dat uw abonnement vooraf is toegestaan. Gebruik uw contact personen in micro soft om uw abonnementen-Id's op te geven.

> [!IMPORTANT]
> De functionaliteit van CMK is regionaal. Uw Azure Key Vault, *cluster* bron en gekoppelde log Analytics-werk ruimten moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

### <a name="storing-encryption-key-kek"></a>Versleutelings sleutel opslaan (KEK)

Maak of gebruik een Azure Key Vault die u al moet genereren of importeer een sleutel die moet worden gebruikt voor gegevens versleuteling. De Azure Key Vault moet worden geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw gegevens in Azure Monitor te beveiligen. U kunt deze configuratie controleren onder eigenschappen in uw Key Vault, de beveiliging van zowel *zacht verwijderen* als *leegmaken* moet zijn ingeschakeld.

![Zacht verwijderen en beveiligings instellingen opschonen](media/customer-managed-keys/soft-purge-protection.png)

Deze instellingen zijn beschikbaar via CLI en Power shell:
- [Voorlopig verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Beveiligings](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) beveiligingen verwijderen tegen het verwijderen van het geheim of de kluis, zelfs na het zacht verwijderen

### <a name="create-cluster-resource"></a>*Cluster* bron maken

Deze resource wordt gebruikt als een tussenliggende identiteits verbinding tussen uw Key Vault en uw Log Analytics-werk ruimten. Nadat u een bevestiging hebt ontvangen dat uw abonnementen zijn toegestaan, maakt u een Log Analytics *cluster* resource in de regio waar uw werk ruimten zich bevinden.

U moet het *capaciteits reserverings* niveau (SKU) opgeven bij het maken van een *cluster* bron. Het *capaciteits reserverings* niveau kan binnen het bereik van 1.000 tot 2.000 GB per dag liggen en kan worden bijgewerkt in stappen van 100 later. Als u een capaciteits reserverings niveau nodig hebt dat hoger is dan 2.000 GB per dag, neemt u contact met ons op LAIngestionRate@microsoft.com . [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)

De eigenschap *billingType* bepaalt het facturerings kenmerk voor de *cluster* bron en de bijbehorende gegevens:
- *Cluster* (standaard): de kosten voor de capaciteits reservering voor uw cluster worden toegeschreven aan de *cluster* bron.
- *Werk ruimten* : de kosten voor de capaciteits reservering voor uw cluster worden proportioneel toegeschreven aan de werk ruimten in het cluster, waarbij een deel *van het gebruik* wordt gefactureerd als de totale opgenomen gegevens voor de dag onder de capaciteits reservering vallen. Zie [log Analytics toegewezen clusters](manage-cost-storage.md#log-analytics-dedicated-clusters) voor meer informatie over het prijs model van het cluster. 

> [!NOTE]
> * Nadat u de *cluster* bron hebt gemaakt, kunt u deze bijwerken met *SKU*, *keyVaultProperties* of *BILLINGTYPE* met behulp van een patch rest-aanvraag.
> * U kunt *billingType* op dit moment bijwerken met behulp van rest-aanvraag. dit wordt niet ondersteund in Power shell

Deze bewerking is asynchroon en kan enige tijd worden voltooid.

> [!IMPORTANT]
> Kopieer het antwoord en sla het op, omdat u de details in de volgende stappen nodig hebt.
> 
**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} 
```

**REST**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

De identiteit wordt toegewezen aan de *cluster* bron op het moment van aanmaak.

**Beantwoord**

200 OK en koptekst.

Hoewel het inrichten van het Log Analytics cluster a is voltooid, kunt u de inrichtings status op twee manieren controleren:

1. Kopieer de URL-waarde voor Azure-AsyncOperation uit het antwoord en volg de controle van de [asynchrone bewerkings status](#asynchronous-operations-and-status-check).
2. Verzend een GET-aanvraag op de *cluster* resource en kijk naar de waarde *provisioningState* . Het is *ProvisioningAccount* tijdens het *inrichten en voltooid wanneer het is voltooid* .

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Beantwoord**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "provisioningState": "ProvisioningAccount",
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

De GUID ' principalId ' wordt gegenereerd door de beheerde identiteits service voor de *cluster* bron.

### <a name="grant-key-vault-permissions"></a>Key Vault machtigingen verlenen

Werk uw Key Vault bij met een nieuw toegangs beleid om machtigingen te verlenen aan uw *cluster* bron. Deze machtigingen worden gebruikt door de aan Azure Monitor opslag voor gegevens versleuteling. Open uw Key Vault in Azure Portal en klik vervolgens op toegangs beleid en vervolgens op toegangs beleid toevoegen om een beleid te maken met de volgende instellingen:

- Belang rijke machtigingen: Selecteer Get, terugloop sleutel en de machtigingen voor de uitpakken sleutel.
- Selecteer Principal: Voer de naam van de *cluster* resource of de principal-id in die in de vorige stap is geretourneerd.

![Key Vault machtigingen verlenen](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

De machtiging *Get* is vereist om te controleren of uw Key Vault is geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw Azure monitor gegevens te beveiligen.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Cluster bron bijwerken met sleutel-id Details

Deze stap wordt uitgevoerd tijdens de eerste en in toekomstige belang rijke versie-updates in uw Key Vault. Het informeert Azure Monitor opslag over de sleutel versie die moet worden gebruikt voor gegevens versleuteling. Wanneer u de nieuwe sleutel bijwerkt, wordt deze gebruikt om de opslag sleutel (AEK) in te pakken en in te pakken.

Als u de *cluster* bron met de details van de Key Vault *sleutel-id* wilt bijwerken, selecteert u de huidige versie van de sleutel in azure Key Vault om de details van de sleutel-id op te halen.

![Key Vault machtigingen verlenen](media/customer-managed-keys/key-identifier-8bit.png)

Werk de *cluster* bron-KeyVaultProperties bij met sleutel-id-Details.

Deze bewerking is asynchroon wanneer de sleutel-id-Details worden bijgewerkt en kan enige tijd duren. Het is synchroon bij het bijwerken van de capaciteits waarde.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> U kunt de *cluster* resource- *SKU*, *keyVaultProperties* of *billingType* bijwerken met behulp van patch.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

' KeyVaultProperties ' bevat de details van de Key Vault sleutel-id.

**Beantwoord**

200 OK en koptekst.
Het duurt de doorgifte van de sleutel-id enkele minuten om te volt ooien. U kunt de status van de update op twee manieren controleren:
1. Kopieer de URL-waarde voor Azure-AsyncOperation uit het antwoord en volg de controle van de [asynchrone bewerkings status](#asynchronous-operations-and-status-check).
2. Verzend een GET-aanvraag voor de *cluster* bron en Bekijk de *KeyVaultProperties* -eigenschappen. Uw recent bijgewerkte sleutel-id-details moeten in het antwoord worden geretourneerd.

Een antwoord op de GET-aanvraag voor de *cluster* bron moet er als volgt uitzien wanneer de sleutel-id-Update is voltooid:

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Werkruimte koppeling met *cluster* resource

U moet schrijf machtigingen hebben voor uw werk ruimte en *cluster* resource om deze bewerking uit te voeren, waaronder de volgende acties:

- In de werk ruimte: micro soft. OperationalInsights/werk ruimten/schrijven
- In *cluster* resource: micro soft. OperationalInsights/clusters/schrijven

> [!IMPORTANT]
> Deze stap moet pas worden uitgevoerd nadat de inrichting van het Log Analytics cluster is voltooid. Als u werk ruimten en opname gegevens koppelt vóór het inrichten, worden opgenomen gegevens verwijderd en kunnen deze niet worden hersteld.

Deze bewerking is asynchroon en kan enige tijd worden voltooid.

**PowerShell**

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

**REST**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Beantwoord**

200 OK en koptekst.

Opgenomen gegevens worden tijdens de koppelings bewerking opgeslagen die zijn versleuteld met uw beheerde sleutel. Dit kan tot 90 minuten duren. U kunt de status van de werkruimte koppeling op twee manieren controleren:

1. Kopieer de URL-waarde voor Azure-AsyncOperation uit het antwoord en volg de controle van de [asynchrone bewerkings status](#asynchronous-operations-and-status-check).
2. Een [werk ruimte verzenden: aanvraag ophalen](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) en het antwoord bekijken, de gekoppelde werk ruimte heeft een clusterResourceId onder "functies".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Beantwoord**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="cmk-kek-revocation"></a>Intrekken van CMK (KEK)

U kunt de toegang tot gegevens intrekken door de sleutel uit te scha kelen of door het toegangs beleid van het *cluster* bron in uw Key Vault te verwijderen. De Log Analytics-cluster opslag respecteert altijd wijzigingen in de sleutel machtigingen binnen een uur of eerder, en de opslag wordt niet meer beschikbaar. Nieuwe gegevens die zijn opgenomen in werk ruimten die zijn gekoppeld aan uw *cluster*   bron, worden verwijderd en kunnen niet worden hersteld, gegevens zijn niet toegankelijk en query's naar deze werk ruimten mislukken. Eerder opgenomen gegevens blijven in de opslag, zolang uw *cluster* bron en uw werk ruimten niet worden verwijderd. Ontoegankelijke gegevens zijn onderworpen aan het Bewaar beleid voor gegevens en worden verwijderd wanneer de Bewaar termijn wordt bereikt. 

Opgenomen gegevens in de afgelopen 14 dagen worden ook opgeslagen in een hot-cache (met SSD-back-ups) voor een efficiënte bewerking van query-engine. Dit wordt verwijderd bij het verwijderen van de sleutel en wordt ook niet toegankelijk.

Met opslag worden uw Key Vault periodiek gecontroleerd om te proberen om de versleutelings sleutel op te slaan en na het openen van de gegevens en het hervatten van query's binnen 30 minuten.

## <a name="cmk-kek-rotation"></a>CMK (KEK)

Voor de draaiing van CMK is een expliciete update van de *cluster* bron vereist met de nieuwe sleutel versie in azure Key Vault. Volg de instructies in de stap *cluster* resource bijwerken met sleutel-id Details. Als u de nieuwe sleutel-id-Details in de *cluster* bron niet bijwerkt, blijft de log Analytics cluster opslag uw vorige sleutel gebruiken voor versleuteling. Als u de oude sleutel uitschakelt of verwijdert voordat u de nieuwe sleutel in de *cluster* bron bijwerkt, krijgt u een [belang rijke intrekkings](#cmk-kek-revocation) status.

Al uw gegevens blijven toegankelijk na de bewerking voor het wijzigen van de sleutel, omdat gegevens altijd worden versleuteld met de account versleutelings sleutel (AEK) terwijl AEK nu wordt versleuteld met uw nieuwe Key Encryption Key (KEK)-versie in Key Vault.

## <a name="cmk-manage"></a>CMK beheren

- **Alle *cluster* resources voor een resource groep ophalen**
  
  **PowerShell**

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name}
  ```

  **REST**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Beantwoord**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- **Alle *cluster* resources voor een abonnement ophalen**
  
  **PowerShell**

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  **REST**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Beantwoord**
    
  Hetzelfde antwoord als voor*cluster* resources voor een resource groep, maar in het abonnements bereik.

- ***Capaciteits reservering* in *cluster* bron bijwerken**

  Wanneer het gegevens volume naar uw gekoppelde werk ruimten in de loop van de tijd verandert en u het capaciteits reserverings niveau op de juiste wijze wilt bijwerken. Volg de [Update *cluster* bron](#update-cluster-resource-with-key-identifier-details) en geef uw nieuwe capaciteits waarde op. Dit kan binnen het bereik van 1.000 tot 2.000 GB per dag zijn en in stappen van 100. Voor een niveau hoger dan 2.000 GB per dag, bereikt u uw micro soft-contact persoon om dit in te scha kelen. Houd er rekening mee dat u de volledige REST-aanvraag tekst niet hoeft op te geven en moet de SKU bevatten:

  **PowerShell**

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -SkuCapacity {daily-ingestion-gigabyte}
  ```

  **REST**
   
  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- ***BillingType* in *cluster* bron bijwerken**

  De eigenschap *billingType* bepaalt het facturerings kenmerk voor de *cluster* bron en de bijbehorende gegevens:
  - *cluster* (standaard): de facturering wordt toegeschreven aan het abonnement dat als host fungeert voor uw cluster bron
  - *werk ruimten* : de facturering wordt toegeschreven aan de abonnementen die proportioneel als host fungeren voor uw werk ruimten
  
  Volg de [Update *cluster* resource](#update-cluster-resource-with-key-identifier-details) en geef de nieuwe billingType-waarde op. Houd er rekening mee dat u de volledige REST-aanvraag tekst niet hoeft op te geven en moet de *billingType*bevatten:

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Werk ruimte loskoppelen**

  U moet schrijf machtigingen hebben voor de werk ruimte en *cluster* resource om deze bewerking uit te voeren. U kunt een werk ruimte op elk gewenst moment loskoppelen van uw *cluster* bron. Nieuwe opgenomen gegevens na de ontkoppelings bewerking worden opgeslagen in Log Analytics opslag en versleuteld met de micro soft-sleutel. U kunt een query uitvoeren op gegevens die zijn opgenomen in uw werk ruimte vóór en na de loskoppeling, zolang de *cluster* bron is ingericht en geconfigureerd met een geldige Key Vault sleutel.

  Deze bewerking is asynchroon en kan enige tijd worden voltooid.

  **PowerShell**

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -Name {workspace-name} -LinkedServiceName cluster
  ```

  **REST**

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Beantwoord**

  200 OK en koptekst.

  Opgenomen gegevens na de ontkoppelings bewerking worden opgeslagen in Log Analytics opslag. Dit kan 90 minuten duren. U kunt de status van de werk ruimte-koppeling op twee manieren controleren:

  1. Kopieer de URL-waarde voor Azure-AsyncOperation uit het antwoord en volg de controle van de [asynchrone bewerkings status](#asynchronous-operations-and-status-check).
  2. Een [werk ruimte verzenden: aanvraag ophalen](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) en het antwoord bekijken, de niet-gekoppelde werk ruimte heeft niet de *clusterResourceId* onder *functies*.

- **Status van werkruimte koppeling controleren** Een Get-bewerking uitvoeren op de werk ruimte en controleren of *clusterId* aanwezig is in de reactie. De bijbehorende werk ruimte heeft de eigenschap *clusterId* .

  **PowerShell**

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName {resource-group-name} -Name {workspace-name}
  ```

- **De *cluster* bron verwijderen**

  U hebt schrijf machtigingen voor de *cluster* bron nodig om deze bewerking uit te voeren. Er wordt een bewerking voor zacht verwijderen uitgevoerd om het herstellen van uw *cluster* bron met gegevens binnen 14 dagen mogelijk te maken, of het verwijderen per ongeluk of opzettelijk is geslaagd. De *cluster* bron naam blijft gereserveerd tijdens de tijdelijke periode en u kunt geen nieuw cluster met die naam maken. Na de periode van de tijdelijke verwijdering wordt de *cluster* bron naam vrijgegeven, worden uw *cluster* bron en-gegevens permanent verwijderd en kunnen ze niet worden hersteld. De gekoppelde werk ruimte wordt ontkoppeling van de *cluster* resource bij het verwijderen. Nieuwe opgenomen gegevens worden opgeslagen in Log Analytics Storage en versleuteld met de micro soft-sleutel. 
  
  De bewerking van de werk ruimten is asynchroon en kan tot 90 minuten duren.

  **PowerShell**

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}
  ```

  **REST**
  
  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Beantwoord**

  200 OK

- **Uw *cluster* bron en uw gegevens herstellen** 
  
  Een *cluster* bron die in de afgelopen 14 dagen is verwijderd, is in de status voorlopig verwijderen en kan worden hersteld met de bijbehorende gegevens. Omdat alle werk ruimten die zijn ontkoppeld *Cluster* van de cluster *resource,* worden verwijderd, moet u uw werk ruimten na het herstel voor CMK-versleuteling opnieuw koppelen. De herstel bewerking wordt momenteel hand matig uitgevoerd door de product groep. Gebruik uw micro soft-kanaal voor herstel aanvragen.

## <a name="limitationsandconstraints"></a>Beperkingen en beperkingen

-De CMK wordt ondersteund op toegewezen Log Analytics cluster en is geschikt voor klanten die 1 TB per dag of langer verzenden.

-Het maximum aantal *cluster*   resources per regio en abonnement is 2

-U kunt een werk ruimte aan uw *cluster*   bron koppelen en deze vervolgens loskoppelen als CMK niet vereist is voor de werk ruimte.Het aantal werkruimte koppelingen op een bepaalde werk ruimte in een periode van 30 dagen is beperkt tot 2

-Werkruimte koppeling met *cluster*   bron moet alleen worden uitgevoerd nadat u hebt gecontroleerd of de inrichting van het log Analytics cluster is voltooid.Gegevens die vóór de voltooiing naar uw werk ruimte worden verzonden, worden verwijderd en kunnen niet worden hersteld.

-CMK-versleuteling is van toepassing op nieuwe opgenomen gegevens na de CMK-      configuratie.Gegevens die zijn opgenomen vóór de CMK      -Configuratie, blijven versleuteld met micro soft-sleutel.U kunt een query uitvoeren op gegevens die zijn opgenomen      voor en na de CMK-configuratie naadloos.

-De Azure Key Vault moet worden geconfigureerd als herstelbaar.Deze eigenschappen zijn niet standaard ingeschakeld en moeten worden geconfigureerd met CLI of Power shell:

  - [Voorlopig verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 
      moet zijn ingeschakeld    - om de beveiliging op te  [schonen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)moet   worden ingeschakeld om te beschermen tegen het verwijderen van het geheim of de kluis, zelfs na het verwijderen van de software.

- *Cluster*   resource verplaatsing naar een andere resource groep of een ander abonnement      wordt momenteel niet ondersteund.

-Uw Azure Key Vault, *cluster*   resource en gekoppelde werk ruimten moeten zich in dezelfde regio en in dezelfde Azure Active Directory (Azure AD)-Tenant bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

-De koppeling van de werk ruimte met de *cluster*   bron mislukt als deze is      gekoppeld aan een andere *cluster*   bron

## <a name="troubleshooting"></a>Problemen oplossen

- Gedrag met Key Vault Beschik baarheid
  - In normale werking: opslag caches AEK gedurende korte tijd en terugvallen op Key Vault om regel matig de vertraging op te lossen.
    
  - Tijdelijke verbindings fouten--opslag verwerkt tijdelijke fouten (time-outs, verbindings fouten, DNS-problemen) doordat sleutels gedurende langere tijd in de cache blijven staan. Dit geeft een kleine problemen in Beschik baarheid. De mogelijkheden voor het uitvoeren van query's en opname worden zonder onderbreking voortgezet.
    
  - Live site--de niet-beschik baarheid van ongeveer 30 minuten leidt ertoe dat het opslag account niet meer beschikbaar is. De query mogelijkheid is niet beschikbaar en opgenomen gegevens worden gedurende enkele uren in de cache opgeslagen met behulp van micro soft-code om gegevens verlies te voor komen. Wanneer toegang tot Key Vault wordt hersteld, wordt de query beschikbaar en worden de gegevens in de tijdelijke cache opgenomen in de gegevens opslag en versleuteld met CMK.

  - Toegangs snelheid van Key Vault: de frequentie waarmee Azure Monitor toegang tot Key Vault voor verpakte en onverpakte bewerkingen tussen 6 en 60 seconden ligt.

- Als u een *cluster* bron maakt en de KeyVaultProperties onmiddellijk opgeeft, kan de bewerking mislukken omdat het toegangs beleid niet kan worden gedefinieerd totdat de systeem identiteit is toegewezen aan de *cluster* bron.

- Als u een bestaande *cluster* bron bijwerkt met KeyVaultProperties en het toegangs beleid Get ontbreekt in Key Vault, mislukt de bewerking.

- Als u probeert een *cluster* bron te verwijderen die aan een werk ruimte is gekoppeld, mislukt de Verwijder bewerking.

- Als er een conflict fout optreedt tijdens het maken van een *cluster* bron, is het mogelijk dat u de *cluster* resource in de afgelopen 14 dagen hebt verwijderd en een tijdelijke periode hebt om te verwijderen. De *cluster* bron naam blijft gereserveerd tijdens de tijdelijke periode en u kunt geen nieuw cluster met die naam maken. De naam wordt vrijgegeven na de periode voor voorlopig verwijderen wanneer de *cluster* bron permanent wordt verwijderd.

- Als u de *cluster* bron bijwerkt terwijl er een bewerking wordt uitgevoerd, mislukt de bewerking.

- Als u de *cluster* bron niet implementeert, controleert u of uw Azure Key Vault, *cluster*   bron en gekoppelde log Analytics-werk ruimten zich in dezelfde regio bevinden. De kan zich in verschillende abonnementen bevindt.

- Als u de sleutel versie bijwerkt in Key Vault en de nieuwe sleutel-id-details niet bijwerkt in de *cluster* bron, blijft de log Analytics cluster uw vorige sleutel gebruiken en worden uw gegevens niet meer toegankelijk. De nieuwe sleutel-id-gegevens in de *cluster* bron bijwerken om gegevens opname en de mogelijkheid om gegevens op te vragen, te hervatten.

- Gebruik uw contact personen in micro soft voor ondersteuning en Help met betrekking tot de door de klant beheerde sleutel.
