---
title: Azure Monitor door de klant beheerde sleutel configuratie
description: Informatie en stappen voor het configureren van door de klant beheerde sleutel (CMK) voor het versleutelen van gegevens in uw Log Analytics-werk ruimten met behulp van een Azure Key Vault sleutel.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/07/2020
ms.openlocfilehash: d6419e86e1a541638a7053654bfcd7945aa41ae7
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891058"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor door de klant beheerde sleutel configuratie 

Dit artikel bevat achtergrond informatie en stappen voor het configureren van door de klant beheerde sleutels (CMK) voor uw Log Analytics-werk ruimten en Application Insights-onderdelen. Eenmaal geconfigureerd, worden alle gegevens die naar uw werk ruimten worden verzonden, versleuteld met uw Azure Key Vault sleutel.

U wordt aangeraden [beperkingen en beperkingen](#Limitations and constraints) hieronder vóór de configuratie te bekijken.

## <a name="disclaimers"></a>Disclaimers

- Azure Monitor CMK is een functie voor vroegtijdige toegang en ingeschakeld voor geregistreerde abonnementen

- De CMK-implementatie die in dit artikel wordt beschreven, wordt geleverd in de productie kwaliteit en wordt ondersteund als dusdanig, hoewel het een vroege Access-functie is.

- De CMK-functionaliteit wordt geleverd op een toegewezen gegevens archief-cluster, die een Azure Data Explorer (ADX)-cluster is en geschikt is voor klanten die 1 TB per dag of langer kunnen verzenden. 

- Het prijs model CMK is momenteel niet beschikbaar en wordt niet behandeld in dit artikel. In het tweede kwar taal van het kalender jaar (CY) 2020 wordt een prijs model voor toegewezen gegevens archief-cluster verwacht en dit is van toepassing op alle bestaande CMK-implementaties.

- In dit artikel wordt de CMK-configuratie voor Log Analytics-werk ruimten beschreven. CMK voor Application Insights onderdelen wordt ook ondersteund in dit artikel, terwijl er verschillen worden vermeld in de bijlage.

> [!NOTE]
> Log Analytics en Application Insights gebruiken hetzelfde platform voor de gegevens opslag en de query-engine.
> We brengen deze twee winkels samen via de integratie van Application Insights in Log Analytics om één Unified logs Store onder Azure Monitor te maken. Deze wijziging is gepland voor het tweede kwar taal van het kalender jaar 2020. Als u CMK niet hoeft te implementeren voor uw Application Insight-gegevens, wordt u aangeraden te wachten tot de consolidatie is voltooid, omdat dergelijke implementaties worden onderbroken door de consolidatie. u moet CMK opnieuw configureren na de migratie naar het logboek Analytics-werk ruimte. Het minimum van 1 TB per dag geldt op het cluster niveau en totdat de samen voeging is voltooid tijdens het tweede kwar taal Application Insights en Log Analytics afzonderlijke clusters vereist.

## <a name="customer-managed-key-cmk-overview"></a>Overzicht van door de klant beheerde sleutel (CMK)

[Versleuteling op rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) is een veelvoorkomende privacy-en beveiligings vereiste in organisaties. U kunt de versleuteling op de rest van Azure volledig beheren, terwijl u verschillende opties hebt om versleutelings-of versleutelings sleutels nauw keurig te beheren.

De Azure Monitor gegevens opslag zorgt ervoor dat alle gegevens die worden versleuteld met behulp van door Azure beheerde sleutels, terwijl ze zijn opgeslagen in Azure Storage. Azure Monitor biedt ook een optie voor gegevens versleuteling met behulp van uw eigen sleutel die is opgeslagen in [Azure-sleutel kluizen](https://docs.microsoft.com/azure/key-vault/key-vault-overview), die wordt geopend via door het systeem toegewezen [beheerde identiteits](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) verificatie. Deze sleutel kan [software of hardware-HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview)zijn die is beveiligd.
Het Azure Monitor gebruik van versleuteling is identiek aan de manier waarop [Azure Storage versleuteling](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) werkt.

De frequentie waarmee Azure Monitor opslag toegang krijgt Key Vault voor omloop-en onverpakte bewerkingen ligt tussen 6 en 60 seconden. Opslag Azure Monitor  
respecteert altijd wijzigingen in sleutel machtigingen binnen een uur.

## <a name="how-cmk-works-in-azure-monitor"></a>Hoe CMK werkt in Azure Monitor

Azure Monitor maakt gebruik van door het systeem toegewezen beheerde identiteit om toegang tot uw Azure Key Vault te verlenen. Door het systeem toegewezen beheerde identiteit kan alleen worden gekoppeld aan één Azure-resource. De identiteit van het Azure Monitor-archief (ADX-cluster) wordt ondersteund op het cluster niveau en dit bepaalt dat de CMK-mogelijkheid wordt geleverd op een toegewezen ADX-cluster. Ter ondersteuning van CMK op meerdere werk ruimten, een nieuwe Log Analytics resource (*cluster*) wordt uitgevoerd als een tussenliggende identiteits verbinding tussen uw Key Vault en uw log Analytics-werk ruimten. Dit concept voldoet aan de door het systeem toegewezen identiteits beperking en de identiteit wordt gehandhaafd tussen het ADX-cluster en de Log Analytics *cluster* resource *,* terwijl de gegevens van alle gekoppelde werk ruimten met uw Key Vault sleutel worden beveiligd. De aan ADX-cluster opslag maakt gebruik van de beheerde identiteit die\'s gekoppeld aan de *cluster* bron om uw Azure Key Vault via Azure Active Directory te verifiëren en te openen.

![Overzicht van CMK](media/customer-managed-keys/cmk-overview-8bit.png)

## <a name="encryption-keys-management"></a>Beheer van versleutelings sleutels

Er zijn drie soorten sleutels betrokken bij het versleutelen van opslag gegevens:

- **Kek** -sleutel versleutelings sleutel in Key Vault (CMK)
- **AEK** -account versleutelings sleutel
- **Dek** -gegevens versleutelings sleutel

De volgende regels zijn van toepassing:

- Het ADX-opslag account genereert een unieke versleutelings sleutel voor elk opslag account. dit wordt ook wel de AEK

- De AEK wordt gebruikt om DEKs af te leiden. Dit zijn de sleutels die worden gebruikt voor het versleutelen van elk gegevens blok dat naar de schijf wordt geschreven.

- Wanneer u de sleutel in Key Vault configureert en ernaar verwijst in de *cluster* bron, Azure Storage de AEK met uw KEK in azure Key Vault.

- Uw KEK verlaat uw Key Vault nooit en in het geval van een HSM-sleutel, blijft de hardware ongewijzigd.

- Azure Storage gebruikt de beheerde identiteit die is gekoppeld aan de *cluster* bron om te verifiëren en toegang te krijgen tot Azure Key Vault via Azure Active Directory.

- Voor lees-en schrijf bewerkingen worden door Azure Storage aanvragen verzonden naar Azure Key Vault om de AEK te verpakken en de versleutelings bewerkingen en decodering uit te voeren.

## <a name="cmk-provisioning-procedure"></a>CMK-inrichtings procedure

Het inrichtings proces bestaat uit de volgende stappen:

1. Abonnements white list: dit is vereist voor deze vroege Access-functie
2. Azure Key Vault maken en de sleutel opslaan
3. Een *cluster* bron maken
4. Machtigingen verlenen aan uw Key Vault
5. ADX-inrichting (Data-Store) Azure Monitor
6. Koppeling van Log Analytics werk ruimten

De procedure wordt momenteel niet ondersteund in de gebruikers interface en het inrichtings proces wordt uitgevoerd via REST API.

> [!IMPORTANT]
> Elke API-aanvraag moet een Bearer-autorisatie token in de aanvraag header bevatten.

Bijvoorbeeld:

```rst
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2015-11-01-preview]
  authorization: Bearer eyJ0eXAiO....
```

waarbij *eyJ0eXAiO....* het volledige verificatie token vertegenwoordigt. 

U kunt het token verkrijgen met een van de volgende methoden:

1. Gebruik [app-registraties](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) methode.

2. In de Azure Portal
    1. Ga naar Azure Portal in het hulp programma voor ontwikkel aars (F12)
    1. Zoek naar een autorisatie reeks onder ' aanvraag headers ' in een van de exemplaren van ' batch? API-Version '. Het ziet er als volgt uit: ' autorisatie: Bearer \<token\>'. 
    1. Kopieer en voeg deze toe aan uw API-oproep volgens de onderstaande voor beelden.

3. Navigeer naar de site Azure REST-documentatie. Druk op ' Probeer het ' op een API en kopieer het Bearer-token.

### <a name="subscription-whitelisting"></a>White list abonnement

CMK-functionaliteit is een vroegtijdige functie voor toegang. De abonnementen waar u *cluster* bronnen wilt maken, moeten vooraf worden white list door de Azure-product groep. Gebruik uw contact personen in micro soft om uw abonnementen-Id's op te geven.

> [!WARNING]
> De functionaliteit van CMK is regionaal. Uw Azure Key Vault, opslag account, *cluster* bron en gekoppelde log Analytics-werk ruimten moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

### <a name="storing-encryption-key-kek"></a>Versleutelings sleutel opslaan (KEK)

Maak een Azure Key Vault resource en Genereer of importeer een sleutel die moet worden gebruikt voor gegevens versleuteling.

De Azure Key Vault moet worden geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw Azure Monitor gegevens te beveiligen.

[Herstel opties inschakelen](https://docs.microsoft.com/azure/key-vault/key-vault-best-practices#turn-on-recovery-options):
- [Zacht verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) moet zijn ingeschakeld
- Het opschonen van de beveiliging moet zijn ingeschakeld om te beschermen tegen het verwijderen van het geheim of de kluis, zelfs na het zacht verwijderen

### <a name="create-cluster-resource"></a>*Cluster* bron maken

Deze resource wordt gebruikt als een tussenliggende identiteits verbinding tussen uw Key Vault en uw werk ruimten. Nadat u een bevestiging hebt ontvangen dat uw abonnementen zijn white list, maakt u een Log Analytics *cluster* resource in de regio waar uw werk ruimten zich bevinden.

**Maken**

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "region-name",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```

**Antwoord**

De identiteit wordt tijdens het maken aan het *cluster* toegewezen.

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id" //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}

```

Als u de *cluster* bron om welke reden dan ook wilt verwijderen (bijvoorbeeld door deze te maken met een andere naam), gebruikt u deze API-aanroep:

```
DELETE
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
```

### <a name="grant-key-vault-permissions"></a>Key Vault machtigingen verlenen

Werk uw Key Vault bij en voeg het toegangs beleid toe met de machtigingen Get, terugloop sleutel en uitpakken voor de *cluster* resource-id of *cluster* resource naam. Deze machtigingen worden door gegeven aan de Azure Monitor opslag.

![Key Vault machtigingen verlenen](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

De machtiging *Get* is vereist om te controleren of uw Key Vault is geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw Azure monitor gegevens te beveiligen.

Het duurt enkele minuten totdat de *cluster* bron is door gegeven in azure Resource Manager. Bij het configureren van dit toegangs beleid onmiddellijk nadat de *cluster* bron is gemaakt, kan er een tijdelijke fout optreden. In dit geval probeert u het over enkele minuten opnieuw.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Cluster bron bijwerken met sleutel-id Details

Wanneer u een nieuwe versie van een sleutel maakt, moet u de cluster bron bijwerken met Azure Key Vault sleutel-id-Details, zodat Azure Monitor opslag de nieuwe versie kan gebruiken. Als u de sleutel-id wilt ophalen, selecteert u de huidige versie van uw sleutel in Azure Key Vault:

![Key Vault machtigingen verlenen](media/customer-managed-keys/key-identifier-8bit.png)

Werk de *cluster* bron-KeyVaultProperties bij met sleutel-id-Details.

**Update**

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
       "KeyVaultProperties": { //Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
            },
   },
   "location":"region-name",
   "identity": { 
        "type": "systemAssigned" 
        }
}
```

**Antwoord**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
       "KeyVaultProperties": {     // Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name" //Example: Switzerland North
}
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Inrichting van Azure Monitor-gegevens archief (ADX-cluster)

Tijdens de vroegtijdige toegangs periode van de functie wordt het ADX-cluster hand matig ingericht door het product team nadat de vorige stappen zijn voltooid. Gebruik het kanaal dat u met micro soft hebt om de volgende details op te geven:

1. Bevestiging dat de bovenstaande stappen zijn uitgevoerd

2. De *cluster* resource-id die u in het antwoord hebt ontvangen ziet er als volgt uit:

```
"id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
```

De *cluster* bron-id kan op elk gewenst moment worden opgehaald met behulp van een Get API-aanroep.

**De *cluster* bron-id lezen**

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

**Antwoord**
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-Id"
  },
  "properties": {
       "KeyVaultProperties": { // Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
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

> [!NOTE]
> Deze stap moet **alleen** worden uitgevoerd nadat u via uw micro soft-kanaal een bevestiging van de product groep hebt ontvangen die is vervuld door de **inrichting van Azure Monitor Data Store (ADX-cluster)** . Als u werk ruimten en opname gegevens koppelt voordat u deze **inrichting**maakt, worden de gegevens verwijderd en kunnen ze niet worden hersteld.

**Een werk ruimte aan een *cluster* bron koppelen**

```json
PUT https://management.azure.com.resources.windows-int.net/Customer.svc/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name} 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "source": "Azure",
    "customerId": {workspace-id}, //Available in Azure portal under Log Analytics workspace Overview section
    "features": {
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    }
  },
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

**Antwoord**

```
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-id",
    "retentionInDays": value,
    "features": {
      "legacy": value,
      "searchVersion": value,
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    },
    "workspaceCapping": {
      "dailyQuotaGb": value,
      "quotaNextResetTime": "timeStamp",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}

```

Na de koppeling worden gegevens die naar uw werk ruimten worden verzonden, met uw beheerde sleutel opgeslagen.

## <a name="cmk-kek-revocation"></a>Intrekken van CMK (KEK)

Azure Monitor opslag beleeft altijd wijzigingen in de sleutel machtigingen binnen een uur, normaal gesp roken binnenkort en opslag wordt niet meer beschikbaar: alle gegevens die zijn opgenomen in werk ruimten die zijn gekoppeld aan de *cluster* bron, worden verwijderd en mislukken van query's. Voorheen opgenomen gegevens blijven ontoegankelijk in Azure Monitor opslag, zolang uw sleutel is ingetrokken en uw werk ruimten niet worden verwijderd. Ontoegankelijke gegevens zijn onderworpen aan het Bewaar beleid voor gegevens en worden verwijderd wanneer de Bewaar termijn wordt bereikt.

Met opslag worden uw Key Vault periodiek gecontroleerd om te proberen de versleutelings sleutel op te slaan en eenmaal te zijn geopend, gegevens opname en query's te hervatten binnen 30 minuten.

## <a name="cmk-kek-rotation"></a>CMK (KEK)

Voor de draaiing van CMK is een expliciete update van de cluster bron vereist met de nieuwe Azure Key Vault-sleutel versie. Volg de instructies in *cluster* resource bijwerken met sleutel-id details om Azure monitor bij te werken met de nieuwe sleutel versie.

-   Als u uw sleutel in Key Vault draait en de nieuwe versie in Azure Monitor binnenkort niet bijwerkt, is de sleutel niet toegankelijk voor Azure Monitor opslag.

## <a name="limitations-and-constraints"></a>Beperkingen en beperkingen

- De CMK-functie wordt ondersteund op ADX-cluster niveau en vereist een toegewezen Azure Monitor ADX-cluster

- Het maximum aantal *cluster* resources per abonnement is beperkt tot 5

- De koppeling *cluster* bron met de werk ruimte moet alleen worden uitgevoerd nadat u een bevestiging hebt ontvangen van de product groep waaraan de inrichting van het ADX-cluster is voldaan. Gegevens die vóór deze inrichting worden verzonden, worden verwijderd en kunnen niet worden hersteld.

- CMK-versleuteling is van toepassing op nieuwe opgenomen gegevens na de CMK-configuratie. Gegevens die vóór de CMK-configuratie zijn opgenomen, zijn versleuteld met de micro soft-sleutel. U kunt gegevens voor en na de configuratie naadloos opvragen.

- De functionaliteit van CMK is regionaal: uw Azure Key Vault, *cluster* bron en gekoppelde werk ruimten moeten zich in dezelfde regio bevinden, maar kunnen zich in verschillende abonnementen bevinden.

- Nadat de werk ruimte is gekoppeld aan een *cluster* bron, kan deze niet worden gekoppeld aan de *cluster* bron, omdat de gegevens zijn versleuteld met uw sleutel en niet toegankelijk zijn zonder uw Kek in azure Key Vault.

- De Azure Key Vault moet worden geconfigureerd als herstelbaar. Deze eigenschappen zijn niet standaard ingeschakeld:

  - [Zacht verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) is ingeschakeld
  - ' Niet opschonen ' is ingeschakeld voor beveiliging tegen het verwijderen van het geheim of de kluis, zelfs na het zacht verwijderen

- Het verplaatsen van een *cluster* bron naar een andere resource groep of een ander abonnement wordt momenteel niet ondersteund.

- De werkruimte koppeling met de *cluster* bron mislukt als de *cluster* bron zich in een andere Tenant bevindt.

-   Werkruimte koppeling met *cluster* bron mislukt als deze is gekoppeld aan een andere *cluster* bron

## <a name="troubleshooting-and-management"></a>Problemen oplossen en beheren

- Beschik baarheid Key Vault
    - Bij een normale werking gaat het opslag cache geheugen voor korte Peri Oden regel matig terug naar Key Vault om de AEK uit te pakken.
    
    - Tijdelijke verbindings fouten. Opslag verwerkt tijdelijke fouten (time-outs, verbindings fouten, DNS-problemen) doordat sleutels gedurende langere tijd in de cache blijven staan en dat er een kleine problemen in de beschik baarheid is: query mogelijkheid is zonder onderbreking beschikbaar.
            -Opname wordt voortgezet zonder onderbreking
    
    - Als een live site niet meer dan 30 minuten beschikbaar is, wordt het opslag account niet meer Beschik baarheid.
            -   - **query** mogelijkheid is niet beschikbaar- **opname** --gegevens worden gedurende enkele uren in de cache opgeslagen met behulp van micro soft-code om gegevens verlies te voor komen. Wanneer toegang tot Key Vault wordt hersteld, wordt de query beschikbaar en worden de tijdelijke gegevens in de cache opgenomen in de gegevens opslag en versleuteld met CMK.

- Als u een *cluster* bron maakt en de KeyVaultProperties onmiddellijk opgeeft, kan de bewerking mislukken omdat het toegangs beleid niet kan worden gedefinieerd totdat de systeem identiteit is toegewezen aan de *cluster* bron.

- Als u een bestaande *cluster* bron bijwerkt met KeyVaultProperties en het toegangs beleid Get ontbreekt in Key Vault, mislukt de bewerking.

- Als u probeert een *cluster* bron te verwijderen die aan een werk ruimte is gekoppeld, mislukt de Verwijder bewerking.

- Alle clusters ophalen voor een resource groep:

    ```
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
    ```
    
    *Antwoord*

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
             "KeyVaultProperties": { // Key Vault key identifier
                KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
                KeyName: {key-name},
                KeyVersion: {current-version}
                },
            "provisioningState": "Succeeded",
            "clusterType": "LogAnalytics", 
            "clusterId": "cluster-id"
          },
          "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
          "name": "cluster-name",
          "type": "Microsoft.OperationalInsights/clusters",
          "location": "region-name"
        }
      ]
    }
    ```

- Alle clusters voor een abonnement ophalen

    ```
    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
    ```
    
    *Antwoord*
    
    Hetzelfde als het antwoord van voor ' alle clusters voor een resource groep ', maar in het abonnements bereik.
    
- Een *cluster* bron verwijderen:

> U moet alle gekoppelde werk ruimten verwijderen voordat u de *cluster* bron kunt verwijderen:
>
> https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview verwijderen
>

Antwoord

200 OK

## <a name="appendix"></a>Bijlage

Dit artikel is ook van toepassing op Application Insights door de klant beheerde sleutel (CMK), maar u moet wel rekening houden met de aanstaande wijziging om u te helpen bij het plannen van de implementatie van CMK voor uw Application Insight-onderdelen.

Log Analytics en Application Insights gebruiken hetzelfde platform voor gegevens opslag en query-engine: we brengen deze twee winkels samen via integratie van Application Insights in Log Analytics om één Unified logs Store onder Azure Monitor te bieden door de tweede kwar taal van
2020. Met deze wijziging worden uw toepassing inzicht gegeven in Log Analytics werk ruimten en worden query's, inzichten en andere verbeteringen mogelijk gemaakt terwijl de configuratie van CMK in uw werk ruimte ook van toepassing is op uw Application Insights gegevens.

> [!NOTE]
> Als u na het tweede kwar taal van CY 2020 geen CMK op uw Application Insight-gegevens hoeft te implementeren, raden we u aan om te wachten tot de voltooiing van de consolidatie is voltooid, omdat dergelijke implementaties worden onderbroken door de consolidatie. u moet CMK opnieuw configureren op uw werk ruimte.

## <a name="application-insights-cmk-configuration"></a>Configuratie van Application Insights CMK

De configuratie van Application Insights CMK is identiek aan het proces dat in dit artikel wordt geïllustreerd, met inbegrip van beperkingen en probleem oplossing, met uitzonde ring van de volgende stappen:

- *Cluster* bron maken

- Een onderdeel aan een *cluster* bron koppelen

Gebruik de volgende stappen bij het configureren van CMK voor Application Insights in plaats van de hierboven vermelde.

### <a name="create-a-cluster-resource"></a>Een *cluster* bron maken

Deze bron wordt gebruikt als een tussenliggende identiteits verbinding tussen uw Key Vault en uw onderdelen. Nadat u een bevestiging hebt ontvangen dat uw abonnementen zijn white list, maakt u een Log Analytics cluster resource in de regio waar uw onderdelen zich bevinden. Het type van de cluster bron wordt gedefinieerd tijdens de aanmaak tijd door de eigenschap *clusterType* in te stellen op *LogAnalytics*of *ApplicationInsights*. Het moet *ApplicationInsights* zijn voor Application Insights CMK. De *clusterType* -instelling kan niet worden gewijzigd na de configuratie.

Maken:

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "region-name",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

Reactie:

De identiteit wordt tijdens het maken aan het cluster toegewezen.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id" //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="associate-a-component-to-a-cluster-resource"></a>Een onderdeel aan een cluster bron koppelen

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Insights/components/{component-name}?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id" //The id of the cluster resource
  },
  "location": "region-name",
  "kind": "component-type",
}
```

Antwoord

```json
{
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.insights/components/{component-name}",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id" //The id of the cluster resource
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

Na de koppeling worden gegevens die naar uw onderdelen worden verzonden, opgeslagen die zijn versleuteld met uw beheerde sleutel.
