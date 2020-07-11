---
title: Lijst met veilige URL'S voor Windows-virtueel bureau blad-Azure
description: Een lijst met Url's die u moet deblokkeren om ervoor te zorgen dat de implementatie van Windows virtueel bureau blad werkt zoals bedoeld.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9b1bdfc326ff217e68785d823b4af046af3241b7
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225104"
---
# <a name="safe-url-list"></a>Lijst met veilige URL'S

U moet bepaalde Url's blok keren zodat uw Windows Virtual Desktop-implementatie goed werkt. In dit artikel vindt u een overzicht van deze Url's, zodat u weet welke items veilig zijn.

## <a name="virtual-machines"></a>Virtuele machines

De virtuele Azure-machine die u maakt voor Windows Virtual Desktop moet toegang hebben tot de volgende URL's:

|Adres|Uitgaande TCP-poort|Doel|Servicetag|
|---|---|---|---|
|*.wvd.microsoft.com|443|Serviceverkeer|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Updates van Agent- en SXS-stack|AzureCloud|
|*.core.windows.net|443|Agent-verkeer|AzureCloud|
|*.servicebus.windows.net|443|Agent-verkeer|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Agent-verkeer|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows-activering|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Ondersteuning Azure-portal|AzureCloud|

>[!IMPORTANT]
>Windows Virtual Desktop biedt nu ondersteuning voor de FQDN-tag. Zie [Azure Firewall gebruiken om Windows Virtual Desktop-implementaties te beveiligen](../firewall/protect-windows-virtual-desktop.md) voor meer informatie.
>
>We raden u aan om FQDN-tags of servicetags te gebruiken in de plaats van URL's om serviceproblemen te vermijden. De vermelde URL's en tags komen enkel overeen met Windows Virtual Desktop-sites en -resources. Ze bevatten geen URL's voor andere services zoals Azure Active Directory.

In de volgende tabel vindt u optionele URL's waar uw virtuele Azure-machines toegang tot kunnen hebben.

|Adres|Uitgaande TCP-poort|Doel|Servicetag|
|---|---|---|---|
|*.microsoftonline.com|443|Verificatie voor Microsoft Online Services|Geen|
|*.events.data.microsoft.com|443|Telemetrieservice|Geen|
|www.msftconnecttest.com|443|Detecteert of het besturingssysteem een verbinding heeft met het internet|Geen|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Geen|
|login.windows.net|443|Aanmelden bij Microsoft Online Services, Microsoft 365|Geen|
|*.sfx.ms|443|Updates voor OneDrive-clientsoftware|Geen|
|*.digicert.com|443|Controle van certificaatintrekking|Geen|

>[!NOTE]
>Het virtuele bureau blad van Windows heeft momenteel geen lijst met IP-adresbereiken die u kunt blok keren om netwerk verkeer toe te staan. Er wordt momenteel alleen ondersteuning geboden voor het opheffen van de blok kering van specifieke Url's.
>
>Zie [Office 365 url's en IP-](/office365/enterprise/urls-and-ip-address-ranges)adresbereiken voor een lijst met veilige Office-url's, met inbegrip van vereiste url's voor Azure Active Directory.
>
>U moet het jokerteken (*) gebruiken voor URL's met serviceverkeer. Als u liever geen * gebruikt voor agent-gerelateerd verkeer, dan kunt u als volgt URL's zonder jokers vinden:
>
>1. Registreer uw virtuele machines bij de Windows Virtual Desktop-hostgroep.
>2. Open **Logboeken**en ga naar **Windows logs**  >  **Application**  >  **WVD-agent** en zoek naar gebeurtenis-id 3702.
>3. Neem de URL's onder gebeurtenis-id 3702 op in uw whitelist. De URL's onder gebeurtenis-id 3702 zijn regiospecifiek. U moet het deblokkeren van het proces herhalen met de relevante Url's voor elke regio waarin u uw virtuele machines wilt implementeren.

## <a name="remote-desktop-clients"></a>Extern bureaublad-clients

Alle Extern bureaublad-clients die u gebruikt, moeten toegang hebben tot de volgende Url's:

|Adres|Uitgaande TCP-poort|Doel|Client(s)|
|---|---|---|---|
|*.wvd.microsoft.com|443|Serviceverkeer|Alles|
|*.servicebus.windows.net|443|Probleemoplossingsgegevens|Alles|
|go.microsoft.com|443|Microsoft FWLinks|Alles|
|aka.ms|443|Microsoft URL-verkorter|Alles|
|docs.microsoft.com|443|Documentatie|Alles|
|privacy.microsoft.com|443|Privacyverklaring|Alle|
|query.prod.cms.rt.microsoft.com|443|Clientupdates|Windows-pc|

>[!IMPORTANT]
>Het openen van deze URL's is essentieel voor een betrouwbare clientervaring. Toegang blokkeren tot deze URL's wordt niet ondersteund en heeft gevolgen voor de functionaliteit van de service. Deze URL's komen enkel overeen met de clientsites en -resources, en bevatten geen URL's voor andere services zoals Azure Active Directory.