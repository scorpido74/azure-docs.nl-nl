---
title: Zelf studie voor het configureren van Azure Active Directory B2C met Jumio
titleSuffix: Azure AD B2C
description: Zelf studie voor het configureren van Azure Active Directory B2C met Jumio voor geautomatiseerde ID-verificatie, het beveiligen van klant gegevens
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817420"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van Jumio met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het integreren van Azure AD B2C met [Jumio](https://www.jumio.com/). Jumio is een ID-verificatie service waarmee realtime-verificatie via een geautomatiseerde ID wordt ingeschakeld, waarbij klant gegevens worden beschermd.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- Een [Azure AD B2C-Tenant](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). De Tenant is gekoppeld aan uw Azure-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

De Jumio-integratie bevat de volgende onderdelen:

- Azure AD B2C: de autorisatie server die verantwoordelijk is voor het verifiëren van de referenties van de gebruiker, ook wel bekend als de ID-provider

- Jumio: de service die de ID-gegevens gebruikt die door de gebruiker worden verstrekt en deze verifieert.

- Tussenliggende rest-API: deze API implementeert de integratie tussen Azure AD B2C en de Jumio-service.

- Blob-opslag: wordt gebruikt om aangepaste UI-bestanden aan het Azure AD B2C-beleid toe te voegen.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![Scherm afbeelding voor jumio-Architecture-diagram](./media/partner-jumio/jumio-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt op een aanmeldings pagina. Gebruikers selecteren registreren om een nieuw account te maken en gegevens in te voeren op de pagina. Azure AD B2C worden de gebruikers kenmerken verzameld.
| 2. | Azure AD B2C roept de middelste laag-API aan en geeft de gebruikers kenmerken door.
| 3. | Met de middelste laag-API worden gebruikers kenmerken verzameld en getransformeerd in een indeling die door Jumio API kan worden verbruikt. Daarna wordt het verzonden naar Jumio.
| 4. | Nadat Jumio de gegevens heeft verbruikt en verwerkt, wordt het resultaat geretourneerd naar de middelste laag-API.
| 5. | Met de middelste laag-API worden de gegevens verwerkt en worden relevante gegevens teruggestuurd naar Azure AD B2C.
| 6. | Azure AD B2C ontvangt informatie terug van de interface van de middelste laag. Als er een fout melding wordt weer gegeven, wordt er een fout bericht voor de gebruiker getoond. Als er een reactie op succes wordt weer gegeven, wordt de gebruiker geverifieerd en naar de map geschreven.

## <a name="onboard-with-jumio"></a>Onboard met Jumio

1. Als u een Jumio-account wilt maken, neemt u contact op met [Jumio](https://www.jumio.com/contact/)

2. Wanneer een account is gemaakt, wordt de informatie in de API-configuratie gebruikt. In de volgende secties wordt het proces beschreven.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Azure AD B2C configureren met Jumio

### <a name="part-1---deploy-the-api"></a>Deel 1: de API implementeren

Implementeer de meegeleverde [API-code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) voor een Azure-service. De code kan worden gepubliceerd vanuit Visual Studio, gevolgd door deze [instructies](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>U hebt de URL van de geïmplementeerde service nodig om Azure AD te configureren met de vereiste instellingen.

### <a name="part-2---deploy-the-client-certificate"></a>Deel 2: het client certificaat implementeren

1. De aanroep van de Jumio-API wordt beveiligd door een client certificaat. Maak een zelfondertekend certificaat met behulp van de Power shell-voorbeeld code die hieronder wordt vermeld:

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. Het certificaat wordt vervolgens geëxporteerd naar de locatie die is opgegeven voor { ``your-local-path`` }.

3. Volg de instructies in dit [document](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate)om het certificaat te importeren in de Azure-app-service.

### <a name="part-3---create-a-signingencryption-key"></a>Deel 3: een hand tekening/versleutelings sleutel maken

Maak een wille keurige teken reeks met een lengte van meer dan 64 tekens die alleen letters of cijfers bevatten.

Bijvoorbeeld: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Gebruik het onderstaande Power shell-script om een alfanumerieke waarde van 64 tekens te maken.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>Deel 4: de API configureren

Toepassings instellingen kunnen worden [geconfigureerd in de app service in azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). Met deze methode kunnen instellingen veilig worden geconfigureerd zonder deze in een opslag plaats te controleren. U moet de volgende instellingen opgeven voor de rest-API:

| Toepassingsinstellingen | Bron | Notities |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio-account configuratie |     |
|JumioSettings:AuthPassword | Jumio-account configuratie |     |
|AppSettings: SigningCertThumbprint|Vinger afdruk van zelfondertekend certificaat gemaakt|  |
|AppSettings: IdTokenSigningKey| De handtekening sleutel is gemaakt met Power shell | |
| AppSettings: IdTokenEncryptionKey |Versleutelings sleutel gemaakt met Power shell
| AppSettings: IdTokenIssuer | De verlener die moet worden gebruikt voor de JWT-token (een GUID-waarde heeft de voor keur) |
| AppSettings: IdTokenAudience  | De doel groep die moet worden gebruikt voor de JWT-token (een GUID-waarde heeft de voor keur) |
|AppSettings: BaseRedirectUrl | De basis-URL van het B2C-beleid | https://{uw-Tenant naam}. b2clogin. com/{uw-toepassings-id}|
| WEBSITE_LOAD_CERTIFICATES| Vinger afdruk van zelfondertekend certificaat gemaakt |

### <a name="part-5---deploy-the-ui"></a>Deel 5: de gebruikers interface implementeren

1. Stel een [Blob Storage-container in uw opslag account in](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

2. Sla de bestanden van de gebruikers interface op in de map met de [gebruikers interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) van de BLOB-container.

#### <a name="update-ui-files"></a>Gebruikers interface bestanden bijwerken

1. Ga in de bestanden van de gebruikers interface naar de map [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. Open elk HTML-bestand.

3. Zoek en vervang {your-UI-BLOB-container-URL} door de URL van de BLOB-container.

4. Zoek en vervang {uw-Intermediate-API-URL} door de URL van de tussenliggende API app-service.

>[!NOTE]
> Als best practice, raden we aan dat klanten toestemming berichten toevoegen op de pagina met kenmerk verzameling. Informeer gebruikers dat er informatie wordt verzonden naar services van derden voor identiteits verificatie.

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Deel 6: het Azure AD B2C-beleid configureren

1. Ga naar het [Azure AD B2C-beleid](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) in de map beleid.

2. Volg dit [document](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) om [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) te downloaden

3. Configureer het beleid voor de Azure AD B2C Tenant.

>[!NOTE]
>Werk het opgegeven beleid bij om te koppelen aan uw specifieke Tenant.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C Tenant en selecteer onder beleids regels het **Framework identiteits ervaring**selecteren.

2. Selecteer uw eerder gemaakte **SignUpSignIn**.

3. Selecteer **gebruikers stroom uitvoeren** en selecteer de instellingen:

   a. **Toepassing**: Selecteer de geregistreerde app (voor beeld is JWT)

   b. **Antwoord-URL**: de **omleidings-URL** selecteren

   c. Selecteer **gebruikers stroom uitvoeren**.

4. Ga door naar de registratie stroom en maak een account

5. De Jumio-service wordt aangeroepen tijdens de stroom, nadat het gebruikers kenmerk is gemaakt. Als de stroom onvolledig is, controleert u of de gebruiker niet is opgeslagen in de map.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Aan de slag met aangepast beleid in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
