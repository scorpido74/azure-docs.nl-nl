---
title: Azure Monitor door de klant beheerde sleutelconfiguratie
description: Informatie en stappen om cmk (Customer-Managed Key) te configureren om gegevens in uw Log Analytics-werkruimten te versleutelen met behulp van een Azure Key Vault-sleutel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 03/26/2020
ms.openlocfilehash: 563a50d4589a83f710caa8ff2d2d95065909fc5f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384174"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor door de klant beheerde sleutelconfiguratie 

In dit artikel vindt u achtergrondinformatie en stappen voor het configureren van CMK (Customer-Managed Keys) voor uw Log Analytics-werkruimten en onderdelen van Application Insights. Nadat deze is geconfigureerd, worden alle gegevens die naar uw werkruimten of onderdelen worden verzonden, versleuteld met uw Azure Key Vault-sleutel.

We raden u aan [beperkingen en beperkingen](#limitations-and-constraints) hieronder te bekijken voordat u deze configuratie hebt.

## <a name="disclaimers"></a>Disclaimers

- Azure Monitor CMK is een early access-functie die is ingeschakeld voor geregistreerde abonnementen.

- De CMK-implementatie die in dit artikel wordt beschreven, wordt geleverd in productiekwaliteit en wordt als zodanig ondersteund, hoewel het een early access-functie is.

- De CMK-mogelijkheid wordt geleverd op een speciaal data-store-cluster, een Azure Data Explorer (ADX) cluster en geschikt voor klanten die 1 TB per dag of meer verzenden. 

- Het CMK-prijsmodel is momenteel niet beschikbaar en wordt niet behandeld in dit artikel. Een prijsmodel voor dedicated ADX-cluster wordt verwacht in het tweede kwartaal van het kalenderjaar (CY) 2020 en is van toepassing op bestaande CMK-implementaties.

- In dit artikel wordt de CMK-configuratie voor Log Analytics-werkruimten beschreven. CMK for Application Insights-componenten worden ook ondersteund met behulp van dit artikel, terwijl verschillen worden vermeld in de bijlage.

> [!NOTE]
> Log Analytics en Application Insights gebruiken hetzelfde data-store platform en query engine.
> We brengen deze twee winkels samen via integratie van Application Insights in Log Analytics om één unified logs store te maken onder Azure Monitor. Deze wijziging is gepland voor het tweede kwartaal van het kalenderjaar 2020. Als u cmk voor uw Application Insights-gegevens tegen die tijd niet hoeft te implementeren, raden we u aan te wachten op de voltooiing van de consolidatie, omdat dergelijke implementaties worden verstoord door de consolidatie en u CMK opnieuw moet configureren na de migratie naar Logboek Analytics-werkruimte. Het minimum van 1 TB per dag is van toepassing op clusterniveau en totdat de consolidatie in het tweede kwartaal is voltooid, vereisen Application Insights en Log Analytics afzonderlijke clusters.

## <a name="customer-managed-key-cmk-overview"></a>CmK-overzicht (Customer-managed key)

[Encryptie at Rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) is een veelvoorkomende privacy- en beveiligingsvereiste in organisaties. U Azure versleuteling in rust volledig laten beheren, terwijl u verschillende opties hebt om versleuteling of versleutelingssleutels nauwkeurig te beheren.

De Azure Monitor-gegevensopslag zorgt ervoor dat alle gegevens die in rust zijn versleuteld met azure-beheerde sleutels worden opgeslagen in Azure Storage. Azure Monitor biedt ook een optie voor gegevensversleuteling met behulp van uw eigen sleutel die is opgeslagen in [Azure Key Vault,](https://docs.microsoft.com/azure/key-vault/key-vault-overview)die wordt geopend met behulp van door het systeem toegewezen [beheerde identiteitsverificatie.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Deze sleutel kan [software- of hardware-HSM-beveiliging](https://docs.microsoft.com/azure/key-vault/key-vault-overview)zijn.
Het gebruik van versleuteling door Azure Monitor is identiek aan de manier waarop [Azure Storage-versleuteling](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) werkt.

De frequentie waarmee Azure Monitor Storage toegang heeft tot Key Vault voor wrap- en uitpakbewerkingen ligt tussen 6 en 60 seconden.Azure Monitor Storage respecteert altijd binnen een uur wijzigingen in belangrijke machtigingen.

Ingenomen gegevens in de afgelopen 14 dagen wordt ook bewaard in hot-cache (SSD-backed) voor een efficiënte query engine werking. Deze gegevens blijven versleuteld met Microsoft-sleutels, ongeacht cmk-configuratie, maar we werken eraan om de SSD begin 2020 te versleutelen met CMK.

## <a name="how-cmk-works-in-azure-monitor"></a>Hoe CMK werkt in Azure Monitor

Azure Monitor maakt gebruik van door het systeem toegewezen beheerde identiteit om toegang te verlenen tot uw Azure Key Vault.Beheerde identiteit met systeemtoegewezen kan alleen worden gekoppeld aan één Azure-bron. De identiteit van Azure Monitor data-store (ADX-cluster) wordt ondersteund op clusterniveau en dit dicteert dat de CMK-capaciteit wordt geleverd op een speciaal ADX-cluster. Als u CMK op meerdere werkruimten wilt ondersteunen, wordt een nieuwe Log Analytics-bron *(Cluster)* uitgevoerd als een tussenliggende identiteitsverbinding tussen uw Key Vault en uw Log Analytics-werkruimten. Dit concept voldoet aan de door het systeem toegewezen identiteitsbeperking en de identiteit wordt behouden tussen het ADX-cluster en de *clusterbron* Log Analytics, terwijl de gegevens van alle bijbehorende werkruimten worden beveiligd met uw Key Vault-sleutel. De ADX-clusteropslag onder de\'laag gebruikt de beheerde identiteit die is gekoppeld aan de *clusterbron* om uw Azure Key Vault te verifiëren en te openen via Azure Active Directory.

![CMK-overzicht](media/customer-managed-keys/cmk-overview.png)
1.  De sleutelkluis van de klant.
2.  De Log Analytics *Cluster-bron van* de klant met beheerde identiteit met machtigingen voor Key Vault : de identiteit wordt ondersteund op het niveau van het ADX-cluster (Data-Store).
3.  Azure Monitor dedicated ADX-cluster.
4.  Werkruimten van de klant die zijn gekoppeld aan clusterbron voor CMK-versleuteling.

## <a name="encryption-keys-management"></a>Beheer van versleutelingssleutels

Er zijn 3 soorten sleutels betrokken bij opslaggegevensversleuteling:

- **KEK** - Sleutelversleutelingssleutel in sleutelkluis (CMK)
- **AEK** - Accountversleutelingssleutel
- **DEK** - Gegevensversleutelingssleutel

De volgende regels zijn van toepassing:

- Het ADX-opslagaccount genereert een unieke versleutelingssleutel voor elk opslagaccount, dat bekend staat als de AEK.

- De AEK wordt gebruikt om DEKs af te leiden, dat zijn de sleutels die worden gebruikt om elk blok van gegevens geschreven naar de schijf te versleutelen.

- Wanneer u uw sleutel configureert in Key Vault en ernaar verwijst in de *clusterbron,* stuurt de Azure Storage aanvragen naar uw Azure Key Vault om de AEK te verpakken en uit te pakken om gegevensversleuteling en decryptiebewerkingen uit te voeren.

- Uw KEK verlaat nooit uw Key Vault en in het geval van een HSM-sleutel verlaat het nooit de hardware.

- Azure Storage gebruikt de beheerde identiteit die is gekoppeld aan de *clusterbron* om te verifiëren en toegang te krijgen tot Azure Key Vault via Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>CMK-inrichtingsprocedure

Volg voor de CMK-configuratie van Application Insights de inhoud van de bijlage voor stap 3 en 6.

1. Whitelisting voor abonnementen - dit is vereist voor deze early access-functie
2. Azure Key Vault maken en sleutel opslaan
3. Een *clusterbron* maken
4. Azure Monitor-gegevensopslag (ADX-cluster) inrichten
5. Machtigingen verlenen aan uw Key Vault
6. Log Analytics-werkruimten koppelen

De procedure wordt momenteel niet ondersteund in de gebruikersinterface en het inrichtingsproces wordt uitgevoerd via REST API.

> [!IMPORTANT]
> Elke API-aanvraag moet een certificaat voor toestemming aan toonder bevatten in de aanmaningskop.

Bijvoorbeeld:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Waar *eyJ0eXAiO....* het volledige Autorisatietoken vertegenwoordigt. 

U het token verkrijgen met een van de volgende methoden:

1. De methode [App-registraties](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) gebruiken.

2. In de Azure Portal
    1. Navigeren naar Azure-portal in 'hulpprogramma voor ontwikkelaars (F12)
    1. Zoek naar autorisatietekenreeks onder 'Kopteksten aanvragen' in een van de exemplaren 'batch?api-version'. Het kijkt als: "vergunning: Het teken \<\>van de drager ". 
    1. Kopieer en voeg deze toe aan uw API-aanroep volgens de onderstaande voorbeelden.

3. Navigeer naar de documentatiesite van Azure REST. Druk op 'Probeer het' op een API en kopieer het Bearer-token.

### <a name="subscription-whitelisting"></a>Whitelisting van abonnementen

CMK-mogelijkheden zijn een early access-functie. De abonnementen waarbij u *clusterbronnen* wilt maken, moeten vooraf op de witte lijst worden gezet door de Azure-productgroep. Gebruik uw contactpersonen in Microsoft om uw abonnementen-id's op te bieden.

> [!IMPORTANT]
> CMK-mogelijkheden zijn regionaal. Uw Azure Key *Vault,clusterbron* en bijbehorende Log Analytics-werkruimten moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

### <a name="storing-encryption-key-kek"></a>Versleutelingssleutel opslaan (KEK)

Maak of gebruik een Azure Key Vault die u al moet genereren of importeer een sleutel die moet worden gebruikt voor gegevensversleuteling. De Azure Key Vault moet zijn geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw gegevens in Azure Monitor te beschermen. U deze configuratie verifiëren onder eigenschappen in uw Key Vault, zowel *Soft delete-* als *Purge-beveiliging* moeten zijn ingeschakeld.

Deze instellingen zijn beschikbaar via CLI en PowerShell:
- [Voorlopig verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Purge bescherming](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) bewakers tegen kracht verwijderen van de geheime / kluis, zelfs na zachte verwijderen

### <a name="create-cluster-resource"></a>*Clusterbron* maken

Deze bron wordt gebruikt als een tussenliggende identiteitsverbinding tussen uw Key Vault en uw werkruimten. Nadat u een bevestiging hebt ontvangen dat uw abonnementen op de witte lijst staan, maakt u een *clusterbron* log Analytics in het gebied waar uw werkruimten zich bevinden. Toepassingsinzichten en Logboekanalyse *Cluster* vereisen afzonderlijke clusterbronnentypen. Het type *clusterbron* wordt gedefinieerd op het moment van maken door de eigenschap 'clusterType' in te stellen op 'LogAnalytics' of 'ApplicationInsights'. Het brontype Cluster kan daarna niet worden gewijzigd.

Volg de inhoud van de bijlage voor de CMK-configuratie van Application Insights.

U moet het capaciteitsreserveringsniveau (sku) voor de *clusterbron* opgeven bij het maken van een *clusterbron.* Het capaciteitsreserveringsniveau kan tussen de 1000 en 2000 liggen en u het later bijwerken in stappen van 100. Als u een capaciteitsreserveringsniveau van meer dan 2000 nodig hebt, bereikt u uw Microsoft-contactpersoon om dit in te schakelen. Deze eigenschap heeft momenteel geen invloed op facturering, zodra het prijsmodel voor een specifiek cluster is geïntroduceerd, is facturering van toepassing op bestaande CMK-implementaties.

**Maken**

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
De identiteit wordt toegewezen aan de *clusterbron* tijdens het maken.

**Reactie**

202 Geaccepteerd. Dit is een standaard Resource Manager-antwoord voor asynchrone bewerkingen.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure Monitor-gegevensopslag (ADX-cluster) inrichten

Tijdens de vroege toegangsperiode van de functie wordt het ADX-cluster handmatig ingericht door het productteam zodra de vorige stappen zijn voltooid. Gebruik uw Microsoft-kanaal voor de inrichting terwijl u de reactie van de *clusterbron* geeft. 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Het antwoord kopiëren en opslaan, omdat u deze gegevens in latere stappen nodig hebt

**Reactie**
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

>[!Important]
> Het duurt de inrichting van de underly ADX cluster een paar minuten te voltooien. De *provisioningState* waarde geeft zijn staat, het is *ProvisioningAccount* terwijl provisioning en "Geslaagd" wanneer de inrichting is voltooid.
> De GUID 'principalId' wordt gegenereerd door de beheerde identiteitsservice voor de *clusterbron.*

### <a name="grant-key-vault-permissions"></a>Machtigingen voor sleutelkluizen verlenen

Werk uw Sleutelkluis bij met een nieuw toegangsbeleid waarmee machtigingen worden toegekend aan uw *clusterbron.* Deze machtigingen worden gebruikt door de onderlaag Azure Monitor Storage voor gegevensversleuteling. Open uw Sleutelkluis in Azure-portal en klik op 'Toegangsbeleid' en voeg vervolgens '+ Toegangsbeleid toevoegen' toe om een beleid met de volgende instellingen te maken:

- Belangrijkste machtigingen: selecteer 'Get', 'Wrap Key' en 'Uitpaksleutel' machtigingen.
- Selecteer hoofd: voer de hoofd-id-waarde in die in de vorige stap is geretourneerd in het antwoord.

![Key Vault-machtigingen verlenen](media/customer-managed-keys/grant-key-vault-permissions.png)

De machtiging *Verkrijg* is vereist om te controleren of uw Key Vault is geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw Azure Monitor-gegevens te beschermen.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Clusterbron bijwerken met sleutel-id-details

Deze stap is van toepassing op eerste en toekomstige belangrijke versie-updates in uw Key Vault. Het informeert Azure Monitor Storage over de nieuwe sleutelversie.

Als u de *clusterbron* wilt bijwerken met de gegevens van de Key Vault *Key-id,* selecteert u de huidige versie van uw sleutel in Azure Key Vault om de details van de sleutel-id op te halen.

![Machtigingen voor sleutelkluizen verlenen](media/customer-managed-keys/key-identifier-8bit.png)

Werk de *Clusterbron* KeyVaultProperties bij met sleutel-id-details.

**Update**

>[!Warning]
> U moet een volledige hoofdtekst opgeven in *clusterbronupdate* met *identiteit,* *sku,* *KeyVaultProperties* en *locatie.* Als u de details *KeyVaultProperties* mist, wordt de sleutel-id uit de *clusterbron* verwijderd en [wordt de sleutel intrekking veroorzaakt.](#cmk-kek-revocation)

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
"KeyVaultProperties" bevat de key identifier-details van De Sleutelkluis.

**Reactie**

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

### <a name="workspace-association-to-cluster-resource"></a>Werkruimtekoppeling naar *clusterbron*

Volg voor de CMK-configuratie van Application Insights de inhoud van de bijlage voor deze stap.

> [!IMPORTANT]
> Deze stap moet alleen worden uitgevoerd na adx-clusterprovisioning. Als u werkruimten en innamegegevens koppelt voorafgaand aan de inname, worden ingenomen gegevens verwijderd en worden ze niet hersteld.
> Als u wilt controleren of het ADX-cluster is ingericht, voert u *de API Voor clusterbron* Get REST uit en controleert u of de *provisioningstatewaarde* *is geslaagd*.

U moet machtigingen voor 'schrijven' hebben voor zowel uw werkruimte als *clusterbron* om deze bewerking uit te voeren, waaronder de volgende acties:

- In werkruimte: Microsoft.OperationalInsights/workspaces/write
- In *clusterbron:* Microsoft.OperationalInsights/clusters/write

**Een werkruimte koppelen**
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Reactie**

```json
{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

Na de koppeling van werkruimten worden gegevens die zijn ingenomen in uw werkruimten versleuteld opgeslagen met uw beheerde sleutel.

### <a name="workspace-association-verification"></a>Verificatie van de werkruimtekoppeling
U controleren of een werkruimte is gekoppeld aan een *Custer-bron* door te kijken naar de [werkruimten : Antwoord ontvangen.](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) De bijbehorende werkruimte heeft een eigenschap *Cluster* 'clusterResourceId' met de clusterbron-id.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
```

**Reactie**

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

## <a name="cmk-kek-revocation"></a>CMK (KEK) intrekking

U uw toegang tot uw gegevens intrekken door uw sleutel uit te schakelen of het toegangsbeleid *voor clusterbronnen* in uw Sleutelkluis te verwijderen. Azure Monitor Storage respecteert altijd wijzigingen in belangrijke machtigingen binnen een uur, normaal gesproken eerder, en opslag wordt niet beschikbaar. Alle gegevens die zijn ingenomen in werkruimten die zijn gekoppeld aan uw *clusterbron,* worden verwijderd en query's mislukken. Eerder ingenomen gegevens blijven ontoegankelijk in Azure Monitor Storage zolang uw sleutel wordt ingetrokken en uw werkruimten niet worden verwijderd. Ontoegankelijke gegevens worden beheerst door het beleid voor het bewaren van gegevens en worden verwijderd wanneer de bewaring wordt bereikt.

Storage zal uw Key Vault periodiek peilen om te proberen de versleutelingssleutel uit te pakken en zodra deze is geopend, worden gegevensopname en query binnen 30 minuten hervat.

## <a name="cmk-kek-rotation"></a>CMK (KEK) rotatie

Rotatie van CMK vereist een expliciete update van de *clusterbron* met de nieuwe sleutelversie in Azure Key Vault. Als u Azure Monitor wilt bijwerken met uw nieuwe sleutelversie, volgt u de instructies in de stap *Clusterbron* bijwerken met sleutelidentificatiegegevens. Als u uw sleutelversie in Key Vault bijwerkt en de nieuwe sleutel-id-gegevens in de *clusterbron* niet bijwerkt, blijft Azure Monitor Storage uw vorige sleutel gebruiken.
Al uw gegevens zijn toegankelijk na de sleutelrotatiebewerking, inclusief gegevens die vóór de rotatie en daarna zijn ingenomen, omdat alle gegevens versleuteld blijven door de Account Encryption Key (AEK), terwijl deze nu worden versleuteld door uw nieuwe KEK-versie (Key Encryption Key).

## <a name="limitations-and-constraints"></a>Beperkingen en beperkingen

- De CMK-functie wordt ondersteund op ADX-clusterniveau en vereist een speciaal Azure Monitor ADX-cluster

- Het maximum aantal *clusterbronnen* per abonnement is beperkt tot 2

- *Clusterresourcekoppeling* naar werkruimte mag ALLEEN worden uitgevoerd nadat u een bevestiging van de productgroep hebt ontvangen dat de adx-clusterinrichting is voltooid. Gegevens die voorafgaand aan deze provisioning worden verzonden, worden verwijderd en kunnen niet worden hersteld.

- CMK-versleuteling is van toepassing op nieuw ingenomen gegevens na de CMK-configuratie. Gegevens die vóór de CMK-configuratie zijn ingenomen, blijven versleuteld met Microsoft-sleutel. U gegevens voor en na de CMK-configuratie naadloos opvragen.

- Zodra werkruimte is gekoppeld aan een *clusterbron,* kan deze niet meer worden losgekoppeld van de *clusterbron,* omdat gegevens zijn versleuteld met uw sleutel en niet toegankelijk zijn zonder uw KEK in Azure Key Vault.

- De Azure Key Vault moet zijn geconfigureerd als herstelbaar. Deze eigenschappen zijn standaard niet ingeschakeld en moeten worden geconfigureerd met CLI en PowerShell:

  - [Soft Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) moet zijn ingeschakeld
  - [Zuiveringsbescherming](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) moet worden ingeschakeld om te waken tegen het verwijderen van geweld van het geheim / kluis, zelfs na zachte verwijderen

- Toepassingsinzichten en Logboekanalyse vereisen afzonderlijke *clusterbronnen.* Het type *clusterbron* wordt gedefinieerd op het moment van maken door de eigenschap 'clusterType' in te stellen op 'LogAnalytics' of 'ApplicationInsights'. Het brontype *Cluster* kan niet worden gewijzigd.

- *Clusterbron* wordt momenteel niet ondersteund naar een andere resourcegroep of -abonnement.

- Uw Azure Key Vault, *clusterbron* en bijbehorende werkruimten moeten zich in dezelfde regio en in dezelfde Azure Active Directory-tenant (Azure AD) bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

- De koppeling van werkruimte tot *clusterbron* mislukt als deze is gekoppeld aan een andere *clusterbron*

## <a name="troubleshooting-and-management"></a>Probleemoplossing en beheer

- Beschikbaarheid van Key Vault
    - Bij normaal gebruik gaat AEK voor korte tijd periodiek terug naar Key Vault om uit te pakken.
    
    - Tijdelijke verbindingsfouten. Opslag verwerkt tijdelijke fouten (time-outs, verbindingsfouten, DNS-problemen) door dat sleutels nog even in de cache kunnen blijven en dit eventuele kleine blips in beschikbaarheid overwint. De query- en opnamemogelijkheden worden zonder onderbreking voortgezet.
    
    - Live site, onbeschikbaarheid van ongeveer 30 minuten zal ertoe leiden dat de Opslag account niet beschikbaar. De querymogelijkheid is niet beschikbaar en ingenomen gegevens worden gedurende enkele uren in de cache opgeslagen met behulp van microsoft-sleutel om gegevensverlies te voorkomen. Wanneer de toegang tot Key Vault wordt hersteld, wordt de query beschikbaar en worden de tijdelijke gegevens in de cache naar het gegevensarchief ingenomen en versleuteld met CMK.

- Als u een *clusterbron* maakt en de KeyVaultProperties onmiddellijk opgeeft, kan de bewerking mislukken omdat het toegangsbeleid niet kan worden gedefinieerd totdat de systeemidentiteit is toegewezen aan de *clusterbron.*

- Als u bestaande *clusterbronnen* bijwerkt met KeyVaultProperties en 'Get' key Access Policy ontbreekt in Key Vault, mislukt de bewerking.

- Als u een *clusterbron* probeert te verwijderen die is gekoppeld aan een werkruimte, mislukt de verwijderingsbewerking.

- Alle *clusterbronnen* voor een resourcegroep opvragen:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Reactie**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
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

- Download alle *clusterbronnen* voor een abonnement:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Reactie**
    
  Hetzelfde antwoord als voor *'Clusterresources* voor een resourcegroep', maar dan in het abonnementsbereik.
    
- Een *clusterbron* verwijderen: er wordt een bewerking voor soft-delete uitgevoerd om het herstel van uw *clusterbron,* uw gegevens en bijbehorende werkruimten binnen 14 dagen mogelijk te maken, ongeacht of de verwijdering per ongeluk of opzettelijk was. Na de periode voor het verwijderen van soft delete zijn uw *clusterbron* en -gegevens niet-herstelbaar. De naam *van de clusterbron* blijft behouden tijdens de periode voor het verwijderen van soft-delete en u geen nieuw cluster met die naam maken.

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Reactie**

  200 OK

- Herstel uw *clusterbron* en uw gegevens tijdens de periode voor het verwijderen van soft-delete, maak een *clusterbron* met dezelfde naam en in hetzelfde abonnement, resourcegroep en regio. Volg de stap ** *Clusterbron* maken** om uw *clusterbron* te herstellen.


## <a name="appendix"></a>Bijlage

Application Insights Customer Managed Key (CMK) wordt ook ondersteund, maar u moet rekening houden met de volgende wijziging om u te helpen bij het plannen van de implementatie van CMK voor uw Application Insight-componenten.

Log Analytics en Application Insights gebruiken hetzelfde data-store platform en query engine. We brengen deze twee winkels samen via de integratie van Application Insights in Log Analytics om één uniforme logboekopslag onder Azure Monitor te bieden tegen het tweede kwartaal van
2020. Deze wijziging brengt uw Application Insight-gegevens in Log Analytics-werkruimten en maakt query's, inzichten en andere verbeteringen mogelijk, terwijl de configuratie van CMK op uw werkruimte ook van toepassing is op uw Application Insights-gegevens.

> [!NOTE]
> Als u CMK niet hoeft te implementeren voor uw Application Insight-gegevens vóór de integratie, raden we u aan te wachten met Application Insights CMK, omdat dergelijke implementaties worden verstoord door de integratie en u CMK opnieuw moet configureren na de migratie naar Log Analytics-werkruimte. Het minimum van 1 TB per dag is van toepassing op clusterniveau en totdat de consolidatie in het tweede kwartaal is voltooid, vereisen Application Insights en Log Analytics afzonderlijke clusters.

## <a name="application-insights-cmk-configuration"></a>CMK-configuratie met toepassingsinzichten

De configuratie van Application Insights CMK is identiek aan het proces dat in dit artikel wordt geïllustreerd, inclusief beperkingen en probleemoplossing, behalve deze stappen:

- Een *clusterbron* maken
- Een component koppelen aan een *clusterbron*

Gebruik bij het configureren van CMK voor Application Insights deze stappen in plaats van de bovenstaande stappen.

### <a name="create-a-cluster-resource"></a>Een *clusterbron* maken

Deze bron wordt gebruikt als tussenliggende identiteitsverbinding tussen uw Key Vault en uw componenten. Nadat u een bevestiging hebt ontvangen dat uw abonnementen op de witte lijst stonden, maakt u een *Clusterbron* Log Analytics in het gebied waar uw onderdelen zich bevinden. Het type *clusterbron* wordt gedefinieerd op het moment van maken door de eigenschap *clusterType* in te stellen op *LogAnalytics*of *ApplicationInsights*. Het moet *ApplicationInsights* for Application Insights CMK zijn. De *clusterType-instelling* kan niet worden gewijzigd na de configuratie.

**Maken**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**Reactie**

Identiteit wordt toegewezen aan de *clusterbron* tijdens het maken.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
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
"principle-id" is een GUID die is gegenereerd door de managed identity service.

> [!IMPORTANT]
> Kopieer en bewaar de "principe-id" waarde, omdat je het nodig hebt in de volgende stappen.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Een component koppelen aan een *clusterbron* met [componenten - API maken of bijwerken](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)

U moet machtigingen voor 'schrijven' hebben op zowel uw component als *clusterbron* om deze bewerking uit te voeren, waaronder de volgende acties:

- In component: Microsoft.Insights/component/write
- In *clusterbron:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Deze stap moet alleen worden uitgevoerd na adx-clusterprovisioning. Als u componenten en innamegegevens koppelt voorafgaand aan de inname, worden ingenomen gegevens verwijderd en niet kunnen worden hersteld.
> Als u wilt controleren of het ADX-cluster is ingericht, voert u *de API Voor clusterbron* Get REST uit en controleert u of de *provisioningstatewaarde* *is geslaagd*.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

**Reactie**
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

**Een component koppelen**

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
"clusterDefinitionId" is de "clusterId" waarde die in de reactie van de vorige stap.
"vriendelijk" voorbeeld is "web".

**Reactie**

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
'clusterDefinitionId' is *Cluster* de clusterbron-id die aan deze component is gekoppeld.

Na de koppeling worden gegevens die naar uw onderdelen worden verzonden, versleuteld opgeslagen met uw beheerde sleutel.
