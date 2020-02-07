---
title: SSL-certificaten toevoegen en beheren
description: Maak een gratis certificaat, importeer een App Service certificaat, importeer een Key Vault certificaat of koop een App Service certificaat in Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 5df8ae89c16a453b008afed9ee9f8881a0ac4750
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046421"
---
# <a name="add-an-ssl-certificate-in-azure-app-service"></a>Een SSL-certificaat in Azure App Service toevoegen

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In dit artikel wordt beschreven hoe u een persoonlijk certificaat of een openbaar certificaat maakt, uploadt of importeert in App Service. 

Zodra het certificaat is toegevoegd aan uw App Service app of [functie-app](https://docs.microsoft.com/azure/azure-functions/), kunt u [een aangepaste DNS-naam beveiligen](configure-ssl-bindings.md) of [gebruiken in uw toepassings code](configure-ssl-certificate-in-code.md).

De volgende tabel bevat de opties die u hebt voor het toevoegen van certificaten in App Service:

|Optie|Beschrijving|
|-|-|
| Een gratis door App Service beheerd certificaat maken (preview) | Een persoonlijk certificaat dat eenvoudig te gebruiken is als u alleen uw `www` [aangepast domein](app-service-web-tutorial-custom-domain.md) of een niet-gelastig domein in app service moet beveiligen. |
| Een App Service-certificaat kopen | Een persoonlijk certificaat dat wordt beheerd door Azure. Het combineert de eenvoud van geautomatiseerd certificaat beheer en de flexibiliteit van de opties voor het vernieuwen en exporteren. |
| Een certificaat importeren uit Key Vault | Dit is handig als u [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) gebruikt voor het beheren van uw [pkcs12/pfx-profiel-certificaten](https://wikipedia.org/wiki/PKCS_12). Zie [vereisten voor persoonlijke certificaten](#private-certificate-requirements). |
| Een persoonlijk certificaat uploaden | Als u al een persoonlijk certificaat van een externe provider hebt, kunt u het uploaden. Zie [vereisten voor persoonlijke certificaten](#private-certificate-requirements). |
| Een openbaar certificaat uploaden | Open bare certificaten worden niet gebruikt voor het beveiligen van aangepaste domeinen, maar u kunt ze ook in uw code laden als u deze nodig hebt om toegang te krijgen tot externe bronnen. |

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt volgen:

- [Een app service-app maken](/azure/app-service/).
- Gratis certificaat: wijs een subdomein (bijvoorbeeld `www.contoso.com`) toe aan App Service met een CNAME- [record](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Vereisten voor persoonlijke certificaten

> [!NOTE]
> Azure Web Apps biedt **geen** ondersteuning voor AES256 en alle pfx-bestanden moeten worden versleuteld met TrippleDES.

Het [gratis door app service beheerde certificaat](#create-a-free-certificate-preview) of het [app service certificaat](#import-an-app-service-certificate) voldoet al aan de vereisten van app service. Als u ervoor kiest om een persoonlijk certificaat te uploaden of te importeren naar App Service, moet uw certificaat voldoen aan de volgende vereisten:

* Geëxporteerd als een [PFX-bestand met wachtwoord beveiliging](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Bevat een persoonlijke sleutel van minstens 2048 bits
* Bevat alle tussenliggende certificaten in de certificaatketen

Voor het beveiligen van een aangepast domein in een SSL-binding gelden aanvullende vereisten voor het certificaat:

* Bevat een [uitgebreide-sleutel gebruik](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) voor Server verificatie (OID = 1.3.6.1.5.5.7.3.1)
* Ondertekend door een vertrouwde certificeringsinstantie

> [!NOTE]
> **ECC-certificaten (cryptografie met behulp van elliptische krommen)** kunnen met App Service worden gebruikt, maar worden niet in dit artikel beschreven. Werk samen met uw certificeringsinstantie aan de exacte stappen voor het maken van ECC-certificaten.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Een gratis certificaat maken (preview)

Het gratis door App Service beheerde certificaat is een zet-sleutel oplossing voor het beveiligen van uw aangepaste DNS-naam in App Service. Het is een volledig functioneel SSL-certificaat dat wordt beheerd door App Service en automatisch wordt vernieuwd. Het gratis certificaat wordt geleverd met de volgende beperkingen:

- Ondersteunt geen joker tekens.
- Biedt geen ondersteuning voor niet-geblote domeinen.
- Kan niet worden geëxporteerd.
- Biedt geen ondersteuning voor DNS A-records.

> [!NOTE]
> Het gratis certificaat wordt uitgegeven door DigiCert. Voor sommige domeinen van het hoogste niveau moet u DigiCert als een certificaat verlener expliciet toestaan door een CAA- [domein record](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) te maken met de waarde: `0 issue digicert.com`.
> 

Een gratis door App Service beheerd certificaat maken:

Selecteer in het menu links in het <a href="https://portal.azure.com" target="_blank">Azure Portal</a> **app Services** > \<naam van de **app >** .

Selecteer in de linkernavigatiebalk van uw app de **instellingen voor TLS/SSL** > **certificaten voor persoonlijke sleutels (. pfx)**  > **app service beheerd certificaat maken**.

![Een gratis certificaat maken in App Service](./media/configure-ssl-certificate/create-free-cert.png)

In het dialoog venster wordt een niet-onwaar domein gekoppeld aan uw app met een CNAME-record. Selecteer het aangepaste domein waarvoor u een gratis certificaat wilt maken en selecteer **maken**. U kunt slechts één certificaat maken voor elk ondersteund aangepast domein.

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst met **certificaten voor persoonlijke sleutels** .

![Gratis certificaat maken is voltooid](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog steeds een certificaat binding maken. Volg de stappen in [Create binding](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Een App Service Certificate importeren

Als u een App Service Certificate aanschaft vanuit Azure, beheert Azure de volgende taken:

- Zorgt voor het aankoop proces van GoDaddy.
- Voert een domein verificatie van het certificaat uit.
- Onderhoudt het certificaat in [Azure Key Vault](../key-vault/key-vault-overview.md).
- Hiermee beheert u het vernieuwen van certificaten (Zie [certificaat vernieuwen](#renew-certificate)).
- Synchroniseer het certificaat automatisch met de geïmporteerde kopieën in App Service-apps.

Als u een App Service certificaat wilt kopen, gaat u naar [Start Certificate order](#start-certificate-order).

Als u al een werk App Service certificaat hebt, kunt u het volgende doen:

- [Importeer het certificaat in app service](#import-certificate-into-app-service).
- [Beheer het certificaat](#manage-app-service-certificates), zoals vernieuwen, opnieuw genereren en exporteer het.

### <a name="start-certificate-order"></a>Certificaat volgorde starten

Start een App Service-certificaat volgorde op de <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">pagina app service Certificate maken</a>.

![App Service certificaat kopen starten](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Gebruik de volgende tabel om u te helpen bij het configureren van het certificaat. Klik op **Create** als u klaar bent.

| Instelling | Beschrijving |
|-|-|
| Name | Een beschrijvende naam voor uw App Service certificaat. |
| Naam van het Blot-domein | Geef hier het hoofd domein op. Het verleende certificaat beveiligt *zowel* het hoofd domein als het subdomein `www`. In het verleende certificaat bevat het veld algemene naam het hoofd domein en het veld alternatieve naam voor onderwerp bevat het `www` domein. Als u alleen een subdomein wilt beveiligen, geeft u de Fully Qualified Domain Name van het subdomein hier op (bijvoorbeeld `mysubdomain.contoso.com`).|
| Abonnement | Het abonnement dat het certificaat zal bevatten. |
| Resourcegroep | De resource groep die het certificaat zal bevatten. U kunt een nieuwe resource groep gebruiken of dezelfde resource groep selecteren als uw App Service-app, bijvoorbeeld. |
| Certificaat-SKU | Hiermee wordt het type certificaat bepaald dat moet worden gemaakt, of het een standaard certificaat of een [certificaat voor joker tekens](https://wikipedia.org/wiki/Wildcard_certificate)is. |
| Juridische voor waarden | Klik om te bevestigen dat u akkoord gaat met de juridische voor waarden. De certificaten worden opgehaald van GoDaddy. |

### <a name="store-in-azure-key-vault"></a>Opslaan in Azure Key Vault

Zodra het aankoop proces van het certificaat is voltooid, zijn er nog enkele stappen die u moet volt ooien voordat u dit certificaat kunt gaan gebruiken. 

Selecteer het certificaat op de pagina [app service certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en klik vervolgens op **certificaat configuratie** > **stap 1: opslaan**.

![Key Vault opslag van App Service certificaat configureren](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) is een Azure-service die helpt bij het beveiligen van cryptografische sleutels en geheimen die worden gebruikt door Cloud toepassingen en-services. Het is de opslag van de keuze voor App Service certificaten.

Klik op de pagina **Key Vault status** op **Key Vault opslag plaats** om een nieuwe kluis te maken of kies een bestaande kluis. Als u ervoor kiest om een nieuwe kluis te maken, gebruikt u de volgende tabel om u te helpen de kluis te configureren en klikt u op maken. Maak de nieuwe Key Vault binnen hetzelfde abonnement en dezelfde resource groep als uw App Service-app.

| Instelling | Beschrijving |
|-|-|
| Name | Een unieke naam die bestaat uit alfanumerieke tekens en afbreek streepjes. |
| Resourcegroep | Als aanbeveling selecteert u dezelfde resource groep als uw App Service certificaat. |
| Locatie | Selecteer dezelfde locatie als uw App Service-app. |
| Prijscategorie | Zie [Azure Key Vault prijs informatie](https://azure.microsoft.com/pricing/details/key-vault/)voor meer informatie. |
| Toegangsbeleid| Hiermee worden de toepassingen en de toegestane toegang tot de kluis resources gedefinieerd. U kunt deze later configureren, door de stappen [te volgen om verschillende toepassingen toegang te verlenen tot een sleutel kluis](../key-vault/key-vault-group-permissions-for-apps.md). |
| Toegang Virtual Network | Beperk de toegang tot de kluis tot bepaalde virtuele netwerken van Azure. U kunt dit later configureren door de stappen te volgen op [Azure Key Vault firewalls en virtuele netwerken configureren](../key-vault/key-vault-network-security.md) |

Wanneer u de kluis hebt geselecteerd, sluit u de pagina **Key Vault opslag plaats** . Bij **stap 1:** de optie opslaan wordt een groen vinkje weer gegeven voor geslaagde pogingen. Laat de pagina voor de volgende stap geopend.

### <a name="verify-domain-ownership"></a>Domein eigendom verifiëren

Klik op de pagina **certificaat configuratie** die u in de laatste stap hebt gebruikt, op **stap 2: controleren**.

![Domein verifiëren voor App Service certificaat](./media/configure-ssl-certificate/verify-domain.png)

Selecteer **app service verificatie**. Omdat u het domein al aan uw web-app hebt toegewezen (Zie [vereisten](#prerequisites)), is het al gecontroleerd. Klik op **verifiëren** om deze stap te volt ooien. Klik op de knop **vernieuwen** totdat het bericht **certificaat is geverifieerd op domein** wordt weer gegeven.

> [!NOTE]
> Er worden vier typen domein verificatie methoden ondersteund: 
> 
> - **App service** : de handigste optie wanneer het domein al is toegewezen aan een app service-app in hetzelfde abonnement. Er wordt gebruikgemaakt van het feit dat de App Service-app al het eigendom van het domein heeft gecontroleerd.
> - **Domein** : een [app service domein controleren dat u hebt aangeschaft vanuit Azure](manage-custom-dns-buy-domain.md). Azure voegt de bevestigings-TXT-record automatisch voor u toe en voltooit het proces.
> - **E-mail** : Controleer het domein door een e-mail te verzenden naar de domein beheerder. Er worden instructies gegeven wanneer u de optie selecteert.
> - **Hand matig** : Verifieer het domein met behulp van een HTML-pagina (alleen**standaard** certificaat) of een DNS TXT-record. Er worden instructies gegeven wanneer u de optie selecteert.

### <a name="import-certificate-into-app-service"></a>Certificaat importeren in App Service

Selecteer in het menu links in het <a href="https://portal.azure.com" target="_blank">Azure Portal</a> **app Services** > \<naam van de **app >** .

Selecteer in de linkernavigatiebalk van uw app de **instellingen voor TLS/SSL** > **certificaten voor persoonlijke sleutels (. pfx)**  > het **importeren van app service Certificate**.

![App Service certificaat in App Service importeren](./media/configure-ssl-certificate/import-app-service-cert.png)

Selecteer het certificaat dat u zojuist hebt aangeschaft en selecteer **OK**.

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst met **certificaten voor persoonlijke sleutels** .

![App Service certificaat is geïmporteerd](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog steeds een certificaat binding maken. Volg de stappen in [Create binding](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Een certificaat importeren uit Key Vault

Als u Azure Key Vault gebruikt om uw certificaten te beheren, kunt u een PKCS12/pfx-profiel-certificaat importeren van Key Vault in App Service zolang dit [voldoet aan de vereisten](#private-certificate-requirements).

Selecteer in het menu links in het <a href="https://portal.azure.com" target="_blank">Azure Portal</a> **app Services** > \<naam van de **app >** .

Selecteer in de linkernavigatiebalk van uw app de **instellingen voor TLS/SSL** > **certificaten voor persoonlijke sleutels (. pfx)**  > **Key Vault certificaat importeren**.

![Key Vault certificaat in App Service importeren](./media/configure-ssl-certificate/import-key-vault-cert.png)

Gebruik de volgende tabel om u te helpen bij het selecteren van het certificaat.

| Instelling | Beschrijving |
|-|-|
| Abonnement | Het abonnement waarvan de Key Vault deel uitmaakt. |
| Key Vault | De kluis met het certificaat dat u wilt importeren. |
| Certificaat | Selecteer in de lijst met PKCS12/pfx-profiel-certificaten in de kluis. Alle PKCS12/pfx-profiel-certificaten in de kluis worden weer gegeven met hun vinger afdrukken, maar niet alle worden ondersteund in App Service. |

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst met **certificaten voor persoonlijke sleutels** . Als het importeren mislukt met een fout, voldoet het certificaat niet aan de [vereisten voor app service](#private-certificate-requirements).

![Key Vault certificaat is geïmporteerd](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog steeds een certificaat binding maken. Volg de stappen in [Create binding](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Een persoonlijk certificaat uploaden

Zodra u een certificaat van uw certificaat provider hebt ontvangen, volgt u de stappen in deze sectie om de app gereed te maken voor App Service.

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

Als u de certificaataanvraag met OpenSSL hebt gegenereerd, hebt u een bestand met een persoonlijke sleutel gemaakt. Voer de volgende opdracht uit om uw certificaat naar PFX te exporteren. Vervang de tijdelijke aanduidingen _&lt;private-key-file>_ en _&lt;merged-certificate-file>_ door de paden naar uw persoonlijke sleutel en uw bestand met samengevoegde certificaten.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Wanneer u daarom wordt gevraagd, geeft u een wachtwoord voor export op. U gebruikt dit wachtwoord later wanneer u uw SSL-certificaat naar App Service uploadt.

Als u IIS of _Certreq.exe_ hebt gebruikt voor het genereren van uw certificaataanvraag, installeert u het certificaat op uw lokale computer en [exporteert u het certificaat naar PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-certificate-to-app-service"></a>Certificaat uploaden naar App Service

U bent nu klaar om het certificaat te uploaden naar App Service.

Selecteer in het menu links in het <a href="https://portal.azure.com" target="_blank">Azure Portal</a> **app Services** > \<naam van de **app >** .

Selecteer in de linkernavigatiebalk van uw app de **instellingen voor TLS/SSL** > **certificaten voor persoonlijke sleutels (. pfx)**  > uploaden van het **certificaat**.

![Persoonlijk certificaat uploaden in App Service](./media/configure-ssl-certificate/upload-private-cert.png)

In **PFX-certificaatbestand** selecteert u uw PFX-bestand. Typ in **Certificaatwachtwoord** het wachtwoord dat u hebt gemaakt toen u het PFX-bestand exporteerde. Wanneer u klaar bent, klikt u op **uploaden**. 

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst met **certificaten voor persoonlijke sleutels** .

![Certificaat uploaden is voltooid](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog steeds een certificaat binding maken. Volg de stappen in [Create binding](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Een openbaar certificaat uploaden

Open bare certificaten worden ondersteund in de *. CER* -indeling. 

Selecteer in het menu links in het <a href="https://portal.azure.com" target="_blank">Azure Portal</a> **app Services** > \<naam van de **app >** .

Klik vanuit de linkernavigatiebalk van uw app op **TLS/SSL-instellingen** > **open bare certificaten (. CER)**  > **certificaat voor open bare sleutel uploaden**.

Typ in **naam**een naam voor het certificaat. Selecteer in **CER-certificaat bestand**uw CER-bestand.

Klik op **Uploaden**.

![Openbaar certificaat uploaden in App Service](./media/configure-ssl-certificate/upload-public-cert.png)

Nadat het certificaat is geüpload, kopieert u de vinger afdruk van het certificaat en raadpleegt [u het certificaat toegankelijk maken](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>App Service certificaten beheren

In deze sectie wordt beschreven hoe u een App Service certificaat kunt beheren dat u hebt aangeschaft in [een app service certificaat importeren](#import-an-app-service-certificate).

- [Certificaat opnieuw genereren](#rekey-certificate)
- [Certificaat vernieuwen](#renew-certificate)
- [Certificaat exporteren](#export-certificate)
- [Certificaat verwijderen](#delete-certificate)

### <a name="rekey-certificate"></a>Certificaat opnieuw genereren

Als u denkt dat de persoonlijke sleutel van uw certificaat is aangetast, kunt u uw certificaat opnieuw genereren. Selecteer het certificaat op de pagina [app service certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en selecteer vervolgens opnieuw **genereren en synchroniseren** vanuit de linkernavigatiebalk.

Klik op opnieuw **genereren** om het proces te starten. Dit proces kan 1-10 minuten duren.

![Een App Service certificaat opnieuw genereren](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Als u het certificaat opnieuw keying, wordt het certificaat gedistribueerd met een nieuw certificaat dat is uitgegeven door de certificerings instantie.

Zodra de bewerking voor opnieuw genereren is voltooid, klikt u op **synchroniseren**. Met de synchronisatie bewerking worden de hostname-bindingen voor het certificaat in App Service automatisch bijgewerkt zonder dat er uitval tijd voor uw apps wordt veroorzaakt.

> [!NOTE]
> Als u niet op **synchroniseren**klikt, wordt app service uw certificaat automatisch gesynchroniseerd binnen 48 uur.

### <a name="renew-certificate"></a>Certificaat vernieuwen

Als u de automatische verlenging van uw certificaat op elk gewenst moment wilt inschakelen, selecteert u het certificaat op de pagina [app service certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en klikt u vervolgens op **instellingen voor automatisch verlengen** in het linkernavigatievenster. App Service certificaten hebben standaard een geldigheids periode van één jaar.

Selecteer **aan** en klik op **Opslaan**. Certificaten kunnen 60 dagen vóór de verval datum automatisch worden vernieuwd als u automatische verlenging hebt ingeschakeld.

![App Service certificaat automatisch vernieuwen](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Als u het certificaat hand matig wilt vernieuwen, klikt u op **hand matig verlengen**. U kunt aanvragen om uw certificaat 60 dagen voor de verval datum hand matig te verlengen.

Zodra de vernieuwings bewerking is voltooid, klikt u op **synchroniseren**. Met de synchronisatie bewerking worden de hostname-bindingen voor het certificaat in App Service automatisch bijgewerkt zonder dat er uitval tijd voor uw apps wordt veroorzaakt.

> [!NOTE]
> Als u niet op **synchroniseren**klikt, wordt app service uw certificaat automatisch gesynchroniseerd binnen 48 uur.

### <a name="export-certificate"></a>Certificaat exporteren

Omdat een App Service Certificate een [Key Vault geheim](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets)is, kunt u een pfx-kopie van het bestand exporteren en gebruiken voor andere Azure-Services of buiten Azure.

Als u de App Service Certificate als een PFX-bestand wilt exporteren, voert u de volgende opdrachten uit in de [Cloud shell](https://shell.azure.com). U kunt deze ook lokaal uitvoeren als u [Azure cli hebt geïnstalleerd](https://docs.microsoft.com/cli/azure/install-azure-cli). Vervang de tijdelijke aanduidingen door de namen die u hebt gebruikt bij [het maken van het app service certificaat](#start-certificate-order).

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

Het gedownloade bestand *appservicecertificate. pfx* is een onbewerkt pkcs12/pfx-profiel-bestand met zowel de open bare als de persoonlijke certificaten. In elke prompt gebruikt u een lege teken reeks voor het import wachtwoord en de PEM-wachtwoordzin.

### <a name="delete-certificate"></a>Certificaat verwijderen 

Het verwijderen van een App Service certificaat is definitief en onomkeerbaar. Een binding in App Service met dit certificaat wordt ongeldig. Azure plaatst een vergren deling van het certificaat om onbedoelde verwijdering te voor komen. Als u een App Service certificaat wilt verwijderen, moet u eerst de verwijderings vergrendeling verwijderen van het certificaat.

Selecteer het certificaat op de pagina [app service certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en selecteer vervolgens **vergren** delen in het linkernavigatievenster.

Zoek de vergren deling van uw certificaat met het vergrendelings type **verwijderen**. Klik rechts hiervan op **verwijderen**.

![Vergren deling voor App Service certificaat verwijderen](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

U kunt nu het App Service certificaat verwijderen. Selecteer **overzicht** > **verwijderen**in het linkernavigatievenster. Typ in het bevestigings dialoogvenster de naam van het certificaat en selecteer **OK**.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Meer bronnen

* [Een aangepaste DNS-naam beveiligen met een SSL-binding](configure-ssl-bindings.md)
* [HTTPS afdwingen](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 afdwingen](configure-ssl-bindings.md#enforce-tls-versions)
* [Een SSL-certificaat gebruiken in uw toepassings code](configure-ssl-certificate-in-code.md)
* [Veelgestelde vragen: App Service certificaten](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
