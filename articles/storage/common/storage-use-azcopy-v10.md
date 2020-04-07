---
title: Gegevens kopiëren of verplaatsen naar Azure Storage met AzCopy v10 | Microsoft Documenten
description: AzCopy is een command-line hulpprogramma dat u gebruiken om gegevens te kopiëren naar, van of tussen opslagaccounts. Met dit artikel u AzCopy downloaden, verbinding maken met uw opslagaccount en bestanden overzetten.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8701fe6857e95334a5e1d24bfe70feb130d5512c
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756023"
---
# <a name="get-started-with-azcopy"></a>Aan de slag met AzCopy

AzCopy is een command-line hulpprogramma dat u gebruiken om blobs of bestanden te kopiëren van of naar een opslagaccount. Met dit artikel u AzCopy downloaden, verbinding maken met uw opslagaccount en bestanden overzetten.

> [!NOTE]
> AzCopy **V10** is de momenteel ondersteunde versie van AzCopy.
>
> Als u een vorige versie van AzCopy wilt gebruiken, raadpleegt u [de vorige versie van azcopy gebruiken](#previous-version) van dit artikel.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>AzCopy downloaden

Download eerst het uitvoerbaar bestand AzCopy V10 naar een map op uw computer. AzCopy V10 is slechts een uitvoerbaar bestand, dus er is niets te installeren.

- [Windows 64-bits](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bits](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (teer)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Deze bestanden worden gecomprimeerd als een zip-bestand (Windows en Mac) of een teerbestand (Linux). Zie de documentatie voor uw Linux-distributie om het teerbestand op Linux te downloaden en te decomprimeren.

> [!NOTE]
> Als u gegevens van en naar uw [Azure Table-opslagservice](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) wilt kopiëren, installeert u [AzCopy-versie 7.3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>AzCopy uitvoeren

Overweeg voor het gemak de directorylocatie van de AzCopy toe te voegen die uitvoerbaar is voor uw systeempad voor gebruiksgemak. Op die manier `azcopy` u typen uit elke directory op uw systeem.

Als u ervoor kiest de AzCopy-map niet aan uw pad toe te voegen, moet u `azcopy` `.\azcopy` mappen wijzigen in de locatie van uw Uitvoerbare AzCopy en typen of in De opdrachtprompts van Windows PowerShell.

Als u een lijst met `azcopy -h` opdrachten wilt bekijken, typt u en drukt u op ENTER.

Als u meer wilt weten over een specifieke opdracht, `azcopy list -h`moet u alleen de naam van de opdracht opnemen (Bijvoorbeeld: ).

![Inline-hulp](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Zie [azcopy](storage-ref-azcopy.md) voor het vinden van gedetailleerde referentiedocumentatie voor elke opdracht- en opdrachtparameter

> [!NOTE] 
> Als eigenaar van uw Azure Storage-account krijgt u niet automatisch machtigingen toegewezen voor toegang tot gegevens. Voordat u iets zinvols met AzCopy doen, moet u beslissen hoe u autorisatiereferenties aan de opslagservice verstrekt. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Kiezen hoe u autorisatiereferenties opgeeft

U autorisatiereferenties verstrekken met Behulp van Azure Active Directory (AD) of met een SAS-token (Shared Access Signature) gebruiken.

Gebruik deze tabel als leidraad:

| Opslagtype | Momenteel ondersteunde autorisatiemethode |
|--|--|
|**Blob-opslag** | Azure AD & SAS |
|**Blob-opslag (hiërarchische naamruimte)** | Azure AD & SAS |
|**Bestandsopslag** | Alleen SAS |

### <a name="option-1-use-azure-active-directory"></a>Optie 1: Azure Active Directory gebruiken

Door Azure Active Directory te gebruiken, u eenmaal referenties verstrekken in plaats van dat u een SAS-token aan elke opdracht moet toevoegen.  

> [!NOTE]
> Als u in de huidige versie blobs tussen opslagaccounts wilt kopiëren, moet u een SAS-token toevoegen aan elke bron-URL. U het SAS-token alleen weglaten op de bestemmings-URL. Zie [Blobs tussen opslagaccounts kopiëren voor](storage-use-azcopy-blobs.md)voorbeelden .

Het niveau van autorisatie dat u nodig hebt, is gebaseerd op de vraag of u van plan bent om bestanden te uploaden of ze gewoon te downloaden.

Als u alleen bestanden wilt downloaden, controleert u of de [Opslagblob-gegevenslezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) is toegewezen aan uw gebruikersidentiteit, beheerde identiteit of serviceprincipal.

> Gebruikersidentiteiten, beheerde identiteiten en serviceprincipals zijn elk een type *beveiligingsprincipal,* dus we gebruiken de term *beveiligingsprincipal* voor de rest van dit artikel.

Als u bestanden wilt uploaden, controleert u of een van deze rollen is toegewezen aan uw beveiligingsprincipal:

- [Opslagblob-gegevensbijdrager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Eigenaar opslagblobgegevens](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Deze rollen kunnen worden toegewezen aan uw beveiligingsprincipal in een van deze scopes:

- Container (bestandssysteem)
- Storage-account
- Resourcegroep
- Abonnement

Zie Toegang verlenen tot Azure [blob- en wachtrijgegevens met RBAC in de Azure-portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie over het verifiëren en toewijzen van rollen.

> [!NOTE]
> Houd er rekening mee dat het tot vijf minuten kan duren voordat RBAC-roltoewijzingen worden gepropageerd.

U hoeft niet een van deze rollen aan uw beveiligingsprincipal te hebben toegewezen als uw beveiligingsprincipal wordt toegevoegd aan de toegangscontrolelijst (ACL) van de doelcontainer of -map. In de ACL moet uw beveiligingsprincipal toestemming schrijven op de doelmap en toestemming uitvoeren op container en elke bovenliggende map.

Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie.

#### <a name="authenticate-a-user-identity"></a>Een gebruikersidentiteit verifiëren

Nadat u hebt geverifieerd dat uw gebruikersidentiteit het vereiste autorisatieniveau heeft gekregen, opent u een opdrachtprompt, typt u de volgende opdracht en drukt u op enter.

```azcopy
azcopy login
```

Als u tot meer dan één organisatie behoort, moet u de tenant-id van de organisatie waartoe het opslagaccount behoort, opnemen.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Vervang `<tenant-id>` de tijdelijke aanduiding door de tenant-id van de organisatie waartoe het opslagaccount behoort. Als u de tenant-id wilt zoeken, selecteert u **Azure Active Directory > Eigenschappen > Directory-id** in de Azure-portal.

Met deze opdracht retourneert een verificatiecode en de URL van een website. Open de website, geef de code op en kies de knop **Volgende.**

![Een container maken](media/storage-use-azcopy-v10/azcopy-login.png)

Er verschijnt een aanmeldingsvenster. Meld u in dat venster aan bij uw Azure-account met uw Azure-accountreferenties. Nadat u zich hebt aangemeld, u het browservenster sluiten en AzCopy gaan gebruiken.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Een serviceprincipal verifiëren

Dit is een geweldige optie als u van plan bent om AzCopy te gebruiken in een script dat wordt uitgevoerd zonder interactie van de gebruiker, vooral wanneer u on-premises wordt uitgevoerd. Als u van plan bent AzCopy uit te voeren op VM's die in Azure worden uitgevoerd, is een beheerde service-identiteit eenvoudiger te beheren. Zie het gedeelte [Een beheerde identiteit](#managed-identity) van dit artikel verifiëren voor meer informatie.

Voordat u een script uitvoert, moet u zich ten minste één keer interactief aanmelden, zodat u AzCopy de referenties van uw serviceprincipal verstrekken.  Deze referenties worden opgeslagen in een beveiligd en versleuteld bestand, zodat uw script die gevoelige informatie niet hoeft te verstrekken.

U zich aanmelden bij uw account met behulp van een geheim van de klant of met behulp van het wachtwoord van een certificaat dat is gekoppeld aan de app-registratie van uw serviceprincipal.

Zie [Hoe: De portal gebruiken om een Azure AD-toepassing en serviceprincipal te maken die toegang heeft tot bronnen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)voor meer informatie over het maken van serviceprincipal.

Zie [Hoofdobjecten voor toepassingen en services in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) voor meer informatie over serviceprincipals in het algemeen

##### <a name="using-a-client-secret"></a>Een clientgeheim gebruiken

Begin met `AZCOPY_SPA_CLIENT_SECRET` het instellen van de omgevingsvariabele op het klantgeheim van de app-registratie van uw serviceprincipal.

> [!NOTE]
> Zorg ervoor dat u deze waarde instelt op uw opdrachtprompt en niet in de omgevingsvariabele instellingen van uw besturingssysteem. Op die manier is de waarde alleen beschikbaar voor de huidige sessie.

In dit voorbeeld ziet u hoe u dit doen in PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Overweeg een prompt te gebruiken zoals in dit voorbeeld wordt weergegeven. Op die manier wordt uw wachtwoord niet weergegeven in de opdrachtgeschiedenis van uw console.  

Typ vervolgens de volgende opdracht en druk op enter.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Vervang `<application-id>` de tijdelijke aanduiding door de toepassings-id van de app-registratie van uw serviceprincipal. Vervang `<tenant-id>` de tijdelijke aanduiding door de tenant-id van de organisatie waartoe het opslagaccount behoort. Als u de tenant-id wilt zoeken, selecteert u **Azure Active Directory > Eigenschappen > Directory-id** in de Azure-portal. 

##### <a name="using-a-certificate"></a>Een certificaat gebruiken

Als u liever uw eigen referenties gebruikt voor autorisatie, u een certificaat uploaden naar uw app-registratie en dat certificaat vervolgens gebruiken om in te loggen.

Naast het uploaden van uw certificaat naar uw app-registratie, moet u ook een kopie van het certificaat opde machine of VM hebben waar AzCopy wordt uitgevoerd. Deze kopie van het certificaat dient in te staan . PFX of . PEM-formaat, en moet de private key bevatten. De privésleutel moet met een wachtwoord worden beveiligd. Als u Windows gebruikt en uw certificaat alleen in een certificaatarchief bestaat, moet u dat certificaat exporteren naar een PFX-bestand (inclusief de privésleutel). Zie [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) voor advies

Stel vervolgens `AZCOPY_SPA_CERT_PASSWORD` de omgevingsvariabele in op het certificaatwachtwoord.

> [!NOTE]
> Zorg ervoor dat u deze waarde instelt op uw opdrachtprompt en niet in de omgevingsvariabele instellingen van uw besturingssysteem. Op die manier is de waarde alleen beschikbaar voor de huidige sessie.

In dit voorbeeld ziet u hoe u deze taak uitvoeren in PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Typ vervolgens de volgende opdracht en druk op enter.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Vervang `<path-to-certificate-file>` de tijdelijke aanduiding door het relatieve of volledig gekwalificeerde pad naar het certificaatbestand. AzCopy slaat het pad naar dit certificaat op, maar het slaat geen kopie van het certificaat op, dus zorg ervoor dat dit certificaat op zijn plaats blijft. Vervang `<tenant-id>` de tijdelijke aanduiding door de tenant-id van de organisatie waartoe het opslagaccount behoort. Als u de tenant-id wilt zoeken, selecteert u **Azure Active Directory > Eigenschappen > Directory-id** in de Azure-portal.

> [!NOTE]
> Overweeg een prompt te gebruiken zoals in dit voorbeeld wordt weergegeven. Op die manier wordt uw wachtwoord niet weergegeven in de opdrachtgeschiedenis van uw console. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Een beheerde identiteit verifiëren

Dit is een geweldige optie als u van plan bent azcopy te gebruiken in een script dat wordt uitgevoerd zonder interactie van de gebruiker, en het script wordt uitgevoerd vanaf een Azure Virtual Machine (VM). Wanneer u deze optie gebruikt, hoeft u geen referenties op de VM op te slaan.

U zich aanmelden bij uw account met behulp van de door de gebruiker beheerde identiteit die u op uw vm hebt ingeschakeld, of met behulp van de client-id, object-id of resource-id van een door de gebruiker toegewezen beheerde identiteit die u aan uw vm hebt toegewezen.

Zie [Beheerde identiteiten configureren voor Azure-bronnen op een vm configureren voor](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)meer informatie over het inschakelen van een door het systeem breed beheerde identiteit of het maken van een door de gebruiker toegewezen beheerde identiteit.

##### <a name="using-a-system-wide-managed-identity"></a>Een systeembrede beheerde identiteit gebruiken

Zorg er eerst voor dat u een systeembrede beheerde identiteit op uw VM hebt ingeschakeld. Zie [door het systeem toegewezen beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Typ vervolgens in de opdrachtconsole de volgende opdracht en druk op enter.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit gebruiken

Controleer eerst of u een door de gebruiker toegewezen beheerde identiteit op uw vm hebt ingeschakeld. Zie [door de gebruiker toegewezen beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Typ vervolgens in de opdrachtconsole een van de volgende opdrachten en druk op enter.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Vervang `<client-id>` de tijdelijke aanduiding door de client-id van de door de gebruiker toegewezen beheerde identiteit.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Vervang `<object-id>` de tijdelijke aanduiding door de object-id van de door de gebruiker toegewezen beheerde identiteit.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Vervang `<resource-id>` de tijdelijke aanduiding door de resource-id van de door de gebruiker toegewezen beheerde identiteit.

### <a name="option-2-use-a-sas-token"></a>Optie 2: Een SAS-token gebruiken

U een SAS-token toevoegen aan elke bron of bestemmings-URL die wordt gebruikt in uw AzCopy-opdrachten.

Met deze opdracht wordt opnieuw gegevens uit een lokale map gekopieerd naar een blobcontainer. Een fictief SAS-token wordt toegevoegd aan het einde van de URL van de container.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Zie [Gedeelde toegangshandtekeningen (SAS) gebruiken voor](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)meer informatie over SAS-tokens en hoe u er een verkrijgen.

## <a name="transfer-files"></a>Bestandsoverdracht

Nadat u uw identiteit hebt geverifieerd of een SAS-token hebt verkregen, u beginnen met het overbrengen van bestanden.

Zie een van deze artikelen om voorbeeldopdrachten te vinden.

- [Gegevens overbrengen met AzCopy- en blob-opslag](storage-use-azcopy-blobs.md)

- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

- [Gegevens overbrengen met AzCopy- en Amazon S3-buckets](storage-use-azcopy-s3.md)

- [Gegevens overbrengen met AzCopy- en Azure Stack-opslag](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>AzCopy gebruiken in een script

### <a name="obtain-a-static-download-link"></a>Een statische downloadkoppeling verkrijgen

Na verloop van tijd zal de AzCopy [download link](#download-and-install-azcopy) wijzen op nieuwe versies van AzCopy. Als uw script AzCopy downloadt, werkt het script mogelijk niet meer als een nieuwere versie van AzCopy functies wijzigt waarvan uw script afhankelijk is.

Om deze problemen te voorkomen, verkrijgt u een statische (niet-veranderende) koppeling naar de huidige versie van AzCopy. Op die manier downloadt uw script elke keer dat het wordt uitgevoerd dezelfde exacte versie van AzCopy.

Voer de opdracht uitvoeren om de koppeling te verkrijgen:

| Besturingssysteem  | Opdracht |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Voor Linux `--strip-components=1` verwijdert `tar` de opdracht op de opdracht de map op het hoogste niveau die de versienaam bevat, en haalt in plaats daarvan de binaire direct uit in de huidige map. Hierdoor kan het script worden bijgewerkt `azcopy` met een `wget` nieuwe versie van door alleen het bijwerken van de URL.

De URL wordt weergegeven in de uitvoer van deze opdracht. Uw script kan azcopy vervolgens downloaden met behulp van die URL.

| Besturingssysteem  | Opdracht |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Ontsnap aan speciale personages in SAS-tokens

In batchbestanden met `.cmd` de extensie moet je `%` ontsnappen aan de personages die in SAS-tokens worden weergegeven. U dat doen `%` door een `%` extra teken toe te voegen naast bestaande tekens in de SAS-tekenreeks.

### <a name="run-scripts-by-using-jenkins"></a>Scripts uitvoeren met Jenkins

Als u [Jenkins](https://jenkins.io/) wilt gebruiken om scripts uit te voeren, moet u de volgende opdracht aan het begin van het script plaatsen.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>AzCopy gebruiken in Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruikt AzCopy om al zijn gegevensoverdrachtsbewerkingen uit te voeren. U [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken als u gebruik wilt maken van de prestatievoordelen van AzCopy, maar u gebruikt liever een grafische gebruikersinterface dan de opdrachtregel om met uw bestanden te communiceren.

Storage Explorer gebruikt uw accountsleutel om bewerkingen uit te voeren, dus nadat u zich hebt aangemeld bij Storage Explorer, hoeft u geen aanvullende autorisatiereferenties op te geven.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>De vorige versie van AzCopy gebruiken

Als u de vorige versie van AzCopy wilt gebruiken, raadpleegt u een van de volgende koppelingen:

- [AzCopy in Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy op Linux (v7)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy configureren, optimaliseren en oplossen

Zie [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Volgende stappen

Als u vragen, problemen of algemene feedback hebt, dient u deze in op de [GitHub-pagina.](https://github.com/Azure/azure-storage-azcopy)
