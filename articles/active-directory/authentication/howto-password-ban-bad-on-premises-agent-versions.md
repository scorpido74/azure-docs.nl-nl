---
title: Releasegeschiedenis van wachtwoordbeveiligingsagent - Azure Active Directory
description: Documenten versie release en gedragsverandering geschiedenis
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fd33388cb1bdf7c87c44fb3273c6850122a0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847846"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Versiegeschiedenis van azure AD Password Protection-agent

## <a name="121250"></a>1.2.125.0

Releasedatum: 3/22/2019

* Kleine foutfouten oplossen in gebeurtenislogboekberichten
* Eula-overeenkomst bijwerken naar definitieve versie voor algemene beschikbaarheid

> [!NOTE]
> Build 1.2.125.0 is de algemene beschikbaarheid te bouwen. Nogmaals bedankt aan iedereen heeft feedback gegeven over het product!

## <a name="121160"></a>1.2.116.0

Releasedatum: 3/13/2019

* De cmdlets Get-AzureADPasswordProtectionProxy en Get-AzureADPasswordProtectionDCAgent rapporteren nu de softwareversie en de huidige Azure-tenant met de volgende beperkingen:
  * Softwareversie en Azure-tenantgegevens zijn alleen beschikbaar voor DC-agents en proxy's waarop versie 1.2.116.0 of hoger wordt uitgevoerd.
  * Azure-tenantgegevens kunnen niet worden gerapporteerd totdat een herregistratie (of verlenging) van de proxy of forest is opgetreden.
* De Proxy-service vereist nu dat .NET 4.7 is geïnstalleerd.
  * .NET 4.7 moet al worden geïnstalleerd op een volledig bijgewerkte Windows Server. Als dit niet het geval is, download en voer u de installer te downloaden en uit te voeren die is gevonden bij [De .NET Framework 4.7 offline installer voor Windows.](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)
  * Op Server Core-systemen kan het nodig zijn om de /q-vlag door te geven aan de .NET 4.7-installer om deze te laten slagen.
* De Proxy-service ondersteunt nu automatische upgrade. Automatische upgrade maakt gebruik van de Microsoft Azure AD Connect Agent Updater-service die naast de proxyservice is geïnstalleerd. Automatische upgrade is standaard ingeschakeld.
* Automatische upgrade kan worden ingeschakeld of uitgeschakeld met de cmdlet Set-AzureADPasswordProtectionProxyConfiguration. De huidige instelling kan worden opgevraagd met de cmdlet Get-AzureADPasswordProtectionProxyConfiguration.
* De service-binaire voor de DC-agentservice is omgedoopt tot AzureADPasswordProtectionDCAgent.exe.
* De servicebinaire voor de proxyservice is omgedoopt tot AzureADPasswordProtectionProxy.exe. Firewallregels moeten mogelijk dienovereenkomstig worden gewijzigd als een firewall van derden in gebruik is.
  * OPMERKING: als een http-proxyconfig-bestand werd gebruikt in een vorige proxy-installatie, moet het na deze upgrade worden hernoemd (van *proxyservice.exe.config* naar *AzureADPasswordProtectionProxy.exe.config).*
* Alle tijdbeperkte functionaliteitscontroles zijn verwijderd uit de DC-agent.
* Kleine bugs fixes en logging verbeteringen.

## <a name="12650"></a>1.2.65.0

Releasedatum: 2/1/2019

Wijzigingen:

* DC-agent en proxyservice worden nu ondersteund op Server Core. Mininimum OS-vereisten zijn ongewijzigd ten opzichte van voorheen: Windows Server 2012 voor DC-agents en Windows Server 2012 R2 voor proxy's.
* De cmdlets Register-AzureADPasswordProtectionProxy en Register-AzureADPasswordProtectionForest ondersteunen nu op apparaatcode gebaseerde Azure-verificatiemodi.
* De cmdlet Get-AzureADPasswordProtectionDCAgent negeert verminkte en/of ongeldige serviceverbindingspunten. Dit lost de bug waar domeincontrollers soms meerdere keren in de uitvoer zouden verschijnen.
* De cmdlet Get-AzureADPasswordProtectionSummaryReport negeert verminkte en/of ongeldige serviceverbindingspunten. Dit lost de bug waar domeincontrollers soms meerdere keren in de uitvoer zouden verschijnen.
* De Proxy powershell-module is nu geregistreerd bij %ProgramFiles%\WindowsPowerShell\Modules. De PSModulePath-omgevingsvariabele van de machine wordt niet langer gewijzigd.
* Er is een nieuwe cmdlet Get-AzureADPasswordProtectionProxy toegevoegd om te helpen bij het ontdekken van geregistreerde proxy's in een forest of domein.
* De DC-agent gebruikt een nieuwe map in het sysvol-aandeel voor het repliceren van wachtwoordbeleid en andere bestanden.

   Oude maplocatie:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nieuwe maplocatie:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Deze wijziging is aangebracht om vals-positieve "verweesde GPO" waarschuwingen te voorkomen.)

   > [!NOTE]
   > Er wordt geen migratie of het delen van gegevens uitgevoerd tussen de oude map en de nieuwe map. Oudere DC-agentversies blijven de oude locatie gebruiken totdat deze wordt bijgewerkt naar deze versie of hoger. Zodra alle DC-agents versie 1.2.65.0 of hoger uitvoeren, kan de oude sysvol-map handmatig worden verwijderd.

* De DC-agent en proxyservice detecteren en verwijderen nu verminkte kopieën van hun respectievelijke serviceverbindingspunten.
* Elke DC-agent verwijdert periodiek verminkte en verouderde serviceverbindingspunten in zijn domein, voor zowel DC-agent- als proxyserviceverbindingspunten. Zowel dc-agent als proxyserviceverbindingspunten worden als verouderd beschouwd als de heartbeattijdstempel ouder is dan zeven dagen.
* De DC-agent verlengt nu het forestcertificaat indien nodig.
* De proxyservice verlengt nu het proxycertificaat indien nodig.
* Updates voor wachtwoordvalidatiealgoritme: de algemene lijst met verboden wachtwoorden en klantspecifieke lijst met verboden wachtwoorden (indien geconfigureerd) worden gecombineerd voordat het wachtwoord wordt gevalideerd. Een bepaald wachtwoord kan nu worden afgewezen (alleen niet of controleren) als het tokens bevat van zowel de globale als klantspecifieke lijst. De documentatie van het gebeurtenislogboek is bijgewerkt om dit weer te geven; zie [Monitor Azure AD Password Protection](howto-password-ban-bad-on-premises-monitor.md).
* Oplossingen voor prestaties en robuustheid
* Verbeterde logboekregistratie

> [!WARNING]
> Tijdelijke functionaliteit: de DC-agentservice in deze release (1.2.65.0) stopt per 1 september 2019 met het verwerken van wachtwoordvalidatieaanvragen.  DC-agentservices in eerdere releases (zie onderstaande lijst) stoppen met de verwerking per 1 juli 2019. De DC-agentservice in alle versies logt 10021-gebeurtenissen in op het gebeurtenislogboek Admin in de twee maanden voorafgaand aan deze deadlines. Alle tijdslimietbeperkingen worden verwijderd in de komende GA-release. De Proxy agent service is niet beperkt in de tijd in elke versie, maar moet nog steeds worden opgewaardeerd naar de nieuwste versie om te profiteren van alle daaropvolgende bug fixes en andere verbeteringen.

## <a name="12250"></a>1.2.25.0

Releasedatum: 11/01/2018

Fixes:

* DC-agent en proxyservice mogen niet langer mislukken als gevolg van fouten in certificaatvertrouwensrelaties.
* DC-agent en proxyservice hebben extra oplossingen voor FIPS-compatibele machines.
* Proxyservice werkt nu naar behoren in een TLS 1.2-netwerkomgeving.
* Kleine prestatie- en robuustheidsoplossingen
* Verbeterde logboekregistratie

Wijzigingen:

* Het minimaal vereiste besturingssysteemniveau voor de Proxy-service is nu Windows Server 2012 R2. Het minimaal vereiste besturingssysteemniveau voor de DC-agentservice blijft op Windows Server 2012.
* De Proxy-service vereist nu .NET-versie 4.6.2.
* Het wachtwoordvalidatiealgoritme maakt gebruik van een uitgebreide tekennormalisatietabel. Dit kan ertoe leiden dat wachtwoorden worden afgewezen die in eerdere versies zijn geaccepteerd.

## <a name="12100"></a>1.2.10.0

Releasedatum: 8/17/2018

Fixes:

* Register-AzureADPasswordProtectionProxy en Register-AzureADPasswordProtectionForest ondersteunen nu multi-factor authenticatie
* Register-AzureADPasswordProtectionProxy vereist een WS2012 of hoger domeincontroller in het domein om versleutelingsfouten te voorkomen.
* DC-agentservice is betrouwbaarder over het aanvragen van een nieuw wachtwoordbeleid van Azure bij het opstarten.
* DC-agentservice vraagt indien nodig elk uur een nieuw wachtwoordbeleid van Azure aan, maar doet dit nu op een willekeurig geselecteerde begintijd.
* DC-agentservice zal niet langer leiden tot een onbepaalde vertraging in nieuwe DC-advertentie wanneer geïnstalleerd op een server voorafgaand aan de promotie als een replica.
* DC-agentservice eert nu de configuratie-instelling 'Wachtwoordbeveiliging inschakelen op Windows Server Active Directory'
* Zowel DC-agent en proxy installateurs zullen nu ondersteunen in-place upgrade bij het upgraden naar toekomstige versies.

> [!WARNING]
> In-place upgrade van versie 1.1.10.3 wordt niet ondersteund en zal resulteren in een installatiefout. Als u wilt upgraden naar versie 1.2.10 of hoger, moet u eerst de DC-agent en proxyservicesoftware volledig verwijderen en vervolgens de nieuwe versie helemaal opnieuw installeren. Opnieuw registratie van de Azure AD-wachtwoordbeveiliging-proxyservice is vereist.  Het is niet verplicht om het bos opnieuw te registreren.

> [!NOTE]
> In-place upgrades van de DC-agent software vereist een reboot.

* DC-agent en proxyservice ondersteunen nu het uitvoeren van een server die is geconfigureerd om alleen FIPS-compatibele algoritmen te gebruiken.
* Kleine prestatie- en robuustheidsoplossingen
* Verbeterde logboekregistratie

## <a name="11103"></a>1.1.10.3

Releasedatum: 6-15-2018

Eerste openbare preview-release

## <a name="next-steps"></a>Volgende stappen

[Azure AD-wachtwoordbeveiliging implementeren](howto-password-ban-bad-on-premises-deploy.md)
