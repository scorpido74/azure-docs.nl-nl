---
title: Problemen met de implementatie van cloudservices oplossen | Microsoft Documenten
description: Er zijn een paar veelvoorkomende problemen die u tegenkomen bij het implementeren van een cloudservice naar Azure. Dit artikel biedt oplossingen voor een aantal van hen.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: ccb08f853ae0f941dd5f9c0eca8c77f0f650905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122754"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Problemen met de implementatie van cloudservices oplossen
Wanneer u een cloudservicetoepassingspakket implementeert naar Azure, u informatie over de implementatie verkrijgen in het deelvenster **Eigenschappen** in de Azure-portal. U de gegevens in dit deelvenster gebruiken om problemen met de cloudservice op te lossen en u deze informatie aan Azure Support verstrekken wanneer u een nieuw ondersteuningsverzoek opent.

U het deelvenster **Eigenschappen** als volgt vinden:

* Klik in de Azure-portal op de implementatie van uw cloudservice, klik op **Alle instellingen**en klik vervolgens op **Eigenschappen**.

> [!NOTE]
> U de inhoud van het deelvenster **Eigenschappen** naar het klembord kopiëren door op het pictogram in de rechterbovenhoek van het deelvenster te klikken.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Probleem: ik heb geen toegang tot mijn website, maar mijn implementatie is gestart en alle rolexemplaren zijn klaar
De URL-link van de website in de portal bevat de poort niet. De standaardpoort voor websites is 80. Als uw toepassing is geconfigureerd om in een andere poort te worden uitgevoerd, moet u bij het openen van de website het juiste poortnummer aan de URL toevoegen.

1. Klik in de Azure-portal op de implementatie van uw cloudservice.
2. Controleer in het deelvenster **Eigenschappen** van de Azure-portal de poorten voor de rolinstanties (onder **Invoereindpunten).**
3. Als de poort geen 80 is, voegt u de juiste poortwaarde toe aan de URL wanneer u de toepassing opent. Als u een niet-standaardpoort wilt opgeven, typt u de URL, gevolgd door een dubbele punt (:), gevolgd door het poortnummer, zonder spaties.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Probleem: Mijn rol instanties gerecycled zonder dat ik iets doe
Servicehealing vindt automatisch plaats wanneer Azure probleemknooppunten detecteert en daarom rolexemplaren verplaatst naar nieuwe knooppunten. Wanneer dit gebeurt, ziet u mogelijk dat uw rolexemplaren automatisch worden gerecycled. Ga als iemand op de zoek naar de sein of er sprake is van genezing van de dienst:

1. Klik in de Azure-portal op de implementatie van uw cloudservice.
2. Bekijk in het deelvenster **Eigenschappen** van de Azure-portal de informatie en bepaal of serviceherstel heeft plaatsgevonden tijdens de tijd dat u de taakrecycling waarnam.

Rollen worden ook ongeveer één keer per maand gerecycled tijdens updates van host-OS en gast-OS.  
Zie de functie [Rolinstantie opnieuw opstarten vanwege OS-upgrades voor](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) meer informatie

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Probleem: ik kan geen VIP-swap doen en een foutmelding ontvangen
Een VIP-swap is niet toegestaan als er een implementatie-update wordt uitgevoerd. Implementatie-updates kunnen automatisch plaatsvinden wanneer:

* Er is een nieuw gastbesturingssysteem beschikbaar en u bent geconfigureerd voor automatische updates.
* Service healing vindt plaats.

Ga als u op zoek of een automatische update u verhindert een VIP-swap uit te schakelen:

1. Klik in de Azure-portal op de implementatie van uw cloudservice.
2. Kijk in het deelvenster **Eigenschappen** van de Azure-portal naar de waarde van **status**. Als deze **klaar**is, controleert u **Laatst of** er onlangs een is gebeurd die de VIP-swap kan voorkomen.
3. Herhaal stap 1 en 2 voor de productie-implementatie.
4. Als er een automatische update wordt uitgevoerd, wacht u tot deze is voltooid voordat u probeert de VIP-swap te doen.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Probleem: een rolinstantie loopt tussen gestart, initialiseren, bezet en gestopt
Dit probleem kan duiden op een probleem met uw programmacode, pakket of configuratiebestand. In dat geval moet u de status om de paar minuten kunnen zien veranderen en kan de Azure-portal iets zeggen als **Recycling,** **Bezet**of **Initialiseren.** Dit geeft aan dat er iets mis is met de toepassing waardoor de rolinstantie niet wordt uitgevoerd.

Zie het blogbericht [Azure PaaS Compute Diagnostics Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) en Common [issues die ervoor zorgen dat rollen worden gerecycled](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)voor meer informatie over het oplossen van problemen voor dit probleem.

## <a name="problem-my-application-stopped-working"></a>Probleem: Mijn toepassing werkt niet meer
1. Klik in de Azure-portal op de rolinstantie.
2. Houd in het deelvenster **Eigenschappen** van de Azure-portal rekening met de volgende voorwaarden om uw probleem op te lossen:
   * Als de rolinstantie onlangs is gestopt (u de waarde van **het aantal afbreken controleren),** kan de implementatie worden bijgewerkt. Wacht om te zien of de rolinstantie zelfstandig weer functioneert.
   * Als de rolinstantie **bezet**is, controleert u de toepassingscode om te zien of de [statuscheck-gebeurtenis](/previous-versions/azure/reference/ee758135(v=azure.100)) is verwerkt. Mogelijk moet u een code toevoegen of herstellen die deze gebeurtenis verwerkt.
   * Ga door de diagnostische gegevens en probleemoplossingsscenario's in het blogbericht [Azure PaaS Compute Diagnostics Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Als u uw cloudservice recyclet, stelt u de eigenschappen voor de implementatie opnieuw in en worden de gegevens voor het oorspronkelijke probleem effectief gewist.
>
>

## <a name="next-steps"></a>Volgende stappen
Bekijk meer [artikelen over probleemoplossing](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) voor cloudservices.

Zie de [blogreeks van Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)voor meer informatie over het oplossen van problemen met de functieproblemen met de cloudservice door azure PaaS-computerdiagnosegegevens te gebruiken.
