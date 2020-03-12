---
title: Verbeterde beveiligde Score (preview) in Azure Security Center
description: Beschrijving van de verbeterde beveiligde Score (preview-versie) en beveiligings controles in Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2019
ms.author: memildin
ms.openlocfilehash: ad0d8e751e79a23b2e7ac7b9da934f141ca9aa55
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086526"
---
# <a name="the-enhanced-secure-score-preview"></a>De verbeterde beveiligde Score (preview-versie) 

In dit artikel wordt de verbeterde beveiligde Score geïntroduceerd (momenteel in Preview), de bijbehorende beveiligings controles en de voor delen die ze bieden. Ook wordt uitgelegd hoe uw score wordt berekend.

## <a name="introduction-to-secure-score"></a>Inleiding tot beveiligde Score

Azure Security Center heeft twee belang rijke doel stellingen: om u inzicht te geven in de huidige beveiligings situatie en om u te helpen uw beveiliging efficiënt en effectief te verbeteren. Het centrale aspect van Security Center waarmee u deze doelen kunt bereiken, is een veilige Score.

Met Security Center worden uw resources, abonnementen en organisaties doorlopend beoordeeld op beveiligings problemen. Vervolgens worden alle bevindingen in één enkele score geaggregeerd, zodat u in een oogopslag uw huidige beveiligings situatie kunt zien: hoe hoger de score, hoe lager het geïdentificeerde risico niveau is. Gebruik de score om veiligheids inspanningen en projecten in uw organisatie bij te houden. 

De *verbeterde* beveiligde Score (momenteel in Preview) is **gericht op kwets** baarheid voor aanvallen en biedt drie voor delen:

- **Beveiligings** maatregelen: beveiligings aanbevelingen worden nu gegroepeerd in logische sets die beter overeenkomen met uw kwets bare aanvals oppervlakken. Zie [How the Secure Score](secure-score-security-controls.md#how-the-secure-score-is-calculated) (Engelstalig) voor meer informatie.

- **De totale score is beter weerspiegelt de totale postuur** -punten zijn toegekend op het niveau van aanbeveling. Met deze uitbrei ding wordt uw score alleen verbeterd wanneer u *alle* aanbevelingen voor één resource in een besturings element herstelt. Dit betekent dat uw score alleen verbetert wanneer de beveiliging van een resource wordt verbeterd. 

- De **beveiligings status van afzonderlijke kwets bare Opper vlakken is meer zichtbaar** -door de Score per beveiligings beheer weer te geven, wordt de pagina beveiligde Score de plek waar u een gedetailleerd overzicht krijgt van de manier waarop uw organisatie elke afzonderlijke kwets baarheid beveiligt.

De verbeterde beveiligde score wordt weer gegeven als een percentage, zoals wordt weer gegeven in de volgende scherm afbeelding:

[![de verbeterde beveiligde Score (preview) nu een percentage bevat](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)


## <a name="locating-your-secure-score"></a>Uw beveiligde Score zoeken

Security Center wordt uw score prominent weer gegeven: het is het eerste wat wordt weer gegeven op de pagina overzicht. Als u op de pagina speciale beveiligde Score klikt, ziet u de score die is opgesplitst per abonnement. Klik op één abonnement om de gedetailleerde lijst met aanbevelingen met prioriteit weer te geven, en de mogelijke impact die ze herstellen op de Score van het abonnement.

## <a name="how-the-secure-score-is-calculated"></a>Hoe de beveiligde score wordt berekend 

Vóór deze preview Security Center elke aanbeveling afzonderlijk beschouwd en wordt er een waarde aan toegewezen op basis van de ernst. Beveiligings teams die hun beveiligings postuur kunnen verbeteren, hebben de prioriteit van reacties op Security Center aanbevelingen op basis van de volledige lijst met bevindingen. Telkens wanneer u een aanbeveling voor één resource hebt hersteld, is uw beveiligde Score verbeterd.

Als onderdeel van de uitbrei dingen van de beveiligde Score zijn de aanbevelingen nu gegroepeerd in **beveiligings controles**. Een besturings element is een reeks beveiligings aanbevelingen en de instructies die u helpen bij het implementeren van deze aanbevelingen. Besturings elementen zijn logische groeperingen van gerelateerde aanbevelingen. Punten worden niet meer toegekend op het niveau van aanbeveling. In plaats daarvan wordt uw score alleen verbeterd wanneer u *alle* aanbevelingen voor één resource in een besturings element herstelt.

De bijdrage van elk beveiligings beheer voor de algehele beveiligde score wordt duidelijk weer gegeven op de pagina aanbevelingen.

[![de verbeterde beveiligde Score (preview) introduceert beveiligings controles](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Om alle mogelijke punten voor een beveiligings controle op te halen, moeten al uw resources voldoen aan alle beveiligings aanbevelingen binnen het beveiligings beheer. Security Center heeft bijvoorbeeld meerdere aanbevelingen over hoe u uw beheer poorten kunt beveiligen. In het verleden kon u enkele van die gerelateerde en afhankelijke aanbevelingen herstellen, terwijl anderen niet opgelost blijven en uw veilige score wordt verbeterd. Wanneer u objectief bekijkt, is het eenvoudig om te raden dat uw beveiligings niet had verbeterd totdat u ze allemaal hebt opgelost. Nu moet u deze allemaal herstellen om een verschil te maken met uw beveiligde Score.

Het beveiligings beheer met de naam ' systeem updates Toep assen ' heeft bijvoorbeeld een maximum Score van zes punten, die u in de tooltip kunt zien op de mogelijke verhogings waarde van het besturings element:

[![het beveiligings beheer ' systeem updates Toep assen '](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

De kans op het beveiligings beheer ' systeem updates Toep assen ' in de bovenstaande scherm afbeelding toont ' 2% (1 punt) '. Dit betekent dat als u alle aanbevelingen in dit besturings element herstelt, uw score wordt verhoogd met 2% (in dit geval één punt). Ter vereenvoudiging worden de waarden in de kolom mogelijke toename van de lijst met aanbevelingen afgerond op gehele getallen. In de knop info worden de exacte waarden weer gegeven:

* **Maximale score** -het maximum aantal punten dat u kunt krijgen door alle aanbevelingen binnen een besturings element te volt ooien. De maximum score voor een besturings element duidt op de relatieve betekenis van dat besturings element. Gebruik de maximale score waarden om te sorteren aan welke kwesties het eerst moet worden uitgevoerd. 
* **Mogelijke toename** : de resterende punten die voor u beschikbaar zijn in het besturings element. Om deze punten toe te voegen aan uw beveiligde Score, worden alle aanbevelingen van het besturings element opgelost. In het bovenstaande voor beeld is het één punt dat wordt weer gegeven voor het besturings element in feite 0,96 punten.
* **Huidige Score** : de huidige score voor dit besturings element. Elk besturings element draagt bij aan de totale score. In dit voor beeld is het besturings element 5,04 punten bijdraagt aan het totaal. 

### <a name="calculations---understanding-your-score"></a>Berekeningen: uitleg van uw Score

|Gegevens|Formule en voor beeld|
|-|-|
|**Huidige Score van beveiligings beheer**|<br>![vergelijking voor het berekenen van de huidige Score van een beveiligings beheer](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Elk afzonderlijk beveiligings beheer draagt bij aan de beveiligings Score. Elke resource die wordt beïnvloed door een aanbeveling binnen het besturings element, draagt bij aan de huidige Score van het besturings element. De huidige score voor elk besturings element is een meting van de status van de resources *in* het besturings element.<br>![knop info met de waarden die worden gebruikt bij het berekenen van de huidige Score van het beveiligings beheer](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>In dit voor beeld wordt de maximale Score van 6 gedeeld door 78, omdat dat de som is van de in orde zijnde en slechte resources.<br>6/78 = 0,0769<br>Als u wilt vermenigvuldigen met het aantal ongezonde resources (4), resulteert dit in de huidige Score:<br>0,0769 * 4 = **0,31**<br><br>|
|**Beveiligingsscore**<br>Enkel abonnement|<br>![Vergelijking voor het berekenen van de huidige beveiligde Score](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Een beveiligde Score van één abonnement waarbij alle besturings elementen zijn ingeschakeld](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>In dit voor beeld is er één abonnement met alle beveiligings controles beschikbaar (een potentiële maximum Score van 60 punten). De score toont 28 punten van een mogelijke 60 en de resterende 32 punten worden weer gegeven in de sectie ' potentiële Score verhogen ' van de beveiligings controles.<br>![Lijst met besturings elementen en de potentiële toename van de Score](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Beveiligingsscore**<br>Meerdere abonnementen|<br>De huidige score voor alle resources in alle abonnementen wordt toegevoegd en de berekening is dan hetzelfde als voor één abonnement<br><br>Wanneer er meerdere abonnementen worden weer gegeven, evalueert de beveiligde score alle resources binnen het ingeschakelde beleid en worden de gecombineerde impact van elk van de maximale scores van het beveiligings beheer gegroepeerd.<br>![beveiligde score voor meerdere abonnementen waarbij alle besturings elementen zijn ingeschakeld](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>De gecombineerde score is **geen** gemiddelde. in plaats daarvan is het de geëvalueerde postuur van de status van alle resources in alle abonnementen.<br>Ook als u naar de pagina aanbevelingen gaat en de potentiële punten die beschikbaar zijn, opneemt, zult u merken dat het verschil tussen de huidige Score (24) en de Maxi maal beschik bare Score (60) is.|
||||

## <a name="improving-your-secure-score"></a>Uw beveiligde Score verbeteren

Als u uw beveiligde score wilt verbeteren, kunt u de aanbevelingen voor beveiliging herstellen in uw lijst met aanbevelingen. U kunt elke aanbeveling hand matig herstellen voor elke resource, of met behulp van de **oplossing voor snel oplossen!** optie (indien beschikbaar) om een herbemiddeling toe te passen voor een aanbeveling voor een groep resources snel. Zie [aanbevelingen herstellen](security-center-remediate-recommendations.md)voor meer informatie.

Alleen ingebouwde aanbevelingen hebben invloed op de beveiligde Score.

## <a name="security-controls-and-their-recommendations"></a>Beveiligings controles en hun aanbevelingen

De volgende tabel bevat de beveiligings opties in Azure Security Center. Voor elk besturings element ziet u het maximum aantal punten dat u aan uw beveiligde Score kunt toevoegen als u *alle* aanbevelingen die worden vermeld in het besturings element herstelt, voor *al* uw resources. 

> [!TIP]
> Als u deze lijst anders wilt filteren of sorteren, kopieert en plakt u deze in Excel.

|Beveiligings beheer|Maximum aantal beveiligde Score punten|Aanbevelingen|
|----------------|:-------------------:|---------------|
|**MFA inschakelen**|10|-MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement<br>-MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement<br>-MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement|
|**Poorten voor beveiligde beheer**|8|-Just-in-time-netwerk toegangs beheer moet worden toegepast op virtuele machines<br>-Virtuele machines moeten worden gekoppeld aan een netwerk beveiligings groep<br>-Beheer poorten moeten worden gesloten op uw virtuele machines|
|**Systeemupdates toepassen**|6|-Controleren of de agent status problemen moeten worden opgelost op uw computers<br>-Monitoring agent moet worden geïnstalleerd op virtuele-machine schaal sets<br>-Monitoring agent moet op uw computers zijn geïnstalleerd<br>-De versie van het besturings systeem moet worden bijgewerkt voor uw Cloud service rollen<br>-Systeem updates op virtuele-machine schaal sets moeten worden geïnstalleerd<br>-Systeem updates moeten op uw computers worden geïnstalleerd<br>-Uw computers moeten opnieuw worden opgestart om systeem updates toe te passen<br>-Kubernetes services moeten worden bijgewerkt naar een niet-kwets bare Kubernetes-versie<br>-Monitoring agent moet worden geïnstalleerd op uw virtuele machines|
|**Beveiligingsproblemen herstellen**|6|-Geavanceerde gegevens beveiliging moet zijn ingeschakeld op uw SQL-servers<br>-Beveiligings problemen in Azure Container Registry installatie kopieën moeten worden hersteld (preview-versie)<br>-Beveiligings problemen voor uw SQL-data bases moeten worden hersteld<br>-Beveiligings problemen moeten worden opgelost door een oplossing voor de evaluatie van beveiligings lekken<br>-Evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties<br>-Evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers<br>-De evaluatie oplossing voor beveiligings problemen moet op uw virtuele machines worden geïnstalleerd|
|**Versleuteling inschakelen op rest**|4|-Schijf versleuteling moet worden toegepast op virtuele machines<br>-Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld<br>-De variabelen van het Automation-account moeten worden versleuteld<br>-Voor Service Fabric clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign<br>-SQL Server TDE-beveiliging moet worden versleuteld met uw eigen sleutel|
|**Gegevens in transit versleutelen**|4|-API-app mag alleen toegankelijk zijn via HTTPS<br>-Functie-app moet alleen toegankelijk zijn via HTTPS<br>-Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld<br>-Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld<br>-Webtoepassing mag alleen toegankelijk zijn via HTTPS|
|**Toegang en machtigingen beheren**|4|-Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement<br>-Afgeschafte accounts moeten worden verwijderd uit uw abonnement<br>-Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement<br>-Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement<br>-Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement<br>-Externe accounts met schrijf machtigingen moeten worden verwijderd uit uw abonnement<br>-Er moeten meer dan één eigenaar aan uw abonnement zijn toegewezen<br>-Op rollen gebaseerde Access Control (RBAC) moet worden gebruikt voor Kubernetes-Services (preview)<br>-Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie|
|**Beveiligingsconfiguraties herstellen**|4|-Pod-beveiligings beleid moet worden gedefinieerd op Kubernetes-Services (preview)<br>-Beveiligings problemen in configuratie van container beveiliging moeten worden hersteld<br>-Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld<br>-Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld<br>-Monitoring agent moet worden geïnstalleerd op uw virtuele machines<br>-Monitoring agent moet op uw computers zijn geïnstalleerd<br>-Monitoring agent moet worden geïnstalleerd op virtuele-machine schaal sets<br>-Controleren of de agent status problemen moeten worden opgelost op uw computers|
|**Onbevoegde netwerk toegang beperken**|4|-Door sturen via IP op uw virtuele machine moet worden uitgeschakeld<br>-Geautoriseerde IP-bereiken moeten worden gedefinieerd op Kubernetes Services (preview-versie)<br>-(Afgeschaft) toegang tot App Services moet worden beperkt (preview-versie)<br>-(Afgeschaft) de regels voor webtoepassingen op IaaS Nsg's moeten worden gehard<br>-Virtuele machines moeten worden gekoppeld aan een netwerk beveiligings groep<br>-CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw API-app<br>-CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw functie-app<br>-CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw webtoepassing<br>-Externe fout opsporing moet worden uitgeschakeld voor de API-app<br>-Externe fout opsporing moet worden uitgeschakeld voor functie-app<br>-Externe fout opsporing moet worden uitgeschakeld voor webtoepassing<br>-Toegang moet worden beperkt voor strikte netwerk beveiligings groepen met Internet gerichte Vm's<br>-Regels voor netwerk beveiligings groepen voor virtuele machines die zijn gericht op internet, moeten worden gehard|
|**Adaptief toepassings beheer Toep assen**|3|-Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines<br>-Monitoring agent moet worden geïnstalleerd op uw virtuele machines<br>-Monitoring agent moet op uw computers zijn geïnstalleerd<br>-Controleren of de agent status problemen moeten worden opgelost op uw computers|
|**Gegevens classificatie Toep assen**|2|-Gevoelige gegevens in uw SQL-data bases moeten worden geclassificeerd (preview-versie)|
|**Toepassingen beveiligen tegen DDoS-aanvallen**|2|-DDoS Protection standaard moet zijn ingeschakeld|
|**Endpoint Protection inschakelen**|2|-Endpoint Protection-status fouten moeten worden hersteld op schaal sets voor virtuele machines<br>-Endpoint Protection-status problemen moeten worden opgelost op uw computers<br>-Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machine schaal sets<br>-Endpoint Protection-oplossing op virtuele machines installeren<br>-Controleren of de agent status problemen moeten worden opgelost op uw computers<br>-Monitoring agent moet worden geïnstalleerd op virtuele-machine schaal sets<br>-Monitoring agent moet op uw computers zijn geïnstalleerd<br>-Monitoring agent moet worden geïnstalleerd op uw virtuele machines<br>-Endpoint Protection-oplossing op uw computers installeren|
|**Controle en logboek registratie inschakelen**|1|-Auditing op SQL Server moet zijn ingeschakeld<br>-Diagnostische logboeken in App Services moeten worden ingeschakeld<br>-Diagnostische logboeken in Azure Data Lake Store moeten worden ingeschakeld<br>-Diagnostische logboeken in Azure Stream Analytics moeten worden ingeschakeld<br>-Diagnostische logboeken in batch-accounts moeten worden ingeschakeld<br>-Diagnostische logboeken in Data Lake Analytics moeten worden ingeschakeld<br>-Diagnostische logboeken in Event hub moeten zijn ingeschakeld<br>-Diagnostische logboeken in IoT Hub moeten worden ingeschakeld<br>-Diagnostische logboeken in Key Vault moeten worden ingeschakeld<br>-Diagnostische logboeken in Logic Apps moeten worden ingeschakeld<br>-Diagnostische logboeken in de zoek service moeten worden ingeschakeld<br>-Diagnostische logboeken in Service Bus moeten worden ingeschakeld<br>-Diagnostische logboeken in Virtual Machine Scale Sets moeten worden ingeschakeld<br>-Metrische waarschuwings regels moeten worden geconfigureerd voor batch-accounts<br>-SQL-controle-instellingen moeten actie-groepen hebben die zijn geconfigureerd voor het vastleggen van kritieke activiteiten<br>-SQL-servers moeten worden geconfigureerd met controle dagen van meer dan 90 dagen.|
|**Best practices voor beveiliging implementeren**|0|-Toegang tot opslag accounts met firewall-en virtuele-netwerk configuraties moet worden beperkt<br>-Alle autorisatie regels, behalve RootManageSharedAccessKey, moeten worden verwijderd uit de Event hub-naam ruimte<br>-Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers<br>-Autorisatie regels voor het event hub-exemplaar moeten worden gedefinieerd<br>-Opslag accounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources<br>-Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources<br>-Geavanceerde instellingen voor gegevens beveiliging voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van beveiligings waarschuwingen<br>-Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties<br>-Alle geavanceerde bedreigingen beveiligings typen moeten zijn ingeschakeld in de beveiligings instellingen voor geavanceerde gegevens van SQL Managed instance<br>-E-mail meldingen voor beheerders en abonnements eigenaren moeten zijn ingeschakeld in de beveiligings instellingen van SQL Server Advanced Data<br>-De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Server<br>-Subnetten moeten worden gekoppeld aan een netwerk beveiligings groep<br>-Alle geavanceerde bedreigingen beveiligings typen moeten zijn ingeschakeld in de beveiligings instellingen van SQL Server Advanced Data|
||||

## <a name="secure-score-faq"></a>Veelgestelde vragen over beveiligde scores

### <a name="why-has-my-secure-score-gone-down"></a>Waarom is mijn beveiligde Score verdwenen?
Met de wijzigingen die in deze verbeterde beveiligde Score worden geïntroduceerd, moet u alle aanbevelingen voor een resource oplossen om punten te ontvangen. De scores zijn ook gewijzigd in een schaal van 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Als ik slechts drie van de vier aanbevelingen in een beveiligings controle adresseer, wordt mijn beveiligde Score gewijzigd?
Geen deze wijziging wordt pas doorgevoerd wanneer u alle aanbevelingen voor één resource herstelt. Als u de maximum score voor een besturings element wilt ophalen, moet u alle aanbevelingen voor alle resources herstellen.

### <a name="will-this-enhanced-secure-score-replace-the-existing-secure-score"></a>Wordt de bestaande beveiligde Score vervangen door deze verbeterde beveiligde Score? 
Ja, maar voor een tijdje worden ze naast elkaar uitgevoerd om de overgang te vergemakkelijken.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Als een aanbeveling niet van toepassing is en deze in het beleid uitschakelt, wordt mijn beveiligings controle vervuld en mijn beveiligde score bijgewerkt?
Ja. We raden u aan om aanbevelingen uit te scha kelen wanneer ze niet van toepassing zijn in uw omgeving. Zie [beveiligings beleid uitschakelen](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies)voor instructies over het uitschakelen van een specifieke aanbeveling.

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Als een beveiligings besturings element me nul punten naar mijn beveiligde Score biedt, moet ik dit dan negeren?
In sommige gevallen ziet u een maximum Score van het besturings element dat groter is dan nul, maar de impact is nul. Wanneer de incrementele score voor het oplossen van resources verwaarloosbaar is, wordt deze afgerond op nul. Negeer deze aanbevelingen niet omdat ze nog steeds betere beveiliging bieden. De enige uitzonde ring hierop is het besturings element ' extra best practice '. Door deze aanbevelingen te herstellen, wordt uw score niet verhoogd, maar wordt uw algehele beveiliging verbeterd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe de verbeterde beveiligde Score en de nieuwe beveiligings maatregelen worden geïntroduceerd. Raadpleeg de volgende artikelen voor gerelateerde materialen:

- [Meer informatie over de verschillende elementen van een aanbeveling](security-center-recommendations.md)
- [Meer informatie over het oplossen van aanbevelingen](security-center-remediate-recommendations.md)