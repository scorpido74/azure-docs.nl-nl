---
title: Veelgestelde vragen over Azure Active Directory Connect Health - Azure | Microsoft Documenten
description: Deze veelgestelde vragen beantwoorden vragen over Azure AD Connect Health. In deze Veelgestelde vragen worden vragen behandeld over het gebruik van de service, zoals het factureringsmodel, de mogelijkheden, beperkingen en ondersteuning.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0c6484f46731e0ff2d16d00cb0038202511d193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331082"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Veelgestelde vragen over Azure AD Connect Health
Dit artikel bevat antwoorden op veelgestelde vragen (veelgestelde vragen) over Azure Active Directory (Azure AD) Connect Health. Deze veelgestelde vragen hebben betrekking op vragen over het gebruik van de service, waaronder het factureringsmodel, mogelijkheden, beperkingen en ondersteuning.

## <a name="general-questions"></a>Algemene vragen
**V: Ik beheer meerdere Azure AD-mappen. Hoe schakel ik over naar degene met Azure Active Directory Premium?**

Als u wilt schakelen tussen verschillende Azure AD-tenants, selecteert u de momenteel aangemelde **gebruikersnaam** in de rechterbovenhoek en kiest u het juiste account. Als het account hier niet wordt vermeld, selecteert u **Afmelden**en gebruikt u de globale beheerdersreferenties van de map waarop Azure Active Directory Premium is ingeschakeld om u aan te melden.

**V: Welke versie van identiteitsrollen wordt ondersteund door Azure AD Connect Health?**

In de volgende tabel worden de rollen en ondersteunde versies van het besturingssysteem weergegeven.

|Rol| Besturingssysteem / versie|
|--|--|
|Active Directory Federation Services (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Versie 1.0.9125 of hoger|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Houd er rekening mee dat de functies die door de service worden geleverd, kunnen verschillen op basis van de rol en het besturingssysteem. Met andere woorden, alle functies zijn mogelijk niet beschikbaar voor alle versies van het besturingssysteem. Zie de functiebeschrijvingen voor meer informatie.

**V: Hoeveel licenties heb ik nodig om mijn infrastructuur te bewaken?**

* De eerste Connect Health Agent vereist ten minste één Azure AD Premium-licentie.
* Elke extra geregistreerde agent heeft 25 extra Azure AD Premium-licenties nodig.
* Het aantal agents is gelijk aan het totale aantal agents dat is geregistreerd in alle bewaakte rollen (AD FS, Azure AD Connect en/of AD DS).
* Aad Connect Health-licenties vereisen niet dat u de licentie toewijst aan specifieke gebruikers. U hoeft alleen het vereiste aantal geldige licenties te hebben.

Licentiegegevens zijn ook te vinden op de [pagina Azure AD-prijzen](https://aka.ms/aadpricing).

Voorbeeld:

| Geregistreerde agenten | Licenties nodig | Voorbeeldcontroleconfiguratie |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect-server |
| 2 | 26| 1 Azure AD Connect-server en 1 domeincontroller |
| 3 | 51 | 1 AD FS-server (Active Directory Federation Services), 1 AD FS-proxy en 1 domeincontroller |
| 4 | 76 | 1 AD FS-server, 1 AD FS-proxy en 2 domeincontrollers |
| 5 | 101 | 1 Azure AD Connect-server, 1 AD FS-server, 1 AD FS-proxy en 2 domeincontrollers |

**V: Ondersteunt Azure AD Connect Health Azure Germany Cloud?**

Azure AD Connect Health wordt niet ondersteund in Germany Cloud, behalve voor de [rapportfunctie synchronisatiefouten.](how-to-connect-health-sync.md#object-level-synchronization-error-report)

| Rollen | Functies | Ondersteund in German Cloud |
| ------ | --------------- | --- |
| Status verbinden voor synchroniseren | Monitoring / Inzicht / Waarschuwingen / Analyse | Nee |
|  | Foutrapport synchroniseren | Ja |
| Status verbinden voor ADFS | Monitoring / Inzicht / Waarschuwingen / Analyse | Nee |
| Gezondheid verbinden voor voegt | Monitoring / Inzicht / Waarschuwingen / Analyse | Nee |

Als u wilt zorgen dat de agentverbinding van Connect Health voor synchronisatie wordt ingesteld, configureert u de [installatievereiste](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) dienovereenkomstig.

## <a name="installation-questions"></a>Installatievragen

**V: Wat is de impact van het installeren van de Azure AD Connect Health Agent op afzonderlijke servers?**

De impact van het installeren van de Microsoft Azure AD Connect Health Agent, AD FS, proxyservers voor webtoepassingen, Azure AD Connect (sync) servers, domeincontrollers is minimaal met betrekking tot de CPU, geheugenverbruik, netwerkbandbreedte en opslag.

De volgende getallen zijn een benadering:

* CPU-verbruik: ~1-5% toename.
* Geheugenverbruik: tot 10 % van het totale systeemgeheugen.

> [!NOTE]
> Als de agent niet met Azure kan communiceren, slaat de agent de gegevens lokaal op voor een gedefinieerde maximumlimiet. De agent overschrijft de "cached" gegevens op een "minst onlangs onderhouden" basis.
>
>

* Lokale bufferopslag voor Azure AD Connect-statusagents: ~20 MB.
* Voor AD FS-servers raden we u aan een schijfruimte van 1.024 MB (1 GB) in te richten voor het AD FS-controlekanaal voor Azure AD Connect-statusagents om alle controlegegevens te verwerken voordat deze worden overschreven.

**V: Moet ik mijn servers opnieuw opstarten tijdens de installatie van de Azure AD Connect Health Agents?**

Nee. Voor de installatie van de agents hoeft u de server niet opnieuw op te starten. Voor de installatie van een aantal vereiste stappen kan het echter gaan om een herstart van de server.

Op Windows Server 2008 R2 vereist de installatie van .NET 4.5 Framework bijvoorbeeld een herstart van de server.

**V: Werkt Azure AD Connect Health via een http-proxy?**

Ja. Voor lopende bewerkingen u de statusagent configureren om een HTTP-proxy te gebruiken om uitgaande HTTP-aanvragen door te sturen.
Lees meer over [het configureren van HTTP Proxy for Health Agents](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Als u een proxy moet configureren tijdens de registratie van de agent, moet u mogelijk vooraf de proxy-instellingen van Internet Explorer wijzigen.

1. **Connections** > **Lan-instellingen**voor internet explorer->-instellingen **Settings** > **internetopties** > openen .
2. Selecteer **Een proxyserver gebruiken voor uw LAN**.
3. Selecteer **Geavanceerd** als u verschillende proxypoorten hebt voor HTTP en HTTPS/Secure.

**V: Ondersteunt Azure AD Connect Health basisverificatie wanneer u verbinding maakt met HTTP-proxy's?**

Nee. Een mechanisme om een willekeurige gebruikersnaam en wachtwoord voor basisverificatie op te geven, wordt momenteel niet ondersteund.

**V: Welke firewallpoorten moet ik openen om de Azure AD Connect Health Agent te laten werken?**

Zie het [gedeelte vereisten](how-to-connect-health-agent-install.md#requirements) voor de lijst met firewallpoorten en andere verbindingsvereisten.

**V: Waarom zie ik twee servers met dezelfde naam in de Azure AD Connect Health-portal?**

Wanneer u een agent van een server verwijdert, wordt de server niet automatisch verwijderd uit de Azure AD Connect Health-portal. Als u een agent handmatig van een server verwijdert of de server zelf verwijdert, moet u de serververmelding handmatig verwijderen uit de Azure AD Connect-statusportal.

U een server opnieuw afbeelding smaken of een nieuwe server maken met dezelfde details (zoals de naam van de machine). Als u de reeds geregistreerde server niet hebt verwijderd uit de Azure AD Connect-statusportal en u de agent op de nieuwe server hebt geïnstalleerd, ziet u mogelijk twee vermeldingen met dezelfde naam.

Verwijder in dit geval handmatig de vermelding die bij de oudere server hoort. De gegevens voor deze server moeten verouderd zijn.

## <a name="health-agent-registration-and-data-freshness"></a>Registratie van gezondheidsagenten en versheid van gegevens

**V: Wat zijn veelvoorkomende redenen voor de statusfouten van de statusagent en hoe los ik problemen op?**

De zorgverlener kan zich niet registreren om de volgende mogelijke redenen:

* De agent kan niet communiceren met de vereiste eindpunten omdat een firewall verkeer blokkeert. Dit komt vooral vaak voor op proxyservers van webtoepassingen. Zorg ervoor dat u uitgaande communicatie hebt toegestaan naar de vereiste eindpunten en poorten. Zie de [sectie vereisten](how-to-connect-health-agent-install.md#requirements) voor meer informatie.
* Uitgaande communicatie wordt onderworpen aan een TLS-inspectie door de netwerklaag. Dit zorgt ervoor dat het certificaat dat de agent gebruikt wordt vervangen door de inspectieserver/entiteit en de stappen om de agentregistratie te voltooien mislukken.
* De gebruiker heeft geen toegang tot de registratie van de agent. Globale beheerders hebben standaard toegang. U [Op rollen gebaseerd toegangsbeheer](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) gebruiken om toegang te delegeren aan andere gebruikers.

**V: Ik word gewaarschuwd dat "Health Service-gegevens niet up-to-date zijn." Hoe los ik het probleem op?**

Azure AD Connect Health genereert de waarschuwing wanneer deze niet alle gegevenspunten van de server in de afgelopen twee uur ontvangt. [Lees meer](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Operationele vragen
**V: Moet ik controle inschakelen op de proxyservers van de webtoepassing?**

Nee, auditing hoeft niet te worden ingeschakeld op de proxyservers van de webtoepassing.

**V: Hoe worden Azure AD Connect-statuswaarschuwingen opgelost?**

Azure AD Connect-statuswaarschuwingen worden opgelost op basis van een succesvoorwaarde. Azure AD Connect-statusagents detecteren en rapporteren de succesvoorwaarden periodiek aan de service. Voor een paar waarschuwingen is de onderdrukking op tijd gebaseerd. Met andere woorden, als dezelfde foutvoorwaarde niet binnen 72 uur na het genereren van waarschuwingen wordt waargenomen, wordt de waarschuwing automatisch opgelost.

**V: Ik krijg een waarschuwing dat 'Test Authentication Request (Synthetic Transaction) geen token heeft gekregen'. Hoe los ik het probleem op?**

Azure AD Connect Health for AD FS genereert deze waarschuwing wanneer de statusagent die is geïnstalleerd op een AD FS-server geen token kan verkrijgen als onderdeel van een synthetische transactie die is gestart door de statusagent. De agent Gezondheid gebruikt de lokale systeemcontext en probeert een token te krijgen voor een zelfvertrouwende partij. Dit is een catch-all test om ervoor te zorgen dat AD FS zich in een staat van uitgifte van tokens bevindt.

Meestal mislukt deze test omdat de statusagent de naam van de AD FS-boerderij niet kan oplossen. Dit kan gebeuren als de AD FS-servers zich achter een netwerkloadbalancers bevinden en de aanvraag wordt gestart vanaf een knooppunt dat achter de load balancer zit (in tegenstelling tot een gewone client die zich voor de load balancer bevindt). Dit kan worden opgelost door het bestand 'hosts' onder 'C:\Windows\System32\drivers\etc' bij te werken om het IP-adres van de AD FS-server of een loopback IP-adres (127.0.0.1) voor de naam van de AD FS-farm (zoals sts.contoso.com) op te nemen. Als u het hostbestand toevoegt, wordt het netwerkgesprek kortgesloten, waardoor de statusagent het token kan krijgen.

**V: Ik kreeg een e-mail met vermelding van mijn machines zijn niet gepatcht voor de recente ransomware aanvallen. Waarom heb ik deze e-mail ontvangen?**

Azure AD Connect Health-service heeft alle machines gescand die worden bewaakt om ervoor te zorgen dat de vereiste patches zijn geïnstalleerd. De e-mail is naar de tenantbeheerders verzonden als ten minste één machine niet over de kritieke patches beschikte. De volgende logica werd gebruikt om deze bepaling te maken.
1. Zoek alle hotfixes die op de machine zijn geïnstalleerd.
2. Controleer of ten minste één van de HotFixes uit de gedefinieerde lijst aanwezig is.
3. Zo ja, dan is de machine beschermd. Zo niet, dan loopt de machine gevaar voor de aanval.

U het volgende PowerShell-script gebruiken om deze controle handmatig uit te voeren. Het implementeert de bovenstaande logica.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**V: Waarom vertoont de PowerShell-cmdlet <i>Get-MsolDirSyncProvisioningError</i> minder synchronisatiefouten in het resultaat?**

<i>Get-MsolDirSyncProvisioningError</i> retourneert alleen DirSync-provisioningfouten. Daarnaast toont connect health portal ook andere synchronisatiefouttypen, zoals exportfouten. Dit komt overeen met het deltaresultaat van Azure AD Connect. Lees meer over [Azure AD Connect-synchronisatiefouten](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**V: Waarom worden mijn ADFS-audits niet gegenereerd?**

Gebruik PowerShell-cmdlet <i>Get-AdfsProperties -AuditLevel</i> om ervoor te zorgen dat controlelogboeken niet uitgeschakeld zijn. Lees meer over [ADFS-controlelogboeken](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Als er geavanceerde controle-instellingen naar de ADFS-server worden gepusht, worden eventuele wijzigingen met auditpol.exe overschreven (gebeurtenis als de gegenereerde toepassing niet is geconfigureerd). Stel in dit geval het lokale beveiligingsbeleid in om door toepassing gegenereerde fouten en succes te registreren.

**V: Wanneer wordt het agentcertificaat automatisch verlengd voor het verstrijken?**
De agentcertificering wordt **6 maanden** voor de vervaldatum automatisch verlengd. Als deze niet wordt verlengd, moet u ervoor zorgen dat de netwerkverbinding van de agent stabiel is. Start de agentservices opnieuw of update naar de nieuwste versie kan ook het probleem oplossen.


## <a name="related-links"></a>Verwante koppelingen
* [Azure AD Connect Health (Engelstalig)](whatis-hybrid-identity-health.md)
* [Installatie van Azure AD Connect Health Agent](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health-bewerkingen](how-to-connect-health-operations.md)
* [Azure AD Connect Health gebruiken met AD FS](how-to-connect-health-adfs.md)
* [Azure AD Connect-status gebruiken voor synchronisatie](how-to-connect-health-sync.md)
* [Azure AD Connect Health gebruiken met AD DS](how-to-connect-health-adds.md)
* [Versiegeschiedenis van Azure AD Connect Health](reference-connect-health-version-history.md)
