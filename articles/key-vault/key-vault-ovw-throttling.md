---
title: Azure Key Vault-beperkingsrichtlijnen
description: Key Vault wordt beperkt het aantal gelijktijdige aanroepen om te voorkomen dat bronnen overmatig worden gebruikt.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 28e79dffb206e8a62410bf3b4e0e239879b51224
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806674"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault-beperkingsrichtlijnen

Beperking is een proces dat u starten die het aantal gelijktijdige oproepen aan de Azure-service beperkt om te voorkomen dat bronnen overmatig worden gebruikt. Azure Key Vault (AKV) is ontworpen voor het verwerken van een groot aantal aanvragen. Als er een groot aantal aanvragen optreedt, zorgt van de client aanvragen beperking onderhouden van optimale prestaties en betrouwbaarheid van de Azure Sleutelkluis-service.

Limieten voor bandbreedteregeling hangen af van het scenario. Bijvoorbeeld, als u een groot aantal schrijfbewerkingen uitvoert, is de mogelijkheid voor de beperking hoger dan als u alleen leesbewerkingen uitvoert.

## <a name="how-does-key-vault-handle-its-limits"></a>Hoe wordt de grenzen in Key Vault verwerkt?

Service limieten in Key Vault misbruik van bronnen te voor komen en ervoor te zorgen dat de service voor alle clients van Key Vault. Wanneer een service drempel wordt overschreden, wordt door Key Vault alle aanvragen van die client gedurende een bepaalde periode beperkt, wordt de HTTP-status code 429 (te veel aanvragen) geretourneerd en mislukt de aanvraag. Mislukte aanvragen die een aantal van 429 retour neren naar de beperkings limieten die worden bijgehouden door Key Vault. 

Key Vault was oorspronkelijk ontworpen om te worden gebruikt om uw geheimen op te slaan en op te halen tijdens de implementatie.  De wereld heeft zich ontwikkeld en Key Vault wordt tijdens runtime gebruikt om geheimen op te slaan en op te halen, en vaak willen apps en services Key Vault als een Data Base gebruiken.  Huidige limieten bieden geen ondersteuning voor hoge doorvoer snelheden.

Key Vault oorspronkelijk is gemaakt met de limieten die zijn opgegeven in [Azure Key Vault service limieten](key-vault-service-limits.md).  Als u uw Key Vault wilt maximaliseren door middel van tarieven, zijn hier enkele aanbevolen richt lijnen/aanbevolen procedures voor het maximaliseren van uw door Voer:
1. Zorg ervoor dat u de beperking hebt ingesteld.  De client moet voldoen aan het beleid voor exponentiële back-ups voor 429 en ervoor zorgen dat u nieuwe pogingen doet volgens onderstaande instructies.
1. Deel uw Key Vault verkeer onder meerdere kluizen en verschillende regio's.   Gebruik een afzonderlijke kluis voor elk domein voor beveiliging/Beschik baarheid.   Als u vijf apps hebt, elk in twee regio's, raden we 10 kluizen aan met elk geheim dat uniek is voor de app en regio.  Een limiet voor het hele abonnement voor alle transactie typen is vijf keer de limiet voor de individuele sleutel kluis. HSM-andere trans acties per abonnement zijn bijvoorbeeld beperkt tot 5.000 trans acties in tien seconden per abonnement. Overweeg het opslaan van het geheim binnen uw service of app om ook de RPS rechtstreeks te verminderen naar sleutel kluis en/of op Burst gebaseerd verkeer te verwerken.  U kunt het verkeer ook verdelen over verschillende regio's om latentie te minimaliseren en een ander abonnement/kluis gebruiken.  Verzend niet meer dan de limiet van het abonnement voor de Key Vault-service in één Azure-regio.
1. Cache de geheimen die u ophaalt uit Azure Key Vault in het geheugen en gebruik waar mogelijk van geheugen.  Het opnieuw lezen van Azure Key Vault alleen wanneer de kopie in de cache niet meer werkt (bijvoorbeeld omdat deze is gedraaid bij de bron). 
1. Key Vault is ontworpen voor uw eigen service geheimen.   Als u de geheimen van uw klanten opslaat (met name voor scenario's met hoge doorvoer opslag), kunt u overwegen om de sleutels in een Data Base of opslag account met versleuteling te plaatsen en alleen de hoofd sleutel in Azure Key Vault op te slaan.
1. Het versleutelen, verpakken en controleren van open bare-sleutel bewerkingen kunnen worden uitgevoerd zonder toegang tot Key Vault, wat niet alleen het risico op beperking vermindert, maar ook de betrouw baarheid verbetert (zolang u het open bare-sleutel materiaal op de juiste wijze in de cache opslaat).
1. Als u Key Vault gebruikt om referenties voor een service op te slaan, controleert u of deze service ondersteuning biedt voor AAD-verificatie voor het rechtstreeks verifiëren. Dit vermindert de belasting van Key Vault, verbetert de betrouw baarheid en vereenvoudigt uw code omdat Key Vault nu het AAD-token kan gebruiken.  Veel services zijn verplaatst naar met behulp van AAD-verificatie.  Bekijk de huidige lijst bij [Services die ondersteuning bieden voor beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Overweeg uw belasting/implementatie over een langere periode te spreiden om te blijven onder de huidige RPS-limieten.
1. Als uw app meerdere knoop punten omvat die dezelfde geheimen moeten lezen, kunt u het beste een uitzonderings patroon gebruiken, waarbij de ene entiteit het geheim leest van Key Vault en uitwaaiers naar alle knoop punten.   De opgehaalde geheimen alleen in de cache opslaan in het geheugen.
Als u merkt dat het bovenstaande nog niet aan uw behoeften voldoet, vult u de onderstaande tabel in en neemt u contact met ons op om te bepalen welke extra capaciteit er kan worden toegevoegd (voor beeld hieronder is alleen bedoeld voor demonstratie doeleinden).

| Kluis naam | Kluis regio | Object type (geheim, sleutel of certificaat) | Bewerking (en) * | Sleutel type | Sleutel lengte of-curve | HSM-sleutel?| Er is een constante status RPS vereist | Piek RPS vereist |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Sleutel | Aanmelden | EC | P-256 | Nee | 200 | 1000 |

Zie [Azure Key Vault-bewerkingen](/rest/api/keyvault/key-operations)\* voor een volledige lijst met mogelijke waarden.

Als aanvullende capaciteit is goedgekeurd, let dan op het volgende als resultaat van de capaciteits toename:
1. Wijzigingen in het gegevens consistentie model. Zodra een kluis in de lijst met extra doorvoer capaciteit is toegestaan, wordt de Key Vault service gegevens consistentie garantie gewijzigd (nood zakelijk om te voldoen aan de RPS van een hoger volume omdat de onderliggende Azure Storage service niet kan worden uitgevoerd).  In een kort gezegd:
  1. **Zonder vermelding toestaan**: de Key Vault-service geeft de resultaten van een schrijf bewerking weer (bijvoorbeeld Geheimset, CreateKey) direct in volgende aanroepen (bijvoorbeeld SecretGet).
  1. **Met vermelding toestaan**: de Key Vault-service geeft de resultaten van een schrijf bewerking weer (bijvoorbeeld Geheimset, CreateKey) binnen 60 seconden in volgende aanroepen (bijvoorbeeld SecretGet).
1. Client code moet voldoen aan het beleid voor uitstel van 429 nieuwe pogingen. De client code die de Key Vault-service aanroept, mag niet direct Key Vault aanvragen opnieuw proberen wanneer de 429-respons code wordt ontvangen.  De Azure Key Vault beperkings richtlijnen die hier worden gepubliceerd, wordt aanbevolen exponentiële uitstel toe te passen wanneer u een 429 HTTP-respons code ontvangt.

Als u een geldige zakelijke-aanvraag voor hogere beperkingslimieten hebt, neem dan contact met ons.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Hoe u uw app in reactie op Servicelimieten beperken

Hier volgen enkele **Aanbevolen procedures** voor het uitvoeren van een beperking van uw service:
- Verminder het aantal bewerkingen per aanvraag.
- Verminder de frequentie van aanvragen.
- Vermijd direct nieuwe pogingen. 
    - Alle aanvragen gebracht op basis van de gebruikslimieten.

Wanneer u foutafhandeling van uw app kunt implementeren, gebruikt u de HTTP-foutcode 429 voor het detecteren van client-side '-beperking. Als de aanvraag is mislukt opnieuw met een foutcode HTTP 429, ondervindt u nog steeds een limiet voor de Azure-service. Echter ook doorgaan met de aanbevolen client-side methode beperken, de aanvraag opnieuw proberen totdat het is gelukt.

Hieronder ziet u code die exponentieel uitstel implementeert. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Het gebruik van deze code in C# een client toepassing is eenvoudig. 

### <a name="recommended-client-side-throttling-method"></a>Aanbevolen methode voor client-side '-beperking

Beginnen met beperking van de client met behulp van een benadering exponentieel uitstel op HTTP-foutcode 429:

1. Wacht 1 seconde, aanvraag voor opnieuw proberen
2. Als u nog steeds beperkt 2 seconden wachten, opnieuw proberen de aanvraag
3. Als u nog steeds beperkt 4 seconden wachten, opnieuw proberen de aanvraag
4. Als u nog steeds beperkt 8 seconden wachten, opnieuw proberen de aanvraag
5. Als u nog steeds beperkt 16 seconden wachten, opnieuw proberen de aanvraag

Op dit moment moet niet worden aan te bieden u HTTP 429-responscodes.

## <a name="see-also"></a>Zie ook

Zie voor een uitgebreidere richting van de beperking op de Microsoft Cloud, [patroon beperking](https://docs.microsoft.com/azure/architecture/patterns/throttling).

