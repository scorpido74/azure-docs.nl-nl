---
title: Azure Monitor door de klant beheerde sleutel configuratie
description: Informatie en stappen voor het configureren van door de klant beheerde sleutel (CMK) voor het versleutelen van gegevens in uw Log Analytics-werk ruimten met behulp van een Azure Key Vault sleutel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: 25fdb0aefacbdd9c2630a69981a67821ac155786
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758817"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor door de klant beheerde sleutel configuratie 

Dit artikel bevat achtergrond informatie en stappen voor het configureren van door de klant beheerde sleutels (CMK) voor uw Log Analytics-werk ruimten en Application Insights-onderdelen. Eenmaal geconfigureerd, worden alle gegevens die naar uw werk ruimten of onderdelen worden verzonden, versleuteld met uw Azure Key Vault sleutel.

U wordt aangeraden [beperkingen en beperkingen](#limitations-and-constraints) hieronder vóór de configuratie te bekijken.

## <a name="disclaimers"></a>Disclaimers

- Azure Monitor CMK is een functie voor vroegtijdige toegang en ingeschakeld voor geregistreerde abonnementen.

- De CMK-implementatie die in dit artikel wordt beschreven, wordt geleverd in de productie kwaliteit en wordt ondersteund als dusdanig, hoewel het een vroege Access-functie is.

- De CMK-functionaliteit wordt geleverd op een toegewezen gegevens archief, een Azure Data Explorer-cluster (ADX) en is geschikt voor klanten die 1 TB per dag of langer verzenden. 

- Het prijs model CMK is momenteel niet beschikbaar en wordt niet behandeld in dit artikel. Er wordt een prijs model voor een toegewezen ADX-cluster verwacht in het tweede kwar taal van het kalender jaar (CY) 2020 en is van toepassing op alle bestaande CMK-implementaties.

- In dit artikel wordt de CMK-configuratie voor Log Analytics-werk ruimten beschreven. CMK voor Application Insights onderdelen wordt ook ondersteund in dit artikel, terwijl er verschillen worden vermeld in de bijlage.

> [!NOTE]
> Log Analytics en Application Insights gebruiken hetzelfde platform voor de gegevens opslag en de query-engine.
> We brengen deze twee winkels samen via de integratie van Application Insights in Log Analytics om één Unified logs Store onder Azure Monitor te maken. Deze wijziging is gepland voor het tweede kwar taal van het kalender jaar 2020. Als u CMK niet hoeft te implementeren voor uw Application Insights gegevens, raden we u aan te wachten tot de consolidatie is voltooid, omdat dergelijke implementaties worden onderbroken door de consolidatie. u moet CMK na de migratie naar Log Analytics werk ruimte opnieuw configureren. Het minimum van 1 TB per dag geldt op het cluster niveau en totdat de samen voeging is voltooid tijdens het tweede kwar taal, Application Insights en Log Analytics afzonderlijke clusters vereisen.

## <a name="customer-managed-key-cmk-overview"></a>Overzicht van door de klant beheerde sleutel (CMK)

[Versleuteling op rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) is een veelvoorkomende privacy-en beveiligings vereiste in organisaties. U kunt de versleuteling op de rest van Azure volledig beheren, terwijl u verschillende opties hebt om versleutelings-of versleutelings sleutels nauw keurig te beheren.

De Azure Monitor gegevens opslag zorgt ervoor dat alle gegevens die worden versleuteld met behulp van door Azure beheerde sleutels, terwijl ze zijn opgeslagen in Azure Storage. Azure Monitor biedt ook een optie voor gegevens versleuteling met behulp van uw eigen sleutel die is opgeslagen in uw [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), dat wordt geopend met behulp van door het systeem toegewezen [beheerde identiteits](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) verificatie. Deze sleutel kan [software of hardware-HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview)zijn die is beveiligd.
Het Azure Monitor gebruik van versleuteling is identiek aan de manier waarop [Azure Storage versleuteling](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) werkt.

De frequentie waarmee Azure Monitor opslag toegang krijgt Key Vault voor omloop-en onverpakte bewerkingen ligt tussen 6 en 60 seconden.Azure Monitor Storage respecteert altijd wijzigingen in de sleutel machtigingen binnen een uur.

Opgenomen gegevens in de afgelopen 14 dagen worden ook opgeslagen in een hot-cache (met SSD-back-ups) voor een efficiënte bewerking van query-engine. Deze gegevens blijven versleuteld met micro soft-sleutels, ongeacht de CMK-configuratie, maar we werken eraan om de SSD te versleutelen met CMK in de eerste helft van 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Hoe CMK werkt in Azure Monitor

Azure Monitor maakt gebruik van door het systeem toegewezen beheerde identiteit om toegang tot uw Azure Key Vault te verlenen.Door het systeem toegewezen beheerde identiteit kan alleen worden gekoppeld aan één Azure-resource. De identiteit van Azure Monitor-gegevens archief (ADX-cluster) wordt ondersteund op het cluster niveau en dit bepaalt dat de CMK-mogelijkheid wordt geleverd op een toegewezen ADX-cluster. Ter ondersteuning van CMK op meerdere werk ruimten, een nieuwe Log Analytics resource (*cluster*) wordt uitgevoerd als een tussenliggende identiteits verbinding tussen uw Key Vault en uw log Analytics-werk ruimten. Dit concept voldoet aan de door het systeem toegewezen identiteits beperking en de identiteit wordt gehandhaafd tussen het ADX-cluster en de Log Analytics *cluster* resource, terwijl de gegevens van alle gekoppelde werk ruimten met uw Key Vault sleutel worden beveiligd. De aan ADX-cluster opslag maakt gebruik van de\'beheerde identiteit die aan de *cluster* bron is gekoppeld om uw Azure Key Vault via Azure Active Directory te verifiëren en te gebruiken.

![Overzicht van CMK](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Key Vault van de klant.
2.    De Log Analytics *cluster* bron van de klant met beheerde identiteit met machtigingen voor Key Vault: de identiteit wordt ondersteund op het niveau van de gegevens opslag (ADX cluster).
3.    Azure Monitor toegewezen ADX-cluster.
4.    De werk ruimten van de klant die zijn gekoppeld aan de *cluster* bron voor CMK-versleuteling.

## <a name="encryption-keys-management"></a>Beheer van versleutelings sleutels

Er zijn drie soorten sleutels betrokken bij het versleutelen van opslag gegevens:

- **Kek** -sleutel versleutelings sleutel (CMK)
- **AEK** -account versleutelings sleutel
- **Dek** -gegevens versleutelings sleutel

De volgende regels zijn van toepassing:

- De ADX-opslag accounts genereren een unieke versleutelings sleutel voor elk opslag account, die wordt aangeduid als de AEK.

- De AEK wordt gebruikt om DEKs af te leiden. Dit zijn de sleutels die worden gebruikt voor het versleutelen van elk gegevens blok dat naar de schijf wordt geschreven.

- Wanneer u de sleutel in Key Vault configureert en ernaar verwijst in de *cluster* bron, verzendt de Azure Storage aanvragen naar uw Azure Key Vault om de AEK in te pakken en het decoderen van de gegevens voor het versleutelen en ontsleutelen van bewerkingen uit te voeren.

- Uw KEK verlaat uw Key Vault nooit en in het geval van een HSM-sleutel verlaat het nooit de hardware.

- Azure Storage gebruikt de beheerde identiteit die is gekoppeld aan de *cluster* bron om te verifiëren en toegang te krijgen tot Azure Key Vault via Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>CMK-inrichtings procedure

Voor Application Insights CMK-configuratie volgt u de bijlage-inhoud voor stap 3 en 6.

1. Abonnements white list: dit is vereist voor deze vroege Access-functie
2. Azure Key Vault maken en de sleutel opslaan
3. Een *cluster* bron maken
4. Inrichting van Azure Monitor-gegevens archief (ADX-cluster)
5. Machtigingen verlenen aan uw Key Vault
6. Log Analytics werk ruimten koppelen

De procedure wordt momenteel niet ondersteund in de gebruikers interface en het inrichtings proces wordt uitgevoerd via REST API.

> [!IMPORTANT]
> Elke API-aanvraag moet een Bearer-autorisatie token in de aanvraag header bevatten.

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

Sommige bewerkingen in deze configuratie procedure worden asynchroon uitgevoerd, omdat deze niet snel kunnen worden voltooid. Met het antwoord voor asynchrone bewerking wordt in eerste instantie een HTTP-status code 200 (OK) en koptekst met de *Azure-AsyncOperation-* eigenschap geretourneerd wanneer deze wordt geaccepteerd:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

U kunt de status van de asynchrone bewerking controleren door een GET-aanvraag te verzenden naar de waarde van de *Azure-AsyncOperation-* header:
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

### <a name="subscription-whitelisting"></a>White list abonnement

CMK-functionaliteit is een vroegtijdige functie voor toegang. De abonnementen waar u *cluster* bronnen wilt maken, moeten vooraf worden white list door de Azure-product groep. Gebruik uw contact personen in micro soft om uw abonnementen-Id's op te geven.

> [!IMPORTANT]
> De functionaliteit van CMK is regionaal. Uw Azure Key Vault, *cluster* bron en gekoppelde log Analytics-werk ruimten moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

### <a name="storing-encryption-key-kek"></a>Versleutelings sleutel opslaan (KEK)

Maak of gebruik een Azure Key Vault die u al moet genereren of importeer een sleutel die moet worden gebruikt voor gegevens versleuteling. De Azure Key Vault moet worden geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw gegevens in Azure Monitor te beveiligen. U kunt deze configuratie controleren onder eigenschappen in uw Key Vault, de beveiliging van zowel *zacht verwijderen* als *leegmaken* moet zijn ingeschakeld.

![Zacht verwijderen en beveiligings instellingen opschonen](media/customer-managed-keys/soft-purge-protection.png)

Deze instellingen zijn beschikbaar via CLI en Power shell:
- [Voorlopig verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Beveiligings](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) beveiligingen verwijderen tegen het verwijderen van het geheim of de kluis, zelfs na het zacht verwijderen

### <a name="create-cluster-resource"></a>*Cluster* bron maken

Deze resource wordt gebruikt als een tussenliggende identiteits verbinding tussen uw Key Vault en uw Log Analytics-werk ruimten. Nadat u een bevestiging hebt ontvangen dat uw abonnementen zijn white list, maakt u een Log Analytics *cluster* resource in de regio waar uw werk ruimten zich bevinden. Voor Application Insights en Log Analytics zijn verschillende typen *cluster* bronnen vereist. Het type van de *cluster* bron wordt gedefinieerd tijdens de aanmaak tijd door de eigenschap *clusterType* in te stellen op *LogAnalytics*of *ApplicationInsights*. Het cluster bron type kan niet worden gewijzigd na.

Volg de inhoud van bijlage voor Application Insights CMK-configuratie.

U moet het capaciteits reserverings niveau (SKU) opgeven bij het maken van een *cluster* bron. Het capaciteits reserverings niveau kan binnen het bereik van 1.000 tot 2.000 GB per dag liggen en kan worden bijgewerkt in stappen van 100 later. Als u een capaciteits reserverings niveau nodig hebt dat hoger is dan 2.000 GB per dag, bereikt u uw micro soft-contact persoon om het te activeren. Deze eigenschap heeft momenteel geen invloed op facturering. Wanneer het prijs model voor een toegewezen cluster wordt geïntroduceerd, wordt de facturering toegepast op alle bestaande CMK-implementaties.

**Maken**

Deze aanvraag voor Resource Manager is een asynchrone bewerking.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
De identiteit wordt toegewezen aan de *cluster* bron op het moment van aanmaak.

**Beantwoord**

200 OK en koptekst.
Tijdens de vroegtijdige toegangs periode van de functie wordt het ADX-cluster hand matig ingericht. Terwijl het ADX-cluster wordt ingericht tijdens het volt ooien, kunt u de inrichtings status op twee manieren controleren:
1. Kopieer de URL-waarde voor Azure-AsyncOperation uit het antwoord en volg de controle van de [asynchrone bewerkings status](#asynchronous-operations-and-status-check).
2. Verzend een GET-aanvraag op de *cluster* resource en kijk naar de waarde *provisioningState* . Het is *ProvisioningAccount* tijdens het *inrichten en voltooid wanneer het is voltooid* .

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Inrichting van Azure Monitor-gegevens archief (ADX-cluster)

Tijdens de vroegtijdige toegangs periode van de functie wordt het ADX-cluster hand matig ingericht door het product team nadat de vorige stappen zijn voltooid. Gebruik uw micro soft-kanaal voor deze stap en geef de reactie van het *cluster* bron op. 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Kopieer het antwoord en sla het op, omdat u de details in de volgende stappen nodig hebt.

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
    "clusterType": "LogAnalytics", 
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

Werk uw Key Vault bij met een nieuw toegangs beleid dat machtigingen verleent aan uw *cluster* bron. Deze machtigingen worden gebruikt door de aan Azure Monitor opslag voor gegevens versleuteling. Open uw Key Vault in Azure Portal en klik vervolgens op toegangs beleid en vervolgens op toegangs beleid toevoegen om een beleid te maken met de volgende instellingen:

- Belang rijke machtigingen: Selecteer Get, terugloop sleutel en de machtigingen voor de uitpakken sleutel.
- Selecteer Principal: Voer de waarde van de principal-id in die is geretourneerd in het antwoord in de vorige stap.

![Key Vault machtigingen verlenen](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

De machtiging *Get* is vereist om te controleren of uw Key Vault is geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw Azure monitor gegevens te beveiligen.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Cluster bron bijwerken met sleutel-id Details

Deze stap wordt uitgevoerd tijdens de eerste en in toekomstige belang rijke versie-updates in uw Key Vault. Het informeert Azure Monitor opslag over de sleutel versie die moet worden gebruikt voor gegevens versleuteling. Wanneer u de nieuwe sleutel bijwerkt, wordt deze gebruikt om de opslag sleutel (AEK) in te pakken en in te pakken.

Als u de *cluster* bron met de details van de Key Vault *sleutel-id* wilt bijwerken, selecteert u de huidige versie van de sleutel in azure Key Vault om de details van de sleutel-id op te halen.

![Key Vault machtigingen verlenen](media/customer-managed-keys/key-identifier-8bit.png)

Werk de *cluster* bron-KeyVaultProperties bij met sleutel-id-Details.

**Bijwerken**

Deze aanvraag van een resource manager is een asynchrone bewerking bij het bijwerken van sleutel-id-Details, terwijl deze synchroon is tijdens het bijwerken van de capaciteits waarde.

> [!Warning]
> U moet een volledige hoofd tekst opgeven in de update van *cluster* resources, waaronder *identiteit*, *SKU*, *KeyVaultProperties* en *locatie*. Als de *KeyVaultProperties* -details ontbreken, wordt de sleutel-id uit de *cluster* bron verwijderd en wordt de [sleutel intrekking](#cmk-kek-revocation)veroorzaakt.

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
     "capacity": 1000
     },
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Werkruimte koppeling met *cluster* resource
Voor Application Insights CMK-configuratie volgt u de bijlage inhoud voor deze stap.

U moet schrijf machtigingen hebben voor uw werk ruimte en *cluster* resource om deze bewerking uit te voeren, waaronder de volgende acties:

- In de werk ruimte: micro soft. OperationalInsights/werk ruimten/schrijven
- In *cluster* resource: micro soft. OperationalInsights/clusters/schrijven

> [!IMPORTANT]
> Deze stap moet pas worden uitgevoerd nadat het ADX-cluster is ingericht. Als u werk ruimten en opname gegevens koppelt vóór het inrichten, worden opgenomen gegevens verwijderd en kunnen deze niet worden hersteld.

**Een werk ruimte koppelen**

Deze aanvraag voor Resource Manager is een asynchrone bewerking.

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
    "retentionInDays": days,
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

U kunt uw toegang tot uw gegevens intrekken door de sleutel uit te scha kelen of het *cluster* bron toegangs beleid in uw Key Vault te verwijderen. Met Azure Monitor opslag worden de wijzigingen in de sleutel machtigingen binnen een uur altijd in acht genomen, normaal gesp roken binnenkort en opslag wordt niet meer beschikbaar. Alle gegevens die zijn opgenomen in werk ruimten die zijn gekoppeld aan uw *cluster* bron, worden verwijderd en mislukken van query's. Voorheen opgenomen gegevens blijven ontoegankelijk in Azure Monitor opslag, zolang u uw *cluster* bron niet verwijdert en uw werk ruimten niet worden verwijderd. Ontoegankelijke gegevens zijn onderworpen aan het Bewaar beleid voor gegevens en worden verwijderd wanneer de Bewaar termijn wordt bereikt.

Met opslag worden uw Key Vault periodiek gecontroleerd om te proberen de versleutelings sleutel op te slaan en eenmaal te zijn geopend, gegevens opname en query's te hervatten binnen 30 minuten.

## <a name="cmk-kek-rotation"></a>CMK (KEK)

Voor de draaiing van CMK is een expliciete update van de *cluster* bron vereist met de nieuwe sleutel versie in azure Key Vault. Volg de instructies in *cluster* resource bijwerken met sleutel-id details om Azure monitor bij te werken met de nieuwe sleutel versie. Als u de sleutel versie bijwerkt in Key Vault en de nieuwe sleutel-id-details niet bijwerkt in de *cluster* bron, blijft Azure monitor opslag uw vorige sleutel gebruiken.
Al uw gegevens zijn toegankelijk na de bewerking voor het wijzigen van de sleutel, inclusief gegevens die zijn opgenomen vóór de draaiing en daarna, omdat alle gegevens worden versleuteld door de versleutelings sleutel van het account (AEK) terwijl AEK nu wordt versleuteld door de nieuwe Key Encryption Key (KEK)-versie.

## <a name="limitations-and-constraints"></a>Beperkingen en beperkingen

- De functie CMK wordt ondersteund op ADX-cluster niveau en vereist een speciaal Azure Monitor ADX-cluster met een vereiste voor het verzenden van 1 TB per dag of langer.

- Het maximum aantal *cluster* resources per abonnement is beperkt tot 2

- De *cluster* bron koppeling met de werk ruimte moet alleen worden uitgevoerd nadat u hebt gecontroleerd of de inrichting van het ADX-cluster is voltooid. Gegevens die worden verzonden naar uw werk ruimte voordat de inrichting is voltooid, worden verwijderd en kunnen niet worden hersteld.

- CMK-versleuteling is van toepassing op nieuwe opgenomen gegevens na de CMK-configuratie. Gegevens die zijn opgenomen vóór de CMK-configuratie, blijven versleuteld met micro soft-sleutel. U kunt een query uitvoeren op gegevens die zijn opgenomen voor en na de CMK-configuratie naadloos.

- U kunt de koppeling van een werk ruimte met een *cluster* bron ongedaan maken als u besluit dat CMK niet vereist is voor een bepaalde werk ruimte. Nieuwe opgenomen gegevens na de ontkoppelings bewerking worden opgeslagen in de gedeelde Log Analytics-opslag, zoals voordat deze was gekoppeld aan de *cluster* bron. U kunt een query uitvoeren op gegevens die zijn opgenomen voor en na de dekoppeling naadloos als uw *cluster* bron is ingericht en geconfigureerd met een geldige Key Vault sleutel.

- De Azure Key Vault moet worden geconfigureerd als herstelbaar. Deze eigenschappen zijn niet standaard ingeschakeld en moeten worden geconfigureerd met CLI of Power shell:

  - [Zacht verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) moet zijn ingeschakeld
  - Het [opschonen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) van de beveiliging moet zijn ingeschakeld om te beschermen tegen het verwijderen van het geheim of de kluis, zelfs na het zacht verwijderen

- Voor Application Insights en Log Analytics zijn afzonderlijke *cluster* bronnen vereist. Het type van de *cluster* bron wordt gedefinieerd tijdens de aanmaak tijd door de eigenschap clusterType in te stellen op ' LogAnalytics ' of ' ApplicationInsights '. Het bron type van het *cluster* kan niet worden gewijzigd.

- Het verplaatsen van een *cluster* bron naar een andere resource groep of een ander abonnement wordt momenteel niet ondersteund.

- Uw Azure Key Vault, *cluster* resource en gekoppelde werk ruimten moeten zich in dezelfde regio en in dezelfde Azure Active Directory (Azure AD)-Tenant bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

- Werkruimte koppeling met *cluster* bron mislukt als deze is gekoppeld aan een andere *cluster* bron

## <a name="troubleshooting-and-management"></a>Problemen oplossen en beheren

- Beschik baarheid Key Vault
    - In normale werking: opslag caches AEK gedurende korte tijd en terugvallen op Key Vault om regel matig de vertraging op te lossen.
    
    - Tijdelijke verbindings fouten--opslag verwerkt tijdelijke fouten (time-outs, verbindings fouten, DNS-problemen) doordat sleutels gedurende langere tijd in de cache blijven staan. Dit geeft een kleine problemen in Beschik baarheid. De mogelijkheden voor het uitvoeren van query's en opname worden zonder onderbreking voortgezet.
    
    - Live site--de niet-beschik baarheid van ongeveer 30 minuten leidt ertoe dat het opslag account niet meer beschikbaar is. De query mogelijkheid is niet beschikbaar en opgenomen gegevens worden gedurende enkele uren in de cache opgeslagen met behulp van micro soft-code om gegevens verlies te voor komen. Wanneer toegang tot Key Vault wordt hersteld, wordt de query beschikbaar en worden de gegevens in de tijdelijke cache opgenomen in de gegevens opslag en versleuteld met CMK.

- Als u een *cluster* bron maakt en de KeyVaultProperties onmiddellijk opgeeft, kan de bewerking mislukken omdat het toegangs beleid niet kan worden gedefinieerd totdat de systeem identiteit is toegewezen aan de *cluster* bron.

- Als u een bestaande *cluster* bron bijwerkt met KeyVaultProperties en het toegangs beleid Get ontbreekt in Key Vault, mislukt de bewerking.

- Als u probeert een *cluster* bron te verwijderen die aan een werk ruimte is gekoppeld, mislukt de Verwijder bewerking.

- Als er een conflict fout optreedt tijdens het maken van een *cluster* bron, is het mogelijk dat u de *cluster* resource in de afgelopen 14 dagen hebt verwijderd en een tijdelijke periode hebt om te verwijderen. De *cluster* bron naam blijft gereserveerd tijdens de tijdelijke periode en u kunt geen nieuw cluster met die naam maken. De naam wordt vrijgegeven na de periode voor voorlopig verwijderen wanneer de *cluster* bron permanent wordt verwijderd.

- Alle *cluster* resources voor een resource groep ophalen:

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
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- Alle *cluster* resources voor een abonnement ophalen:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Beantwoord**
    
  Hetzelfde antwoord als voor*cluster* resources voor een resource groep, maar in het abonnements bereik.

- *Capaciteits reservering* in *cluster* bron bijwerken: wanneer het gegevens volume aan uw gekoppelde werk ruimten wordt gewijzigd en u het capaciteits reserverings niveau voor facturerings overwegingen wilt bijwerken, volgt u de [Update *cluster* resource](#update-cluster-resource-with-key-identifier-details) en geeft u de nieuwe capaciteits waarde op. Het capaciteits reserverings niveau kan binnen het bereik van 1.000 tot 2.000 GB per dag liggen en in stappen van 100. Voor een niveau hoger dan 2.000 GB per dag, bereikt u uw micro soft-contact persoon om dit in te scha kelen.

- De *cluster* bron verwijderen: er wordt een bewerking voor zacht verwijderen uitgevoerd om het herstellen van uw *cluster* bron met gegevens binnen 14 dagen mogelijk te maken, of het verwijderen per ongeluk of opzettelijk is geslaagd. De *cluster* bron naam blijft gereserveerd tijdens de tijdelijke periode en u kunt geen nieuw cluster met die naam maken. Na de periode van de tijdelijke verwijdering wordt de *cluster* bron naam vrijgegeven, worden uw *cluster* bron en-gegevens permanent verwijderd en kunnen ze niet worden hersteld. Elke gekoppelde werk ruimte wordt de koppeling van de *cluster* bron bij het verwijderen. Nieuwe opgenomen gegevens worden opgeslagen in gedeelde Log Analytics-opslag en versleuteld met de micro soft-sleutel. De werk ruimten die zijn gekoppeld, zijn asynchroon.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Beantwoord**

  200 OK

- Herstel uw *cluster* bron en uw gegevens: een *cluster* bron die in de afgelopen 14 dagen is verwijderd, is in de status zacht verwijderen en kan worden hersteld. Dit wordt op dit moment hand matig uitgevoerd door de product groep. Gebruik uw micro soft-kanaal voor herstel aanvragen.

## <a name="appendix"></a>Bijlage

Application Insights door de klant beheerde sleutel (CMK) wordt ook ondersteund, maar u moet rekening houden met de volgende wijziging om u te helpen de implementatie van CMK te plannen voor uw Application Insight-onderdelen.

Log Analytics en Application Insights gebruiken hetzelfde platform voor de gegevens opslag en de query-engine. We brengen deze twee winkels samen via de integratie van Application Insights in Log Analytics om één Unified logs Store onder Azure Monitor te bieden in het tweede kwar taal van
2020. Met deze wijziging worden uw toepassing inzicht gegeven in Log Analytics werk ruimten en worden query's, inzichten en andere verbeteringen mogelijk gemaakt terwijl de configuratie van CMK in uw werk ruimte ook van toepassing is op uw Application Insights gegevens.

> [!NOTE]
> Als u geen CMK voor uw Application Insight-gegevens hoeft te implementeren vóór de integratie, raden we u aan te wachten met Application Insights CMK, omdat dergelijke implementaties worden onderbroken door de integratie. u moet CMK opnieuw configureren na de migratie naar Log Analytics werk ruimte. Het minimum van 1 TB per dag geldt op het cluster niveau en totdat de samen voeging is voltooid tijdens het tweede kwar taal, Application Insights en Log Analytics afzonderlijke clusters vereisen.

## <a name="application-insights-cmk-configuration"></a>Configuratie van Application Insights CMK

De configuratie van Application Insights CMK is identiek aan het proces dat in dit artikel wordt geïllustreerd, met inbegrip van beperkingen en probleem oplossing, met uitzonde ring van de volgende stappen:

- Een *cluster* bron maken
- Een onderdeel aan een *cluster* bron koppelen

Gebruik de volgende stappen bij het configureren van CMK voor Application Insights in plaats van de hierboven vermelde.

### <a name="create-a-cluster-resource"></a>Een *cluster* bron maken

Deze bron wordt gebruikt als een tussenliggende identiteits verbinding tussen uw Key Vault en uw onderdelen. Nadat u een bevestiging hebt ontvangen dat uw abonnementen zijn white list, maakt u een Log Analytics *cluster* resource in de regio waar uw onderdelen zich bevinden. Het type van de *cluster* bron wordt gedefinieerd tijdens de aanmaak tijd door de eigenschap *clusterType* in te stellen op *LogAnalytics*of *ApplicationInsights*. Het moet *ApplicationInsights* zijn voor Application Insights CMK. De *clusterType* -instelling kan niet worden gewijzigd na de configuratie.

**Maken**

Deze aanvraag voor Resource Manager is een asynchrone bewerking.

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
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**Beantwoord**

200 OK en koptekst.
Tijdens de vroegtijdige toegangs periode van de functie wordt het ADX-cluster hand matig ingericht. Terwijl het ADX-cluster wordt ingericht tijdens het volt ooien, kunt u de inrichtings status op twee manieren controleren:
1. Kopieer de URL-waarde voor Azure-AsyncOperation uit het antwoord en volg de controle van de [asynchrone bewerkings status](#asynchronous-operations-and-status-check).
2. Verzend een GET-aanvraag op de *cluster* resource en kijk naar de waarde *provisioningState* . Het is *ProvisioningAccount* tijdens het *inrichten en voltooid wanneer het is voltooid* .

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Een onderdeel koppelen aan een *cluster* bron met behulp van [onderdelen-API maken of bijwerken](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)

U moet schrijf machtigingen hebben voor uw onderdeel en *cluster* bron om deze bewerking uit te voeren, waaronder de volgende acties:

- In onderdeel: micro soft. Insights/onderdeel/schrijven
- In *cluster* resource: micro soft. OperationalInsights/clusters/schrijven

> [!IMPORTANT]
> Deze stap moet pas worden uitgevoerd nadat het ADX-cluster is ingericht. Als u tijdens het inrichten componenten koppelt en gegevens opneemt, worden opgenomen gegevens verwijderd en kunnen deze niet worden hersteld.
> Als u wilt controleren of het ADX-cluster is ingericht, voert u *cluster* resource ophalen rest API uit en controleert u of de waarde van *provisioningState* is *geslaagd*.

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

> [!IMPORTANT]
> Kopieer en bewaar het antwoord, want u hebt dit nodig in de volgende stappen.

**Een onderdeel koppelen**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
' clusterDefinitionId ' is de waarde ' clusterId ' die is opgenomen in de reactie van de vorige stap.
"soort" voor beeld is "Web".

**Beantwoord**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
' clusterDefinitionId ' is de *cluster* resource-id die is gekoppeld aan dit onderdeel.

Na de koppeling worden gegevens die naar uw onderdelen worden verzonden, opgeslagen die zijn versleuteld met uw beheerde sleutel.
