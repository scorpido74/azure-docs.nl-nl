---
title: SSL-certificaten toevoegen en beheren
description: Maak een gratis certificaat, importeer een App Service-certificaat, importeer een Key Vault-certificaat of koop een App Service-certificaat in Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 120caf459a7a8ca4e60d5e447a1e4130c0bce389
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239761"
---
# <a name="add-an-ssl-certificate-in-azure-app-service"></a>Een SSL-certificaat toevoegen in Azure App Service

[Azure App Service](overview.md) biedt een zeer schaalbare, zelfpatchende webhostingservice. In dit artikel ziet u hoe u een privécertificaat of een openbaar certificaat maken, uploaden of importeren in App Service. 

Zodra het certificaat is toegevoegd aan uw App Service-app of [functie-app,](https://docs.microsoft.com/azure/azure-functions/)u [er een aangepaste DNS-naam mee beveiligen](configure-ssl-bindings.md) of gebruiken in uw [toepassingscode.](configure-ssl-certificate-in-code.md)

In de volgende tabel worden de opties weergegeven die u hebt voor het toevoegen van certificaten in App-service:

|Optie|Beschrijving|
|-|-|
| Een gratis certificaat voor beheerde app-service maken (voorbeeld) | Een privécertificaat dat eenvoudig te gebruiken is als `www` u alleen uw [aangepaste domein](app-service-web-tutorial-custom-domain.md) of een niet-naakt domein in App Service moet beveiligen. |
| Een App Service-certificaat kopen | Een privécertificaat dat wordt beheerd door Azure. Het combineert de eenvoud van geautomatiseerd certificaatbeheer en de flexibiliteit van vernieuwings- en exportopties. |
| Een certificaat importeren uit Key Vault | Handig als u [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) gebruikt om uw [PKCS12-certificaten](https://wikipedia.org/wiki/PKCS_12)te beheren. Zie [Vereisten voor privécertificaten](#private-certificate-requirements). |
| Een privécertificaat uploaden | Als u al een privécertificaat van een externe provider hebt, u het uploaden. Zie [Vereisten voor privécertificaten](#private-certificate-requirements). |
| Een openbaar certificaat uploaden | Openbare certificaten worden niet gebruikt om aangepaste domeinen te beveiligen, maar u ze in uw code laden als u ze nodig hebt om toegang te krijgen tot externe bronnen. |

## <a name="prerequisites"></a>Vereisten

Volg deze handleiding:

- [Maak een App Service-app](/azure/app-service/).
- Alleen gratis certificaat: breng een subdomein `www.contoso.com`(bijvoorbeeld) naar App Service met een [CNAME-record.](app-service-web-tutorial-custom-domain.md#map-a-cname-record)

## <a name="private-certificate-requirements"></a>Vereisten voor privécertificaten

> [!NOTE]
> Azure Web Apps ondersteunt **geen** AES256 en alle pfx-bestanden moeten worden versleuteld met TripleDES.

Het [gratis App Service Managed Certificate](#create-a-free-certificate-preview) of het App [Service-certificaat](#import-an-app-service-certificate) voldoen al aan de vereisten van App Service. Als u ervoor kiest een privécertificaat te uploaden of te importeren naar App Service, moet uw certificaat aan de volgende vereisten voldoen:

* Geëxporteerd als [een PFX-bestand met wachtwoord](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Bevat een persoonlijke sleutel van minstens 2048 bits
* Bevat alle tussenliggende certificaten in de certificaatketen

Als u een aangepast domein in een SSL-binding wilt beveiligen, heeft het certificaat aanvullende vereisten:

* Bevat een [uitgebreid sleutelgebruik](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) voor serververificatie (OID = 1.3.6.1.5.5.7.3.1)
* Ondertekend door een vertrouwde certificeringsinstantie

> [!NOTE]
> **ECC-certificaten (cryptografie met behulp van elliptische krommen)** kunnen met App Service worden gebruikt, maar worden niet in dit artikel beschreven. Werk samen met uw certificeringsinstantie aan de exacte stappen voor het maken van ECC-certificaten.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Een gratis certificaat maken (Voorbeeld)

Het gratis App Service Managed Certificate is een turn-key oplossing voor het beveiligen van uw aangepaste DNS-naam in App Service. Het is een volledig functioneel SSL-certificaat dat wordt beheerd door App Service en automatisch wordt vernieuwd. Het gratis certificaat wordt geleverd met de volgende beperkingen:

- Ondersteunt geen wildcardcertificaten.
- Ondersteunt geen naakte domeinen.
- Is niet exporteerbaar.
- Ondersteunt geen DNS A-records.

> [!NOTE]
> Het gratis certificaat wordt uitgegeven door DigiCert. Voor sommige topdomeinen moet u DigiCert expliciet toestaan als certificaatuitgever door een [CAA-domeinrecord](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) te maken met de waarde: `0 issue digicert.com`.
> 

Ga als lid van het werk om een gratis door de app-service beheerd certificaat te maken:

Selecteer in de <a href="https://portal.azure.com" target="_blank">Azure-portal</a>in het linkermenu de>**\<app-naam app-naam ** **van App Services.** > 

Selecteer **tls/SSL-instellingen** > **Private Key Certificates (.pfx)** > App Service Managed**Certificate**in de linkernavigatie van uw app.

![Gratis certificaat maken in App-service](./media/configure-ssl-certificate/create-free-cert.png)

Elk niet-naakt domein dat goed is toegewezen aan uw app met een CNAME-record, wordt in het dialoogvenster weergegeven. Selecteer het aangepaste domein om een gratis certificaat voor te maken en selecteer **Maken**. U slechts één certificaat maken voor elk ondersteund aangepast domein.

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst **Met privésleutelcertificaten.**

![Gratis certificaat maken voltooid](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog steeds een certificaatbinding maken. Volg de stappen in [Binding maken](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Een App-servicecertificaat importeren

Als u een App Service Certificate in Azure aanschaft, beheert Azure de volgende taken:

- Zorgt voor het aankoopproces van GoDaddy.
- Voert domeinverificatie van het certificaat uit.
- Onderhoudt het certificaat in [Azure Key Vault](../key-vault/key-vault-overview.md).
- Beheert certificaatverlenging (zie [Certificaat verlengen).](#renew-certificate)
- Synchroniseer het certificaat automatisch met de geïmporteerde exemplaren in App Service-apps.

Als u een App Service-certificaat wilt kopen, gaat u naar [Certificaatvolgorde starten](#start-certificate-order).

Als u al een werkend App Service-certificaat hebt, u het:

- [Importeer het certificaat in App Service](#import-certificate-into-app-service).
- [Beheer het certificaat](#manage-app-service-certificates), zoals vernieuwen, opnieuw sleutelen en exporteren.

### <a name="start-certificate-order"></a>Certificaatorder starten

Start een app-servicecertificaatorder op de <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">pagina App Service Certificate create</a>.

![Aankoop van App Service-certificaat starten](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Gebruik de volgende tabel om u te helpen het certificaat te configureren. Klik op **Create** als u klaar bent.

| Instelling | Beschrijving |
|-|-|
| Name | Een vriendelijke naam voor uw App Service-certificaat. |
| Naakte domeinnaam | Geef hier het hoofddomein op. Het uitgegeven certificaat beveiligt zowel `www` het hoofddomein *als* het subdomein. In het uitgegeven certificaat bevat het veld Algemene naam het hoofddomein `www` en het veld Alternatieve naam onderwerp bevat het domein. Als u alleen een subdomein wilt beveiligen, geeft u hier `mysubdomain.contoso.com`de volledig gekwalificeerde domeinnaam van het subdomein op (bijvoorbeeld).|
| Abonnement | Het abonnement dat het certificaat bevat. |
| Resourcegroep | De resourcegroep die het certificaat bevat. U bijvoorbeeld een nieuwe brongroep gebruiken of dezelfde resourcegroep selecteren als uw App Service-app. |
| Certificaat SKU | Hiermee bepaalt u het type certificaat dat moet worden gemaakt, of het nu gaat om een standaardcertificaat of een [wildcardcertificaat](https://wikipedia.org/wiki/Wildcard_certificate). |
| Wettelijke voorwaarden | Klik hier om te bevestigen dat u akkoord gaat met de wettelijke voorwaarden. De certificaten zijn verkrijgbaar bij GoDaddy. |

### <a name="store-in-azure-key-vault"></a>Opslaan in Azure Key Vault

Zodra het aankoopproces van certificaten is voltooid, zijn er nog enkele stappen die u moet voltooien voordat u dit certificaat gebruiken. 

Selecteer het certificaat op de pagina [App Service-certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en klik op Stap 1 **certificaatconfiguratie:** > **Opslaan**.

![Key Vault-opslag van App Service-certificaat configureren](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) is een Azure-service die cryptografische sleutels en geheimen beschermt die worden gebruikt door cloudtoepassingen en -services. Het is de opslag van keuze voor App Service-certificaten.

Klik op de pagina **Key Vault Status** op Key Vault **Repository** om een nieuwe kluis te maken of kies een bestaande kluis. Als u ervoor kiest een nieuwe kluis te maken, gebruikt u de volgende tabel om u te helpen de kluis te configureren en op Maken te klikken. Maak de nieuwe Key Vault in dezelfde abonnements- en resourcegroep als uw App Service-app.

| Instelling | Beschrijving |
|-|-|
| Name | Een unieke naam die bestaat uit alfanumerieke tekens en streepjes. |
| Resourcegroep | Selecteer als aanbeveling dezelfde resourcegroep als uw App Service-certificaat. |
| Locatie | Selecteer dezelfde locatie als uw App Service-app. |
| Prijscategorie | Zie voor meer informatie de [prijsgegevens van Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Toegangsbeleidsregels| Hiermee definieert u de toepassingen en de toegestane toegang tot de kluisbronnen. U het later configureren, volgens de stappen bij [Meerdere toepassingen toegang verlenen tot een sleutelkluis.](../key-vault/key-vault-group-permissions-for-apps.md) |
| Toegang tot virtueel netwerk | Beperk de toegang tot bepaalde virtuele Azure-netwerken. U het later configureren, volgens de stappen bij [Azure Key Vault Firewalls en Virtual Networks configureren](../key-vault/key-vault-network-security.md) |

Zodra u de kluis hebt geselecteerd, sluit u de pagina **Key Vault Repository.** De **optie Stap 1: Opslaan** moet een groen vinkje voor succes weergeven. Houd de pagina open voor de volgende stap.

### <a name="verify-domain-ownership"></a>Domeineigendom verifiëren

Klik op dezelfde **pagina Certificaatconfiguratie** die u in de laatste stap hebt gebruikt op **Stap 2: Verifiëren**.

![Domein voor App Service-certificaat verifiëren](./media/configure-ssl-certificate/verify-domain.png)

Selecteer **Verificatie van app-service**. Aangezien u het domein al aan uw web-app hebt toegewezen (zie [Vereisten),](#prerequisites)is het al geverifieerd. Klik op **Verifiëren** om deze stap te voltooien. Klik **op** de knop Vernieuwen totdat het **berichtcertificaat is geverifieerd.**

> [!NOTE]
> Vier typen domeinverificatiemethoden worden ondersteund: 
> 
> - **App Service** - De meest handige optie wanneer het domein al is toegewezen aan een App Service-app in hetzelfde abonnement. Het maakt gebruik van het feit dat de App Service-app het domeineigendom al heeft geverifieerd.
> - **Domein** : controleer een [App Service-domein dat u in Azure hebt gekocht.](manage-custom-dns-buy-domain.md) Azure voegt automatisch de verificatie-TXT-record voor u toe en voltooit het proces.
> - **E-mail** - Verifieer het domein door een e-mail naar de domeinbeheerder te sturen. Instructies worden gegeven wanneer u de optie selecteert.
> - **Handleiding** - Controleer het domein met behulp van een HTML-pagina **(alleen standaardcertificaat)** of een DNS TXT-record. Instructies worden gegeven wanneer u de optie selecteert.

### <a name="import-certificate-into-app-service"></a>Certificaat importeren in App-service

Selecteer in de <a href="https://portal.azure.com" target="_blank">Azure-portal</a>in het linkermenu de>**\<app-naam app-naam ** **van App Services.** > 

Selecteer **tls/SSL-instellingen** > **Private Key Certificates (.pfx)** > Import App Service**Certificate**in de linkernavigatie van uw app.

![App-servicecertificaat importeren in App-service](./media/configure-ssl-certificate/import-app-service-cert.png)

Selecteer het certificaat dat u zojuist hebt gekocht en selecteer **OK**.

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst **Met privésleutelcertificaten.**

![App-servicecertificaat importeren voltooid](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog steeds een certificaatbinding maken. Volg de stappen in [Binding maken](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Een certificaat importeren uit Key Vault

Als u Azure Key Vault gebruikt om uw certificaten te beheren, u een PKCS12-certificaat importeren uit Key Vault in App Service zolang het [aan de vereisten voldoet.](#private-certificate-requirements)

Selecteer in de <a href="https://portal.azure.com" target="_blank">Azure-portal</a>in het linkermenu de>**\<app-naam app-naam ** **van App Services.** > 

Selecteer **tls/SSL-instellingen** > **Private Key Certificates (.pfx)** > Import Key Vault**Certificate**in de linkernavigatie van uw app.

![Key Vault-certificaat importeren in App-service](./media/configure-ssl-certificate/import-key-vault-cert.png)

Gebruik de volgende tabel om u te helpen het certificaat te selecteren.

| Instelling | Beschrijving |
|-|-|
| Abonnement | Het abonnement waartoe de Key Vault behoort. |
| Key Vault | De kluis met het certificaat dat u wilt importeren. |
| Certificaat | Kies uit de lijst met PKCS12-certificaten in de kluis. Alle PKCS12-certificaten in de kluis worden weergegeven met hun duimafdrukken, maar niet alle worden ondersteund in App Service. |

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst **Met privésleutelcertificaten.** Als de import mislukt met een fout, voldoet het certificaat niet aan de [vereisten voor App Service](#private-certificate-requirements).

![Certificaat Sleutelkluis importeren voltooid](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog steeds een certificaatbinding maken. Volg de stappen in [Binding maken](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Een privécertificaat uploaden

Zodra u een certificaat hebt verkregen bij uw certificaatprovider, volgt u de stappen in deze sectie om het klaar te maken voor app-service.

### <a name="merge-intermediate-certificates"></a>Tussenliggende certificaten samenvoegen

Als uw certificeringsinstantie meerdere certificaten in de certificaatketen geeft, moet u de certificaten in de juiste volgorde samenvoegen.

U doet dit door elk certificaat dat u hebt ontvangen in een teksteditor te openen.

Maak een bestand voor het samengevoegde certificaat met de naam _mergedcertificate.crt_. Kopieer de inhoud van elk certificaat in dit bestand in een teksteditor. De volgorde van uw certificaten moet de volgorde in de certificaatketen volgen, beginnend met uw certificaat en eindigend met het hoofdcertificaat. Het lijkt op het volgende voorbeeld:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Certificaat naar PFX exporteren

Exporteer uw samengevoegde SSL-certificaat met de persoonlijke sleutel die met uw certificaataanvraag is gegenereerd.

Als u de certificaataanvraag met OpenSSL hebt gegenereerd, hebt u een bestand met een persoonlijke sleutel gemaakt. Voer de volgende opdracht uit om uw certificaat naar PFX te exporteren. Vervang de tijdelijke aanduidingen _ &lt;met een privésleutelbestand>_ en _ &lt;samengevoegd-certificaatbestand>_ door de paden naar uw privésleutel en uw samengevoegde certificaatbestand.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Wanneer u daarom wordt gevraagd, geeft u een wachtwoord voor export op. U gebruikt dit wachtwoord later wanneer u uw SSL-certificaat naar App Service uploadt.

Als u IIS of _Certreq.exe_ hebt gebruikt voor het genereren van uw certificaataanvraag, installeert u het certificaat op uw lokale computer en [exporteert u het certificaat naar PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-certificate-to-app-service"></a>Certificaat uploaden naar App-service

U het certificaat nu uploaden naar App Service.

Selecteer in de <a href="https://portal.azure.com" target="_blank">Azure-portal</a>in het linkermenu de>**\<app-naam app-naam ** **van App Services.** > 

Selecteer **tls/SSL-instellingen** > **Private Key Certificates (.pfx)** > Upload**Certificate**in de linkernavigatie van uw app.

![Privécertificaat uploaden in App-service](./media/configure-ssl-certificate/upload-private-cert.png)

In **PFX-certificaatbestand** selecteert u uw PFX-bestand. Typ in **Certificaatwachtwoord** het wachtwoord dat u hebt gemaakt toen u het PFX-bestand exporteerde. Klik op **Uploaden**als u klaar bent. 

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst **Met privésleutelcertificaten.**

![Uploadcertificaat voltooid](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog steeds een certificaatbinding maken. Volg de stappen in [Binding maken](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Een openbaar certificaat uploaden

Openbare certificaten worden ondersteund in de *.cer-indeling.* 

Selecteer in de <a href="https://portal.azure.com" target="_blank">Azure-portal</a>in het linkermenu de>**\<app-naam app-naam ** **van App Services.** > 

Klik links in de navigatie van uw app op **TLS/SSL-instellingen** > **Openbare certificaten (.cer)** > Public Key Certificate**uploaden.**

Typ in **Naam**een naam voor het certificaat. Selecteer in **het bestand CER-certificaat**uw CER-bestand.

Klik op **Uploaden**.

![Openbaar certificaat uploaden in App-service](./media/configure-ssl-certificate/upload-public-cert.png)

Zodra het certificaat is geüpload, kopieert u de duimafdruk van het certificaat en ziet [u het certificaat toegankelijk maken.](configure-ssl-certificate-in-code.md#make-the-certificate-accessible)

## <a name="manage-app-service-certificates"></a>App Service-certificaten beheren

In deze sectie ziet u hoe u een App Service-certificaat beheert dat u hebt gekocht in [Een App Service-certificaat importeren.](#import-an-app-service-certificate)

- [Hersleutelcertificaat](#rekey-certificate)
- [Certificaat verlengen](#renew-certificate)
- [Certificaat exporteren](#export-certificate)
- [Certificaat verwijderen](#delete-certificate)

### <a name="rekey-certificate"></a>Hersleutelcertificaat

Als u denkt dat de privésleutel van uw certificaat is gecompromitteerd, u uw certificaat opnieuw sleutelen. Selecteer het certificaat op de pagina [App Service-certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en selecteer **Vervolgens Opnieuw sleutelen en Synchroniseren** vanaf de linkernavigatie.

Klik **op Opnieuw sleutelen** om het proces te starten. Dit proces kan 1-10 minuten in beslag nemen.

![Een App-servicecertificaat opnieuw sleutelen](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Als u uw certificaat opnieuw sleutelt, wordt het certificaat gehersleuteld met een nieuw certificaat dat is afgegeven door de certificaatautoriteit.

Zodra de rekey-bewerking is voltooid, klikt u op **Synchroniseren**. De synchronisatiebewerking werkt automatisch de hostnaambindingen voor het certificaat in App-service bij zonder dat uw apps minder tijd uitvalt.

> [!NOTE]
> Als u niet op **Synchroniseren**klikt, synchroniseert App Service uw certificaat automatisch binnen 48 uur.

### <a name="renew-certificate"></a>Certificaat verlengen

Als u automatische verlenging van uw certificaat op elk gewenst moment wilt inschakelen, selecteert u het certificaat op de pagina [App-servicecertificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en klikt u vervolgens op **Instellingen voor automatisch verlengen** in de linkernavigatie. Standaard hebben App Service-certificaten een geldigheidsduur van één jaar.

Selecteer **Aan** en klik op **Opslaan**. Certificaten kunnen 60 dagen voor het verstrijken automatisch worden verlengd als u automatische verlenging hebt ingeschakeld.

![App Service-certificaat automatisch verlengen](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Als u het certificaat handmatig wilt verlengen, klikt u op **Handmatig verlengen**. U vragen om uw certificaat 60 dagen voor het verstrijken handmatig te verlengen.

Zodra de vernieuwingsbewerking is voltooid, klikt u op **Synchroniseren**. De synchronisatiebewerking werkt automatisch de hostnaambindingen voor het certificaat in App-service bij zonder dat uw apps minder tijd uitvalt.

> [!NOTE]
> Als u niet op **Synchroniseren**klikt, synchroniseert App Service uw certificaat automatisch binnen 48 uur.

### <a name="export-certificate"></a>Certificaat exporteren

Omdat een App Service Certificate een [Key Vault-geheim](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets)is, u een PFX-kopie exporteren en gebruiken voor andere Azure-services of buiten Azure.

Als u het App Service Certificate als PFX-bestand wilt exporteren, voert u de volgende opdrachten uit in de [Cloud Shell](https://shell.azure.com). U het ook lokaal uitvoeren als u [Azure CLI hebt geïnstalleerd.](https://docs.microsoft.com/cli/azure/install-azure-cli) Vervang de tijdelijke aanduidingen door de namen die u hebt gebruikt toen u [het App Service-certificaat maakte.](#start-certificate-order)

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

Het gedownloade *appservicecertificate.pfx-bestand* is een ruw PKCS12-bestand dat zowel de openbare als de privécertificaten bevat. Gebruik in elke prompt een lege tekenreeks voor het importwachtwoord en de pem-paszin.

### <a name="delete-certificate"></a>Certificaat verwijderen 

Het verwijderen van een App Service-certificaat is definitief en onomkeerbaar. Als u een bron van een App Service Certificate overmaakt, wordt het certificaat ingetrokken. Elke binding in App Service met dit certificaat wordt ongeldig. Om onbedoelde verwijdering te voorkomen, plaatst Azure een vergrendeling op het certificaat. Als u een App Service-certificaat wilt verwijderen, moet u eerst het vergrendelingsbericht op het certificaat verwijderen.

Selecteer het certificaat op de pagina [App-servicecertificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en selecteer **Sloten** in de linkernavigatie.

Zoek het slot op uw certificaat met het vergrendelingstype **Verwijderen**. Selecteer Rechts verwijderen **.**

![Vergrendeling voor App Service-certificaat verwijderen](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Nu u het App Service-certificaat verwijderen. Selecteer **Overzicht** > **verwijderen**in de linkernavigatie . Typ in het bevestigingsdialoogvenster de certificaatnaam en selecteer **OK**.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

### <a name="azure-cli"></a>Azure-CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Meer bronnen

* [Een aangepaste DNS-naam beveiligen met een SSL-binding](configure-ssl-bindings.md)
* [HTTPS afdwingen](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 afdwingen](configure-ssl-bindings.md#enforce-tls-versions)
* [Een SSL-certificaat gebruiken in uw toepassingscode](configure-ssl-certificate-in-code.md)
* [Veelgestelde vragen: App-servicecertificaten](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
