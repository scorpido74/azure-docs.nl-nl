---
title: Communicatiebeveiliging voor de Microsoft Threat Modeling Tool
titleSuffix: Azure
description: oplossingen voor bedreigingen die worden blootgesteld in de Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 1945025ff89a784908a1a3dffd2240172a6e2449
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687988"
---
# <a name="security-frame-communication-security--mitigations"></a>Beveiligingskader: Communicatiebeveiliging | Mitigaties 
| Product/service | Artikel |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Beveiligde communicatie naar Gebeurtenishub met SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Controleer de bevoegdheden van serviceaccount en controleer of de aangepaste Services of ASP.NET pagina's de beveiliging van CRM respecteren](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Gegevensbeheergateway gebruiken terwijl u on-premises SQL Server verbindt met Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Identiteitsserver** | <ul><li>[Ervoor zorgen dat al het verkeer naar de identiteitsserver via https-verbinding is](#identity-https)</li></ul> |
| **Webtoepassing** | <ul><li>[X.509-certificaten verifiëren die worden gebruikt om SSL-, TLS- en DTLS-verbindingen te verifiëren](#x509-ssltls)</li><li>[SSL-certificaat configureren voor aangepast domein in Azure App Service](#ssl-appservice)</li><li>[Al het verkeer naar Azure App Service afdwingen via HTTPS-verbinding](#appservice-https)</li><li>[Http Strict Transport Security (HSTS) inschakelen](#http-hsts)</li></ul> |
| **Database** | <ul><li>[Zorgen voor SQL-serververbindingsversleuteling en certificaatvalidatie](#sqlserver-validation)</li><li>[Versleutelde communicatie naar SQL-server forceren](#encrypted-sqlserver)</li></ul> |
| **Azure-opslag** | <ul><li>[Controleren of de communicatie met Azure Storage via HTTPS is](#comm-storage)</li><li>[MD5-hash valideren na het downloaden van blob als HTTPS niet kan worden ingeschakeld](#md5-https)</li><li>[SMB 3.0-compatibele client gebruiken om te zorgen voor versleuteling van gegevens in transitnaar Azure File Shares](#smb-shares)</li></ul> |
| **Mobiele client** | <ul><li>[Certificaatvastmaken implementeren](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[HTTPS inschakelen - Secure Transport-kanaal](#https-transport)</li><li>[WCF: Beveiligingsniveau voor berichten instellen op EncryptAndSign](#message-protection)</li><li>[WCF: gebruik een account met de minste bevoegdheden om uw WCF-service uit te voeren](#least-account-wcf)</li></ul> |
| **Web-API** | <ul><li>[Al het verkeer naar web-API's via HTTPS-verbinding forceren](#webapi-https)</li></ul> |
| **Azure Cache voor Redis** | <ul><li>[Ervoor zorgen dat de communicatie met Azure Cache voor Redis via SSL verloopt](#redis-ssl)</li></ul> |
| **IoT-veldgateway** | <ul><li>[Beveiligde gatewayvan apparaat naar veld](#device-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Secure Device to Cloud Gateway communicatie met SSL/TLS](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Beveiligde communicatie naar Gebeurtenishub met SSL/TLS

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Event Hub | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Overzicht van verificatie en beveiligingsmodel van Gebeurtenishubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Stappen** | AmQP- of HTTP-verbindingen beveiligen met gebeurtenishub met SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Controleer de bevoegdheden van serviceaccount en controleer of de aangepaste Services of ASP.NET pagina's de beveiliging van CRM respecteren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Dynamics CRM | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Controleer de bevoegdheden van serviceaccount en controleer of de aangepaste Services of ASP.NET pagina's de beveiliging van CRM respecteren |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Gegevensbeheergateway gebruiken terwijl u on-premises SQL Server verbindt met Azure Data Factory

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Data Factory | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Gekoppelde servicetypen - Azure en On-premises |
| **Verwijzingen**              |[Gegevensbeheergateway verplaatsen tussen on-premises en Azure Data Factory,](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway) [Gegevensbeheergateway](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Stappen** | <p>De Data Management Gateway (DMG) tool is vereist om verbinding te maken met gegevensbronnen die zijn beschermd achter corpnet of een firewall.</p><ol><li>Het vergrendelen van de machine isoleert het Gereedschap DMG en voorkomt dat defecte programma's de gegevensbronmachine beschadigen of snooping. (Bijv. de laatste updates moeten worden geïnstalleerd, minimale vereiste poorten inschakelen, gecontroleerde beheervan accounts, controle ingeschakeld, schijfversleuteling ingeschakeld, enz.)</li><li>Data Gateway-sleutel moet worden gedraaid met regelmatige tussenpozen of wanneer het wachtwoord van het DMG-serviceaccount wordt verlengd</li><li>Gegevensdoorvoer via Link Service moet worden versleuteld</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Ervoor zorgen dat al het verkeer naar de identiteitsserver via https-verbinding is

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Identiteitsserver | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [IdentityServer3 - Sleutels, handtekeningen en cryptografie](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - Implementatie](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Stappen** | Standaard vereist IdentityServer dat alle binnenkomende verbindingen over HTTPS komen. Het is absoluut verplicht dat communicatie met IdentityServer alleen via beveiligde transporten wordt uitgevoerd. Er zijn bepaalde implementatiescenario's zoals SSL offloading waar deze vereiste kan worden versoepeld. Zie de pagina Met de implementatie van de identiteitsserver in de verwijzingen voor meer informatie. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>X.509-certificaten verifiëren die worden gebruikt om SSL-, TLS- en DTLS-verbindingen te verifiëren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Toepassingen die SSL, TLS of DTLS gebruiken, moeten de X.509-certificaten van de entiteiten waarmee ze verbinding maken volledig verifiëren. Dit omvat de verificatie van de certificaten voor:</p><ul><li>Domeinnaam</li><li>Geldigheidsdatums (zowel begin- als vervaldatums)</li><li>Intrekkingsstatus</li><li>Gebruik (bijvoorbeeld serververificatie voor servers, clientverificatie voor clients)</li><li>Vertrouwensketen. Certificaten moeten worden geketend aan een rootcertificeringsinstantie (CA) die wordt vertrouwd door het platform of expliciet is geconfigureerd door de beheerder</li><li>De sleutellengte van de openbare sleutel van het certificaat moet >2048 bits zijn</li><li>Hashing algoritme moet SHA256 en hoger |

## <a name="configure-ssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>SSL-certificaat configureren voor aangepast domein in Azure App Service

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | EnvironmentType - Azure |
| **Verwijzingen**              | [HTTPS inschakelen voor een app in Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Stappen** | Azure maakt standaard al HTTPS in voor elke app met een wildcardcertificaat voor het *.azurewebsites.net-domein. Echter, net als alle wildcard domeinen, het is niet zo veilig als het gebruik van een aangepast domein met eigen certificaat [Verwijzen](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Het wordt aanbevolen om SSL in te schakelen voor het aangepaste domein dat de geïmplementeerde app zal worden geopend via|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Al het verkeer naar Azure App Service afdwingen via HTTPS-verbinding

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | EnvironmentType - Azure |
| **Verwijzingen**              | [HTTPS afdwingen op Azure App Service](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Stappen** | <p>Hoewel Azure al HTTPS voor Azure-app-services inschakelt met een wildcardcertificaat voor het domein *.azurewebsites.net, wordt https niet afgedwongen. Bezoekers kunnen nog steeds toegang krijgen tot de app via HTTP, wat de veiligheid van de app in gevaar kan brengen en dus moet HTTPS expliciet worden afgedwongen. ASP.NET MVC-toepassingen moet het [filter Vereisenhttps](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) gebruiken dat een onbeveiligd HTTP-verzoek dwingt om opnieuw via HTTPS te worden verzonden.</p><p>De URL Rewrite-module, die is opgenomen in Azure App Service, kan ook worden gebruikt om HTTPS af te dwingen. URL Rewrite module stelt ontwikkelaars in staat om regels te definiëren die worden toegepast op binnenkomende aanvragen voordat de aanvragen aan uw toepassing worden overhandigd. URL Rewrite-regels worden gedefinieerd in een web.config-bestand dat is opgeslagen in de hoofdmap van de toepassing</p>|

### <a name="example"></a>Voorbeeld
Het volgende voorbeeld bevat een basisregel voor het herschrijven van URL die alle binnenkomend verkeer dwingt https te gebruiken
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Deze regel werkt door een HTTP-statuscode van 301 (permanente omleiding) terug te sturen wanneer de gebruiker een pagina aanvraagt met HTTP. De 301 verwijst het verzoek om naar dezelfde URL als de gevraagde bezoeker, maar vervangt het HTTP-gedeelte van de aanvraag door HTTPS. Bijvoorbeeld, `HTTP://contoso.com` zou worden doorgestuurd `HTTPS://contoso.com`naar . 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Http Strict Transport Security (HSTS) inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [OWASP HTTP Strict Transport Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **Stappen** | <p>HTTP Strict Transport Security (HSTS) is een opt-in beveiligingsverbetering die door een webtoepassing wordt opgegeven door het gebruik van een speciale reactieheader. Zodra een ondersteunde browser deze header ontvangt, voorkomt die browser dat berichten via HTTP naar het opgegeven domein worden verzonden en in plaats daarvan alle communicatie via HTTPS verzenden. Het voorkomt ook HTTPS klik door middel van prompts op browsers.</p><p>Om HSTS te implementeren, moet de volgende antwoordkop voor een website wereldwijd worden geconfigureerd, in code of in config. Strikte transportbeveiliging: max-leeftijd=300; includeSubDomains HSTS pakt de volgende bedreigingen aan:</p><ul><li>Gebruikersbladwijzers of handmatig `https://example.com` typen en is onderworpen aan een man-in-the-middle-aanvaller: HSTS stuurt automatisch HTTP-aanvragen door naar HTTPS voor het doeldomein</li><li>Webapplicatie die puur HTTPS-artikelen per ongeluk is, bevat HTTP-koppelingen of dient inhoud via HTTP: HSTS stuurt automatisch HTTP-aanvragen door naar HTTPS voor het doeldomein</li><li>Een man-in-the-middle-aanvaller probeert verkeer van een slachtoffergebruiker te onderscheppen met behulp van een ongeldig certificaat en hoopt dat de gebruiker het slechte certificaat accepteert: HSTS staat niet toe dat een gebruiker het ongeldige certificaatbericht overschrijft</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Zorgen voor SQL-serververbindingsversleuteling en certificaatvalidatie

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | SQL Azure  |
| **Kenmerken**              | SQL-versie - V12 |
| **Verwijzingen**              | [Aanbevolen procedures voor het schrijven van secure connection strings voor SQL Database](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Stappen** | <p>Alle communicatie tussen SQL Database en een clienttoepassing wordt te allen tijde versleuteld met Secure Sockets Layer (SSL). SQL Database biedt geen ondersteuning voor onversleutelde verbindingen. Als u certificaten wilt valideren met toepassingscode of hulpprogramma's, vraagt u expliciet een versleutelde verbinding aan en vertrouwt u de servercertificaten niet. Als uw toepassingscode of -hulpprogramma's geen versleutelde verbinding aanvragen, ontvangen ze nog steeds versleutelde verbindingen</p><p>Ze kunnen echter niet valideren van de server certificaten en dus zal gevoelig zijn voor "man in the middle" aanvallen. Als u certificaten wilt valideren `Encrypt=True` `TrustServerCertificate=False` met ADO.NET toepassingscode, stelt u in en in de tekenreeks voor databaseverbindingen. Als u certificaten wilt valideren via SQL Server Management Studio, opent u het dialoogvenster Verbinding maken met server. Klik op Verbinding versleutelen op het tabblad Verbindingseigenschappen</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Versleutelde communicatie naar SQL-server forceren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | OnPrem (OnPrem) |
| **Kenmerken**              | SQL-versie - MSSQL2016, SQL-versie - MSSQL2012, SQL-versie - MSSQL2014 |
| **Verwijzingen**              | [Versleutelde verbindingen naar de databaseengine inschakelen](https://msdn.microsoft.com/library/ms191192)  |
| **Stappen** | Ssl-versleuteling inschakelen verhoogt de beveiliging van gegevens die via netwerken worden verzonden tussen exemplaren van SQL Server en toepassingen. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Controleren of de communicatie met Azure Storage via HTTPS is

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Versleuteling op transportniveau voor Azure-opslag – HTTPS gebruiken](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Stappen** | Als u de beveiliging van Azure Storage-gegevens onderweg wilt garanderen, gebruikt u altijd het HTTPS-protocol wanneer u de REST-API's aanroept of toegang krijgt tot objecten in de opslag. Ook shared access signatures, die kunnen worden gebruikt om toegang tot Azure Storage-objecten te delegeren, bevatten een optie om op te geven dat alleen het HTTPS-protocol kan worden gebruikt bij het gebruik van Gedeelde Toegangshandtekeningen, zodat iedereen die koppelingen met SAS-tokens verzendt, het juiste protocol gebruikt.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>MD5-hash valideren na het downloaden van blob als HTTPS niet kan worden ingeschakeld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | StorageType - Blob |
| **Verwijzingen**              | [Overzicht van Windows Azure Blob MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Stappen** | <p>Windows Azure Blob-service biedt mechanismen om de gegevensintegriteit te garanderen, zowel op de toepassings- als transportlagen. Als u om welke reden dan ook HTTP in plaats van HTTPS moet gebruiken en u werkt met blokblobs, u MD5-controle gebruiken om de integriteit van de blobs die worden overgedragen te verifiëren</p><p>Dit zal helpen bij de bescherming tegen netwerk / transport laag fouten, maar niet noodzakelijkerwijs met intermediaire aanvallen. Als u HTTPS gebruiken, dat beveiliging op transportniveau biedt, is het gebruik van MD5-controle overbodig en overbodig.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Gebruik smb 3.0-compatibele client om te zorgen voor versleuteling van gegevens in transitnaar Azure File-shares

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Mobiele client | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | StorageType - Bestand |
| **Verwijzingen**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Azure File Storage SMB Support voor Windows Clients](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Stappen** | Azure File Storage ondersteunt HTTPS bij het gebruik van de REST API, maar wordt vaker gebruikt als een SMB-bestandsshare die is gekoppeld aan een vm. SMB 2.1 biedt geen ondersteuning voor versleuteling, dus verbindingen zijn alleen toegestaan binnen dezelfde regio in Azure. SMB 3.0 ondersteunt versleuteling en kan echter worden gebruikt met Windows Server 2012 R2, Windows 8, Windows 8.1 en Windows 10, waardoor toegang tussen regio's en zelfs toegang op het bureaublad mogelijk is. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Certificaatvastmaken implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen, Windows Phone |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Certificaat en openbare sleutel vastmaken](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **Stappen** | <p>Certificate pinning verdedigt tegen Man-In-The-Middle (MITM) aanvallen. Pinning is het proces van het associëren van een host met hun verwachte X509-certificaat of openbare sleutel. Zodra een certificaat of openbare sleutel bekend is of wordt gezien voor een host, wordt het certificaat of de openbare sleutel gekoppeld of 'vastgemaakt' aan de host. </p><p>Wanneer een tegenstander probeert SSL MITM-aanval uit te voeren, zal de sleutel van de server van de aanvaller tijdens de SSL-handshake dus afwijken van de sleutel van het `ServerCertificateValidationCallback` vastgemaakte certificaat en wordt het verzoek verwijderd, waardoor het vastzetten van mitm-certificaten kan worden voorkomen door de gemachtigde van ServicePointManager te implementeren.</p>|

### <a name="example"></a>Voorbeeld
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>HTTPS inschakelen - Secure Transport-kanaal

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Stappen** | De toepassingsconfiguratie moet ervoor zorgen dat HTTPS wordt gebruikt voor alle toegang tot gevoelige informatie.<ul><li>**UITLEG:** Als een toepassing gevoelige informatie verwerkt en geen versleuteling op berichtniveau gebruikt, mag deze alleen via een versleuteld transportkanaal communiceren.</li><li>**AANBEVELINGEN:** Zorg ervoor dat HTTP-transport is uitgeschakeld en schakel in plaats daarvan HTTPS-transport in. Vervang bijvoorbeeld de `<httpTransport/>` `<httpsTransport/>` tag door. Vertrouw niet op een netwerkconfiguratie (firewall) om te garanderen dat de toepassing alleen via een beveiligd kanaal kan worden geopend. Vanuit filosofisch oogpunt mag de toepassing niet afhankelijk zijn van het netwerk voor de beveiliging ervan.</li></ul><p>Vanuit praktisch oogpunt houden de mensen die verantwoordelijk zijn voor het beveiligen van het netwerk niet altijd de beveiligingsvereisten van de toepassing bij als ze evolueren.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Beveiligingsniveau voor berichten instellen op EncryptAndSign

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Stappen** | <ul><li>**UITLEG:** Wanneer het beschermingsniveau is ingesteld op "geen" wordt berichtbeveiliging uitgeschakeld. Vertrouwelijkheid en integriteit wordt bereikt met de juiste mate van instelling.</li><li>**Aanbevelingen:**<ul><li>wanneer `Mode=None` - Berichtbeveiliging uitschakelt</li><li>wanneer `Mode=Sign` - Tekens maar niet het bericht versleutelt; moet worden gebruikt wanneer gegevensintegriteit belangrijk is</li><li>wanneer `Mode=EncryptAndSign` - Het bericht ondertekenen en versleutelen</li></ul></li></ul><p>Overweeg om versleuteling uit te schakelen en uw bericht alleen te ondertekenen wanneer u alleen de integriteit van de informatie hoeft te valideren zonder zich zorgen te maken over vertrouwelijkheid. Dit kan handig zijn voor bewerkingen of servicecontracten waarin u de oorspronkelijke afzender moet valideren, maar er geen gevoelige gegevens worden verzonden. Let er bij het verlagen van het beschermingsniveau op dat het bericht geen persoonsgegevens bevat.</p>|

### <a name="example"></a>Voorbeeld
Het configureren van de service en de bewerking om alleen het bericht te ondertekenen, wordt weergegeven in de volgende voorbeelden. Voorbeeld van `ProtectionLevel.Sign`servicecontract: het volgende is een voorbeeld van het gebruik van ProtectionLevel.Sign op servicecontractniveau: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Voorbeeld
Operation contract `ProtectionLevel.Sign` example of (for Granular Control): `ProtectionLevel.Sign` Het volgende is een voorbeeld van het gebruik op operationeel contract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: gebruik een account met de minste bevoegdheden om uw WCF-service uit te voeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Stappen** | <ul><li>**UITLEG:** Voer geen WCF-services uit onder een account met beheerders of hoge bevoegdheden. in het geval van een compromis van de diensten zal dit leiden tot een grote impact.</li><li>**AANBEVELINGEN:** Gebruik een account met de minste bevoegdheden om uw WCF-service te hosten, omdat het het aanvalsoppervlak van uw toepassing vermindert en de potentiële schade vermindert als u wordt aangevallen. Als het serviceaccount aanvullende toegangsrechten vereist voor infrastructuurbronnen zoals MSMQ, het gebeurtenislogboek, prestatiemeteritems en het bestandssysteem, moeten aan deze bronnen de juiste machtigingen worden gegeven, zodat de WCF-service succesvol kan worden uitgevoerd.</li></ul><p>Als uw service namens de oorspronkelijke beller toegang moet krijgen tot specifieke bronnen, gebruikt u imitatie en overdracht om de identiteit van de beller door te voeren voor een downstreamautorisatiecontrole. Gebruik in een ontwikkelingsscenario het lokale netwerkserviceaccount, een speciaal ingebouwd account dat de bevoegdheden heeft verminderd. Maak in een productiescenario een aangepast domeinserviceaccount met de minste bevoegdheden.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Al het verkeer naar web-API's via HTTPS-verbinding forceren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [SSL afdwingen in een web-API-controller](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Stappen** | Als een toepassing zowel een HTTPS- als een HTTP-binding heeft, kunnen clients HTTP nog steeds gebruiken om toegang te krijgen tot de site. Gebruik hiervoor een actiefilter om ervoor te zorgen dat aanvragen voor beveiligde API's altijd via HTTPS zijn.|

### <a name="example"></a>Voorbeeld 
In de volgende code wordt een web-api-verificatiefilter weergegeven dat controleert op SSL: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Voeg dit filter toe aan alle Web API-acties waarvoor SSL nodig is: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-ssl"></a><a id="redis-ssl"></a>Ervoor zorgen dat de communicatie met Azure Cache voor Redis via SSL verloopt

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Cache voor Redis | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Azure Redis SSL-ondersteuning](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Stappen** | Redis-server biedt geen ondersteuning voor SSL uit de doos, maar Azure Cache voor Redis wel. Als u verbinding maakt met Azure Cache voor Redis en uw client SSL ondersteunt, zoals StackExchange.Redis, moet u SSL gebruiken. Standaard is de niet-SSL-poort uitgeschakeld voor nieuwe Azure-cache voor Redis-exemplaren. Controleer of de veilige standaardinstellingen niet worden gewijzigd, tenzij er een afhankelijkheid is van SSL-ondersteuning voor redis-clients. |

Houd er rekening mee dat Redis is ontworpen om toegankelijk te zijn voor vertrouwde clients in vertrouwde omgevingen. Dit betekent dat het meestal geen goed idee is om de Redis-instantie rechtstreeks bloot te stellen aan het internet of, in het algemeen, aan een omgeving waar niet-vertrouwde clients rechtstreeks toegang hebben tot de Redis TCP-poort of UNIX-socket. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Beveiligde gatewayvan apparaat naar veld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-veldgateway | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Voor IP-apparaten kan het communicatieprotocol doorgaans worden ingekapseld in een SSL/TLS-kanaal om gegevens tijdens het transport te beschermen. Voor andere protocollen die SSL/TLS niet ondersteunen, moet u onderzoeken of er veilige versies van het protocol zijn die beveiliging bieden op transport- of berichtlaag. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Secure Device to Cloud Gateway communicatie met SSL/TLS

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT Cloud Gateway | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Kies uw communicatieprotocol](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Stappen** | Beveilig HTTP/AMQP- of MQTT-protocollen met SSL/TLS. |
