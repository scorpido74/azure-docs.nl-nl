---
title: Richtlijnen voor azure key vault-beperking
description: Key Vault beperking beperkt het aantal gelijktijdige oproepen om overmatig gebruik van resources te voorkomen.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c4923e86f8678458d6301503043413fb8a5629b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197365"
---
# <a name="azure-key-vault-throttling-guidance"></a>Richtlijnen voor azure key vault-beperking

Beperking is een proces dat u initieert en dat het aantal gelijktijdige aanroepen naar de Azure-service beperkt om overmatig gebruik van resources te voorkomen. Azure Key Vault (AKV) is ontworpen om een groot aantal aanvragen te verwerken. Als er een overweldigend aantal aanvragen optreedt, helpt het beperken van de verzoeken van uw klant om de optimale prestaties en betrouwbaarheid van de AKV-service te behouden.

Beperkingslimieten variëren afhankelijk van het scenario. Als u bijvoorbeeld een groot aantal schrijfbewerkingen uitvoert, is de mogelijkheid voor beperking hoger dan wanneer u alleen leesbewerkingen uitvoert.

## <a name="how-does-key-vault-handle-its-limits"></a>Hoe gaat Key Vault om met zijn limieten?

Servicelimieten in Key Vault voorkomen misbruik van resources en zorgen voor de kwaliteit van de service voor alle klanten van Key Vault. Wanneer een servicedrempel wordt overschreden, beperkt Key Vault eventuele verdere aanvragen van die client gedurende een bepaalde periode, retourneert HTTP-statuscode 429 (Te veel aanvragen) en mislukt de aanvraag. Mislukte aanvragen die een 429 tellen voor de gashendellimieten die door Key Vault worden bijgehouden, retourneren. 

Key Vault is oorspronkelijk ontworpen om te worden gebruikt om uw geheimen op te slaan en op te halen tijdens de implementatie.  De wereld is geëvolueerd en Key Vault wordt op tijd gebruikt om geheimen op te slaan en op te halen, en vaak willen apps en services Key Vault gebruiken als een database.  Huidige limieten bieden geen ondersteuning voor hoge doorvoersnelheden.

Key Vault is oorspronkelijk gemaakt met de limieten die zijn opgegeven in [Azure Key Vault-servicelimieten.](key-vault-service-limits.md)  Om uw Key Vault te maximaliseren door putsnelheden, zijn hier enkele aanbevolen richtlijnen / aanbevolen procedures voor het maximaliseren van uw doorvoer:
1. Zorg ervoor dat je throttling op zijn plaats hebt.  Client moet exponentiële back-off beleid voor 429's eren en ervoor zorgen dat u retries doet volgens de onderstaande richtlijnen.
1. Verdeel uw Key Vault-verkeer over meerdere kluizen en verschillende regio's.   Gebruik een aparte kluis voor elk domein van beveiliging/beschikbaarheid.   Als je vijf apps hebt, elk in twee regio's, raden we 10 kluizen aan die elk de geheimen bevatten die uniek zijn voor app en regio.  Een abonnementslimiet voor alle transactietypen is vijf keer de afzonderlijke limiet voor sleutelkluizen. HSM-andere transacties per abonnement zijn bijvoorbeeld beperkt tot 5.000 transacties in 10 seconden per abonnement. Overweeg om het geheim in uw service of app te plaatsen om de RPS ook rechtstreeks te reduceren tot sleutelkluis en/of burst-gebaseerd verkeer te verwerken.  U uw verkeer ook verdelen over verschillende regio's om de latentie te minimaliseren en een ander abonnement/kluis te gebruiken.  Stuur niet meer dan de abonnementslimiet naar de Key Vault-service in één Azure-regio.
1. Bewaar de geheimen die u ophaalt uit Azure Key Vault in het geheugen en hergebruik waar mogelijk uit het geheugen.  Alleen opnieuw lezen vanuit Azure Key Vault wanneer de kopie in de cache niet meer werkt (bijvoorbeeld omdat deze bij de bron is gedraaid). 
1. Key Vault is ontworpen voor uw eigen diensten geheimen.   Als u de geheimen van uw klanten opslaat (met name voor opslagscenario's met een hoge doorvoer), u overwegen de sleutels in een database of opslagaccount met versleuteling op te nemen en alleen de hoofdsleutel op te slaan in Azure Key Vault.
1. Versleutelen, verpakken en verifiëren van openbare sleutelbewerkingen kunnen worden uitgevoerd zonder toegang tot Key Vault, wat niet alleen het risico op beperking vermindert, maar ook de betrouwbaarheid verbetert (zolang u het materiaal van de openbare sleutel goed in de cache opslaat).
1. Als u Key Vault gebruikt om referenties voor een service op te slaan, controleert u of die service AAD-verificatie ondersteunt om deze rechtstreeks te verifiëren. Dit vermindert de belasting van Key Vault, verbetert de betrouwbaarheid en vereenvoudigt uw code, omdat Key Vault nu het AAD-token kan gebruiken.  Veel diensten zijn verhuisd naar het gebruik van AAD Auth.  Zie de huidige lijst bij [Services die beheerde identiteiten voor Azure-resources ondersteunen.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)
1. Overweeg uw belasting/implementatie over een langere periode te wankelen om onder de huidige RPS-limieten te blijven.
1. Als uw app meerdere knooppunten bevat die hetzelfde geheim(en) moeten lezen, u overwegen een uitwaaierpatroon te gebruiken, waarbij één entiteit het geheim van Key Vault leest en naar alle knooppunten wordt verspreid.   Cache de opgehaalde geheimen alleen in het geheugen.
Als u vindt dat het bovenstaande nog steeds niet aan uw behoeften voldoet, vul dan de onderstaande tabel in en neem contact met ons op om te bepalen welke extra capaciteit kan worden toegevoegd (bijvoorbeeld hieronder alleen voor illustratieve doeleinden).

| Naam van de kluis | Kluisregio | Objecttype (Geheim, Sleutel of Certificaat) | Bewerking(en)* | Sleuteltype | Sleutellengte of curve | HSM-toets?| Steady state RPS nodig | Piek RPS nodig |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Sleutel | Teken | EC | P-256 | Nee | 200 | 1000 |

\*Zie [Azure Key Vault-bewerkingen](/rest/api/keyvault/key-operations)voor een volledige lijst met mogelijke waarden.

Als de extra capaciteit wordt goedgekeurd, moet u rekening houden met het volgende als gevolg van de capaciteitsverhogingen:
1. Wijzigingen in het gegevensconsistentiemodel. Zodra een kluis is toegestaan met extra doorvoercapaciteit, garandeert de key vault-servicestatus wijzigingen (die nodig zijn om te voldoen aan hogere RPS met een hoger volume omdat de onderliggende Azure Storage-service niet kan bijhouden).  In een notendop:
  1. **Zonder vermelding toestaan**: De Key Vault-service weerspiegelt de resultaten van een schrijfbewerking (bijv. SecretSet, CreateKey) onmiddellijk in latere oproepen (bijv. SecretGet, KeySign).
  1. **Met allow listing**: De Key Vault-service weerspiegelt de resultaten van een schrijfbewerking (bijv. SecretSet, CreateKey) binnen 60 seconden in volgende oproepen (bijv. SecretGet, KeySign).
1. Clientcode moet het back-offbeleid voor 429 nieuwe pogingen honoreren. De clientcode die de Key Vault-service aanroept, mag Key Vault-verzoeken niet onmiddellijk opnieuw proberen wanneer deze een antwoordcode van 429 ontvangt.  De hier gepubliceerde richtlijnen voor azure key vault-beperking raadt u aan exponentiële back-off toe te passen wanneer u een 429 Http-antwoordcode ontvangt.

Als je een geldige business case hebt voor hogere gashendellimieten, neem dan contact met ons op.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Hoe u uw app beperken als reactie op servicelimieten

De volgende **aanbevolen procedures** die u moet implementeren wanneer uw service wordt beperkt:
- Verminder het aantal bewerkingen per aanvraag.
- Verlaag de frequentie van aanvragen.
- Vermijd onmiddellijke pogingen. 
    - Alle aanvragen worden berekend aan de hand van uw gebruikslimieten.

Wanneer u de foutafhandeling van uw app implementeert, gebruikt u de HTTP-foutcode 429 om de noodzaak van beperking aan de clientzijde te detecteren. Als de aanvraag opnieuw mislukt met een HTTP 429-foutcode, komt u nog steeds een Azure-servicelimiet tegen. Blijf de aanbevolen methode voor beperking aan de clientzijde gebruiken en probeer de aanvraag opnieuw totdat het lukt.

Code die exponentiële back-off implementeert, wordt hieronder weergegeven. 
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


Het gebruik van deze code in een client C# toepassing is eenvoudig. 

### <a name="recommended-client-side-throttling-method"></a>Aanbevolen methode voor beperking aan de clientzijde

Op HTTP-foutcode 429 begint u uw client te beperken met behulp van een exponentiële back-offbenadering:

1. Wacht 1 seconde, probeer het verzoek opnieuw
2. Als nog steeds beperkt wachten 2 seconden, opnieuw proberen verzoek
3. Als nog steeds gasgevend 4 seconden wachten, opnieuw proberen verzoek
4. Als nog steeds beperkt wachten 8 seconden, opnieuw proberen verzoek
5. Als nog steeds gasloos wachten 16 seconden, opnieuw proberen verzoek

Op dit moment moet u geen HTTP 429-antwoordcodes krijgen.

## <a name="see-also"></a>Zie ook

Zie [Beperkingspatroon](https://docs.microsoft.com/azure/architecture/patterns/throttling)voor een diepere oriëntatie van beperking op de Microsoft Cloud.

