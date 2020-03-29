---
title: Versleuteling aan de clientzijde met Python
titleSuffix: Azure Storage
description: De Azure Storage Client Library for Python ondersteunt client-side encryptie voor maximale beveiliging voor uw Azure Storage-toepassingen.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 16e66cd762b86b27dc6703542ca7261b2300a33b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895374"
---
# <a name="client-side-encryption-with-python"></a>Versleuteling aan de clientzijde met Python

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Overzicht
De [Azure Storage Client Library for Python](https://pypi.python.org/pypi/azure-storage) ondersteunt het versleutelen van gegevens binnen clienttoepassingen voordat ze naar Azure Storage worden geüpload en gegevens decoderen tijdens het downloaden naar de client.

> [!NOTE]
> De Azure Storage Python-bibliotheek bevindt zich in een voorbeeld.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Versleuteling en decryptie via de enveloptechniek
De processen van encryptie en decryptie volgen de envelop techniek.

### <a name="encryption-via-the-envelope-technique"></a>Versleuteling via de enveloptechniek
Versleuteling via de enveloptechniek werkt op de volgende manier:

1. De Azure-opslagclientbibliotheek genereert een INHOUDsversleutelingssleutel (CEK), een symmetrische sleutel voor eenmalig gebruik.
2. Gebruikersgegevens worden versleuteld met behulp van deze CEK.
3. De CEK wordt vervolgens verpakt (versleuteld) met behulp van de sleutel encryptiesleutel (KEK). De KEK wordt geïdentificeerd door een sleutel-id en kan een asymmetrische sleutelpaar of een symmetrische sleutel zijn, die lokaal wordt beheerd.
   De opslagclientbibliotheek zelf heeft nooit toegang tot KEK. De bibliotheek beroept zich op het sleutelwikkelalgoritme dat wordt geleverd door het KEK. Gebruikers kunnen ervoor kiezen om aangepaste providers te gebruiken voor het inpakken/ uitpakken van sleutels indien gewenst.
4. De versleutelde gegevens worden vervolgens geüpload naar de Azure Storage-service. De ingepakte sleutel samen met een aantal extra versleutelingsmetagegevens wordt opgeslagen als metagegevens (op een blob) of geïnterpoleerd met de versleutelde gegevens (wachtrijberichten en tabelentiteiten).

### <a name="decryption-via-the-envelope-technique"></a>Decryptie via de enveloptechniek
Decryptie via de envelop techniek werkt op de volgende manier:

1. De clientbibliotheek gaat ervan uit dat de gebruiker de sleutelversleutelingssleutel (KEK) lokaal beheert. De gebruiker hoeft niet te weten welke specifieke sleutel is gebruikt voor versleuteling. In plaats daarvan kan een sleutelresolver, die verschillende sleutel-id's voor sleutels oplost, worden ingesteld en gebruikt.
2. De clientbibliotheek downloadt de versleutelde gegevens samen met versleutelingsmateriaal dat op de service is opgeslagen.
3. De ingepakte content encryptie sleutel (CEK) wordt vervolgens uitgepakt (gedecodeerd) met behulp van de sleutel encryptiesleutel (KEK). Ook hier heeft de clientbibliotheek geen toegang tot KEK. Het beroept zich gewoon op het uitpakalgoritme van de aangepaste provider.
4. De inhoudsversleutelingssleutel (CEK) wordt vervolgens gebruikt om de versleutelde gebruikersgegevens te decoderen.

## <a name="encryption-mechanism"></a>Versleutelingsmechanisme
De opslagclientbibliotheek maakt gebruik van [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) om gebruikersgegevens te versleutelen. Specifiek, [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) modus met AES. Elke dienst werkt iets anders, dus we zullen elk van hen hier bespreken.

### <a name="blobs"></a>Blobs
De clientbibliotheek ondersteunt momenteel alleen versleuteling van hele blobs. Met name versleuteling wordt ondersteund wanneer gebruikers de **methoden maken*** gebruiken. Voor downloads worden zowel volledige als bereikdownloads ondersteund en is parallelle nis van zowel upload als download beschikbaar.

Tijdens versleuteling genereert de clientbibliotheek een willekeurige Initialisatievector (IV) van 16 bytes, samen met een willekeurige inhoudsversleutelingssleutel (CEK) van 32 bytes, en voert de envelopversleuteling van de blobgegevens uit met behulp van deze informatie. De ingepakte CEK en een aantal extra encryptie metadata worden vervolgens opgeslagen als blob metadata samen met de versleutelde blob op de service.

> [!WARNING]
> Als je je eigen metadata voor de blob bewerkt of uploadt, moet je ervoor zorgen dat deze metagegevens behouden blijven. Als je nieuwe metadata uploadt zonder deze metadata, gaan de verpakte CEK, IV en andere metadata verloren en zal de blob-inhoud nooit meer worden opgehaald.
> 
> 

Het downloaden van een versleutelde blob omvat het ophalen van de inhoud van de hele blob met behulp van de **get*** gemak methoden. De verpakte CEK wordt uitgepakt en samen met de IV (opgeslagen als blob metadata in dit geval) om de gedecodeerde gegevens terug te keren naar de gebruikers.

Het downloaden van een willekeurig bereik **(get*** methoden met bereik parameters doorgegeven in) in de versleutelde blob gaat het aanpassen van het bereik dat door gebruikers om een kleine hoeveelheid extra gegevens die kunnen worden gebruikt om met succes decoderen van het gevraagde bereik te krijgen.

Blokkeer blobs en paginablobs kunnen alleen worden versleuteld/gedecodeerd met dit schema. Er is momenteel geen ondersteuning voor het versleutelen van append blobs.

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
3. De ingepakte CEK en enkele extra encryptiemetadata worden vervolgens opgeslagen als twee extra gereserveerde eigenschappen. De eerste gereserveerde eigenschap (ClientEncryptionMetadata1)\_is een tekenreekseigenschap die de informatie over IV, versie en verpakte sleutel bevat. De tweede gereserveerde eigenschap (ClientEncryptionMetadata2)\_is een binaire eigenschap die de informatie bevat over de eigenschappen die zijn versleuteld. De informatie in deze\_tweede eigenschap (ClientEncryptionMetadata2) is zelf versleuteld.
4. Vanwege deze extra gereserveerde eigenschappen die nodig zijn voor versleuteling, kunnen gebruikers nu slechts 250 aangepaste eigenschappen hebben in plaats van 252. De totale omvang van de entiteit moet minder dan 1 MB bedragen.

   Houd er rekening mee dat alleen tekenreekseigenschappen kunnen worden versleuteld. Als andere typen eigenschappen moeten worden versleuteld, moeten ze worden omgezet in tekenreeksen. De versleutelde tekenreeksen worden op de service opgeslagen als binaire eigenschappen en worden na dedecryptie omgezet in tekenreeksen (ruwe tekenreeksen, niet EntityProperties met type EdmType.STRING).

   Voor tabellen moeten gebruikers naast het versleutelingsbeleid ook de eigenschappen opgeven die moeten worden versleuteld. Dit kan worden gedaan door deze eigenschappen op te slaan in TableEntity-objecten met het type ingesteld op EdmType.STRING en de set true te versleutelen of de encryption_resolver_function in te stellen op het object tableservice. Een versleutelingsresolver is een functie die een partitiesleutel, rijsleutel en eigenschapsnaam neemt en een booleaan retourneert die aangeeft of die eigenschap moet worden versleuteld. Tijdens versleuteling gebruikt de clientbibliotheek deze informatie om te beslissen of een eigenschap moet worden versleuteld tijdens het schrijven naar de draad. De gemachtigde voorziet ook in de mogelijkheid van logica rond hoe eigenschappen worden versleuteld. (Als X bijvoorbeeld eigenschap A versleutelt; anders eigenschappen A en B versleutelen.) Houd er rekening mee dat het niet nodig is om deze informatie te verstrekken tijdens het lezen of opvragen van entiteiten.

### <a name="batch-operations"></a>Batchbewerkingen
Eén versleutelingsbeleid is van toepassing op alle rijen in de batch. De clientbibliotheek genereert intern een nieuwe willekeurige IV en willekeurige CEK per rij in de batch. Gebruikers kunnen er ook voor kiezen om verschillende eigenschappen voor elke bewerking in de batch te versleutelen door dit gedrag in de versleutelingsoplosser te definiëren.
Als een batch wordt gemaakt als contextmanager via de methode tableservice batch(), wordt het versleutelingsbeleid van de tabelservice automatisch toegepast op de batch. Als een batch expliciet wordt gemaakt door de constructor aan te roepen, moet het versleutelingsbeleid als parameter worden doorgegeven en ongewijzigd worden gelaten voor de levensduur van de batch.
Houd er rekening mee dat entiteiten worden versleuteld wanneer ze in de batch worden ingevoegd met behulp van het versleutelingsbeleid van de batch (entiteiten zijn NIET versleuteld op het moment dat de batch wordt vastgelegd met behulp van het versleutelingsbeleid van de tabelservice).

### <a name="queries"></a>Query's
> [!NOTE]
> Omdat de entiteiten zijn versleuteld, u geen query's uitvoeren die filteren op een versleutelde eigenschap.  Als u het probeert, zijn de resultaten onjuist, omdat de service versleutelde gegevens probeert te vergelijken met niet-versleutelde gegevens.
> 
> 
> Als u querybewerkingen wilt uitvoeren, moet u een sleutelresolver opgeven die alle sleutels in de resultaatset kan oplossen. Als een entiteit in het queryresultaat niet kan worden opgelost naar een provider, wordt er in de clientbibliotheek een fout gegenereerd. Voor elke query die serverzijdeprojecties uitvoert, voegt de clientbibliotheek standaard de speciale eigenschappen van versleutelingsmetagegevens (ClientEncryptionMetadata1\_en \_ClientEncryptionMetadata2) toe aan de geselecteerde kolommen.
> 
> [!IMPORTANT]
> Wees je bewust van deze belangrijke punten bij het gebruik van client-side encryptie:
> 
> * Wanneer u van een versleutelde blob leest of schrijft, gebruikt u hele blob-uploadopdrachten en opdrachten voor het downloaden van blobs per hele blob. Schrijf niet naar een versleutelde blob met protocolbewerkingen zoals Putblock, Put Block List, Write Pages of Clear Pages. anders u de versleutelde blob beschadigen en onleesbaar maken.
> * Voor tabellen bestaat een vergelijkbare beperking. Zorg ervoor dat u versleutelde eigenschappen niet bijwerkt zonder de versleutelingsmetagegevens bij te werken.
> * Als u metagegevens instelt op de versleutelde blob, u de versleutelingsgerelateerde metagegevens overschrijven die nodig zijn voor decryptie, omdat het instellen van metagegevens niet additief is. Dit geldt ook voor momentopnamen; vermijd het opgeven van metagegevens tijdens het maken van een momentopname van een versleutelde blob. Als metagegevens moeten worden ingesteld, moet u eerst de **get_blob_metadata-methode** aanroepen om de huidige versleutelingsmetagegevens te krijgen en gelijktijdige schrijfbewerkingen te voorkomen terwijl metagegevens worden ingesteld.
> * Schakel de **require_encryption** vlag in op het serviceobject voor gebruikers die alleen met versleutelde gegevens moeten werken. Zie hieronder voor meer info.

De opslagclientbibliotheek verwacht dat de meegeleverde KEK en de belangrijkste resolver de volgende interface implementeren. [Azure Key Vault-ondersteuning](https://azure.microsoft.com/services/key-vault/) voor Python KEK-beheer is in behandeling en wordt in deze bibliotheek geïntegreerd wanneer deze is voltooid.

## <a name="client-api--interface"></a>Client-API / Interface
Nadat een opslagserviceobject (d.w.z. blockblobservice) is gemaakt, kan de gebruiker waarden toewijzen aan de velden die een versleutelingsbeleid vormen: key_encryption_key, key_resolver_function en require_encryption. Gebruikers kunnen alleen een KEK, alleen een resolver of beide. key_encryption_key is het basissleuteltype dat wordt geïdentificeerd met behulp van een sleutel-id en dat de logica biedt voor het verpakken/uitpakken. key_resolver_function wordt gebruikt om een sleutel op te lossen tijdens het decryptieproces. Het retourneert een geldige KEK gegeven een sleutel-id. Dit biedt gebruikers de mogelijkheid om te kiezen tussen meerdere sleutels die op meerdere locaties worden beheerd.

Het KEK moet de volgende methoden implementeren om gegevens succesvol te versleutelen:

* wrap_key(cek): hiermee wordt de opgegeven CEK (bytes) verpakt met behulp van een algoritme naar keuze van de gebruiker. Geeft als resultaat de ingepakte sleutel.
* get_key_wrap_algorithm(): geeft als resultaat het algoritme dat wordt gebruikt om sleutels te verpakken.
* get_kid(): geeft als resultaat de tekenreekssleutel-id voor deze KEK.
  Het KEK moet de volgende methoden implementeren om gegevens succesvol te decoderen:
* unwrap_key(cek, algoritme): geeft als resultaat de uitgepakte vorm van de opgegeven CEK met behulp van het door tekenreeksen opgegeven algoritme.
* get_kid(): geeft als resultaat een tekenreekssleutel-id voor deze KEK.

De sleutelresolver moet op zijn minst een methode implementeren die, mits een sleutel-id, de bijbehorende KEK-implementatie van de bovenstaande interface retourneert. Alleen deze methode wordt toegewezen aan de eigenschap key_resolver_function op het serviceobject.

* Voor versleuteling wordt de sleutel altijd gebruikt en het ontbreken van een sleutel zal resulteren in een fout.
* Voor decryptie:

  * De sleutelresolver wordt aangeroepen als deze is opgegeven om de sleutel te krijgen. Als de resolver is opgegeven, maar geen toewijzing voor de sleutel-id heeft, wordt er een fout gegenereerd.
  * Als resolver niet is opgegeven, maar een sleutel is opgegeven, wordt de sleutel gebruikt als de id overeenkomt met de vereiste sleutel-id. Als de id niet overeenkomt, wordt er een fout gegenereerd.

    De versleutelingsvoorbeelden in azure.storage.samples tonen een gedetailleerder end-to-end scenario voor blobs, wachtrijen en tabellen.
      Voorbeeldimplementaties van de KEK en key resolver worden respectievelijk in de voorbeeldbestanden als KeyWrapper en KeyResolver opgenomen.

### <a name="requireencryption-mode"></a>Encryptiemodus vereisen
Gebruikers kunnen optioneel een werkingsmodus inschakelen waarin alle uploads en downloads moeten worden versleuteld. In deze modus mislukken pogingen om gegevens te uploaden zonder versleutelingsbeleid of gegevens te downloaden die niet zijn versleuteld op de service. De **require_encryption** vlag op het serviceobject bepaalt dit gedrag.

### <a name="blob-service-encryption"></a>Blob-serviceversleuteling
Stel de velden versleutelingsbeleid in op het object blockblobservice. Al het andere wordt intern door de klantenbibliotheek afgehandeld.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Versleuteling van wachtrijservice
Stel de velden versleutelingsbeleid in op het object queueservice. Al het andere wordt intern door de klantenbibliotheek afgehandeld.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>Versleuteling van tabelservice
Naast het maken van een versleutelingsbeleid en het instellen ervan op aanvraagopties, moet u een **encryption_resolver_function** op de **tabelservice**opgeven of het kenmerk Versleutelen instellen op de EntiteitProperty.

### <a name="using-the-resolver"></a>De resolver gebruiken

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.


def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False


# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Kenmerken gebruiken
Zoals hierboven vermeld, kan een eigenschap worden gemarkeerd voor versleuteling door deze op te slaan in een Object EntityProperty en het versleutelveld in te stellen.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Versleuteling en prestaties
Houd er rekening mee dat het versleutelen van uw opslaggegevens leidt tot extra algemene kosten voor prestaties. De inhoudssleutel en IV moeten worden gegenereerd, de inhoud zelf moet worden versleuteld en extra metagegevens moeten worden geformatteerd en geüpload. Deze overhead is afhankelijk van de hoeveelheid gegevens die wordt versleuteld. We raden klanten aan om hun applicaties altijd te testen op prestaties tijdens de ontwikkeling.

## <a name="next-steps"></a>Volgende stappen
* Het [Azure Storage Client Library for Java PyPi-pakket downloaden](https://pypi.python.org/pypi/azure-storage)
* De [Azure Storage Client Library for Python Source Code downloaden van GitHub](https://github.com/Azure/azure-storage-python)
