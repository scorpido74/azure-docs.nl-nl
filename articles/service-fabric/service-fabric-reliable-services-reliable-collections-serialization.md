---
title: Serialisatie van objecten betrouwbare verzameling
description: Meer informatie over azure servicefabric betrouwbare verzamelingen maken objectserialisatie, inclusief de standaardstrategie en hoe u aangepaste serialisatie definieert.'
ms.topic: conceptual
ms.date: 5/8/2017
ms.openlocfilehash: 666e1bb45a9c75ee143f15a0d871d6ae1408eca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639544"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serialisatie van betrouwbare verzamelingsobjecten in Azure Service Fabric
Betrouwbare collecties repliceren en blijven hun artikelen repliceren om ervoor te zorgen dat ze duurzaam zijn in machinestoringen en stroomuitval.
Zowel om items te repliceren als te blijven bestaan, moet Reliable Collections ze serialiseren.

Reliable Collections' krijgt de juiste serializer voor een bepaald type van Reliable State Manager.
Reliable State Manager bevat ingebouwde serializers en maakt het mogelijk aangepaste serializers te registreren voor een bepaald type.

## <a name="built-in-serializers"></a>Ingebouwde serialisators

Reliable State Manager bevat ingebouwde serializer voor sommige veelvoorkomende typen, zodat ze standaard efficiënt kunnen worden geserialiseerd. Voor andere typen valt Reliable State Manager terug om de [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx)te gebruiken.
Ingebouwde serialisators zijn efficiënter omdat ze weten dat hun typen niet kunnen veranderen en ze hoeven geen informatie over het type zoals de typenaam op te nemen.

Reliable State Manager heeft ingebouwde serializer voor de volgende typen: 
- GUID
- Booleaanse waarde
- Byte
- sbyte
- byte[]
- Char
- tekenreeks
- decimal
- double
- float
- int
- uint
- long
- ulong
- Korte
- ushort ushort

## <a name="custom-serialization"></a>Aangepaste serialisatie

Aangepaste serialisators worden vaak gebruikt om de prestaties te verhogen of om de gegevens te versleutelen via de draad en op de schijf. Onder andere redenen, aangepaste serializers zijn vaak efficiënter dan generieke serializer omdat ze niet nodig om informatie over het type serialiseren. 

[IReliableStateManager.TryAddStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) wordt gebruikt om een aangepaste serialisator voor het gegeven type T te registreren. Deze registratie moet plaatsvinden in de bouw van de StatefulServiceBase om ervoor te zorgen dat voordat het herstel begint, alle betrouwbare collecties toegang hebben tot de relevante serializer om hun aanhoudende gegevens te lezen.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Aangepaste serialisators krijgen voorrang op ingebouwde serialisators. Wanneer bijvoorbeeld een aangepaste serializer voor int wordt geregistreerd, wordt deze gebruikt om gehele getallen te serialiseren in plaats van de ingebouwde serializer voor int.

### <a name="how-to-implement-a-custom-serializer"></a>Een aangepaste serialisator implementeren

Een aangepaste serializer moet de [IStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) interface implementeren.

> [!NOTE]
> IStateSerializer\<T> bevat een overbelasting voor Write and Read die een extra T-basiswaarde in zich opneemt. Deze API is voor differentiële serialisatie. Momenteel is differentiële serialisatie functie niet blootgesteld. Vandaar dat deze twee overbelastingen niet worden aangeroepen totdat differentiële serialisatie is blootgesteld en ingeschakeld.

Hieronder volgt een voorbeeld van een aangepast type ordersleutel dat vier eigenschappen bevat

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Hieronder volgt een voorbeeld implementatie\<van IStateSerializer OrderKey>.
Houd er rekening mee dat lees- en schrijfoverbelasting en basiswaarde in zich opnemen, en noemen hun respectieve overbelasting voor forwards-compatibiliteit.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Uitbreidbaarheid
In een [upgrade met rollende toepassingen](service-fabric-application-upgrade.md)wordt de upgrade toegepast op een subset van knooppunten, één upgradedomein tegelijk. Tijdens dit proces, zullen sommige upgrade domeinen worden op de nieuwere versie van uw toepassing, en sommige upgrade domeinen zullen worden op de oudere versie van uw toepassing. Tijdens de implementatie moet de nieuwe versie van uw toepassing de oude versie van uw gegevens kunnen lezen en de oude versie van uw toepassing moet de nieuwe versie van uw gegevens kunnen lezen. Als de gegevensindeling niet vooruit- en achterwaarts compatibel is, kan de upgrade mislukken of erger nog, gegevens kunnen verloren gaan of beschadigd raken.

Als u ingebouwde serializer gebruikt, hoeft u zich geen zorgen te maken over compatibiliteit.
Als u echter een aangepaste serializer of de DataContractSerializer gebruikt, moeten de gegevens oneindig achterwaarts en voorwaarts compatibel zijn.
Met andere woorden, elke versie van serializer moet in staat zijn om elke versie van het type te serialiseren en te de-serialiseren.

Gebruikers van gegevenscontracten moeten de goed gedefinieerde versieregels volgen voor het toevoegen, verwijderen en wijzigen van velden. Data Contract heeft ook ondersteuning voor het omgaan met onbekende velden, aansluiten bij de serialisatie en deserialisatie proces, en het omgaan met klasse erfenis. Zie [Gegevenscontract gebruiken](https://msdn.microsoft.com/library/ms733127.aspx)voor meer informatie .

Gebruikers van aangepaste serialisator moeten zich houden aan de richtlijnen van de serialisator die ze gebruiken om ervoor te zorgen dat het compatibel is met de serie.
Veelvoorkomende manier om alle versies te ondersteunen, is het toevoegen van grootte-informatie aan het begin en alleen het toevoegen van optionele eigenschappen.
Op deze manier kan elke versie zo veel lezen dat het kan en springen over het resterende deel van de stream.

## <a name="next-steps"></a>Volgende stappen
  * [Serialisatie en upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Ontwikkelaarsreferentie voor betrouwbare verzamelingen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Upgraden van uw toepassing Met Visual Studio](service-fabric-application-upgrade-tutorial.md) u een applicatie-upgrade doorlopen met Visual Studio.
  * [Het upgraden van uw toepassing met Powershell](service-fabric-application-upgrade-tutorial-powershell.md) leidt u door een applicatie-upgrade met PowerShell.
  * Bepaal hoe uw toepassing wordt bijgewerkt met [upgradeparameters.](service-fabric-application-upgrade-parameters.md)
  * Meer informatie over het gebruik van geavanceerde functionaliteit tijdens het upgraden van uw toepassing door te verwijzen naar [geavanceerde onderwerpen.](service-fabric-application-upgrade-advanced.md)
  * Los veelvoorkomende problemen op in toepassingsupgrades door te verwijzen naar de stappen in [het oplossen van toepassingsupgrades.](service-fabric-application-upgrade-troubleshooting.md)
