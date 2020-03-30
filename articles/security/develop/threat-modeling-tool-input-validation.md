---
title: Invoervalidatie - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
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
ms.openlocfilehash: f443bf3111d2ab97874bdc62ec1370d17e2fc406
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728062"
---
# <a name="security-frame-input-validation--mitigations"></a>Beveiligingsframe: invoervalidatie | Mitigaties 
| Product/service | Artikel |
| --------------- | ------- |
| **Webtoepassing** | <ul><li>[XSLT-scripting uitschakelen voor alle transformaties met niet-vertrouwde stijlbladen](#disable-xslt)</li><li>[Zorg ervoor dat elke pagina die door de gebruiker controleerbare inhoud kan bevatten, zich afziet van automatische MIME-sniffing](#out-sniffing)</li><li>[Xml-entiteitsresolutie harderen of uitschakelen](#xml-resolution)</li><li>[Toepassingen die http.sys gebruiken, voeren verificatie van URL-canonicalisatie uit](#app-verification)</li><li>[Zorg ervoor dat er geschikte besturingselementen zijn bij het accepteren van bestanden van gebruikers](#controls-users)</li><li>[Ervoor zorgen dat typeveilige parameters worden gebruikt in webtoepassing voor gegevenstoegang](#typesafe)</li><li>[Afzonderlijke modelbindingsklassen of bindende filterlijsten gebruiken om kwetsbaarheid voor MVC-massatoewijzing te voorkomen](#binding-mvc)</li><li>[Onvertrouwde webuitvoer coderen voordat deze wordt weergegeven](#rendering)</li><li>[Invoervalidatie en -filtering uitvoeren op alle eigenschappen van het tekenreekstype Model](#typemodel)</li><li>[Ontsmetting moet worden toegepast op formuliervelden die alle tekens accepteren, bijvoorbeeld teksteditor met rijke tekst](#richtext)</li><li>[DOM-elementen niet toewijzen aan putten die geen ingebouwde codering hebben](#inbuilt-encode)</li><li>[Alle omleidingen binnen de toepassing valideren worden gesloten of veilig uitgevoerd](#redirect-safe)</li><li>[Invoervalidatie implementeren op alle parameters voor tekenreekstypen die door methoden voor verwerkingsapparaten worden geaccepteerd](#string-method)</li><li>[Time-out voor bovengrens instellen voor verwerking van reguliere expressies om DoS te voorkomen vanwege slechte reguliere expressies](#dos-expression)</li><li>[Vermijd het gebruik van Html.Raw in razorweergaven](#html-razor)</li></ul> | 
| **Database** | <ul><li>[Dynamische query's niet gebruiken in opgeslagen procedures](#stored-proc)</li></ul> |
| **Web-API** | <ul><li>[Ervoor zorgen dat modelvalidatie wordt uitgevoerd op Web API-methoden](#validation-api)</li><li>[Invoervalidatie implementeren op alle tekenreekstypeparameters die worden geaccepteerd door web-API-methoden](#string-api)</li><li>[Ervoor zorgen dat typeveilige parameters worden gebruikt in Web API voor gegevenstoegang](#typesafe-api)</li></ul> | 
| **Azure Document DB** | <ul><li>[Parameterized SQL-query's gebruiken voor Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF-invoervalidatie via schemabinding](#schema-binding)</li><li>[WCF- Invoervalidatie via parameterinspecteurs](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>XSLT-scripting uitschakelen voor alle transformaties met niet-vertrouwde stijlbladen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [XSLT-beveiliging](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [xsltSettings.EnableScript, eigenschap](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Stappen** | XSLT ondersteunt scripting in `<msxml:script>` stijlbladen met behulp van het element. Hierdoor kunnen aangepaste functies worden gebruikt in een XSLT-transformatie. Het script wordt uitgevoerd onder de context van het proces dat de transformatie uitvoert. XSLT-script moet worden uitgeschakeld wanneer u zich in een niet-vertrouwde omgeving bevindt om de uitvoering van niet-vertrouwde code te voorkomen. *Als u .NET gebruikt:* XSLT-scripting is standaard uitgeschakeld. U moet er echter voor zorgen dat het `XsltSettings.EnableScript` niet expliciet is ingeschakeld via de eigenschap.|

### <a name="example"></a>Voorbeeld 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Voorbeeld
Als u MSXML 6.0 gebruikt, is XSLT-scripting standaard uitgeschakeld. U moet er echter voor zorgen dat het niet expliciet is ingeschakeld via de eigenschap AllowXsltScript van het XML DOM-object. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Voorbeeld
Als u MSXML 5 of lager gebruikt, is XSLT-scripting standaard ingeschakeld en moet u deze expliciet uitschakelen. Stel de eigenschap XML DOM-object AllowXsltScript in op false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Zorg ervoor dat elke pagina die door de gebruiker controleerbare inhoud kan bevatten, zich afziet van automatische MIME-sniffing

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [IE8-beveiligingsonderdeel V - Uitgebreide bescherming](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Stappen** | <p>Voor elke pagina die door de gebruiker controleerbare `X-Content-Type-Options:nosniff`inhoud kan bevatten, moet u de HTTP-koptekst gebruiken. Als u aan deze vereiste wilt voldoen, u de vereiste koppagina per pagina instellen voor alleen die pagina's die door de gebruiker controleerbare inhoud kunnen bevatten, of u deze globaal instellen voor alle pagina's in de toepassing.</p><p>Elk type bestand dat van een webserver wordt geleverd, heeft een bijbehorend [MIME-type](https://en.wikipedia.org/wiki/Mime_type) (ook wel *een inhoudstype*genoemd) dat de aard van de inhoud beschrijft (dat wil zeggen afbeelding, tekst, toepassing, enz.)</p><p>De header X-Content-Type-Options is een HTTP-header waarmee ontwikkelaars kunnen opgeven dat hun inhoud niet mime-gesnoven mag worden. Deze header is ontworpen om MIME-Sniffing-aanvallen te beperken. Ondersteuning voor deze koptekst is toegevoegd in Internet Explorer 8 (IE8)</p><p>Alleen gebruikers van Internet Explorer 8 (IE8) profiteren van X-Content-Type-Options. Eerdere versies van Internet Explorer respecteren momenteel niet de header X-Content-Type-Options</p><p>Internet Explorer 8 (en hoger) zijn de enige grote browsers die een MIME-sniffing opt-out functie implementeren. Als en wanneer andere grote browsers (Firefox, Safari, Chrome) vergelijkbare functies implementeren, wordt deze aanbeveling bijgewerkt om ook syntaxis voor die browsers op te nemen</p>|

### <a name="example"></a>Voorbeeld
Als u de vereiste koptekst wereldwijd wilt inschakelen voor alle pagina's in de toepassing, u een van de volgende handelingen doen: 

* Voeg de koptekst toe in het web.config-bestand als de toepassing wordt gehost door Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* De koptekst toevoegen\_via de algemene toepassing BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Aangepaste HTTP-module implementeren 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* U de vereiste koptekst alleen inschakelen voor specifieke pagina's door deze toe te voegen aan afzonderlijke antwoorden: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Xml-entiteitsresolutie harderen of uitschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [UITBREIDING VAN XML-entiteiten,](https://capec.mitre.org/data/definitions/197.html) [XML Denial of Service-aanvallen en -verdediging ,](https://msdn.microsoft.com/magazine/ee335713.aspx) [MSXML-beveiligingsoverzicht](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [aanbevolen procedures voor het beveiligen van MSXML-code](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [NSXMLParserDelegate Protocol Reference](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), Externe verwijzingen [oplossen](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Stappen**| <p>Hoewel het niet op grote schaal wordt gebruikt, is er een functie van XML waarmee de XML-parser macro-entiteiten kan uitbreiden met waarden die zijn gedefinieerd in het document zelf of uit externe bronnen. Het document kan bijvoorbeeld een entiteit 'bedrijfsnaam' definiëren met de waarde 'Microsoft', zodat elke keer dat de tekst&companyname;in het document wordt weergegeven, deze automatisch wordt vervangen door de tekst Microsoft. Of het document kan een entiteit "MSFTStock" definiëren die verwijst naar een externe webservice om de huidige waarde van Microsoft-voorraad op te halen.</p><p>Dan verschijnt&MSFTStock;het op elk moment in het document, maar wordt automatisch vervangen door de huidige aandelenkoers. Deze functionaliteit kan echter worden misbruikt om DoS-voorwaarden (Denial of Service) te maken. Een aanvaller kan meerdere entiteiten nesten om een XML-bom met exponentiële uitbreiding te maken die al het beschikbare geheugen op het systeem verbruikt. </p><p>Als alternatief kan hij een externe referentie maken die een oneindige hoeveelheid gegevens terugstroomt of die gewoon de draad ophangt. Als gevolg hiervan moeten alle teams de interne en/of externe XML-entiteitsresolutie volledig uitschakelen als hun toepassing deze niet gebruikt, of de hoeveelheid geheugen en tijd die de toepassing kan gebruiken voor entiteitsresolutie handmatig beperken als deze functionaliteit absoluut noodzakelijk is. Als de entiteit geen oplossing vereist is voor uw toepassing, schakelt u deze vervolgens uit. </p>|

### <a name="example"></a>Voorbeeld
Voor .NET Framework-code u de volgende benaderingen gebruiken:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Houd er rekening `ProhibitDtd` mee `XmlReaderSettings` dat de `XmlTextReader` standaardwaarde van in waar is, maar daarin onjuist is. Als u XmlReaderSettings gebruikt, hoeft u ProhibitDtd niet expliciet op true in te stellen, maar wordt het aanbevolen omwille van de veiligheid dat u doet. Houd er ook rekening mee dat de klasse XmlDocument standaard entiteitsresolutie toestaat. 

### <a name="example"></a>Voorbeeld
Als u entiteitsresolutie voor `XmlDocument.Load(XmlReader)` XmlDocuments wilt uitschakelen, gebruikt u de overbelasting van de methode Laden en stelt u de juiste eigenschappen in het argument XmlReader in om de resolutie uit te schakelen, zoals wordt geïllustreerd in de volgende code: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Voorbeeld
Als het uitschakelen van entiteitsresolutie niet mogelijk is voor uw toepassing, stelt u de eigenschap XmlReaderSettings.MaxCharactersFromEntities in op een redelijke waarde volgens de behoeften van uw toepassing. Dit zal de impact van potentiële exponentiële uitbreiding DoS-aanvallen beperken. De volgende code geeft een voorbeeld van deze aanpak: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Voorbeeld
Als u inline entiteiten moet oplossen, maar externe entiteiten niet hoeft op te lossen, stelt u de eigenschap XmlReaderSettings.XmlResolver in op null. Bijvoorbeeld: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Houd er rekening mee dat In MSXML6 ProhibitDTD standaard is ingesteld op true (dtd-verwerking uitschakelen). Voor Apple OSX/iOS-code zijn er twee XML-parsers die u gebruiken: NSXMLParser en libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Toepassingen die http.sys gebruiken, voeren verificatie van URL-canonicalisatie uit

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Elke toepassing die http.sys gebruikt, moet de volgende richtlijnen volgen:</p><ul><li>Beperk de URL-lengte tot niet meer dan 16.384 tekens (ASCII of Unicode). Dit is de absolute maximale URL-lengte op basis van de standaard instelling Internet Information Services (IIS) 6. Websites moeten streven naar een kortere lengte dan dit, indien mogelijk</li><li>Gebruik de standaard .NET Framework-klasse I/O-klassen (zoals FileStream) omdat deze profiteren van de canonicalisatieregels in de .NET FX</li><li>Maak expliciet een lijst met bekende bestandsnamen</li><li>Expliciet weigeren bekende bestandstypen die u niet zal dienen UrlScan verwerpt: exe, vleermuis, cmd, com, htw, ida, idq, htr, idc, shtm[l], stm, printer, ini, pol, dat files</li><li>Vang de volgende uitzonderingen:<ul><li>System.ArgumentException (voor apparaatnamen)</li><li>System.NotSupportedException (voor gegevensstromen)</li><li>System.IO.FileNotFoundException (voor ongeldige ontsnapte bestandsnamen)</li><li>System.IO.DirectoryNotFoundException (voor ongeldige ontsnapte dirs)</li></ul></li><li>*Roep niet* naar Win32-bestand I/O API's. Op een ongeldige URL gracieus een 400 fout terug te keren naar de gebruiker, en log de echte fout.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Zorg ervoor dat er geschikte besturingselementen zijn bij het accepteren van bestanden van gebruikers

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Tabel voor het uploaden van bestanden onbeperkt](https://www.owasp.org/index.php/Unrestricted_File_Upload)bestand, [bestandshandtekening](https://www.garykessler.net/library/file_sigs.html) |
| **Stappen** | <p>Geüploade bestanden vormen een aanzienlijk risico voor toepassingen.</p><p>De eerste stap in veel aanvallen is om wat code te krijgen om het systeem te worden aangevallen. Dan hoeft de aanval alleen maar een manier te vinden om de code uit te voeren. Met behulp van een bestandsupload kan de aanvaller de eerste stap uitvoeren. De gevolgen van onbeperkte bestandsupload kunnen variëren, waaronder volledige systeemovername, een overbelast bestandssysteem of database, het doorsturen van aanvallen naar back-endsystemen en eenvoudige ontgezichting.</p><p>Het hangt af van wat de applicatie doet met het geüploade bestand en vooral waar het wordt opgeslagen. Server side validatie van bestandsuploads ontbreekt. De volgende beveiligingsbesturingselementen moeten worden geïmplementeerd voor de functionaliteit voor het uploaden van bestanden:</p><ul><li>Check bestandsextensie (alleen een geldige set toegestane bestandstypen moet worden geaccepteerd)</li><li>Maximale limiet voor bestandsgrootte</li><li>Bestand mag niet worden geüpload naar webroot; de locatie moet een map op niet-systeemstation zijn</li><li>Naamgevingconventie moet worden gevolgd, zodat de geüploade bestandsnaam enige willekeur heeft, om te voorkomen dat bestandsoverschrijft</li><li>Bestanden moeten worden gescand op anti-virus voor het schrijven naar de schijf</li><li>Zorg ervoor dat de bestandsnaam en andere metagegevens (bijvoorbeeld bestandspad) worden gevalideerd voor schadelijke tekens</li><li>Handtekening van bestandsindeling moet worden gecontroleerd om te voorkomen dat een gebruiker een gemaskerd bestand uploadt (bijvoorbeeld het uploaden van een exe-bestand door de extensie te wijzigen in txt)</li></ul>| 

### <a name="example"></a>Voorbeeld
Raadpleeg de onderstaande klasse voor het laatste punt met betrekking tot de validatie van de bestandsindeling: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Ervoor zorgen dat typeveilige parameters worden gebruikt in webtoepassing voor gegevenstoegang

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Als u de verzameling Parameters gebruikt, behandelt SQL de invoer eerder als een letterlijke waarde dan als uitvoerbare code. De verzameling Parameters kan worden gebruikt om type- en lengtebeperkingen voor invoergegevens af te dwingen. Waarden buiten het bereik leiden tot een uitzondering. Als niet voor typeveilige SQL-parameters wordt gebruikt, kunnen aanvallers mogelijk injectieaanvallen uitvoeren die zijn ingesloten in de ongefilterde invoer.</p><p>Gebruik typeveilige parameters bij het maken van SQL-query's om mogelijke SQL-injectieaanvallen te voorkomen die kunnen optreden met ongefilterde invoer. U typeveilige parameters gebruiken met opgeslagen procedures en met dynamische SQL-instructies. Parameters worden door de database als letterlijke waarden behandeld en niet als uitvoerbare code. Parameters worden ook gecontroleerd op type en lengte.</p>|

### <a name="example"></a>Voorbeeld 
In de volgende code ziet u hoe u veilige parameters voor tekst gebruiken met de SqlParameterCollection bij het aanroepen van een opgeslagen procedure. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
In het voorgaande codevoorbeeld mag de invoerwaarde niet langer zijn dan 11 tekens. Als de gegevens niet voldoen aan het type of de lengte die door de parameter wordt gedefinieerd, wordt met de klasse SqlParameter een uitzondering gemaakt. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Afzonderlijke modelbindingsklassen of bindende filterlijsten gebruiken om kwetsbaarheid voor MVC-massatoewijzing te voorkomen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Metagegevenskenmerken](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [kwetsbaarheid en mitigatie](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation)van openbare sleutels , volledige handleiding voor [massatoewijzing in ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Aan de slag met EF met Behulp van MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Stappen** | <ul><li>**Wanneer moet ik op zoek naar over-posting kwetsbaarheden? -** Op te veel plaatsen van kwetsbaarheden kunnen zich voorkomen op elke plaats waar u modelklassen van gebruikersinvoer bindt. Frameworks zoals MVC kunnen gebruikersgegevens weergeven in aangepaste .NET-klassen, waaronder Plain Old CLR-objecten (POCOs). MVC vult deze modelklassen automatisch met gegevens uit de aanvraag, wat een handige weergave biedt voor het omgaan met gebruikersinvoer. Wanneer deze klassen eigenschappen bevatten die niet door de gebruiker moeten worden ingesteld, kan de toepassing kwetsbaar zijn voor over-posting-aanvallen, waardoor de gebruiker controle heeft over gegevens die de toepassing nooit heeft bedoeld. Net als MVC-modelbinding ondersteunen databasetoegangstechnologieën zoals object/relationele mappers zoals Entity Framework vaak ook het gebruik van POCO-objecten om databasegegevens weer te geven. Deze gegevensmodelklassen bieden hetzelfde gemak bij het omgaan met databasegegevens als MVC bij het omgaan met gebruikersinvoer. Omdat zowel MVC als de database vergelijkbare modellen ondersteunen, zoals POCO-objecten, lijkt het eenvoudig om dezelfde klassen voor beide doeleinden opnieuw te gebruiken. Deze praktijk slaagt er niet in om de scheiding van zorgen te bewaren, en het is een gemeenschappelijke ruimte waar onbedoelde eigenschappen worden blootgesteld aan modelbinding, waardoor aanvallen op over-posting mogelijk worden.</li><li>**Waarom zou ik mijn ongefilterde databasemodelklassen niet gebruiken als parameters voor mijn MVC-acties?** Omdat MVC model binding alles in die klasse zal binden. Zelfs als de gegevens niet in uw weergave worden weergegeven, kan een kwaadwillende gebruiker een HTTP-verzoek verzenden met deze gegevens inbegrepen, en MVC zal deze graag binden omdat uw actie zegt dat databaseklasse de vorm is van gegevens die moet worden geaccepteerd voor invoer van gebruikers.</li><li>**Waarom zou ik de zorg over de vorm die wordt gebruikt voor model binding? -** Met behulp van ASP.NET MVC-modelbinding met overdreven brede modellen stelt een toepassing bloot aan over-posting-aanvallen. Over-posting kan aanvallers in staat stellen om toepassingsgegevens te wijzigen die verder gaan dan wat de ontwikkelaar van plan was, zoals het overschrijven van de prijs voor een item of de beveiligingsbevoegdheden voor een account. Toepassingen moeten actiespecifieke bindingsmodellen (of specifieke toegestane eigenschappenfilterlijsten) gebruiken om een expliciet contract te bieden voor welke niet-vertrouwde invoer dit via modelbinding toestaat.</li><li>**Is het hebben van aparte bindingmodellen gewoon dupliceren code? -** Nee, het is een kwestie van scheiding van zorgen. Als u databasemodellen hergebruikt in actiemethoden, zegt u dat elke eigenschap (of subeigenschap) in die klasse door de gebruiker kan worden ingesteld in een HTTP-aanvraag. Als dat niet is wat u wilt dat MVC doet, hebt u een filterlijst of een afzonderlijke klassevorm nodig om MVC te laten zien welke gegevens in plaats daarvan afkomstig kunnen zijn van gebruikersinvoer.</li><li>**Als ik afzonderlijke bindingsmodellen heb voor gebruikersinvoer, moet ik dan al mijn gegevensannotatiekenmerken dupliceren? -** Niet per se. U MetadataTypeAttribute in de klasse databasemodel gebruiken om te koppelen aan de metagegevens in een modelbindingsklasse. Houd er rekening mee dat het type waarnaar wordt verwezen door het Metagegevenskenmerk een subset van het verwijzingstype moet zijn (het kan minder eigenschappen hebben, maar niet meer).</li><li>**Het is vervelend om gegevens heen en weer te verplaatsen tussen gebruikersinvoermodellen en databasemodellen. Kan ik gewoon kopiëren over alle eigenschappen met behulp van reflectie? -** Ja. De enige eigenschappen die in de bindingsmodellen worden weergegeven, zijn de eigenschappen waarvan u hebt vastgesteld dat ze veilig zijn voor gebruikersinvoer. Er is geen veiligheidsreden die voorkomt dat reflectie wordt gebruikt om alle eigenschappen te kopiëren die gemeenschappelijk zijn tussen deze twee modellen.</li><li>**Hoe zit het met [Bind(Uitsluiten ="â €|")]. Kan ik dat gebruiken in plaats van aparte bindingsmodellen? -** Deze aanpak wordt afgeraden. Het gebruik van [Binden(Uitsluiten ="â€|")] betekent dat elke nieuwe eigenschap standaard bindt is. Wanneer een nieuwe eigenschap wordt toegevoegd, is er een extra stap om te onthouden om dingen veilig te houden, in plaats van het ontwerp standaard te beveiligen. Afhankelijk van de ontwikkelaar die deze lijst controleert elke keer dat een eigenschap wordt toegevoegd, is riskant.</li><li>**Is [Bind(Include ="â€|)] nuttig voor bewerkingen bewerken? -** №. [Bind(Include ="â€|")] is alleen geschikt voor insert-achtige bewerkingen (het toevoegen van nieuwe gegevens). Voor bewerkingen in UPDATE-stijl (bestaande gegevens herzien), gebruikt u een andere benadering, zoals het hebben van afzonderlijke bindende modellen of het doorgeven van een expliciete lijst met toegestane eigenschappen naar UpdateModel of TryUpdateModel. Het toevoegen van een kenmerk [Binden(Opnemen ="â€|)) aan een bewerking bewerken betekent dat MVC een objectinstantie maakt en alleen de vermelde eigenschappen instelt, waardoor alle andere eigenschappen op hun standaardwaarden blijven. Wanneer de gegevens worden gehandhaafd, wordt de bestaande entiteit volledig vervangen door de waarden voor weggelaten eigenschappen opnieuw in te stellen op de standaardwaarden. Als IsAdmin bijvoorbeeld is weggelaten uit een kenmerk [Binden(Opnemen ="â€|))) op een bewerking bewerken, wordt elke gebruiker wiens naam via deze actie is bewerkt, gereset naar IsAdmin = false (elke bewerkte gebruiker zou de beheerdersstatus verliezen). Als u updates van bepaalde eigenschappen wilt voorkomen, gebruikt u een van de andere bovenstaande benaderingen. Houd er rekening mee dat sommige versies van MVC-tooling controllerklassen genereren met [Bind(Opnemen ="â€|)] over bewerkingen bewerken en impliceren dat het verwijderen van een eigenschap uit die lijst over-postingaanvallen zal voorkomen. Zoals hierboven beschreven, werkt deze benadering echter niet zoals bedoeld en worden in plaats daarvan alle gegevens in de weggelaten eigenschappen gereset naar hun standaardwaarden.</li><li>**Voor Bewerkingen maken, zijn er kanttekeningen met [Bind(Include ="â €|")] in plaats van afzonderlijke bindingsmodellen? -** Ja. Ten eerste werkt deze aanpak niet voor Scenario's bewerken, waarbij twee afzonderlijke benaderingen moeten worden gehandhaafd om alle over-posting kwetsbaarheden te beperken. Ten tweede, afzonderlijke bindende modellen af te dwingen scheiding van zorgen tussen de vorm die wordt gebruikt voor input van de gebruiker en de vorm die wordt gebruikt voor persistentie, iets [Bind (Include ="â € |")] niet doen. Ten derde, merk op dat [Bind(Include ="â€|")] alleen eigenschappen op het hoogste niveau kan verwerken; u niet alleen delen van subeigenschappen (zoals Details.Name) toestaan in het kenmerk. Ten slotte, en misschien wel het belangrijkste, met behulp van [Bind (Include ="â € |")] voegt een extra stap die moet worden onthouden wanneer de klasse wordt gebruikt voor modelbinding. Als een nieuwe actiemethode zich rechtstreeks aan de gegevensklasse bindt en vergeet een kenmerk [Binden(opnemen ="â€|)) op te nemen, kan deze standaard kwetsbaar zijn voor aanvallen op over-posting, zodat de [Bind(Include ="â€|))] benadering standaard iets minder veilig is. Als u [Bind(Include ="â€|")) gebruikt, moet u ervoor zorgen dat u het altijd opgeeft wanneer uw gegevensklassen worden weergegeven als parameters voor actiemethoden.</li><li>**Hoe zit het met het kenmerk [Binden(Opnemen ="â€|)) op de modelklasse zelf? Vermijdt deze aanpak niet de noodzaak om te onthouden dat het attribuut op elke actiemethode wordt geplaatst? -** Deze aanpak werkt in sommige gevallen. Met behulp van [Bind(Include ="â €|")] op het modeltype zelf (in plaats van op actieparameters met behulp van deze klasse), vermijdt u de noodzaak om te onthouden om het kenmerk [Binden (Opnemen ="â €|")] op elke actiemethode op te nemen. Als u het kenmerk rechtstreeks op de klasse gebruikt, wordt een afzonderlijke oppervlakte van deze klasse gemaakt voor modelbindingsdoeleinden. Deze aanpak maakt echter slechts één modelbindingsvorm per modelklasse mogelijk. Als één actiemethode modelbinding van een veld moet toestaan (bijvoorbeeld een actie die alleen door de beheerder wordt uitgevoerd en andere acties moeten modelbinding van dit veld voorkomen, werkt deze aanpak niet. Elke klasse kan slechts één modelbindingsvorm hebben; Als verschillende acties verschillende modelbindingsvormen nodig hebben, moeten ze deze afzonderlijke vormen weergeven met afzonderlijke modelbindingsklassen of afzonderlijke kenmerken van [Binden(opnemen ="â €|")] op de actiemethoden.</li><li>**Wat zijn bindende modellen? Zijn ze hetzelfde als modellen bekijken? -** Dit zijn twee verwante concepten. Het term bindingsmodel verwijst naar een modelklasse die wordt gebruikt in de parameterlijst van een actie (de vorm is doorgegeven van MVC-model dat aan de actiemethode is gebonden). Het termenweergavemodel verwijst naar een modelklasse die wordt doorgegeven van een actiemethode naar een weergave. Het gebruik van een weergavespecifiek model is een veelvoorkomende benadering voor het doorgeven van gegevens van een actiemethode naar een weergave. Vaak is deze vorm ook geschikt voor modelbinding en kan het term view-model worden gebruikt om hetzelfde model dat op beide plaatsen wordt gebruikt, door te verwijzen. Om precies te zijn, deze procedure spreekt specifiek over bindende modellen, gericht op de vorm doorgegeven aan de actie, dat is wat telt voor massa-toewijzing doeleinden.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Onvertrouwde webuitvoer coderen voordat deze wordt weergegeven

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemene webformulieren, MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Hoe cross-site scripting te voorkomen in ASP.NET,](https://msdn.microsoft.com/library/ms998274.aspx) [Cross-site Scripting](https://cwe.mitre.org/data/definitions/79.html), [XSS (Cross Site Scripting) Preventie Cheat Sheet](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Stappen** | Cross-site scripting (vaak afgekort als XSS) is een aanvalsvector voor online services of een toepassing/component die invoer van het web verbruikt. XSS-kwetsbaarheden kunnen een aanvaller toestaan om script uit te voeren op de machine van een andere gebruiker via een kwetsbare webapplicatie. Kwaadaardige scripts kunnen worden gebruikt om cookies te stelen en anderszins knoeien met de machine van een slachtoffer via JavaScript. XSS wordt voorkomen door het valideren van gebruikersinvoer, ervoor te zorgen dat het goed is gevormd en codering voordat het wordt weergegeven in een webpagina. Invoervalidatie en uitvoercodering kunnen worden uitgevoerd met behulp van webbeveiligingsbibliotheek. Gebruik voor Beheerde code (C,\#VB.NET, enz.) een of meer geschikte coderingsmethoden uit de Web Protection -bibliotheek (Anti-XSS), afhankelijk van de context waarin de gebruikersinvoer zich manifesteert:| 

### <a name="example"></a>Voorbeeld

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Invoervalidatie en -filtering uitvoeren op alle eigenschappen van het tekenreekstype Model

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Validatie,](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation) [Valideren van modelgegevens in een MVC-toepassing,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) [leidende principes voor uw ASP.NET MVC-toepassingen toevoegen](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Stappen** | <p>Alle invoerparameters moeten worden gevalideerd voordat ze in de toepassing worden gebruikt om ervoor te zorgen dat de toepassing wordt beschermd tegen schadelijke gebruikersinvoer. Valideer de invoerwaarden met behulp van validaties met regelmatige expressies aan serverzijde met een whitelistvalidatiestrategie. Onontsmette gebruikersingangen / parameters die aan de methoden worden doorgegeven, kunnen kwetsbaarheden in code-injectie veroorzaken.</p><p>Voor webtoepassingen kunnen toegangspunten ook formuliervelden, QueryStrings, cookies, HTTP-headers en webserviceparameters bevatten.</p><p>De volgende invoervalidatiecontroles moeten worden uitgevoerd op modelbinding:</p><ul><li>De modeleigenschappen moeten worden geannoteerd met regularexpression-annotatie, voor het accepteren van toegestane tekens en de maximaal toelaatbare lengte</li><li>De verwerkingsmethoden moeten de geldigheid van ModelState uitvoeren</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>Ontsmetting moet worden toegepast op formuliervelden die alle tekens accepteren, bijvoorbeeld teksteditor met rijke tekst

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Onveilige invoer](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3)coderen , [HTML-ontsmettingsmiddel](https://github.com/mganss/HtmlSanitizer) |
| **Stappen** | <p>Identificeer alle statische markeringstags die u wilt gebruiken. Een gangbare praktijk is om opmaak `<b>` te beperken `<i>` tot veilige HTML-elementen, zoals (vet) en (cursief).</p><p>Voordat u de gegevens schrijft, codeert u deze. Dit maakt een kwaadaardig script veilig door ervoor te zorgen dat het wordt behandeld als tekst, niet als uitvoerbare code.</p><ol><li>ASP.NET aanvraagvalidatie uitschakelen door het kenmerk ValidateRequest="false" toe te voegen aan de \@ paginarichtlijn</li><li>De tekenreeksinvoer coderen met de methode HtmlEncode</li><li>Gebruik een StringBuilder en roep de methode Vervang aan om selectief de codering te verwijderen op de HTML-elementen die u wilt toestaan</li></ol><p>De pagina-in de verwijzingen schakelt ASP.NET `ValidateRequest="false"`aanvraagvalidatie uit door in te stellen . Het HTML-codeert de invoer `<b>` en `<i>` maakt selectief de en alternatief, een .NET-bibliotheek voor HTML-ontsmetting kan ook worden gebruikt.</p><p>HtmlSanitizer is een .NET-bibliotheek voor het reinigen van HTML-fragmenten en documenten uit constructies die kunnen leiden tot XSS-aanvallen. Het maakt gebruik van AngleSharp om HTML en CSS te ontleden, te manipuleren en weer te geven. HtmlSanitizer kan worden geïnstalleerd als een NuGet-pakket en de gebruikersinvoer kan worden doorgegeven via relevante HTML- of CSS-ontsmettingsmethoden, indien van toepassing, aan de serverzijde. Houd er rekening mee dat ontsmetting als beveiligingscontrole alleen als een laatste optie moet worden beschouwd.</p><p>Invoervalidatie en uitvoercodering worden beschouwd als betere beveiligingscontroles.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>DOM-elementen niet toewijzen aan putten die geen ingebouwde codering hebben

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Veel javascript-functies doen standaard geen codering. Wanneer u via dergelijke functies niet-vertrouwde invoer aan DOM-elementen toegeeft, kan dit leiden tot xss-uitvoeringen (cross site script).| 

### <a name="example"></a>Voorbeeld
Hieronder volgen onveilige voorbeelden: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Niet gebruiken; `innerHtml` in `innerText`plaats daarvan gebruiken . Evenzo, in `$("#elm").html()`plaats van , gebruik`$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Alle omleidingen binnen de toepassing valideren worden gesloten of veilig uitgevoerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Het OAuth 2.0 Authorization Framework - Open Redirectors](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Stappen** | <p>Toepassingsontwerp dat moet worden omgeleid naar een door de gebruiker geleverde locatie, moet de mogelijke omleidingsdoelen beperken naar een vooraf gedefinieerde "veilige" lijst met sites of domeinen. Alle omleidingen in de aanvraag moeten worden gesloten / veilig.</p><p>Om dit te doen:</p><ul><li>Alle omleidingen identificeren</li><li>Implementeer een geschikte mitigatie voor elke omleiding. Passende oplossingen zijn redirect whitelist of bevestiging van de gebruiker. Als een website of service met een open omleidingskwetsbaarheid gebruikmaakt van Facebook/OAuth/OpenID-identiteitsproviders, kan een aanvaller het aanmeldingstoken van een gebruiker stelen en zich voordoen als die gebruiker. Dit is een inherent risico bij het gebruik van OAuth, dat is gedocumenteerd in RFC 6749 "The OAuth 2.0 Authorization Framework", sectie 10.15 "Open Redirects" Op dezelfde manier kunnen de referenties van gebruikers worden aangetast door spear phishing-aanvallen met behulp van open omleidingen</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Invoervalidatie implementeren op alle parameters voor tekenreekstypen die door methoden voor verwerkingsapparaten worden geaccepteerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Modelgegevens valideren in een MVC-toepassing,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) [leidende principes voor uw ASP.NET MVC-toepassingen](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Stappen** | Voor methoden die alleen primitief gegevenstype accepteren, en niet modellen als argument, moet invoervalidatie met behulp van Reguliere expressie worden uitgevoerd. Hier regex.IsMatch moet worden gebruikt met een geldig regex patroon. Als de invoer niet overeenkomt met de opgegeven reguliere expressie, moet het besturingselement niet verder gaan en moet er een adequate waarschuwing met betrekking tot validatiefouten worden weergegeven.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Time-out voor bovengrens instellen voor verwerking van reguliere expressies om DoS te voorkomen vanwege slechte reguliere expressies

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemene webformulieren, MVC5, MVC6  |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [StandaardRegexMatchTimeout, eigenschap](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Stappen** | Stel de wereldwijde standaardtime-out in om denial of service-aanvallen te garanderen tegen slecht gemaakte reguliere expressies, die veel backtracking veroorzaken. Als de verwerkingstijd langer duurt dan de gedefinieerde bovengrens, wordt een time-outuitzondering verwijderd. Als er niets is geconfigureerd, zou de time-out oneindig zijn.| 

### <a name="example"></a>Voorbeeld
De volgende configuratie gooit bijvoorbeeld een RegexMatchTimeoutException als de verwerking meer dan 5 seconden duurt: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Vermijd het gebruik van Html.Raw in razorweergaven

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Stap | ASP.NET Webpagina's (Razor) voeren automatische HTML-codering uit. Alle tekenreeksen die worden afgedrukt op ingesloten codenuggets (@ blokken) worden automatisch HTML-gecodeerd. Wanneer `HtmlHelper.Raw` methode echter wordt aangeroepen, wordt markering geretourneerd die niet HTML-gecodeerd is. Als `Html.Raw()` helper methode wordt gebruikt, het omzeilt de automatische codering bescherming die Razor biedt.|

### <a name="example"></a>Voorbeeld
Hieronder volgt een onveilig voorbeeld: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Niet gebruiken, `Html.Raw()` tenzij u markeringen moet weergeven. Deze methode voert geen uitvoercodering impliciet uit. Gebruik andere ASP.NET helpers, bijvoorbeeld`@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Dynamische query's niet gebruiken in opgeslagen procedures

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Een SQL-injectieaanval maakt misbruik van kwetsbaarheden in invoervalidatie om willekeurige opdrachten in de database uit te voeren. Dit kan optreden wanneer uw toepassing invoer gebruikt om dynamische SQL-instructies te construeren om toegang te krijgen tot de database. Het kan ook optreden als uw code opgeslagen procedures gebruikt die worden doorgegeven tekenreeksen die ruwe gebruikersinvoer bevatten. Met behulp van de SQL-injectieaanval kan de aanvaller willekeurige opdrachten uitvoeren in de database. Alle SQL-instructies (inclusief de SQL-instructies in opgeslagen procedures) moeten worden geparameteriseerd. Geparameteriseerde SQL-instructies accepteren tekens die een speciale betekenis hebben voor SQL (zoals één citaat) zonder problemen, omdat ze sterk zijn getypt. |

### <a name="example"></a>Voorbeeld
Hieronder volgt een voorbeeld van onveilige dynamische opgeslagen procedure: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Voorbeeld
Volgende is dezelfde opgeslagen procedure veilig geïmplementeerd: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Ervoor zorgen dat modelvalidatie wordt uitgevoerd op Web API-methoden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Modelvalidatie in ASP.NET Web-API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Stappen** | Wanneer een client gegevens naar een web-API verzendt, is het verplicht om de gegevens te valideren voordat een verwerking wordt gedaan. Voor ASP.NET web-API's die modellen als invoer accepteren, gebruikt u gegevensaantekeningen op modellen om validatieregels in te stellen voor de eigenschappen van het model.|

### <a name="example"></a>Voorbeeld
De volgende code toont hetzelfde aan: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Voorbeeld
In de actiemethode van de API-controllers moet de geldigheid van het model expliciet worden gecontroleerd zoals hieronder wordt weergegeven: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Invoervalidatie implementeren op alle tekenreekstypeparameters die worden geaccepteerd door web-API-methoden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, MVC 5, MVC 6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Modelgegevens valideren in een MVC-toepassing,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) [leidende principes voor uw ASP.NET MVC-toepassingen](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Stappen** | Voor methoden die alleen primitief gegevenstype accepteren, en niet modellen als argument, moet invoervalidatie met behulp van Reguliere expressie worden uitgevoerd. Hier regex.IsMatch moet worden gebruikt met een geldig regex patroon. Als de invoer niet overeenkomt met de opgegeven reguliere expressie, moet het besturingselement niet verder gaan en moet er een adequate waarschuwing met betrekking tot validatiefouten worden weergegeven.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Ervoor zorgen dat typeveilige parameters worden gebruikt in Web API voor gegevenstoegang

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Als u de verzameling Parameters gebruikt, behandelt SQL de invoer eerder als een letterlijke waarde dan als uitvoerbare code. De verzameling Parameters kan worden gebruikt om type- en lengtebeperkingen voor invoergegevens af te dwingen. Waarden buiten het bereik leiden tot een uitzondering. Als niet voor typeveilige SQL-parameters wordt gebruikt, kunnen aanvallers mogelijk injectieaanvallen uitvoeren die zijn ingesloten in de ongefilterde invoer.</p><p>Gebruik typeveilige parameters bij het maken van SQL-query's om mogelijke SQL-injectieaanvallen te voorkomen die kunnen optreden met ongefilterde invoer. U typeveilige parameters gebruiken met opgeslagen procedures en met dynamische SQL-instructies. Parameters worden door de database als letterlijke waarden behandeld en niet als uitvoerbare code. Parameters worden ook gecontroleerd op type en lengte.</p>|

### <a name="example"></a>Voorbeeld
In de volgende code ziet u hoe u veilige parameters voor tekst gebruiken met de SqlParameterCollection bij het aanroepen van een opgeslagen procedure. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
In het voorgaande codevoorbeeld mag de invoerwaarde niet langer zijn dan 11 tekens. Als de gegevens niet voldoen aan het type of de lengte die door de parameter wordt gedefinieerd, wordt met de klasse SqlParameter een uitzondering gemaakt. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Parameterized SQL-query's gebruiken voor Cosmos DB

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Document DB | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [SQL-parameterisatie aankondigen in Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Stappen** | Hoewel Azure Cosmos DB alleen-lezen query's ondersteunt, is SQL-injectie nog steeds mogelijk als query's worden opgebouwd door samen te gaan met gebruikersinvoer. Het is mogelijk voor een gebruiker om toegang te krijgen tot gegevens die ze niet zouden moeten openen binnen dezelfde verzameling door het maken van kwaadaardige SQL-query's. Gebruik geparameteriseerde SQL-query's als query's zijn opgebouwd op basis van gebruikersinvoer. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>WCF-invoervalidatie via schemabinding

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Stappen** | <p>Gebrek aan validatie leidt tot verschillende type injectie aanvallen.</p><p>Berichtvalidatie vertegenwoordigt één verdedigingslinie in de bescherming van uw WCF-toepassing. Met deze aanpak valideert u berichten met behulp van schema's om WCF-servicebewerkingen te beschermen tegen aanvallen door een kwaadwillende client. Valideer alle berichten die door de client zijn ontvangen om de client te beschermen tegen aanvallen door een kwaadwillende service. Berichtvalidatie maakt het mogelijk om berichten te valideren wanneer bewerkingen berichtcontracten of gegevenscontracten verbruiken, wat niet kan worden gedaan met parametervalidatie. Met berichtvalidatie u validatielogica maken in schema's, waardoor u meer flexibiliteit biedt en de ontwikkelingstijd vermindert. Schema's kunnen worden hergebruikt voor verschillende toepassingen binnen de organisatie, waardoor standaarden worden gemaakt voor gegevensweergave. Bovendien u met berichtvalidatie bewerkingen beveiligen wanneer ze complexere gegevenstypen verbruiken met contracten die bedrijfslogica vertegenwoordigen.</p><p>Als u berichtvalidatie wilt uitvoeren, bouwt u eerst een schema dat de bewerkingen van uw service en de gegevenstypen die door deze bewerkingen worden verbruikt, weergeeft. Vervolgens maakt u een .NET-klasse die een aangepaste berichtcontrole voor clientberichten en aangepaste verzenderberichtcontrole implementeert om de verzonden/ontvangen berichten naar/van de service te valideren. Vervolgens implementeert u een aangepast eindpuntgedrag om berichtvalidatie in te schakelen op zowel de client als de service. Ten slotte implementeert u een aangepast configuratie-element op de klasse waarmee u het uitgebreide aangepaste eindpuntgedrag in het configuratiebestand van de service of de client blootstellen</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF- Invoervalidatie via parameterinspecteurs

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Stappen** | <p>Invoer en gegevensvalidatie vormen een belangrijke verdedigingslinie in de bescherming van uw WCF-toepassing. U moet alle parameters valideren die in WCF-servicebewerkingen worden blootgesteld om de service te beschermen tegen aanvallen door een kwaadwillende client. Omgekeerd moet u ook alle retourwaarden valideren die door de client worden ontvangen om de client te beschermen tegen aanvallen door een schadelijke service</p><p>WCF biedt verschillende uitbreidbaarheidspunten waarmee u het wcf-runtime-gedrag aanpassen door aangepaste extensies te maken. Berichtinspecteurs en parameterinspecteurs zijn twee uitbreidbaarheidsmechanismen die worden gebruikt om meer controle te krijgen over de gegevens die tussen een client en een service worden doorgegeven. U moet parametercontroleurs alleen gebruiken voor invoervalidatie en berichtinspecteurs gebruiken wanneer u het hele bericht moet inspecteren dat in en uit een service stroomt.</p><p>Als u invoervalidatie wilt uitvoeren, bouwt u een .NET-klasse en implementeert u een aangepaste parametercontrole om parameters voor bewerkingen in uw service te valideren. Vervolgens implementeert u een aangepast eindpuntgedrag om validatie in te schakelen op zowel de client als de service. Ten slotte implementeert u een aangepast configuratie-element op de klasse waarmee u het uitgebreide aangepaste eindpuntgedrag in het configuratiebestand van de service of de client blootstellen</p>|
