---
title: Beheerde identiteit gebruiken met een toepassing
description: Beheerde identiteiten gebruiken in Azure Service Fabric-toepassingscode om toegang te krijgen tot Azure Services.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: 8f1f355d6add16f3b3ec25bc569f9b198a8d6778
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461562"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>De beheerde identiteit van een Service Fabric-toepassing gebruiken om toegang te krijgen tot Azure-services

Service Fabric-toepassingen kunnen beheerde identiteiten gebruiken om toegang te krijgen tot andere Azure-bronnen die azure Active Directory-gebaseerde verificatie ondersteunen. Een toepassing kan een [toegangstoken](../active-directory/develop/developer-glossary.md#access-token) verkrijgen dat zijn identiteit vertegenwoordigt, dat kan worden toegewezen of door de gebruiker is toegewezen, en het gebruiken als een 'drager' token om zichzelf te authenticeren naar een andere service - ook wel bekend als een [beveiligde resourceserver](../active-directory/develop/developer-glossary.md#resource-server). Het token vertegenwoordigt de identiteit die is toegewezen aan de Service Fabric-toepassing en wordt alleen uitgegeven aan Azure-resources (inclusief SF-toepassingen) die die identiteit delen. Raadpleeg de [documentatie van het beheerde identiteitsoverzicht](../active-directory/managed-identities-azure-resources/overview.md) voor een gedetailleerde beschrijving van beheerde identiteiten, evenals het onderscheid tussen door het systeem toegewezen en door de gebruiker toegewezen identiteiten. We zullen verwijzen naar een managed-identity-enabled Service Fabric applicatie als de [client applicatie](../active-directory/develop/developer-glossary.md#client-application) in dit artikel.

> [!IMPORTANT]
> Een beheerde identiteit vertegenwoordigt de koppeling tussen een Azure-bron en een serviceprincipal in de bijbehorende Azure AD-tenant die is gekoppeld aan het abonnement dat de bron bevat. In het kader van Service Fabric worden beheerde identiteiten daarom alleen ondersteund voor toepassingen die als Azure-resources worden geïmplementeerd. 

> [!IMPORTANT]
> Voordat de beheerde identiteit van een Service Fabric-toepassing wordt gebruikt, moet de clienttoepassing toegang krijgen tot de beveiligde bron. Raadpleeg de lijst met [Azure-services die Azure AD-verificatie ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) om te controleren op ondersteuning en vervolgens naar de documentatie van de desbetreffende service voor specifieke stappen om een identiteitstoegang tot bronnen van belang te verlenen. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Een toegangstoken aanschaffen met REST API
In clusters die zijn ingeschakeld voor beheerde identiteit, wordt in de runtime van Service Fabric een localhost-eindpunt weergegeven dat toepassingen kunnen gebruiken om toegangstokens te verkrijgen. Het eindpunt is beschikbaar op elk knooppunt van het cluster en is toegankelijk voor alle entiteiten op dat knooppunt. Geautoriseerde bellers kunnen toegangstokens verkrijgen door dit eindpunt aan te roepen en een verificatiecode te presenteren; de code wordt gegenereerd door de runtime van Service Fabric voor elke afzonderlijke activering van servicecodepakketten en is gebonden aan de levensduur van het proces dat dat servicecodepakket host.

Met name de omgeving van een servicefabric-service met managed-identity wordt met de volgende variabelen ingezaaid:
- 'IDENTITY_ENDPOINT': het eindpunt van localhost dat overeenkomt met de beheerde identiteit van de service
- 'IDENTITY_HEADER': een unieke verificatiecode die de service op het huidige knooppunt weergeeft
- 'IDENTITY_SERVER_THUMBPRINT' : Duimafdruk van service fabric managed identity server

> [!IMPORTANT]
> De toepassingscode moet de waarde van de 'IDENTITY_HEADER' omgevingsvariabele als gevoelige gegevens beschouwen - deze mag niet worden geregistreerd of anderszins worden verspreid. De verificatiecode heeft geen waarde buiten het lokale knooppunt of nadat het proces dat de service host is beëindigd, maar wel de identiteit van de Service Fabric-service vertegenwoordigt en dus moet worden behandeld met dezelfde voorzorgsmaatregelen als het toegangstoken zelf.

Om een token te verkrijgen, voert de client de volgende stappen uit:
- vormt een URI door het beheerde identiteitseindpunt (IDENTITY_ENDPOINT waarde) samen te stellen met de API-versie en de resource (doelgroep) die nodig is voor het token
- hiermee wordt een GET http(s)-aanvraag voor de opgegeven URI
- voegt de juiste servercertificaatvalidatielogica toe
- hiermee voegt u de verificatiecode (IDENTITY_HEADER waarde) toe als koptekst aan de aanvraag
- dient het verzoek in

Een succesvol antwoord bevat een JSON-payload die het resulterende toegangstoken vertegenwoordigt, evenals metagegevens die het beschrijven. Een mislukte reactie bevat ook een verklaring van de fout. Zie hieronder voor meer informatie over foutafhandeling.

Toegangstokens worden in de cache opgeslagen door Service Fabric op verschillende niveaus (knooppunt, cluster, resourceproviderservice), dus een succesvol antwoord betekent niet noodzakelijkerwijs dat het token rechtstreeks is uitgegeven in reactie op het verzoek van de gebruikerstoepassing. Tokens worden minder dan hun levensduur in de cache opgeslagen en dus ontvangt een toepassing gegarandeerd een geldig token. Het wordt aanbevolen dat de toepassingscode zichzelf alle toegangstokens in slaat die het verwerft; de caching sleutel moet (een afleiding van) het publiek bevatten. 

Voorbeeldaanvraag:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
Hierbij

| Element | Beschrijving |
| ------- | ----------- |
| `GET` | Het HTTP-werkwoord, dat aangeeft dat u gegevens uit het eindpunt wilt ophalen. In dit geval een OAuth-toegangstoken. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Het beheerde identiteitseindpunt voor Service Fabric-toepassingen, geleverd via de IDENTITY_ENDPOINT omgevingsvariabele. |
| `api-version` | Een parameter querytekenreeks, die de API-versie van de Managed Identity Token Service opgeeft; momenteel is `2019-07-01-preview`de enige geaccepteerde waarde, en is onderhevig aan verandering. |
| `resource` | Een parameter querytekenreeks die de URI van de app-id van de doelbron aangeeft. Dit wordt weerspiegeld `aud` als de (publieks)claim van het uitgegeven token. In dit voorbeeld wordt een token gevraagd om toegang te\/krijgen tot Azure Key Vault, waarvan een URI voor app-id's https is: /vault.azure.net/. |
| `Secret` | Een http-aanvraagkopveld, vereist door de Service Fabric Managed Identity Token Service voor Service Fabric-services om de beller te verifiëren. Deze waarde wordt geleverd door de SF-runtime via IDENTITY_HEADER omgevingsvariabele. |


Voorbeeldrespons:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
Hierbij

| Element | Beschrijving |
| ------- | ----------- |
| `token_type` | Het type token; in dit geval een toegangstoken "Drager", wat betekent dat de presentator ('drager') van dit token het beoogde onderwerp van het token is. |
| `access_token` | Het gevraagde toegangstoken. Wanneer u een beveiligde REST-API aanroept, wordt het token ingesloten in het veld van de `Authorization` aanvraagkoptekst als een 'drager'-token, waardoor de API de beller kan verifiëren. | 
| `expires_on` | De tijdstempel van het verstrijken van het toegangstoken; weergegeven als het aantal seconden van "1970-01-01T0:0:0Z UTC" en `exp` komt overeen met de claim van het token. In dit geval verloopt het token op 2019-08-08T06:10:11+00:00 (in RFC 3339)|
| `resource` | De bron waarvoor het toegangstoken is `resource` uitgegeven, opgegeven via de querytekenreeksparameter van de aanvraag. komt overeen met de 'aud'-claim van het token. |


## <a name="acquiring-an-access-token-using-c"></a>Een toegangstoken aanschaffen met C #
Het bovenstaande wordt, in C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Toegang tot Key Vault vanuit een Service Fabric-toepassing met Managed Identity
Dit voorbeeld bouwt voort op het bovenstaande om aan te tonen dat toegang wordt verkrijgd tot een geheim dat is opgeslagen in een Key Vault met behulp van een beheerde identiteit.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Foutafhandeling
Het veld 'statuscode' van de HTTP-antwoordkop geeft de successtatus van de aanvraag aan; een '200 OK'-status geeft succes aan en het antwoord bevat het toegangstoken zoals hierboven beschreven. Hieronder volgt een korte opsomming van mogelijke foutreacties.

| Statuscode | Foutreden | Hoe om te gaan |
| ----------- | ------------ | ------------- |
| 404 Niet gevonden. | Onbekende verificatiecode of de toepassing is geen beheerde identiteit toegewezen. | De installatie- of tokenacquisitiecode van de toepassing corrigeren. |
| 429 Te veel verzoeken. |  Throttle limiet bereikt, opgelegd door AAD of SF. | Probeer het opnieuw met Exponential Backoff. Zie de richtlijnen hieronder. |
| 4xx Fout in aanvraag. | Een of meer van de aanvraagparameters waren onjuist. | Probeer het niet opnieuw.  Bekijk de foutgegevens voor meer informatie.  4xx fouten zijn ontwerptijdfouten.|
| 5xx Fout van service. | Het subsysteem beheerde identiteit of Azure Active Directory heeft een tijdelijke fout geretourneerd. | Het is veilig om opnieuw te proberen na een korte tijd. U een beperking voorwaarde (429) raken bij het opnieuw proberen.|

Als er een fout optreedt, bevat de bijbehorende HTTP-antwoordbody een JSON-object met de foutgegevens:

| Element | Beschrijving |
| ------- | ----------- |
| code | Foutcode. |
| correlationId | Een correlatie-ID die kan worden gebruikt voor het debuggen. |
| message | Verbose beschrijving van de fout. **Foutbeschrijvingen kunnen op elk gewenst moment worden gewijzigd. Niet afhankelijk van de foutmelding zelf.**|

Voorbeeldfout:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Hieronder volgt een lijst met typische fouten in servicefabric die specifiek zijn voor beheerde identiteiten:

| Code | Bericht | Beschrijving | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Geheim is niet te vinden in de aanvraagheaders. | De verificatiecode is niet bij het verzoek geleverd. | 
| ManagedIdentityNotFound | Beheerde identiteit niet gevonden voor de opgegeven toepassingshost. | De toepassing heeft geen identiteit of de verificatiecode is onbekend. |
| ArgumentnullorEmpty | De parameter 'resource' mag geen null of lege tekenreeks zijn. | De resource (doelgroep) is niet in de aanvraag opgenomen. |
| Ongeldige apiversie | De api-versie '' wordt niet ondersteund. Ondersteunde versie is '2019-07-01-preview'. | Ontbrekende of niet-ondersteunde API-versie opgegeven in de aanvraag URI. |
| InternalServerError | Er is een fout opgetreden. | Er is een fout opgetreden in het subsysteem beheerde identiteit, mogelijk buiten de servicestructuur. Meest waarschijnlijke oorzaak is een onjuiste waarde opgegeven voor de resource (controleer op trailing '/'?) | 

## <a name="retry-guidance"></a>Richtlijnen opnieuw proberen 

Meestal is de enige herprobeerbare foutcode 429 (Te veel aanvragen); interne serverfouten/5xx-foutcodes kunnen opnieuw worden geprobeerd, hoewel de oorzaak permanent kan zijn. 

Beperkingslimieten zijn van toepassing op het aantal oproepen naar het subsysteem Beheerde identiteit - met name de 'upstream'-afhankelijkheden (de Managed Identity Azure-service of de secure token-service). Service Fabric caches tokens op verschillende niveaus in de pijplijn, maar gezien de gedistribueerde aard van de betrokken componenten, kan de beller inconsistente throttling reacties ervaren (d.w.z. krijgen gewurgd op een knooppunt / instantie van een toepassing, maar niet op een ander knooppunt, terwijl het aanvragen van een token voor dezelfde identiteit.) Wanneer de beperkingsvoorwaarde is ingesteld, kunnen volgende aanvragen van dezelfde toepassing mislukken met de HTTP-statuscode 429 (Te veel aanvragen) totdat de voorwaarde is gewist.  

Het wordt aanbevolen dat aanvragen die zijn mislukt als gevolg van beperking opnieuw worden geprobeerd met een exponentiële back-off, als volgt: 

| Oproepindex | Actie bij ontvangst van 429 | 
| --- | --- | 
| 1 | Wacht 1 seconde en probeer opnieuw |
| 2 | Wacht 2 seconden en probeer opnieuw |
| 3 | Wacht 4 seconden en probeer opnieuw |
| 4 | Wacht 8 seconden en probeer opnieuw |
| 4 | Wacht 8 seconden en probeer opnieuw |
| 5 | Wacht 16 seconden en probeer opnieuw |

## <a name="resource-ids-for-azure-services"></a>Bron-iD's voor Azure-services
Zie [Azure-services die Azure AD-verificatie ondersteunen](../active-directory/managed-identities-azure-resources/services-support-msi.md) voor een lijst met bronnen die Azure AD ondersteunen en hun respectieve bron-id's.

## <a name="next-steps"></a>Volgende stappen
* [Een Azure Service Fabric-toepassing implementeren met een beheerde identiteit met systeemtoegewezen](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-bronnen](./how-to-grant-access-other-resources.md)
