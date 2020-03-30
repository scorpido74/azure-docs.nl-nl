---
title: Werken met betrouwbare verzamelingen
description: Ontdek de aanbevolen procedures voor het werken met betrouwbare verzamelingen binnen een Azure Service Fabric-toepassing.
ms.topic: conceptual
ms.date: 02/22/2019
ms.openlocfilehash: 4a1f48d9523e5d753c222f0526e210a30e1927e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645970"
---
# <a name="working-with-reliable-collections"></a>Werken met betrouwbare verzamelingen
Service Fabric biedt een stateful programmeermodel dat beschikbaar is voor .NET-ontwikkelaars via Betrouwbare Collecties. Met name Service Fabric biedt betrouwbare woordenboek en betrouwbare wachtrijklassen. Wanneer u deze klassen gebruikt, wordt uw status verdeeld (voor schaalbaarheid), gerepliceerd (voor beschikbaarheid) en afgehandeld binnen een partitie (voor ACID-semantiek). Laten we eens kijken naar een typisch gebruik van een betrouwbaar woordenboek object en zien wat het eigenlijk doet.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
}
```

Voor alle bewerkingen op betrouwbare woordenboekobjecten (met uitzondering van ClearAsync, die niet onuitvoerbaar is), is een ITransaction-object vereist. Dit object heeft er alle wijzigingen aan gekoppeld die u probeert aan te brengen in een betrouwbaar woordenboek en/of betrouwbare wachtrijobjecten binnen één partitie. U verwerft een ITransaction-object door de CreateTransaction-methode van de partitie aan te roepen.

In de bovenstaande code wordt het iTransaction-object doorgegeven aan de AddAsync-methode van een betrouwbaar woordenboek. Intern, woordenboek methoden die een sleutel te accepteren nemen een lezer / schrijver slot in verband met de sleutel. Als de methode de waarde van de sleutel wijzigt, neemt de methode een schrijfslot op de sleutel en als de methode alleen leest van de waarde van de sleutel, wordt een leesslot op de sleutel genomen. Aangezien AddAsync de waarde van de sleutel wijzigt aan de nieuwe, doorgegeven waarde, wordt het schrijfslot van de sleutel genomen. Dus, als 2 (of meer) threads proberen om waarden toe te voegen met dezelfde sleutel op hetzelfde moment, een draad zal verwerven van de schrijfvergrendeling, en de andere threads zal blokkeren. Standaard blokkeren methoden maximaal 4 seconden om het slot te verkrijgen; na 4 seconden gooien de methoden een TimeoutException. Er bestaat overbelasting van methoden, zodat u een expliciete time-outwaarde doorgeven als u dat liever hebt.

Meestal schrijft u uw code om te reageren op een TimeoutException door deze te vangen en de hele bewerking opnieuw uit te proberen (zoals in de bovenstaande code). In mijn eenvoudige code, ik ben gewoon bellen Task.Delay passeren 100 milliseconden elke keer. Maar in werkelijkheid, zou je beter af met behulp van een soort van exponentiële back-off vertraging plaats.

Zodra het slot is aangeschaft, voegt AddAsync de verwijzingen naar sleutels en waardeobjecten toe aan een intern tijdelijk woordenboek dat is gekoppeld aan het ITransaction-object. Dit wordt gedaan om u te voorzien van lees-je-eigen-schrijft semantiek. Dat wil zeggen, nadat u AddAsync hebt gebeld, zal een latere oproep naar TryGetValueAsync (met hetzelfde ITransaction-object) de waarde retourneren, zelfs als u de transactie nog niet hebt gepleegd. AddAsync serialiseert vervolgens uw sleutel- en waardeobjecten om arrays te byteen en voegt deze bytearrays toe aan een logboekbestand op het lokale knooppunt. Ten slotte stuurt AddAsync de bytearrays naar alle secundaire replica's, zodat ze dezelfde sleutel-/waarde-informatie hebben. Hoewel de sleutel-/waarde-informatie naar een logboekbestand is geschreven, wordt de informatie niet beschouwd als onderdeel van het woordenboek totdat de transactie waaraan ze zijn gekoppeld, is vastgelegd.

In de bovenstaande code verbindt de call to CommitAsync alle bewerkingen van de transactie. Specifiek, het voegt commit informatie toe aan het logbestand op het lokale knooppunt en stuurt ook de commit record naar alle secundaire replica's. Zodra een quorum (meerderheid) van de replica's heeft geantwoord, worden alle gegevenswijzigingen als permanent beschouwd en worden alle vergrendelingen die zijn gekoppeld aan sleutels die via het ITransaction-object zijn gemanipuleerd, vrijgegeven, zodat andere threads/transacties dezelfde sleutels en hun waarden kunnen manipuleren.

Als CommitAsync niet wordt aangeroepen (meestal vanwege een uitzondering die wordt gegooid), wordt het ITransaction-object verwijderd. Bij het verwijderen van een niet-vastgelegd ITransaction-object voegt Service Fabric de aborteergegevens toe aan het logboekbestand van het lokale knooppunt en hoeft er niets naar een van de secundaire replica's te worden verzonden. En dan, alle sloten in verband met sleutels die werden gemanipuleerd via de transactie worden vrijgegeven.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Veelvoorkomende valkuilen en hoe ze te vermijden
Nu u begrijpt hoe de betrouwbare collecties intern werken, laten we eens kijken naar een aantal veel voorkomende misbruiken van hen. Zie de code hieronder:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Wanneer u met een gewone .NET-woordenboek werkt, u een sleutel/waarde aan het woordenboek toevoegen en vervolgens de waarde van een eigenschap wijzigen (zoals LastLogin). Deze code werkt echter niet goed met een betrouwbaar woordenboek. Onthoud uit de eerdere discussie dat de aanroep naar AddAsync de sleutel-/waardeobjecten serialiseert om arrays te byteen en vervolgens de arrays opslaat in een lokaal bestand en ze ook naar de secundaire replica's stuurt. Als u later een eigenschap wijzigt, verandert dit alleen de waarde van de eigenschap in het geheugen; het heeft geen invloed op het lokale bestand of de gegevens die naar de replica's worden verzonden. Als het proces crasht, wat in het geheugen wordt weggegooid. Wanneer een nieuw proces wordt gestart of als een andere replica primair wordt, is de oude eigenschapswaarde wat er beschikbaar is.

Ik kan niet genoeg benadrukken hoe gemakkelijk het is om het soort fout hierboven getoond. En, zult u alleen leren over de fout als / wanneer het proces naar beneden gaat. De juiste manier om de code te schrijven is gewoon om de twee regels te keren:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Hier is een ander voorbeeld met een veel voorkomende fout:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Nogmaals, met reguliere .NET woordenboeken, de bovenstaande code werkt prima en is een gemeenschappelijk patroon: de ontwikkelaar maakt gebruik van een sleutel op te zoeken een waarde. Als de waarde bestaat, wijzigt de ontwikkelaar de waarde van een eigenschap. Echter, met betrouwbare collecties, deze code vertoont hetzelfde probleem als reeds besproken: **je moet niet wijzigen van een object als je eenmaal hebt gegeven aan een betrouwbare collectie.**

De juiste manier om een waarde in een betrouwbare verzameling bij te werken, is om een verwijzing naar de bestaande waarde te krijgen en het object waarnaar wordt verwezen in deze referentie onveranderlijk te beschouwen. Maak vervolgens een nieuw object dat een exacte kopie is van het oorspronkelijke object. U nu de status van dit nieuwe object wijzigen en het nieuwe object in de verzameling schrijven, zodat het wordt geserialiseerd om arrays te byte, toegevoegd aan het lokale bestand en naar de replica's wordt verzonden. Na het aanbrengen van de wijziging(en) hebben de in-memory objecten, het lokale bestand en alle replica's exact dezelfde status. Alles is goed!

De onderstaande code toont de juiste manier om een waarde bij te werken in een betrouwbare verzameling:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Onveranderlijke gegevenstypen definiëren om fout van programmeurs te voorkomen
Idealiter willen we dat de compiler fouten rapporteert wanneer u per ongeluk code produceert die de status muteert van een object dat u als onveranderlijk moet beschouwen. Maar de C# compiler heeft niet de mogelijkheid om dit te doen. Om potentiële programmeurbugs te voorkomen, raden we u ten zeerste aan om de typen die u gebruikt met betrouwbare verzamelingen te definiëren als onveranderlijke typen. Concreet betekent dit dat u zich houdt aan kernwaardetypen (zoals getallen [Int32, UInt64, enz.], DateTime, Guid, TimeSpan en dergelijke). U string ook gebruiken. Het is het beste om te voorkomen dat collectie eigenschappen als serialiseren en deserialiseren ze kunnen vaak pijn doen prestaties. Als u echter verzameleigenschappen wilt gebruiken, raden we u ten zeerste het gebruik van . DE ONveranderlijke collectiesbibliotheek van NET[(System.Collections.Immutable).](https://www.nuget.org/packages/System.Collections.Immutable/) Deze bibliotheek is beschikbaar https://nuget.orgom te downloaden van . We raden u ook aan uw lessen af te dichten en velden waar mogelijk alleen-lezen te maken.

Het onderstaande userinfo-type laat zien hoe u een onveranderlijk type definiëren dat gebruikmaakt van bovengenoemde aanbevelingen.

```csharp
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Het type ItemId is ook een onveranderlijk type zoals hier wordt weergegeven:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Schemaversiebeheer (upgrades)
Intern serialiseren betrouwbare verzamelingen uw objecten met behulp van . NET's DataContractSerializer. De geserialiseerde objecten blijven bestaan naar de lokale schijf van de primaire replica en worden ook naar de secundaire replica's verzonden. Naarmate uw service ouder wordt, wilt u waarschijnlijk het soort gegevens (schema) wijzigen dat uw service vereist. Benader het versiebeheer van uw gegevens met grote zorg. Eerst en vooral moet u altijd in staat zijn om oude gegevens te deserialiseren. Concreet betekent dit dat uw deserialisatiecode oneindig achterwaarts compatibel moet zijn: versie 333 van uw servicecode moet kunnen werken op gegevens die 5 jaar geleden in een betrouwbare verzameling zijn geplaatst via versie 1 van uw servicecode.

Bovendien wordt servicecode één upgradedomein tegelijk geüpgraded. Tijdens een upgrade worden dus twee verschillende versies van uw servicecode tegelijkertijd uitgevoerd. U moet voorkomen dat de nieuwe versie van uw servicecode het nieuwe schema gebruikt, omdat oude versies van uw servicecode mogelijk het nieuwe schema niet kunnen verwerken. Indien mogelijk moet u elke versie van uw service ontwerpen om compatibel te zijn met één versie. Concreet betekent dit dat V1 van uw servicecode alle schema-elementen moet kunnen negeren die niet expliciet worden verwerkt. Het moet echter in staat zijn om alle gegevens op te slaan die het niet expliciet kent en deze terug te schrijven bij het bijwerken van een woordenboeksleutel of -waarde.

> [!WARNING]
> Hoewel u het schema van een sleutel wijzigen, moet u ervoor zorgen dat de hashcode en de is-algoritmen van uw sleutel stabiel zijn. Als u de werking van een van deze algoritmen wijzigt, u de sleutel in het betrouwbare woordenboek nooit meer opzoeken.
> .NET Strings kunnen worden gebruikt als een sleutel, maar gebruik de tekenreeks zelf als de sleutel - gebruik het resultaat van String.GetHashCode niet als de sleutel.

U ook een zogenaamde tweeupgrade uitvoeren. Met een upgrade in twee fasen upgradet u uw service van V1 naar V2: V2 bevat de code die weet hoe om te gaan met de nieuwe schemawijziging, maar deze code wordt niet uitgevoerd. Wanneer de V2-code V1-gegevens leest, werkt deze erop en schrijft het V1-gegevens. Nadat de upgrade is voltooid in alle upgradedomeinen, u op de een of andere manier naar de lopende V2-exemplaren aangeven dat de upgrade is voltooid. (Een manier om dit signaal is de uitrol van een configuratie-upgrade; dit is wat maakt dit een twee-fase upgrade.) Nu kunnen de V2-exemplaren V1-gegevens lezen, converteren naar V2-gegevens, ernaar werken en het uitschrijven als V2-gegevens. Wanneer andere instanties V2-gegevens lezen, hoeven ze deze niet te converteren, ze werken er gewoon op en schrijven V2-gegevens uit.

## <a name="next-steps"></a>Volgende stappen
Zie [Forward-Compatible Data Contracts](https://msdn.microsoft.com/library/ms731083.aspx) voor meer informatie over het maken van forward compatible data contracts

Zie [Data Contract Versioning](https://msdn.microsoft.com/library/ms731138.aspx) voor aanbevolen procedures voor versiecontracten

Zie [Version-Tolerant Serialization Callbacks](https://msdn.microsoft.com/library/ms733734.aspx) voor meer informatie over het implementeren van versietolerante gegevenscontracten

[Zie IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx) voor meer informatie over het bieden van een gegevensstructuur die kan samenwerken tussen meerdere versies