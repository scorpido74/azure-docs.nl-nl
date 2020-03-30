---
title: Releasenotities van Microsoft Azure Storage Explorer
description: Releasenotes voor Microsoft Azure Storage Explorer
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 986da8980a569583ef454833957ace85dd1bfbb6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351065"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Releasenotities van Microsoft Azure Storage Explorer

Dit artikel bevat de nieuwste releasenotes voor Azure Storage Explorer en releasenotes voor eerdere versies. 

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) is een zelfstandige app waarmee u eenvoudig werken met Azure Storage-gegevens op Windows, macOS en Linux.

Als u eerdere versies van Storage Explorer wilt downloaden, u de [pagina Releases](https://github.com/microsoft/AzureStorageExplorer/releases) van onze GitHub-repo bezoeken.

## <a name="version-1110"></a>Versie 1.11.0
11/4/2019

### <a name="new"></a>Nieuw
* Bewerkingen voor Blobs, ADLS Gen2 en Managed Disks maken gebruik van de geïntegreerde AzCopy. Meer in het bijzonder worden de volgende bewerkingen uitgevoerd met AzCopy:
   * Blobs
      * Open voor bewerken + Uploaden
      * Uploaden, inclusief &-neerzetten slepen
      * Download
      * #1249 & plakken kopiëren
      * Verwijderen
   * ADLS Gen2 Blobs
      * Uploaden, inclusief &-neerzetten slepen
      * Download
      * & plakken kopiëren
      * Verwijderen, inclusief map verwijderen
   * Beheerde schijven
      * Uploaden
      * Download
      * & plakken kopiëren

   Daarnaast zijn verschillende veelgevraagde functies toegevoegd aan de geïntegreerde AzCopy-ervaring:
   * Conflictoplossingen - u wordt tijdens overdrachten gevraagd om conflicten op te lossen. #1455
   * Upload en upload als paginablobs - u kiezen of AzCopy vhd- en .vhdx-bestanden uploadt als paginablobs. #1164 en #1601
   * Configureerbare AzCopy-parameters - Er zijn verschillende instellingen toegevoegd om de prestaties en het gebruik van azcopy af te stemmen. Zie hieronder meer details.

* Om ADLS Gen2- en Blobs multiprotocol toegang mogelijk te maken en ADLS Gen2-ervaringen verder te verbeteren, hebben we de volgende functies toegevoegd voor de ADLS Gen2-accounts:
   * Zoeken met behulp van vriendelijke namen om ACL-machtigingen in te stellen
   * Verborgen containers weergeven, zoals $logs en $web
   * Containerlease verwerven en breken
   * Blob lease-#848 verwerven en breken
   * Beleid voor containertoegang beheren
   * Blob-toegangslagen configureren
   * Klodders & plakken kopiëren

* In deze release bekijken we 17 extra talen. U overschakelen naar een taal naar keuze op de instellingenpagina onder 'Toepassing' → 'Regionale instellingen' → 'Taal (voorvertoning). We werken nog steeds hard aan het vertalen van extra strings en het verbeteren van de vertaalkwaliteit. Mocht u feedback hebben over een vertaling, of als u een tekenreeks opmerkt die nog niet is vertaald, open dan [een probleem op GitHub.](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=)
* Bij elke release proberen we een aantal instellingen aan boord te krijgen om de prima draaiende Storage Explorer mogelijk te maken. In deze release hebben we instellingen toegevoegd om AzCopy verder te configureren en serviceknooppunten te verbergen:
   * AzCopy bandbreedtelimiet - helpt bepalen hoeveel van het netwerk AzCopy gebruikt. U vindt deze instelling op "Transfers" → "AzCopy" → "Maximale overdrachtssnelheid". #1099
   * AzCopy MD5-controle - hiermee u configureren of en hoe strikt AzCopy controleert op MD5-hashes bij het downloaden. U vindt deze instelling op "Transfers" → "AzCopy" → "Check MD5".
   * AzCopy-gelijktijdigheid en geheugenbuffergrootte - standaard analyseert AzCopy uw machine om redelijke standaardwaarden voor deze instellingen te bepalen. Maar als u prestatieproblemen ondervindt, kunnen deze geavanceerde instellingen worden gebruikt om verder op maat te maken hoe AzCopy op uw computer wordt uitgevoerd. U deze instellingen vinden onder Transfers → AzCopy. #994
   * Serviceknooppunten weergeven en verbergen : deze instellingen bieden u de opties om een van de Azure-services die Storage Explorer ondersteunt, weer te geven of te verbergen. U deze instellingen vinden onder de sectie Services. #1877

* Bij het maken van een momentopname van een beheerde schijf wordt nu een standaardnaam opgegeven. #1847
* Wanneer u een ADLS Gen2 Blob-container koppelt met Azure AD, wordt '(ADLS Gen2)" naast het knooppunt weergegeven. #1861

### <a name="fixes"></a>Oplossingen
* Bij het kopiëren, uploaden of downloaden van grote schijven, zou Storage Explorer soms niet intetrekken toegang tot de schijven die betrokken zijn bij de operatie. Dit probleem is opgelost. #2048
* Tabelstatistieken zijn mislukt bij het weergeven van een partitiesleutelquery. Dit probleem is opgelost. #1886

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer 1.11.0 vereist nu een DFS-eindpunt (zoals 'myaccount.dfs.core.windows.net') om te koppelen aan ADLS Gen2-containers. In eerdere versies van Storage Explorer u een blobeindpunt gebruiken. Deze bijlagen werken mogelijk niet meer na een upgrade naar 1.11.0. Als u dit probleem ondervindt, koppelt u het DFS-eindpunt opnieuw.
* Numerieke instellingen worden niet gecontroleerd op de vraag of ze in een geldig bereik liggen.#2140
* Het kopiëren van blobcontainers van het ene opslagaccount naar het andere in de structuurweergave kan mislukken. We onderzoeken het probleem.#2124
* De instelling Automatisch vernieuwen heeft nog geen invloed op alle bewerkingen in de Blob Explorer.
* Functies voor beheerde schijf worden niet ondersteund in Azure Stack.
* Als een schijf uploaden of plakken mislukt en er een nieuwe schijf is gemaakt voordat de fout is mislukt, verwijdert Storage Explorer de schijf niet voor u.
* Afhankelijk van wanneer u een schijfupload of -plak annuleert, is het mogelijk om de nieuwe schijf in een beschadigde staat achter te laten. Als dit gebeurt, moet u de nieuwe schijf verwijderen of handmatig de schijf-API's bellen om de inhoud van de schijf zodanig te vervangen dat deze niet langer beschadigd is.
* Bij het gebruik van RBAC vereist Storage Explorer enkele beheerderslaagmachtigingen om toegang te krijgen tot uw opslagbronnen. Zie de [handleiding voor probleemoplossing](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor meer informatie.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
   * Beheerde schijven
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [handleiding voor probleemoplossing van](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.

## <a name="previous-releases"></a>Vorige versies

* [Versie 1.10.1](#version-1101)
* [Versie 1.10.0](#version-1100)
* [Versie 1.9.0](#version-190)
* [Versie 1.8.1](#version-181)
* [Versie 1.8.0](#version-180)
* [Versie 1.7.0](#version-170)
* [Versie 1.6.2](#version-162)
* [Versie 1.6.1](#version-161)
* [Versie 1.6.0](#version-160)
* [Versie 1.5.0](#version-150)
* [Versie 1.4.4](#version-144)
* [Versie 1.4.3](#version-143)
* [Versie 1.4.2](#version-142)
* [Versie 1.4.1](#version-141)
* [Versie 1.3.0](#version-130)
* [Versie 1.2.0](#version-120)
* [Versie 1.1.0](#version-110)
* [Versie 1.0.0](#version-100)
* [Versie 0.9.6](#version-096)
* [Versie 0.9.5](#version-095)
* [Versie 0.9.4 en 0.9.3](#version-094-and-093)
* [Versie 0.9.2](#version-092)
* [Versie 0.9.1 en 0.9.0](#version-091-and-090)
* [Versie 0.8.16](#version-0816)
* [Versie 0.8.14](#version-0814)
* [Versie 0.8.13](#version-0813)
* [Versie 0.8.12 en 0.8.11 en 0.8.10](#version-0812-and-0811-and-0810)
* [Versie 0.8.9 en 0.8.8](#version-089-and-088)
* [Versie 0.8.7](#version-087)
* [Versie 0.8.6](#version-086)
* [Versie 0.8.5](#version-085)
* [Versie 0.8.4](#version-084)
* [Versie 0.8.3](#version-083)
* [Versie 0.8.2](#version-082)
* [Versie 0.8.0](#version-080)
* [Versie 0.7.20160509.0](#version-07201605090)
* [Versie 0.7.20160325.0](#version-07201603250)
* [Versie 0.7.20160129.1](#version-07201601291)
* [Versie 0.7.20160105.0](#version-07201601050)
* [Versie 0.7.2015116.0](#version-07201511160)

## <a name="version-1101"></a>Versie 1.10.1
9/19/2019

### <a name="hotfix"></a>Hotfix
* Sommige gebruikers ondervonden een fout in 1.10.0 tijdens een poging om hun gegevens te bekijken in hun ADLS Gen 1-accounts. Met deze fout kon het explorer-paneel niet goed renderen. Dit probleem is opgelost. #1853 #1865

### <a name="new"></a>Nieuw
* Storage Explorer heeft nu een speciale gebruikersinterface voor instellingen. U het openen via Instellingen bewerken → of door te klikken op het pictogram Instellingen (het tandwiel) op de verticale werkbalk aan de linkerkant. Deze functie is de eerste stap die we nemen in de richting van het verstrekken van een verscheidenheid van [de gebruiker gevraagde instellingen](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Vanaf deze release worden de volgende instellingen ondersteund:
  * Thema
  * Proxy
  * Afmelden bij het afsluiten #6
  * Aanmelden voor apparaatcodestroom inschakelen
  * Automatisch vernieuwen #1526
  * AzCopy inschakelen
  * AzCopy SAS-duur Als er andere instellingen zijn die u wilt toevoegen, opent u [een probleem op GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) waarin de instelling wordt beschreven die u wilt zien.
* Storage Explorer ondersteunt nu Beheerde schijven. U kunt:
  * Een on-premises VHD uploaden naar een nieuwe schijf
  * Een schijf downloaden
  * Schijven kopiëren en plakken tussen resourcegroepen en -regio's
  * Schijven verwijderen
  * Een momentopname van een schijf maken

Het uploaden, downloaden en cross-region kopiëren van schijven worden aangedreven door AzCopy v10.
* Storage Explorer kan nu worden geïnstalleerd via de Snap store op Linux. Wanneer u installeert via de Snap-winkel, worden alle afhankelijkheden voor u geïnstalleerd, inclusief .NET Core! Momenteel hebben we geverifieerd dat Storage Explorer goed draait op Ubuntu en CentOS. Als u problemen ondervindt bij het installeren van de Snap-winkel op andere Linux-distro's, opent u [een probleem op GitHub.](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=) Zie onze [handleiding aan](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux)de slag voor meer informatie over het installeren in de Snap-winkel. #68
* Er zijn twee belangrijke wijzigingen aangebracht bij Azure Active Directory (Azure AD) die bedoeld zijn om de functie nuttiger te maken voor ADLS Gen2-gebruikers:
  * U selecteert nu de tenant waarin de resource zit die u koppelt. Dit betekent dat u geen RBAC-toegang meer hoeft te hebben tot het abonnement van de resource.
  * Als u een ADLS Gen2 Blob Container koppelt, u nu een specifiek pad in de container toevoegen.
* Wanneer u ACL's voor ADLS Gen2-bestanden en -mappen beheert, toont Storage Explorer nu de vriendelijke namen voor entiteiten in de ACL. #957
* Wanneer u via OID toevoegt aan een ADLS Gen2 ACL, zal Storage Explorer nu valideren dat de OID tot een geldige entiteit in uw tenant behoort. #1603
* De sneltoetsen voor het navigeren tussen tabbladen maken nu gebruik van meer standaard toetscombinaties. #1018
* Als u op een tabblad klikt, wordt deze nu gesloten. #1348
* Als een AzCopy-overdracht overslaan en geen fouten bevat, wordt in Storage Explorer nu een waarschuwingspictogram weergegeven om te benadrukken dat er is overgeslagen. #1490
* De geïntegreerde AzCopy is bijgewerkt naar versie 10.2.1. Bovendien u nu de versie van AzCopy bekijken die is geïnstalleerd in het dialoogvenster Over. #1343

### <a name="fixes"></a>Oplossingen
* Veel gebruikers hebben verschillende "kan lezen versie van ongedefinieerde" of "kan niet lezen verbinding van ongedefinieerde" fouten bij het werken met bijgevoegde opslagaccounts. Hoewel we nog steeds de oorzaak van dit probleem blijven onderzoeken, hebben we in 1.10.0 de foutafhandeling rond het laden van bijgevoegde opslagaccounts verbeterd. #1626, #985 en #1532
* Het was mogelijk voor de explorer boom (linkerkant) te krijgen in een staat waar de focus zou springen naar de bovenste knooppunt herhaaldelijk. Dit probleem is opgelost. #1596
* Bij het beheren van de momentopnamen van een blob lezen schermlezers de tijdstempel die aan de momentopname is gekoppeld, niet. Dit probleem is opgelost. #1202
* Proxy-instelling op macOS werd niet op tijd ingesteld voor het verificatieproces om ze te gebruiken. Dit probleem is opgelost. #1567
* Als een opslagaccount in een soevereine cloud is gekoppeld met naam en sleutel, zou AzCopy niet werken. Dit probleem is opgelost. #1544
* Wanneer u via een verbindingstekenreeks wordt gekoppeld, verwijdert Storage Explorer nu de naloopruimten. #1387

### <a name="known-issues"></a>Bekende problemen
* De instelling Automatisch vernieuwen heeft nog geen invloed op alle bewerkingen in de Blob Explorer.
* Functies voor beheerde schijf worden niet ondersteund in Azure Stack.
* Als een schijf uploaden of plakken mislukt en er een nieuwe schijf is gemaakt voordat de fout is mislukt, verwijdert Storage Explorer de schijf niet voor u.
* Afhankelijk van wanneer u een schijfupload of -plak annuleert, is het mogelijk om de nieuwe schijf in een beschadigde staat achter te laten. Als dit gebeurt, moet u de nieuwe schijf verwijderen of handmatig de schijf-API's bellen om de inhoud van de schijf zodanig te vervangen dat deze niet langer beschadigd is.
* Afhankelijk van wanneer u een schijfupload of -plak annuleert, is het mogelijk om de nieuwe schijf in een beschadigde staat achter te laten. Als dit gebeurt, moet u de nieuwe schijf verwijderen of handmatig de schijf-API's bellen om de inhoud van de schijf zodanig te vervangen dat deze niet langer beschadigd is.
* Bij het uitvoeren van een niet-AzCopy Blob download, wordt de MD5 voor grote bestanden niet geverifieerd. Dit is te wijten aan een bug in de Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Bij het gebruik van RBAC vereist Storage Explorer enkele beheerderslaagmachtigingen om toegang te krijgen tot uw opslagbronnen. Zie de [handleiding voor probleemoplossing](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor meer informatie.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
   * Beheerde schijven
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [handleiding voor probleemoplossing van](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.


## <a name="version-1100"></a>Versie 1.10.0
9/12/2019

### <a name="new"></a>Nieuw

* Storage Explorer heeft nu een speciale gebruikersinterface voor instellingen. U het openen via Instellingen bewerken → of door te klikken op het pictogram Instellingen (het tandwiel) op de verticale werkbalk aan de linkerkant. Deze functie is de eerste stap die we nemen in de richting van het verstrekken van een verscheidenheid van [de gebruiker gevraagde instellingen](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Vanaf deze release worden de volgende instellingen ondersteund:
    * Thema
    * Proxy
    * Afmelden bij het [afsluiten #6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Aanmelden voor apparaatcodestroom inschakelen
    * Automatisch [vernieuwen #1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * AzCopy inschakelen
    * AzCopy SAS-duur

    Als er andere instellingen zijn die u wilt toevoegen, opent u [een probleem op GitHub waarin de instelling wordt beschreven die u wilt zien.](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=)
* Storage Explorer ondersteunt nu Beheerde schijven. U kunt:
    * Een on-premises VHD uploaden naar een nieuwe schijf
    * Een schijf downloaden
    * Schijven kopiëren en plakken tussen resourcegroepen en -regio's
    * Schijven verwijderen
    * Een momentopname van een schijf maken

    Het uploaden, downloaden en cross-region kopiëren van schijven worden aangedreven door AzCopy v10.
* Storage Explorer kan nu worden geïnstalleerd via de Snap store op Linux. Wanneer u installeert via de Snap-winkel, worden alle afhankelijkheden voor u geïnstalleerd, inclusief .NET Core! Momenteel hebben we geverifieerd dat Storage Explorer goed draait op Ubuntu en CentOS. Als u problemen ondervindt bij het installeren van de Snap-winkel op andere Linux-distro's, opent u [een probleem op GitHub.](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=) Zie onze [handleiding aan](https://aka.ms/storageexplorer/snapinformation)de slag voor meer informatie over het installeren in de Snap-winkel. [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Er zijn twee belangrijke wijzigingen aangebracht bij Azure Active Directory (Azure AD) die bedoeld zijn om de functie nuttiger te maken voor ADLS Gen2-gebruikers: * U selecteert nu de tenant waarin de bron zich bevindt. Dit betekent dat u geen RBAC-toegang meer hoeft te hebben tot het abonnement van de resource.
        * Als u een ADLS Gen2 Blob Container bevestigt, u nu een specifiek pad in de container toevoegen.
* Wanneer u ACL's voor ADLS Gen2-bestanden en -mappen beheert, toont Storage Explorer nu de vriendelijke namen voor entiteiten in de ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Wanneer u via OID toevoegt aan een ADLS Gen2 ACL, zal Storage Explorer nu valideren dat de OID tot een geldige entiteit in uw tenant behoort. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* De sneltoetsen voor het navigeren tussen tabbladen maken nu gebruik van meer standaard toetscombinaties. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Als u op een tabblad klikt, wordt deze nu gesloten. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Als een AzCopy-overdracht overslaan en geen fouten bevat, wordt in Storage Explorer nu een waarschuwingspictogram weergegeven om te benadrukken dat er is overgeslagen. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* De geïntegreerde AzCopy is bijgewerkt naar versie 10.2.1. Bovendien u nu de versie van AzCopy bekijken die is geïnstalleerd in het dialoogvenster Over. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Oplossingen

* Veel gebruikers hebben verschillende "kan lezen versie van ongedefinieerde" of "kan niet lezen verbinding van ongedefinieerde" fouten bij het werken met bijgevoegde opslagaccounts. Hoewel we nog steeds de oorzaak van dit probleem blijven onderzoeken, hebben we in 1.10.0 de foutafhandeling rond het laden van bijgevoegde opslagaccounts verbeterd. [#1626,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626) [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)en [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Het was mogelijk voor de explorer boom (linkerkant) te krijgen in een staat waar de focus zou springen naar de bovenste knooppunt herhaaldelijk. Dit probleem is opgelost. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Bij het beheren van de momentopnamen van een blob lezen schermlezers de tijdstempel die aan de momentopname is gekoppeld, niet. Dit probleem is opgelost. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Proxy-instelling op macOS werd niet op tijd ingesteld voor het verificatieproces om ze te gebruiken. Dit probleem is opgelost. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Als een opslagaccount in een soevereine cloud is gekoppeld met naam en sleutel, zou AzCopy niet werken. Dit probleem is opgelost. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Wanneer u via een verbindingstekenreeks wordt gekoppeld, verwijdert Storage Explorer nu de naloopruimten. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Bekende problemen

* De instelling Automatisch vernieuwen heeft nog geen invloed op alle bewerkingen in de Blob Explorer.
* Functies voor beheerde schijf worden niet ondersteund in Azure Stack.
* Als een schijf uploaden of plakken mislukt en er een nieuwe schijf is gemaakt voordat de fout is mislukt, verwijdert Storage Explorer de schijf niet voor u.
* Afhankelijk van wanneer u een schijfupload of -plak annuleert, is het mogelijk om de nieuwe schijf in een beschadigde staat achter te laten. Als dit gebeurt, moet u de nieuwe schijf verwijderen of handmatig de schijf-API's bellen om de inhoud van de schijf zodanig te vervangen dat deze niet langer beschadigd is.
* Bij het uitvoeren van een niet-AzCopy Blob download, wordt de MD5 voor grote bestanden niet geverifieerd. Dit is te wijten aan een bug in de Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Bij het gebruik van RBAC vereist Storage Explorer enkele beheerderslaagmachtigingen om toegang te krijgen tot uw opslagbronnen. Zie de [handleiding voor probleemoplossing](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor meer informatie.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
   * Beheerde schijven
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [handleiding voor probleemoplossing van](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.

## <a name="version-190"></a>Versie 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Azure Storage Explorer 1.9.0 downloaden
- [Azure Storage Explorer 1.9.0 voor Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.9.0 voor Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.9.0 voor Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nieuw

* U blobcontainers nu koppelen via Azure AD (RBAC- of ACL-machtigingen). Deze functie is bedoeld om gebruikers te helpen die toegang hebben tot containers, maar niet de opslagaccounts waarin de containers zich bevinden. Zie onze handleiding aan de slag voor meer informatie over deze functie.
* Verwerven en break lease nu werken met RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Het beheren van toegangsbeleid en het instellen van het niveau van openbare toegang werken nu met RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Blobmappen verwijderen werkt nu met RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Als u de blobtoegangslaag wijzigt, werkt nu met RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Je Quick Access nu snel opnieuw instellen via 'Help' → 'Resetten'. [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Preview-functies

* Aanmelden voor apparaatcodestroom is nu beschikbaar voor een voorbeeld. Ga naar 'Voorbeeld' → 'Aanmelden voor apparaatcodestroom gebruiken'. We moedigen gebruikers die problemen hebben gehad met lege aanmeldingsvensters aan om deze functie uit te proberen, omdat het een betrouwbaardere vorm van aanmelding kan blijken te zijn.
* Storage Explorer geïntegreerd met AzCopy is momenteel beschikbaar voor een preview. Ga naar 'Preview' → "AzCopy gebruiken voor verbeterde blobupload en download". Blob-overdrachten die zijn voltooid met AzCopy moeten sneller en performant zijn.

### <a name="fixes"></a>Oplossingen

* Vast niet in staat om meer dan 50 abonnementen te laden voor een account. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Fixed the "Sign in" button not working on the infobar that appears when a direct link fails. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Fixed not being to upload .app files on macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Fixed "Retry All" werkt niet voor een mislukte blob hernoemen. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Fixed "Cancel" werkt niet tijdens het openen van een blob. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Fixed multiple spelling and tooltip issues throughout the product. Veel dank aan allen die deze problemen gemeld! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336) [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), #1368 , [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368) [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Bekende problemen

* Bij het uitvoeren van een niet-AzCopy Blob download, wordt de MD5 voor grote bestanden niet geverifieerd. Dit is te wijten aan een bug in de Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Bij het gebruik van RBAC vereist Storage Explorer enkele beheerderslaagmachtigingen om toegang te krijgen tot uw opslagbronnen. Zie de [handleiding voor probleemoplossing](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor meer informatie.
* Een poging om toegang te krijgen tot ADLS Gen2 Blobs wanneer u zich achter een proxy bevindt, kan mislukken.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [handleiding voor probleemoplossing van](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.

## <a name="version-181"></a>Versie 1.8.1
5/13/2019

### <a name="hotfixes"></a>Hotfixes
* In sommige gevallen zou het klikken op 'Meer laden' op resourceniveau de volgende pagina met bronnen niet retourneren. Dit probleem is opgelost. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* Op Windows zouden AzCopy-downloads mislukken als één bestand of map werd gedownload en de naam van het bestand of de map een teken had dat ongeldig was voor een Windows-pad. Dit probleem is opgelost. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* In uiterst zeldzame gevallen, tijdens het uitvoeren van een hernaam van een bestandsshare of een naamswijziging in een bestandsshare, als de kopieën voor de naamswijziging zijn mislukt of als Storage Explore het succes van de kopieën met Azure niet kon bevestigen, is er de mogelijkheid voor Storage Explorer om de originele bestanden voordat de kopie klaar was. Dit probleem is opgelost.

### <a name="new"></a>Nieuw

* De geïntegreerde AzCopy-versie is bijgewerkt naar versie 10.1.0.
* Ctrl/Cmd+R kan nu worden gebruikt om de momenteel gerichte editor te vernieuwen. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* De Azure Stack Storage API-versie is gewijzigd in 2017-04-17.
* Het dialoogvenster Toegangs beheren voor ADLS Gen2 houdt het masker nu gesynchroniseerd op een manier die vergelijkbaar is met andere POSIX-machtigingen. De gebruikersinterface waarschuwt u ook als er een wijziging wordt aangebracht waardoor de machtigingen van een gebruiker of groep de grenzen van het masker overschrijden. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Voor AzCopy-uploads is de vlag voor het berekenen en instellen van de MD5-hash nu ingeschakeld. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Preview-functies

* Aanmelden voor apparaatcodestroom is nu beschikbaar voor een voorbeeld. Ga naar 'Voorbeeld' → 'Aanmelden voor apparaatcodestroom gebruiken'. We moedigen gebruikers die problemen hebben gehad met lege aanmeldingsvensters aan om deze functie uit te proberen, omdat het een betrouwbaardere vorm van aanmelding kan blijken te zijn.
* Storage Explorer geïntegreerd met AzCopy is momenteel beschikbaar voor een preview. Ga naar 'Preview' → "AzCopy gebruiken voor verbeterde blobupload en download". Blob-overdrachten die zijn voltooid met AzCopy moeten sneller en performant zijn.

### <a name="fixes"></a>Oplossingen

* In het dialoogvenster Toegangsbeleid wordt niet langer een vervaldatum ingesteld voor opslagtoegangsbeleid dat geen vervaldatum heeft. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Er zijn enkele wijzigingen aangebracht in het dialoogvenster SAS genereren om ervoor te zorgen dat opgeslagen toegangsbeleid correct wordt gebruikt bij het genereren van een SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Wanneer u een niet-512-byte-uitgelijnd bestand probeert te uploaden naar een pagina blob, stelt Storage Explorer nu een relevantere fout bloot. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Het kopiëren van een Blob-container die een weergavenaam heeft gebruikt, zou mislukken. Nu wordt de werkelijke naam van de Blob-container gebruikt. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Een poging om bepaalde acties uit te voeren op een ADLS Gen2-map met unicode-tekens in zijn naam zou mislukken. Alle acties moeten nu werken. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Bekende problemen

* Bij het uitvoeren van een niet-AzCopy Blob download, wordt de MD5 voor grote bestanden niet geverifieerd. Dit is te wijten aan een bug in de Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Bij het gebruik van RBAC vereist Storage Explorer enkele beheerderslaagmachtigingen om toegang te krijgen tot uw opslagbronnen. Zie de [handleiding voor probleemoplossing](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor meer informatie.
* Een poging om toegang te krijgen tot ADLS Gen2 Blobs wanneer u zich achter een proxy bevindt, kan mislukken.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [handleiding voor probleemoplossing van](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.

## <a name="version-180"></a>Versie 1.8.0
1/5/2019

### <a name="new"></a>Nieuw

* De geïntegreerde AzCopy-versie is bijgewerkt naar versie 10.1.0.
* Ctrl/Cmd+R kan nu worden gebruikt om de momenteel gerichte editor te vernieuwen. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* De Azure Stack Storage API-versie is gewijzigd in 2017-04-17.
* Het dialoogvenster Toegangs beheren voor ADLS Gen2 houdt het masker nu gesynchroniseerd op een manier die vergelijkbaar is met andere POSIX-machtigingen. De gebruikersinterface waarschuwt u ook als er een wijziging wordt aangebracht waardoor de machtigingen van een gebruiker of groep de grenzen van het masker overschrijden. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Voor AzCopy-uploads is de vlag voor het berekenen en instellen van de MD5-hash nu ingeschakeld. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Preview-functies

* Aanmelden voor apparaatcodestroom is nu beschikbaar voor een voorbeeld. Ga naar 'Voorbeeld' → 'Aanmelden voor apparaatcodestroom gebruiken'. We moedigen gebruikers die problemen hebben gehad met lege aanmeldingsvensters aan om deze functie uit te proberen, omdat het een betrouwbaardere vorm van aanmelding kan blijken te zijn.
* Storage Explorer geïntegreerd met AzCopy is momenteel beschikbaar voor een preview. Ga naar 'Preview' → "AzCopy gebruiken voor verbeterde blobupload en download". Blob-overdrachten die zijn voltooid met AzCopy moeten sneller en performant zijn.

### <a name="fixes"></a>Oplossingen

* In het dialoogvenster Toegangsbeleid wordt niet langer een vervaldatum ingesteld voor opslagtoegangsbeleid dat geen vervaldatum heeft. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Er zijn enkele wijzigingen aangebracht in het dialoogvenster SAS genereren om ervoor te zorgen dat opgeslagen toegangsbeleid correct wordt gebruikt bij het genereren van een SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Wanneer u een niet-512-byte-uitgelijnd bestand probeert te uploaden naar een pagina blob, stelt Storage Explorer nu een relevantere fout bloot. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Het kopiëren van een Blob-container die een weergavenaam heeft gebruikt, zou mislukken. Nu wordt de werkelijke naam van de Blob-container gebruikt. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Een poging om bepaalde acties uit te voeren op een ADLS Gen2-map met unicode-tekens in zijn naam zou mislukken. Alle acties moeten nu werken. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Bekende problemen

* Bij het uitvoeren van een niet-AzCopy Blob download, wordt de MD5 voor grote bestanden niet geverifieerd. Dit is te wijten aan een bug in de Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Bij het gebruik van RBAC vereist Storage Explorer enkele beheerderslaagmachtigingen om toegang te krijgen tot uw opslagbronnen. Zie de [handleiding voor probleemoplossing](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor meer informatie.
* Een poging om toegang te krijgen tot ADLS Gen2 Blobs wanneer u zich achter een proxy bevindt, kan mislukken.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [handleiding voor probleemoplossing van](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.

## <a name="version-170"></a>Versie 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Azure Storage Explorer 1.7.0 downloaden
- [Azure Storage Explorer 1.7.0 voor Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.7.0 voor Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.7.0 voor Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nieuw

* U nu de eigenaar en de eigenaargroep wijzigen wanneer u de toegang voor een ADLS Gen2-container, bestand of map beheert.
* In Windows is het bijwerken van Storage Explorer vanuit het product nu een incrementele installatie. Dit moet resulteren in een snellere update-ervaring. Als u liever een schone installatie, dan u het [installatieprogramma](https://azure.microsoft.com/features/storage-explorer/) zelf downloaden en vervolgens handmatig installeren. #1089

### <a name="preview-features"></a>Preview-functies

* Aanmelden voor apparaatcodestroom is nu beschikbaar voor een voorbeeld. Ga naar 'Voorbeeld' → 'Aanmelden voor apparaatcodestroom gebruiken'. We moedigen gebruikers die problemen hebben gehad met lege aanmeldingsvensters aan om deze functie uit te proberen, omdat het een betrouwbaardere vorm van aanmelding kan blijken te zijn. #938
* Storage Explorer geïntegreerd met AzCopy is momenteel beschikbaar voor een preview. Ga naar 'Preview' → "AzCopy gebruiken voor verbeterde blobupload en download". Blob-overdrachten die zijn voltooid met AzCopy moeten sneller en performant zijn.

### <a name="fixes"></a>Oplossingen

* U nu het blobtype kiezen dat u wilt uploaden als wanneer AzCopy is ingeschakeld. #1111
* Als u voorheen statische websites voor een ADLS Gen2-opslagaccount had ingeschakeld en deze vervolgens met naam en sleutel had gekoppeld, zou Storage Explorer niet hebben gedetecteerd dat de hiërarchische naamruimte is ingeschakeld. Dit probleem is opgelost. #1081
* In de blobeditor is sorteren op bewaardagen of status verbroken. Dit probleem is opgelost. #1106
* Na 1.5.0 wachtte Storage Explorer niet langer tot kopieën aan de serverzijde waren voltooid voordat het succes rapporteerde tijdens een hernoeming of kopiëren & plakken. Dit probleem is opgelost. #976
* Bij het gebruik van de experimentele AzCopy-functie was de opdracht die is gekopieerd na het klikken op 'Command naar klembord kopiëren' niet altijd alleen uitgevoerd. Nu worden alle opdrachten die nodig zijn om de overdracht handmatig uit te voeren, gekopieerd. #1079
* Voorheen waren ADLS Gen2-blobs niet toegankelijk als u achter een proxy zat. Dit was te wijten aan een bug in een nieuwe netwerkbibliotheek die wordt gebruikt door de Storage SDK. In 1.7.0 is een poging gedaan om dit probleem te verzachten, maar sommige mensen kunnen problemen blijven zien. Een volledige oplossing zal worden vrijgegeven in een toekomstige update. #1090
* In 1.7.0 onthoudt het dialoogvenster Bestands opslaan nu correct de laatste locatie waarop u een bestand hebt opgeslagen. #16
* In het deelvenster Eigenschappen werd de SKU-laag van een opslagaccount weergegeven als de soort van het account. Dit probleem is opgelost. #654
* Soms was het onmogelijk om de lease van een blob te verbreken, zelfs als u de naam van de blob correct hebt ingevoerd. Dit probleem is opgelost. #1070

### <a name="known-issues"></a>Bekende problemen

* Bij het gebruik van RBAC vereist Storage Explorer enkele beheerderslaagmachtigingen om toegang te krijgen tot uw opslagbronnen. Zie de [handleiding voor probleemoplossing](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) voor meer informatie.
* Een poging om toegang te krijgen tot ADLS Gen2 Blobs wanneer u zich achter een proxy bevindt, kan mislukken.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Versie 1.6.2
1/9/2019

### <a name="hotfixes"></a>Hotfixes
* In 1.6.1 werden entiteiten die door ObjectId aan ADLS Gen2-ACL's zijn toegevoegd en die geen gebruikers waren, altijd als groepen toegevoegd. Nu worden alleen groepen toegevoegd als groepen en worden entiteiten zoals Enterprise Applications enService Principals als gebruikers toegevoegd. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Als een ADLS Gen2 Storage-account geen containers had en was gekoppeld met naam en sleutel, dan zou Storage Explorer niet detecteren dat het opslagaccount ADLS Gen2 was. Dit probleem is opgelost. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* In 1.6.0, conflicten tijdens kopiëren en plakken zou niet vragen om een oplossing. In plaats daarvan zou de conflicterende kopie gewoon mislukken. Nu, bij het eerste conflict, wordt u gevraagd hoe u het zou willen oplossen. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Vanwege API-beperkingen zijn alle validatie van ObjectIds in het dialoogvenster Toegang beheren uitgeschakeld. Validatie vindt nu alleen plaats voor upn's van de gebruiker. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* In het dialoogvenster Toegang tot beheren van ADLS kunnen de machtigingen voor een groep niet worden gewijzigd. Dit probleem is opgelost. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Ondersteuning voor het uploaden van slepen en neerzetten toegevoegd aan de ADLS Gen2-editor. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* De eigenschap URL in het dialoogvenster Eigenschappen voor ADLS Gen2-bestanden en -mappen miste soms een '/'. Dit probleem is opgelost. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Als het verkrijgen van de huidige machtigingen voor een ADLS Gen2-container, bestand of map mislukt, wordt de fout nu correct weergegeven in het activiteitenlogboek. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Het tijdelijke pad dat is gemaakt voor het openen van bestanden is ingekort om de kans op het maken van een pad dat langer is dan MAX_PATH op Windows te verminderen. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Het dialoogvenster Verbinding wordt nu correct weergegeven wanneer er geen aangemelde gebruikers zijn en er geen resources zijn gekoppeld. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* In 1.6.0 zou het opslaan van eigenschappen voor niet-HNS Blobs en Bestanden de waarde van elke eigenschap coderen. Dit resulteerde in onnodige codering van waarden die alleen ASCII-tekens bevatten. Nu worden waarden alleen gecodeerd als ze niet-ASCII-tekens bevatten. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Het uploaden van een map naar een niet-HNS Blob-container zou mislukken als een SAS werd gebruikt en de SAS geen leesmachtigingen had. Dit probleem is opgelost. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Het annuleren van een AzCopy-overdracht werkte niet. Dit probleem is opgelost. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy zou mislukken wanneer u een map uit een ADLS Gen2 Blob-container probeert te downloaden als de map spaties in zijn naam had. Dit probleem is opgelost. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* De CosmosDB editor werd gebroken in 1.6.0. Het is nu opgelost. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nieuw

* U Nu Storage Explorer gebruiken om toegang te krijgen tot uw Blob-gegevens via [RBAC.](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409) Als u bent aangemeld en Storage Explorer de sleutels voor uw opslagaccount niet kan ophalen, wordt een OAuth-token gebruikt om te verifiëren wanneer u interactie heeft met uw gegevens.
* Storage Explorer ondersteunt nu ADLS Gen2 Storage-accounts. Wanneer Storage Explorer detecteert dat hiërarchische naamruimte is ingeschakeld voor een Opslagaccount, ziet u '(ADLS Gen2 Preview)" naast de naam van uw opslagaccount. Storage Explorer kan detecteren of hiërarchische naamruimte is ingeschakeld wanneer u bent aangemeld of dat u uw opslagaccount met naam en sleutel hebt gekoppeld. Voor ADLS Gen2 Storage-accounts u Storage Explorer gebruiken om:
  * Containers maken en verwijderen
  * Containereigenschappen en machtigingen beheren (links)
  * Gegevens in containers weergeven en navigeren
  * Nieuwe mappen maken
  * Bestanden en mappen uploaden, downloaden, hernoemen en verwijderen
  * Bestands- en mapeigenschappen en machtigingen beheren (rechterkant).
    
    Andere typische Blob-functies, zoals Soft Delete en Snapshots, zijn momenteel niet beschikbaar. Het beheren van machtigingen is ook alleen beschikbaar wanneer u bent aangemeld. Bovendien gebruikt Storage Explorer, wanneer u in een ADLS Gen2-opslagaccount werkt, AzCopy voor alle uploads en downloads en standaard voor het gebruik van naam en sleutelreferenties voor alle bewerkingen, indien beschikbaar.
* Na sterke feedback van gebruikers kan break lease opnieuw worden gebruikt om leases op meerdere blobs tegelijk te breken.

### <a name="known-issues"></a>Bekende problemen

* Bij het downloaden van een ADLS Gen2 Storage-account, als een van de bestanden die worden overgedragen al bestaat, dan zal AzCopy soms crashen. Dit zal worden opgelost in een aankomende hotfix.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Versie 1.6.1
12/18/2018

### <a name="hotfixes"></a>Hotfixes
* Vanwege API-beperkingen zijn alle validatie van ObjectIds in het dialoogvenster Toegang beheren uitgeschakeld. Validatie vindt nu alleen plaats voor upn's van de gebruiker. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* In het dialoogvenster Toegang tot beheren van ADLS kunnen de machtigingen voor een groep niet worden gewijzigd. Dit probleem is opgelost. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Ondersteuning voor het uploaden van slepen en neerzetten toegevoegd aan de ADLS Gen2-editor. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* De eigenschap URL in het dialoogvenster Eigenschappen voor ADLS Gen2-bestanden en -mappen miste soms een '/'. Dit probleem is opgelost. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Als het verkrijgen van de huidige machtigingen voor een ADLS Gen2-container, bestand of map mislukt, wordt de fout nu correct weergegeven in het activiteitenlogboek. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Het tijdelijke pad dat is gemaakt voor het openen van bestanden is ingekort om de kans op het maken van een pad dat langer is dan MAX_PATH op Windows te verminderen. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Het dialoogvenster Verbinding wordt nu correct weergegeven wanneer er geen aangemelde gebruikers zijn en er geen resources zijn gekoppeld. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* In 1.6.0 zou het opslaan van eigenschappen voor niet-HNS Blobs en Bestanden de waarde van elke eigenschap coderen. Dit resulteerde in onnodige codering van waarden die alleen ASCII-tekens bevatten. Nu worden waarden alleen gecodeerd als ze niet-ASCII-tekens bevatten. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Het uploaden van een map naar een niet-HNS Blob-container zou mislukken als een SAS werd gebruikt en de SAS geen leesmachtigingen had. Dit probleem is opgelost. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Het annuleren van een AzCopy-overdracht werkte niet. Dit probleem is opgelost. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy zou mislukken wanneer u een map uit een ADLS Gen2 Blob-container probeert te downloaden als de map spaties in zijn naam had. Dit probleem is opgelost. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* De CosmosDB editor werd gebroken in 1.6.0. Het is nu opgelost. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nieuw

* U Nu Storage Explorer gebruiken om toegang te krijgen tot uw Blob-gegevens via [RBAC.](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409) Als u bent aangemeld en Storage Explorer de sleutels voor uw opslagaccount niet kan ophalen, wordt een OAuth-token gebruikt om te verifiëren wanneer u interactie heeft met uw gegevens.
* Storage Explorer ondersteunt nu ADLS Gen2 Storage-accounts. Wanneer Storage Explorer detecteert dat hiërarchische naamruimte is ingeschakeld voor een Opslagaccount, ziet u '(ADLS Gen2 Preview)" naast de naam van uw opslagaccount. Storage Explorer kan detecteren of hiërarchische naamruimte is ingeschakeld wanneer u bent aangemeld of dat u uw opslagaccount met naam en sleutel hebt gekoppeld. Voor ADLS Gen2 Storage-accounts u Storage Explorer gebruiken om:
  * Containers maken en verwijderen
  * Containereigenschappen en machtigingen beheren (links)
  * Gegevens in containers weergeven en navigeren
  * Nieuwe mappen maken
  * Bestanden en mappen uploaden, downloaden, hernoemen en verwijderen
  * Bestands- en mapeigenschappen en machtigingen beheren (rechterkant).
    
    Andere typische Blob-functies, zoals Soft Delete en Snapshots, zijn momenteel niet beschikbaar. Het beheren van machtigingen is ook alleen beschikbaar wanneer u bent aangemeld. Bovendien gebruikt Storage Explorer, wanneer u in een ADLS Gen2-opslagaccount werkt, AzCopy voor alle uploads en downloads en standaard voor het gebruik van naam en sleutelreferenties voor alle bewerkingen, indien beschikbaar.
* Na sterke feedback van gebruikers kan break lease opnieuw worden gebruikt om leases op meerdere blobs tegelijk te breken.

### <a name="known-issues"></a>Bekende problemen

* Bij het downloaden van een ADLS Gen2 Storage-account, als een van de bestanden die worden overgedragen al bestaat, dan zal AzCopy soms crashen. Dit zal worden opgelost in een aankomende hotfix.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Versie 1.6.0
5-12-2018

### <a name="new"></a>Nieuw

* U Nu Storage Explorer gebruiken om toegang te krijgen tot uw Blob-gegevens via [RBAC.](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409) Als u bent aangemeld en Storage Explorer de sleutels voor uw opslagaccount niet kan ophalen, wordt een OAuth-token gebruikt om te verifiëren wanneer u interactie heeft met uw gegevens.
* Storage Explorer ondersteunt nu ADLS Gen2 Storage-accounts. Wanneer Storage Explorer detecteert dat hiërarchische naamruimte is ingeschakeld voor een Opslagaccount, ziet u '(ADLS Gen2 Preview)" naast de naam van uw opslagaccount. Storage Explorer kan detecteren of hiërarchische naamruimte is ingeschakeld wanneer u bent aangemeld of dat u uw opslagaccount met naam en sleutel hebt gekoppeld. Voor ADLS Gen2 Storage-accounts u Storage Explorer gebruiken om:
  * Containers maken en verwijderen
  * Containereigenschappen en machtigingen beheren (links)
  * Gegevens in containers weergeven en navigeren
  * Nieuwe mappen maken
  * Bestanden en mappen uploaden, downloaden, hernoemen en verwijderen
  * Bestands- en mapeigenschappen en machtigingen beheren (rechterkant).
    
    Andere typische Blob-functies, zoals Soft Delete en Snapshots, zijn momenteel niet beschikbaar. Het beheren van machtigingen is ook alleen beschikbaar wanneer u bent aangemeld. Bovendien gebruikt Storage Explorer, wanneer u in een ADLS Gen2-opslagaccount werkt, AzCopy voor alle uploads en downloads en standaard voor het gebruik van naam en sleutelreferenties voor alle bewerkingen, indien beschikbaar.
* Na sterke feedback van gebruikers kan break lease opnieuw worden gebruikt om leases op meerdere blobs tegelijk te breken.

### <a name="known-issues"></a>Bekende problemen

* Bij het downloaden van een ADLS Gen2 Storage-account, als een van de bestanden die worden overgedragen al bestaat, dan zal AzCopy soms crashen. Dit zal worden opgelost in een aankomende hotfix.
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Versie 1.5.0
10/29/2018

### <a name="new"></a>Nieuw

* U [azcopy v10 (Preview)](https://github.com/Azure/azure-storage-azcopy) nu gebruiken voor het uploaden en downloaden van Blobs. Als u deze functie wilt inschakelen, gaat u naar het menu 'Experimenteel' en klikt u op 'AzCopy gebruiken voor verbeterde blobupload en download'. Indien ingeschakeld, wordt AzCopy gebruikt in de volgende scenario's:
   * Upload mappen en bestanden naar blobcontainers, via de werkbalk of slepen en neerzetten.
   * Het downloaden van mappen en bestanden, via de werkbalk of het contextmenu.

* Bovendien, bij het gebruik van AzCopy:
   * U de opdracht AzCopy kopiëren die wordt gebruikt om de overdracht naar uw klembord uit te voeren. Klik in het activiteitenlogboek op 'AzCopy-opdracht kopiëren naar klembord'.
   * Je moet de blob-editor handmatig vernieuwen na het uploaden.
   * Het uploaden van bestanden om blobs toe te beelden wordt niet ondersteund en vhd-bestanden worden geüpload als paginablobs en alle andere bestanden worden geüpload als blokblobs.
   * Fouten en conflicten die optreden tijdens het uploaden of downloaden worden pas opgedoken nadat een upload of download is voltooid.

Ten slotte zal er ondersteuning komen voor het gebruik van AzCopy met bestandsshares in de toekomst.
* Storage Explorer maakt nu gebruik van Electron versie 2.0.11.
* Het breken van leases kan nu alleen worden uitgevoerd op een blob tegelijk. Daarnaast moet je de naam invoeren van de blob waarvan je de lease breekt. Deze wijziging is aangebracht om de kans op het per ongeluk breken van een huurovereenkomst te verminderen, met name voor VM's. #394
* Als u ooit aanmeldingsproblemen tegenkomt, u nu proberen de verificatie opnieuw in te stellen. Ga naar het menu 'Help' en klik op 'Opnieuw instellen' om toegang te krijgen tot deze mogelijkheid. #419

### <a name="fix"></a>Fix

* Na sterke feedback van gebruikers is het standaard emulatorknooppunt opnieuw ingeschakeld. U nog steeds extra emulatorverbindingen toevoegen via het dialoogvenster Verbinding, maar als uw emulator is geconfigureerd om de standaardpoorten te gebruiken, u ook het knooppunt Emulator * Standaardpoorten gebruiken onder 'Lokale & gekoppelde/opslagaccounts'. #669
* Met Storage Explorer u niet langer blobmetagegevenswaarden instellen met voorloop- of achterliggende witruimte. #760
* De knop Aanmelden is altijd ingeschakeld op dezelfde pagina's van het dialoogvenster Verbinding. Het is nu uitgeschakeld indien nodig. #761
* Quick Access genereert niet langer een fout in de console wanneer er geen Quick Access-items zijn toegevoegd.

### <a name="known-issues"></a>Bekende problemen

* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op dit probleem.
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron. Als u dit probleem wilt oplossen bij het uploaden naar of downloaden van een blobcontainer, u de experimentele AzCopy-functie gebruiken.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Als u probeert deze functies te gebruiken tijdens het werken met Azure Stack-resources, kunnen er onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Versie 1.4.4
10/15/2018

### <a name="hotfixes"></a>Hotfixes
* De Azure Resource Management API-versie is teruggedraaid om azure-gebruikers van de Amerikaanse overheid te deblokkeren. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Laadspinners gebruiken nu CSS-animaties om de hoeveelheid GPU die door Storage Explorer wordt gebruikt, te verminderen. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nieuw
* Externe bronbijlagen, zoals voor SAS-verbindingen en emulators, zijn aanzienlijk verbeterd. U kunt nu:
   * Pas de weergavenaam aan van de resource die u aanmaakt. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Koppel aan meerdere lokale emulators met behulp van verschillende poorten. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Gekoppelde resources toevoegen aan Snelle toegang. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer ondersteunt nu Soft Delete. U kunt:
   * Configureer een beleid voor softdelete door met de rechtermuisknop op het blobcontainersknooppunt voor uw opslagaccount te klikken.
   * Bekijk zachte verwijderde blobs in de Blob Editor door 'Actieve en verwijderde blobs' te selecteren in de vervolgkeuzelijst naast de navigatiebalk.
   * Verwijder zachte verwijderde blobs.

### <a name="fixes"></a>Oplossingen
* De actie CorS-instellingen configureren is niet langer beschikbaar op Premium Storage-accounts omdat Premium Storage-accounts CORS niet ondersteunen. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Er is nu een eigenschap Gedeelde toegangshandtekening voor SAS Attached Services. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* De actie Standaardtoegangslaag instellen is nu beschikbaar voor Blob- en GPV2-opslagaccounts die zijn vastgemaakt aan Quick Access. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Soms kan Storage Explorer geen klassieke opslagaccounts weergeven. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekende problemen
* Wanneer u emulators gebruikt, zoals Azure Storage Emulator of Azurite, moet u ze laten luisteren naar verbindingen op hun standaardpoorten. Anders kan Storage Explorer geen verbinding met hen maken.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op [dit probleem.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond [hier](https://github.com/Azure/azure-storage-node/issues/317)beschreven .
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Versie 1.4.3
10/11/2018

### <a name="hotfixes"></a>Hotfixes
* De Azure Resource Management API-versie is teruggedraaid om azure-gebruikers van de Amerikaanse overheid te deblokkeren. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Laadspinners gebruiken nu CSS-animaties om de hoeveelheid GPU die door Storage Explorer wordt gebruikt, te verminderen. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nieuw
* Externe bronbijlagen, zoals voor SAS-verbindingen en emulators, zijn aanzienlijk verbeterd. U kunt nu:
   * Pas de weergavenaam aan van de resource die u aanmaakt. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Koppel aan meerdere lokale emulators met behulp van verschillende poorten. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Gekoppelde resources toevoegen aan Snelle toegang. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer ondersteunt nu Soft Delete. U kunt:
   * Configureer een beleid voor softdelete door met de rechtermuisknop op het blobcontainersknooppunt voor uw opslagaccount te klikken.
   * Bekijk zachte verwijderde blobs in de Blob Editor door 'Actieve en verwijderde blobs' te selecteren in de vervolgkeuzelijst naast de navigatiebalk.
   * Verwijder zachte verwijderde blobs.

### <a name="fixes"></a>Oplossingen
* De actie CorS-instellingen configureren is niet langer beschikbaar op Premium Storage-accounts omdat Premium Storage-accounts CORS niet ondersteunen. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Er is nu een eigenschap Gedeelde toegangshandtekening voor SAS Attached Services. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* De actie Standaardtoegangslaag instellen is nu beschikbaar voor Blob- en GPV2-opslagaccounts die zijn vastgemaakt aan Quick Access. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Soms kan Storage Explorer geen klassieke opslagaccounts weergeven. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekende problemen
* Wanneer u emulators gebruikt, zoals Azure Storage Emulator of Azurite, moet u ze laten luisteren naar verbindingen op hun standaardpoorten. Anders kan Storage Explorer geen verbinding met hen maken.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op [dit probleem.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond [hier](https://github.com/Azure/azure-storage-node/issues/317)beschreven .
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Versie 1.4.2
09/24/2018

### <a name="hotfixes"></a>Hotfixes
* Update Azure Resource Management API-versie naar 2018-07-01 om ondersteuning toe te voegen voor nieuwe Azure Storage-accountsoorten. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Nieuw
* Externe bronbijlagen, zoals voor SAS-verbindingen en emulators, zijn aanzienlijk verbeterd. U kunt nu:
   * Pas de weergavenaam aan van de resource die u aanmaakt. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Koppel aan meerdere lokale emulators met behulp van verschillende poorten. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Gekoppelde resources toevoegen aan Snelle toegang. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer ondersteunt nu Soft Delete. U kunt:
   * Configureer een beleid voor softdelete door met de rechtermuisknop op het blobcontainersknooppunt voor uw opslagaccount te klikken.
   * Bekijk zachte verwijderde blobs in de Blob Editor door 'Actieve en verwijderde blobs' te selecteren in de vervolgkeuzelijst naast de navigatiebalk.
   * Verwijder zachte verwijderde blobs.

### <a name="fixes"></a>Oplossingen
* De actie CorS-instellingen configureren is niet langer beschikbaar op Premium Storage-accounts omdat Premium Storage-accounts CORS niet ondersteunen. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Er is nu een eigenschap Gedeelde toegangshandtekening voor SAS Attached Services. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* De actie Standaardtoegangslaag instellen is nu beschikbaar voor Blob- en GPV2-opslagaccounts die zijn vastgemaakt aan Quick Access. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Soms kan Storage Explorer geen klassieke opslagaccounts weergeven. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekende problemen
* Wanneer u emulators gebruikt, zoals Azure Storage Emulator of Azurite, moet u ze laten luisteren naar verbindingen op hun standaardpoorten. Anders kan Storage Explorer geen verbinding met hen maken.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op [dit probleem.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond [hier](https://github.com/Azure/azure-storage-node/issues/317)beschreven .
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Versie 1.4.1
08/28/2018

### <a name="hotfixes"></a>Hotfixes
* Bij de eerste lancering kon Storage Explorer de sleutel die wordt gebruikt om gevoelige gegevens te versleutelen, niet genereren. Dit zou problemen veroorzaken bij het gebruik van Quick Access en het koppelen van resources. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Als voor uw account mfa niet voor de thuistenant is vereist, maar voor sommige andere tenants wel, kan Storage Explorer geen abonnementen aanbieden. Nu, na het aanmelden met een dergelijk account, zal Storage Explorer u vragen om uw referenties opnieuw in te voeren en MFA uit te voeren. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Storage Explorer kan geen resources van Azure Germany en Azure US Government toevoegen. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Als u zich hebt aangemeld bij twee accounts met hetzelfde e-mailadres, wordt uw resources soms niet weergegeven in de structuurweergave. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Op langzamere Windows-machines, zou het welkomstscherm soms een aanzienlijke hoeveelheid tijd in beslag nemen om te verschijnen. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Het dialoogvenster Verbinding wordt weergegeven, zelfs als er gekoppelde accounts of services zijn. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Nieuw
* Externe bronbijlagen, zoals voor SAS-verbindingen en emulators, zijn aanzienlijk verbeterd. U kunt nu:
   * Pas de weergavenaam aan van de resource die u aanmaakt. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Koppel aan meerdere lokale emulators met behulp van verschillende poorten. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Gekoppelde resources toevoegen aan Snelle toegang. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer ondersteunt nu Soft Delete. U kunt:
   * Configureer een beleid voor softdelete door met de rechtermuisknop op het blobcontainersknooppunt voor uw opslagaccount te klikken.
   * Bekijk zachte verwijderde blobs in de Blob Editor door 'Actieve en verwijderde blobs' te selecteren in de vervolgkeuzelijst naast de navigatiebalk.
   * Verwijder zachte verwijderde blobs.

### <a name="fixes"></a>Oplossingen
* De actie CorS-instellingen configureren is niet langer beschikbaar op Premium Storage-accounts omdat Premium Storage-accounts CORS niet ondersteunen. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Er is nu een eigenschap Gedeelde toegangshandtekening voor SAS Attached Services. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* De actie Standaardtoegangslaag instellen is nu beschikbaar voor Blob- en GPV2-opslagaccounts die zijn vastgemaakt aan Quick Access. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Soms kan Storage Explorer geen klassieke opslagaccounts weergeven. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekende problemen
* Wanneer u emulators gebruikt, zoals Azure Storage Emulator of Azurite, moet u ze laten luisteren naar verbindingen op hun standaardpoorten. Anders kan Storage Explorer geen verbinding met hen maken.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op [dit probleem.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond [hier](https://github.com/Azure/azure-storage-node/issues/317)beschreven .
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Versie 1.3.0
07/09/2018

### <a name="new"></a>Nieuw
* Toegang tot de $web containers die worden gebruikt door Statische Websites wordt nu ondersteund. Hierdoor u eenvoudig bestanden en mappen uploaden en beheren die door uw website worden gebruikt. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* De app-balk op macOS is gereorganiseerd. Wijzigingen omvatten een menu Bestand, enkele wijzigingen in de sneltoets en verschillende nieuwe opdrachten onder het app-menu. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Het eindpunt voor de autoriteit voor het aanmelden bij Azure US Government is gewijzigd inhttps://login.microsoftonline.us/
* Toegankelijkheid: Wanneer een schermlezer actief is, werkt toetsenbordnavigatie nu met de tabellen die worden gebruikt voor het weergeven van items aan de rechterkant. U de pijltoetsen gebruiken om door rijen en kolommen te navigeren, Enter om standaardacties aan te roepen, de contextmenusleutel om het contextmenu voor een item te openen en Shift of Control om te multiselecteren. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Oplossingen
*  Op sommige machines, kind processen duurde een lange tijd om te beginnen. Wanneer dit zou gebeuren, verschijnt er een fout 'onderliggend proces dat niet tijdig is gestart'. De tijd die is toegewezen voor een onderliggend proces om te beginnen is nu verhoogd van 20 naar 90 seconden. Als u nog steeds last hebt van dit probleem, u reageren op het gekoppelde GitHub-probleem. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Bij het gebruik van een SAS die geen leesmachtigingen had, was het niet mogelijk om een grote blob te uploaden. De logica voor upload en upload is aangepast om in dit scenario te werken. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Als u het openbare toegangsniveau voor een container instelt, wordt alle toegangsbeleidsregels verwijderd en omgekeerd. Nu blijven het toegangsniveau en het toegangsbeleid behouden bij het instellen van een van de twee. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* 'AccessTierChangeTime' is afgekapt in het dialoogvenster Eigenschappen. Dit probleem is opgelost. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* Het voorvoegsel 'Microsoft Azure Storage Explorer -' ontbrak in het dialoogvenster Nieuwe map maken. Dit probleem is opgelost. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Toegankelijkheid: het dialoogvenster Entiteit toevoegen was moeilijk te navigeren bij het gebruik van VoiceOver. Er zijn verbeteringen aangebracht. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Toegankelijkheid: de achtergrondkleur van de knop Samenvouwen/uitvouwen voor het deelvenster Handelingen en eigenschappen was niet in overeenstemming met vergelijkbare ui-besturingselementen in het thema Black met een hoog contrast. De kleur is veranderd. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Toegankelijkheid: In het thema Hoog contrast zwart was de focusstyling voor de knop 'X' in het dialoogvenster Eigenschappen niet zichtbaar. Dit probleem is opgelost. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Toegankelijkheid: de tabbladen Acties en eigenschappen misten verschillende aria-waarden, wat resulteerde in een subpar-schermlezerervaring. De ontbrekende aria-waarden zijn nu toegevoegd. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Toegankelijkheid: Samengevouwen boomknooppunten aan de linkerkant kregen geen aria-uitgevouwen waarde van false. Dit probleem is opgelost. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Bekende problemen
* Het loskoppelen van een resource die via SAS URI is gekoppeld, zoals een blobcontainer, kan een fout veroorzaken waardoor andere bijlagen niet correct worden weergegeven. Als u dit probleem wilt omzeilen, vernieuwt u het groepsknooppunt. Zie [dit probleem](https://github.com/Microsoft/AzureStorageExplorer/issues/537) voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op [dit probleem.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond [hier](https://github.com/Azure/azure-storage-node/issues/317)beschreven .
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Azure Stack biedt geen ondersteuning voor de volgende functies en pogingen om ze te gebruiken tijdens het werken met Azure Stack kunnen onverwachte fouten veroorzaken:
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Versie 1.2.0
06/12/2018

### <a name="new"></a>Nieuw
* Als Storage Explorer geen abonnementen laadt van slechts een subset van uw tenants, worden alle met succes geladen abonnementen weergegeven, samen met een foutbericht specifiek voor de tenants die zijn mislukt. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Wanneer er een update beschikbaar is, u er in Windows voor kiezen om 'Bijwerken op sluiten' te gebruiken. Wanneer deze optie is gekozen, wordt het installatieprogramma voor de update uitgevoerd nadat u Storage Explorer hebt gesloten. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Momentopname herstellen is toegevoegd aan het contextmenu van de bestandsshare-editor bij het bekijken van een momentopname voor bestandsshare. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* De knop Wachtrij wissen is nu altijd ingeschakeld. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Ondersteuning voor het aanmelden bij ADFS Azure Stack is opnieuw ingeschakeld. Azure Stack-versie 1804 of hoger is vereist. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Oplossingen
* Als u momentopnamen hebt bekeken voor een bestandsshare waarvan de naam een voorvoegsel was van een ander bestandsshare in hetzelfde opslagaccount, worden de momentopnamen voor de andere bestandsshare ook weergegeven. Dit probleem is opgelost. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Wanneer het herstellen via SAS zou het herstellen van een bestand uit een momentopname voor bestandsshare resulteren in een fout. Dit probleem is opgelost. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Bij het bekijken van momentopnamen voor een blob is de actie Momentopname promoten ingeschakeld toen de basisblob en één momentopname zijn geselecteerd. De actie is nu alleen ingeschakeld als één momentopname is geselecteerd. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Als een enkele taak (zoals het downloaden van een blob) is gestart en later is mislukt, wordt deze niet automatisch opnieuw geprobeerd totdat u een andere taak van hetzelfde type hebt gestart. Alle taken moeten nu automatisch opnieuw proberen, ongeacht het aantal taken dat u in de wachtrij hebt staan.
* Editors die zijn geopend voor nieuw gemaakte blobcontainers in GPV2- en Blob Storage-accounts hadden geen kolom Access Tier. Dit probleem is opgelost. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Een kolom Access Tier wordt soms niet weergegeven wanneer een opslagaccount of blobcontainer via SAS is gekoppeld. De kolom wordt nu altijd weergegeven, maar met een lege waarde als er geen Access Tier-set is ingesteld. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Het instellen van de toegangslaag van een nieuw geüploade blokblob is uitgeschakeld. Dit probleem is opgelost. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Als de knop 'Tab openen houden' is aangeroepen met behulp van het toetsenbord, gaat de toetsenbordfocus verloren. Nu gaat de focus naar het tabblad dat open werd gehouden. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Voor een query in de querybouwer gaf VoiceOver geen bruikbare beschrijving van de huidige operator. Het is nu meer beschrijvend. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* De pagination links voor de verschillende redacteuren waren niet beschrijvend. Ze zijn veranderd om meer beschrijvend te zijn. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* In het dialoogvenster Entiteit toevoegen kon VoiceOver niet aankondigen van welke kolom een invoerelement deel uitmaakte. De naam van de huidige kolom is nu opgenomen in de beschrijving van het element. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Keuzerondjes en selectievakjes hadden geen zichtbare rand wanneer deze waren gericht. Dit probleem is opgelost. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Bekende problemen
* Wanneer u emulators gebruikt, zoals Azure Storage Emulator of Azurite, moet u ze laten luisteren naar verbindingen op hun standaardpoorten. Anders kan Storage Explorer geen verbinding met hen maken.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op [dit probleem.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond [hier](https://github.com/Azure/azure-storage-node/issues/317)beschreven .
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Versie 1.1.0
05/09/2018

### <a name="new"></a>Nieuw
* Storage Explorer ondersteunt nu het gebruik van Azurite. Opmerking: de verbinding met Azurite is hardgecodeerd met de standaard ontwikkelingseindpunten.
* Storage Explorer ondersteunt nu Access-lagen voor Blob Only- en GPV2-opslagaccounts. Meer informatie over Toegangslagen [vindt u hier](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* Een begintijd is niet langer vereist bij het genereren van een SAS.

### <a name="fixes"></a>Oplossingen
* Het ophalen van abonnementen voor accounts van de Amerikaanse overheid werd verbroken. Dit probleem is opgelost. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* De vervaldatum voor toegangsbeleid is niet correct opgeslagen. Dit probleem is opgelost. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Bij het genereren van een SAS-URL voor een item in een container werd de naam van het item niet aan de URL toegevoegd. Dit probleem is opgelost. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Bij het maken van een SAS zijn vervaldatums die in het verleden zijn, soms de standaardwaarde. Dit was te wijten aan Storage Explorer met behulp van de laatst gebruikte start-en vervaldatum als standaardwaarden. Elke keer dat u het SAS-dialoogvenster opent, wordt er een nieuwe set standaardwaarden gegenereerd. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Bij het kopiëren tussen opslagaccounts wordt een 24-uurs SAS gegenereerd. Als de kopie duurde meer dan 24 uur, dan is de kopie zou mislukken. We hebben de SAS's verhoogd tot de laatste 1 week om de kans op een kopie niet als gevolg van een verlopen SAS te verminderen. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Voor sommige activiteiten te klikken op "Annuleren" zou niet altijd werken. Dit probleem is opgelost. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Voor sommige activiteiten was de overdrachtssnelheid verkeerd. Dit probleem is opgelost. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* De spelling van 'Vorige' in het menu Weergave klopte niet. Het is nu goed gespeld. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* De laatste pagina van de Windows-installer had een "Volgende" knop. Het is gewijzigd in een "Finish" knop. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Tabfocus was niet zichtbaar voor knoppen in dialoogvensters bij het gebruik van het thema HC Zwart. Het is nu zichtbaar. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* De behuizing van 'Automatisch oplossen' voor acties in het activiteitenlogboek was verkeerd. Het is nu correct. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Wanneer u een entiteit uit een tabel verwijderde, wordt in het dialoogvenster een foutpictogram weergegeven. Het dialoogvenster maakt nu gebruik van een waarschuwingspictogram. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Bekende problemen
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, u zich mogelijk niet aanmelden. Als u het probleem wilt omzeilen, verwijdert u de inhoud van ~/. IdentityService/AadConfiguraties. Als u dit doet, u geen commentaar geven op [dit probleem.](https://github.com/Microsoft/AzureStorageExplorer/issues/97)
* Azurite heeft nog niet alle Storage API's volledig geïmplementeerd. Hierdoor kunnen er onverwachte fouten of gedrag optreden bij het gebruik van Azurite voor ontwikkelingsopslag.
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Uploaden vanuit uw OneDrive-map werkt niet vanwege een bug in NodeJS. De bug is opgelost, maar nog niet geïntegreerd in Electron.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond [hier](https://github.com/Azure/azure-storage-node/issues/317)beschreven .
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Versie 1.0.0
04/16/2018

### <a name="new"></a>Nieuw
* Verbeterde verificatie waarmee Storage Explorer dezelfde accountwinkel kan gebruiken als Visual Studio 2017. Als u deze functie wilt gebruiken, moet u opnieuw inloggen op uw accounts en uw gefilterde abonnementen opnieuw instellen.
* Voor Azure Stack-accounts die worden ondersteund door AAD, haalt Storage Explorer azure stack-abonnementen nu op wanneer 'Target Azure Stack' is ingeschakeld. U hoeft niet langer een aangepaste inlogomgeving te maken.
* Er zijn verschillende snelkoppelingen toegevoegd om snellere navigatie mogelijk te maken. Deze omvatten het omdraaien van verschillende panelen en het verplaatsen tussen redacteuren. Zie het menu Weergave voor meer informatie.
* Storage Explorer feedback leeft nu op GitHub. U onze problemenpagina bereiken door linksonder op de [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues)knop Feedback te klikken of door naar. Voel je vrij om suggesties te doen, problemen te melden, vragen te stellen of een andere vorm van feedback achter te laten.
* Als u tls/SSL-certificaatproblemen tegenkomt en het gewraakte certificaat niet vinden, u `--ignore-certificate-errors` Storage Explorer nu starten vanaf de opdrachtregel met de vlag. Wanneer u met deze vlag wordt gestart, negeert Storage Explorer TLS/SSL-certificaatfouten.
* Er is nu een 'Download'-optie in het contextmenu voor blob- en bestandsitems.
* Verbeterde toegankelijkheid en ondersteuning voor schermlezers. Als u vertrouwt op toegankelijkheidsfuncties, raadpleegt u onze [toegankelijkheidsdocumentatie](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) voor meer informatie.
* Storage Explorer maakt nu gebruik van Electron 1.8.3

### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken
* Storage Explorer is overgestapt op een nieuwe verificatiebibliotheek. Als onderdeel van de overstap naar de bibliotheek moet u opnieuw inloggen op uw accounts en uw gefilterde abonnementen opnieuw instellen
* De methode die wordt gebruikt om gevoelige gegevens te versleutelen, is gewijzigd. Dit kan ertoe leiden dat sommige van uw Quick Access-items opnieuw moeten worden toegevoegd en/of dat sommige van u aan u gekoppelde resources opnieuw moeten worden gekoppeld.

### <a name="fixes"></a>Oplossingen
* Sommige gebruikers achter proxy's zouden groepsblob-uploads of downloads laten onderbreken door een foutmelding 'Niet in staat om op te lossen'. Dit probleem is opgelost.
* Als aanmelden nodig was tijdens het gebruik van een directe koppeling, verschijnt er een leeg dialoogvenster als u op de prompt 'Aanmelden' klikt. Dit probleem is opgelost.
* Op Linux, als Storage Explorer niet kan starten vanwege een GPU-procescrash, wordt u nu op de hoogte gebracht van de crash, wordt verteld dat u de schakelaar '--disable-gpu' moet gebruiken en Storage Explorer wordt automatisch opnieuw opgestart met de ingeschakelde schakelaar.
* Ongeldig toegangsbeleid was moeilijk te identificeren in het dialoogvenster Toegangsbeleid. Ongeldige id's van het toegangsbeleid worden nu in het rood beschreven voor meer zichtbaarheid.
* Het activiteitenlogboek zou soms grote gebieden van witruimte tussen de verschillende delen van een activiteit hebben. Dit probleem is opgelost.
* Als u in de tabelqueryeditor een tijdstempelclausule in een ongeldige status hebt achtergelaten en vervolgens een andere clausule probeerde te wijzigen, wordt de editor bevroren. De editor zal nu de tijdstempelclausule herstellen naar de laatste geldige status wanneer een wijziging in een andere clausule wordt gedetecteerd.
* Als u pauzeerde tijdens het typen van uw zoekopdracht in de structuurweergave, begint de zoekopdracht en wordt de focus gestolen uit het tekstvak. Nu moet u expliciet beginnen met zoeken door op de knop 'Enter' te drukken of door op de zoekknop voor de start te klikken.
* De opdracht 'Gedeelde toegangshandtekening' worden soms uitgeschakeld wanneer u met de rechtermuisknop op een bestand in een bestandsshare klikt. Dit probleem is opgelost.
* Als het resourcetreeknooppunt met focus tijdens het zoeken is uitgefilterd, u niet in de bronstructuur worden weergegeven en de pijltoetsen gebruiken om door de resourcestructuur te navigeren. Als het knooppunt van de gerichte resourcestructuur nu verborgen is, wordt het eerste knooppunt in de resourcestructuur automatisch gericht.
* Een extra scheidingsteken zou soms zichtbaar zijn in de werkbalk van de editor. Dit probleem is opgelost.
* Het broodkruimeltekstvak zou soms overlopen. Dit probleem is opgelost.
* De blob- en bestandsshare-editors worden soms voortdurend vernieuwd wanneer ze veel bestanden tegelijk uploaden. Dit probleem is opgelost.
* De functie 'Mapstatistieken' had geen zin in de weergave Momentopnamen voor bestandsshare. Het is nu uitgeschakeld.
* Op Linux is het menu Bestand niet weergegeven. Dit probleem is opgelost.
* Bij het uploaden van een map naar een bestandsshare is standaard alleen de inhoud van de map geüpload. Het standaardgedrag is nu om de inhoud van de map te uploaden naar een overeenkomende map in de bestandsshare.
* Het bestellen van knoppen in verschillende dialogen was omgekeerd. Dit probleem is opgelost.
* Verschillende beveiligingsgerelateerde oplossingen.

### <a name="known-issues"></a>Bekende problemen
* In zeldzame gevallen kan de boomfocus vast komen te zitten op Quick Access. Als u de focus wilt ontsteken, u Alles vernieuwen.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Versie 0.9.6
02/28/2018

### <a name="fixes"></a>Oplossingen
* Een probleem verhinderde dat verwachte blobs/bestanden in de editor worden vermeld. Dit probleem is opgelost.
* Een probleem heeft ervoor gezorgd dat u hebt geschakeld door te schakelen tussen momentopnameweergaven om items onjuist weer te geven. Dit probleem is opgelost.

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer biedt geen ondersteuning voor ADFS-accounts.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond [hier](https://github.com/Azure/azure-storage-node/issues/317)beschreven .
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* In het deelvenster Accountinstellingen kan worden weergegeven dat u referenties opnieuw moet invoeren om abonnementen te filteren.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Versie 0.9.5
02/06/2018

### <a name="new"></a>Nieuw

* Ondersteuning voor momentopnamen van bestandsshares:
    * Maak en beheer momentopnamen voor uw bestandsshares.
    * Schakel tijdens het verkennen eenvoudig van weergave tussen momentopnamen van uw bestandsshares.
    * Herstel eerdere versies van uw bestanden.
* Voorbeeld van ondersteuning voor Azure Data Lake Store:
    * Maak verbinding met uw ADLS-bronnen via meerdere accounts.
    * Maak verbinding met ADLS-bronnen en deel deze met Behulp van ADL-URI's.
    * Voer opnieuw basisbestands-/mapbewerkingen uit.
    * Losse mappen vastmaken aan Snelle toegang.
    * Mapstatistieken weergeven.

### <a name="fixes"></a>Oplossingen
* Verbeteringen in de prestaties opstart.
* Verschillende bug fixes.

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer biedt geen ondersteuning voor ADFS-accounts.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* In het deelvenster Accountinstellingen kan worden weergegeven dat u referenties opnieuw moet invoeren om abonnementen te filteren.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Versie 0.9.4 en 0.9.3
01/21/2018

### <a name="new"></a>Nieuw
* Uw bestaande Storage Explorer-venster wordt opnieuw gebruikt wanneer:
    * Directe koppelingen openen die zijn gegenereerd in Storage Explorer.
    * Storage Explorer openen vanuit portal.
    * Storage Explorer openen vanuit de Azure Storage VS Code-extensie (binnenkort beschikbaar).
* Added mogelijkheid to open a new Storage Explorer window from within Storage Explorer.
    * Voor Windows is er een optie 'Nieuw venster' onder Bestandsmenu en in het contextmenu van de taakbalk.
    * Voor Mac is er een optie 'Nieuw venster' onder App-menu.

### <a name="fixes"></a>Oplossingen
* Een beveiligingsprobleem opgelost. Upgrade zo snel mogelijk naar 0.9.4.
* Oude activiteiten waren niet adequaat schoongemaakt. Dit had invloed op de prestaties van langlopende taken. Ze worden nu correct opgeruimd.
* Acties waarbij grote aantallen bestanden en mappen betrokken zouden af en toe leiden tot Opslag Explorer te bevriezen. Aanvragen voor Azure voor bestandsshares worden nu beperkt om het gebruik van systeembronnen te beperken.

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer biedt geen ondersteuning voor ADFS-accounts.
* Sneltoetsen voor 'View Explorer' en 'Accountbeheer weergeven' moeten respectievelijk Ctrl/Cmd+Shift+E en Ctrl/Cmd+Shift+A zijn.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* In het deelvenster Accountinstellingen kan worden weergegeven dat u referenties opnieuw moet invoeren om abonnementen te filteren.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Versie 0.9.2
11/01/2017

### <a name="hotfixes"></a>Hotfixes
* Onverwachte gegevenswijzigingen waren mogelijk bij het bewerken van De waarden bewerken van Datumtijd voor tabelentiteiten, afhankelijk van de lokale tijdzone. De editor maakt nu gebruik van een plain text box, met nauwkeurige, consistente controle over Edm.DateTime waarden.
* Het uploaden/downloaden van een groep blobs wanneer deze is gekoppeld met naam en sleutel, zou niet starten. Dit probleem is opgelost.
* Voorheen zou Storage Explorer u alleen vragen om een verouderd account opnieuw te verifiëren als een of meer van de abonnementen van het account zijn geselecteerd. Nu vraagt Storage Explorer u zelfs als het account volledig is uitgefilterd.
* Het domein eindpunten voor Azure US Government was verkeerd. Het is opgelost.
* De knop Van toepassing in het deelvenster Accounts beheren was soms moeilijk te klikken. Dit mag niet meer gebeuren.

### <a name="new"></a>Nieuw
* Voorbeeld van ondersteuning voor Azure Cosmos DB:
    * [Online documentatie](./cosmos-db/storage-explorer.md)
    * Databases en verzamelingen maken
    * Gegevens verwerken
    * Documenten opvragen, maken of verwijderen
    * Opgeslagen procedures, door de gebruiker gedefinieerde functies of triggers bijwerken
    * Verbindingstekenreeksen gebruiken om verbinding te maken met uw databases en deze te beheren
* Verbeterde de prestaties van het uploaden / downloaden van veel kleine blobs.
* Added a "Retry All" action if there are failures in a blob upload group or blob download group.
* Storage Explorer pauzeert nu de iteratie tijdens het uploaden/downloaden van blobs als wordt gedetecteerd dat uw netwerkverbinding is verbroken. U de iteratie vervolgens hervatten zodra de netwerkverbinding is hersteld.
* Toegevoegd de mogelijkheid om "Allsluiten", "Anderen sluiten", en "Sluiten" tabbladen via context menu.
* Storage Explorer maakt nu gebruik van native dialogen en native contextmenu's.
* Storage Explorer is nu toegankelijker. Verbeteringen zijn onder andere:
    * Verbeterde ondersteuning voor schermlezers, voor NVDA op Windows en voor VoiceOver op Mac
    * Verbeterde thema's met een hoog contrast
    * Toetsenbordtabben- en toetsenbordfocuscorrecties

### <a name="fixes"></a>Oplossingen
* Als u een blob met een ongeldige Windows-bestandsnaam hebt geprobeerd te openen of downloaden, mislukt de bewerking. Storage Explorer detecteert nu of een blobnaam ongeldig is en vraagt of u deze wilt coderen of de blob wilt overslaan. Storage Explorer detecteert ook of een bestandsnaam lijkt te zijn gecodeerd en vraagt u of u deze wilt decoderen voordat u deze uploadt.
* Tijdens het uploaden van blobs zou de editor voor de doelblobcontainer soms niet goed worden vernieuwd. Dit probleem is opgelost.
* De ondersteuning voor verschillende vormen van verbindingstekenreeksen en SAS-URL's is achteruitgegaan. We hebben alle bekende problemen opgelost, maar stuur feedback als u verdere problemen ondervindt.
* De updatemelding is voor sommige gebruikers verbroken in 0.9.0. Dit probleem is opgelost, en voor degenen die getroffen zijn door de bug, u handmatig downloaden van de nieuwste versie van Storage Explorer [hier](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer biedt geen ondersteuning voor ADFS-accounts.
* Sneltoetsen voor 'View Explorer' en 'Accountbeheer weergeven' moeten respectievelijk Ctrl/Cmd+Shift+E en Ctrl/Cmd+Shift+A zijn.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* In het deelvenster Accountinstellingen kan worden weergegeven dat u referenties opnieuw moet invoeren om abonnementen te filteren.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Versie 0.9.1 en 0.9.0
10/20/2017
### <a name="new"></a>Nieuw
* Voorbeeld van ondersteuning voor Azure Cosmos DB:
    * [Online documentatie](./cosmos-db/storage-explorer.md)
    * Databases en verzamelingen maken
    * Gegevens verwerken
    * Documenten opvragen, maken of verwijderen
    * Opgeslagen procedures, door de gebruiker gedefinieerde functies of triggers bijwerken
    * Verbindingstekenreeksen gebruiken om verbinding te maken met uw databases en deze te beheren
* Verbeterde de prestaties van het uploaden / downloaden van veel kleine blobs.
* Added a "Retry All" action if there are failures in a blob upload group or blob download group.
* Storage Explorer pauzeert nu de iteratie tijdens het uploaden/downloaden van blobs als wordt gedetecteerd dat uw netwerkverbinding is verbroken. U de iteratie vervolgens hervatten zodra de netwerkverbinding is hersteld.
* Toegevoegd de mogelijkheid om "Allsluiten", "Anderen sluiten", en "Sluiten" tabbladen via context menu.
* Storage Explorer maakt nu gebruik van native dialogen en native contextmenu's.
* Storage Explorer is nu toegankelijker. Verbeteringen zijn onder andere:
    * Verbeterde ondersteuning voor schermlezers, voor NVDA op Windows en voor VoiceOver op Mac
    * Verbeterde thema's met een hoog contrast
    * Toetsenbordtabben- en toetsenbordfocuscorrecties

### <a name="fixes"></a>Oplossingen
* Als u een blob met een ongeldige Windows-bestandsnaam hebt geprobeerd te openen of downloaden, mislukt de bewerking. Storage Explorer detecteert nu of een blobnaam ongeldig is en vraagt of u deze wilt coderen of de blob wilt overslaan. Storage Explorer detecteert ook of een bestandsnaam lijkt te zijn gecodeerd en vraagt u of u deze wilt decoderen voordat u deze uploadt.
* Tijdens het uploaden van blobs zou de editor voor de doelblobcontainer soms niet goed worden vernieuwd. Dit probleem is opgelost.
* De ondersteuning voor verschillende vormen van verbindingstekenreeksen en SAS-URL's is achteruitgegaan. We hebben alle bekende problemen opgelost, maar stuur feedback als u verdere problemen ondervindt.
* De updatemelding is voor sommige gebruikers verbroken in 0.9.0. Dit probleem is opgelost, en voor degenen die getroffen zijn door de bug, u handmatig downloaden van de nieuwste versie van Storage Explorer [hier](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer biedt geen ondersteuning voor ADFS-accounts.
* Sneltoetsen voor 'View Explorer' en 'Accountbeheer weergeven' moeten respectievelijk Ctrl/Cmd+Shift+E en Ctrl/Cmd+Shift+A zijn.
* Wanneer u Azure Stack target, kan het uploaden van bepaalde bestanden als toevoegende blobs mislukken.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit komt omdat we gebruik maken van de filter te annuleren werk rond hier beschreven.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* In het deelvenster Accountinstellingen kan worden weergegeven dat u referenties opnieuw moet invoeren om abonnementen te filteren.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* De Electron shell gebruikt door Storage Explorer heeft problemen met een aantal GPU (grafische verwerkingseenheid) hardware versnelling. Als Storage Explorer een leeg (leeg) hoofdvenster weergeeft, u proberen Storage Explorer vanaf `--disable-gpu` de opdrachtregel te starten en GPU-versnelling uit te schakelen door de schakelaar toe te voegen:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Versie 0.8.16
8/21/2017

### <a name="new"></a>Nieuw
* Wanneer u een blob opent, vraagt Storage Explorer u het gedownloade bestand te uploaden als een wijziging wordt gedetecteerd
* Verbeterde aanmeldingservaring voor Azure Stack
* Verbeterde de prestaties van het uploaden / downloaden van veel kleine bestanden op hetzelfde moment


### <a name="fixes"></a>Oplossingen
* Voor sommige blobtypen zou de keuze om te 'vervangen' tijdens een uploadconflict soms resulteren in het opnieuw starten van de upload.
* In versie 0.8.15, uploads zou soms kraam op 99%.
* Bij het uploaden van bestanden naar een bestandsshare, als u ervoor kiest om te uploaden naar een map die nog niet bestond, zou uw upload mislukken.
* Storage Explorer genereerde ten onrechte tijdstempels voor handtekeningen voor gedeelde toegang en tabelquery's.


### <a name="known-issues"></a>Bekende problemen
* Het gebruik van een naam en sleutelverbindingstekenreeks werkt momenteel niet. Het zal worden vastgesteld in de volgende release. Tot die tijd u gebruik maken van hechten met naam en sleutel.
* Als u een bestand probeert te openen met een ongeldige Windows-bestandsnaam, leidt de download tot een fout van het bestand dat niet is gevonden.
* Nadat u op 'Annuleren' hebt geklikt op een taak, kan het even duren voordat die taak is geannuleerd. Dit is een beperking van de Azure Storage Node-bibliotheek.
* Nadat u een blob-upload hebt voltooid, wordt het tabblad waarmee de upload is gestart, vernieuwd. Dit is een verandering ten opzichte van eerder gedrag, en zal ook leiden tot u terug te worden genomen naar de wortel van de container waarin u zich bevindt.
* Als u het verkeerde PIN/Smartcard-certificaat kiest, moet u opnieuw worden opgestart om Storage Explorer die beslissing te laten vergeten.
* In het deelvenster Accountinstellingen kan worden weergegeven dat u referenties opnieuw moet invoeren om abonnementen te filteren.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* Voor gebruikers op Ubuntu 14.04 moet u ervoor zorgen dat GCC up-to-date is - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17.04 moet u GConf installeren - dit kan worden gedaan door de volgende opdrachten uit te voeren en vervolgens uw machine opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Versie 0.8.14
06/22/2017

### <a name="new"></a>Nieuw

* Updated Electron version to 1.7.2 to take to take van several critical security updates Updated Electron version to 1.7.2 to take to take of several critical security updates Updated Electron version to 1.7.2 to take to take of several critical security updates Updated Electron version to 1
* U nu snel toegang krijgen tot de online handleiding voor probleemoplossing in het Help-menu
* [Handleiding voor][2] probleemoplossing voor Storage Explorer
* [Instructies][3] voor het maken van verbinding met een Azure Stack-abonnement

### <a name="known-issues"></a>Bekende problemen

* Knoppen in het dialoogvenster mapbevestiging verwijderen registreren zich niet met muisklikken op Linux. werken is het gebruik van de Enter-toets
* Als u kiest voor de verkeerde PIN / Smartcard certificaat dan moet u opnieuw opstarten om Storage Explorer vergeet de beslissing
* Het hebben van meer dan 3 groepen blobs of bestanden die tegelijkertijd worden geüpload, kan fouten veroorzaken
* Het deelvenster Accountinstellingen kan aantonen dat u referenties opnieuw moet invoeren om abonnementen te filteren
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandsshares ondersteunt, wordt er nog steeds een knooppunt voor bestandsshares weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* Ubuntu 14.04 installatie moet gcc versie bijgewerkt of opgewaardeerd - stappen om te upgraden zijn hieronder:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Versie 0.8.13
12/05/2017

#### <a name="new"></a>Nieuw

* [Handleiding voor][2] probleemoplossing voor Storage Explorer
* [Instructies][3] voor het maken van verbinding met een Azure Stack-abonnement

#### <a name="fixes"></a>Oplossingen

* Opgelost: Het uploaden van bestanden had een grote kans op het veroorzaken van een geheugenfout
* Opgelost: U zich nu aanmelden met pin/smartcard
* Opgelost: Open in Portal werkt nu met Azure China 21Vianet, Azure Germany, Azure US Government en Azure Stack
* Opgelost: Tijdens het uploaden van een map naar een blobcontainer, zou er soms een fout met 'Illegale bewerking' optreden
* Opgelost: Alles selecteren is uitgeschakeld tijdens het beheren van momentopnamen
* Opgelost: de metagegevens van de basisblob kunnen worden overschreven na het bekijken van de eigenschappen van de momentopnamen

#### <a name="known-issues"></a>Bekende problemen

* Als u kiest voor de verkeerde PIN / Smartcard certificaat dan moet u opnieuw opstarten om Storage Explorer vergeet de beslissing
* Tijdens het in- of uitzoomen kan het zoomniveau tijdelijk worden gereset naar het standaardniveau
* Het hebben van meer dan 3 groepen blobs of bestanden die tegelijkertijd worden geüpload, kan fouten veroorzaken
* Het deelvenster Accountinstellingen kan aantonen dat u referenties opnieuw moet invoeren om abonnementen te filteren
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* Ubuntu 14.04 installatie moet gcc versie bijgewerkt of opgewaardeerd - stappen om te upgraden zijn hieronder:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Versie 0.8.12 en 0.8.11 en 0.8.10
04/07/2017

#### <a name="new"></a>Nieuw

* Storage Explorer wordt nu automatisch gesloten wanneer u een update installeert vanuit de updatemelding
* Snelle toegang op hun plaats biedt een verbeterde ervaring voor het werken met uw veelgebruikte bronnen
* In de Blob Container-editor u nu zien tot welke virtuele machine een gehuurde blob behoort
* U nu het linkerzijpaneel samenvouwen
* Discovery wordt nu tegelijkertijd uitgevoerd als downloaden
* Statistieken in de blobcontainer-, bestandsshare- en tabeleditors gebruiken om de grootte van uw bron of selectie te bekijken
* U zich nu aanmelden bij azure active directory(AAD) gebaseerde Azure Stack-accounts.
* U nu archiefbestanden van meer dan 32 MB uploaden naar Premium-opslagaccounts
* Verbeterde toegankelijkheidsondersteuning
* U nu vertrouwde Base-64 gecodeerde X.509 TLS/SSL-certificaten&gt; toevoegen&gt; door naar Bewerken - SSL-certificaten - Importcertificaten te gaan

#### <a name="fixes"></a>Oplossingen

* Opgelost: na het vernieuwen van de referenties van een account, wordt de structuurweergave soms niet automatisch vernieuwd
* Opgelost: het genereren van een SAS voor emulatorwachtrijen en -tabellen zou resulteren in een ongeldige URL
* Opgelost: premium opslagaccounts kunnen nu worden uitgebreid terwijl een proxy is ingeschakeld
* Opgelost: de knop Toepassen op de pagina accountbeheer zou niet werken als u 1 of 0 accounts had geselecteerd
* Opgelost: het uploaden van blobs waarvoor conflictoplossingen nodig zijn, kan mislukken - opgelost in 0.8.11
* Opgelost: het verzenden van feedback werd verbroken in 0.8.11 - vast in 0.8.12

#### <a name="known-issues"></a>Bekende problemen

* Na een upgrade naar 0.8.10 moet u al uw referenties vernieuwen.
* Tijdens het in- of uitzoomen kan het zoomniveau tijdelijk worden gereset naar het standaardniveau.
* Het hebben van meer dan 3 groepen blobs of bestanden die tegelijkertijd worden geüpload, kan fouten veroorzaken.
* Het deelvenster Accountinstellingen kan aantonen dat u referenties opnieuw moet invoeren om abonnementen te filteren.
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging.
* Hoewel Azure Stack momenteel geen bestandenshares ondersteunt, wordt er nog steeds een bestanddeeltknooppunt weergegeven onder een gekoppeld Azure Stack-opslagaccount.
* Ubuntu 14.04 installatie moet gcc versie bijgewerkt of opgewaardeerd - stappen om te upgraden zijn hieronder:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Versie 0.8.9 en 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Nieuw

* Storage Explorer 0.8.9 downloadt automatisch de nieuwste versie voor updates.
* Hotfix: het gebruik van een portal gegenereerde SAS URI om een opslagaccount te koppelen zou resulteren in een fout.
* U nu blobmomentopnamen maken, beheren en promoten.
* U zich nu aanmelden bij Azure China 21Vianet, Azure Germany en Azure US Government accounts.
* U nu het zoomniveau wijzigen. Gebruik de opties in het menu Weergave om Intezoomen, Uitzoomen en Zoomen opnieuw instellen.
* Unicode-tekens worden nu ondersteund in metagegevens van gebruikers voor blobs en bestanden.
* Toegankelijkheidsverbeteringen.
* De releasenotes van de volgende versie kunnen worden bekeken vanuit de updatemelding. U de huidige releasenotities ook bekijken in het Menu Help.

#### <a name="fixes"></a>Oplossingen

* Opgelost: het versienummer wordt nu correct weergegeven in het Configuratiescherm op Windows
* Opgelost: zoeken is niet langer beperkt tot 50.000 knooppunten
* Opgelost: upload naar een bestandsshare dat voor altijd is gesponnen als de bestemmingsmap nog niet bestond
* Opgelost: verbeterde stabiliteit voor lange uploads en downloads

#### <a name="known-issues"></a>Bekende problemen

* Tijdens het in- of uitzoomen kan het zoomniveau tijdelijk worden gereset naar het standaardniveau.
* Quick Access werkt alleen met op abonnementen gebaseerde items. Lokale bronnen of resources die via sleutel of SAS-token zijn gekoppeld, worden niet ondersteund in deze release.
* Het kan enkele seconden duren voordat Quick Access naar de doelbron navigeert, afhankelijk van het aantal resources dat u hebt.
* Het hebben van meer dan 3 groepen blobs of bestanden die tegelijkertijd worden geüpload, kan fouten veroorzaken.

12/16/2016
### <a name="version-087"></a>Versie 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nieuw

* U aan het begin van een update kiezen hoe u conflicten oplost, sessie downloadt of kopieert in het venster Activiteiten
* De plaats boven een tabblad plaatsen om het volledige pad van de opslagbron te bekijken
* Wanneer u op een tabblad klikt, wordt het gesynchroniseerd met de locatie in het navigatiedeelvenster aan de linkerkant

#### <a name="fixes"></a>Oplossingen

* Opgelost: Storage Explorer is nu een vertrouwde app op Mac
* Opgelost: Ubuntu 14.04 wordt opnieuw ondersteund
* Opgelost: soms knippert de gebruikersinterface van het account toevoegen bij het laden van abonnementen
* Opgelost: Soms stonden niet alle opslagbronnen in het navigatiedeelvenster aan de linkerkant
* Opgelost: in het actievenster worden soms lege acties weergegeven
* Opgelost: de venstergrootte van de laatste gesloten sessie blijft nu behouden
* Opgelost: u meerdere tabbladen voor dezelfde bron openen via het contextmenu

#### <a name="known-issues"></a>Bekende problemen

* Quick Access werkt alleen met op abonnementen gebaseerde items. Lokale bronnen of resources die via sleutel of SAS-token zijn gekoppeld, worden niet ondersteund in deze release
* Het kan enkele seconden duren voordat Quick Access naar de doelbron navigeert, afhankelijk van het aantal resources dat u hebt
* Het hebben van meer dan 3 groepen blobs of bestanden die tegelijkertijd worden geüpload, kan fouten veroorzaken
* Zoekhandvatten zoeken in ongeveer 50.000 knooppunten - hierna kunnen de prestaties worden beïnvloed of kunnen er een onverwerkte uitzondering worden gemaakt
* Voor het eerst met de Storage Explorer op macOS ziet u mogelijk meerdere aanwijzingen waarin wordt gevraagd om toestemming van de gebruiker om toegang te krijgen tot sleutelhangers. We raden u aan Altijd toestaan te selecteren, zodat de prompt niet meer wordt weergegeven

11/18/2016
### <a name="version-086"></a>Versie 0.8.6

#### <a name="new"></a>Nieuw

* U nu de meest gebruikte services vastmaken aan de Quick Access voor eenvoudige navigatie
* U nu meerdere editors openen in verschillende tabbladen. Eén klik om een tijdelijk tabblad te openen; dubbelklik om een permanent tabblad te openen. U ook op het tijdelijke tabblad klikken om er een permanent tabblad van te maken
* We hebben merkbare prestatie- en stabiliteitsverbeteringen aangebracht voor uploads en downloads, vooral voor grote bestanden op snelle machines
* Lege 'virtuele' mappen kunnen nu worden gemaakt in blobcontainers
* We hebben opnieuw scoped zoeken geïntroduceerd met onze nieuwe verbeterde substring zoeken, dus je hebt nu twee opties voor het zoeken:
    * Globale zoekopdracht - voer gewoon een zoekterm in het zoektekstvak in
    * Scoped search - klik op het vergrootglaspictogram naast een knooppunt en voeg vervolgens een zoekterm toe aan het einde van het pad of klik met de rechtermuisknop en selecteer 'Zoeken vanaf hier'.
* We hebben verschillende thema's toegevoegd: Light (default), Dark, High Contrast Black en High Contrast White. Ga naar Bewerken&gt; - Thema's om de voorkeur voor thema's te wijzigen
* U blob- en bestandseigenschappen wijzigen
* We ondersteunen nu gecodeerde (base64) en ongecodeerde wachtrijberichten
* Op Linux is nu een 64-bits OS vereist. Voor deze release ondersteunen we alleen 64-bits Ubuntu 16.04.1 LTS
* We hebben ons logo bijgewerkt!

#### <a name="fixes"></a>Oplossingen

* Opgelost: Problemen met het bevriezen van het scherm
* Opgelost: verbeterde beveiliging
* Opgelost: soms kunnen dubbele bijgevoegde accounts worden weergegeven
* Opgelost: een blob met een niet-gedefinieerd inhoudstype kan een uitzondering genereren
* Opgelost: Het querypaneel openen op een lege tabel was niet mogelijk
* Opgelost: varieert bugs in zoekopdrachten
* Opgelost: het aantal geladen resources verhoogd van 50 naar 100 wanneer u op 'Meer laden' klikt
* Opgelost: Als er bij een account is aangemeld, selecteren we nu standaard alle abonnementen voor dat account

#### <a name="known-issues"></a>Bekende problemen

* Deze release van de Storage Explorer draait niet op Ubuntu 14.04
* Als u meerdere tabbladen voor dezelfde bron wilt openen, klikt u niet continu op dezelfde bron. Klik op een andere bron en ga dan terug en klik vervolgens op de oorspronkelijke bron om deze opnieuw te openen op een ander tabblad
* Quick Access werkt alleen met op abonnementen gebaseerde items. Lokale bronnen of resources die via sleutel of SAS-token zijn gekoppeld, worden niet ondersteund in deze release
* Het kan enkele seconden duren voordat Quick Access naar de doelbron navigeert, afhankelijk van het aantal resources dat u hebt
* Het hebben van meer dan 3 groepen blobs of bestanden die tegelijkertijd worden geüpload, kan fouten veroorzaken
* Zoekhandvatten zoeken in ongeveer 50.000 knooppunten - hierna kunnen de prestaties worden beïnvloed of kunnen er een onverwerkte uitzondering worden gemaakt

03/10/2016
### <a name="version-085"></a>Versie 0.8.5

#### <a name="new"></a>Nieuw

* Kan nu door portal gegenereerde SAS-sleutels gebruiken om te koppelen aan opslagaccounts en -bronnen

#### <a name="fixes"></a>Oplossingen

* Opgelost: raceconditie tijdens zoeken zorgde er soms voor dat knooppunten niet uitbreidbaar werden
* Opgelost: 'HTTP gebruiken' werkt niet wanneer u verbinding maakt met opslagaccounts met accountnaam en sleutel
* Opgelost: SAS-toetsen (speciaal door portal gegenereerde toetsen) geven een fout met 'trailing slash' terug
* Opgelost: problemen met het importeren van tabel
    * Soms werden partitiesleutel en rijsleutel omgekeerd
    * Niet in staat om 'null'-partitiesleutels te lezen

#### <a name="known-issues"></a>Bekende problemen

* Zoeken verwerkt zoeken in ongeveer 50.000 knooppunten - hierna kunnen de prestaties worden beïnvloed
* Azure Stack biedt momenteel geen ondersteuning voor bestanden, dus als u bestanden probeert uit te vouwen, wordt een fout weergegeven

09/12/2016
### <a name="version-084"></a>Versie 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nieuw

* Directe koppelingen genereren naar opslagaccounts, containers, wachtrijen, tabellen of bestandsshares voor delen en eenvoudige toegang tot uw bronnen - Windows- en Mac OS-ondersteuning
* Zoeken naar uw blobcontainers, tabellen, wachtrijen, bestandsshares of opslagaccounts in het zoekvak
* U nu clausules groeperen in de tabelquerybouwer
* Blobcontainers, bestandsshares, tabellen, blobs, blobmappen, bestanden en mappen wijzigen en kopiëren/plakken vanuit aan sAS gekoppelde accounts en containers
* Als u blobcontainers en bestandsshares opnieuw noemt en kopieert, behouden ze nu eigenschappen en metagegevens

#### <a name="fixes"></a>Oplossingen

* Opgelost: kan tabelentiteiten niet bewerken als ze booleaanse of binaire eigenschappen bevatten

#### <a name="known-issues"></a>Bekende problemen

* Zoeken verwerkt zoeken in ongeveer 50.000 knooppunten - hierna kunnen de prestaties worden beïnvloed

08/03/2016
### <a name="version-083"></a>Versie 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nieuw

* De naam van containers, tabellen, bestandsshares wijzigen
* Verbeterde ervaring met querybouwer
* Mogelijkheid om query's op te slaan en te laden
* Directe koppelingen naar opslagaccounts of containers, wachtrijen, tabellen of bestandsshares voor het delen en eenvoudig openen van uw bronnen (alleen windows - macOS-ondersteuning binnenkort!)
* Mogelijkheid om CORS-regels te beheren en te configureren

#### <a name="fixes"></a>Oplossingen

* Opgelost: Microsoft-accounts vereisen elke 8-12 uur opnieuw verificatie

#### <a name="known-issues"></a>Bekende problemen

* Soms lijkt de gebruikersinterface bevroren - het maximaliseren van het venster helpt dit probleem op te lossen
* macOS-installatie vereist mogelijk verhoogde machtigingen
* Het deelvenster Accountinstellingen kan aantonen dat u referenties opnieuw moet invoeren om abonnementen te filteren
* Als u de naam van bestandsshares, blobcontainers en tabellen hernoemt, worden geen metagegevens of andere eigenschappen in de container bewaard, zoals bestandssharequota, niveau voor openbare toegang of toegangsbeleid
* Als u de naam van blobs (afzonderlijk of in een hernoemde blobcontainer) anders noemt, worden geen momentopnamen bewaard. Alle andere eigenschappen en metagegevens voor blobs, bestanden en entiteiten blijven behouden tijdens een naamswijziging
* Het kopiëren of hernoemen van resources werkt niet binnen sas-gekoppelde accounts

07/07/2016
### <a name="version-082"></a>Versie 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nieuw

* Opslagaccounts worden gegroepeerd op abonnementen; ontwikkelingsopslag en resources die via sleutel of SAS zijn gekoppeld, worden weergegeven onder (Lokaal en bijgevoegd) knooppunt
* Afmelden bij accounts in het deelvenster Azure-accountinstellingen
* Proxy-instellingen configureren om aanmelding in te schakelen en te beheren
* Blob-leases maken en verbreken
* Blobcontainers, wachtrijen, tabellen en bestanden openen met één klik

#### <a name="fixes"></a>Oplossingen

* Opgelost: wachtrijberichten die zijn ingevoegd met .NET- of Java-bibliotheken zijn niet correct gedecodeerd vanaf base64
* Opgelost: $metrics tabellen worden niet weergegeven voor Blob Storage-accounts
* Opgelost: het knooppunt van tabellen werkt niet voor lokale (Ontwikkelings)opslag

#### <a name="known-issues"></a>Bekende problemen

* macOS-installatie vereist mogelijk verhoogde machtigingen

06/15/2016
### <a name="version-080"></a>Versie 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nieuw

* Ondersteuning voor bestandsshare: bekijken, uploaden, downloaden, kopiëren van bestanden en mappen, SAS-URI's (maken en verbinden)
* Verbeterde gebruikerservaring voor verbinding maken met opslag met SAS-URI's of accountsleutels
* Tabelqueryresultaten exporteren
* Volgorde en aanpassing van tabelkolom
* $logs blobcontainers en $metrics tabellen voor opslagaccounts weergeven met ingeschakelde statistieken
* Verbeterd export- en importgedrag, inclusief eigenschapswaardetype

#### <a name="fixes"></a>Oplossingen

* Opgelost: het uploaden of downloaden van grote blobs kan leiden tot onvolledige uploads/downloads
* Opgelost: het bewerken, toevoegen of importeren van een entiteit met een numerieke tekenreekswaarde ("1") wordt geconverteerd naar dubbele
* Opgelost: Kan het tafelknooppunt niet uitbreiden in de lokale ontwikkelingsomgeving

#### <a name="known-issues"></a>Bekende problemen

* $metrics tabellen zijn niet zichtbaar voor Blob Storage-accounts
* Wachtrijberichten die programmatisch zijn toegevoegd, worden mogelijk niet correct weergegeven als de berichten zijn gecodeerd met Base64-codering

05/17/2016
### <a name="version-07201605090"></a>Versie 0.7.20160509.0

#### <a name="new"></a>Nieuw

* Betere foutafhandeling voor app-crashes

#### <a name="fixes"></a>Oplossingen

* Fixed bug where InfoBar messages sometimes don't show up when sign-in credentials were required

#### <a name="known-issues"></a>Bekende problemen

* Tabellen: het toevoegen, bewerken of importeren van een entiteit met een eigenschap met een dubbelzinnig numerieke waarde, zoals '1' of `Edm.String`'1.0', en de gebruiker probeert deze als een , de waarde komt terug via de client-API als een Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Versie 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nieuw

* Tabelondersteuning: bewerkingen voor het weergeven, opvragen, exporteren, importeren en CRUD voor entiteiten
* Wachtrijondersteuning: berichten weergeven, toevoegen, in de wachtrij plaatsen
* SAS-URI's genereren voor opslagaccounts
* Verbinding maken met opslagaccounts met SAS-URI's
* Meldingen bijwerken voor toekomstige updates in Storage Explorer
* Bijgewerkte look en feel

#### <a name="fixes"></a>Oplossingen

* Verbeteringen in prestaties en betrouwbaarheid

### <a name="known-issues-amp-mitigations"></a>Problemen &amp; met bekende problemen

* Downloaden van grote blobbestanden werkt niet goed - we raden aan azcopy te gebruiken terwijl we dit probleem aanpakken
* Accountgegevens worden niet opgehaald of in de cache opgeslagen als de thuismap niet kan worden gevonden of niet kan worden geschreven naar
* Als we een entiteit met een dubbelzinnig numerieke waarde, zoals '1' of '1.0' toevoegen, bewerken of importeren, en `Edm.String`de gebruiker deze probeert te verzenden als een , komt de waarde terug via de client-API als een Edm.Double
* Bij het importeren van CSV-bestanden met multiline-records kunnen de gegevens worden gehakt of

02/03/2016

### <a name="version-07201601291"></a>Versie 0.7.20160129.1

#### <a name="fixes"></a>Oplossingen

* Verbeterde algehele prestaties bij het uploaden, downloaden en kopiëren van blobs

01/14/2016

### <a name="version-07201601050"></a>Versie 0.7.20160105.0

#### <a name="new"></a>Nieuw

* Linux-ondersteuning (pariteitsfuncties naar OSX)
* Blobcontainers toevoegen met de SAS-toets (Shared Access Signatures)
* Opslagaccounts toevoegen voor Azure China 21Vianet
* Opslagaccounts toevoegen met aangepaste eindpunten
* De inhoudstekst- en afbeeldingsblobs openen en weergeven
* Blob-eigenschappen en metagegevens weergeven en bewerken

#### <a name="fixes"></a>Oplossingen

* Opgelost: het uploaden of downloaden van een groot aantal blobs (500+) kan er soms voor zorgen dat de app een wit scherm heeft
* Opgelost: bij het instellen van het openbare toegangsniveau voor blobcontainers wordt de nieuwe waarde pas bijgewerkt nadat u de focus op de container opnieuw hebt ingesteld. Het dialoogvenster wordt ook altijd standaard weergegeven in 'Geen openbare toegang' en niet op de werkelijke huidige waarde.
* Betere algemene toetsenbord/toegankelijkheid en ui-ondersteuning
* De tekst tekst van broodkruimelsgeschiedenis loopt om wanneer deze lang is met witte ruimte
* SAS-dialoogvenster ondersteunt invoervalidatie
* Lokale opslag blijft beschikbaar, zelfs als gebruikersreferenties zijn verlopen
* Wanneer een geopende blobcontainer wordt verwijderd, wordt de blobverkenner aan de rechterkant gesloten

#### <a name="known-issues"></a>Bekende problemen

* Linux installeren moet gcc versie bijgewerkt of opgewaardeerd - stappen om te upgraden zijn hieronder:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Versie 0.7.2015116.0

#### <a name="new"></a>Nieuw

* macOS en Windows-versies
* Aanmelden om uw opslagaccounts te bekijken: gebruik uw Org-account, Microsoft-account, 2FA, enz.
* Lokale ontwikkelopslag (gebruik opslagemulator, alleen Windows)
* Ondersteuning voor Azure Resource Manager en Classic-bronnen
* Blobs, wachtrijen of tabellen maken en verwijderen
* Zoeken naar specifieke blobs, wachtrijen of tabellen
* De inhoud van blobcontainers verkennen
* Bekijk en navigeer door mappen
* Blobs en mappen uploaden, downloaden en verwijderen
* Blob-eigenschappen en metagegevens weergeven en bewerken
* SAS-sleutels genereren
* Sap (Stored Access Policies) beheren en maken
* Zoeken naar blobs op voorvoegsel
* Sleep 'n drop-bestanden om te uploaden of te downloaden

#### <a name="known-issues"></a>Bekende problemen

* Wanneer blobcontainer-toegangsniveau wordt ingesteld, wordt de nieuwe waarde pas bijgewerkt nadat u de focus op de container opnieuw hebt ingesteld
* Wanneer u het dialoogvenster opent om het openbare toegangsniveau in te stellen, wordt altijd 'Geen openbare toegang' weergegeven als standaardwaarde en niet als de werkelijke huidige waarde
* Kan de naam van gedownloade blobs niet wijzigen
* Activiteitslogboekvermeldingen worden soms 'vastgelopen' in een lopende status wanneer er een fout optreedt en wordt de fout niet weergegeven
* Soms crasht of wordt volledig wit wanneer het proberen om een groot aantal blobs te uploaden of te downloaden
* Soms werkt het annuleren van een kopieerbewerking niet
* Als u tijdens het maken van een container (blob/wachtrij/tabel) een ongeldige naam invoert en doorgaat met het maken van een andere containertype, u geen focus instellen op het nieuwe type
* Kan geen nieuwe map maken of de naam van map wijzigen




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
