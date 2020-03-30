---
title: Veilige score in Azure Security Center
description: Beschrijving van de beveiligde score van Azure Security Center en de beveiligingsbesturingselementen
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
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: d7eea9cd83e72b6ffeaae319a8e87c065015e6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415789"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Verbeterde beveiligde score (voorbeeld) in Azure Security Center

## <a name="introduction-to-secure-score"></a>Inleiding om de score veilig te stellen

Azure Security Center heeft twee belangrijke doelen: u helpen uw huidige beveiligingssituatie te begrijpen en u te helpen uw beveiliging efficiënt en effectief te verbeteren. Het centrale aspect van Security Center dat u in staat stelt om deze doelen te bereiken is veilige score.

Security Center beoordeelt uw resources, abonnementen en organisatie voortdurend op beveiligingsproblemen. Vervolgens worden alle bevindingen samengevoegd tot één score, zodat u in één oogopslag uw huidige beveiligingssituatie zien: hoe hoger de score, hoe lager het geïdentificeerde risiconiveau. Gebruik de score om beveiligingsinspanningen en -projecten in uw organisatie bij te houden. 

De beveiligde scorepagina van Security Center bevat:

- **De score** - De veilige score wordt weergegeven als een procentuele waarde, maar de onderliggende waarden zijn ook duidelijk:

    [![Veilige score weergegeven als een procentuele waarde met de onderliggende getallen ook duidelijk](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Beveiligingsbesturingselementen** - Elk besturingselement is een logische groep gerelateerde beveiligingsaanbevelingen en weerspiegelt uw kwetsbare aanvalsoppervlakken. Een besturingselement is een reeks beveiligingsaanbevelingen, met instructies waarmee u deze aanbevelingen implementeren. Uw score verbetert alleen wanneer u *alle* aanbevelingen voor één resource binnen een besturingselement herstelt.

    Als u onmiddellijk wilt zien hoe goed uw organisatie elk afzonderlijk aanvalsoppervlak beveiligt, bekijkt u de scores voor elke beveiligingscontrole.

    Zie Hieronder hoe [de beveiligde score wordt berekend](secure-score-security-controls.md#how-the-secure-score-is-calculated) voor meer informatie. 


>[!TIP]
> Eerdere versies van Security Center toegekend punten op het aanbevelingsniveau: wanneer u een aanbeveling voor een enkele resource hebt gesaneerd, is uw beveiligde score verbeterd. Vandaag de dag verbetert uw score alleen als u *alle* aanbevelingen voor één resource binnen een besturingselement herstelt. Dus uw score verbetert alleen als u de beveiliging van een resource hebt verbeterd.
> Hoewel deze verbeterde versie nog in preview is, is de eerdere beveiligde score-ervaring beschikbaar als optie van de Azure Portal. 


## <a name="locating-your-secure-score"></a>Uw veilige score lokaliseren

Security Center geeft uw score prominent weer: dit is het eerste dat wordt weergegeven op de pagina Overzicht. Als u doorklikt naar de speciale beveiligde scorepagina, ziet u de score uitgesplitst naar abonnement. Klik op één abonnement om de gedetailleerde lijst met geprioritteerde aanbevelingen te bekijken en de mogelijke impact die het herstellen ervan zal hebben op de score van het abonnement.

## <a name="how-the-secure-score-is-calculated"></a>Hoe de veilige score wordt berekend 

De bijdrage van elke beveiligingscontrole aan de algehele veilige score wordt duidelijk weergegeven op de aanbevelingspagina.

[![De verbeterde beveiligde score introduceert beveiligingscontroles](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Om alle mogelijke punten voor een beveiligingscontrole te krijgen, moeten al uw resources voldoen aan alle beveiligingsaanbevelingen binnen de beveiligingscontrole. Beveiligingscentrum heeft bijvoorbeeld meerdere aanbevelingen met betrekking tot het beveiligen van uw beheerpoorten. In het verleden kon u een aantal van die gerelateerde en onderling afhankelijke aanbevelingen herstellen terwijl u anderen onopgelost laat, en uw beveiligde score zou verbeteren. Wanneer objectief bekeken, is het gemakkelijk om te beweren dat uw veiligheid niet was verbeterd totdat u ze allemaal had opgelost. Nu moet je ze allemaal herstellen om een verschil te maken voor je beveiligde score.

Het beveiligingsbesturingselement met de naam 'Systeemupdates toepassen' heeft bijvoorbeeld een maximale score van zes punten, die u in de knopinfo zien over de potentiële verhogingswaarde van het besturingselement:

[![De beveiligingscontrole "Systeemupdates toepassen"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Het potentieel voor de beveiligingscontrole "Apply system updates" in de screenshot hierboven toont "2% (1 Punt)". Dat betekent dat als u alle aanbevelingen in dit besturingselement herstelt, uw score met 2% zal toenemen (in dit geval één punt). Voor de eenvoud worden de waarden in de kolom 'Potentiële toename' van de aanbevelingen lijst afgerond op hele getallen. De tooltips tonen de precieze waarden:

* **Maximale score** - Het maximum aantal punten dat je verdienen door het invullen van alle aanbevelingen binnen een besturingselement. De maximale score voor een besturingselement geeft de relatieve betekenis van die controle aan. Gebruik de maximale scorewaarden om te triageen welke problemen om eerst aan te werken. 
* **Potentiële toename** - De resterende punten die beschikbaar zijn binnen het besturingselement. Als u deze punten wilt toevoegen aan uw beveiligde score, herstelt u alle aanbevelingen van het besturingselement. In het bovenstaande voorbeeld is het bovenstaande punt eigenlijk 0,96 punten.
* **Huidige score** - De huidige score voor dit besturingselement. Elke controle draagt bij aan de totale score. In dit voorbeeld draagt de besturing 5,04 punten bij aan het totaal. 

### <a name="calculations---understanding-your-score"></a>Berekeningen - inzicht in uw score

|Gegevens|Formule en voorbeeld|
|-|-|
|**De huidige score van beveiligingscontrole**|<br>![Vergelijking voor het berekenen van de huidige score van een beveiligingsbesturingselement](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Elke individuele beveiligingscontrole draagt bij aan de beveiligingsscore. Elke resource die wordt beïnvloed door een aanbeveling binnen het besturingselement, draagt bij aan de huidige score van het besturingselement. De huidige score voor elk besturingselement is een maat voor de status van de resources *binnen* het besturingselement.<br>![Knopinfo met de waarden die worden gebruikt bij het berekenen van de huidige score van het beveiligingsbesturingselement](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>In dit voorbeeld wordt de maximale score van 6 gedeeld door 78, omdat dat de som is van de gezonde en ongezonde resources.<br>6 / 78 = 0,0769<br>Vermenigvuldigen met het aantal gezonde resources (4) resulteert in de huidige score:<br>0,0769 * 4 = **0,31**<br><br>|
|**Beveiligingsscore**<br>Enkel abonnement|<br>![Vergelijking voor het berekenen van de huidige veilige score](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Beveiligde score met één abonnement met alle besturingselementen ingeschakeld](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>In dit voorbeeld is er één abonnement met alle beschikbare beveiligingscontroles (een potentiële maximale score van 60 punten). De score toont 28 punten van een mogelijke 60 en de resterende 32 punten worden weerspiegeld in de "Potentiële score stijging" cijfers van de security controls.<br>![Lijst van besturingselementen en de potentiële scoreverhoging](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Beveiligingsscore**<br>Meerdere abonnementen|<br>De huidige score voor alle resources voor alle abonnementen wordt toegevoegd en de berekening is dan hetzelfde als voor één abonnement<br><br>Bij het bekijken van meerdere abonnementen evalueert de veilige score alle resources binnen alle ingeschakelde beleidsregels en groepeert ze hun gecombineerde impact op de maximale score van elk beveiligingsbesturingselement.<br>![Veilige score voor meerdere abonnementen met alle besturingselementen ingeschakeld](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>De gecombineerde score is **geen** gemiddelde; het is eerder de geëvalueerde houding van de status van alle resources voor alle abonnementen.<br>Ook hier, als je naar de aanbevelingen pagina en optellen van de potentiële punten beschikbaar, vindt u dat het het verschil tussen de huidige score (24) en de maximale score beschikbaar (60).|
||||

## <a name="improving-your-secure-score"></a>Het verbeteren van uw veilige score

Als u uw beveiligde score wilt verbeteren, moet u beveiligingsaanbevelingen uit uw aanbevelingenlijst herstellen. U elke aanbeveling handmatig herstellen voor elke resource, of met behulp van de **Quick Fix!** optie (indien beschikbaar) om een herstel voor een aanbeveling snel toe te passen op een groep resources. Zie [Aanbevelingen voor herstel.](security-center-remediate-recommendations.md)

>[!IMPORTANT]
> Alleen ingebouwde aanbevelingen hebben een impact op de veilige score.

## <a name="security-controls-and-their-recommendations"></a>Beveiligingscontroles en hun aanbevelingen

In de onderstaande tabel worden de beveiligingsbesturingselementen in Azure Security Center weergegeven. Voor elk besturingselement u het maximum aantal punten zien dat u aan uw beveiligde score toevoegen als u *alle* aanbevelingen in het besturingselement voor *al* uw resources herstelt. 

> [!TIP]
> Als u deze lijst anders wilt filteren of sorteren, kopieert en plakt u deze in Excel.

|Beveiligingscontrole|Maximale veilige scorepunten|Aanbevelingen|
|----------------|:-------------------:|---------------|
|**MFA inschakelen**|10|- MFA moet zijn ingeschakeld op accounts met eigenaarmachtigingen voor uw abonnement<br>- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement<br>- MFA moet worden ingeschakeld accounts met schrijfmachtigingen op uw abonnement|
|**Beveiligde beheerpoorten**|8|- Just-In-Time netwerktoegangscontrole moet worden toegepast op virtuele machines<br>- Virtuele machines moeten worden gekoppeld aan een netwerkbeveiligingsgroep<br>- Beheerpoorten moeten worden gesloten op uw virtuele machines|
|**Systeemupdates toepassen**|6|- Monitoring agent gezondheidsproblemen moeten worden opgelost op uw machines<br>- Bewakingsagent moet worden geïnstalleerd op virtuele machineschaalsets<br>- Bewakingsagent moet op uw machines worden geïnstalleerd<br>- DE OS-versie moet worden bijgewerkt voor uw cloudservicerollen<br>- Systeemupdates op virtuele machineschaalsets moeten worden geïnstalleerd<br>- Systeemupdates moeten op uw machines worden geïnstalleerd<br>- Uw machines moeten opnieuw worden opgestart om systeemupdates toe te passen<br>- Kubernetes Services moet worden geüpgraded naar een niet-kwetsbare Kubernetes-versie<br>- Bewakingsagent moet op uw virtuele machines worden geïnstalleerd|
|**Beveiligingsproblemen herstellen**|6|- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers<br>- Kwetsbaarheden in Azure Container Registry-afbeeldingen moeten worden verholpen<br>- Kwetsbaarheden in uw SQL-databases moeten worden verholpen<br>- Kwetsbaarheden moeten worden verholpen door een oplossing voor kwetsbaarheidsbeoordeling<br>- Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-beheerde exemplaren<br>- Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-servers<br>- Kwetsbaarheidsbeoordelingsoplossing moet op uw virtuele machines worden geïnstalleerd|
|**Versleuteling in rust inschakelen**|4|- Schijfversleuteling moet worden toegepast op virtuele machines<br>- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld<br>- Variabelen van automatiseringsaccount moeten worden versleuteld<br>- Clusterfabricclusters moeten de eigenschap ClusterProtectionLevel hebben ingesteld op EncryptAndSign<br>- SQL server TDE protector moet worden versleuteld met uw eigen sleutel|
|**Gegevens versleutelen tijdens het transport**|4|- API-app mag alleen toegankelijk zijn via HTTPS<br>- Functie-app mag alleen toegankelijk zijn via HTTPS<br>- Alleen beveiligde verbindingen met uw Redis-cache moeten worden ingeschakeld<br>- Veilige overdracht naar opslagaccounts moet worden ingeschakeld<br>- Webtoepassing mag alleen toegankelijk zijn via HTTPS|
|**Toegang en machtigingen beheren**|4|- Er moeten maximaal 3 eigenaren worden aangewezen voor uw abonnement<br>- Afgeschafte accounts moeten uit uw abonnement worden verwijderd (Voorbeeld)<br>- Afgeschafte accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd (Voorbeeld)<br>- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd (Voorbeeld)<br>- Externe accounts met leesmachtigingen moeten uit uw abonnement worden verwijderd<br>- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd (Voorbeeld)<br>- Er moeten meer dan één eigenaar aan uw abonnement zijn toegewezen<br>- RBAC (Role-Based Access Control) moet worden gebruikt op Kubernetes Services (Preview)<br>- Clusters van servicefabric mogen alleen Azure Active Directory gebruiken voor clientverificatie|
|**Beveiligingsconfiguraties herstellen**|4|- Pod-beveiligingsbeleid moet worden gedefinieerd op Kubernetes Services<br>- Kwetsbaarheden in containerbeveiligingsconfiguraties moeten worden verholpen<br>- Kwetsbaarheden in beveiligingsconfiguratie op uw machines moeten worden verholpen<br>- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen<br>- Bewakingsagent moet op uw virtuele machines worden geïnstalleerd<br>- Bewakingsagent moet op uw machines worden geïnstalleerd<br>- Bewakingsagent moet worden geïnstalleerd op virtuele machineschaalsets<br>- Monitoring agent gezondheidsproblemen moeten worden opgelost op uw machines|
|**Ongeautoriseerde netwerktoegang beperken**|4|- IP-forwarding op uw virtuele machine moet worden uitgeschakeld<br>- Geautoriseerde IP-bereiken moeten worden gedefinieerd op Kubernetes Services (Preview)<br>- (DEPRECATED) Toegang tot App Services moet worden beperkt (Preview)<br>- (DEPRECATED) De regels voor webapplicaties op IaaS NSGs moeten worden verhard<br>- Virtuele machines moeten worden gekoppeld aan een netwerkbeveiligingsgroep<br>- CORS mag niet toestaan dat elke bron toegang heeft tot uw API-app<br>- CORS mag niet toestaan dat elke resource toegang heeft tot uw functie-app<br>- CORS mag niet toestaan dat elke resource toegang heeft tot uw webtoepassing<br>- Foutopsporing op afstand moet worden uitgeschakeld voor API-app<br>- Het op afstand debuggen moet worden uitgeschakeld voor functie-app<br>- Foutopsporing op afstand moet worden uitgeschakeld voor webtoepassing<br>- De toegang moet worden beperkt voor tolerante netwerkbeveiligingsgroepen met vm's op internet<br>- Network Security Group Rules voor internet met virtuele machines moeten worden verhard|
|**Adaptief toepassingsbesturingselement toepassen**|3|- Adaptieve toepassingsbesturingselementen moeten worden ingeschakeld op virtuele machines<br>- Bewakingsagent moet op uw virtuele machines worden geïnstalleerd<br>- Bewakingsagent moet op uw machines worden geïnstalleerd<br>- Monitoring agent gezondheidsproblemen moeten worden opgelost op uw machines|
|**Gegevensclassificatie toepassen**|2|- Gevoelige gegevens in uw SQL-databases moeten worden geclassificeerd (Preview)|
|**Toepassingen beschermen tegen DDoS-aanvallen**|2|- DDoS Protection Standard moet worden ingeschakeld|
|**Endpoint-beveiliging inschakelen**|2|- Gezondheidsfouten in eindpuntbescherming moeten worden gesaneerd op virtuele machineschaalsets<br>- Gezondheidsproblemen met endpointbescherming moeten op uw machines worden opgelost<br>- Endpoint-beveiligingsoplossing moet worden geïnstalleerd op virtuele machineschaalsets<br>- Installeer endpoint beveiligingsoplossing op virtuele machines<br>- Monitoring agent gezondheidsproblemen moeten worden opgelost op uw machines<br>- Bewakingsagent moet worden geïnstalleerd op virtuele machineschaalsets<br>- Bewakingsagent moet op uw machines worden geïnstalleerd<br>- Bewakingsagent moet op uw virtuele machines worden geïnstalleerd<br>- Installeer endpoint beveiligingsoplossing op uw machines|
|**Controle en logboekregistratie inschakelen**|1|- Auditing op SQL-server moet worden ingeschakeld<br>- Diagnostische logboeken in App Services moeten zijn ingeschakeld<br>- Diagnostische logboeken in Azure Data Lake Store moeten zijn ingeschakeld<br>- Diagnostische logboeken in Azure Stream Analytics moeten zijn ingeschakeld<br>- Diagnostische logboeken in Batch-accounts moeten zijn ingeschakeld<br>- Diagnostische logboeken in Data Lake Analytics moeten zijn ingeschakeld<br>- Diagnostische logboeken in gebeurtenishub moeten zijn ingeschakeld<br>- Diagnostische logboeken in IoT Hub moeten zijn ingeschakeld<br>- Diagnostische logboeken in Key Vault moeten zijn ingeschakeld<br>- Diagnostische logboeken in Logische apps moeten zijn ingeschakeld<br>- Diagnostische logboeken in de zoekservice moeten zijn ingeschakeld<br>- Diagnostische logboeken in servicebus moeten zijn ingeschakeld<br>- Diagnostische logboeken in virtuele machineschaalsets moeten zijn ingeschakeld<br>- Metrische waarschuwingsregels moeten worden geconfigureerd op Batch-accounts<br>- SQL Auditing-instellingen moeten actiegroepen hebben geconfigureerd om kritieke activiteiten vast te leggen<br>- SQL-servers moeten worden geconfigureerd met controleretentiedagen van meer dan 90 dagen.|
|**Best practices voor beveiliging implementeren**|0|- Toegang tot opslagaccounts met firewall en virtuele netwerkconfiguraties moet worden beperkt<br>- Alle autorisatieregels behalve RootManageSharedAccessKey moeten worden verwijderd uit de naamruimte van Gebeurtenishub<br>- Een Azure Active Directory-beheerder moet zijn ingericht voor SQL-servers<br>- Autorisatieregels voor de instantie Gebeurtenishub moeten worden gedefinieerd<br>- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-bronnen<br>- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-bronnen<br>- Geavanceerde instellingen voor gegevensbeveiliging voor SQL-server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen<br>- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw beheerde instanties<br>- Alle geavanceerde typen bedreigingsbeveiliging moeten zijn ingeschakeld in geavanceerde instellingen voor sql-beheerde gegevensbeveiliging<br>- E-mailmeldingen naar beheerders en eigenaren van abonnementen moeten worden ingeschakeld in geavanceerde gegevensbeveiligingsinstellingen van SQL-server<br>- Geavanceerde typen bedreigingsbeveiliging moeten worden ingesteld op 'Alles' in sql-server geavanceerde gegevensbeveiligingsinstellingen<br>- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep<br>- Alle geavanceerde typen bedreigingsbeveiliging moeten zijn ingeschakeld in geavanceerde gegevensbeveiligingsinstellingen van SQL-server|
||||

## <a name="secure-score-faq"></a>Veelgestelde vragen over een veilige score

### <a name="why-has-my-secure-score-gone-down"></a>Waarom is mijn score voor een veilige score gedaald?
Security Center is overgestapt op een verbeterde beveiligde score (momenteel in preview-status) die wijzigingen in de manier waarop de score wordt berekend bevat. Nu moet u alle aanbevelingen oplossen voor een resource om punten te ontvangen. De scores veranderden ook in een schaal van 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Als ik slechts drie van de vier aanbevelingen in een beveiligingscontrole aanspreek, verandert mijn beveiligde score dan?
Nee. Het zal niet veranderen totdat u alle aanbevelingen voor een enkele resource te saneren. Om de maximale score voor een besturingselement te krijgen, moet u alle aanbevelingen voor alle resources herstellen.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>Is de eerdere ervaring van de beveiligde score nog beschikbaar? 
Ja. Voor een tijdje zullen ze naast elkaar lopen om de overgang te vergemakkelijken. Verwacht dat het vorige model op tijd wordt uitgefaseerd. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Als een aanbeveling niet op mij van toepassing is en ik deze in het beleid uitschakel, wordt mijn beveiligingscontrole uitgevoerd en wordt mijn beveiligde score bijgewerkt?
Ja. We raden u aan aanbevelingen uit te schakelen wanneer deze niet van toepassing zijn in uw omgeving. Zie [Beveiligingsbeleid uitschakelen](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies)voor instructies over het uitschakelen van een specifieke aanbeveling.

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Als een beveiligingscontrole biedt me nul punten in de richting van mijn veilige score, moet ik negeren?
In sommige gevallen ziet u een maximale score van meer dan nul, maar de impact is nul. Wanneer de incrementele score voor het repareren van resources te verwaarlozen is, wordt deze afgerond op nul. Negeer deze aanbevelingen niet omdat ze nog steeds beveiligingsverbeteringen met zich meebrengen. De enige uitzondering is het besturingselement 'Extra aanbevolen praktijken'. Het herstellen van deze aanbevelingen zal uw score niet verhogen, maar het zal uw algehele beveiliging verbeteren.

## <a name="next-steps"></a>Volgende stappen

Dit artikel beschreef de beveiligde score en de beveiligingscontroles die het introduceert. Zie voor gerelateerd materiaal de volgende artikelen:

- [Meer informatie over de verschillende elementen van een aanbeveling](security-center-recommendations.md)
- [Meer informatie over het herstellen van aanbevelingen](security-center-remediate-recommendations.md)