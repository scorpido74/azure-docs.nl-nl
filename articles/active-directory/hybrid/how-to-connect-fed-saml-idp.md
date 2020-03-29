---
title: 'Azure AD Connect: gebruik een SAML 2.0-identiteitsprovider voor eenmalig aanmelden - Azure'
description: In dit document wordt beschreven dat u een Idp die voldoet aan DE SAML 2.0 voor eenmalig aanmeldingen.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff4f1b81f476159c13d3725cd3cb1766aec7dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70305099"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Een SAML 2.0-id-provider (IdP) gebruiken voor eenmalige aanmelding

Dit document bevat informatie over het gebruik van een SAML 2.0-compatibele SP-Lite-profielgebaseerde identiteitsprovider als de voorkeursbeveiligingstokenservice (STS) / identiteitsprovider. Dit scenario is handig wanneer u al een gebruikersmap en wachtwoordarchief hebt die toegankelijk zijn met SAML 2.0. Deze bestaande gebruikersmap kan worden gebruikt voor aanmelding bij Office 365 en andere door Azure AD beveiligde bronnen. De SAML 2.0 SP-Lite profiel is gebaseerd op de veelgebruikte Security Assertion Markup Language (SAML) federated identity standaard om een sign-on en attribuut uitwisseling kader te bieden.

>[!NOTE]
>Zie de [compatibiliteitslijst van Azure AD-federaties](how-to-connect-fed-compatibility.md) voor een lijst met idps van derden van derden die zijn getest voor gebruik met Azure AD

Microsoft ondersteunt deze aanmeldingservaring als de integratie van een Microsoft-cloudservice, zoals Office 365, met uw goed geconfigureerde SAML 2.0-profielgebaseerde IdP. SAML 2.0-identiteitsproviders zijn producten van derden en daarom biedt Microsoft geen ondersteuning voor de implementatie, configuratie en het oplossen van problemen met de beste praktijken. Eenmaal goed geconfigureerd, kan de integratie met de SAML 2.0-identiteitsprovider worden getest op de juiste configuratie met behulp van de Microsoft Connectivity Analyzer Tool, die hieronder nader wordt beschreven. Voor meer informatie over uw SAML 2.0 SP-Lite profielgebaseerde identiteitsprovider, vraag de organisatie die het heeft geleverd.

> [!IMPORTANT]
> Slechts een beperkte set clients zijn beschikbaar in dit aanmeldingsscenario bij SAML 2.0-identiteitsproviders, dit omvat:
> 
> - Webgebaseerde clients zoals Outlook Web Access en SharePoint Online
> - E-mailrijke clients die basisverificatie en een ondersteunde Exchange-toegangsmethode gebruiken, zoals IMAP, POP, Active Sync, MAPI, enz.
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (verschillende iOS-versies)
>     - Verschillende Google Android-apparaten
>     - Windows Phone 7, Windows Phone 7.8 en Windows Phone 8.0
>     - Windows 8 Mail-client en Windows 8.1 Mail-client
>     - Windows 10 Mail-client

Alle andere clients zijn niet beschikbaar in dit aanmeldingsscenario bij uw SAML 2.0 Identity Provider. De Lync 2010-bureaubladclient kan zich bijvoorbeeld niet aanmelden bij de service met uw SAML 2.0-identiteitsprovider die is geconfigureerd voor eenmalige aanmelding.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Azure AD SAML 2.0-protocolvereisten
Dit document bevat gedetailleerde vereisten voor het protocol en de opmaak van berichten die uw SAML 2.0-identiteitsprovider moet implementeren om met Azure AD te kunnen worden aangemeld om aanmelding bij een of meer Microsoft-cloudservices (zoals Office 365) mogelijk te maken. De SAML 2.0 relying party (SP-STS) voor een Microsoft-cloudservice die in dit scenario wordt gebruikt, is Azure AD.

Het wordt aanbevolen dat u ervoor zorgt dat uw SAML 2.0-identiteitsprovider uitvoerberichten zo vergelijkbaar zijn met de meegeleverde voorbeeldsporen. Gebruik waar mogelijk ook specifieke kenmerkwaarden uit de meegeleverde Azure AD-metagegevens. Zodra u tevreden bent met uw uitvoerberichten, u testen met de Microsoft Connectivity Analyzer zoals hieronder beschreven.

De Azure AD-metagegevens kunnen [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml)worden gedownload van deze URL: .
Voor klanten in China die het China-specifieke exemplaar van Office 365 [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml)gebruiken, moet het volgende federatieeindpunt worden gebruikt: .

## <a name="saml-protocol-requirements"></a>SAML-protocolvereisten
In deze sectie wordt beschreven hoe de paren van het verzoek en het antwoordbericht worden samengesteld om u te helpen uw berichten correct op te maken.

Azure AD kan worden geconfigureerd om te werken met identiteitsproviders die het SAML 2.0 SP Lite-profiel gebruiken met een aantal specifieke vereisten zoals hieronder vermeld. Met behulp van de voorbeeldSAML-aanvraag- en antwoordberichten en geautomatiseerde en handmatige tests u werken aan interoperabiliteit met Azure AD.

## <a name="signature-block-requirements"></a>Vereisten voor handtekeningblokken
In het SAML-antwoordbericht bevat het knooppunt Handtekening informatie over de digitale handtekening voor het bericht zelf. Het handtekeningblok heeft de volgende vereisten:

1. Het beweringsknooppunt zelf moet worden ondertekend
2.  Het RSA-sha1-algoritme moet worden gebruikt als digestmethod. Andere algoritmes voor digitale handtekeningen worden niet geaccepteerd.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  U ook het XML-document ondertekenen. 
4.  Het transformatiealgoritme moet overeenkomen met de waarden in het volgende voorbeeld:`<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Het algoritme SignatureMethod moet overeenkomen met het volgende voorbeeld:`<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Ondersteunde bindingen
Bindingen zijn de transportgerelateerde communicatieparameters die nodig zijn. De volgende eisen gelden voor de bindingen

1. HTTPS is het vereiste transport.
2.  Azure AD vereist HTTP POST voor het indienen van token tijdens het aanmelden.
3.  Azure AD gebruikt HTTP POST voor het verificatieverzoek aan de identiteitsprovider en REDIRECT voor het afmeldbericht aan de identiteitsprovider.

## <a name="required-attributes"></a>Vereiste kenmerken
In deze tabel worden vereisten weergegeven voor specifieke kenmerken in het SAML 2.0-bericht.
 
|Kenmerk|Beschrijving|
| ----- | ----- |
|Naam-ID|De waarde van deze bewering moet gelijk zijn aan de Onveranderlijke ID van de Azure AD-gebruiker. Het kan maximaal 64 alfa numerieke tekens zijn. Alle niet-html-veilige tekens moeten worden gecodeerd, bijvoorbeeld een "+" teken wordt weergegeven als ".2B".|
|IDPE-e-mail|De UPN (User Principal Name) wordt in het SAML-antwoord weergegeven als een element met de naam IDPEmail De userPrincipalName (UPN) van de gebruiker in Azure AD/Office 365. De UPN is in e-mailadres formaat. UPN-waarde in Windows Office 365 (Azure Active Directory).|
|Verlener|Vereist om een URI van de identiteitsprovider te zijn. Gebruik de uitgever niet opnieuw uit de voorbeeldberichten. Als u meerdere topdomeinen in uw Azure AD-tenants hebt, moet de uitgever overeenkomen met de opgegeven URI-instelling die per domein is geconfigureerd.|

>[!IMPORTANT]
>Azure AD ondersteunt momenteel de volgende NameID-indeling URI voor SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-format:persistent.

## <a name="sample-saml-request-and-response-messages"></a>Voorbeeld van SAML-aanvraag- en antwoordberichten
Er wordt een aanvraag- en antwoordberichtpaar weergegeven voor de uitwisseling van aanmeldingen.
Het volgende is een voorbeeldaanvraagbericht dat vanuit Azure AD naar een voorbeeldsaml 2.0-identiteitsprovider wordt verzonden. De voorbeeldsaml 2.0-identiteitsprovider is Ad FS (Active Directory Federation Services) dat is geconfigureerd voor het gebruik van het SAML-P-protocol. Interoperabiliteitstests zijn ook voltooid met andere SAML 2.0-identiteitsproviders.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Het volgende is een voorbeeldreactiebericht dat wordt verzonden van de voorbeeldsaml 2.0-compatibele identiteitsprovider naar Azure AD / Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Uw SAML 2.0-compatibele identiteitsprovider configureren
Deze sectie bevat richtlijnen voor het configureren van uw SAML 2.0-identiteitsprovider om te reageren op Azure AD om interne aanmeldingstoegang tot een of meer Microsoft-cloudservices (zoals Office 365) in te schakelen met behulp van het SAML 2.0-protocol. De SAML 2.0-relying-partij voor een Microsoft-cloudservice die in dit scenario wordt gebruikt, is Azure AD.

## <a name="add-azure-ad-metadata"></a>Azure AD-metagegevens toevoegen
Uw SAML 2.0-identiteitsprovider moet zich houden aan informatie over de Azure AD-relying-partij. Azure AD publiceert https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlmetagegevens op .

Het wordt aanbevolen dat u altijd de nieuwste Azure AD-metagegevens importeert bij het configureren van uw SAML 2.0-identiteitsprovider.

>[!NOTE]
>Azure AD leest geen metagegevens van de identiteitsprovider.

## <a name="add-azure-ad-as-a-relying-party"></a>Azure AD toevoegen als relying party
U moet de communicatie tussen uw SAML 2.0-identiteitsprovider en Azure AD inschakelen. Deze configuratie is afhankelijk van uw specifieke identiteitsprovider en u moet verwijzen naar documentatie hiervoor. U stelt de relying party ID meestal in op dezelfde als de entiteits-id van de Azure AD-metagegevens.

>[!NOTE]
>Controleer of de klok op uw SAML 2.0-identiteitsproviderserver is gesynchroniseerd met een nauwkeurige tijdbron. Een onnauwkeurige kloktijd kan ervoor zorgen dat federatieve aanmeldingen mislukken.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Windows PowerShell installeren voor aanmelding bij SAML 2.0-identiteitsprovider
Nadat u uw SAML 2.0-identiteitsprovider hebt geconfigureerd voor gebruik met Azure AD-aanmelding, is de volgende stap het downloaden en installeren van de Azure Active Directory Module voor Windows PowerShell. Eenmaal geïnstalleerd, gebruikt u deze cmdlets om uw Azure AD-domeinen te configureren als federatieve domeinen.

De Azure Active Directory Module voor Windows PowerShell is een download voor het beheren van uw organisatiegegevens in Azure AD. Deze module installeert een set cmdlets naar Windows PowerShell; u voert deze cmdlets uit om één aanmeldingstoegang tot Azure AD in te stellen en op zijn beurt tot alle cloudservices waarop u bent geabonneerd. Zie voor instructies over het downloaden en installeren van de cmdlets[https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Een vertrouwensrelatie instellen tussen uw SAML-identiteitsprovider en Azure AD
Voordat u de federatie op een Azure AD-domein configureert, moet een aangepast domein zijn geconfigureerd. U het standaarddomein dat door Microsoft wordt geleverd, niet beoordelen. Het standaarddomein van Microsoft eindigt met 'onmicrosoft.com'.
U voert een reeks cmdlets uit in de Windows PowerShell-opdrachtregelinterface om domeinen toe te voegen of om te zetten voor één aanmelding.

Elk Azure Active Directory-domein dat u wilt gebruiken met uw SAML 2.0-identiteitsprovider, moet worden toegevoegd als één aanmeldingsdomein of geconverteerd om één aanmeldingsdomein uit een standaarddomein te zijn. Als u een domein toevoegt of converteert, wordt een vertrouwensrelatie ingesteld tussen uw SAML 2.0-identiteitsprovider en Azure AD.

De volgende procedure leidt u door het omzetten van een bestaand standaarddomein naar een federatief domein met SAML 2.0 SP-Lite. 

>[!NOTE]
>Uw domein kan een storing ondervinden die gebruikers tot 2 uur na het nemen van deze stap beïnvloedt.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Een domein configureren in uw Azure AD Directory voor federatie


1. Maak verbinding met uw Azure AD Directory als tenantbeheerder: Connect-MsolService .
2.  Configureer het gewenste Office 365-domein om federatie met SAML 2.0 te gebruiken:`$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $BrandName -Authentication Federated -PassiveLogOnUri $LogOnUrl -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $MyURI -LogOffUri $LogOffUrl -PreferredAuthenticationProtocol $Protocol` 

3.  U de handtekening van het ondertekeningscertificaat base64 verkrijgen in uw IDP-metagegevensbestand. Een voorbeeld van deze locatie is verstrekt, maar kan enigszins verschillen op basis van uw implementatie.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Zie voor meer informatie over "Set-MsolDomainAuthentication" raadpleegt u: [https://technet.microsoft.com/library/dn194112.aspx](https://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>U mag `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` het gebruik alleen uitvoeren als u een ECP-extensie instelt voor uw identiteitsprovider. Exchange Online-clients, met uitzondering van Outlook Web Application (OWA), vertrouwen op een actief eindpunt op basis van POST. Als uw SAML 2.0 STS een actief eindpunt implementeert dat vergelijkbaar is met de ECP-implementatie van een actief eindpunt van Shibboleth, kunnen deze rijke clients mogelijk communiceren met de Exchange Online-service.

Zodra federatie is geconfigureerd, u teruggaan naar 'niet-gefedereerd' (of 'beheerd'), maar deze wijziging duurt maximaal twee uur en het vereist het toewijzen van nieuwe willekeurige wachtwoorden voor cloud-gebaseerde aanmelding aan elke gebruiker. Terugschakelen naar 'beheerd' kan in sommige scenario's nodig zijn om een fout in uw instellingen opnieuw in te stellen. Zie voor meer informatie [https://msdn.microsoft.com/library/windowsazure/dn194122.aspx](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx)over domeinconversie: .

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Gebruikersprincipals inrichten voor Azure AD / Office 365
Voordat u uw gebruikers verifiëren bij Office 365, moet u Azure AD inrichten met gebruikersprincipals die overeenkomen met de bewering in de SAML 2.0-claim. Als deze gebruikersprincipals niet van tevoren bekend zijn bij Azure AD, kunnen ze niet worden gebruikt voor federatieve aanmelding. Azure AD Connect of Windows PowerShell kunnen worden gebruikt voor het inrichten van gebruikersprincipals.

Azure AD Connect kan worden gebruikt om principals voor uw domeinen in uw Azure AD Directory in te richten vanuit de on-premises Active Directory. Zie [Uw on-premises mappen integreren met Azure Active Directory](whatis-hybrid-identity.md)voor meer gedetailleerde informatie.

Windows PowerShell kan ook worden gebruikt om het toevoegen van nieuwe gebruikers aan Azure AD te automatiseren en om wijzigingen te synchroniseren vanuit de on-premises directory. Als u de Windows PowerShell-cmdlets wilt gebruiken, moet u de [Azure Active Directory-modules](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0)downloaden.

Deze procedure laat zien hoe u één gebruiker toevoegt aan Azure AD.


1. Maak verbinding met uw Azure AD Directory als tenantbeheerder: Connect-MsolService.
2.  Een nieuwe gebruikersprincipal maken:
    ```powershell
    New-MsolUser
      -UserPrincipalName elwoodf1@contoso.com
      -ImmutableId ABCDEFG1234567890
      -DisplayName "Elwood Folk"
      -FirstName Elwood 
      -LastName Folk 
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
      -UsageLocation "US" 
    ```

Voor meer informatie over "New-MsolUser" kassa,[https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>De waarde "UserPrinciplName" moet overeenkomen met de waarde die u voor "IDPEmail" in uw SAML 2.0-claim verzendt en de waarde 'Onveranderlijke ID' moet overeenkomen met de waarde die wordt verzonden in uw bewering 'NameID'.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Controleer eenmalige aanmelding met uw SAML 2.0 IDP
Bekijk als beheerder de informatie voordat u eenmalige aanmelding (ook wel identiteitsfederatie genoemd) controleert en beheert, en voert u de stappen uit in de volgende artikelen om eenmalige aanmelding in te stellen bij uw SAML 2.0 SP-Lite-gebaseerde identiteitsprovider:


1.  U hebt de Azure AD SAML 2.0-protocolvereisten beoordeeld
2.  U hebt uw SAML 2.0-identiteitsprovider geconfigureerd
3.  Windows PowerShell installeren voor eenmalige aanmelding bij SAML 2.0-identiteitsprovider
4.  Een vertrouwensrelatie instellen tussen SAML 2.0-identiteitsprovider en Azure AD
5.  Ingericht een bekende test gebruiker opdrachtgever voor Azure Active Directory (Office 365) hetzij via Windows PowerShell of Azure AD Connect.
6.  Adreslijstsynchronisatie configureren met [Azure AD Connect](whatis-hybrid-identity.md).

Nadat u één aanmelding hebt ingesteld bij uw SAML 2.0 SP-Lite-gebaseerde identiteitsprovider, moet u controleren of deze correct werkt.

>[!NOTE]
>Als u een domein hebt geconverteerd in plaats van er een toe te voegen, kan het tot 24 uur duren voordat u een enkele aanmelding hebt ingesteld.
Voordat u eenmalige aanmelding verifieert, moet u de synchronisatie van Active Directory voltooien, uw mappen synchroniseren en uw gesynchroniseerde gebruikers activeren.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Gebruik het gereedschap om te controleren of eenmalige aanmelding correct is ingesteld
Als u wilt controleren of eenmalige aanmelding correct is ingesteld, u de volgende procedure uitvoeren om te bevestigen dat u zich aanmelden bij de cloudservice met uw bedrijfsreferenties.

Microsoft heeft een tool geleverd die u gebruiken om uw SAML 2.0-gebaseerde identiteitsprovider te testen. Voordat u het testprogramma uitvoert, moet u een Azure AD-tenant hebben geconfigureerd om te reageren op uw identiteitsprovider.

>[!NOTE]
>De Connectivity Analyzer vereist Internet Explorer 10 of hoger.



1. Download de Connectivity Analyzer van. [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client)
2.  Klik op Nu installeren om te beginnen met het downloaden en installeren van het gereedschap.
3.  Selecteer 'Ik kan geen federatie instellen met Office 365, Azure of andere services die Azure Active Directory gebruiken'.
4.  Zodra de tool is gedownload en uitgevoerd, ziet u het venster Connectiviteitsdiagnose. De tool zal u door het testen van uw federatie verbinding.
5.  De Connectivity Analyzer opent uw SAML 2.0 IDP voor u om u aan te ![melden, voert de referenties in voor de gebruikersprincipal die u test: SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  In het aanmeldingsvenster voor federatietests moet u een accountnaam en wachtwoord invoeren voor de Azure AD-tenant die is geconfigureerd om te worden gefedereerd met uw SAML 2.0-identiteitsprovider. De tool probeert u aan te melden met behulp van deze referenties en gedetailleerde resultaten van tests die tijdens de aanmeldingspoging zijn uitgevoerd, worden als uitvoer verstrekt.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Dit venster toont een mislukt resultaat van het testen. Als u op Gedetailleerde resultaten bekijken klikt, worden informatie weergegeven over de resultaten voor elke test die is uitgevoerd. U de resultaten ook opslaan op schijf om ze te delen.
 
>[!NOTE]
>De Connectivity analyzer test ook Active Federation met behulp van de WS*-gebaseerde en ECP/PAOS protocollen. Als u deze niet gebruikt, u de volgende fout negeren: De actieve aanmeldingsstroom testen met het active federation-eindpunt van uw identiteitsprovider.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Controleer handmatig of eenmalige aanmelding correct is ingesteld
Handmatige verificatie biedt extra stappen die u nemen om ervoor te zorgen dat uw SAML 2.0-identiteitsprovider in veel scenario's naar behoren werkt.
Voer de volgende stappen uit om te controleren of eenmalige aanmelding correct is ingesteld:


1. Meld u op een computer die is verbonden aan een domein bij uw cloudservice met dezelfde aanmeldingsnaam die u voor uw bedrijfsreferenties gebruikt.
2.  Klik in het wachtwoordvak. Als eenmalige aanmelding is ingesteld, wordt het wachtwoordvak in de schaduw gesteld en ziet u het &lt;volgende&gt;bericht: "U bent nu verplicht om u aan te melden bij uw bedrijf."
3.  Klik op de &lt;aanmelding&gt; op uw bedrijfslink. Als u zich aanmelden, is er één aanmelding ingesteld.

## <a name="next-steps"></a>Volgende stappen


- [Beheer en aanpassing van Active Directory Federation Services met Azure AD Connect](how-to-connect-fed-management.md)
- [Compatibiliteitslijst voor Azure AD-federatie](how-to-connect-fed-compatibility.md)
- [Aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md)
