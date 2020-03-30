---
title: Meer informatie over de nieuwste Azure Guest OS Releases | Microsoft Documenten
description: Het laatste persbericht en SDK-compatibiliteit voor Azure Cloud Services Guest OS.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/26/2020
ms.author: raiye
ms.openlocfilehash: f265faef4e6520f5c74fce9a13b3d81118707025
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371127"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure Guest OS-releases en SDK-compatibiliteitsmatrix
Biedt u up-to-date informatie over de nieuwste Azure Guest OS-releases voor Cloud Services. Met deze informatie u uw upgradepad plannen voordat een gastbesturingssysteem is uitgeschakeld. Als u uw rollen configureert om *automatische* updates van het gastbesturingssysteem te gebruiken zoals beschreven in [azure guest os-update-instellingen,][Azure Guest OS Update Settings]is het niet van vitaal belang dat u deze pagina leest.

> [!IMPORTANT]
> Deze pagina is van toepassing op web- en werknemersrollen van Cloud Services, die bovenop een gastbesturingssysteem worden uitgevoerd. Het **is niet van toepassing op** IaaS Virtual Machines.
>
>


> [!TIP]
>  Abonneer u op de [RSS-feed voor gastapparatenvan os update] om de meest tijdige melding te ontvangen over alle wijzigingen in gastbesturingssysteem.
>
>

> [!IMPORTANT]
> Alleen de laatste 2 versies van het gastbesturingssysteem worden ondersteund en beschikbaar in de Azure-portal.
>
>

Weet u niet zeker hoe u uw gastbesturingssysteem bijwerken? Kijk [hier eens][cloud updates] naar.

## <a name="news-updates"></a>Nieuwsupdates

###### <a name="march-5-2020"></a>**5 maart 2020**
De Februari Guest OS heeft vrijgegeven. 

###### <a name="january-24-2020"></a>**24 januari 2020**
De Januari Guest OS heeft vrijgegeven. 

###### <a name="january-8-2020"></a>**8 januari 2020**
De December Guest OS heeft vrijgegeven.

###### <a name="december-5-2019"></a>**5 december 2019**
De November Guest OS heeft vrijgegeven.

###### <a name="november-1-2019"></a>**1 november 2019**
De Oktober Guest OS heeft vrijgegeven.

###### <a name="october-7-2019"></a>**7 oktober 2019**
De September Guest OS heeft vrijgegeven.

###### <a name="september-4-2019"></a>**4 september 2019**
De augustus Guest OS heeft vrijgegeven.

###### <a name="july-26-2019"></a>**26 juli 2019**
De Juli Guest OS heeft vrijgegeven.

###### <a name="july-8-2019"></a>**8 juli 2019**
De Juni Guest OS heeft vrijgegeven.

###### <a name="june-6-2019"></a>**6 juni 2019**
De May Guest OS heeft vrijgegeven.

###### <a name="may-7-2019"></a>**7 mei 2019**
De April Guest OS heeft vrijgegeven.

###### <a name="march-26-2019"></a>**26 maart 2019**
De March Guest OS heeft vrijgegeven.

###### <a name="march-12-2019"></a>**12 maart 2019**
De Februari Guest OS heeft vrijgegeven.

###### <a name="february-5-2019"></a>**5 februari 2019**
De Januari Guest OS heeft vrijgegeven.

###### <a name="january-24-2019"></a>**24 januari 2019**
Family 6 Guest OS (Windows Server 2019) is uitgebracht.

###### <a name="january-7-2019"></a>**7 januari 2019**
De December Guest OS heeft vrijgegeven.

###### <a name="december-14-2018"></a>**14 december 2018**
De November Guest OS heeft vrijgegeven.

###### <a name="november-8-2018"></a>**8 november 2018**
De Oktober Guest OS heeft vrijgegeven.

###### <a name="october-12-2018"></a>**12 oktober 2018**
De September Guest OS heeft vrijgegeven.

## <a name="releases"></a>Releases

## <a name="family-6-releases"></a>Familie 6 releases
**Windows Server 2019**

.NET Framework geïnstalleerd: 3.5, 4.7.2

> [!NOTE]
> De Windows Azure SDK voor .NET - 3.0 kan [hier][Windows Azure SDK]worden gedownload.
>
>Installatiestappen:
>1. Verwijder oudere versies van MicrosoftAzureAuthoringTools*.msi
>2. De [Azure SDK voor .NET installeren - 3.0][Windows Azure SDK]
>3. Start uw machine opnieuw op
>4. Een nieuw Cloud Service-project maken en één werknemersrol toevoegen
>5. De OS-familie wijzigen in 6 en een pakket bouwen
>6. Het pakket implementeren naar Azure met behulp van de Azure-portal of Visual Studio
>
>Guest OS Family 6 release dwingt TLS 1.2 af door TLS 1.0 en 1.1 expliciet uit te schakelen en een specifieke set cipher suites te definiëren. Meer [weten?]


| Configuratietekenreeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
|  WA-GUEST-OS-6.16_202002-01  |  5 maart 2020  |  Post 6.18  |  
|  WA-GUEST-OS-6.15_202001-01  |  24 januari 2020  |  Post 6.17  |  
|~~WA-GUEST-OS-6.14_201912-01~~| 8 januari 2020 | 5 maart 2020 |  
|~~WA-GUEST-OS-6.13_201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA-GUEST-OS-6.12_201910-01~~| 1 november 2019 | 8 januari 2020 |  
|~~WA-GUEST-OS-6.11_201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-6.10_201908-01~~| 4 augustus 2019 | 1 november 2019  |  
|~~WA-GUEST-OS-6.9_201907-0~~|26 juli 2019 | 7 oktober 2019 |
|~~WA-GUEST-OS-6.8_201906-01~~|8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-6.7_201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-6.6_201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-6.5_201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-6.4_201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-6.3_201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-6.2_201812-01~~ |24 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 januari 2019 |5 februari 2019 |

## <a name="family-5-releases"></a>Familie 5 releases
**Windows Server 2016**

.NET Framework geïnstalleerd: 3.5, 4.6

> [!NOTE]
> Het RDP-wachtwoord voor OS-familie 5 moet minimaal 10 tekens bevatten.
>


| Configuratietekenreeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
|  WA-GUEST-OS-5.40_202002-01  |  5 maart 2020  |  Post 5.42  |  
|  WA-GUEST-OS-5.39_202001-01  |  24 januari 2020  |  Post 5.41  |  
|~~WA-GUEST-OS-5.38_201912-01~~| 8 januari 2020 | 5 maart 2020 |  
|~~WA-GUEST-OS-5.37_201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA-GUEST-OS-5.36_201910-01~~| 1 november 2019 | 8 januari 2020 |  
|~~WA-GUEST-OS-5.35_201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-5.34_201908-01~~|  4 augustus 2019  | 1 november 2019 |  
|~~WA-GUEST-OS-5.33_201907-01~~| 26 juli 2019 | 7 oktober 2019 |  
|~~WA-GUEST-OS-5.32_201906-01~~|8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-5.31_201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-5.30_201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-5.29_201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-5.28_201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-5.27_201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-5.26_201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-4-releases"></a>Familie 4 releases
**Windows Server 2012 R2**

.NET Framework geïnstalleerd: 3.5, 4.5.1, 4.5.2

| Configuratietekenreeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
|  WA-GUEST-OS-4.75_202002-01  |  5 maart 2020  |  Post 4.77  |  
|  WA-GUEST-OS-4.74_202001-01  |  24 januari 2020  |  Post 4.76  |  
|~~WA-GUEST-OS-4.73_201912-01~~| 8 januari 2020 | 5 maart 2020 |  
|~~WA-GUEST-OS-4.72_201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA-GUEST-OS-4.71_201910-01~~| 1 november 2019 | 8 januari 2020 |  
|~~WA-GUEST-OS-4.70_201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-4.69_201908-01~~| 4 augustus 2019 | 1 november 2019 |  
|~~WA-GUEST-OS-4.68_201907-01~~| 26 juli 2019  | 7 oktober 2019 |
|~~WA-GUEST-OS-4.67_201906-01~~| 8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-4.66_201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-4.65_201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-4.64_201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-4.63_201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-4.62_201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-4.61_201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-3-releases"></a>Familie 3 releases
**Windows Server 2012**

.NET Framework geïnstalleerd: 3.5, 4.5

| Configuratietekenreeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
|  WA-GUEST-OS-3.82_202002-01  |  5 maart 2020  |  Post 3.84  |  
|  WA-GUEST-OS-3.81_202001-01  |  24 januari 2020  |  Post 3.83  |  
|~~WA-GUEST-OS-3.80_201912-01~~| 8 januari 2020 | 5 maart 2020 |  
|~~WA-GUEST-OS-3.79_201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA-GUEST-OS-3.78_201910-01~~| 1 november 2019 | 8 januari 2020 |  
|~~WA-GUEST-OS-3.77_201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-3.76_201908-01~~|  4 augustus 2019  |  1 november 2019  |  
|~~WA-GUEST-OS-3.75_201907-01~~| 26 juli 2019 | 7 oktober 2019 |
|~~WA-GUEST-OS-3.74_201906-01~~| 8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-3.73_201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-3.72_201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-3.71_201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-3.70_201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-3.69_201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-3.68_201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-2-releases"></a>Familie 2 releases
**Windows Server 2008 R2 SP1**

.NET Framework geïnstalleerd: 3.5 (inclusief 2.0 en 3.0), 4.5

| Configuratietekenreeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
|  WA-GUEST-OS-2.95_202002-01  |  5 maart 2020  |  Post 2.97  |  
|  WA-GUEST-OS-2.94_202001-01  |  24 januari 2020  |  Post 2.96  |  
|~~WA-GUEST-OS-2.93_201912-01~~| 8 januari 2020 | 5 maart 2020 |  
|~~WA-GUEST-OS-2.92_201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA-GUEST-OS-2.91_201910-01~~| 1 november 2019 | 8 januari 2020 |  
|~~WA-GUEST-OS-2.90_201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-2.89_201908-01~~| 4 augustus 2019 | 1 november 2019 |  
|~~WA-GUEST-OS-2.88_201907-01~~| 26 juli 2019 | 7 oktober 2019 |
|~~WA-GUEST-OS-2.87_201906-01~~|8 juli 2019 | 4 augustus 2019 |
|~~WA-GUEST-OS-2.86_201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-2.85_201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-2.84_201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-2.83_201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-2.82_201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="msrc-patch-updates"></a>MSRC patch updates
De lijst met patches die zijn opgenomen bij elke maandelijkse Guest OS release is [hier][patches]beschikbaar.

## <a name="sdk-support"></a>SDK-ondersteuning
Hoewel het [pensioenbeleid voor de Azure SDK][retire policy sdk] aangeeft dat alleen versies boven 2.2 worden ondersteund, u met specifieke Gast-OS-families eerdere versies gebruiken. Gebruik altijd de nieuwste ondersteunde SDK.

| Gast OS-familie | Compatibele SDK-versies |
| --- | --- |
| 6 |Versie 2.9.6+ |
| 5 |Versie 2.9.5.1+ |
| 4 |Versie 2.1+ |
| 3 |Versie 1.8+ |
| 2 |Versie 1.3+ |
| 1 |Versie 1.0+ |

## <a name="guest-os-release-information"></a>Informatie over de release van gast-besturingssysteem
Er zijn drie datums die belangrijk zijn voor gastosreleases: **releasedatum,** **uitgeschakelde** datum en **vervaldatum.** Een gastbesturingssysteem wordt als beschikbaar beschouwd wanneer het zich in de portal bevindt en kan worden geselecteerd als het doelgast-besturingssysteem. Wanneer een gastbesturingssysteem de **uitgeschakelde** datum bereikt, wordt het uit Azure verwijderd. Elke Cloud Service die is gericht op dat gastbesturingssysteem werkt echter nog steeds als normaal.

Het venster tussen de **uitgeschakelde** datum en de **vervaldatum** biedt u een buffer om eenvoudig over te schakelen van het ene gastbesturingssysteem naar een nieuwer. Als u *automatisch als* gastbesturingssysteem gebruikt, bent u altijd op de nieuwste versie en hoeft u zich geen zorgen te maken dat deze afloopt.

Wanneer de **vervaldatum** verstrijkt, wordt elke Cloud Service die dat gastbesturingssysteem nog steeds gebruikt, gestopt, verwijderd of gedwongen om te upgraden. U meer lezen over het pensioenbeleid [hier][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Uitleg gastOS-familieversie
De Guest OS-families zijn gebaseerd op uitgebrachte versies van Microsoft Windows Server. Het gastbesturingssysteem is het onderliggende besturingssysteem waarop Azure Cloud Services wordt uitgevoerd. Elk gastbesturingssysteem heeft een gezins-, versie- en releasenummer.

* **Gast OS-familie**  
  Een windows server-besturingssysteemrelease waarop een gastbesturingssysteem is gebaseerd. *Gezins3* is bijvoorbeeld gebaseerd op Windows Server 2012.
* **Gast-OS-versie**  
  Specifiek voor een gastbesturingssysteemfamilieafbeelding plus relevante [MSRC-patches (Microsoft Security Response Center)][msrc] die beschikbaar zijn op de datum waarop de nieuwe Gast-OS-versie wordt geproduceerd. Niet alle patches kunnen worden opgenomen.

    Getallen beginnen bij 0 en verhogen met 1 telkens wanneer een nieuwe set updates wordt toegevoegd. Trailing nullen worden alleen weergegeven als belangrijk. Dat wil zeggen, versie 2.10 is een andere, veel latere versie dan versie 2.1.
* **Gast OS release**  
  Een heruitgave van een Guest OS-versie. Er vindt een herrelease plaats als Microsoft problemen constateert tijdens het testen; die veranderingen vereisen. De nieuwste release vervangt altijd alle eerdere releases, openbaar of niet. Met de Azure-portal kunnen gebruikers alleen de nieuwste versie voor een bepaalde versie kiezen. Implementaties die worden uitgevoerd op een vorige release worden meestal niet gedwongen geüpgraded, afhankelijk van de ernst van de bug.

In het onderstaande voorbeeld, 2 is de familie, 12 is de versie en "rel2" is de release.

**Gast OS release** - 2.12 rel2

**Configuratietekenreeks voor deze release** - WA-GUEST-OS-2.12_201208-02

De configuratietekenreeks voor een gastbesturingssysteem heeft dezelfde informatie ingebed, samen met een datum die laat zien welke MSRC-patches voor die release in aanmerking zijn genomen. In dit voorbeeld werden MSRC-patches die tot en met augustus 2012 voor Windows Server 2008 R2 zijn geproduceerd, in aanmerking genomen voor opname. Alleen patches die specifiek op die versie van Windows Server zijn toegepast, zijn inbegrepen. Als er bijvoorbeeld een MSRC-patch van toepassing is op Microsoft Office, wordt deze niet opgenomen omdat dat product geen deel uitmaakt van de basisafbeelding van Windows Server.

## <a name="guest-os-system-update-process"></a>GastOS-systeemupdateproces
Deze pagina bevat informatie over aankomende Gast OS Releases. Klanten hebben aangegeven dat ze willen weten wanneer een release plaatsvindt omdat hun cloudservicerollen opnieuw worden opgestart als ze zijn ingesteld op 'Automatisch' bijwerken. Gast OS releases meestal optreden 2-3 weken na de MSRC update release die optreedt op de tweede dinsdag van elke maand. Nieuwe releases bevatten alle relevante MSRC-patches voor elke Gast-OS-familie.

Microsoft Azure brengt voortdurend updates uit. Het gastbesturingssysteem is slechts één dergelijke update in de pijplijn. Een release kan worden beïnvloed door vele factoren te talrijk om hier lijst. Bovendien draait Azure op letterlijk honderdduizenden machines. Dit betekent dat het onmogelijk is om een exacte datum en tijd te geven wanneer je rol(en) opnieuw wordt opgestart. We werken aan een plan om reboots te beperken of te timen.

Wanneer een nieuwe versie van het gastbesturingssysteem wordt gepubliceerd, kan het tijd kosten om volledig te worden gepropageerd in Azure. Als services worden bijgewerkt naar het nieuwe gastbesturingssysteem, worden ze opnieuw opgestart ter ere van updatedomeinen. Services die zijn ingesteld op het gebruik van "Automatische" updates, krijgen eerst een release. Na de update ziet u de nieuwe gastversie voor uw service in de Azure-portal. In deze periode kunnen opnieuw releases optreden. Sommige versies kunnen worden geïmplementeerd over langere perioden en automatische upgrade reboots kan niet optreden gedurende vele weken na de officiële releasedatum. Zodra een gastbesturingssysteem beschikbaar is, u die versie expliciet kiezen uit de portal of in uw configuratiebestand.

Voor een groot deel van waardevolle informatie over opnieuw opstarten en verwijzingen naar meer informatie technische details van Gast en Host OS updates, zie de MSDN blog post getiteld [Role Instance Herstart wegens OS Upgrades][restarts].

Als u uw gastbesturingssysteem handmatig bijwerkt, raadpleegt u het [pensioenbeleid voor gastbesturingssysteem][retirepolicy] voor aanvullende informatie.

## <a name="guest-os-supportability-and-retirement-policy"></a>Ondersteuning van gast-besturingssysteem en pensioenbeleid
De Guest OS supportability en pensioen beleid wordt [hier][retirepolicy]uitgelegd .

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[RsS-feed gastos-update]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[Meer]: https://docs.microsoft.com/azure/cloud-services/applications-dont-support-tls-1-2  
