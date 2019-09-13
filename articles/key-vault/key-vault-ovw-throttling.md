---
title: Azure Key Vault-beperkingsrichtlijnen
description: Key Vault wordt beperkt het aantal gelijktijdige aanroepen om te voorkomen dat bronnen overmatig worden gebruikt.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: f10f40551701cafd94692afc0916972b1fd73aff
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883043"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault-beperkingsrichtlijnen

Beperking is een proces dat u starten die het aantal gelijktijdige oproepen aan de Azure-service beperkt om te voorkomen dat bronnen overmatig worden gebruikt. Azure Key Vault (AKV) is ontworpen voor het verwerken van een groot aantal aanvragen. Als er een groot aantal aanvragen optreedt, zorgt van de client aanvragen beperking onderhouden van optimale prestaties en betrouwbaarheid van de Azure Sleutelkluis-service.

Limieten voor bandbreedteregeling hangen af van het scenario. Bijvoorbeeld, als u een groot aantal schrijfbewerkingen uitvoert, is de mogelijkheid voor de beperking hoger dan als u alleen leesbewerkingen uitvoert.

## <a name="how-does-key-vault-handle-its-limits"></a>Hoe wordt de grenzen in Key Vault verwerkt?

Er zijn er Servicelimieten in Key Vault te voorkomen dat misbruik van resources en ervoor zorgen dat quality of service voor alle clients van de Sleutelkluis. Wanneer een service-drempel wordt overschreden, beperkt Key Vault nieuwe aanvragen van die client gedurende een bepaalde periode. Als dit gebeurt, Key Vault retourneert HTTP-statuscode 429 (te veel aanvragen), en de aanvragen mislukken. Ook mislukte aanvragen die 429 geteld naar de beperkingslimieten bijgehouden door Key Vault. 

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
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


Het gebruik van deze code in een\# client C-toepassing is eenvoudig. In het volgende voor beeld ziet u hoe u de klasse httpclient maakt gebruikt.

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

Houd er rekening mee dat deze code alleen geschikt is voor het testen van het concept. 

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

