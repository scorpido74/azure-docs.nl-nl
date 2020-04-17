---
title: Versleuteling aan de clientzijde met Java voor Microsoft Azure Storage | Microsoft Documenten
description: De Azure Storage Client Library for Java ondersteunt versleuteling en integratie aan clientzijde met Azure Key Vault voor maximale beveiliging voor uw Azure Storage-toepassingen.
services: storage
author: tamram
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 32691e0ddcee3f5410b12f07a2fb80806345bc26
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460508"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Client-Side Encryption en Azure Key Vault met Java voor Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Overzicht
De [Azure Storage Client Library for Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) ondersteunt het versleutelen van gegevens binnen clienttoepassingen voordat ze naar Azure Storage worden geüpload en gegevens decoderen tijdens het downloaden naar de client. De bibliotheek ondersteunt ook integratie met [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor beheer van opslagaccountsleutels.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Versleuteling en decryptie via de enveloptechniek
De processen van encryptie en decryptie volgen de envelop techniek.  

### <a name="encryption-via-the-envelope-technique"></a>Versleuteling via de enveloptechniek
Versleuteling via de enveloptechniek werkt op de volgende manier:  

1. De Azure-opslagclientbibliotheek genereert een INHOUDsversleutelingssleutel (CEK), een symmetrische sleutel voor eenmalig gebruik.  
2. Gebruikersgegevens worden versleuteld met behulp van deze CEK.   
3. De CEK wordt vervolgens verpakt (versleuteld) met behulp van de sleutel encryptiesleutel (KEK). De KEK wordt geïdentificeerd door een sleutel-id en kan een asymmetrisch sleutelpaar of een symmetrische sleutel zijn en lokaal worden beheerd of opgeslagen in Azure Key Vaults.  
   De opslagclientbibliotheek zelf heeft nooit toegang tot KEK. De bibliotheek beroept zich op het sleutelwikkelalgoritme dat wordt geleverd door Key Vault. Gebruikers kunnen ervoor kiezen om aangepaste providers te gebruiken voor het inpakken/ uitpakken van sleutels indien gewenst.  
4. De versleutelde gegevens worden vervolgens geüpload naar de Azure Storage-service. De ingepakte sleutel samen met een aantal extra versleutelingsmetagegevens wordt opgeslagen als metagegevens (op een blob) of geïnterpoleerd met de versleutelde gegevens (wachtrijberichten en tabelentiteiten).

### <a name="decryption-via-the-envelope-technique"></a>Decryptie via de enveloptechniek
Decryptie via de envelop techniek werkt op de volgende manier:  

1. De clientbibliotheek gaat ervan uit dat de gebruiker de sleutelversleutelingssleutel (KEK) lokaal of in Azure Key Vaults beheert. De gebruiker hoeft niet te weten welke specifieke sleutel is gebruikt voor versleuteling. In plaats daarvan kan een sleutelresolver worden ingesteld die verschillende sleutel-id's voor sleutels oplost.  
2. De clientbibliotheek downloadt de versleutelde gegevens samen met versleutelingsmateriaal dat op de service is opgeslagen.  
3. De ingepakte content encryptie sleutel (CEK) wordt vervolgens uitgepakt (gedecodeerd) met behulp van de sleutel encryptiesleutel (KEK). Ook hier heeft de clientbibliotheek geen toegang tot KEK. Het beroept zich gewoon op het uitpakalgoritme van de aangepaste of Key Vault-provider.  
4. De inhoudsversleutelingssleutel (CEK) wordt vervolgens gebruikt om de versleutelde gebruikersgegevens te decoderen.

## <a name="encryption-mechanism"></a>Versleutelingsmechanisme
De opslagclientbibliotheek maakt gebruik van [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) om gebruikersgegevens te versleutelen. Specifiek, [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) modus met AES. Elke dienst werkt iets anders, dus we zullen elk van hen hier bespreken.

### <a name="blobs"></a>Blobs
De clientbibliotheek ondersteunt momenteel alleen versleuteling van hele blobs. Versleuteling wordt specifiek ondersteund wanneer gebruikers de **methoden upload *** of de **openOutputStream-methode** gebruiken. Voor downloads worden zowel volledige als bereikdownloads ondersteund.  

Tijdens versleuteling genereert de clientbibliotheek een willekeurige Initialisatievector (IV) van 16 bytes, samen met een willekeurige inhoudsversleutelingssleutel (CEK) van 32 bytes, en voert de envelopversleuteling van de blobgegevens uit met behulp van deze informatie. De ingepakte CEK en een aantal extra encryptie metadata worden vervolgens opgeslagen als blob metadata samen met de versleutelde blob op de service.

> [!WARNING]
> Als je je eigen metadata voor de blob bewerkt of uploadt, moet je ervoor zorgen dat deze metagegevens behouden blijven. Als je nieuwe metadata uploadt zonder deze metadata, gaan de verpakte CEK, IV en andere metadata verloren en zal de blob-inhoud nooit meer worden opgehaald.
> 
> 

Het downloaden van een versleutelde blob omvat het ophalen van de inhoud van de hele blob met behulp van de **download**/**openInputStream** gemaksmethoden. De verpakte CEK wordt uitgepakt en samen met de IV (opgeslagen als blob metadata in dit geval) om de gedecodeerde gegevens terug te keren naar de gebruikers.

Het downloaden van een willekeurig bereik **(downloadRange** methoden) in de versleutelde blob omvat het aanpassen van het bereik dat door gebruikers om een kleine hoeveelheid extra gegevens die kunnen worden gebruikt om met succes decoderen van het gevraagde bereik te krijgen.  

Alle blobtypen (blokblobs, paginablobs en toevoegende blobs) kunnen met dit schema worden versleuteld/gedecodeerd.

### <a name="queues"></a>Wachtrijen
Aangezien wachtrijberichten van elke indeling kunnen zijn, definieert de clientbibliotheek een aangepaste indeling die de Initialisatievector (IV) en de gecodeerde inhoudsversleutelingssleutel (CEK) in de berichttekst bevat.  

Tijdens versleuteling genereert de clientbibliotheek een willekeurige IV van 16 bytes samen met een willekeurige CEK van 32 bytes en voert de envelopversleuteling van de wachtrijberichttekst uit met behulp van deze informatie. De ingepakte CEK en enkele extra encryptiemetadata worden vervolgens toegevoegd aan het versleutelde wachtrijbericht. Dit gewijzigde bericht (zie hieronder) wordt opgeslagen op de service.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Tijdens dedecryptie wordt de ingepakte sleutel uit het wachtrijbericht gehaald en uitgepakt. De IV wordt ook uit het wachtrijbericht gehaald en samen met de uitgepakte sleutel gebruikt om de wachtrijberichtgegevens te decoderen. Houd er rekening mee dat de metagegevens van de versleuteling klein zijn (minder dan 500 bytes), dus terwijl deze meetelt voor de 64KB-limiet voor een wachtrijbericht, moet de impact beheersbaar zijn.

### <a name="tables"></a>Tabellen
De clientbibliotheek ondersteunt versleuteling van entiteitseigenschappen voor het invoegen en vervangen van bewerkingen.

> [!NOTE]
> Samenvoegen wordt momenteel niet ondersteund. Aangezien een subset van eigenschappen mogelijk eerder is versleuteld met een andere sleutel, zal het eenvoudig samenvoegen van de nieuwe eigenschappen en het bijwerken van de metagegevens resulteren in gegevensverlies. Het samenvoegen vereist extra serviceoproepen om de reeds bestaande entiteit uit de service te lezen, of het gebruik van een nieuwe sleutel per eigenschap, die beide niet geschikt zijn om prestatieredenen.
> 
> 

Tabelgegevensversleuteling werkt als volgt:  

1. Gebruikers geven de eigenschappen op die moeten worden versleuteld.  
2. De clientbibliotheek genereert een willekeurige Initialisatie Vector (IV) van 16 bytes, samen met een willekeurige inhoud steller (CEK) van 32 bytes voor elke entiteit, en voert envelop encryptie op de afzonderlijke eigenschappen worden versleuteld door het afleiden van een nieuwe IV per eigenschap. De versleutelde eigenschap wordt opgeslagen als binaire gegevens.  
3. De ingepakte CEK en enkele extra encryptiemetadata worden vervolgens opgeslagen als twee extra gereserveerde eigenschappen. De eerste gereserveerde eigenschap (_ClientEncryptionMetadata1) is een tekenreekseigenschap die de informatie over IV, versie en verpakte sleutel bevat. De tweede gereserveerde eigenschap (_ClientEncryptionMetadata2) is een binaire eigenschap die de informatie bevat over de eigenschappen die zijn versleuteld. De informatie in deze tweede eigenschap (_ClientEncryptionMetadata2) is zelf versleuteld.  
4. Vanwege deze extra gereserveerde eigenschappen die nodig zijn voor versleuteling, kunnen gebruikers nu slechts 250 aangepaste eigenschappen hebben in plaats van 252. De totale omvang van de entiteit moet minder dan 1 MB bedragen.  
   
   Houd er rekening mee dat alleen tekenreekseigenschappen kunnen worden versleuteld. Als andere typen eigenschappen moeten worden versleuteld, moeten ze worden omgezet in tekenreeksen. De versleutelde tekenreeksen worden opgeslagen op de service als binaire eigenschappen, en ze worden omgezet terug naar strings na decryptie.
   
   Voor tabellen moeten gebruikers naast het versleutelingsbeleid ook de eigenschappen opgeven die moeten worden versleuteld. Dit kan worden gedaan door een kenmerk [Versleutelen] op te geven (voor POCO-entiteiten die afkomstig zijn van TableEntity) of een versleutelingsresolver in aanvraagopties. Een versleutelingsresolver is een gemachtigde die een partitiesleutel, rijsleutel en eigenschapsnaam neemt en een booleaan retourneert die aangeeft of die eigenschap moet worden versleuteld. Tijdens versleuteling gebruikt de clientbibliotheek deze informatie om te beslissen of een eigenschap moet worden versleuteld tijdens het schrijven naar de draad. De gemachtigde voorziet ook in de mogelijkheid van logica rond hoe eigenschappen worden versleuteld. (Als X bijvoorbeeld eigenschap A versleutelt; anders eigenschappen A en B versleutelen.) Houd er rekening mee dat het niet nodig is om deze informatie te verstrekken tijdens het lezen of opvragen van entiteiten.

### <a name="batch-operations"></a>Batchbewerkingen
In batchbewerkingen wordt dezelfde KEK gebruikt voor alle rijen in die batchbewerking, omdat de clientbibliotheek slechts één optieobject (en dus één beleid/KEK) per batchbewerking toestaat. De clientbibliotheek genereert echter intern een nieuwe willekeurige IV en willekeurige CEK per rij in de batch. Gebruikers kunnen er ook voor kiezen om verschillende eigenschappen voor elke bewerking in de batch te versleutelen door dit gedrag in de versleutelingsoplosser te definiëren.

### <a name="queries"></a>Query's
> [!NOTE]
> Omdat de entiteiten zijn versleuteld, u geen query's uitvoeren die filteren op een versleutelde eigenschap.  Als u het probeert, zijn de resultaten onjuist, omdat de service versleutelde gegevens probeert te vergelijken met niet-versleutelde gegevens.
> 
> 
> Als u querybewerkingen wilt uitvoeren, moet u een sleutelresolver opgeven die alle sleutels in de resultaatset kan oplossen. Als een entiteit in het queryresultaat niet kan worden opgelost naar een provider, wordt er in de clientbibliotheek een fout gegenereerd. Voor elke query die serverzijdeprojecties uitvoert, voegt de clientbibliotheek standaard de speciale eigenschappen van versleutelingsmetagegevens (_ClientEncryptionMetadata1 en _ClientEncryptionMetadata2) toe aan de geselecteerde kolommen.

## <a name="azure-key-vault"></a>Azure Key Vault
Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met Azure Key Vault kunnen gebruikers sleutels en geheimen versleutelen (zoals verificatiesleutels, opslagaccountsleutels, gegevensversleutelingssleutels, . PFX-bestanden en wachtwoorden met behulp van sleutels die worden beschermd door hardwarebeveiligingsmodules (HSM's). Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md) voor meer informatie.

De opslagclientbibliotheek maakt gebruik van de Key Vault-kernbibliotheek om een gemeenschappelijk framework in Azure te bieden voor het beheren van sleutels. Gebruikers krijgen ook het extra voordeel van het gebruik van de Key Vault-extensiesbibliotheek. De extensiesbibliotheek biedt nuttige functionaliteit rond eenvoudige en naadloze symmetric/RSA lokale en cloudkeyproviders, evenals met aggregatie en caching.

### <a name="interface-and-dependencies"></a>Interface en afhankelijkheden
Er zijn drie Key Vault-pakketten:  

* azure-keyvault-core bevat de IKey en IKeyResolver. Het is een klein pakket zonder afhankelijkheden. De opslagclientbibliotheek voor Java definieert deze als een afhankelijkheid.
* azure-keyvault bevat de Key Vault REST-client.  
* azure-keyvault-extensies bevatten extensiecode die implementaties van cryptografische algoritmen en een RSAKey en een SymmetricKey bevat. Het is afhankelijk van de naamruimten Core en KeyVault en biedt functionaliteit om een geaggregeerde resolver te definiëren (wanneer gebruikers meerdere sleutelproviders willen gebruiken) en een caching-sleutelresolver. Hoewel de opslagclientbibliotheek niet direct afhankelijk is van dit pakket, hebben gebruikers dit pakket nodig als gebruikers Azure Key Vault willen gebruiken om hun sleutels op te slaan of om de Key Vault-extensies te gebruiken om de lokale en cloudcryptografische providers te gebruiken.  
  
  Key Vault is ontworpen voor hoogwaardige masterkeys en beperkingslimieten per Key Vault zijn hiermee ontworpen. Bij het uitvoeren van client-side encryptie met Key Vault, het voorkeurmodel is het gebruik van symmetrische master keys opgeslagen als geheimen in Key Vault en lokaal in de cache opgeslagen. Gebruikers moeten het volgende doen:  

1. Maak een geheime offline en upload deze naar Key Vault.  
2. Gebruik de basis-id van het geheim als parameter om de huidige versie van het geheim op te lossen voor versleuteling en deze informatie lokaal in de cache op te nemen. CachingKeyResolver gebruiken voor caching; gebruikers worden niet verwacht dat ze hun eigen caching-logica implementeren.  
3. Gebruik de caching resolver als invoer tijdens het maken van het versleutelingsbeleid.
   Meer informatie over Key Vault gebruik is te vinden in de encryptie code monsters.

## <a name="best-practices"></a>Aanbevolen procedures
Versleutelingsondersteuning is alleen beschikbaar in de opslagclientbibliotheek voor Java.

> [!IMPORTANT]
> Wees je bewust van deze belangrijke punten bij het gebruik van client-side encryptie:
> 
> * Wanneer u van een versleutelde blob leest of schrijft, gebruikt u hele blob-uploadopdrachten en opdrachten voor het downloaden van blobs per hele blob. Schrijf niet naar een versleutelde blob met protocolbewerkingen zoals Putblock, Put Block List, Write Pages, Clear Pages of Append Block. anders u de versleutelde blob beschadigen en onleesbaar maken.
> * Voor tabellen bestaat een vergelijkbare beperking. Zorg ervoor dat u versleutelde eigenschappen niet bijwerkt zonder de versleutelingsmetagegevens bij te werken.  
> * Als u metagegevens instelt op de versleutelde blob, u de versleutelingsgerelateerde metagegevens overschrijven die nodig zijn voor decryptie, omdat het instellen van metagegevens niet additief is. Dit geldt ook voor momentopnamen; vermijd het opgeven van metagegevens tijdens het maken van een momentopname van een versleutelde blob. Als metagegevens moeten worden ingesteld, moet u eerst de **methode downloadKenmerken** aanroepen om de huidige versleutelingsmetagegevens te krijgen en gelijktijdige schrijfbewerkingen te voorkomen terwijl metagegevens worden ingesteld.  
> * Schakel de **vlag vereistVersleuteling** in de standaardaanvraagopties in voor gebruikers die alleen met versleutelde gegevens moeten werken. Zie hieronder voor meer info.  
> 
> 

## <a name="client-api--interface"></a>Client-API / Interface
Tijdens het maken van een object EncryptionPolicy kunnen gebruikers alleen een sleutel (het implementeren van IKey), alleen een resolver (het implementeren van IKeyResolver) of beide bieden. IKey is het basissleuteltype dat wordt geïdentificeerd met behulp van een sleutel-id en dat de logica biedt voor het verpakken/uitpakken. IKeyResolver wordt gebruikt om een sleutel op te lossen tijdens het decryptieproces. Het definieert een ResolveKey-methode die een IKey retourneert met een sleutel-id. Dit biedt gebruikers de mogelijkheid om te kiezen tussen meerdere sleutels die op meerdere locaties worden beheerd.

* Voor versleuteling wordt de sleutel altijd gebruikt en het ontbreken van een sleutel zal resulteren in een fout.  
* Voor decryptie:  
  
  * De sleutelresolver wordt aangeroepen als deze is opgegeven om de sleutel te krijgen. Als de resolver is opgegeven, maar geen toewijzing voor de sleutel-id heeft, wordt er een fout gegenereerd.  
  * Als resolver niet is opgegeven, maar een sleutel is opgegeven, wordt de sleutel gebruikt als de id overeenkomt met de vereiste sleutel-id. Als de id niet overeenkomt, wordt er een fout gegenereerd.  
    
    De [versleutelingsvoorbeelden](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) tonen een gedetailleerder end-to-end scenario voor blobs, wachtrijen en tabellen, samen met Key Vault-integratie.

### <a name="requireencryption-mode"></a>Encryptiemodus vereisen
Gebruikers kunnen optioneel een werkingsmodus inschakelen waarin alle uploads en downloads moeten worden versleuteld. In deze modus mislukken pogingen om gegevens te uploaden zonder versleutelingsbeleid of gegevens te downloaden die niet zijn versleuteld op de service. Met de **vlag vereistVersleuteling** van het object met aanvraagopties bepaalt dit gedrag. Als uw toepassing alle objecten versleutelt die zijn opgeslagen in Azure Storage, u de eigenschap **requireEncryption** instellen op de standaardaanvraagopties voor het serviceclientobject.   

Gebruik **cloudblobclient.getDefaultRequestOptions().setRequireEncryption(true)** om versleuteling te vereisen voor alle blobbewerkingen die via dat clientobject worden uitgevoerd.

### <a name="blob-service-encryption"></a>Blob-serviceversleuteling
Maak een **blobencryptionbeleidobject** en stel het in de aanvraagopties (per API of op clientniveau met **DefaultRequestOptions).** Al het andere wordt intern door de klantenbibliotheek afgehandeld.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Versleuteling van wachtrijservice
Maak een object **QueueEncryptionPolicy** en stel het in in de aanvraagopties (per API of op clientniveau met **DefaultRequestOptions).** Al het andere wordt intern door de klantenbibliotheek afgehandeld.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```

### <a name="table-service-encryption"></a>Versleuteling van tabelservice
Naast het maken van een versleutelingsbeleid en het instellen op aanvraagopties, moet u een **EncryptionResolver** opgeven in **TableRequestOptions**of het kenmerk [Versleutelen] instellen op de getter en setter van de entiteit.

### <a name="using-the-resolver"></a>De resolver gebruiken

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```

### <a name="using-attributes"></a>Kenmerken gebruiken
Zoals hierboven vermeld, als de entiteit TableEntity implementeert, kunnen de eigenschappengetter en setter worden versierd met het kenmerk [Versleutelen] in plaats van de **EncryptionResolver**op te geven.

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```

## <a name="encryption-and-performance"></a>Versleuteling en prestaties

Houd er rekening mee dat het versleutelen van uw opslaggegevens leidt tot extra algemene kosten voor prestaties. De inhoudssleutel en IV moeten worden gegenereerd, de inhoud zelf moet worden versleuteld en aanvullende metagegevens moeten worden opgemaakt en geüpload. Deze overhead is afhankelijk van de hoeveelheid gegevens die wordt versleuteld. We raden klanten aan om hun applicaties altijd te testen op prestaties tijdens de ontwikkeling.

## <a name="next-steps"></a>Volgende stappen

* Het [Azure Storage Client Library for Java Maven-pakket downloaden](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* De [Azure Storage Client Library voor Java Source Code downloaden van GitHub](https://github.com/Azure/azure-storage-java)
* Download de Azure Key Vault Maven Library voor Java Maven-pakketten:
  * [Kernpakket](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core)
  * [Klantpakket](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault)
* Ga naar de [Azure Key Vault-documentatie](../../key-vault/general/overview.md)
