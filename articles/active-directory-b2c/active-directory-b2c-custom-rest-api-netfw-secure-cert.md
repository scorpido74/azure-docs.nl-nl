---
title: Een REST-service beveiligen met behulp van client certificaten
titleSuffix: Azure AD B2C
description: Uw aangepaste REST API claim uitwisselingen in uw Azure AD B2C beveiligen door gebruik te maken van client certificaten
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1d396de57a12285fb6cc682510fa6f95585465d0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949854"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Uw REST-service beveiligen met behulp van client certificaten

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In een verwant artikel maakt u [een rest-service](active-directory-b2c-custom-rest-api-netfw.md) die samenwerkt met Azure Active Directory B2C (Azure AD B2C).

In dit artikel leert u hoe u de toegang tot uw Azure-web-app (REST API) kunt beperken met behulp van een client certificaat. Dit mechanisme heet TLS wederzijdse verificatie of verificatie van *client certificaten*. Alleen services die over de juiste certificaten beschikken, zoals Azure AD B2C, hebben toegang tot uw service.

>[!NOTE]
>U kunt ook uw REST-service beveiligen met behulp van [http-basis verificatie](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). HTTP Basic-verificatie wordt echter minder veilig beschouwd via een client certificaat. Onze aanbeveling is de REST-service te beveiligen met behulp van verificatie via client certificaten, zoals beschreven in dit artikel.

In dit artikel vindt u informatie over:
* Stel uw web-app in voor gebruik van verificatie van client certificaten.
* Upload het certificaat naar Azure AD B2C-beleids sleutels.
* Configureer uw aangepaste beleid voor het gebruik van het client certificaat.

## <a name="prerequisites"></a>Vereisten
* Voer de stappen in het artikel [integratie rest API claims uitwissels](active-directory-b2c-custom-rest-api-netfw.md) uit.
* Een geldig certificaat (een. pfx-bestand met een persoonlijke sleutel) ophalen.

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Stap 1: een web-app configureren voor verificatie van client certificaten
Als u **Azure app service** wilt instellen om client certificaten te vereisen, stelt u de web-app `clientCertEnabled` site-instelling in op *waar*. Als u deze wijziging wilt aanbrengen, opent u de pagina Web-app in het Azure Portal. Klik in de linkernavigatiebalk onder **instellingen** SSL- **instellingen**selecteren. Schakel in de sectie **client certificaten** de optie **Inkomend client certificaat** in.

>[!NOTE]
>Zorg ervoor dat uw Azure App Service plan Standard of hoger is. Zie [Azure app service indieping-overzicht van plannen](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)voor meer informatie.

>[!NOTE]
>Zie voor meer informatie over het instellen van de eigenschap **CLIENTCERTENABLED** [TLS wederzijdse verificatie configureren voor web-apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Stap 2: Upload uw certificaat naar Azure AD B2C-beleids sleutels
Nadat u `clientCertEnabled` op *waar*hebt ingesteld, is voor de communicatie met de rest-API een client certificaat vereist. Ga als volgt te werk om het client certificaat te verkrijgen, te uploaden en op te slaan in uw Azure AD B2C Tenant:
1. Selecteer in uw Azure AD B2C-Tenant **B2C instellingen** > **Framework voor identiteits ervaring**.

2. Selecteer **beleids sleutels**om de sleutels weer te geven die beschikbaar zijn in uw Tenant.

3. Selecteer **Toevoegen**.
    Het venster **een sleutel maken** wordt geopend.

4. Selecteer **uploaden**in het vak **Opties** .

5. Typ **B2cRestClientCertificate**in het vak **naam** .
    Het voor voegsel *B2C_1A_* automatisch toegevoegd.

6. Selecteer in het vak **bestand uploaden** het pfx-bestand van uw certificaat met een persoonlijke sleutel.

7. Typ in het vak **wacht** woord het wacht woord van het certificaat.

    ![Beleids sleutel uploaden op de pagina een sleutel maken in Azure Portal](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Selecteer **Maken**.

8. Selecteer **beleids sleutels**om de sleutels weer te geven die beschikbaar zijn in uw Tenant en te bevestigen dat u de `B2C_1A_B2cRestClientCertificate` sleutel hebt gemaakt.

## <a name="step-3-change-the-technical-profile"></a>Stap 3: het technische profiel wijzigen
Als u verificatie van client certificaten wilt ondersteunen in uw aangepaste beleid, wijzigt u het technische profiel door het volgende te doen:

1. Open in uw werkmap het extensie beleids bestand *TrustFrameworkExtensions. XML* .

2. Zoek naar het `<TechnicalProfile>` knoop punt dat `Id="REST-API-SignUp"`bevat.

3. Zoek het element `<Metadata>`.

4. Wijzig de *AuthenticationType* in *ClientCertificate*als volgt:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Voeg direct na het afsluitende `<Metadata>` element het volgende XML-fragment toe:

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Nadat u het fragment hebt toegevoegd, moet uw technische profiel eruitzien zoals in de volgende XML-code:

    ![XML-elementen voor ClientCertificate-verificatie instellen](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Stap 4: het beleid uploaden naar uw Tenant

1. In de [Azure Portal](https://portal.azure.com)gaat u naar de [context van uw Azure AD B2C-Tenant](active-directory-b2c-navigate-to-b2c-context.md)en selecteert u **Azure AD B2C**.

2. Selecteer een **Framework voor identiteits ervaring**.

3. Selecteer **alle beleids regels**.

4. Selecteer **beleid uploaden**.

5. Selecteer het selectie vakje het **beleid overschrijven als dit bestaat** .

6. Upload het bestand *TrustFrameworkExtensions. XML* en controleer of het validatie wordt door gegeven.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Stap 5: het aangepaste beleid testen met behulp van nu uitvoeren
1. Open **Azure AD B2C-instellingen**en selecteer vervolgens **Framework voor identiteits ervaring**.

    >[!NOTE]
    >Voor het uitvoeren van nu moet ten minste één toepassing vooraf worden geregistreerd op de Tenant. Zie het artikel Azure AD B2C [aan de slag](active-directory-b2c-get-started.md) of het artikel over het registreren van [toepassingen](active-directory-b2c-app-registration.md) voor meer informatie over het registreren van toepassingen.

2. Open **B2C_1A_signup_signin**, het aangepaste beleid RELYING Party (RP) dat u hebt geüpload en selecteer **nu uitvoeren**.

3. Test het proces door **test** te typen in het vak **naam** .
    In Azure AD B2C wordt boven in het venster een fout bericht weer gegeven.

    ![De opgegeven naam is gemarkeerd en de invoer validatie fout wordt weer gegeven](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. In het vak **naam** typt u een naam (anders dan ' test ').
    Azure AD B2C de gebruiker aan te melden en vervolgens een loyaliteits nummer naar uw toepassing te verzenden. Noteer het nummer in dit JWT-voor beeld:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >Als u het fout bericht ontvangt, *is de naam niet geldig. Geef een geldige naam*op. Dit betekent dat Azure AD B2C de rest-service is aangeroepen terwijl het client certificaat wordt aangeboden. De volgende stap is het valideren van het certificaat.

## <a name="step-6-add-certificate-validation"></a>Stap 6: certificaat validatie toevoegen
Het client certificaat dat Azure AD B2C verzonden naar uw REST-service, wordt niet gevalideerd door het Azure App Service-platform, behalve om te controleren of het certificaat bestaat. Het valideren van het certificaat is de verantwoordelijkheid van de web-app.

In deze sectie voegt u voorbeeld code ASP.NET toe die de certificaat eigenschappen valideert voor verificatie doeleinden.

> [!NOTE]
>Zie voor meer informatie over het configureren van Azure App Service voor client certificaat verificatie [wederzijdse TLS-verificatie configureren voor web-apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6,1 toepassings instellingen toevoegen aan het web. config-bestand van het project
In het Visual Studio-project dat u eerder hebt gemaakt, voegt u de volgende toepassings instellingen toe aan het bestand *Web. config* na het `appSettings`-element:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Vervang de **onderwerpnaam**van het certificaat, de naam van de **verlener**en de **vinger afdruk** van het certificaat door de waarden van uw certificaat.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6,2 de functie IsValidClientCertificate toevoegen
Open het *Controllers\IdentityController.cs* -bestand en voeg de volgende functie toe aan de klasse `Identity` controller:

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }

    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

In de voor gaande voorbeeld code accepteren we het certificaat alleen geldig als aan de volgende voor waarden wordt voldaan:
* Het certificaat is niet verlopen en is actief voor de huidige tijd op de server.
* De `Subject` naam van het certificaat is gelijk aan de waarde voor de instelling van de `ClientCertificate:Subject` toepassing.
* De `Issuer` naam van het certificaat is gelijk aan de waarde voor de instelling van de `ClientCertificate:Issuer` toepassing.
* De `thumbprint` van het certificaat is gelijk aan de waarde voor de instelling van de `ClientCertificate:Thumbprint` toepassing.

>[!IMPORTANT]
>Afhankelijk van de gevoeligheid van uw service moet u mogelijk meer validaties toevoegen. U moet bijvoorbeeld testen of het certificaat is gekoppeld aan een vertrouwde basis instantie, validatie van de organisatie naam van de verlener, enzovoort.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6,3 de functie IsValidClientCertificate aanroepen
Open het *Controllers\IdentityController.cs* -bestand en voeg aan het begin van de functie `SignUp()` het volgende code fragment toe:

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Nadat u het fragment hebt toegevoegd, moet uw `Identity`-controller eruitzien als de volgende code:

![Certificaat validatie code toevoegen](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Stap 7: uw project naar Azure publiceren en dit testen
1. Klik in **Solution Explorer**met de rechter muisknop op het project **contoso. AADB2C. API** en selecteer vervolgens **publiceren**.

2. Herhaal ' stap 6 ' en test uw aangepaste beleid opnieuw met de certificaat validatie. Probeer het beleid uit te voeren en zorg ervoor dat alles werkt nadat u de validatie hebt toegevoegd.

3. Wijzig de waarde van `ClientCertificate:Subject` in het bestand *Web. config* in **ongeldig**. Voer het beleid opnieuw uit en er wordt een fout bericht weer gegeven.

4. Wijzig de waarde in **geldig**en controleer of het beleid uw rest API kan aanroepen.

Als u deze stap moet oplossen, raadpleegt u [Logboeken verzamelen met behulp van Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>Beschrijving De volledige beleids bestanden en code downloaden
* Nadat u de walkthrough aan de [slag met aangepast beleid](active-directory-b2c-get-started-custom.md) hebt voltooid, wordt u aangeraden om uw scenario te bouwen met behulp van uw eigen aangepaste beleids bestanden. Voor uw referentie hebben we [voorbeeld beleids bestanden](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)gegeven.
* U kunt de volledige code downloaden van een [voor beeld van een Visual Studio-oplossing voor referentie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).
