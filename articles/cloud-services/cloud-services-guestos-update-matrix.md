---
title: Meer informatie over de nieuwste versies van het Azure-gast besturingssysteem | Microsoft Docs
description: De nieuwste versie nieuws en SDK-compatibiliteit voor Azure Cloud Services-gast besturingssysteem.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 12/5/2019
ms.author: raiye
ms.openlocfilehash: c22ed9da2d2947fa4085a4695acd8803964ca757
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894625"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure Guest OS releases en SDK Compatibility Matrix
Voorziet in actuele informatie over de nieuwste versies van het Azure-gast besturingssysteem voor Cloud Services. Deze informatie helpt u bij het plannen van het upgradepad voordat een gast besturingssysteem wordt uitgeschakeld. Als u uw rollen configureert voor het gebruik van *automatische* updates van gast besturingssystemen zoals beschreven in de update-instellingen van het [Azure-gast besturingssysteem][Azure Guest OS Update Settings], is het niet belang rijk dat u deze pagina leest.

> [!IMPORTANT]
> Deze pagina is van toepassing op Cloud Services Web-en werk rollen, die bovenop een gast besturingssysteem worden uitgevoerd. Het is **niet van toepassing** op IaaS virtual machines.
>
>


> [!TIP]
>  Abonneer u op de RSS-feed voor het [RSS-feed voor het gast besturingssysteem bijwerken] om de meest recente melding over alle wijzigingen in het gast besturingssysteem te ontvangen.
>
>

> [!IMPORTANT]
> Alleen de nieuwste twee versies van het gast besturingssysteem worden ondersteund en zijn beschikbaar in de Azure Portal.
>
>

Weet u niet zeker hoe u uw gast besturingssysteem kunt bijwerken? Bekijk [Dit][cloud updates] uit.

## <a name="news-updates"></a>Nieuws updates

###### <a name="december-5-2019"></a>**5 december 2019**
Het besturings systeem november gast is uitgebracht.

###### <a name="november-1-2019"></a>**1 november 2019**
Het besturings systeem van oktober gast is uitgebracht.

###### <a name="october-7-2019"></a>**7 oktober 2019**
Het besturings systeem september gast is uitgebracht.

###### <a name="september-4-2019"></a>**4 september 2019**
Het besturings systeem voor augustus gast is uitgebracht.

###### <a name="july-26-2019"></a>**26 juli 2019**
Het besturings systeem van juli wordt uitgebracht.

###### <a name="july-8-2019"></a>**8 juli 2019**
Het besturings systeem van juni gast is uitgebracht.

###### <a name="june-6-2019"></a>**6 juni 2019**
Het kan zijn dat het besturings systeem gast is losgelaten.

###### <a name="may-7-2019"></a>**7 mei 2019**
Het besturings systeem van april gast is uitgebracht.

###### <a name="march-26-2019"></a>**26 maart 2019**
Het besturings systeem maart gast is uitgebracht.

###### <a name="march-12-2019"></a>**12 maart 2019**
Het besturings systeem februari gast is uitgebracht.

###### <a name="february-5-2019"></a>**5 februari 2019**
Het besturings systeem voor januari gast is uitgebracht.

###### <a name="january-24-2019"></a>**24 januari 2019**
Family 6-gast besturingssysteem (Windows Server 2019) is uitgebracht.

###### <a name="january-7-2019"></a>**7 januari 2019**
Het besturings systeem december gast is uitgebracht.

###### <a name="december-14-2018"></a>**14 december 2018**
Het besturings systeem november gast is uitgebracht.

###### <a name="november-8-2018"></a>**8 november 2018**
Het besturings systeem van oktober gast is uitgebracht.

###### <a name="october-12-2018"></a>**12 oktober 2018**
Het besturings systeem september gast is uitgebracht.

## <a name="releases"></a>Releases

## <a name="family-6-releases"></a>Versies van familie 6
**Windows Server 2019**

.NET Framework geïnstalleerd: 3,5, 4.7.2

> [!NOTE]
> De Windows Azure SDK voor .NET-3,0 kan [hier][Windows Azure SDK]worden gedownload.
>
>Installatie stappen:
>1. Verwijder alle oudere versies van MicrosoftAzureAuthoringTools*. msi
>2. De [Azure SDK voor .net-3,0][Windows Azure SDK] installeren
>3. De computer opnieuw opstarten
>4. Een nieuw Cloud service project maken en één worker-rol toevoegen
>5. De besturingssysteem familie wijzigen in 6 en een pakket bouwen
>6. Het pakket implementeren op Azure met behulp van de Azure Portal of Visual Studio
>


| Configuratie teken reeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
| WA-GUEST-OS-6.13 _201911-01 | 5 december 2019 | Post 6,15 |  
| WA-GUEST-OS-6,12 _201910-01 | 1 november 2019 | Post 6,14 |  
|~~WA-GUEST-OS-6.11 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-6.10 _201908-01~~| 4 augustus 2019 | 1 november 2019  |  
|~~WA-GUEST-OS-6,9 _201907-0~~|26 juli 2019 | 7 oktober 2019 |
|~~WA-GUEST-OS-6,8 _201906-01~~|8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-6,7 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-6.6_201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-6.5_201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-6.4_201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-6.3_201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-6.2_201812-01~~ |24 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 januari 2019 |5 februari 2019 |

## <a name="family-5-releases"></a>Versies van familie 5
**Windows Server 2016**

.NET Framework geïnstalleerd: 3,5, 4,6

> [!NOTE]
> Het RDP-wacht woord voor besturingssysteem familie 5 moet mini maal 10 tekens bevatten.
>


| Configuratie teken reeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
| WA-GUEST-OS-5.37 _201911-01 | 5 december 2019 | Post 5,39 |  
| WA-GUEST-OS-5.36 _201910-01 | 1 november 2019 | Post 5,38 |  
|~~WA-GUEST-OS-5.35 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-5.34 _201908-01~~|  4 augustus 2019  | 1 november 2019 |  
|~~WA-GUEST-OS-5.33 _201907-01~~| 26 juli 2019 | 7 oktober 2019 |  
|~~WA-GUEST-OS-5.32 _201906-01~~|8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-5.31 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-5.30_201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-5.29_201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-5.28_201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-5.27_201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-5.26_201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-4-releases"></a>Releases van familie 4
**Windows Server 2012 R2**

.NET Framework geïnstalleerd: 3,5, 4.5.1, 4.5.2

| Configuratie teken reeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
| WA-GUEST-OS-4.72 _201911-01 | 5 december 2019 | Post 4,74 |  
| WA-GUEST-OS-4.71 _201910-01 | 1 november 2019 | Post 4,73 |  
|~~WA-GUEST-OS-4.70 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-4.69 _201908-01~~| 4 augustus 2019 | 1 november 2019 |  
|~~WA-GUEST-OS-4.68 _201907-01~~| 26 juli 2019  | 7 oktober 2019 |
|~~WA-GUEST-OS-4.67 _201906-01~~| 8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-4.66 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-4.65_201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-4.64_201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-4.63_201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-4.62_201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-4.61_201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-3-releases"></a>Versies van familie 3
**Windows Server 2012**

.NET Framework geïnstalleerd: 3,5, 4,5

| Configuratie teken reeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
| WA-GUEST-OS-3.79 _201911-01 | 5 december 2019 | Post 3,81 |  
| WA-GUEST-OS-3.78 _201910-01 | 1 november 2019 | Post 3,80 |  
|~~WA-GUEST-OS-3.77 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-3.76 _201908-01~~|  4 augustus 2019  |  1 november 2019  |  
|~~WA-GAST BESTURINGSSYSTEEM-3,75 _201907-01~~| 26 juli 2019 | 7 oktober 2019 |
|~~WA-GUEST-OS-3.74 _201906-01~~| 8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-3.73 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-3.72_201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-3.71_201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-3.70_201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-3.69_201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-3.68_201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-2-releases"></a>Versies van familie 2
**Windows Server 2008 R2 SP1**

.NET Framework geïnstalleerd: 3,5 (inclusief 2,0 en 3,0), 4,5

| Configuratie teken reeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
| WA-GUEST-OS-2.93 _201911-01 | 5 december 2019 | Post 2,94 |  
| WA-GUEST-OS-2.91 _201910-01 | 1 november 2019 | Post 2,93 |  
|~~WA-GUEST-OS-2.90 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-2.89 _201908-01~~| 4 augustus 2019 | 1 november 2019 |  
|~~WA-GUEST-OS-2,88 _201907-01~~| 26 juli 2019 | 7 oktober 2019 |
|~~WA-GUEST-OS-2.87 _201906-01~~|8 juli 2019 | 4 augustus 2019 |
|~~WA-GUEST-OS-2.86 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-2.85_201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-2.84_201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-2.83_201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-2.82_201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="msrc-patch-updates"></a>Updates van MSRC-patch
De lijst met patches die zijn opgenomen in elke maandelijkse versie van het gast besturingssysteem, is [hier][patches]beschikbaar.

## <a name="sdk-support"></a>SDK-ondersteuning
Hoewel het [pensionerings beleid voor de Azure SDK][retire policy sdk] aangeeft dat alleen de versies hoger dan 2,2 worden ondersteund, kunt u met specifieke gast besturingssysteem families eerdere versies gebruiken. U moet altijd de meest recente ondersteunde SDK gebruiken.

| Gast besturingssysteem familie | Compatibele SDK-versies |
| --- | --- |
| 6 |Versie 2.9.6 + |
| 5 |Versie 2.9.5.1 + |
| 4 |Versie 2.1 + |
| 3 |Versie 1.8 + |
| 2 |Versie 1.3 + |
| 1 |Versie 1.0 + |

## <a name="guest-os-release-information"></a>Release-informatie voor het gast besturingssysteem
Er zijn drie datums die belang rijk zijn voor de releases van gast besturingssystemen: **release** datum, **Uitgeschakelde** datum en **verval** datum. Een gast besturingssysteem wordt beschouwd als beschikbaar wanneer het zich in de portal bevindt en kan worden geselecteerd als het doel-gast besturingssysteem. Wanneer een gast besturingssysteem de **Uitgeschakelde** datum bereikt, wordt het verwijderd uit Azure. Elke Cloud service die gericht is op het gast besturingssysteem, blijft echter gewoon functioneren.

In het venster tussen de **Uitgeschakelde** datum en de **verval** datum kunt u eenvoudig van het ene naar het andere gast besturingssysteem overstappen. Als u *automatisch* gebruikt als uw gast besturingssysteem, hebt u altijd de nieuwste versie en hoeft u zich geen zorgen te maken over het verlopen van het besturings systeem.

Wanneer de **verloop** datum wordt verstreken, wordt een Cloud service die nog steeds gebruikmaakt van dat gast besturingssysteem, gestopt, verwijderd of gedwongen bijgewerkt. U kunt [hier][retirepolicy]meer lezen over het pensionerings beleid.

## <a name="guest-os-family-version-explanation"></a>Gast besturingssysteem familie-versie uitleg
De gast besturingssysteem families zijn gebaseerd op release versies van micro soft Windows Server. Het gast besturingssysteem is het onderliggende besturings systeem waarop Azure Cloud Services worden uitgevoerd. Elk gast besturingssysteem heeft een familie, versie en release nummer.

* **Gast besturingssysteem familie**  
  Een versie van het Windows Server-besturings systeem waarop een gast besturingssysteem is gebaseerd. Bijvoorbeeld: *Family 3* is gebaseerd op Windows Server 2012.
* **Versie van gast besturingssysteem**  
  Specifiek voor een installatie kopie van een gast besturingssysteem-serie en relevante [MSRC-patches (micro soft Security Response Center)][msrc] die beschikbaar zijn op de datum waarop de nieuwe versie van het gast besturingssysteem is geproduceerd. Mogelijk zijn niet alle patches opgenomen.

    Nummers beginnen bij 0 en elke keer dat er een nieuwe set updates wordt toegevoegd, verhoogd met 1. Volg nullen worden alleen weer gegeven als belang rijk. Dat wil zeggen versie 2,10 is een andere, veel latere versie dan versie 2,1.
* **Release van gast besturingssysteem**  
  Een heruitgave van een versie van een gast besturingssysteem. Een heruitgave treedt op als micro soft problemen tijdens het testen vindt; wijzigingen moeten worden aangebracht. De meest recente versie heeft altijd voor rang op eerdere releases, openbaar of niet. Met de Azure Portal kunnen gebruikers alleen de meest recente release voor een bepaalde versie kiezen. Implementaties die op een eerdere versie worden uitgevoerd, worden doorgaans niet geforceerd bijgewerkt, afhankelijk van de ernst van de fout.

In het onderstaande voor beeld is 2 de familie, 12 de versie en ' rel2 ' is de release.

**Release van gast besturingssysteem** -2,12 rel2

**Configuratie teken reeks voor deze release** -wa-Guest-OS-2.12 _201208-02

In de configuratie teken reeks voor een gast besturingssysteem zijn dezelfde gegevens Inge sloten, samen met een datum waarop wordt aangegeven welke MSRC-patches voor die release werden overwogen. In dit voor beeld worden de MSRC-patches die voor Windows Server 2008 R2 zijn geproduceerd tot en met augustus 2012, opgenomen. Er zijn alleen patches opgenomen die specifiek van toepassing zijn op die versie van Windows Server. Als een MSRC-patch bijvoorbeeld van toepassing is op Microsoft Office, wordt deze niet opgenomen omdat dat product geen deel uitmaakt van de basis installatie kopie van Windows Server.

## <a name="guest-os-system-update-process"></a>Update proces voor het gast besturingssysteem systeem
Deze pagina bevat informatie over aanstaande versies van het gast besturingssysteem. Klanten hebben aangegeven dat ze willen weten wanneer een release plaatsvindt omdat hun Cloud service rollen opnieuw worden opgestart als ze zijn ingesteld op ' automatische ' update. Gast besturingssysteem releases vinden doorgaans 2-3 weken na de release van de MSRC-update die op de tweede dinsdag van elke maand plaatsvindt. Nieuwe releases bevatten alle relevante MSRC-patches voor elke gast besturingssysteem familie.

De updates worden voortdurend door Microsoft Azure vrijgegeven. Het gast besturingssysteem is slechts één update in de pijp lijn. Een release kan worden beïnvloed door een groot aantal factoren die hier kunnen worden weer gegeven. Bovendien wordt Azure op duizenden computers uitgevoerd op meer dan duizend tallen. Dit betekent dat het niet mogelijk is om een exacte datum en tijd te geven wanneer uw rol (len) opnieuw wordt opgestart. Er wordt gewerkt aan een abonnement om het opnieuw opstarten te beperken of in te stellen.

Wanneer een nieuwe versie van het gast besturingssysteem wordt gepubliceerd, kan het enige tijd duren voordat Azure wordt door gegeven. Wanneer Services worden bijgewerkt naar het nieuwe gast besturingssysteem, worden ze opnieuw opgestart met update domeinen. Services die zijn ingesteld voor het gebruik van automatische updates, krijgen eerst een release. Na de update wordt de nieuwe versie van het gast besturingssysteem voor uw service weer gegeven in de Azure Portal. Tijdens deze periode kunnen er opnieuw uitgebrachte versies plaatsvinden. Sommige versies kunnen meer tijd in beslag nemen en het automatisch opnieuw opstarten van de upgrade wordt mogelijk niet veel weken na de officiële release datum uitgevoerd. Zodra een gast besturingssysteem beschikbaar is, kunt u de desbetreffende versie expliciet kiezen in de portal of in uw configuratie bestand.

Zie het MSDN-blog bericht met de titel [instantie wordt opnieuw opgestart vanwege upgrades van het besturings systeem][restarts]voor een grote hoeveelheid informatie over het opnieuw opstarten en verwijzingen naar meer informatie technische details van gast-en host OS-updates.

Als u het gast besturingssysteem hand matig bijwerkt, raadpleegt u het [uittredings beleid voor gast besturingssysteem][retirepolicy] voor aanvullende informatie.

## <a name="guest-os-supportability-and-retirement-policy"></a>Ondersteunings beleid voor gast besturingssystemen en buiten gebruik stellen
Het beleid voor de ondersteuning van gast besturingssystemen en het buiten gebruik stellen wordt [hier][retirepolicy]uitgelegd.

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[RSS-feed voor het gast besturingssysteem bijwerken]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
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
