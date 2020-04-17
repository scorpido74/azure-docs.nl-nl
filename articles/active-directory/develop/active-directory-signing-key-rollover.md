---
title: Rollover van ondertekeningssleutels in Azure AD
description: In dit artikel worden de aanbevolen procedures voor de rollover van de ondertekeningssleutel voor Azure Active Directory besproken
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: e0a38eb03df3d1da64172842fb6eca3cd762f9cd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537233"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Rollover van sleutels ondertekenen in Azure Active Directory
In dit artikel wordt besproken wat u moet weten over de openbare sleutels die worden gebruikt in Azure Active Directory (Azure AD) om beveiligingstokens te ondertekenen. Het is belangrijk op te merken dat deze sleutels periodiek omrollen en in geval van nood onmiddellijk kunnen worden omgedraaid. Alle toepassingen die Azure AD gebruiken, moeten het sleutelrolloverproces programmatisch kunnen verwerken of een periodiek handmatig rolloverproces kunnen opzetten. Lees verder om te begrijpen hoe de sleutels werken, hoe u de impact van de rollover op uw toepassing beoordelen en hoe u uw toepassing bijwerken of een periodiek handmatig rolloverproces opzetten om de sleutelrollover te verwerken indien nodig.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Overzicht van ondertekeningssleutels in Azure AD
Azure AD maakt gebruik van public-key cryptografie gebouwd op industriestandaarden om vertrouwen te creëren tussen zichzelf en de toepassingen die het gebruiken. In de praktijk werkt dit op de volgende manier: Azure AD gebruikt een ondertekeningssleutel die bestaat uit een openbaar en privé sleutelpaar. Wanneer een gebruiker zich aanmeldt bij een toepassing die Azure AD gebruikt voor verificatie, maakt Azure AD een beveiligingstoken dat informatie over de gebruiker bevat. Dit token wordt ondertekend door Azure AD met behulp van de privésleutel voordat het wordt teruggestuurd naar de toepassing. Om te controleren of het token geldig is en afkomstig is van Azure AD, moet de toepassing de handtekening van het token valideren met behulp van de openbare sleutel die is blootgesteld door Azure AD die is opgenomen in het [detectiedocument van](https://openid.net/specs/openid-connect-discovery-1_0.html) de tenant openid connect of het [metagegevensdocument](../azuread-dev/azure-ad-federation-metadata.md)van de SAML/WS-Fed-federatie .

Om veiligheidsredenen rolt de ondertekeningssleutel van Azure AD periodiek en kan, in geval van nood, onmiddellijk worden omgerold. Elke toepassing die integreert met Azure AD moet bereid zijn om een belangrijke rollover-gebeurtenis te verwerken, ongeacht hoe vaak deze kan voorkomen. Als dit niet het zo is en uw toepassing probeert een verlopen sleutel te gebruiken om de handtekening op een token te verifiëren, mislukt de aanmeldingsaanvraag.

Er is altijd meer dan één geldige sleutel beschikbaar in het detectiedocument van OpenID Connect en het metagegevensdocument van de federatie. Uw aanvraag moet bereid zijn om een van de sleutels die in het document zijn opgegeven te gebruiken, omdat de ene sleutel binnenkort kan worden uitgerold, een andere kan de vervanging ervan zijn, enzovoort.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Hoe u beoordelen of uw aanvraag wordt beïnvloed en wat u eraan doen
Hoe uw toepassing omgaat met sleutelrollover, is afhankelijk van variabelen zoals het type toepassing of welk identiteitsprotocol en bibliotheek zijn gebruikt. In de onderstaande secties wordt beoordeeld of de meest voorkomende typen toepassingen worden beïnvloed door de sleutelrollover en worden richtlijnen gegeven voor het bijwerken van de toepassing om automatische rollover te ondersteunen of de sleutel handmatig bij te werken.

* [Native clienttoepassingen die toegang hebben tot bronnen](#nativeclient)
* [Webtoepassingen / API's die toegang krijgen tot bronnen](#webclient)
* [Webtoepassingen / API's die resources beschermen en zijn gebouwd met Azure App Services](#appservices)
* [Webtoepassingen / API's die resources beschermen met .NET OWIN OpenID Connect, WS-Fed of WindowsAzureActiveDirectoryBearerAuthentication middleware](#owin)
* [Webapplicaties / API's die resources beschermen met .NET Core OpenID Connect of JwtBearerAuthentication middleware](#owincore)
* [Webtoepassingen / API's die resources beschermen met de module Node.js passport-azure-ad](#passport)
* [Webapplicaties / API's die resources beschermen en zijn gemaakt met Visual Studio 2015 of hoger](#vs2015)
* [Webapplicaties die resources beschermen en zijn gemaakt met Visual Studio 2013](#vs2013)
* Web API's beschermen resources en zijn gemaakt met Visual Studio 2013
* [Webapplicaties die resources beschermen en zijn gemaakt met Visual Studio 2012](#vs2012)
* [Webapplicaties die resources beschermen en zijn gemaakt met Visual Studio 2010, 2008 o met Windows Identity Foundation](#vs2010)
* [Webapplicaties / API's die bronnen beschermen met behulp van andere bibliotheken of handmatig een van de ondersteunde protocollen implementeren](#other)

Deze richtlijn is **niet** van toepassing op:

* Toepassingen die zijn toegevoegd vanuit Azure AD Application Gallery (inclusief Aangepast) hebben afzonderlijke richtlijnen met betrekking tot ondertekeningssleutels. [Meer informatie.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* On-premises toepassingen die via de proxy van de toepassing worden gepubliceerd, hoeven zich geen zorgen te maken over het ondertekenen van sleutels.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Native clienttoepassingen die toegang hebben tot bronnen
Toepassingen die alleen toegang hebben tot bronnen (d.w.z. Microsoft Graph, KeyVault, Outlook API en andere Microsoft API's) verkrijgen over het algemeen alleen een token en geven het door aan de eigenaar van de bron. Gezien het feit dat ze geen middelen beschermen, inspecteren ze het token niet en hoeven ze er daarom niet voor te zorgen dat het goed is ondertekend.

Native client applicaties, of desktop of mobiel, vallen in deze categorie en worden dus niet beïnvloed door de rollover.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Webtoepassingen / API's die toegang krijgen tot bronnen
Toepassingen die alleen toegang hebben tot bronnen (d.w.z. Microsoft Graph, KeyVault, Outlook API en andere Microsoft API's) verkrijgen over het algemeen alleen een token en geven het door aan de eigenaar van de bron. Gezien het feit dat ze geen middelen beschermen, inspecteren ze het token niet en hoeven ze er daarom niet voor te zorgen dat het goed is ondertekend.

Webtoepassingen en web-API's die de app-only flow (clientreferenties / clientcertificaat) gebruiken, vallen in deze categorie en worden dus niet beïnvloed door de rollover.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Webtoepassingen / API's die resources beschermen en zijn gebouwd met Azure App Services
De Functionaliteit voor verificatie / autorisatie (Azure App Services) heeft al de nodige logica om de sleutelrollover automatisch af te handelen.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Webtoepassingen / API's die resources beschermen met .NET OWIN OpenID Connect, WS-Fed of WindowsAzureActiveDirectoryBearerAuthentication middleware
Als uw toepassing de middleware .NET OWIN OpenID Connect, WS-Fed of WindowsAzureActiveDirectoryBearerAuthentication gebruikt, heeft deze al de nodige logica om de rollover van sleutels automatisch af te handelen.

U bevestigen dat uw toepassing een van deze gebruikt door te zoeken naar een van de volgende fragmenten in de Startup.cs of Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Webapplicaties / API's die resources beschermen met .NET Core OpenID Connect of JwtBearerAuthentication middleware
Als uw toepassing de .NET Core OWIN OpenID Connect of JwtBearerAuthentication middleware gebruikt, heeft deze al de nodige logica om de sleutelrollover automatisch af te handelen.

U bevestigen dat uw toepassing een van deze gebruikt door te zoeken naar een van de volgende fragmenten in de Startup.cs of Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Webtoepassingen / API's die resources beschermen met de module Node.js passport-azure-ad
Als uw toepassing de node.js-paspoort-advertentiemodule gebruikt, heeft deze al de nodige logica om de sleutelrollover automatisch af te handelen.

U bevestigen dat uw aanvraag paspoort-advertentie door te zoeken naar het volgende fragment in de app.js van uw toepassing

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Webapplicaties / API's die resources beschermen en zijn gemaakt met Visual Studio 2015 of hoger
Als uw toepassing is gebouwd met behulp van een sjabloon voor webtoepassingen in Visual Studio 2015 of hoger en u **Werk- of schoolaccounts** hebt geselecteerd in het menu **Verificatie wijzigen,** heeft deze al de nodige logica om de rollover van sleutels automatisch af te handelen. Deze logica, ingebed in de OWIN OpenID Connect middleware, haalt en caches de sleutels uit het Detectiedocument OpenID Connect op en ververst deze periodiek.

Als u handmatig verificatie aan uw oplossing hebt toegevoegd, beschikt uw toepassing mogelijk niet over de vereiste sleutelrolloverlogica. U moet het zelf schrijven, of volg de stappen in [webapplicaties / API's met behulp van andere bibliotheken of handmatig implementeren van een van de ondersteunde protocollen](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Webapplicaties die resources beschermen en zijn gemaakt met Visual Studio 2013
Als uw toepassing is gebouwd met behulp van een sjabloon voor webtoepassingen in Visual Studio 2013 en u **Organisatieaccounts** hebt geselecteerd in het menu **Verificatie wijzigen,** heeft deze al de nodige logica om de sleutelrollover automatisch af te handelen. Deze logica slaat de unieke id van uw organisatie en de ondertekeningssleutelgegevens op in twee databasetabellen die aan het project zijn gekoppeld. U de verbindingstekenreeks voor de database vinden in het Web.config-bestand van het project.

Als u handmatig verificatie aan uw oplossing hebt toegevoegd, beschikt uw toepassing mogelijk niet over de vereiste sleutelrolloverlogica. U moet het zelf schrijven, of volg de stappen in [webapplicaties / API's met behulp van andere bibliotheken of handmatig implementeren van een van de ondersteunde protocollen.](#other).

Met de volgende stappen u controleren of de logica goed werkt in uw toepassing.

1. Open de oplossing in Visual Studio 2013 en klik op het tabblad **Server explorer** in het rechtervenster.
2. **Gegevensverbindingen uitvouwen,** **standaardverbinding**en vervolgens **tabellen**. Zoek de tabel **Autoriteitsleutels uitgeven,** klik er met de rechtermuisknop op en klik op **Tabelgegevens weergeven.**
3. In de tabel **UitgifteAutoriteitSleutels** is er ten minste één rij, wat overeenkomt met de duimafdrukwaarde voor de sleutel. Verwijder alle rijen in de tabel.
4. Klik met de rechtermuisknop op de tabel **Tenants** en klik vervolgens op **Tabelgegevens weergeven**.
5. In de tabel **Tenants** is er ten minste één rij, wat overeenkomt met een unieke adreslijsttenant-id. Verwijder alle rijen in de tabel. Als u de rijen in zowel de tabel **Tenants** als de tabel **Van Autoriteitsleutels** niet verwijdert, krijgt u een foutmelding bij runtime.
6. Maak de toepassing en voer deze uit. Nadat je bent ingelogd op je account, kun je de toepassing stoppen.
7. Ga terug naar de **Server Explorer** en bekijk de waarden in de tabel Autoriteitsleutels en **Tenants** **uitgeven.** U zult merken dat ze automatisch opnieuw zijn gevuld met de juiste informatie uit het metagegevensdocument van de federatie.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Web API's beschermen resources en zijn gemaakt met Visual Studio 2013
Als u een web-API-toepassing hebt gemaakt in Visual Studio 2013 met behulp van de web-API-sjabloon en vervolgens **Organisatieaccounts** hebt geselecteerd in het menu **Verificatie wijzigen,** hebt u al de benodigde logica in uw toepassing.

Als u verificatie handmatig hebt geconfigureerd, volgt u de onderstaande instructies om te leren hoe u uw web-API configureert om de belangrijkste gegevens automatisch bij te werken.

In het volgende codefragment wordt uitgelegd hoe u de nieuwste sleutels uit het metagegevensdocument van de federatie ophalen en gebruikt u vervolgens de [JWT-tokenhandler](https://msdn.microsoft.com/library/dn205065.aspx) om het token te valideren. Het codefragment gaat ervan uit dat u uw eigen cachingmechanisme gebruikt om de sleutel voort te houden om toekomstige tokens van Azure AD te valideren, of dit nu in een database, configuratiebestand of elders is.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Webapplicaties die resources beschermen en zijn gemaakt met Visual Studio 2012
Als uw toepassing is gebouwd in Visual Studio 2012, hebt u waarschijnlijk het hulpprogramma voor identiteit en toegang gebruikt om uw toepassing te configureren. Het is ook waarschijnlijk dat u gebruik maakt van de [Validering Issuer Name Registry (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). De VINR is verantwoordelijk voor het bijhouden van informatie over vertrouwde identiteitsproviders (Azure AD) en de sleutels die worden gebruikt om tokens te valideren die door hen zijn uitgegeven. De VINR maakt het ook gemakkelijk om de belangrijkste informatie die is opgeslagen in een Web.config-bestand automatisch bij te werken door het nieuwste federatiemetagegevensdocument te downloaden dat is gekoppeld aan uw directory, te controleren of de configuratie verouderd is met het nieuwste document en de toepassing bij te werken om de nieuwe sleutel te gebruiken als dat nodig is.

Als u uw toepassing hebt gemaakt met behulp van een van de codevoorbeelden of walkthrough-documentatie van Microsoft, is de sleutelrolloverlogica al opgenomen in uw project. U zult merken dat de onderstaande code al bestaat in uw project. Als uw toepassing deze logica nog niet heeft, volgt u de onderstaande stappen om deze toe te voegen en om te controleren of deze correct werkt.

1. Voeg in **Solution Explorer**een verwijzing toe naar de **system.identitymodel-assemblage** voor het juiste project.
2. Open het **Global.asax.cs** bestand en voeg de volgende richtlijnen toe:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Voeg de volgende methode toe aan het **Global.asax.cs** bestand:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Roep de methode **RefreshValidationSettings()** aan in de methode **Application_Start(),** **Global.asax.cs zoals** weergegeven:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Zodra u deze stappen hebt gevolgd, wordt de Web.config van uw toepassing bijgewerkt met de meest recente informatie uit het metagegevensdocument van de federatie, inclusief de nieuwste sleutels. Deze update vindt plaats elke keer dat uw groep van toepassingen wordt gerecycled in IIS; standaard IIS is ingesteld om toepassingen elke 29 uur te recyclen.

Volg de onderstaande stappen om te controleren of de sleutelrolloverlogica werkt.

1. Nadat u hebt geverifieerd dat uw toepassing de bovenstaande code gebruikt, opent u het **web.config-bestand** en navigeert u naar het ** \<>** blok van uitgeverNameRegistry, specifiek op zoek naar de volgende paar regels:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Wijzig in de ** \<instelling thumbprint=">** de duimafdrukwaarde door een teken te vervangen door een ander teken. Sla het **bestand Web.config** op.
3. Bouw de toepassing en voer deze uit. Als u het aanmeldingsproces voltooien, werkt uw toepassing de sleutel bij door de vereiste informatie te downloaden uit het federatiemetagegevensdocument van uw directory. Als u problemen ondervindt bij het aanmelden, moet u ervoor zorgen dat de wijzigingen in uw toepassing correct zijn door het [aanmelden aan uw webtoepassing toe te voegen met het Azure AD-artikel](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) of het volgende codevoorbeeld te downloaden en te inspecteren: [Multi-Tenant Cloud Application for Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Webapplicaties die resources beschermen en zijn gemaakt met Visual Studio 2008 of 2010 en Windows Identity Foundation (WIF) v1.0 voor .NET 3.5
Als u een toepassing op WIF v1.0 hebt gebouwd, is er geen mechanisme om de configuratie van uw toepassing automatisch te vernieuwen om een nieuwe sleutel te gebruiken.

* *Gemakkelijkste manier* Gebruik de FedUtil-tooling die is opgenomen in de WIF SDK, waarmee het nieuwste metagegevensdocument kan worden opgehaald en uw configuratie kan worden bijgewerkt.
* Werk uw toepassing bij naar .NET 4.5, inclusief de nieuwste versie van WIF in de naamruimte van het systeem. U vervolgens het [Validerende Issuer Name Registry (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) gebruiken om automatische updates van de configuratie van de toepassing uit te voeren.
* Voer een handmatige rollover uit volgens de instructies aan het einde van dit begeleidingsdocument.

Instructies om de FedUtil te gebruiken om uw configuratie bij te werken:

1. Controleer of u de WIF v1.0 SDK op uw ontwikkelmachine hebt geïnstalleerd voor Visual Studio 2008 of 2010. U [het downloaden van hier](https://www.microsoft.com/en-us/download/details.aspx?id=4451) als je nog niet hebt geïnstalleerd.
2. Open in Visual Studio de oplossing en klik met de rechtermuisknop op het toepasselijke project en selecteer **Metagegevens van de federatie bijwerken.** Als deze optie niet beschikbaar is, zijn FedUtil en/of de WIF v1.0 SDK niet geïnstalleerd.
3. Selecteer in de prompt **Bijwerken** om te beginnen met het bijwerken van de federatiemetagegevens. Als u toegang hebt tot de serveromgeving waar de toepassing wordt gehost, u optioneel de [automatische updateplanner van](https://msdn.microsoft.com/library/ee517272.aspx)FedUtil gebruiken.
4. Klik **op Voltooien** om het updateproces te voltooien.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Webapplicaties / API's die bronnen beschermen met behulp van andere bibliotheken of handmatig een van de ondersteunde protocollen implementeren
Als u een andere bibliotheek gebruikt of een van de ondersteunde protocollen handmatig implementeert, moet u de bibliotheek of uw implementatie controleren om ervoor te zorgen dat de sleutel wordt opgehaald uit het detectiedocument van OpenID Connect of het metagegevensdocument van de federatie. Een manier om dit te controleren is door een zoekopdracht in uw code of de code van de bibliotheek uit te voeren voor oproepen naar het OpenID-detectiedocument of het metagegevensdocument van de federatie.

Als ze ergens worden opgeslagen of hardcoded in uw toepassing, u handmatig ophalen van de sleutel en dienovereenkomstig bijwerken door het uitvoeren van een handmatige rollover volgens de instructies aan het einde van dit begeleidingsdocument. **Het wordt sterk aangemoedigd dat u uw toepassing verbetert om automatische rollover te ondersteunen** met behulp van een van de benaderingen die in dit artikel worden beschreven om toekomstige onderbrekingen en overhead te voorkomen als Azure AD de rollover-cadans verhoogt of een nood-out-of-band-rollover heeft.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Hoe u uw toepassing testen om te bepalen of deze wordt beïnvloed
U valideren of uw toepassing automatische sleutelrollover ondersteunt door de scripts te downloaden en de instructies in [deze GitHub-repository te volgen.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Een handmatige rollover uitvoeren als uw toepassing geen automatische rollover ondersteunt
Als uw toepassing **geen** ondersteuning biedt voor automatische rollover, moet u een proces instellen dat periodiek de ondertekeningssleutels van Azure AD controleert en een handmatige rollover dienovereenkomstig uitvoert. [Deze GitHub repository](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) bevat scripts en instructies over hoe dit te doen.
