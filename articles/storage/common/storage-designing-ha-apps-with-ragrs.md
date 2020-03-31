---
title: Ontwerpen van zeer beschikbare toepassingen met behulp van georedundante opslag
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van georedundante opslag voor leestoegang om een zeer beschikbare toepassing te ontwerpen die flexibel genoeg is om storingen aan te pakken.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 592be1710893791e80dfe4b20e1323e789b33e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157089"
---
# <a name="designing-highly-available-applications-using-read-access-geo-redundant-storage"></a>Ontwerpen van zeer beschikbare toepassingen met behulp van georedundante opslag met leestoegang

Een gemeenschappelijk kenmerk van cloudgebaseerde infrastructuren zoals Azure Storage is dat ze een zeer beschikbaar platform bieden voor hostingtoepassingen. Ontwikkelaars van cloudgebaseerde applicaties moeten zorgvuldig overwegen hoe ze dit platform kunnen gebruiken om zeer beschikbare applicaties aan hun gebruikers te leveren. In dit artikel wordt gespitst op hoe ontwikkelaars een van azure's georedundante replicatieopties kunnen gebruiken om ervoor te zorgen dat hun Azure Storage-toepassingen in hoge mate beschikbaar zijn.

Opslagaccounts die zijn geconfigureerd voor georedundante replicatie, worden synchroon gerepliceerd in het primaire gebied en vervolgens asynchroon gerepliceerd naar een secundair gebied dat honderden kilometers verwijderd is. Azure Storage biedt twee typen georedundante replicatie:

* [Geo-zone-redundante opslag (GZRS) (preview)](storage-redundancy.md) biedt replicatie voor scenario's die zowel hoge beschikbaarheid als maximale duurzaamheid vereisen. Gegevens worden synchroon gerepliceerd in drie Azure-beschikbaarheidszones in het primaire gebied met behulp van zoneredundante opslag (ZRS) en vervolgens asynchroon gerepliceerd naar het secundaire gebied. Voor leestoegang tot gegevens in de secundaire regio u geozoneredundante opslag (RA-GZRS) inschakelen voor leestoegang tot gegevens in de secundaire regio.
* [Georedundante opslag (GRS)](storage-redundancy.md) biedt cross-regionale replicatie om te beschermen tegen regionale storingen. Gegevens worden drie keer synchroon gerepliceerd in het primaire gebied met behulp van lokaal redundante opslag (LRS) en vervolgens asynchroon gerepliceerd naar het secundaire gebied. Voor leestoegang tot gegevens in de secundaire regio u georedundante opslag (RA-GRS) gebruiken voor leestoegang tot gegevens in de secundaire regio.

In dit artikel ziet u hoe u uw toepassing ontwerpt om een storing in de primaire regio af te handelen. Als het primaire gebied niet meer beschikbaar is, kan uw toepassing zich aanpassen om leesbewerkingen uit te voeren ten opzichte van het secundaire gebied. Zorg ervoor dat uw opslagaccount is geconfigureerd voor RA-GRS of RA-GZRS voordat u aan de slag gaat.

Zie [Bedrijfscontinuïteit en disaster recovery (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor informatie over welke primaire regio's zijn gekoppeld.

Er zijn codefragmenten opgenomen in dit artikel, en een link naar een compleet voorbeeld aan het einde dat u downloaden en uitvoeren.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Overwegingen voor het ontwerp van toepassingen bij het lezen van de secundaire

Het doel van dit artikel is om u te laten zien hoe u een toepassing die zal blijven functioneren (zij het in een beperkte capaciteit) zelfs in het geval van een grote ramp in het primaire datacenter te ontwerpen. U uw toepassing zo ontwerpen dat tijdelijke of langlopende problemen worden opgelost door uit het secundaire gebied te lezen wanneer er een probleem is dat het lezen vanuit de primaire regio verstoort. Wanneer de primaire regio weer beschikbaar is, kan uw toepassing terugkeren naar het lezen vanuit de primaire regio.

Houd rekening met deze belangrijke punten bij het ontwerpen van uw aanvraag voor RA-GRS of RA-GZRS:

* Azure Storage onderhoudt een alleen-lezen kopie van de gegevens die u opslaat in uw primaire regio in een secundaire regio. Zoals hierboven vermeld, bepaalt de opslagservice de locatie van de secundaire regio.

* De alleen-lezen kopie komt [uiteindelijk overeen](https://en.wikipedia.org/wiki/Eventual_consistency) met de gegevens in het primaire gebied.

* Voor blobs, tabellen en wachtrijen u het secundaire gebied opvragen voor een waarde *voor laatste synchronisatietijd* die u vertelt wanneer de laatste replicatie van het primaire naar het secundaire gebied heeft plaatsgevonden. (Dit wordt momenteel niet ondersteund voor Azure Files, waarvoor op dit moment geen RA-GRS-redundantie is.)

* U de clientbibliotheek voor opslag gebruiken om gegevens te lezen en te schrijven in het primaire of secundaire gebied. U leesverzoeken ook automatisch omleiden naar het secundaire gebied als een leesverzoek uitgaat naar de primaire regio.

* Als het primaire gebied niet meer beschikbaar is, u een failoveraccount starten. Wanneer u niet naar het secundaire gebied gaat, worden de DNS-vermeldingen die naar het primaire gebied verwijzen, gewijzigd om naar het secundaire gebied te verwijzen. Nadat de failover is voltooid, wordt de schrijftoegang hersteld voor GRS- en RA-GRS-accounts. Zie [Failovervoor rampherstel en opslagaccount (preview) in Azure Storage](storage-disaster-recovery-guidance.md)voor meer informatie.

> [!NOTE]
> Door klanten beheerde accountfailover (preview) is nog niet beschikbaar in regio's die GZRS/RA-GZRS ondersteunen, dus klanten kunnen momenteel geen failovergebeurtenissen voor accounts beheren met GZRS- en RA-GZRS-accounts. Tijdens de preview beheert Microsoft eventuele failovergebeurtenissen die van invloed zijn op GZRS/RA-GZRS-accounts.

### <a name="using-eventually-consistent-data"></a>Uiteindelijk consistente gegevens gebruiken

De voorgestelde oplossing gaat ervan uit dat het aanvaardbaar is om mogelijk verouderde gegevens terug te sturen naar de oproeptoepassing. Omdat gegevens in het secundaire gebied uiteindelijk consistent zijn, is het mogelijk dat de primaire regio ontoegankelijk wordt voordat een update naar het secundaire gebied is voltooid.

Stel dat uw klant een update met succes indient, maar dat het primaire gebied mislukt voordat de update wordt doorgegeven aan de secundaire regio. Wanneer de klant vraagt om de gegevens terug te lezen, ontvangt hij de verouderde gegevens uit het secundaire gebied in plaats van de bijgewerkte gegevens. Bij het ontwerpen van uw toepassing moet u beslissen of dit acceptabel is en zo ja, hoe u de klant een bericht stuurt. 

Later in dit artikel laten we zien hoe u de laatste synchronisatietijd voor de secundaire gegevens controleren om te controleren of de secundaire up-to-date is.

### <a name="handling-services-separately-or-all-together"></a>Diensten afzonderlijk of alles bij elkaar afhandelen

Hoewel het onwaarschijnlijk is, is het mogelijk dat één service niet meer beschikbaar is, terwijl de andere services nog volledig functioneel zijn. U de herpogingen en alleen-lezenmodus voor elke service afzonderlijk verwerken (blobs, wachtrijen, tabellen), of u nieuwe pogingen algemeen verwerken voor alle opslagservices samen.

Als u bijvoorbeeld wachtrijen en blobs in uw toepassing gebruikt, u besluiten om afzonderlijke code in te zetten om opnieuw te proberen fouten voor elk van deze apparaten te verwerken. Als u vervolgens een nieuwe poging van de blob-service krijgt, maar de wachtrijservice nog steeds werkt, wordt alleen het deel van uw toepassing dat blobs verwerkt, beïnvloed. Als u besluit om alle opslagservice-pogingen generiek te verwerken en een aanroep naar de blobservice een opnieuw probeerbare fout retourneert, worden aanvragen voor zowel de blobservice als de wachtrijservice beïnvloed.

Uiteindelijk hangt dit af van de complexiteit van uw toepassing. U besluiten de fouten niet per service af te handelen, maar in plaats daarvan leesaanvragen voor alle opslagservices om te leiden naar het secundaire gebied en de toepassing in de alleen-lezenmodus uit te voeren wanneer u een probleem met een opslagservice in het primaire gebied detecteert.

### <a name="other-considerations"></a>Andere overwegingen

Dit zijn de andere overwegingen die we zullen bespreken in de rest van dit artikel.

* Het verwerken van nieuwe pogingen van leesaanvragen met behulp van het patroon Van de Stroomonderbreker

* Uiteindelijk consistente gegevens en de laatste synchronisatietijd

* Testen

## <a name="running-your-application-in-read-only-mode"></a>Uw toepassing uitvoeren in de alleen-lezen modus

Als u zich wilt voorbereiden op een storing in het primaire gebied, moet u zowel mislukte leesaanvragen als mislukte updateverzoeken kunnen afhandelen (met update in dit geval inserts, updates en verwijderingen). Als het primaire gebied mislukt, kunnen leesaanvragen worden doorgestuurd naar het secundaire gebied. Updateverzoeken kunnen echter niet worden doorgestuurd naar het secundaire omdat het secundaire alleen-lezen is. Om deze reden moet u uw toepassing ontwerpen om in de alleen-lezen modus uit te voeren.

U bijvoorbeeld een vlag instellen die is ingeschakeld voordat updateaanvragen worden ingediend bij Azure Storage. Wanneer een van de updateaanvragen binnenkomt, u deze overslaan en een passend antwoord aan de klant retourneren. U zelfs bepaalde functies helemaal uitschakelen totdat het probleem is opgelost en gebruikers op de hoogte stellen dat deze functies tijdelijk niet beschikbaar zijn.

Als u besluit om fouten voor elke service afzonderlijk te behandelen, moet u ook de mogelijkheid hanteren om uw toepassing in de alleen-lezen modus per service uit te voeren. U bijvoorbeeld alleen-lezen vlaggen hebben voor elke service die kan worden ingeschakeld en uitgeschakeld. Vervolgens u de vlag op de juiste plaatsen in uw code verwerken.

In staat zijn om uw toepassing in read-only modus uit te voeren heeft een andere kant voordeel - het geeft u de mogelijkheid om beperkte functionaliteit te garanderen tijdens een grote applicatie-upgrade. U uw toepassing activeren om in de alleen-lezen modus uit te voeren en naar het secundaire datacenter te wijzen, zodat niemand toegang heeft tot de gegevens in de primaire regio terwijl u upgrades uitvoert.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Updates verwerken wanneer deze worden uitgevoerd in de alleen-lezenmodus

Er zijn veel manieren om updateaanvragen te verwerken wanneer u alleen-lezen uitvoert. We zullen dit niet uitgebreid behandelen, maar over het algemeen zijn er een paar patronen die u overweegt.

1. U op uw gebruiker reageren en hen vertellen dat u momenteel geen updates accepteert. Een contactbeheersysteem kan klanten bijvoorbeeld in staat stellen contactgegevens te openen, maar geen updates uit te voeren.

2. U uw updates in een andere regio in een wachtrij plaatsen. In dit geval schrijft u uw aanvragen voor in behandeling zijnde updateaanvragen naar een wachtrij in een andere regio en hebt u een manier om deze aanvragen te verwerken nadat het primaire datacenter weer online is gekomen. In dit scenario moet u de klant laten weten dat de gevraagde update in de wachtrij staat voor latere verwerking.

3. U uw updates naar een opslagaccount in een andere regio schrijven. Wanneer het primaire datacenter vervolgens weer online komt, u een manier hebben om deze updates samen te voegen in de primaire gegevens, afhankelijk van de structuur van de gegevens. Als u bijvoorbeeld afzonderlijke bestanden maakt met een datum-/tijdstempel in de naam, u deze bestanden terugkopiëren naar het primaire gebied. Dit werkt voor sommige workloads zoals logging en iOT-gegevens.

## <a name="handling-retries"></a>Retries verwerken

Met de Azure Storage-clientbibliotheek u bepalen welke fouten opnieuw kunnen worden geprobeerd. Een fout van 404 (resource niet gevonden) wordt bijvoorbeeld niet opnieuw geprobeerd, omdat het opnieuw proberen waarschijnlijk niet tot succes zal leiden. Aan de andere kant kan een 500-fout opnieuw worden geprobeerd omdat het een serverfout is en het probleem gewoon een tijdelijk probleem kan zijn. Bekijk voor meer informatie de [open source-code voor de klasse ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) in de .NET-opslagclientbibliotheek. (Zoek naar de Methode Voor Try-opnieuw.)

### <a name="read-requests"></a>Leesverzoeken

Leesaanvragen kunnen worden doorgestuurd naar secundaire opslag als er een probleem is met primaire opslag. Zoals hierboven vermeld in [Het gebruik van uiteindelijk consistente gegevens](#using-eventually-consistent-data), moet het aanvaardbaar zijn voor uw toepassing om mogelijk verouderde gegevens te lezen. Als u de opslagclientbibliotheek gebruikt om toegang te krijgen tot gegevens van het secundaire, u het gedrag van een leesaanvraag opnieuw proberen opgeven door een waarde voor de eigenschap **LocationMode** in te stellen op een van de volgende opties:

* **PrimaryOnly** (de standaardinstelling)

* **PrimaryThenSecondary**

* **Alleen secundair**

* **SecondaryThenPrimary**

Wanneer u de **Locatiemodus** instelt op **PrimaryThenSecondary**, als het oorspronkelijke leesverzoek mislukt op het primaire eindpunt met een fout die opnieuw kan worden geprobeerd, doet de client automatisch een ander leesverzoek naar het secundaire eindpunt. Als de fout een servertime-out is, moet de client wachten tot de time-out verloopt voordat deze een nieuwe apparaatfout van de service ontvangt.

Er zijn in principe twee scenario's om rekening mee te houden wanneer u beslist hoe u op een opnieuw probeerbare fout moet reageren:

* Dit is een geïsoleerd probleem en volgende aanvragen voor het primaire eindpunt geven geen nieuwe fout. Een voorbeeld van waar dit kan gebeuren is wanneer er een tijdelijke netwerkfout optreedt.

    In dit scenario is er geen significante prestatiesanctie in het hebben van **LocationMode** ingesteld op **PrimaryThenSecondary,** omdat dit slechts zelden gebeurt.

* Dit is een probleem met ten minste één van de opslagservices in de primaire regio en alle volgende aanvragen voor die service in de primaire regio zullen waarschijnlijk opnieuw te proberen fouten voor een bepaalde periode retourneren. Een voorbeeld hiervan is als de primaire regio volledig ontoegankelijk is.

    In dit scenario is er een prestatiestraf omdat al uw leesverzoeken eerst het primaire eindpunt proberen, wachten tot de time-out verloopt en vervolgens overschakelen naar het secundaire eindpunt.

Voor deze scenario's moet u vaststellen dat er een doorlopend probleem is met het primaire eindpunt en alle leesaanvragen rechtstreeks naar het secundaire eindpunt verzenden door de eigenschap **LocationMode** in te stellen op **SecondaryOnly**. Op dit moment moet u ook de toepassing wijzigen om in de alleen-lezen modus uit te voeren. Deze aanpak staat bekend als de [Circuit Breaker Pattern](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Aanvragen bijwerken

Het Circuit Breaker-patroon kan ook worden toegepast om aanvragen bij te werken. Updateverzoeken kunnen echter niet worden doorgestuurd naar secundaire opslag, die alleen-lezen is. Voor deze aanvragen moet u de eigenschap **LocationMode** laten instellen op **PrimaryOnly** (de standaardinstelling). Als u deze fouten wilt verwerken, u een statistiek toepassen op deze aanvragen, zoals 10 fouten op een rij, en wanneer uw drempelwaarde is bereikt, schakelt u de toepassing om in de alleen-lezenmodus. U dezelfde methoden gebruiken om terug te keren naar de updatemodus als hieronder beschreven in het volgende gedeelte over het Circuit Breaker-patroon.

## <a name="circuit-breaker-pattern"></a>Patroon Circuitonderbreker

Als u het patroon Van de Stroomonderbreker in uw toepassing gebruikt, kan worden voorkomen dat een bewerking die waarschijnlijk herhaaldelijk mislukt, opnieuw wordt geprobeerd. Hiermee kan de toepassing blijven worden uitgevoerd in plaats van tijd in beslag te nemen terwijl de bewerking exponentieel opnieuw wordt geprobeerd. Het detecteert ook wanneer de fout is opgelost, op welk moment de toepassing de bewerking opnieuw kan proberen.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Hoe het stroomonderbrekerpatroon te implementeren

Om vast te stellen dat er een bestaand probleem is met een primair eindpunt, u controleren hoe vaak de client opnieuw probeerbare fouten tegenkomt. Omdat elk geval anders is, moet u beslissen over de drempel die u wilt gebruiken voor de beslissing om over te schakelen naar het secundaire eindpunt en de toepassing in de alleen-lezenmodus uit te voeren. U bijvoorbeeld besluiten om de switch uit te voeren als er 10 fouten op een rij zijn zonder successen. Een ander voorbeeld is om over te schakelen als 90% van de aanvragen in een periode van 2 minuten mislukt.

Voor het eerste scenario u eenvoudig een telling van de fouten bijhouden en als er een succes is voordat u het maximum bereikt, stelt u het aantal terug op nul. Voor het tweede scenario is een manier om het te implementeren het object MemoryCache (in .NET) te gebruiken. Voeg voor elk verzoek een CacheItem toe aan de cache, stel de waarde in op succes (1) of fail (0) en stel de verlooptijd in op 2 minuten vanaf nu (of wat uw tijdsdruk ook is). Wanneer de vervaldatum van een item is bereikt, wordt de vermelding automatisch verwijderd. Dit geeft je een rollend venster van 2 minuten. Elke keer dat u een aanvraag indient bij de opslagservice, gebruikt u eerst een Linq-query in het object MemoryCache om het percentagesucces te berekenen door de waarden op te tellen en te delen door het aantal. Wanneer het percentage succes daalt tot onder een drempelwaarde (zoals 10%), stel de eigenschap **LocationMode** voor leesaanvragen op **SecondaryOnly** en schakel de toepassing in alleen-lezen modus voordat u verdergaat.

De drempel van fouten die worden gebruikt om te bepalen wanneer u de overstap moet maken, kan variëren van service tot service in uw toepassing, dus u moet overwegen ze configureerbare parameters te maken. Dit is ook waar u besluit om opnieuw te proberen fouten van elke service afzonderlijk of als een, zoals eerder besproken.

Een andere overweging is hoe u meerdere exemplaren van een toepassing moet verwerken en wat u moet doen wanneer u opnieuw probeerbare fouten in elke instantie detecteert. U bijvoorbeeld 20 VM's hebben die worden uitgevoerd met dezelfde toepassing geladen. Behandelt u elk exemplaar afzonderlijk? Als één instantie problemen begint te krijgen, wilt u het antwoord beperken tot slechts die ene instantie, of wilt u proberen alle instanties op dezelfde manier te laten reageren wanneer één instantie een probleem heeft? Het afzonderlijk verwerken van de instanties is veel eenvoudiger dan proberen de respons over deze instanties te coördineren, maar hoe u dit doet, is afhankelijk van de architectuur van uw toepassing.

### <a name="options-for-monitoring-the-error-frequency"></a>Opties voor het bewaken van de foutfrequentie

U hebt drie belangrijke opties voor het bewaken van de frequentie van nieuwe pogingen in het primaire gebied om te bepalen wanneer u moet overschakelen naar het secundaire gebied en de toepassing te wijzigen om in de alleen-lezenmodus uit te voeren.

* Voeg een handler toe voor de gebeurtenis [**Opnieuw proberen**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) op het object [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) dat u doorgeeft aan uw opslagaanvragen: dit is de methode die in dit artikel wordt weergegeven en wordt gebruikt in het bijbehorende voorbeeld. Deze gebeurtenissen worden afvuurd wanneer de client een aanvraag opnieuw indient, zodat u bijhouden hoe vaak de client opnieuw probeerbare fouten tegenkomt op een primair eindpunt.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* In de methode [**Evalueren**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) in een aangepast beleid voor opnieuw proberen u aangepaste code uitvoeren wanneer er een nieuwe poging plaatsvindt. Naast het opnemen wanneer een nieuwe poging plaatsvindt, geeft dit u ook de mogelijkheid om uw gedrag opnieuw proberen aan te passen.

    ```csharp 
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

* De derde benadering is het implementeren van een aangepaste monitoringcomponent in uw toepassing die voortdurend uw primaire opslageindpunt pingt met dummyleesaanvragen (zoals het lezen van een kleine blob) om de status ervan te bepalen. Dit zou een aantal middelen in nemen, maar niet een aanzienlijk bedrag. Wanneer een probleem wordt ontdekt dat uw drempel bereikt, voert u de overstap naar **SecondaryOnly** en alleen-lezen modus uit.

Op een gegeven moment wilt u terugschakelen naar het primaire eindpunt en updates toestaan. Als u een van de eerste twee hierboven genoemde methoden gebruikt, u gewoon teruggaan naar het primaire eindpunt en de updatemodus inschakelen nadat een willekeurig geselecteerde hoeveelheid tijd of aantal bewerkingen is uitgevoerd. U het dan laten gaan door de retry logica weer. Als het probleem is opgelost, blijft het primaire eindpunt worden gebruikt en worden updates toegestaan. Als er nog steeds een probleem is, schakelt deze opnieuw terug naar het secundaire eindpunt en de alleen-lezenmodus nadat u niet voldoet aan de criteria die u hebt ingesteld.

Voor het derde scenario u, wanneer het pingen van het primaire opslageindpunt opnieuw wordt, de switch terugnaar **PrimaryOnly** activeren en updates blijven toestaan.

## <a name="handling-eventually-consistent-data"></a>Verwerking van uiteindelijk consistente gegevens

Georedundante opslag werkt door transacties van het primaire naar het secundaire gebied te repliceren. Dit replicatieproces garandeert dat de gegevens in het secundaire gebied *uiteindelijk consistent zijn.* Dit betekent dat alle transacties in de primaire regio uiteindelijk in de secundaire regio zullen verschijnen, maar dat er een vertraging kan zijn voordat ze verschijnen, en dat er geen garantie is dat de transacties in de secundaire regio aankomen in dezelfde volgorde als die waarin ze werden oorspronkelijk toegepast in de primaire regio. Als uw transacties niet in de secundaire regio aankomen, *kunt* u uw gegevens in de secundaire regio in een inconsistente status beschouwen totdat de service inhaalt.

In de volgende tabel ziet u een voorbeeld van wat er kan gebeuren wanneer u de gegevens van een werknemer bijwerkt om deze lid te maken van de *beheerdersrol.* In het belang van dit voorbeeld vereist dit dat u de **entiteit van** de werknemer bijwerkt en een entiteit **voor beheerdersrollen** bijwerkt met een telling van het totale aantal beheerders. Merk op hoe de updates buiten de orde worden toegepast in de secundaire regio.

| **Tijd** | **Transactie**                                            | **Replicatie**                       | **Laatste synchronisatietijd** | **Resultaat** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transactie A: <br> Werknemer invoegen <br> entiteit in primaire |                                   |                    | Transactie A ingevoegd in primaire,<br> nog niet gerepliceerd. |
| T1       |                                                            | Transactie A <br> gerepliceerd naar<br> Secundaire | T1 | Transactie A gerepliceerd naar secundair. <br>Laatste synchronisatietijd bijgewerkt.    |
| T2       | Transactie B:<br>Update<br> werknemerentiteit<br> in de primaire  |                                | T1                 | Transactie B geschreven naar primaire,<br> nog niet gerepliceerd.  |
| T3       | Transactie C:<br> Update <br>beheerder<br>rolentiteit in<br>Primaire |                    | T1                 | Transactie C geschreven naar primaire,<br> nog niet gerepliceerd.  |
| *T4*     |                                                       | Transactie C <br>gerepliceerd naar<br> Secundaire | T1         | Transactie C gerepliceerd naar secundair.<br>LastSyncTime niet bijgewerkt omdat <br>transactie B is nog niet gerepliceerd.|
| *T5*     | Entiteiten lezen <br>van secundaire                           |                                  | T1                 | U krijgt de verouderde waarde voor werknemer <br> entiteit omdat transactie B niet <br> nog niet gerepliceerd. Je krijgt de nieuwe waarde voor<br> entiteit van de beheerdersrol omdat C<br> Gerepliceerd. Laatste synchronisatietijd nog steeds niet<br> is bijgewerkt omdat transactie B<br> heeft niet gerepliceerd. U de<br>entiteit van de beheerdersrol is inconsistent <br>omdat de entiteitsdatum/-tijd na <br>de laatste synchronisatietijd. |
| *T6*     |                                                      | Transactie B<br> gerepliceerd naar<br> Secundaire | T6                 | *T6* – Alle transacties via C hebben <br>gerepliceerd, Laatste synchronisatietijd<br> wordt bijgewerkt. |

Neem in dit voorbeeld aan dat de client overschakelt naar lezen vanuit het secundaire gebied op T5. De entiteit kan op dit moment de entiteit van de **beheerdersrol** met succes lezen, maar de entiteit bevat een waarde voor het aantal beheerders dat niet overeenkomt met het aantal **werknemersentiteiten** dat op dit moment is gemarkeerd als beheerders in het secundaire gebied. Uw klant kan deze waarde gewoon weergeven, met het risico dat het inconsistente informatie is. Als alternatief kan de client proberen te bepalen dat de **beheerdersrol** zich in een mogelijk inconsistente status bevindt omdat de updates buiten de orde zijn gebeurd en de gebruiker hiervan op de hoogte stellen.

Om te herkennen dat er mogelijk inconsistente gegevens zijn, kan de client de waarde gebruiken van de *laatste synchronisatietijd* die u op elk gewenst moment krijgen door een opslagservice op te vragen. Dit geeft aan wanneer de gegevens in het secundaire gebied voor het laatst consistent waren en wanneer de service alle transacties vóór dat moment had toegepast. In het bovenstaande voorbeeld wordt, nadat de service de **werknemerentiteit** in het secundaire gebied heeft ingevoegd, de laatste synchronisatietijd ingesteld op *T1*. Het blijft op *T1* totdat de service de **werknemerentiteit** in de secundaire regio bijwerkt wanneer deze is ingesteld op *T6.* Als de client de laatste synchronisatietijd ophaalt wanneer de entiteit op *T5*wordt gelezen, kan deze worden vergeleken met de tijdstempel op de entiteit. Als de tijdstempel op de entiteit later is dan de laatste synchronisatietijd, bevindt de entiteit zich in een mogelijk inconsistente status en u uitvoeren wat de juiste actie voor uw toepassing is. Als u dit veld gebruikt, weet u wanneer de laatste update voor de primaire update is voltooid.

Zie [De eigenschap Laatste synchronisatietijd controleren voor meer](last-sync-time-get.md)informatie over het controleren van de laatste synchronisatietijd .

## <a name="testing"></a>Testen

Het is belangrijk om te testen of uw toepassing zich gedraagt zoals verwacht wanneer deze opnieuw te proberen fouten tegenkomt. U moet bijvoorbeeld testen of de toepassing overschakelt naar de secundaire en in de alleen-lezen modus wanneer het een probleem detecteert, en schakelt terug wanneer de primaire regio weer beschikbaar is. Om dit te doen, moet u een manier om opnieuw te proberen fouten simuleren en bepalen hoe vaak ze optreden.

U [Fiddler](https://www.telerik.com/fiddler) gebruiken om HTTP-antwoorden in een script te onderscheppen en te wijzigen. Dit script kan antwoorden identificeren die afkomstig zijn van uw primaire eindpunt en de HTTP-statuscode wijzigen in een code die de opslagclientbibliotheek herkent als een opnieuw probeerbare fout. Dit codefragment toont een eenvoudig voorbeeld van een Fiddler-script dat reacties op leesverzoeken tegen de **tabel met werknemersgegevens** onderschept om een 502-status te retourneren:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

U dit voorbeeld uitbreiden om een breder scala aan aanvragen te onderscheppen en alleen de **reactiecode** op sommige aanvragen wijzigen om een scenario in de echte wereld beter te simuleren. Zie [Een aanvraag of antwoord wijzigen](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) in de fiddler-documentatie voor meer informatie over het aanpassen van Fiddler-scripts.

Als u de drempelwaarden voor het overschakelen van uw toepassing naar alleen-lezenmodus configureerbaar hebt gemaakt, is het eenvoudiger om het gedrag te testen met niet-productietransactievolumes.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Storage Redundancy Options en Read Access Geo-Redundant Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)voor meer informatie over lezen vanuit het secundaire gebied, waaronder een ander voorbeeld van de manier waarop de eigenschap Laatste synchronisatietijd is ingesteld.

* Zie [Azure Samples – Gebruik het circuitbreakerpatroon met RA-GRS-opslag](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs)voor een volledig voorbeeld dat laat zien hoe u heen en weer schakelt tussen de primaire en secundaire eindpunten.
