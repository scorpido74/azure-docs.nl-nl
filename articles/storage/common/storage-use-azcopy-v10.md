---
title: Gegevens kopiëren of verplaatsen naar Azure Storage met behulp van AzCopy V10 toevoegen | Microsoft Docs
description: AzCopy is een opdracht regel programma dat u kunt gebruiken om gegevens naar, van of tussen opslag accounts te kopiëren. Dit artikel helpt u bij het downloaden van AzCopy, het maken van een verbinding met uw opslag account en het overdragen van bestanden.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: bf1303c6bb13677fdea73b2a1a707c655c58b7c1
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945661"
---
# <a name="get-started-with-azcopy"></a>Aan de slag met AzCopy

AzCopy is een opdracht regel programma dat u kunt gebruiken voor het kopiëren van blobs of bestanden naar of van een opslag account. Dit artikel helpt u bij het downloaden van AzCopy, het maken van een verbinding met uw opslag account en het overdragen van bestanden.

> [!NOTE]
> AzCopy **V10 toevoegen** is de momenteel ondersteunde versie van AzCopy.
>
> Als u een eerdere versie van AzCopy wilt gebruiken, raadpleegt u de sectie [de vorige versie van AzCopy gebruiken](#previous-version) van dit artikel.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>AzCopy downloaden

Down load eerst het uitvoer bare bestand van AzCopy V10 toevoegen naar een map op uw computer. AzCopy V10 toevoegen is slechts een uitvoerbaar bestand, dus er is niets om te installeren.

- [Windows 64-bits](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bits](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Deze bestanden worden gecomprimeerd als een zip-bestand (Windows en Mac) of een tar-bestand (Linux). Raadpleeg de documentatie voor uw Linux-distributie om het tar-bestand in Linux te downloaden en te decomprimeren.

> [!NOTE]
> Als u gegevens wilt kopiëren van en naar uw [Azure Table Storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) -service, installeert u vervolgens [AzCopy versie 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>AzCopy uitvoeren

Voor het gemak kunt u de maplocatie van het uitvoer bare bestand AzCopy toevoegen aan het systeempad om gebruiks gemak te gebruiken. Op die manier kunt u typen `azcopy` vanuit een wille keurige map op uw systeem.

Als u ervoor kiest om de AzCopy-map niet aan uw pad toe te voegen, moet u de mappen wijzigen op de locatie van het uitvoer bare AzCopy-bestand en-type `azcopy` of `.\azcopy` in Windows Power shell-opdracht prompts.

Typ `azcopy -h` en druk op de Enter-toets om een lijst met opdrachten weer te geven.

Als u meer wilt weten over een specifieke opdracht, neemt u alleen de naam van de opdracht op (bijvoorbeeld: `azcopy list -h` ).

![Inline-Help](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Zie [azcopy](storage-ref-azcopy.md) voor gedetailleerde Naslag informatie voor elke opdracht en opdracht parameter.

> [!NOTE] 
> Als eigenaar van uw Azure Storage-account, worden er niet automatisch machtigingen toegewezen voor toegang tot gegevens. Voordat u iets kunt doen met AzCopy, moet u bepalen hoe u autorisatie referenties voor de opslag service opgeeft. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Kiezen hoe u autorisatie referenties opgeeft

U kunt autorisatie referenties opgeven door gebruik te maken van Azure Active Directory (AD) of door gebruik te maken van een Shared Access Signature SAS-token.

Gebruik deze tabel als richt lijn:

| Opslagtype | Momenteel ondersteunde autorisatie methode |
|--|--|
|**Blob-opslag** | Azure AD & SAS |
|**Blob-opslag (hiërarchische naam ruimte)** | Azure AD & SAS |
|**Bestands opslag** | Alleen SAS |

### <a name="option-1-use-azure-active-directory"></a>Optie 1: Azure Active Directory gebruiken

Door Azure Active Directory te gebruiken, kunt u eenmaal referenties opgeven in plaats van een SAS-token aan elke opdracht toe te voegen.  

> [!NOTE]
> Als u in de huidige release blobs tussen opslag accounts wilt kopiëren, moet u een SAS-token toevoegen aan elke bron-URL. U kunt de SAS-token alleen weglaten van de doel-URL. Zie [blobs kopiëren tussen opslag accounts](storage-use-azcopy-blobs.md)voor voor beelden.

Het machtigings niveau dat u nodig hebt, is gebaseerd op de vraag of u bestanden wilt uploaden of gewoon wilt downloaden.

Als u alleen bestanden wilt downloaden, controleert u of de [gegevens lezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) van de opslag-blob is toegewezen aan uw gebruikers-id, beheerde identiteit of Service-Principal.

> Gebruikers identiteiten, beheerde identiteiten en service-principals zijn elk type *beveiligings-principal*, dus gebruiken we de term *Security Principal* voor de rest van dit artikel.

Als u bestanden wilt uploaden, controleert u of een van deze rollen is toegewezen aan uw beveiligingsprincipal:

- [Inzender voor Storage BLOB-gegevens](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)
- [Eigenaar van gegevens van opslag-BLOB](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Deze rollen kunnen worden toegewezen aan uw beveiligingsprincipal in een van deze bereiken:

- Container (bestands systeem)
- Storage-account
- Resourcegroep
- Abonnement

Zie voor meer informatie over het controleren en toewijzen [van rollen toegang verlenen tot Azure Blob en gegevens wachtrij met RBAC in het Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Houd er rekening mee dat de toewijzing van RBAC-rollen tot vijf minuten kan duren.

U hoeft niet een van deze rollen te hebben toegewezen aan uw beveiligingsprincipal als uw beveiligingsprincipal wordt toegevoegd aan de toegangs beheer lijst (ACL) van de doel container of directory. In de ACL heeft uw beveiligings-principal schrijf machtigingen nodig voor de doel directory, en moet de machtiging voor het uitvoeren van de container en elke bovenliggende map worden uitgevoerd.

Zie [toegangs beheer in azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie.

#### <a name="authenticate-a-user-identity"></a>Een gebruikers identiteit verifiëren

Nadat u hebt gecontroleerd of uw gebruikers-id het benodigde autorisatie niveau heeft gekregen, opent u een opdracht prompt, typt u de volgende opdracht en drukt u vervolgens op ENTER.

```azcopy
azcopy login
```

Als u deel uitmaakt van meer dan één organisatie, neemt u de Tenant-ID op van de organisatie waartoe het opslag account behoort.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Vervang de `<tenant-id>` tijdelijke aanduiding door de Tenant-id van de organisatie waartoe het opslag account behoort. Als u de Tenant-ID wilt vinden, selecteert u **Azure Active Directory > eigenschappen > Directory-id** in het Azure Portal.

Met deze opdracht worden een verificatie code en de URL van een website geretourneerd. Open de website, geef de code op en kies vervolgens de knop **volgende** .

![Een container maken](media/storage-use-azcopy-v10/azcopy-login.png)

Er wordt een aanmeldings venster weer gegeven. Meld u in dat venster aan bij uw Azure-account met behulp van de referenties van uw Azure-account. Nadat u zich hebt aangemeld, kunt u het browser venster sluiten en AzCopy gaan gebruiken.

<a id="service-principal"></a>

#### <a name="authenticate-a-service-principal"></a>Een Service-Principal verifiëren

Dit is een uitstekende optie als u van plan bent AzCopy te gebruiken in een script dat wordt uitgevoerd zonder tussen komst van de gebruiker, met name wanneer u on-premises uitvoert. Als u van plan bent AzCopy uit te voeren op Vm's die worden uitgevoerd in azure, is een beheerde service-identiteit eenvoudiger te beheren. Zie de sectie [een beheerde identiteit verifiëren](#managed-identity) in dit artikel voor meer informatie.

Voordat u een script uitvoert, moet u zich ten minste één keer interactief aanmelden zodat u AzCopy kunt opgeven met de referenties van uw service-principal.  Deze referenties worden opgeslagen in een beveiligd en versleuteld bestand, zodat uw script geen gevoelige informatie hoeft op te geven.

U kunt zich aanmelden bij uw account door gebruik te maken van een client geheim of door het wacht woord te gebruiken van een certificaat dat is gekoppeld aan de app-registratie van uw service-principal.

Zie [How to: de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)voor meer informatie over het maken van een service-principal.

Zie [toepassings-en Service-Principal-objecten in azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) voor meer informatie over service-principals in het algemeen.

##### <a name="using-a-client-secret"></a>Een client geheim gebruiken

Begin door de `AZCOPY_SPA_CLIENT_SECRET` omgevings variabele in te stellen op het client geheim van de app-registratie van de Service-Principal.

> [!NOTE]
> Zorg ervoor dat u deze waarde instelt vanaf de opdracht prompt en niet in de omgevings variabele-instellingen van uw besturings systeem. Op die manier is de waarde alleen beschikbaar voor de huidige sessie.

In dit voor beeld ziet u hoe u dit kunt doen in Power shell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> U kunt een prompt gebruiken zoals in dit voor beeld wordt weer gegeven. Op die manier wordt uw wacht woord niet weer gegeven in de opdracht geschiedenis van de console.  

Typ vervolgens de volgende opdracht en druk vervolgens op ENTER.

```azcopy
azcopy login --service-principal --certificate-path path-to-certificate-file --application-id application-id --tenant-id=tenant-id
```

Vervang de `<application-id>` tijdelijke aanduiding door de toepassings-id van de app-registratie van uw service-principal. Vervang de `<tenant-id>` tijdelijke aanduiding door de Tenant-id van de organisatie waartoe het opslag account behoort. Als u de Tenant-ID wilt vinden, selecteert u **Azure Active Directory > eigenschappen > Directory-id** in het Azure Portal. 

##### <a name="using-a-certificate"></a>Een certificaat gebruiken

Als u uw eigen referenties voor autorisatie wilt gebruiken, kunt u een certificaat uploaden naar de registratie van uw app en dat certificaat vervolgens gebruiken om u aan te melden.

Naast het uploaden van uw certificaat naar de registratie van uw app, moet u ook een kopie van het certificaat hebben opgeslagen op de machine of VM waarop AzCopy wordt uitgevoerd. Deze kopie van het certificaat moet zich in bestaan. PFX of. PEM-indeling en moet de persoonlijke sleutel bevatten. De persoonlijke sleutel moet met een wacht woord zijn beveiligd. Als u Windows gebruikt en uw certificaat alleen bestaat in een certificaat archief, moet u het certificaat exporteren naar een PFX-bestand (met inbegrip van de persoonlijke sleutel). Zie [export-pfx](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) voor meer informatie.

Vervolgens stelt u de `AZCOPY_SPA_CERT_PASSWORD` omgevings variabele in op het certificaat wachtwoord.

> [!NOTE]
> Zorg ervoor dat u deze waarde instelt vanaf de opdracht prompt en niet in de omgevings variabele-instellingen van uw besturings systeem. Op die manier is de waarde alleen beschikbaar voor de huidige sessie.

In dit voor beeld ziet u hoe u deze taak kunt uitvoeren in Power shell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Typ vervolgens de volgende opdracht en druk vervolgens op ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Vervang de `<path-to-certificate-file>` tijdelijke aanduiding door het relatieve of volledig gekwalificeerde pad naar het certificaat bestand. AzCopy slaat het pad naar dit certificaat op, maar er wordt geen kopie van het certificaat opgeslagen. Zorg er dus voor dat u dat certificaat blijft bewaren. Vervang de `<tenant-id>` tijdelijke aanduiding door de Tenant-id van de organisatie waartoe het opslag account behoort. Als u de Tenant-ID wilt vinden, selecteert u **Azure Active Directory > eigenschappen > Directory-id** in het Azure Portal.

> [!NOTE]
> U kunt een prompt gebruiken zoals in dit voor beeld wordt weer gegeven. Op die manier wordt uw wacht woord niet weer gegeven in de opdracht geschiedenis van de console. 

<a id="managed-identity"></a>

#### <a name="authenticate-a-managed-identity"></a>Een beheerde identiteit verifiëren

Dit is een uitstekende optie als u van plan bent AzCopy te gebruiken in een script dat wordt uitgevoerd zonder tussen komst van de gebruiker en het script wordt uitgevoerd vanaf een virtuele machine van Azure (VM). Wanneer u deze optie gebruikt, hoeft u geen referenties op te slaan op de VM.

U kunt zich aanmelden bij uw account met behulp van een door het systeem beheerde identiteit die u op uw virtuele machine hebt ingeschakeld, of met behulp van de client-ID, object-ID of Resource-ID van een door de gebruiker toegewezen beheerde identiteit die u aan uw VM hebt toegewezen.

Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele machine met behulp van de Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)voor meer informatie over het inschakelen van een door het systeem beheerde identiteit of het maken van een door de gebruiker toegewezen beheerde identiteit.

##### <a name="using-a-system-wide-managed-identity"></a>Een beheerde identiteit voor het hele systeem gebruiken

Zorg er eerst voor dat u een op het hele systeem beheerde identiteit hebt ingeschakeld op uw VM. Zie door [het systeem toegewezen beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Typ vervolgens in de opdracht console de volgende opdracht en druk vervolgens op ENTER.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit gebruiken

Zorg er eerst voor dat u een door de gebruiker toegewezen beheerde identiteit hebt ingeschakeld op de virtuele machine. Zie door de [gebruiker toegewezen beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Typ in de opdracht console een van de volgende opdrachten en druk vervolgens op ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Vervang de `<client-id>` tijdelijke aanduiding door de client-id van de door de gebruiker toegewezen beheerde identiteit.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Vervang de `<object-id>` tijdelijke aanduiding door de object-id van de door de gebruiker toegewezen beheerde identiteit.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Vervang de `<resource-id>` tijdelijke aanduiding door de resource-id van de door de gebruiker toegewezen beheerde identiteit.

### <a name="option-2-use-a-sas-token"></a>Optie 2: een SAS-token gebruiken

U kunt een SAS-token toevoegen aan elke bron-of doel-URL die wordt gebruikt in uw AzCopy-opdrachten.

Met dit voor beeld worden gegevens uit een lokale map recursief gekopieerd naar een BLOB-container. Een fictief SAS-token wordt toegevoegd aan het einde van de container-URL.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Zie [using Shared Access signatures (SAS) (Engelstalig)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)voor meer informatie over SAS-tokens en hoe u er een kunt verkrijgen.

## <a name="transfer-files"></a>Bestandsoverdracht

Nadat u uw identiteit hebt geverifieerd of een SAS-token hebt aangeschaft, kunt u beginnen met het overdragen van bestanden.

Zie een van deze artikelen om voorbeeld opdrachten te vinden.

- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)

- [Gegevens overdragen met AzCopy en File Storage](storage-use-azcopy-files.md)

- [Gegevens overdragen met AzCopy en Amazon S3-buckets](storage-use-azcopy-s3.md)

- [Gegevens overdragen met AzCopy en Azure Stack Storage](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>AzCopy gebruiken in een script

### <a name="obtain-a-static-download-link"></a>Een statische Download koppeling verkrijgen

Na verloop van tijd verwijst de [Download koppeling](#download-and-install-azcopy) AzCopy naar nieuwe versies van AzCopy. Als uw script AzCopy downloadt, werkt het script mogelijk niet meer als een nieuwere versie van AzCopy functies wijzigt waarvan uw script afhankelijk is.

Als u deze problemen wilt voor komen, moet u een statische koppeling (niet wijzigen) verkrijgen met de huidige versie van AzCopy. Op die manier downloadt uw script dezelfde exacte versie van AzCopy elke keer dat deze wordt uitgevoerd.

Voer de volgende opdracht uit om de koppeling te verkrijgen:

| Besturingssysteem  | Opdracht |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Voor Linux `--strip-components=1` wordt op de `tar` opdracht de map op het hoogste niveau met de versie naam verwijderd en in plaats daarvan wordt het binaire bestand rechtstreeks naar de huidige map geëxtraheerd. Hierdoor kan het script worden bijgewerkt met een nieuwe versie van `azcopy` door alleen de URL bij te werken `wget` .

De URL wordt weer gegeven in de uitvoer van deze opdracht. Uw script kan vervolgens AzCopy downloaden met behulp van deze URL.

| Besturingssysteem  | Opdracht |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Speciale tekens in SAS-tokens escapepen

In batch-bestanden met de `.cmd` extensie moet u de `%` tekens die worden weer gegeven in SAS-tokens weglaten. U kunt dit doen door een extra teken toe te voegen `%` naast `%` de bestaande tekens in de SAS-token teken reeks.

### <a name="run-scripts-by-using-jenkins"></a>Scripts uitvoeren met behulp van Jenkins

Als u van plan bent [Jenkins](https://jenkins.io/) te gebruiken om scripts uit te voeren, moet u de volgende opdracht aan het begin van het script plaatsen.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>AzCopy gebruiken in Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruikt AzCopy om alle bewerkingen voor gegevens overdracht uit te voeren. U kunt [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken als u de prestatie voordelen van AzCopy wilt benutten, maar u liever een graphical user interface gebruikt in plaats van de opdracht regel om te communiceren met uw bestanden.

Storage Explorer gebruikt uw account sleutel voor het uitvoeren van bewerkingen, dus nadat u zich hebt aangemeld bij Storage Explorer, hoeft u geen aanvullende autorisatie referenties op te geven.

<a id="previous-version"></a>

## <a name="use-the-previous-version-of-azcopy"></a>De vorige versie van AzCopy gebruiken

Als u de vorige versie van AzCopy moet gebruiken, raadpleegt u een van de volgende koppelingen:

- [AzCopy in Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy op Linux (V7)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy configureren, optimaliseren en problemen oplossen

Zie [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Volgende stappen

Als u vragen, problemen of algemene feedback hebt, verzendt u deze [op](https://github.com/Azure/azure-storage-azcopy) de pagina github.
