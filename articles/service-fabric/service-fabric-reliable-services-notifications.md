---
title: Meldingen van betrouwbare services
description: Conceptuele documentatie voor meldingen van servicefabric betrouwbare services voor betrouwbaar staatsbeheer en betrouwbaar woordenboek
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: 1f3239ea1da252ccd84c6572b562756c8fd1677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639561"
---
# <a name="reliable-services-notifications"></a>Meldingen van betrouwbare services
Met meldingen kunnen clients de wijzigingen bijhouden die worden aangebracht in een object waarin ze geïnteresseerd zijn. Twee typen objecten ondersteunen meldingen: *Reliable State Manager* en Reliable *Dictionary*.

Veelvoorkomende redenen voor het gebruik van meldingen zijn:

* Gematerialiseerde weergaven bouwen, zoals secundaire indexen of geaggregeerde gefilterde weergaven van de status van de replica. Een voorbeeld is een gesorteerde index van alle sleutels in Reliable Dictionary.
* Het verzenden van bewakingsgegevens, zoals het aantal gebruikers dat in het afgelopen uur is toegevoegd.

Meldingen worden geactiveerd als onderdeel van het toepassen van bewerkingen. Daarom moeten meldingen zo snel mogelijk worden afgehandeld en moeten synchrone gebeurtenissen geen dure bewerkingen bevatten.

## <a name="reliable-state-manager-notifications"></a>Betrouwbare state manager-meldingen
Reliable State Manager geeft meldingen voor de volgende gebeurtenissen:

* Transactie
  * Doorvoeren
* Staatsmanager
  * Opnieuw bouwen
  * Toevoeging van een betrouwbare staat
  * Verwijdering van een betrouwbare staat

Reliable State Manager volgt de huidige transacties aan boord. De enige wijziging in de transactiestatus waardoor een melding wordt geactiveerd, is een transactie die wordt vastgelegd.

Reliable State Manager onderhoudt een verzameling betrouwbare staten zoals Reliable Dictionary en Reliable Queue. Reliable State Manager vuurt meldingen af wanneer deze verzameling verandert: een betrouwbare status wordt toegevoegd of verwijderd of de hele verzameling wordt opnieuw opgebouwd.
De Reliable State Manager-collectie wordt in drie gevallen opnieuw opgebouwd:

* Herstel: wanneer een replica wordt gestart, herstelt deze de vorige status van de schijf. Aan het einde van het herstel gebruikt het **NotifyStateManagerChangedEventArgs** om een gebeurtenis af te vuren die de set herstelde betrouwbare statussen bevat.
* Volledige kopie: Voordat een replica kan deelnemen aan de configuratieset, moet deze worden gebouwd. Soms vereist dit een volledige kopie van de status van Reliable State Manager van de primaire replica die moet worden toegepast op de niet-actieve secundaire replica. Reliable State Manager op de secundaire replica gebruikt **NotifyStateManagerChangedEventArgs** om een gebeurtenis af te vuren die de set betrouwbare statussen bevat die deze heeft verkregen van de primaire replica.
* Herstel: In scenario's voor noodherstel kan de status van de replica worden hersteld vanuit een back-up via **RestoreAsync.** In dergelijke gevallen gebruikt Reliable State Manager op de primaire replica **NotifyStateManagerChangedEventArgs** om een gebeurtenis af te vuren die de set betrouwbare statussen bevat die is hersteld vanuit de back-up.

Als u zich wilt registreren voor transactiemeldingen en/of meldingen van staatsbeheerbeheer, moet u zich registreren bij de gebeurtenissen **TransactionChanged** of **StateManagerChanged** op Reliable State Manager. Een gemeenschappelijke plaats om te registreren bij deze gebeurtenis handlers is de constructor van uw stateful service. Wanneer u zich registreert bij de constructor, mist u geen melding die wordt veroorzaakt door een wijziging tijdens de levensduur van **IReliableStateManager.**

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

De **gebeurtenishandler TransactionChanged** gebruikt **NotifyTransactionChangedEventArgs** om details over de gebeurtenis te geven. Het bevat de eigenschap actie (bijvoorbeeld **NotifyTransactionChangedAction.Commit)** die het type wijziging aangeeft. Het bevat ook de transactieeigenschap die een verwijzing biedt naar de transactie die is gewijzigd.

> [!NOTE]
> Vandaag de dag worden **transactionchanged** gebeurtenissen alleen verhoogd als de transactie is vastgelegd. De actie is dan gelijk aan **NotifyTransactionChangedAction.Commit**. Maar in de toekomst kunnen gebeurtenissen worden verhoogd voor andere soorten wijzigingen in de transactiestatus. We raden u aan de actie te controleren en de gebeurtenis alleen te verwerken als deze wordt verwacht.
> 
> 

Hieronder volgt een voorbeeld **transactieveranderde** gebeurtenishandler.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

De **gebeurtenishandler StateManagerChanged** gebruikt **NotifyStateManagerChangedEventArgs** om details over de gebeurtenis te geven.
**NotifyStateManagerChangedEventArgs** heeft twee subklassen: **NotifyStateManagerRebuildEventArgs** en **NotifyStateManagerSingleEntityChangedEventArgs**.
U gebruikt de eigenschap action in **NotifyStateManagerChangedEventArgs** om **NotifyStateManagerChangedEventArgs** naar de juiste subklasse te casten:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **MeldingStateManagerChangedAction.Add** and **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Hieronder volgt een voorbeeld **van StateManagerChanged-meldingshandler.**

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Meldingen over betrouwbaar woordenboek
Reliable Dictionary biedt meldingen voor de volgende gebeurtenissen:

* Opnieuw opbouwen: aangeroepen wanneer **ReliableDictionary** de status heeft hersteld van een herstelde of gekopieerde lokale status of back-up.
* Duidelijk: Gebeld wanneer de status van **ReliableDictionary** is gewist via de **ClearAsync-methode.**
* Toevoegen: Aangeroepen wanneer een item is toegevoegd aan **ReliableDictionary**.
* Update: Gebeld wanneer een item in **IReliableDictionary** is bijgewerkt.
* Verwijderen: Aangeroepen wanneer een item in **IReliableDictionary** is verwijderd.

Als u meldingen van betrouwbaar woordenboek wilt ontvangen, moet u zich registreren bij de **gebeurtenishandler DictionaryChanged** op **IReliableDictionary**. Een gemeenschappelijke plaats om te registreren bij deze gebeurtenishandlers is in de **reliablestatemanager.StateManagerChanged** add notification.
Als u zich registreert wanneer **IReliableDictionary** wordt toegevoegd aan **IReliableStateManager,** u geen meldingen meer ontvangen.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** is de voorbeeldmethode die de voorgaande **OnStateManagerChangedHandler** aanroept.
> 
> 

De voorgaande code stelt de **IReliableNotificationAsyncCallback-interface** in, samen met **DictionaryChanged**. Omdat **NotifyDictionaryRebuildEventArgs** een **IAsyncEnumerable-interface** bevat - die asynchroon moet worden opgesomd - worden herstelmeldingen geactiveerd via **RebuildNotificationAsyncCallback** in plaats van **OnDictionaryChangedHandler**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> In de voorgaande code wordt als onderdeel van de verwerking van de herstelmelding eerst de gehandhaafde geaggregeerde status gewist. Omdat de betrouwbare verzameling wordt herbouwd met een nieuwe status, zijn alle eerdere meldingen niet relevant.
> 
> 

De **gebeurtenishandler DictionaryChanged** gebruikt **NotifyDictionaryChangedEventArgs** om details over de gebeurtenis te geven.
**NotifyDictionaryChangedEventArgs** heeft vijf subklassen. Gebruik de eigenschap actie in **NotifyDictionaryChangedEventArgs** om **NotifyDictionaryChangedEventArgs** naar de juiste subklasse te casten:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add:** **NotifyDictionaryItemAddedEventArgs** NotifyDictionaryChangedAction.Add : NotifyDictionaryItemAddedEventArgs NotifyDictionaryChangedAction.Add : NotifyDictionaryItemAddedEventArgs NotifyDictionary
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Aanbevelingen
* *Voltooi* de meldingsgebeurtenissen zo snel mogelijk.
* *Voer geen* dure bewerkingen uit (bijvoorbeeld I/O-bewerkingen) als onderdeel van synchrone gebeurtenissen.
* *Controleer* het actietype voordat u de gebeurtenis verwerkt. In de toekomst kunnen nieuwe actietypen worden toegevoegd.

Hier zijn een aantal dingen om in gedachten te houden:

* Meldingen worden afgevuurd als onderdeel van de uitvoering van een bewerking. Een herstelmelding wordt bijvoorbeeld geactiveerd als de laatste stap van een herstelbewerking. Een herstel wordt pas voltooid nadat de meldingsgebeurtenis is verwerkt.
* Omdat meldingen worden geactiveerd als onderdeel van de toepassingsbewerkingen, zien clients alleen meldingen voor lokaal vastgelegde bewerkingen. En omdat bewerkingen gegarandeerd alleen lokaal worden vastgelegd (met andere woorden, gelogd), kunnen ze in de toekomst al dan niet ongedaan worden gemaakt.
* Op het redopad wordt voor elke toegepaste bewerking één melding geactiveerd. Dit betekent dat als transactie T1 Create(X), Delete(X) en Create(X) bevat, u één melding krijgt voor het maken van X, één voor het verwijderen en één voor het opnieuw maken, in die volgorde.
* Voor transacties die meerdere bewerkingen bevatten, worden bewerkingen toegepast in de volgorde waarin ze zijn ontvangen op de primaire replica van de gebruiker.
* Als onderdeel van het verwerken van valse voortgang kunnen sommige bewerkingen ongedaan worden gemaakt. Meldingen worden verhoogd voor dergelijke ongedaan maken operaties, het rollen van de status van de replica terug naar een stabiel punt. Een belangrijk verschil van meldingen ongedaan maken is dat gebeurtenissen met dubbele sleutels worden samengevoegd. Als transactie T1 bijvoorbeeld ongedaan wordt gemaakt, ziet u één melding naar Delete(X).

## <a name="next-steps"></a>Volgende stappen
* [Betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
* [Betrouwbare services snel van start](service-fabric-reliable-services-quick-start.md)
* [Back-up en herstel van betrouwbare services (herstel na noodgevallen)](service-fabric-reliable-services-backup-restore.md)
* [Ontwikkelaarsreferentie voor betrouwbare verzamelingen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

