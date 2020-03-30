---
title: Geavanceerde multistage-aanvalsdetectie in Azure Sentinel
description: Gebruik Fusion-technologie in Azure Sentinel om waarschuwingsvermoeidheid te verminderen en bruikbare incidenten te maken die zijn gebaseerd op geavanceerde multistage-aanvalsdetectie.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587920"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Geavanceerde multistage-aanvalsdetectie in Azure Sentinel


> [!IMPORTANT]
> Sommige Fusion-functies in Azure Sentinel zijn momenteel in openbare preview.
> Deze functies worden geleverd zonder een serviceniveauovereenkomst en worden niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.



Door fusietechnologie te gebruiken die is gebaseerd op machine learning, kan Azure Sentinel automatisch aanvallen op meerdere podia detecteren door afwijkend gedrag en verdachte activiteiten te combineren die in verschillende stadia van de kill-chain worden waargenomen. Azure Sentinel genereert vervolgens incidenten die anders zeer moeilijk te vangen zouden zijn. Bij deze incidenten zijn twee of meer waarschuwingen of activiteiten nodig. Door het ontwerp, deze incidenten zijn laag volume, high fidelity, en hoge ernst.

Aangepast voor uw omgeving, deze detectie vermindert niet alleen vals-positieve tarieven, maar kan ook aanvallen detecteren met beperkte of ontbrekende informatie.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuratie voor geavanceerde detectie van aanvallen met meerdere fasen

Deze detectie is standaard ingeschakeld in Azure Sentinel. Gebruik de volgende instructies om de status te controleren of uit te schakelen omdat u een alternatieve oplossing gebruikt om incidenten te maken op basis van meerdere waarschuwingen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als u dat nog niet hebt gedaan.

2. Navigeren naar **Azure Sentinel** > **Configuration** > **Analytics**

3. Selecteer **Actieve regels** en zoek Geavanceerde **multistage-aanvalsdetectie** in de kolom **NAAM.** Controleer de kolom **STATUS** om te controleren of deze detectie is ingeschakeld of uitgeschakeld.

4. Als u de status wilt wijzigen, selecteert u dit item en selecteert u op het geavanceerde **mes voor aanvalsdetectie op meerdere plaatsen** de optie **Bewerken**.

5. In het **wizardblad regelcreatie** wordt de statuswijziging automatisch voor u geselecteerd, dus selecteer **Volgende: Controleren**en **vervolgens Opslaan**. 

Regelsjablonen zijn niet van toepassing op de geavanceerde multistage-aanvalsdetectie.

> [!NOTE]
> Azure Sentinel gebruikt momenteel 30 dagen aan historische gegevens om de machine learning-systemen te trainen. Deze gegevens worden altijd versleuteld met de sleutels van Microsoft terwijl deze door de machine learning-pijplijn gaan. De trainingsgegevens worden echter niet versleuteld met [behulp van Customer Managed Keys (CMK)](customer-managed-keys.md) als u CMK hebt ingeschakeld in uw Azure Sentinel-werkruimte. Als u zich wilt afmelden voor Fusion, navigeert u naar **Azure Sentinel** \> **Configuration** \> ** \> Analytics Active rules \> Advanced Multistage Attack Detection** en selecteert u In de kolom **Status** de optie **Uitschakelen.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusion met Palo Alto Networks en Microsoft Defender ATP

Deze scenario's combineren twee van de fundamentele logboeken die worden gebruikt door beveiligingsanalisten: firewalllogboeken van Palo Alto Networks en end-point detectielogboeken van Microsoft Defender ATP. In alle onderstaande scenario's wordt een verdachte activiteit gedetecteerd in het eindpunt waarbij een extern IP-adres is betrokken, dit wordt gevolgd door afwijkend verkeer van het externe IP-adres terug naar de firewall. In Palo Alto-logboeken richt Azure Sentinel zich op [bedreigingslogboeken](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)en wordt verkeer als verdacht beschouwd wanneer bedreigingen zijn toegestaan (verdachte gegevens, bestanden, overstromingen, pakketten, scans, spyware, URL's, virussen, kwetsbaarheden, bosbranden, bosbranden).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Netwerkaanvraag voor TOR-anonymization-service, gevolgd door afwijkend verkeer dat is gemarkeerd door de firewall van Palo Alto Networks.

In dit scenario detecteert Azure Sentinel eerst een waarschuwing dat Microsoft Defender Advanced Threat Protection een netwerkaanvraag heeft gedetecteerd voor een TOR-anonymisatieservice die leidt tot afwijkende activiteit. Dit werd gestart onder account {account name} met SID ID {sid} op {time}. Het uitgaande IP-adres van de verbinding was {IndividualIp}.
Vervolgens werd ongebruikelijke activiteit gedetecteerd door de Palo Alto Networks Firewall op {TimeGenerated}. Dit geeft aan dat er kwaadaardig verkeer in uw netwerk is ingevoerd Het doel-IP-adres voor het netwerkverkeer is {DestinationIP}.

Dit scenario bevindt zich momenteel in een openbare preview.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell maakte een verdachte netwerkverbinding gevolgd door afwijkend verkeer gemarkeerd door Palo Alto Networks firewall.

In dit scenario detecteert Azure Sentinel eerst een waarschuwing dat Microsoft Defender Advanced Threat Protection heeft gedetecteerd dat PowerShell een verdachte netwerkverbinding heeft gemaakt die leidde tot afwijkende activiteit die werd gedetecteerd door een Palo Alto Network Firewall. Dit is geïnitieerd door het account {accountname} met SID ID {sid} op {time}. Het uitgaande IP-adres van de verbinding was {IndividualIp}. Vervolgens werd ongebruikelijke activiteit gedetecteerd door de Palo Alto Networks Firewall op {TimeGenerated}. Dit geeft aan dat kwaadwillig verkeer uw netwerk is binnengekomen. Het doel-IP-adres voor het netwerkverkeer is {DestinationIP}.

Dit scenario bevindt zich momenteel in een openbare preview.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Uitgaande verbinding met IP met een geschiedenis van ongeautoriseerde toegangspogingen gevolgd door afwijkend verkeer dat is gemarkeerd door de firewall van Palo Alto Networks

In dit scenario detecteert Azure Sentinel een waarschuwing dat Microsoft Defender Advanced Threat Protection een uitgaande verbinding met een IP-adres heeft gedetecteerd met een geschiedenis van ongeautoriseerde toegangspogingen die leiden tot abnormale activiteit die wordt gedetecteerd door de Palo Alto Netwerken Firewall. Dit is geïnitieerd door het account {accountname} met SID ID {sid} op {time}. Het uitgaande IP-adres van de verbinding was {IndividualIp}. Hierna werd ongebruikelijke activiteit gedetecteerd door de Palo Alto Networks Firewall op {TimeGenerated}. Dit geeft aan dat kwaadwillig verkeer uw netwerk is binnengekomen. Het doel-IP-adres voor het netwerkverkeer is {DestinationIP}.

Dit scenario bevindt zich momenteel in een openbare preview.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusion met identity protection en Microsoft Cloud App Security

Met behulp van geavanceerde multistage-aanvalsdetectie ondersteunt Azure Sentinel de volgende scenario's die anomaliegebeurtenissen van Azure Active Directory Identity Protection en Microsoft Cloud App Security combineren:

- [Onmogelijke reizen naar atypische locatie, gevolgd door afwijkende Office 365-activiteit](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Aanmeldingsactiviteit voor onbekende locatie, gevolgd door afwijkende Office 365-activiteit](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Aanmeldingsactiviteit van geïnfecteerd apparaat, gevolgd door afwijkende Office 365-activiteit](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Aanmeldingsactiviteit vanaf anoniem IP-adres, gevolgd door afwijkende Office 365-activiteit](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Aanmeldingsactiviteit van gebruiker met gelekte referenties, gevolgd door afwijkende Office 365-activiteit](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

U moet de [azure AD Identity Protection-gegevensconnector](connect-azure-ad-identity-protection.md) en de [cloud-app-beveiligingsconnectoren](connect-cloud-app-security.md) hebben geconfigureerd.

In de volgende beschrijvingen geeft Azure Sentinel de werkelijke waarde van uw gegevens weer die op deze pagina wordt weergegeven als variabelen tussen haakjes. Bijvoorbeeld, de werkelijke weergavenaam van \<een account in plaats van \< *accountnaam*>, en het werkelijke aantal in plaats van *nummer*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Onmogelijke reizen naar atypische locatie, gevolgd door afwijkende Office 365-activiteit

Er zijn zeven mogelijke Azure Sentinel-incidenten die onmogelijk reizen combineren met atypische locatiewaarschuwingen van Azure AD Identity Protection en afwijkende Office 365-waarschuwingen die worden gegenereerd door Microsoft Cloud App Security:

- **Onmogelijke verplaatsing naar atypische locaties die leiden tot exfiltratie van Office 365-postvak**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een onmogelijke reis naar *locatie*>, een atypische locatie, gevolgd door een verdachte inbox doorstuurregel die is ingesteld op het postvak IN van een gebruiker.
    
    Dit kan erop wijzen dat het account is gecompromitteerd en dat het postvak wordt gebruikt om informatie van uw organisatie te exfiltreren. De \< *gebruikersnaam>* een doorstuurregel voor inboxen gemaakt of bijgewerkt \<die alle binnenkomende e-mail doorstuurt naar het *e-mailadres* van het externe adres>.

- **Onmogelijke reizen naar atypische locaties die leiden tot verdachte beheeractiviteiten van cloud-apps**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een onmogelijke reis naar *locatie*>, een atypische locatie.
    
    Vervolgens> \<de *accountnaam* \<uitgevoerd op *het aantal*> administratieve activiteiten in één sessie.

- **Onmogelijke reizen naar atypische locaties die leiden tot massa-verwijdering van bestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> \< *locatie*>, een atypische locatie. 
    
    Vervolgens> \<de *accountnaam* \<het *aantal*> unieke bestanden in één sessie verwijderd.

- **Onmogelijke reizen naar atypische locaties die leiden tot massa-bestand te downloaden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een onmogelijke reis naar *locatie*>, een atypische locatie. 
    
    Vervolgens> \<de *accountnaam* gedownload \<over *het aantal*> unieke bestanden in één sessie.

- **Onmogelijke reizen naar atypische locaties die leiden tot Office 365-imitatie**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een onmogelijke reis naar *locatie*>, een atypische locatie. 
    
    Vervolgens> \<de *accountnaam* een ongebruikelijk\<bedrag *(aantal activiteiten*>) van imitatieactiviteiten in één sessie uitgevoerd.

- **Onmogelijke reizen naar atypische locaties die leiden tot het delen van massabestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een onmogelijke reis naar *locatie*>, een atypische locatie. 
    
    Vervolgens> \<de *accountnaam* \<gedeeld over *het aantal*> unieke bestanden in één sessie.

- **Onmogelijke reizen naar atypische locaties die leiden tot ransomware in cloud-app**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een onmogelijke reis naar *locatie*>, een atypische locatie. 
    
    Vervolgens> \<de *accountnaam* \< *het aantal*> bestanden geüpload \<en een totaal *aantal*> bestanden verwijderd. 
    
    Deze activiteit patroon is indicatief voor een potentiële ransomware aanval.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Aanmeldingsactiviteit voor onbekende locatie, gevolgd door afwijkende Office 365-activiteit

Er zijn zeven mogelijke Azure Sentinel-incidenten die aanmeldingsactiviteit combineren voor onbekende locatiewaarschuwingen van Azure AD-identiteitsbeveiliging en afwijkende Office 365-waarschuwingen die worden gegenereerd door Microsoft Cloud App Security.

- **Aanmeldingsgebeurtenis vanaf een onbekende locatie die leidt tot exfiltratie van Exchange Online-postvak**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> vanaf \< *locatie*>, een onbekende locatie, gevolgd door een verdachte inbox doorstuurregel die is ingesteld op het postvak in van een gebruiker.
    
    Dit kan erop wijzen dat het account is gecompromitteerd en dat het postvak wordt gebruikt om informatie van uw organisatie te exfiltreren. De \< *gebruikersnaam>* een doorstuurregel voor inboxen gemaakt of bijgewerkt \<die alle binnenkomende e-mail doorstuurt naar het *e-mailadres* van het externe adres>. 

- **Aanmeldingsgebeurtenis vanaf een onbekende locatie die leidt tot verdachte beheeractiviteiten van cloud-apps**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van \< *locatie*>, een onbekende locatie. 
    
    Vervolgens> \<de *accountnaam* \<uitgevoerd ten opzichte *van het aantal*> administratieve activiteiten in één sessie.

- **Aanmeldingsgebeurtenis vanaf een onbekende locatie die leidt tot het verwijderen van massabestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van \< *locatie*>, een onbekende locatie. 
    
    Vervolgens> \<de *accountnaam* \<het *aantal*> unieke bestanden in één sessie verwijderd.

- **Aanmeldingsgebeurtenis vanaf een onbekende locatie die leidt tot het downloaden van massabestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van \< *locatie*>, een onbekende locatie. 
    
    Vervolgens> \<de *accountnaam* gedownload \<over *het aantal*> unieke bestanden in één sessie.

- **Aanmeldingsgebeurtenis vanaf een onbekende locatie die leidt tot Office 365-imitatie**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van \< *locatie*>, een onbekende locatie.
    
    Vervolgens> \<de *accountnaam* zich \<voorgedaan boven *het aantal*> verschillende accounts in één sessie.

- **Aanmeldingsgebeurtenis vanaf een onbekende locatie die leidt tot het delen van massabestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van \< *locatie*>, een onbekende locatie. 
    
    Vervolgens> \<de *accountnaam* \<gedeeld over *het aantal*> unieke bestanden in één sessie.

- **Aanmeldingsgebeurtenis vanaf een onbekende locatie die leidt tot ransomware in de cloud-app**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van \< *locatie*>, een onbekende locatie. 
    
    Vervolgens> \<de *accountnaam* \< *het aantal*> bestanden geüpload \<en een totaal *aantal*> bestanden verwijderd. 
    
    Deze activiteit patroon is indicatief voor een potentiële ransomware aanval.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Aanmeldingsactiviteit van geïnfecteerd apparaat, gevolgd door afwijkende Office 365-activiteit

Er zijn zeven mogelijke Azure Sentinel-incidenten die aanmeldingsactiviteit combineren van geïnfecteerde apparaatwaarschuwingen van Azure AD-identiteitsbeveiliging en afwijkende Office 365-waarschuwingen die worden gegenereerd door Microsoft Cloud App Security:

- **Aanmeldingsgebeurtenis vanaf een geïnfecteerd apparaat dat leidt tot exfiltratie van Office 365-postvak**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van een apparaat dat mogelijk met malware is geïnfecteerd, gevolgd door een verdachte inbox doorstuurregel die is ingesteld op het postvak IN van een gebruiker.
    
    Dit kan erop wijzen dat het account is gecompromitteerd en dat het postvak wordt gebruikt om informatie van uw organisatie te exfiltreren. De \< *gebruikersnaam>* een doorstuurregel voor inboxen gemaakt of bijgewerkt \<die alle binnenkomende e-mail doorstuurt naar het *e-mailadres* van het externe adres>. 

- **Aanmeldingsgebeurtenis vanaf een geïnfecteerd apparaat dat leidt tot verdachte administratieve activiteit in de cloud-app**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van een apparaat dat mogelijk met malware is geïnfecteerd.
    
    Vervolgens> \<de *accountnaam* \<uitgevoerd ten opzichte *van het aantal*> administratieve activiteiten in één sessie.

- **Aanmeldingsgebeurtenis vanaf een geïnfecteerd apparaat dat leidt tot het verwijderen van massabestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van een apparaat dat mogelijk met malware is geïnfecteerd. 
    
    Vervolgens> \<de *accountnaam* \<het *aantal*> unieke bestanden in één sessie verwijderd.

- **Aanmeldingsgebeurtenis vanaf een geïnfecteerd apparaat dat leidt tot het downloaden van massabestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van een apparaat dat mogelijk met malware is geïnfecteerd. 
    
    Vervolgens> \<de *accountnaam* gedownload \<over *het aantal*> unieke bestanden in één sessie.

- **Aanmeldingsgebeurtenis vanaf een geïnfecteerd apparaat dat leidt tot Office 365-imitatie**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van een apparaat dat mogelijk met malware is geïnfecteerd. 
    
    Vervolgens> \<de *accountnaam* zich \<voorgedaan boven *het aantal*> verschillende accounts in één sessie.

- **Aanmeldingsgebeurtenis vanaf een geïnfecteerd apparaat dat leidt tot het delen van massabestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van een apparaat dat mogelijk met malware is geïnfecteerd. 
    
    Vervolgens> \<de *accountnaam* \<gedeeld over *het aantal*> unieke bestanden in één sessie.

- **Aanmeldingsgebeurtenis vanaf een geïnfecteerd apparaat dat leidt tot ransomware in de cloud-app**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van een apparaat dat mogelijk met malware is geïnfecteerd. 
    
    Vervolgens> \<de *accountnaam* \< *het aantal*> bestanden geüpload \<en een totaal *aantal*> bestanden verwijderd. 
    
    Deze activiteit patroon is indicatief voor een potentiële ransomware aanval.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Aanmeldingsactiviteit vanaf anoniem IP-adres, gevolgd door afwijkende Office 365-activiteit

Er zijn zeven mogelijke Azure Sentinel-incidenten die aanmeldingsactiviteiten combineren van anonieme IP-adreswaarschuwingen van Azure AD-identiteitsbeveiliging en afwijkende Office 365-waarschuwingen die worden gegenereerd door Microsoft Cloud App Security:

- **Aanmeldingsgebeurtenis vanaf een anoniem IP-adres dat leidt tot exfiltratie van Office 365-postvak**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op *accountnaam*> van een anoniem IP-adres van een \< *proxy-IP-adres*>, gevolgd door een verdachte inbox doorstuurregel die is ingesteld op het postvak IN van een gebruiker.
    
    Dit kan erop wijzen dat het account is gecompromitteerd en dat het postvak wordt gebruikt om informatie van uw organisatie te exfiltreren. De \< *gebruikersnaam>* een doorstuurregel voor inboxen gemaakt of bijgewerkt \<die alle binnenkomende e-mail doorstuurt naar het *e-mailadres* van het externe adres>. 

- **Aanmeldingsgebeurtenis vanaf een anoniem IP-adres dat leidt tot verdachte beheeractiviteiten van cloud-apps**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een anoniem IP-adres van een *proxy-IP-adres*>. 
    
    Vervolgens> \<de *accountnaam* \<uitgevoerd ten opzichte *van het aantal*> administratieve activiteiten in één sessie.

- **Aanmeldingsgebeurtenis vanaf een anoniem IP-adres dat leidt tot het verwijderen van massabestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een anoniem IP-adres van een *proxy-IP-adres*>. 
    
    Vervolgens> \<de *accountnaam* \<het *aantal*> unieke bestanden in één sessie verwijderd.

- **Aanmeldingsgebeurtenis vanaf een anoniem IP-adres dat leidt tot het downloaden van massabestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een anoniem IP-adres van een *proxy-IP-adres*>. 
    
    Vervolgens> \<de *accountnaam* gedownload \<over *het aantal*> unieke bestanden in één sessie.

- **Aanmeldingsgebeurtenis vanaf een anoniem IP-adres dat leidt naar Office 365-imitatie**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een anoniem IP-adres van een *proxy-IP-adres*>. 
    
    Vervolgens> \<de *accountnaam* zich \<voorgedaan boven *het aantal*> verschillende accounts in één sessie.

- **Aanmeldingsgebeurtenis vanaf een anoniem IP-adres dat leidt tot het delen van massabestanden**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een anoniem IP-adres van een *proxy-IP-adres*>. 
    
    Vervolgens> \<de *accountnaam* \<gedeeld over *het aantal*> unieke bestanden in één sessie.

- **Aanmeldingsgebeurtenis van een anoniem IP-adres naar ransomware in de cloud-app**
    
    Deze waarschuwing is een indicatie van \<een aanmeldingsgebeurtenis op \< *accountnaam*> van een anoniem IP-adres van een *proxy-IP-adres*>. 
    
    Vervolgens> \<de *accountnaam* \< *het aantal*> bestanden geüpload \<en een totaal *aantal*> bestanden verwijderd. 
    
    Deze activiteit patroon is indicatief voor een potentiële ransomware aanval.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Aanmeldingsactiviteit van gebruiker met gelekte referenties, gevolgd door afwijkende Office 365-activiteit

Er zijn zeven mogelijke Azure Sentinel-incidenten die aanmeldingsactiviteit van de gebruiker combineren met gelekte referentieswaarschuwingen van Azure AD-identiteitsbeveiliging en afwijkende Office 365-waarschuwingen die worden gegenereerd door Microsoft Cloud App Security:

- **Aanmeldingsgebeurtenis van gebruiker met gelekte referenties die leiden tot exfiltratie van Office 365-postvak**
    
    Deze waarschuwing is een indicatie dat \<de aanmeldingsgebeurtenis op *accountnaam*> gelekte referenties heeft gebruikt, gevolgd door een verdachte inbox doorstuurregel die is ingesteld op het postvak IN van een gebruiker. 
    
    Dit kan erop wijzen dat het account is gecompromitteerd en dat het postvak wordt gebruikt om informatie van uw organisatie te exfiltreren. De \< *gebruikersnaam>* een doorstuurregel voor inboxen gemaakt of bijgewerkt \<die alle binnenkomende e-mail doorstuurt naar het *e-mailadres* van het externe adres>. 

- **Aanmeldingsgebeurtenis van gebruiker met gelekte referenties die leiden tot verdachte beheeractiviteit van cloud-apps**
    
    Deze waarschuwing is een indicatie dat \<de aanmeldingsgebeurtenis op *accountnaam*> gelekte referenties heeft gebruikt.
    
    Vervolgens> \<de *accountnaam* \<uitgevoerd ten opzichte *van het aantal*> administratieve activiteiten in één sessie.

- **Aanmeldingsgebeurtenis van gebruiker met gelekte referenties die leiden tot het verwijderen van massabestanden**
    
    Deze waarschuwing is een indicatie dat \<de aanmeldingsgebeurtenis op *accountnaam*> gelekte referenties heeft gebruikt.
    
    Vervolgens> \<de *accountnaam* \<het *aantal*> unieke bestanden in één sessie verwijderd.

- **Aanmeldingsgebeurtenis van gebruiker met gelekte referenties die leiden tot het downloaden van massabestanden**
    
    Deze waarschuwing is een indicatie dat \<de aanmeldingsgebeurtenis op *accountnaam*> gelekte referenties heeft gebruikt.
    
    Vervolgens> \<de *accountnaam* gedownload \<over *het aantal*> unieke bestanden in één sessie.

- **Aanmeldingsgebeurtenis van gebruiker met gelekte referenties die leiden tot Office 365-imitatie**
    
    Deze waarschuwing is een indicatie dat \<de aanmeldingsgebeurtenis op *accountnaam*> gelekte referenties heeft gebruikt. 
    
    Vervolgens> \<de *accountnaam* zich \<voorgedaan boven *het aantal*> verschillende accounts in één sessie.

- **Aanmeldingsgebeurtenis van gebruiker met gelekte referenties die leiden tot het delen van massabestanden**
    
    Deze waarschuwing is een indicatie dat \<de aanmeldingsgebeurtenis op *accountnaam*> gelekte referenties heeft gebruikt.
    
    Vervolgens> \<de *accountnaam* \<gedeeld over *het aantal*> unieke bestanden in één sessie.

- **Aanmeldingsgebeurtenis van gebruiker met gelekte referenties naar ransomware in cloud-app**
    
    Deze waarschuwing is een indicatie dat \<de aanmeldingsgebeurtenis op *accountnaam*> gelekte referenties heeft gebruikt. 
    
    Vervolgens> \<de *accountnaam* \< *het aantal*> bestanden geüpload \<en een totaal *aantal*> bestanden verwijderd. 
    
    Deze activiteit patroon is indicatief voor een potentiële ransomware aanval.

## <a name="next-steps"></a>Volgende stappen

Nu u meer hebt geleerd over geavanceerde multistage-aanvalsdetectie, bent u mogelijk geïnteresseerd in de volgende quickstart om te leren hoe u inzicht krijgt in uw gegevens en potentiële bedreigingen: [Aan de slag met Azure Sentinel](quickstart-get-visibility.md).

Als u klaar bent om de voor u gemaakte incidenten te onderzoeken, raadpleegt u de volgende zelfstudie: [Incidenten onderzoeken met Azure Sentinel.](tutorial-investigate-cases.md)

