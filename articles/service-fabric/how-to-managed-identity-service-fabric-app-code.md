---
title: Azure Service Fabric-beheerde identiteit gebruiken met Service Fabric toepassingen | Microsoft Docs
description: Beheerde identiteiten gebruiken vanuit Service Fabric toepassings code
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 7/25/2019
ms.author: atsenthi
ms.openlocfilehash: 8e535fc581e186abd032206c2bbf78623d95967f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899773"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services-preview"></a>De beheerde identiteit van een Service Fabric-toepassing gebruiken voor toegang tot Azure-Services (preview)

Service Fabric toepassingen kunnen beheerde identiteiten gebruiken om toegang te krijgen tot andere Azure-resources die ondersteuning bieden voor op Azure Active Directory gebaseerde verificatie. Een toepassing kan een [toegangs token](../active-directory/develop/developer-glossary.md#access-token) verkrijgen dat de identiteit vertegenwoordigt, die door het systeem of de gebruiker kan worden toegewezen, en dit als een Bearer-token gebruiken om zichzelf te verifiëren bij een andere service, ook wel bekend als een [beveiligde bron server](../active-directory/develop/developer-glossary.md#resource-server). Het token vertegenwoordigt de identiteit die is toegewezen aan de Service Fabric-toepassing en wordt alleen verleend aan Azure-resources (inclusief SF-toepassingen) die deze identiteit delen. Raadpleeg de documentatie van het [overzicht van beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) voor een gedetailleerde beschrijving van beheerde identiteiten, evenals het onderscheid tussen systeem toewijzingen en door de gebruiker toegewezen identiteiten. In dit artikel wordt gerefereerd aan een met Managed Identity geschikte Service Fabric toepassing als de [client toepassing](../active-directory/develop/developer-glossary.md#client-application) .

> [!IMPORTANT]
> Een beheerde identiteit vertegenwoordigt de koppeling tussen een Azure-resource en een Service-Principal in de bijbehorende Azure AD-Tenant die is gekoppeld aan het abonnement dat de resource bevat. In de context van Service Fabric worden beheerde identiteiten alleen ondersteund voor toepassingen die als Azure-resources worden geïmplementeerd. 

> [!IMPORTANT]
> Voordat de beheerde identiteit van een Service Fabric toepassing wordt gebruikt, moet aan de client toepassing toegang worden verleend tot de beveiligde bron. Raadpleeg de lijst met Azure- [Services die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) om te controleren op ondersteuning en vervolgens naar de documentatie van de betreffende service voor specifieke stappen voor het verlenen van identiteits toegang tot belang rijke bronnen. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Een toegangs Token ophalen met behulp van REST API
In clusters waarvoor een beheerde identiteit is ingeschakeld, maakt de Service Fabric-runtime gebruik van een localhost-eind punt dat toepassingen kunnen gebruiken om toegangs tokens te verkrijgen. Het eind punt is beschikbaar op elk knoop punt van het cluster en is toegankelijk voor alle entiteiten op het knoop punt. Geautoriseerde bellers kunnen toegangs tokens verkrijgen door dit eind punt aan te roepen en een verificatie code te presen teren. de code wordt gegenereerd door de Service Fabric runtime voor elke afzonderlijke activering van service code-pakket en is gebonden aan de levens duur van het proces dat als host fungeert voor het service code pakket.

Met name de omgeving van een Service Fabric service met beheerde identiteiten wordt geseedd met de volgende variabelen:
- ' MSI_ENDPOINT ': het localhost-eind punt, voltooid met het pad, de API-versie en de para meters die overeenkomen met de beheerde identiteit van die service
- ' MSI_SECRET ': een verificatie code, een ondoorzichtige teken reeks en vertegenwoordigt de service op het huidige knoop punt

> [!NOTE]
> De namen ' MSI_ENDPOINT ' en ' MSI_SECRET ' verwijzen naar de vorige aanwijzing van beheerde identiteiten (' Managed Service Identity ') en die nu is afgeschaft. De namen zijn ook consistent met de equivalente namen van omgevings variabelen die worden gebruikt door andere Azure-Services die beheerde identiteiten ondersteunen.

> [!IMPORTANT]
> De toepassings code moet rekening houden met de waarde van de omgevings variabele ' MSI_SECRET ' als gevoelige gegevens. deze kan niet worden geregistreerd of anderszins worden verspreid. De verificatie code heeft geen waarde buiten het lokale knoop punt of nadat het proces dat als host fungeert voor de service is beëindigd, maar het de identiteit van de Service Fabric-service vertegenwoordigt, en moet dus worden behandeld met dezelfde voorzorgsmaatregelen als het toegangs token zelf.

Voor het verkrijgen van een token voert de client de volgende stappen uit:
- een URI vormt door het samen voegen van het beheerde identiteits eindpunt (MSI_ENDPOINT waarde) met de API-versie en de bron (doel groep) die vereist is voor het token
- Hiermee maakt u een GET HTTP-aanvraag voor de opgegeven URI
- Hiermee wordt de verificatie code (MSI_SECRET-waarde) als koptekst aan de aanvraag toegevoegd
- Hiermee wordt de aanvraag verzonden

Een geslaagde reactie bevat een JSON-Payload die het resulterende toegangs token en meta gegevens beschrijft. Een mislukte reactie omvat ook een uitleg van de fout. Zie hieronder voor meer informatie over het afhandelen van fouten.

Toegangs tokens worden in de cache opgeslagen door Service Fabric op verschillende niveaus (knoop punt, cluster, resource Provider service), waardoor een geslaagde reactie niet noodzakelijkerwijs impliceert dat het token rechtstreeks is uitgegeven als reactie op de aanvraag van de gebruikers toepassing. Tokens worden voor minder dan hun levens duur in de cache opgeslagen, zodat een toepassing gegarandeerd een geldig token kan ontvangen. Het is raadzaam dat de toepassings code zichzelf bewaart alle toegangs tokens die worden opgehaald. de cache sleutel moet (een afleiding van) de doel groep bevatten. 


Voorbeeld aanvraag:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
positie

| Element | Description |
| ------- | ----------- |
| `GET` | De HTTP-term waarmee wordt aangegeven dat u gegevens wilt ophalen uit het eind punt. In dit geval een OAuth-toegangs token. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | Het beheerde identiteits eindpunt voor Service Fabric toepassingen, dat wordt gegeven via de omgevings variabele MSI_ENDPOINT. |
| `api-version` | Een query reeks parameter, waarmee de API-versie van de beheerde identiteits token service wordt opgegeven. Momenteel is `2019-07-01-preview`de enige geaccepteerde waarde en deze kan worden gewijzigd. |
| `resource` | Een query teken reeks parameter, waarmee de App-ID-URI van de doel resource wordt aangegeven. Dit wordt weer gegeven als de `aud` claim (publiek) van het uitgegeven token. In dit voor beeld wordt een token aangevraagd voor toegang tot Azure Key Vault, waarvan de URI van\/de App-ID https:/keyvault.Azure.com/is. |
| `Secret` | Een veld voor de HTTP-aanvraag header, dat wordt vereist door de Service Fabric Managed Identity token service voor Service Fabric Services om de oproepende functie te verifiëren. Deze waarde wordt gegeven door de omgevings variabele SF runtime via MSI_SECRET. |


Voorbeeld antwoord:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://keyvault.azure.com/"
}
```
positie

| Element | Description |
| ------- | ----------- |
| `token_type` | Het type token; in dit geval is het toegangs token ' Bearer ', wat betekent dat de presentator (' Bearer ') van dit token het beoogde onderwerp van het token is. |
| `access_token` | Het aangevraagde toegangs token. Wanneer u een beveiligd rest API aanroept, wordt het token in `Authorization` het veld aanvraag header Inge sloten als een Bearer-token, waardoor de API de aanroeper kan verifiëren. | 
| `expires_on` | De tijds tempel van de verval datum van het toegangs token; wordt weer gegeven als het aantal seconden van "1970-01-01T0:0: 0Z UTC" en komt overeen met de `exp` claim van het token. In dit geval verloopt het token op 2019-08-08T06:10:11 + 00:00 (in RFC 3339)|
| `resource` | De resource waarvoor het toegangs token is uitgegeven, opgegeven via de `resource` query teken reeks parameter van de aanvraag, komt overeen met de claim ' AUD ' van de token. |


## <a name="acquiring-an-access-token-using-c"></a>Een toegangs token verkrijgen metC#
De bovenstaande wordt weer in C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;

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
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("MSI_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("MSI_SECRET");
            var managedIdentityAuthenticationHeader = "secret";
            var managedIdentityApiVersion = "2019-07-01-preview";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);

            try
            {
                var response = await new HttpClient().SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var accessToken = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                return accessToken.Trim('"');
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
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Toegang tot Key Vault vanuit een Service Fabric-toepassing met behulp van beheerde identiteit
Dit voor beeld bouwt voort op het bovenstaande om de toegang tot een geheim dat is opgeslagen in een Key Vault met beheerde identiteit te demonstreren.

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

            Console.WriteLine($"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Console.WriteLine("\n== Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    var secretMetadata = secretResponse.Body.ToString();
                    Console.WriteLine($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    Console.WriteLine($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
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

            Console.WriteLine(response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority"></param>
        /// <param name="resource"></param>
        /// <param name="scope"></param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            if (config.DoVerboseLogging)
                Console.WriteLine($"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";
            if (config.DoVerboseLogging)
                Console.WriteLine($"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            if (config.DoVerboseLogging)
                Console.WriteLine($"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            if (config.DoVerboseLogging)
                Console.WriteLine($"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var accessToken = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            if (config.DoVerboseLogging)
                Console.WriteLine("returning access code..");

            return accessToken.Trim('"');
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
```

## <a name="error-handling"></a>Foutafhandeling
Het veld status code van de HTTP-antwoord header geeft de status van het succes van de aanvraag aan. de status ' 200 OK ' geeft aan dat geslaagd is en het antwoord bevat het toegangs token zoals hierboven is beschreven. Hieronder vindt u een korte opsomming van mogelijke fout berichten.

| Statuscode | Fout reden | Omgaan met |
| ----------- | ------------ | ------------- |
| 404 niet gevonden. | Onbekende verificatie code of de toepassing heeft geen beheerde identiteit toegewezen. | Corrigeer de toepassings instellingen of de code voor het ophalen van tokens. |
| 429 te veel aanvragen. |  De beperkings limiet is bereikt, opgelegd door AAD of SF. | Probeer het opnieuw met exponentiële uitstel. Zie de onderstaande instructies. |
| 4xx-fout in de aanvraag. | Een of meer van de aanvraag parameters zijn onjuist. | Probeer het niet opnieuw.  Raadpleeg de fout Details voor meer informatie.  4xx fouten zijn tijdens de ontwerp fase.|
| 5xx-fout van service. | Het subsysteem Managed Identity of Azure Active Directory retourneerde een tijdelijke fout. | Het is veilig om na een korte tijd opnieuw te proberen. U kunt een beperkings voorwaarde (429) bereiken wanneer u het opnieuw probeert.|

Als er een fout optreedt, bevat de bijbehorende HTTP-antwoord tekst een JSON-object met de fout Details:

| Element | Description |
| ------- | ----------- |
| code | Fout code. |
| correlationId | Een correlatie-ID die kan worden gebruikt voor fout opsporing. |
| message | Uitgebreide beschrijving van de fout. **Fout beschrijvingen kunnen op elk gewenst moment worden gewijzigd. Niet afhankelijk van het fout bericht zelf.**|

Voorbeeld fout:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Hier volgt een lijst met typische Service Fabric fouten die specifiek zijn voor beheerde identiteiten:

| Code | Message | Description | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Het geheim is niet gevonden in de aanvraag headers. | De verificatie code is niet in de aanvraag opgenomen. | 
| ManagedIdentityNotFound | Beheerde identiteit niet gevonden voor de opgegeven toepassingshost. | De toepassing heeft geen identiteit, of de verificatie code is onbekend. |
| ArgumentNullOrEmpty | De para meter ' resource ' mag niet null of een lege teken reeks zijn. | De resource (doel groep) is niet in de aanvraag opgenomen. |
| InvalidApiVersion | De API-versie wordt niet ondersteund. De ondersteunde versie is 2019-07-01-preview. | Ontbrekende of niet-ondersteunde API-versie opgegeven in de aanvraag-URI. |
| InternalServerError | Er is een fout opgetreden. | Er is een fout opgetreden in het beheerde identiteits subsysteem, mogelijk buiten de Service Fabric stack. De meest waarschijnlijke oorzaak is een onjuiste waarde die is opgegeven voor de resource (Controleer op het navolgen van '/'?) | 

## <a name="retry-guidance"></a>Richt lijnen voor opnieuw proberen 

Normaal gesp roken is de fout code alleen opnieuw proberen 429 (te veel aanvragen). interne server fouten/5xx-fout codes kunnen opnieuw worden geprobeerd, hoewel de oorzaak mogelijk permanent is. 

Beperkings limieten zijn van toepassing op het aantal aanroepen van het subsysteem Managed Identity, met name de upstream-afhankelijkheden (de beheerde identiteit Azure-service of de Secure Token-Service). Service Fabric tokens op verschillende niveaus in de pijp lijn op te slaan in de cache, maar gezien de gedistribueerde aard van de betrokken onderdelen, kan de aanroeper mogelijk inconsistente beperkings reacties ondervinden (dat wil zeggen, de beperking wordt beperkt op één knoop punt of exemplaar van een toepassing, maar niet op een een ander knoop punt tijdens het aanvragen van een token voor dezelfde identiteit.) Wanneer de beperkings voorwaarde is ingesteld, kunnen volgende aanvragen van dezelfde toepassing mislukken met de HTTP-status code 429 (te veel aanvragen) totdat de voor waarde is gewist.  

Het wordt aanbevolen dat aanvragen die zijn mislukt door beperking, als volgt opnieuw worden geprobeerd met een exponentiële uitstel: 

| Index aanroepen | Actie voor het ontvangen van 429 | 
| --- | --- | 
| 1 | Wacht 1 seconde en probeer het opnieuw |
| 2 | Wacht 2 seconden en probeer het opnieuw |
| 3 | Wacht 4 seconden en probeer het opnieuw |
| 4 | Wacht 8 seconden en probeer het opnieuw |
| 4 | Wacht 8 seconden en probeer het opnieuw |
| 5 | Wacht 16 seconden en probeer het opnieuw |

## <a name="resource-ids-for-azure-services"></a>Resource-Id's voor Azure-Services
Zie [Azure-Services die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-msi.md) voor een lijst met resources die ondersteuning bieden voor Azure AD en de bijbehorende resource-id's.

## <a name="next-steps"></a>Volgende stappen
* [Een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-resources](./how-to-grant-access-other-resources.md)