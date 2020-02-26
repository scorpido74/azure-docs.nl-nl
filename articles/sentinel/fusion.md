---
title: Geavanceerde aanvals detectie in azure-Sentinel
description: Gebruik fusie technologie in azure Sentinel om waarschuwings-intensiefheid te verminderen en incidenten te maken die zijn gebaseerd op geavanceerde aanvals detectie.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587920"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Geavanceerde aanvals detectie in azure-Sentinel


> [!IMPORTANT]
> Sommige Fusion-functies in azure Sentinel zijn momenteel beschikbaar als open bare preview.
> Deze functies worden zonder service level agreement gegeven en worden niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.



Door gebruik te maken van fusie technologie die is gebaseerd op machine learning, kan Azure Sentinel automatisch meerdere fase aanvallen detecteren door afwijkend gedrag en verdachte activiteiten te combi neren die in verschillende stadia van de Kill-keten worden waargenomen. Azure Sentinel genereert vervolgens incidenten die op een andere manier moeilijk te ondervangen zijn. Deze incidenten omsluiten twee of meer waarschuwingen of activiteiten. Het ontwerp van deze incidenten is een laag volume, een hoge mate van kwaliteit en een hoge ernst.

Deze detectie verkleint uw omgeving, maar vermindert niet alleen valse positieve tarieven, maar kan ook aanvallen met beperkte of ontbrekende gegevens detecteren.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuratie voor geavanceerde detectie van aanvallen met een andere fase

Deze detectie is standaard ingeschakeld in azure Sentinel. Gebruik de volgende instructies om de status te controleren of het mogelijk uit te scha kelen omdat u een alternatieve oplossing gebruikt om incidenten te maken op basis van meerdere waarschuwingen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als u dat nog niet hebt gedaan.

2. Ga naar **Azure Sentinel** > **Configuration** > **Analytics**

3. Selecteer **actieve regels** en zoek in de kolom **naam** de **Geavanceerde detectie van aanvallen** met meerdere fasen. Controleer de kolom **status** om te controleren of deze detectie is ingeschakeld of uitgeschakeld.

4. Als u de status wilt wijzigen **, selecteert u**dit item en selecteert u op de Blade **Geavanceerde aanvals detectie** .

5. Op de Blade wizard voor het **maken van regels** wordt de status wijziging automatisch voor u geselecteerd, dus Selecteer **volgende: controleren**en vervolgens **Opslaan**. 

Regel sjablonen zijn niet van toepassing op de geavanceerde detectie van de multifase-aanval.

> [!NOTE]
> Azure Sentinel maakt momenteel gebruik van 30 dagen aan historische gegevens om de machine learning systemen te trainen. Deze gegevens worden altijd versleuteld met behulp van de sleutels van micro soft, zoals door de machine learning pijp lijn wordt door gegeven. De trainings gegevens worden echter niet versleuteld met behulp van door de [klant beheerde sleutels (CMK)](customer-managed-keys.md) als u CMK hebt ingeschakeld in uw Azure Sentinel-werk ruimte. Als u wilt deel nemen aan fusie, gaat u naar **Azure Sentinel** \> **configuratie** \> **Analytics \> actieve regels \> geavanceerde aanval** van meerdere fasen en selecteert u in de kolom **status** de optie **uitschakelen.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusie met Palo Alto-netwerken en micro soft Defender ATP

In deze scenario's worden twee van de fundamentele logboeken gecombineerd die worden gebruikt door beveiligings analisten: Firewall logboeken van Palo Alto-netwerken en logboeken voor eindpunt detectie vanuit micro soft Defender ATP. In alle scenario's die hieronder worden weer gegeven, wordt er een verdachte activiteit gedetecteerd in het eind punt met een extern IP-adres. vervolgens wordt dit gevolgd door afwijkend verkeer van het externe IP-adres terug naar de firewall. In Palo Alto-Logboeken wordt Azure Sentinel gericht op [bedreigings logboeken](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)en verkeer wordt beschouwd als verdacht Wanneer bedreigingen zijn toegestaan (verdachte gegevens, bestanden, flooden, pakketten, scans, spyware, url's, virussen, beveiligings problemen, bosbrand-virussen, Wildfires).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Netwerk aanvraag naar TOR anoniem maken-service, gevolgd door afwijkend verkeer dat is gemarkeerd met de firewall van Palo Alto Networks.

In dit scenario detecteert Azure Sentinel eerst een waarschuwing dat er een netwerk aanvraag naar een TOR anoniem maken-service is gedetecteerd die leidt tot afwijkende activiteiten. Dit is gestart onder account {account naam} met SID-ID {sid} om {time}. Het uitgaande IP-adres voor de verbinding is {IndividualIp}.
Vervolgens is ongebruikelijke activiteit gedetecteerd door de firewall Palo Alto Networks op {TimeGenerated}. Dit duidt op schadelijk verkeer dat uw netwerk heeft opgegeven het doel-IP-adres voor het netwerk verkeer is {DestinationIP}.

Dit scenario is momenteel beschikbaar als open bare preview.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Power Shell heeft een verdachte netwerk verbinding gemaakt, gevolgd door afwijkend verkeer dat is gemarkeerd met de firewall Palo Alto Networks.

In dit scenario detecteert Azure Sentinel eerst een waarschuwing dat er door Power shell een verdachte netwerk verbinding is gemaakt die leidt tot afwijkende activiteiten die zijn gedetecteerd door een Palo Alto-netwerk firewall. Dit is gestart door het account {account naam} met SID-ID {sid} om {time}. Het uitgaande IP-adres voor de verbinding is {IndividualIp}. Vervolgens is ongebruikelijke activiteit gedetecteerd door de firewall Palo Alto Networks op {TimeGenerated}. Dit geeft aan dat het schadelijke verkeer uw netwerk heeft binnengekomen. Het doel-IP-adres voor het netwerk verkeer is {DestinationIP}.

Dit scenario is momenteel beschikbaar als open bare preview.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Uitgaande verbinding met IP met een geschiedenis van ongeoorloofde toegangs pogingen gevolgd door afwijkend verkeer dat is gemarkeerd door de firewall Palo Alto Networks

In dit scenario detecteert Azure Sentinel een waarschuwing dat een uitgaande verbinding met een IP-adres door micro soft Defender Advanced Threat Protection is gedetecteerd met een geschiedenis van ongeoorloofde toegangs pogingen die leiden tot afwijkende activiteiten die worden gedetecteerd door de Palo Alto Netwerk firewall. Dit is gestart door het account {account naam} met SID-ID {sid} om {time}. Het uitgaande IP-adres voor de verbinding is {IndividualIp}. Hierna is ongebruikelijke activiteit gedetecteerd door de firewall Palo Alto Networks op {TimeGenerated}. Dit geeft aan dat het schadelijke verkeer uw netwerk heeft binnengekomen. Het doel-IP-adres voor het netwerk verkeer is {DestinationIP}.

Dit scenario is momenteel beschikbaar als open bare preview.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusie met identiteits beveiliging en Microsoft Cloud App Security

Met geavanceerde detectie van aanvallen met meerdere fasen ondersteunt Azure Sentinel de volgende scenario's die afwijkende gebeurtenissen van Azure Active Directory Identity Protection en Microsoft Cloud App Security combi neren:

- [Onmogelijke reis naar ongewone locatie, gevolgd door afwijkende Office 365-activiteit](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Aanmeldings activiteit voor onbekende locatie gevolgd door afwijkende Office 365-activiteit](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Aanmeldings activiteiten vanaf geïnfecteerd apparaat, gevolgd door afwijkende Office 365-activiteit](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Aanmeldings activiteit vanaf een anoniem IP-adres, gevolgd door afwijkende Office 365-activiteit](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Aanmeldings activiteit van de gebruiker met de gelekte referenties, gevolgd door een afwijkend Office 365-activiteit](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

U moet beschikken over de [Azure AD Identity Protection Data Connector](connect-azure-ad-identity-protection.md) en de [Cloud app Security](connect-cloud-app-security.md) -connectors zijn geconfigureerd.

In de volgende beschrijvingen geeft Azure Sentinel de werkelijke waarde weer van uw gegevens die op deze pagina worden weer gegeven als variabelen tussen vier Kante haken. Bijvoorbeeld, de werkelijke weergave naam van een account in plaats van \<*account naam*> en het werkelijke nummer in plaats van \<*nummer*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Onmogelijke reis naar ongewone locatie, gevolgd door afwijkende Office 365-activiteit

Er zijn zeven mogelijke Azure-Sentinel-incidenten die onmogelijke reis naar ongewone locatie waarschuwingen combi neren van Azure AD Identity Protection en afwijkende Office 365-waarschuwingen die door Microsoft Cloud App Security worden gegenereerd:

- **Onmogelijke reis naar ongewone locaties die leiden tot Office 365-postvak exfiltration**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een onmogelijk traject naar \<*locatie*>, een ongewoone locatie, gevolgd door een verdachte postvak-doorstuur regel, ingesteld op het postvak in van een gebruiker.
    
    Dit kan erop wijzen dat het account is aangetast en dat het postvak wordt gebruikt om gegevens van uw organisatie te exfiltreren. De gebruikers \<*account naam*> een regel voor het door sturen van het postvak in gemaakt of bijgewerkt waarmee alle binnenkomende e-mails worden doorgestuurd naar het externe adres \<*e-mail adres*>.

- **Onmogelijke reis naar ongewone locaties die leiden tot verdachte Cloud app-beheer activiteiten**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een onmogelijke reis naar \<*locatie*>, een ongewoone locatie.
    
    Vervolgens wordt het account \<*account naam*> uitgevoerd via \<*nummer*> beheer activiteiten tijdens één sessie.

- **Onmogelijke reis naar ongewone locaties die leiden tot massale bestands verwijdering**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> naar \<*locatie*>, een ongewoone locatie. 
    
    Vervolgens wordt het account \<*account naam*> een \<*aantal*> unieke bestanden verwijderd tijdens één sessie.

- **Onmogelijke reis naar ongewone locaties die leiden tot massale bestands downloads**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een onmogelijke reis naar \<*locatie*>, een ongewoone locatie. 
    
    Vervolgens wordt het account \<*account naam*> gedownload via \<*aantal*> unieke bestanden tijdens één sessie.

- **Onmogelijke reis naar ongewone locaties die leiden tot Office 365-imitatie**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een onmogelijke reis naar \<*locatie*>, een ongewoone locatie. 
    
    Vervolgens wordt het account \<*account naam*> een ongebruikelijke hoeveelheid (\<*aantal activiteiten*>) van imitatie activiteiten in één sessie uitgevoerd.

- **Onmogelijke reis naar ongewone locaties die leiden tot massale bestands deling**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een onmogelijke reis naar \<*locatie*>, een ongewoone locatie. 
    
    Vervolgens wordt het account \<*account naam*> gedeeld via \<*aantal*> unieke bestanden tijdens één sessie.

- **Onmogelijke reis naar ongewone locaties die leiden tot Ransomware in de Cloud-app**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een onmogelijke reis naar \<*locatie*>, een ongewoone locatie. 
    
    Vervolgens wordt het account \<*account naam*> geüpload \<*aantal*> bestanden, en wordt het totale *aantal \<bestanden*verwijderd. 
    
    Dit activiteiten patroon is indicatief voor een mogelijke Ransomware-aanval.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Aanmeldings activiteit voor onbekende locatie gevolgd door afwijkende Office 365-activiteit

Er zijn zeven mogelijke Azure-Sentinel-incidenten die aanmeldings activiteiten combi neren voor onbekende locatie waarschuwingen van Azure AD Identity Protection en afwijkende Office 365-waarschuwingen die door Microsoft Cloud App Security worden gegenereerd.

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot Exchange Online-postvak exfiltration**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van \<*locatie*>, een onbekende locatie, gevolgd door een verdachte postvak-doorstuur regel, ingesteld op het postvak in van een gebruiker.
    
    Dit kan erop wijzen dat het account is aangetast en dat het postvak wordt gebruikt om gegevens van uw organisatie te exfiltreren. De gebruikers \<*account naam*> een regel voor het door sturen van het postvak in gemaakt of bijgewerkt waarmee alle binnenkomende e-mails worden doorgestuurd naar het externe adres \<*e-mail adres*>. 

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot verdachte Cloud app-beheer activiteiten**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van \<*locatie*>, een onbekende locatie. 
    
    Vervolgens wordt het account \<*account naam*> uitgevoerd via \<*aantal*> beheer activiteiten tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot massale bestands verwijdering**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van \<*locatie*>, een onbekende locatie. 
    
    Vervolgens wordt het account \<*account naam*> een \<*aantal*> unieke bestanden verwijderd tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot massale bestands downloads**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van \<*locatie*>, een onbekende locatie. 
    
    Vervolgens wordt het account \<*account naam*> gedownload via \<*aantal*> unieke bestanden tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot Office 365-imitatie**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van \<*locatie*>, een onbekende locatie.
    
    Vervolgens wordt het account \<*account naam*> geïmiteerd \<*aantal*> accounts in één sessie.

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot massaal delen van bestanden**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van \<*locatie*>, een onbekende locatie. 
    
    Vervolgens wordt het account \<*account naam*> gedeeld via \<*aantal*> unieke bestanden tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot Ransomware in de Cloud-app**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van \<*locatie*>, een onbekende locatie. 
    
    Vervolgens wordt het account \<*account naam*> geüpload \<*aantal*> bestanden, en wordt het totale *aantal \<bestanden*verwijderd. 
    
    Dit activiteiten patroon is indicatief voor een mogelijke Ransomware-aanval.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Aanmeldings activiteiten vanaf geïnfecteerd apparaat, gevolgd door afwijkende Office 365-activiteit

Er zijn zeven mogelijke Azure-Sentinel-incidenten die aanmeldings activiteiten combi neren van geïnfecteerde apparaatstuurprogramma's van Azure AD Identity Protection en afwijkende Office 365-waarschuwingen die zijn gegenereerd door Microsoft Cloud App Security:

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat dat het Office 365-postvak exfiltration**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een apparaat dat mogelijk is geïnfecteerd met malware, gevolgd door een verdachte doorstuur regel voor Postvak in, ingesteld in het postvak in van een gebruiker.
    
    Dit kan erop wijzen dat het account is aangetast en dat het postvak wordt gebruikt om gegevens van uw organisatie te exfiltreren. De gebruikers \<*account naam*> een regel voor het door sturen van het postvak in gemaakt of bijgewerkt waarmee alle binnenkomende e-mails worden doorgestuurd naar het externe adres \<*e-mail adres*>. 

- **Aanmeldings gebeurtenis van een geïnfecteerd apparaat die tot verdachte Cloud app-beheer activiteit leidt**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een apparaat dat mogelijk is geïnfecteerd met schadelijke software.
    
    Vervolgens wordt het account \<*account naam*> uitgevoerd via \<*aantal*> beheer activiteiten tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor massale bestanden kunnen worden verwijderd**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een apparaat dat mogelijk is geïnfecteerd met schadelijke software. 
    
    Vervolgens wordt het account \<*account naam*> een \<*aantal*> unieke bestanden verwijderd tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor massale bestanden kunnen worden gedownload**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een apparaat dat mogelijk is geïnfecteerd met schadelijke software. 
    
    Vervolgens wordt het account \<*account naam*> gedownload via \<*aantal*> unieke bestanden tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat die leidt tot Office 365-imitatie**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een apparaat dat mogelijk is geïnfecteerd met schadelijke software. 
    
    Vervolgens wordt het account \<*account naam*> geïmiteerd \<*aantal*> accounts in één sessie.

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor massa bestanden kunnen worden gedeeld**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een apparaat dat mogelijk is geïnfecteerd met schadelijke software. 
    
    Vervolgens wordt het account \<*account naam*> gedeeld via \<*aantal*> unieke bestanden tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat die tot Ransomware kan leiden in de Cloud-app**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een apparaat dat mogelijk is geïnfecteerd met schadelijke software. 
    
    Vervolgens wordt het account \<*account naam*> geüpload \<*aantal*> bestanden, en wordt het totale *aantal \<bestanden*verwijderd. 
    
    Dit activiteiten patroon is indicatief voor een mogelijke Ransomware-aanval.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Aanmeldings activiteit vanaf een anoniem IP-adres, gevolgd door afwijkende Office 365-activiteit

Er zijn zeven mogelijke Azure-Sentinel-incidenten die aanmeldings activiteiten combi neren van anonieme IP-adres waarschuwingen van Azure AD Identity Protection en afwijkende Office 365-waarschuwingen die door Microsoft Cloud App Security worden gegenereerd:

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot Office 365-postvak exfiltration**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een IP-adres van een anonieme proxy \<*ip-adres*>, gevolgd door een verdachte postvak in-doorstuur regel, ingesteld op het postvak in van een gebruiker.
    
    Dit kan erop wijzen dat het account is aangetast en dat het postvak wordt gebruikt om gegevens van uw organisatie te exfiltreren. De gebruikers \<*account naam*> een regel voor het door sturen van het postvak in gemaakt of bijgewerkt waarmee alle binnenkomende e-mails worden doorgestuurd naar het externe adres \<*e-mail adres*>. 

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot verdachte Cloud app-beheer activiteiten**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een IP-adres van een anonieme proxy \<*ip-adres*>. 
    
    Vervolgens wordt het account \<*account naam*> uitgevoerd via \<*aantal*> beheer activiteiten tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot massale bestands verwijdering**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een IP-adres van een anonieme proxy \<*ip-adres*>. 
    
    Vervolgens wordt het account \<*account naam*> een \<*aantal*> unieke bestanden verwijderd tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot massale bestands downloads**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een IP-adres van een anonieme proxy \<*ip-adres*>. 
    
    Vervolgens wordt het account \<*account naam*> gedownload via \<*aantal*> unieke bestanden tijdens één sessie.

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot Office 365-imitatie**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een IP-adres van een anonieme proxy \<*ip-adres*>. 
    
    Vervolgens wordt het account \<*account naam*> geïmiteerd \<*aantal*> accounts in één sessie.

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot massaal delen van bestanden**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een IP-adres van een anonieme proxy \<*ip-adres*>. 
    
    Vervolgens wordt het account \<*account naam*> gedeeld via \<*aantal*> unieke bestanden tijdens één sessie.

- **Aanmeldings gebeurtenis van een anoniem IP-adres naar Ransomware in de Cloud-app**
    
    Deze waarschuwing geeft een indicatie van een aanmeldings gebeurtenis door \<*account naam*> van een IP-adres van een anonieme proxy \<*ip-adres*>. 
    
    Vervolgens wordt het account \<*account naam*> geüpload \<*aantal*> bestanden, en wordt het totale *aantal \<bestanden*verwijderd. 
    
    Dit activiteiten patroon is indicatief voor een mogelijke Ransomware-aanval.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Aanmeldings activiteit van de gebruiker met de gelekte referenties, gevolgd door een afwijkend Office 365-activiteit

Er zijn zeven mogelijke Azure-Sentinel-incidenten die aanmeldings activiteiten combi neren van de gebruiker met gelekte aanmeldings gegevens van Azure AD Identity Protection en afwijkende Office 365-waarschuwingen die door Microsoft Cloud App Security worden gegenereerd:

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties die het gevolg zijn van het Office 365-postvak exfiltration**
    
    Deze waarschuwing geeft aan dat de aanmeldings gebeurtenis door \<*account naam*> gebruikte gelekte referenties, gevolgd door een verdachte postvak in-doorstuur regel, is ingesteld in het postvak in van een gebruiker. 
    
    Dit kan erop wijzen dat het account is aangetast en dat het postvak wordt gebruikt om gegevens van uw organisatie te exfiltreren. De gebruikers \<*account naam*> een regel voor het door sturen van het postvak in gemaakt of bijgewerkt waarmee alle binnenkomende e-mails worden doorgestuurd naar het externe adres \<*e-mail adres*>. 

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor de verdachte Cloud app Administrative-activiteit**
    
    Deze waarschuwing geeft aan dat de aanmeldings gebeurtenis door \<*account naam*> gebruikte gelekte referenties.
    
    Vervolgens wordt het account \<*account naam*> uitgevoerd via \<*aantal*> beheer activiteiten tijdens één sessie.

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor het verwijderen van massale bestanden**
    
    Deze waarschuwing geeft aan dat de aanmeldings gebeurtenis door \<*account naam*> gebruikte gelekte referenties.
    
    Vervolgens wordt het account \<*account naam*> een \<*aantal*> unieke bestanden verwijderd tijdens één sessie.

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor het downloaden van massale bestanden**
    
    Deze waarschuwing geeft aan dat de aanmeldings gebeurtenis door \<*account naam*> gebruikte gelekte referenties.
    
    Vervolgens wordt het account \<*account naam*> gedownload via \<*aantal*> unieke bestanden tijdens één sessie.

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties die het gevolg zijn van een Office 365-imitatie**
    
    Deze waarschuwing geeft aan dat de aanmeldings gebeurtenis door \<*account naam*> gebruikte gelekte referenties. 
    
    Vervolgens wordt het account \<*account naam*> geïmiteerd \<*aantal*> accounts in één sessie.

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor het delen van massa bestanden**
    
    Deze waarschuwing geeft aan dat de aanmeldings gebeurtenis door \<*account naam*> gebruikte gelekte referenties.
    
    Vervolgens wordt het account \<*account naam*> gedeeld via \<*aantal*> unieke bestanden tijdens één sessie.

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor Ransomware in de Cloud-app**
    
    Deze waarschuwing geeft aan dat de aanmeldings gebeurtenis door \<*account naam*> gebruikte gelekte referenties. 
    
    Vervolgens wordt het account \<*account naam*> geüpload \<*aantal*> bestanden, en wordt het totale *aantal \<bestanden*verwijderd. 
    
    Dit activiteiten patroon is indicatief voor een mogelijke Ransomware-aanval.

## <a name="next-steps"></a>Volgende stappen

Nu u meer hebt geleerd over geavanceerde detectie van aanvallen met meerdere fasen, bent u mogelijk geïnteresseerd in de volgende Snelstartgids om te leren hoe u uw gegevens en mogelijke bedreigingen kunt zien: aan de [slag met Azure Sentinel](quickstart-get-visibility.md).

Als u klaar bent voor het onderzoeken van de incidenten die voor u zijn gemaakt, raadpleegt u de volgende zelf studie: [incidenten onderzoeken met Azure Sentinel](tutorial-investigate-cases.md).

