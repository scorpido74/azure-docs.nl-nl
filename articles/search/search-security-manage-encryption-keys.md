---
title: Encryptie-at-rest met behulp van door de klant beheerde sleutels
titleSuffix: Azure Cognitive Search
description: Vul serverversleuteling aan boven indexen en synoniemenkaarten in Azure Cognitive Search met sleutels die u maakt en beheert in Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899944"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Versleuteling-op-rest van inhoud in Azure Cognitive Search met behulp van door de klant beheerde sleutels in Azure Key Vault

Azure Cognitive Search versleutelt standaard geïndexeerde inhoud in rust met [servicebeheerde sleutels.](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models) U standaardversleuteling aanvullen met een extra versleutelingslaag met sleutels die u maakt en beheert in Azure Key Vault. Dit artikel leidt u door de stappen.

Versleuteling aan de serverzijde wordt ondersteund door integratie met [Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-overview) U uw eigen versleutelingssleutels maken en opslaan in een sleutelkluis, of u de API's van Azure Key Vault gebruiken om versleutelingssleutels te genereren. Met Azure Key Vault u ook het gebruik van de sleutel controleren. 

Versleuteling met door de klant beheerde sleutels wordt geconfigureerd op index- of synoniemkaartniveau wanneer deze objecten worden gemaakt en niet op het niveau van de zoekservice. U inhoud die al bestaat niet versleutelen. 

Sleutels hoeven niet allemaal in dezelfde Key Vault te staan. Eén zoekservice kan meerdere versleutelde indexen of synoniemkaarten hosten die elk versleuteld zijn met hun eigen door de klant beheerde versleutelingssleutels die zijn opgeslagen in verschillende key vaults.  U ook indexen en synoniemkaarten in dezelfde service hebben die niet zijn versleuteld met door de klant beheerde sleutels. 

> [!IMPORTANT] 
> Deze functie is beschikbaar op de [REST API versie 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) en [.NET SDK versie 8.0-preview](search-dotnet-sdk-migration-version-9.md). Er is momenteel geen ondersteuning voor het configureren van door klanten beheerde versleutelingssleutels in de Azure-portal. De zoekservice moet na januari 2019 worden gemaakt en kan geen Gratis (gedeelde) service zijn.

## <a name="prerequisites"></a>Vereisten

In dit voorbeeld worden de volgende services gebruikt. 

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. 

+ [Maak een Azure Key Vault-bron](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) of zoek een bestaande kluis onder uw abonnement.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) of [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) wordt gebruikt voor configuratietaken.

+ [Postbode,](search-get-started-postman.md) [Azure PowerShell](search-create-index-rest-api.md) en [Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) kunnen worden gebruikt om de REST API aan te roepen. Er is op dit moment geen portalondersteuning voor door de klant beheerde versleuteling.

>[!Note]
> Vanwege de aard van de versleuteling met de functie Beheerde sleutels van de klant kan Azure Cognitive Search uw gegevens niet ophalen als uw kluissleutel van Azure Key is verwijderd. Om gegevensverlies te voorkomen als gevolg van onbedoelde key vault-sleutelverwijderingen, **moet** u Soft Delete en Purge Protection in Key Vault inschakelen voordat deze kan worden gebruikt. Zie [Azure Key Vault soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)voor meer informatie.   

## <a name="1---enable-key-recovery"></a>1 - Sleutelherstel inschakelen

Nadat u de Azure Key Vault-bron hebt gemaakt, schakelt u **Soft Delete** and **Purge Protection** in de geselecteerde kluis Sleutel in door de volgende PowerShell- of Azure CLI-opdrachten uit te voeren:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Maak een nieuwe sleutel

Als u een bestaande sleutel gebruikt om Azure Cognitive Search-inhoud te versleutelen, slaat u deze stap over.

1. [Meld u aan bij azure portal](https://portal.azure.com) en navigeer naar het dashboard van de sleutelkluis.

1. Selecteer de instelling **Sleutels** in het linkernavigatiedeelvenster en klik op **+ Genereren/importeren**.

1. Kies in het deelvenster **Een sleutel** maken in de lijst **met opties**de methode die u wilt gebruiken om een sleutel te maken. U een nieuwe sleutel **genereren,** een bestaande sleutel **uploaden** of **Back-up herstellen** gebruiken om een back-up van een sleutel te selecteren.

1. Voer een **naam** voor uw sleutel in en selecteer optioneel andere belangrijke eigenschappen.

1. Klik op de knop **Maken** om de implementatie te starten.

Noteer de key identifier – deze is samengesteld uit de **sleutelwaarde Uri,** de **sleutelnaam**en de **sleutelversie**. U hebt deze nodig om een versleutelde index te definiëren in Azure Cognitive Search.
 
![Een nieuwe sleutelkluissleutel maken](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Een nieuwe sleutelkluissleutel maken")

## <a name="3---create-a-service-identity"></a>3 - Een service-identiteit maken

Door een identiteit toe te kennen aan uw zoekservice u Key Vault toegangsrechten verlenen aan uw zoekservice. Uw zoekservice gebruikt de identiteit om te verifiëren met Azure Key vault.

Azure Cognitive Search ondersteunt twee manieren om identiteit toe te wijzen: een beheerde identiteit of een extern beheerde Azure Active Directory-toepassing. 

Gebruik indien mogelijk een beheerde identiteit. Het is de eenvoudigste manier om een identiteit toe te wijzen aan uw zoekservice en moet in de meeste scenario's werken. Als u meerdere sleutels gebruikt voor indexen en synoniemenkaarten, of als uw oplossing zich in een gedistribueerde architectuur bevindt die verificatie op basis van identiteit disqualificeert, gebruikt u de geavanceerde [extern beheerde Azure Active Directory-benadering](#aad-app) die aan het einde van dit artikel wordt beschreven.

 In het algemeen stelt een beheerde identiteit uw zoekservice in staat om te verifiëren naar Azure Key Vault zonder referenties in code op te slaan. De levenscyclus van dit type beheerde identiteit is gekoppeld aan de levenscyclus van uw zoekservice, die slechts één beheerde identiteit kan hebben. [Meer informatie over beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Als u een beheerde identiteit wilt maken, [meldt u zich aan bij Azure-portal](https://portal.azure.com) en opent u uw dashboard voor de zoekservice. 

1. Klik op **Identiteit** in het linkernavigatiedeelvenster, wijzig de status in **Aan**en klik op **Opslaan**.

![Een beheerde identiteit inschakelen](./media/search-enable-msi/enable-identity-portal.png "Een manged-identiteit inschakelen")

## <a name="4---grant-key-access-permissions"></a>4 - Sleuteltoegangsmachtigingen verlenen

Als u wilt dat uw zoekservice uw Key Vault-sleutel gebruikt, moet u uw zoekservice bepaalde toegangsmachtigingen verlenen.

Toegangsmachtigingen kunnen op elk gewenst moment worden ingetrokken. Zodra deze is ingetrokken, wordt elke zoekservice-index of synoniemskaart die die sleutelkluis gebruikt, onbruikbaar. Als u op een later tijdstip toegangsmachtigingen voor de sleutelkluis herstelt, wordt de toegang tot index\synoniemkaart hersteld. Zie [Beveiligde toegang tot een sleutelkluis voor](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)meer informatie.

1. [Meld u aan bij azure portal](https://portal.azure.com) en open uw overzichtspagina voor sleutelkluizen. 

1. Selecteer de instelling **Voor toegangsbeleid** in het linkernavigatiedeelvenster en klik **op +Nieuw toevoegen**.

   ![Nieuw toegangsbeleid voor sleutelkluizen toevoegen](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Nieuw toegangsbeleid voor sleutelkluizen toevoegen")

1. Klik **op Hoofd selecteren** en selecteer uw Azure Cognitive Search-service. U ernaar zoeken op naam of op de object-id die is weergegeven nadat u een beheerde identiteit hebt ingemaakt.

   ![De hoofdsom van het toegangsbeleid voor belangrijke kluizen selecteren](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "De hoofdsom van het toegangsbeleid voor belangrijke kluizen selecteren")

1. Klik op **Belangrijke machtigingen** en selecteer *Get*, *Uitpakken sleutel* en Wrap *Key*. U de sjabloon *Azure Data Lake Storage of Azure Storage* gebruiken om snel de vereiste machtigingen te selecteren.

   Azure Cognitive Search moet worden verleend met de volgende [toegangsmachtigingen:](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)

   * *Get* - stelt uw zoekservice in staat om de openbare delen van uw sleutel op te halen in een Key Vault
   * *Wrap Key* - stelt uw zoekservice in staat om uw sleutel te gebruiken om de interne encryptiesleutel te beschermen
   * *Sleutel uitpakken* - stelt uw zoekservice in staat om uw sleutel te gebruiken om de interne versleutelingssleutel uit te pakken

   ![Belangrijke machtigingen voor toegangsbeleid voor belangrijke kluizen selecteren](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Belangrijke machtigingen voor toegangsbeleid voor belangrijke kluizen selecteren")

1. Klik **op OK** en sla de wijzigingen in het toegangsbeleid **op.**

> [!Important]
> Versleutelde inhoud in Azure Cognitive Search is geconfigureerd om een specifieke Azure Key Vault-sleutel met een specifieke **versie**te gebruiken. Als u de sleutel of versie wijzigt, moet de index- of synoniemkaart worden bijgewerkt om de nieuwe sleutel\versie te gebruiken **voordat** u de vorige sleutel\versie verwijderd. Als u dit niet doet, wordt de index- of synoniemkaart onbruikbaar, zodat u de inhoud niet decoderen zodra de toegang tot de sleutel is verloren.   

## <a name="5---encrypt-content"></a>5 - Inhoud versleutelen

Het maken van een index- of synoniemkaart die is versleuteld met door de klant beheerde sleutel, is nog niet mogelijk via Azure portal. Gebruik Azure Cognitive Search REST API om een dergelijke index- of synoniemkaart te maken.

Zowel index- als synoniemkaart ondersteunen een nieuwe versleutelingop het hoogste **niveauSleuteleigenschap** die wordt gebruikt om de sleutel op te geven. 

Met behulp van de **sleutelkluis Uri,** **de sleutelnaam** en de **belangrijkste versie** van uw key vault key, kunnen we een **encryptieSleuteldefinitie** maken:

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
> Geen van deze sleutelkluisgegevens wordt als geheim beschouwd en kan eenvoudig worden opgehaald door te bladeren naar de relevante Azure Key Vault-sleutelpagina in Azure-portal.

Als u een AAD-toepassing gebruikt voor Key Vault-verificatie in plaats van een beheerde identiteit te gebruiken, voegt u de **AAD-toepassingstoegangsreferenties** toe aan uw versleutelingssleutel: 
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

## <a name="example-index-encryption"></a>Voorbeeld: Indexversleuteling
De details van het maken van een nieuwe index via de REST API kunnen worden gevonden op [Create Index (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-index)waar het enige verschil hier is het opgeven van de encryptie sleuteldetails als onderdeel van de index definitie: 

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
U nu de aanvraag voor het maken van indexen verzenden en vervolgens de index normaal gebruiken.

## <a name="example-synonym-map-encryption"></a>Voorbeeld: Synoniemkaartversleuteling

De details van het maken van een nieuwe synoniemkaart via de REST API zijn te vinden op [Create Synonym Map (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)waar het enige verschil hier is het opgeven van de coderingssleuteldetails als onderdeel van de synoniemkaartdefinitie: 

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
U nu het verzoek voor het maken van synoniemkaarten verzenden en deze vervolgens normaal gebruiken.

>[!Important] 
> Hoewel **encryptionKey** niet kan worden toegevoegd aan bestaande Azure Cognitive Search-indexen of synoniemenkaarten, kan deze worden bijgewerkt door verschillende waarden op te geven voor een van de drie sleutelkluisdetails (bijvoorbeeld het bijwerken van de belangrijkste versie). Wanneer u overstapt op een nieuwe Key Vault-sleutel of een nieuwe sleutelversie, moet elke Azure Cognitive Search-index of synoniemenkaart die de sleutel gebruikt, eerst worden bijgewerkt om de nieuwe sleutel\-versie te gebruiken **voordat** de vorige sleutel\-versie wordt verwijderd. Als u dit niet doet, wordt de index- of synoniemkaart onbruikbaar, omdat deze de inhoud niet kan decoderen zodra de toegang tot de sleutel is verloren.   
> Als u op een later tijdstip toegangsmachtigingen voor de sleutelkluis herstelt, wordt de toegang tot inhoud hersteld.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Geavanceerd: een extern beheerde Azure Active Directory-toepassing gebruiken

Wanneer een beheerde identiteit niet mogelijk is, u een Azure Active Directory-toepassing maken met een beveiligingsprincipal voor uw Azure Cognitive Search-service. Concreet is een beheerde identiteit onder deze omstandigheden niet levensvatbaar:

* U uw zoekservice geen toegangsmachtigingen verlenen aan de kluis Sleutel (bijvoorbeeld als de zoekservice zich in een andere Active Directory-tenant bevindt dan de Azure Key Vault).

* Er is één zoekservice vereist voor het hosten van meerdere versleutelde indexen\synoniemkaarten, die elk een andere sleutelsleutelvan een andere sleutelkluis gebruiken, waarbij elke sleutelkluis **een andere identiteit** moet gebruiken voor verificatie. Als het gebruik van een andere identiteit om verschillende sleutelkluizen te beheren geen vereiste is, u overwegen de bovenstaande optie beheerde identiteit te gebruiken.  

Azure Cognitive Search ondersteunt azure Active Directory (AAD)-toepassingen voor verificatie tussen uw zoekservice en Key Vault om dergelijke topologieën mogelijk te maken.    
Ga als u een AAD-toepassing in de portal aan:

1. [Een Azure Active Directory-toepassing maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Krijg de toepassings-ID en verificatiesleutel,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) omdat deze nodig zijn voor het maken van een versleutelde index. Waarden die u moet opgeven, zijn **onder andere toepassings-id** en **verificatiesleutel.**

>[!Important]
> Wanneer u besluit een AAD-toepassing van verificatie te gebruiken in plaats van een beheerde identiteit, moet u rekening houden met het feit dat Azure Cognitive Search niet bevoegd is om uw AAD-toepassing namens u te beheren en het is aan u om uw AAD-toepassing te beheren, zoals periodiek rotatie van de toepassingsverificatiesleutel.
> Wanneer u een AAD-toepassing of de verificatiesleutel wijzigt, moet elke Azure Cognitive Search-index of synoniemenkaart die die toepassing gebruikt, eerst worden bijgewerkt om de nieuwe toepassings-ID\-sleutel te gebruiken **voordat** u de vorige toepassing of de autorisatiesleutel ervan verwijderde en voordat u de toegang tot de Key Vault intrekt.
> Als u dit niet doet, wordt de index- of synoniemkaart onbruikbaar, omdat deze de inhoud niet kan decoderen zodra de toegang tot de sleutel is verloren.   

## <a name="next-steps"></a>Volgende stappen

Als u niet bekend bent met de Azure-beveiligingsarchitectuur, controleert u de [Azure-beveiligingsdocumentatie](https://docs.microsoft.com/azure/security/)en in het bijzonder dit artikel:

> [!div class="nextstepaction"]
> [Gegevensversleuteling-in-rust](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
