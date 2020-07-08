---
title: 'Versleuteling: op rust met door de klant beheerde sleutels'
titleSuffix: Azure Cognitive Search
description: Aanvulling op server-side encryptie over indexen en synoniemen in azure Cognitive Search met behulp van sleutels die u in Azure Key Vault maakt en beheert.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: f6bda61960efd9a5e176f8792601e315ba96bcca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85553284"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Versleuteling: op rest van inhoud in azure Cognitive Search met door de klant beheerde sleutels in Azure Key Vault

Standaard versleutelt Azure Cognitive Search geïndexeerde inhoud op rest met door [service beheerde sleutels](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). U kunt standaard versleuteling aanvullen met een extra versleutelings laag met behulp van sleutels die u in Azure Key Vault maakt en beheert. Dit artikel begeleidt u stapsgewijs door de stappen.

Versleuteling aan de server zijde wordt ondersteund via integratie met [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). U kunt uw eigen versleutelings sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Api's van Azure Key Vault gebruiken om versleutelings sleutels te genereren. Met Azure Key Vault kunt u ook het sleutel gebruik controleren. 

Versleuteling met door de klant beheerde sleutels wordt geconfigureerd op het kaart niveau index of synoniem wanneer deze objecten worden gemaakt en niet op het niveau van de zoek service. U kunt geen inhoud versleutelen die al bestaat. 

Sleutels hoeven niet allemaal in hetzelfde Key Vault te zijn. Eén zoek service kan fungeren als host voor meerdere versleutelde indexen of synoniemen die zijn versleuteld met hun eigen door de klant beheerde versleutelings sleutels die zijn opgeslagen in verschillende sleutel kluizen.  U kunt ook indexen en synoniemen toewijzen in dezelfde service die niet zijn versleuteld met door de klant beheerde sleutels. 

> [!IMPORTANT] 
> Deze functie is beschikbaar in de [rest API](https://docs.microsoft.com/rest/api/searchservice/) en [.net SDK versie 8,0-Preview](search-dotnet-sdk-migration-version-9.md). Er is momenteel geen ondersteuning voor het configureren van door de klant beheerde versleutelings sleutels in de Azure Portal. De zoek service moet na januari 2019 zijn gemaakt en kan geen gratis (gedeelde) service zijn.

## <a name="prerequisites"></a>Vereisten

In dit voor beeld worden de volgende services gebruikt. 

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. 

+ [Een Azure Key Vault resource maken](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) of een bestaande kluis vinden onder uw abonnement.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) of [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) wordt gebruikt voor configuratie taken.

+ [Postman](search-get-started-postman.md), [Azure POWERSHELL](search-create-index-rest-api.md) en [.NET SDK preview](https://aka.ms/search-sdk-preview) kunnen worden gebruikt om de rest API aan te roepen. Er is op dit moment geen portal ondersteuning voor door de klant beheerde versleuteling.

>[!Note]
> Vanwege de aard van de versleuteling met de functie door de klant beheerde sleutels, kunnen Azure Cognitive Search uw gegevens niet ophalen als uw Azure Key kluis-sleutel is verwijderd. Als u gegevens verlies wilt voor komen dat wordt veroorzaakt door onKey Vault opzettelijke verwijderingen van sleutels, **moet** u voorlopig verwijderen en beveiliging opschonen in Key Vault inschakelen voordat het kan worden gebruikt. Zie [Azure Key Vault voorlopig verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)voor meer informatie.   

## <a name="1---enable-key-recovery"></a>1-sleutel herstel inschakelen

Nadat u de Azure Key Vault resource hebt gemaakt, schakelt u de optie **voorlopig verwijderen** en **beveiliging opschonen** in de geselecteerde sleutel kluis in door de volgende Power shell-of Azure cli-opdrachten uit te voeren:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2-een nieuwe sleutel maken

Als u een bestaande sleutel gebruikt voor het versleutelen van Azure Cognitive Search inhoud, slaat u deze stap over.

1. [Meld u aan bij Azure Portal](https://portal.azure.com) en navigeer naar het Key kluis-dash board.

1. Selecteer de instelling **sleutels** in het navigatie deel venster links en klik op **+ genereren/importeren**.

1. Kies in het deel venster **een sleutel maken** in de lijst met **Opties**de methode die u wilt gebruiken om een sleutel te maken. U kunt een nieuwe sleutel **genereren** , een bestaande sleutel **uploaden** of **back-up terugzetten** gebruiken om een back-up van een sleutel te selecteren.

1. Voer een **naam** in voor uw sleutel en selecteer desgewenst andere sleutel eigenschappen.

1. Klik op de knop **maken** om de implementatie te starten.

Noteer de sleutel-id. Dit is samengesteld uit de **sleutel waarde-URI**, de naam van de **sleutel**en de **sleutel versie**. U hebt deze nodig voor het definiëren van een versleutelde index in azure Cognitive Search.
 
![Een nieuwe sleutel kluis sleutel maken](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Een nieuwe sleutel kluis sleutel maken")

## <a name="3---create-a-service-identity"></a>3-een service-identiteit maken

Als u een identiteit toewijst aan uw zoek service, kunt u Key Vault toegangs machtigingen verlenen aan uw zoek service. De identiteit van de zoek service wordt gebruikt voor verificatie met Azure Key kluis.

Azure Cognitive Search ondersteunt twee manieren voor het toewijzen van identiteit: een beheerde identiteit of een extern beheerde Azure Active Directory-toepassing. 

Gebruik, indien mogelijk, een beheerde identiteit. Het is de eenvoudigste manier om een identiteit aan uw zoek service toe te wijzen en in de meeste scenario's te gebruiken. Als u meerdere sleutels gebruikt voor indexen en synoniemen, of als uw oplossing zich in een gedistribueerde architectuur bevindt die op identiteit gebaseerde verificatie niet kent, gebruikt u de geavanceerde [extern beheerde Azure Active Directory benadering](#aad-app) die aan het einde van dit artikel wordt beschreven.

 In het algemeen kan met een beheerde identiteit uw zoek service worden geverifieerd bij Azure Key Vault zonder dat er referenties worden opgeslagen in de code. De levens cyclus van dit type beheerde identiteit is gekoppeld aan de levens cyclus van uw zoek service, maar kan slechts één beheerde identiteit hebben. Meer [informatie over beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Als u een beheerde identiteit wilt maken, [meldt u zich aan bij toAzure Portal](https://portal.azure.com) en opent u het dash board van de zoek service. 

1. Klik op **identiteit** in het navigatie deel venster links, wijzig de status in **op**aan en klik op **Opslaan**.

![Een beheerde identiteit inschakelen](./media/search-enable-msi/enable-identity-portal.png "Een beheerd-identiteit inschakelen")

## <a name="4---grant-key-access-permissions"></a>4-sleutel toegangs machtigingen verlenen

Om ervoor te zorgen dat uw zoek service uw Key Vault sleutel kan gebruiken, moet u de zoek service bepaalde toegangs machtigingen verlenen.

Toegangs machtigingen kunnen op elk gewenst moment worden ingetrokken. Na intrekking worden alle zoek service-indexen of synoniemen toewijzingen die gebruikmaken van die sleutel kluis, onbruikbaar. Als u de toegangs machtigingen voor de sleutel kluis op een later tijdstip herstelt, wordt de toegang tot index\synonym-kaarten hersteld. Zie [Secure Access to a key kluis](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)(Engelstalig) voor meer informatie.

1. [Meld u aan bij Azure Portal](https://portal.azure.com) en open de overzichts pagina van uw sleutel kluis. 

1. Selecteer de instelling **toegangs beleid** in het navigatie deel venster links en klik op **+ Nieuw toevoegen**.

   ![Nieuw sleutel kluis toegangs beleid toevoegen](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Nieuw sleutel kluis toegangs beleid toevoegen")

1. Klik op **Principal selecteren** en selecteer uw Azure Cognitive Search-service. U kunt zoeken op naam of op de object-ID die wordt weer gegeven nadat beheerde identiteit is ingeschakeld.

   ![Sleutel kluis toegangs beleid-Principal selecteren](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Sleutel kluis toegangs beleid-Principal selecteren")

1. Klik op **belang rijke machtigingen** en selecteer *ophalen*, *uitpakken sleutel* en *Terugloop sleutel*. U kunt de *Azure data Lake Storage-of Azure Storage* sjabloon gebruiken om snel de vereiste machtigingen te selecteren.

   Azure Cognitive Search moet worden verleend met de volgende [toegangs machtigingen](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Get* -Hiermee kan uw zoek service de open bare delen van uw sleutel ophalen in een Key Vault
   * *Toets voor tekst* : Hiermee kan uw zoek service uw sleutel gebruiken om de interne versleutelings sleutel te beveiligen
   * *Uitpakken sleutel* : Hiermee kan uw zoek service uw sleutel gebruiken om de interne versleutelings sleutel uit te pakken

   ![Selecteer sleutel kluis toegangs beleid sleutel machtigingen](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Selecteer sleutel kluis toegangs beleid sleutel machtigingen")

1. Klik op **OK** en **Sla** de wijzigingen in het toegangs beleid op.

> [!Important]
> Versleutelde inhoud in azure Cognitive Search is geconfigureerd voor het gebruik van een specifieke Azure Key Vault sleutel met een specifieke **versie**. Als u de sleutel of versie wijzigt, moet de toewijzing van de index of het synoniem worden bijgewerkt voor gebruik van de nieuwe key\version **voordat** u de vorige key\version. verwijdert Als u dit niet doet, wordt de toewijzing van de index of het synoniem onbruikbaar. u kunt de inhoud niet ontsleutelen wanneer de sleutel toegang is verbroken.   

## <a name="5---encrypt-content"></a>5-inhoud versleutelen

Het maken van een index of synoniemen kaart die is versleuteld met door de klant beheerde sleutel is nog niet mogelijk met Azure Portal. Gebruik Azure Cognitive Search REST API om een dergelijke index of synoniemen kaart te maken.

De toewijzing van zowel index als synoniemen ondersteunt een nieuwe **encryptionKey** -eigenschap op het hoogste niveau die wordt gebruikt om de sleutel op te geven. 

Met behulp van de **sleutel kluis-URI**, **sleutel naam** en de **sleutel versie** van uw sleutel kluis kunt u een **encryptionKey** -definitie maken:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Geen van deze sleutel kluis Details worden beschouwd als geheim en kunnen gemakkelijk worden opgehaald door te bladeren naar de relevante Azure Key Vault sleutel pagina in Azure Portal.

Als u een AAD-toepassing gebruikt voor Key Vault-verificatie in plaats van een beheerde identiteit te gebruiken, voegt u de **toegangs referenties** voor de Aad-toepassing toe aan uw versleutelings sleutel: 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>Voor beeld: index versleuteling
De details van het maken van een nieuwe index via de REST API zijn te vinden bij [Create Index (Azure Cognitive Search rest API)](https://docs.microsoft.com/rest/api/searchservice/create-index), waarbij het enige verschil hier de details van de versleutelings sleutel opgeeft als onderdeel van de index definitie: 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
U kunt de aanvraag voor het maken van de index nu verzenden en de index vervolgens normaal gaan gebruiken.

## <a name="example-synonym-map-encryption"></a>Voor beeld: synoniem toewijzing versleuteling

Meer informatie over het maken van een nieuwe synoniemen kaart via de REST API vindt u in de [toewijzing synoniemen maken (Azure Cognitive Search rest API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), waarbij het enige verschil hier de details van de versleutelings sleutel opgeeft als onderdeel van de definitie van de synoniemen kaart: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
U kunt de aanvraag voor het maken van de synoniemen kaart nu verzenden en vervolgens op de normale manier gebruiken.

>[!Important] 
> Hoewel **encryptionKey** niet kan worden toegevoegd aan bestaande Azure Cognitive Search indexen of synoniemen kaarten, kan deze worden bijgewerkt door verschillende waarden op te geven voor een van de drie sleutel kluis Details (bijvoorbeeld het bijwerken van de sleutel versie). Wanneer u overstapt naar een nieuwe Key Vault sleutel of een nieuwe sleutel versie, moeten alle Azure Cognitive Search index-of synoniemen toewijzingen die gebruikmaken van de sleutel eerst worden bijgewerkt voor gebruik van de nieuwe key\version **voordat** u de vorige key\version. verwijdert Als u dit niet doet, wordt de toewijzing van de index of het synoniem onbruikbaar, omdat het niet mogelijk is om de inhoud te ontsleutelen wanneer de sleutel toegang verloren is gegaan.   
> Als u de toegangs machtigingen voor de sleutel kluis op een later tijdstip herstelt, wordt de toegang tot inhoud hersteld.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Geavanceerd: een extern beheerde Azure Active Directory-toepassing gebruiken

Wanneer een beheerde identiteit niet mogelijk is, kunt u een Azure Active Directory-toepassing maken met een beveiligings-principal voor uw Azure Cognitive Search-service. Met name een beheerde identiteit is in deze omstandigheden niet haalbaar:

* U kunt de toegangs machtigingen van de zoek service niet rechtstreeks verlenen aan de sleutel kluis (bijvoorbeeld als de zoek service zich in een andere Active Directory Tenant bevindt dan de Azure Key Vault).

* Er is één zoek service vereist voor het hosten van meerdere versleutelde indexes\synonym-kaarten, elk met een andere sleutel van een andere sleutel kluis, waarbij elke sleutel kluis **een andere identiteit** voor verificatie moet gebruiken. Als u een andere identiteit gebruikt voor het beheren van verschillende sleutel kluizen is geen vereiste, overweeg dan het gebruik van de beheerde identiteits optie hierboven.  

Azure Cognitive Search ondersteunt het gebruik van Azure Active Directory (AAD)-toepassingen voor verificatie tussen uw zoek service en Key Vault om dergelijke topologieën te kunnen ondersteunen.    
Een AAD-toepassing maken in de portal:

1. [Een Azure Active Directory-toepassing maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Haal de toepassings-id en verificatie sleutel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) op, zodat deze zijn vereist voor het maken van een versleutelde index. De waarden die u moet opgeven, zijn onder andere **toepassings-id** en **verificatie sleutel**.

>[!Important]
> Als u besluit om een AAD-verificatie toepassing te gebruiken in plaats van een beheerde identiteit, moet u rekening houden met het feit dat Azure Cognitive Search niet is gemachtigd om uw AAD-toepassing te beheren namens u en is het uw AAD-toepassing te beheren, zoals periodieke rotatie van de verificatie sleutel voor de toepassing.
> Wanneer u een AAD-toepassing of de verificatie sleutel wijzigt, moeten alle Azure-Cognitive Search indexen of synoniemen toewijzingen die gebruikmaken van die toepassing, eerst worden bijgewerkt om gebruik te maken van de nieuwe toepassing ID\key **voordat** u de vorige toepassing of de autorisatie sleutel verwijdert, en voordat u de Key Vault toegang tot de app intrekt.
> Als u dit niet doet, wordt de toewijzing van de index of het synoniem onbruikbaar, omdat het niet mogelijk is om de inhoud te ontsleutelen wanneer de sleutel toegang verloren is gegaan.   

## <a name="next-steps"></a>Volgende stappen

Als u niet bekend bent met de Azure-beveiligings architectuur, raadpleegt u de [documentatie voor Azure-beveiliging](https://docs.microsoft.com/azure/security/), met name dit artikel:

> [!div class="nextstepaction"]
> [Gegevensversleuteling in rust](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
