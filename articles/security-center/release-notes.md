---
title: Releaseopmerkingen voor Azure Security Center
description: Een beschrijving van wat er nieuw is en gewijzigd is in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b1bd0ff28906f35ddc56afbd0287bbe29879deb4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91438948"
---
# <a name="whats-new-in-azure-security-center"></a>Wat is er nieuw in Azure Security Center?

Azure-beveiliging is in actieve ontwikkeling en wordt voortdurend verbeterd. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt deze pagina u informatie over:

- Nieuwe functies
- Opgeloste fouten
- Afgeschafte functionaliteit

Deze pagina wordt regelmatig bijgewerkt. Kom hier daarom regelmatig terug. Als u op zoek bent naar items die ouder zijn dan zes maanden, vindt u deze in het [Archief voor nieuwe functies in Azure AD in Azure Security Center](release-notes-archive.md).


## <a name="september-2020"></a>September 2020

De updates in september zijn onder meer:
- [Security Center krijgt een nieuwe look.](#security-center-gets-a-new-look)
- [Azure Defender uitgebracht](#azure-defender-released)
- [Azure Defender voor Key Vault is algemeen verkrijgbaar](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender voor opslagbeveiliging voor bestanden en ADLS Gen2 is algemeen beschikbaar](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Hulpprogramma's voor assetinventarisatie zijn nu algemeen beschikbaar](#asset-inventory-tools-are-now-generally-available)
- [Een specifiek gevonden beveiligingsprobleem voor scans van containerregisters en virtuele machines uitschakelen](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Een resource uitsluiten van een aanbeveling](#exempt-a-resource-from-a-recommendation)
- [AWS- en GCP-connectors in Security Center bieden een multi-cloudervaring](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Bundel met aanbevelingen voor Kubernetes-workloadbeveiliging](#kubernetes-workload-protection-recommendation-bundle)
- [Verbeteringen van bescherming tegen bedreigingen voor IoT in Azure Defender voor IoT](#iot-threat-protection-enhancements-in-azure-defender-for-iot)
- [Resultaten van evaluatie van beveiligingsproblemen zijn nu beschikbaar in continue export](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Onjuiste beveiligingsconfiguraties voorkomen door aanbevelingen af te dwingen bij het maken van nieuwe resources](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Aanbevelingen voor netwerkbeveiligingsgroep verbeterd](#network-security-group-recommendations-improved)
- [Aanbeveling AKS-preview 'Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services' afgeschaft'](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [E-mailmeldingen van Azure Security Center verbeterd](#email-notifications-from-azure-security-center-improved)
- [Beveiligingsscore bevat geen preview-aanbevelingen](#secure-score-doesnt-include-preview-recommendations)
- [Aanbevelingen bevatten nu een ernst-indicator en vernieuwingsinterval](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center krijgt een nieuwe look.

We hebben een vernieuwde gebruikersinterface voor de portalpagina's van Security Center uitgebracht. De nieuwe pagina's bevatten een nieuwe overzichtspagina en dashboards voor beveiligingsscore, assetinventarisatie en Azure Defender.

De opnieuw ontworpen overzichtspagina heeft nu een tegel voor toegang tot de dashboards voor de beveiligingsscore, assetinventarisatie en Azure Defender. Het bevat ook een tegel met een koppeling naar het dashboard voor naleving van regelgeving.

Meer informatie over de [overzichtspagina](overview-page.md).


### <a name="azure-defender-released"></a>Azure Defender uitgebracht

**Azure Defender** is het Cloud Workload Protection Platform (CWPP) dat is geïntegreerd in Security Center voor geavanceerde, intelligente beveiliging van uw Azure- en hybride workloads. Het vervangt de standaardprijscategorie van Security Center. 

Als u Azure Defender inschakelt in het gedeelte **Prijzen en instellingen** van Azure Security Center, worden de volgende Defender-abonnementen allemaal tegelijkertijd ingeschakeld en kunt u gebruikmaken van een totaaloplossing voor de bescherming van de berekenings-, gegevens- en servicelagen in uw omgeving:

- [Azure Defender voor servers](defender-for-servers-introduction.md)
- [Azure Defender voor App Service](defender-for-app-service-introduction.md)
- [Azure Defender voor Storage](defender-for-storage-introduction.md)
- [Azure Defender voor SQL](defender-for-sql-introduction.md)
- [Azure Defender voor IoT](defender-for-iot-introduction.md)
- [Azure Defender voor Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender voor containerregisters](defender-for-container-registries-introduction.md)

Elk van deze abonnementen wordt afzonderlijk beschreven in de documentatie voor Security Center.

Met zijn speciale dashboard biedt Azure Defender beveiligingswaarschuwingen en geavanceerde bescherming tegen dreigingen voor virtuele machines, SQL databases, containers, webtoepassingen, uw netwerk en meer.

[Meer informatie over Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender voor Key Vault is algemeen verkrijgbaar

Azure Key Vault is een cloudservice die versleutelingssleutels en geheimen, zoals certificaten, verbindingsreeksen en wachtwoorden, beveiligt. 

**Azure Defender voor Key Vault** biedt voor Azure systeemeigen, geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault, waarmee u een extra laag beveiligingsinformatie kunt leveren. Als uitbreiding biedt Azure Defender voor Key Vault consequent bescherming aan veel van de resources die afhankelijk zijn van uw Key Vault-accounts.

Het optionele abonnement is nu algemeen beschikbaar. Deze functie was in preview als 'geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault'.

Daarnaast bevatten de Key Vault-pagina's in Azure Portal nu een speciale pagina **Beveiliging** voor aanbevelingen en waarschuwingen van **Security Center**.

Meer informatie vindt u in [Azure Defender voor Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender voor opslagbeveiliging voor bestanden en ADLS Gen2 is algemeen beschikbaar 

**Azure Defender for Storage** detecteert potentieel schadelijke activiteiten in uw Azure Storage-accounts. Uw gegevens kunnen worden beschermd, ongeacht of deze zijn opgeslagen als blobcontainers, bestandsshares of data lakes.

Ondersteuning voor [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) en [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) is nu algemeen beschikbaar.

Vanaf 1 oktober 2020 gaan we kosten in rekening brengen voor de beveiliging van resources in deze services.

Meer informatie vindt u in [Azure Defender voor Storage](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Hulpprogramma's voor assetinventarisatie zijn nu algemeen beschikbaar

De pagina voor assetinventarisatie van Azure Security Center biedt één pagina voor het weergeven van het beveiligingspostuur van de resources die u hebt verbonden met Security Center.

Security Center analyseert periodiek de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Vervolgens krijgt u aanbevelingen voor het oplossen van deze beveiligingsproblemen.

Wanneer een resource openstaande aanbevelingen heeft, worden deze weergegeven in de inventarisatie.

Meer informatie vindt u in [Uw resources verkennen en beheren met hulpprogramma's voor assetinventarisatie en beheer](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Een specifiek gevonden beveiligingsprobleem voor scans van containerregisters en virtuele machines uitschakelen

Azure Defender bevat scanners voor beveiligingsproblemen om installatiekopieën in uw Azure Container Registry en uw virtuele machines te scannen.

Als u een organisatorische behoefte hebt om een resultaat te negeren in plaats van dit te herstellen, kunt u het eventueel uitschakelen. Uitgeschakelde resultaten hebben geen invloed op uw beveiligingsscore en genereren geen ongewenste ruis.

Wanneer een resultaat overeenkomt met de criteria die u hebt gedefinieerd in de regels voor uitschakelen, wordt dit niet weergegeven in de lijst met resultaten.

Deze optie is beschikbaar op de pagina met aanbevelingsdetails voor:

- **Beveiligingsproblemen met installatiekopieën in Azure Container Registry moeten worden hersteld**
- **Beveiligingsproblemen op uw virtuele machines moeten worden hersteld**

Meer informatie vindt u in [Specifieke resultaten voor uw containerinstallatiekopieën uitschakelen](defender-for-container-registries-usage.md#disable-specific-findings-preview) en [Specifieke resultaten voor uw virtuele machines uitschakelen](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Een resource uitsluiten van een aanbeveling

In sommige gevallen wordt een resource vermeld als niet in orde met betrekking tot een specifieke aanbeveling (en waardoor uw beveiligingsscore lager wordt), zelfs als u denkt dat dit niet zo zou moeten zijn. Dit kan zijn opgelost door een proces dat niet wordt bijgehouden door Security Center. Of misschien heeft uw organisatie besloten het risico voor die specifieke resource te accepteren. 

In dergelijke gevallen kunt u een uitzonderingsregel maken en ervoor zorgen dat de resource in de toekomst niet wordt vermeld als een resource die niet in orde is. Deze regels kunnen gedocumenteerde redenen bevatten, zoals hieronder wordt beschreven.

Meer informatie vindt u in [Een resource uitsluiten van aanbevelingen en de beveiligingsscore](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>AWS- en GCP-connectors in Security Center bieden een multi-cloudervaring

Veel workloads in de cloud beslaan meerdere cloudplatforms, dus moeten cloudbeveiligingsservices hetzelfde doen.

Azure Security Center biedt nu bescherming voor workloads in Azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

Met de onboarding van uw AWS- en GCP-accounts in Security Center worden AWS Security Hub, GCP Security Command en Azure Security Center geïntegreerd. 

Meer informatie vindt u in [Uw AWS-accounts verbinden met Azure Security Center](quickstart-onboard-aws.md) en [Uw GCP-accounts verbinden met Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Bundel met aanbevelingen voor Kubernetes-workloadbeveiliging

Om ervoor te zorgen dat Kubernetes-workloads standaard veilig zijn, voegt Security Center aanbevelingen voor beveiliging op Kubernetes-niveau toe, met inbegrip van afdwingingsopties met Kubernetes Admission Control.

Wanneer u de Azure Policy-invoegtoepassing voor Kubernetes op uw AKS-cluster hebt geïnstalleerd, wordt elke aanvraag voor de Kubernetes API-server gecontroleerd op basis van de vooraf gedefinieerde set aanbevolen procedures voordat deze naar het cluster gaat. Vervolgens kunt u instellingen configureren om de aanbevolen procedures af te dwingen en ze te verplichten voor toekomstige workloads.

U kunt er bijvoorbeeld voor zorgen dat containers met machtigingen niet moeten worden gemaakt, en toekomstige aanvragen hiervoor worden dan geblokkeerd.

Meer informatie vindt u in [Aanbevolen procedures voor workloadbeveiliging met behulp van Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="iot-threat-protection-enhancements-in-azure-defender-for-iot"></a>Verbeteringen van bescherming tegen bedreigingen voor IoT in Azure Defender voor IoT

Azure Defender voor IoT biedt meer mogelijkheden voor bescherming tegen bedreigingen dankzij de toevoeging van CyberX-technologie zonder agent. Dit biedt beveiliging voor niet-beheerde verouderde apparaten die worden gebruikt in omgevingen met operationele technologie (OT) zoals productie, BMS (Building Management Systems), life sciences, nutsbedrijven, olie- en gaswinning en logistiek.

Meer informatie vindt u in [Inleiding tot Azure Defender voor IoT](defender-for-iot-introduction.md).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Resultaten van evaluatie van beveiligingsproblemen zijn nu beschikbaar in continue export

Maak gebruik van continue export om uw waarschuwingen en aanbevelingen in realtime te streamen naar Azure Event Hubs, Log Analytics-werkruimten of Azure Monitor. Daar kunt u deze gegevens integreren met SIEM's (zoals Azure Sentinel, Power BI, Azure Data Explorer en meer).

De hulpprogramma's voor evaluatie van beveiligingsproblemen van Security Center retourneren informatie over uw resources als aanbevelingen voor het uitvoeren van acties in een 'hoofdaanbeveling', zoals 'beveiligingsproblemen op uw virtuele machines, moeten worden hersteld'. 

De beveiligingsresultaten zijn nu beschikbaar voor export door middel van continue export wanneer u aanbevelingen selecteert en de optie **Beveiligingsresultaten insluiten** in te schakelen.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="De schakeloptie Beveiligingsresultaten insluiten in de configuratie voor continue export" :::

Gerelateerde pagina's:

- [De geïntegreerde oplossing van Security Center voor de evaluatie van beveiligingsproblemen met virtuele Azure-machines](deploy-vulnerability-assessment-vm.md)
- [De geïntegreerde oplossing van Security Center voor de evaluatie van beveiligingsproblemen met Azure Container Registry](defender-for-container-registries-usage.md)
- [Continue export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Onjuiste beveiligingsconfiguraties voorkomen door aanbevelingen af te dwingen bij het maken van nieuwe resources

Onjuiste beveiligingsconfiguraties zijn een belangrijke oorzaak van beveiligingsincidenten. Security Center biedt nu de mogelijkheid om onjuiste configuratie van nieuwe resources met betrekking tot specifieke aanbevelingen te helpen *voorkomen*. 

Deze functie kan u helpen uw workloads veilig te houden en uw beveiligingsscore stabiel te houden.

Het afdwingen van een veilige configuratie op basis van een specifieke aanbeveling wordt aangeboden in twee modi:

- Met behulp van de optie **Weigeren** van Azure Policy, kunt u het maken van resources die niet in orde zijn, stoppen

- Met de optie **Afdwingen** kunt u profiteren van het effect **DeployIfNotExist** van Azure Policy en niet-compatibele resources automatisch herstellen wanneer deze worden gemaakt
 
Dit is beschikbaar voor geselecteerde beveiligingsaanbevelingen en is bovenaan de pagina met resourcedetails te vinden.

Meer informatie vindt u in [Onjuiste configuraties voorkomen met de aanbevelingen voor afdwingen/weigeren](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Aanbevelingen voor netwerkbeveiligingsgroep verbeterd

De volgende beveiligingsaanbevelingen met betrekking tot netwerkbeveiligingsgroepen zijn verbeterd om enkele gevallen van fout-positieven te verminderen.

- Alle netwerkpoorten moeten worden beperkt voor de netwerkbeveiligingsgroep die is gekoppeld aan uw VM
- Beheerpoorten moeten gesloten zijn op uw virtuele machines
- Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen
- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Aanbeveling AKS-preview 'Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services' afgeschaft

De preview-aanbeveling 'Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services' wordt afgeschaft zoals beschreven in de documentatie van [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/use-pod-security-policies).

De functie voor beveiligingsbeleid voor pods (preview) wordt afgeschaft en is na 15 oktober 2020 niet langer beschikbaar in Azure Policy voor AKS.

Nadat de functie voor beveiligingsbeleid voor pods (preview) is afgeschaft, moet u de functie op alle bestaande clusters uitschakelen met behulp van de afgeschafte functie om toekomstige clusterupgrades uit te voeren en de ondersteuning van Azure te kunnen blijven gebruiken.


### <a name="email-notifications-from-azure-security-center-improved"></a>E-mailmeldingen van Azure Security Center verbeterd

E-mailmeldingen met betrekking tot beveiligingswaarschuwingen zijn als volgt verbeterd: 

- De mogelijkheid voor het verzenden van e-mailmeldingen over waarschuwingen voor alle ernstniveaus is toegevoegd
- De mogelijkheid om gebruikers op de hoogte te stellen van verschillende RBAC-rollen in het abonnement is toegevoegd
- We informeren abonnementseigenaren standaard proactief over waarschuwingen met een hoge urgentie (die een hoge waarschijnlijkheid hebben een echte inbreuk te zijn)
- Het telefoonnummerveld is verwijderd van de configuratiepagina voor e-mailmeldingen

Meer informatie vindt u in [E-mailmeldingen instellen voor beveiligingswaarschuwingen](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Beveiligingsscore bevat geen preview-aanbevelingen 

Security Center controleert uw resources, abonnementen en organisatie doorlopend op beveiligingsproblemen. Vervolgens worden alle bevindingen tot één enkele score samengevoegd, zodat u in een oogopslag uw huidige beveiligingssituatie kunt zien: hoe hoger de score, hoe lager het geïdentificeerde risiconiveau is.

Als er nieuwe bedreigingen worden gedetecteerd, wordt nieuw beveiligingsadvies beschikbaar gesteld in Security Center via nieuwe aanbevelingen. Om te voorkomen dat uw beveiligingsscore plotseling verandert, en om een respijtperiode te bieden waarin u nieuwe aanbevelingen kunt verkennen voordat ze van invloed zijn op uw scores, worden de aanbevelingen die zijn gemarkeerd als **Preview** niet meer opgenomen in de berekeningen van uw beveiligingsscore. Ze moeten, waar mogelijk, nog steeds worden hersteld. Wanneer de preview-periode is afgelopen, worden ze dus meegerekend in uw score.

Daarnaast worden in **Preview**-aanbevelingen resources niet als 'Niet in orde' weergegeven.

Een voorbeeld van een preview-aanbeveling:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="De schakeloptie Beveiligingsresultaten insluiten in de configuratie voor continue export":::

[Meer informatie over de beveiligingsscore](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Aanbevelingen bevatten nu een ernst-indicator en vernieuwingsinterval

De detailpagina voor aanbevelingen bevat nu een indicator voor het vernieuwingsinterval (indien relevant) en een duidelijke weergave van de ernst van de aanbeveling.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="De schakeloptie Beveiligingsresultaten insluiten in de configuratie voor continue export":::



## <a name="august-2020"></a>Augustus 2020

De updates in augustus zijn onder meer:

- [Inventarisatie van activa - krachtige nieuwe weergave van het beveiligingspostuur van uw assets](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Ondersteuning toegevoegd voor standaardinstellingen voor beveiliging van Azure Active Directory (voor meervoudige verificatie)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Aanbeveling voor service-principals toegevoegd](#service-principals-recommendation-added)
- [Evaluatie van beveiligingsproblemen op VM's - aanbevelingen en beleidsregels geconsolideerd](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nieuw beveiligingsbeleid voor AKS toegevoegd aan ASC-standaardinitiatief: alleen voor gebruik door klanten van beperkte preview](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventarisatie van activa - krachtige nieuwe weergave van het beveiligingspostuur van uw assets

Assetinventarisatie van Azure Security Center (momenteel in preview) biedt een manier om het beveiligingspostuur weer te geven van de resources die u hebt verbonden met Security Center.

Security Center analyseert periodiek de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Vervolgens krijgt u aanbevelingen voor het oplossen van deze beveiligingsproblemen. Wanneer een resource openstaande aanbevelingen heeft, worden deze weergegeven in de inventarisatie.

U kunt de weergave en de filters gebruiken om uw beveiligingspostuur te verkennen en verdere acties uit te voeren op basis van uw bevindingen.

Meer informatie over [assetinventarisatie](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Ondersteuning toegevoegd voor standaardinstellingen voor beveiliging van Azure Active Directory (voor meervoudige verificatie)

Security Center heeft volledige ondersteuning toegevoegd voor [standaardinstellingen voor de beveiliging](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), de gratis identiteitsbeveiliging van Microsoft.

Standaardwaarden voor beveiliging bieden vooraf geconfigureerde instellingen voor identiteitsbeveiliging om uw organisatie te beschermen tegen algemene identiteitsgerelateerde aanvallen. Standaardinstellingen voor beveiliging beschermen al meer dan 5 miljoen tenants in totaal; 50.000-tenants worden ook beveiligd door Security Center.

Security Center biedt nu een beveiligingsaanbeveling wanneer een Azure-abonnement wordt geïdentificeerd waarvoor geen standaardinstellingen voor beveiliging zijn ingeschakeld. Tot nu toe gaf Security Center de aanbeveling meervoudige verificatie in te schakelen met behulp van voorwaardelijke toegang, dat onderdeel is van de Azure Active Directory (AD) Premium-licentie. Klanten die gratis Azure AD gebruiken, raden we nu aan om de standaardinstellingen voor beveiliging in te schakelen. 

Ons doel is om meer klanten te stimuleren om hun cloudomgevingen met meervoudige verificatie te beveiligen en een van de grootste risico's te beperken die ook het meest van invloed zijn op uw [beveiligingsscore](secure-score-security-controls.md).

Meer informatie over [standaardinstellingen voor beveiliging](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


### <a name="service-principals-recommendation-added"></a>Aanbeveling voor service-principals toegevoegd

Er is een nieuwe aanbeveling toegevoegd die Security Center-klanten die beheercertificaten gebruiken om hun abonnementen te beheren, aanraadt om over te schakelen naar service-principals.

De aanbeveling, **Gebruik service-principals om uw abonnementen te beschermen, geen beheercertificaten**, adviseert u service-principals of Azure Resource Manager te gebruiken om uw abonnementen veiliger te beheren. 

Meer informatie vindt u in [Toepassings- en service-principal-objecten in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Evaluatie van beveiligingsproblemen op VM's - aanbevelingen en beleidsregels geconsolideerd

Security Center inspecteert uw VM's om te detecteren of ze een oplossing voor de evaluatie van beveiligingsproblemen uitvoeren. Als er geen oplossing voor de evaluatie van beveiligingsproblemen wordt gevonden, biedt Security Center een aanbeveling om de implementatie te vereenvoudigen.

Als er beveiligingsproblemen worden gevonden, wordt in Security Center een aanbeveling gegeven waarin de resultaten worden beschreven die u zo nodig kunt onderzoeken en herstellen.

Om ervoor te zorgen dat alle gebruikers een consistente ervaring hebben, ongeacht het type scanner dat ze gebruiken, hebben we vier aanbevelingen geïntegreerd in de volgende twee:

|Uniforme aanbeveling|Beschrijving wijziging|
|----|:----|
|**A vulnerability assessment solution should be enabled on your virtual machines** (Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines worden ingeschakeld)|Vervangt de volgende twee aanbevelingen:<br> **•** De ingebouwde oplossing voor evaluatie van beveiligingsproblemen inschakelen op virtuele machines (mogelijk gemaakt door Qualys) (nu afgeschaft) (opgenomen in Standaard-prijscategorie)<br> **•** Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines moeten worden geïnstalleerd (nu afgeschaft) (opgenomen in Standaard- en gratis prijscategorie)|
|**Beveiligingsproblemen op uw virtuele machines moeten worden hersteld**|Vervangt de volgende twee aanbevelingen:<br>**•** Beveiligingsproblemen op uw virtuele machines herstellen (mogelijk gemaakt door Qualys) (nu afgeschaft)<br>**•** Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen (nu afgeschaft)|
|||

U gebruikt nu dezelfde aanbeveling om de uitbreiding voor de evaluatie van beveiligingsproblemen van Security Center of een oplossing met een eigen licentie ("BYOL") van een partner, zoals Qualys of Rapid7, te implementeren.

Wanneer er beveiligingsproblemen worden gevonden en gerapporteerd aan Security Center, wordt u door één aanbeveling gewaarschuwd voor de bevindingen, ongeacht de oplossing voor de evaluatie van beveiligingsproblemen die deze heeft gesignaleerd.

#### <a name="updating-dependencies"></a>Afhankelijkheden bijwerken

Als u scripts, query's of automatiseringen hebt die verwijzen naar de vorige aanbevelingen of beleidssleutels/-namen, gebruikt u de onderstaande tabellen om de verwijzingen bij te werken:

##### <a name="before-august-2020"></a>Vóór augustus 2020

|Aanbeveling|Bereik|
|----|:----|
|**De ingebouwde oplossing voor evaluatie van beveiligingsproblemen inschakelen op virtuele machines (mogelijk gemaakt door Qualys)**<br>Sleutel: 550e890b-e652-4d22-8274-60b3bdb24c63|Ingebouwd|
|**Beveiligingsproblemen op uw virtuele machines herstellen (mogelijk gemaakt door Qualys)**<br>Sleutel: 1195afff-c881-495e-9bc5-1486211ae03f|Ingebouwd|
|**Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines moeten worden geïnstalleerd**<br>Sleutel: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL (Bring Your Own License)|
|**Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen**<br>Sleutel: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL (Bring Your Own License)|
||||


|Beleid|Bereik|
|----|:----|
|**Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines**<br>Beleids-id: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Ingebouwd|
|**Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen**<br>Beleids-id: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL (Bring Your Own License)|
||||


##### <a name="from-august-2020"></a>Vanaf augustus 2020

|Aanbeveling|Bereik|
|----|:----|
|**A vulnerability assessment solution should be enabled on your virtual machines** (Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines worden ingeschakeld)<br>Sleutel: ffff0522-1e88-47fc-8382-2a80ba848f5d|Ingebouwd en BYOL|
|**Beveiligingsproblemen op uw virtuele machines moeten worden hersteld**<br>Sleutel: 1195afff-c881-495e-9bc5-1486211ae03f|Ingebouwd en BYOL|
||||

|Beleid|Bereik|
|----|:----|
|[**Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Beleids-id: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Ingebouwd en BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nieuw beveiligingsbeleid voor AKS toegevoegd aan ASC-standaardinitiatief: alleen voor gebruik door klanten van beperkte preview

Om ervoor te zorgen dat Kubernetes-workloads standaard veilig zijn, voegt Security Center beleid en aanbevelingen voor beveiliging op Kubernetes-niveau toe, met inbegrip van afdwingingsopties met Kubernetes Admission Control.

De vroege fase van dit project bevat een beperkte preview en het toevoegen van nieuwe beleidsregels (standaard uitgeschakeld) aan het ASC-standaardinitiatief.

U kunt dit beleid veilig negeren en dit heeft geen invloed op uw omgeving. Als u het wilt inschakelen, meldt u zich voor de preview-versie aan op https://aka.ms/SecurityPrP en selecteert u een van de volgende opties:

1. **Enkele preview-** : alleen deelnemen aan deze beperkte preview. Vermeld expliciet 'ASC doorlopend scannen' als de preview waaraan u wilt deelnemen.
1. **Doorlopend programma**: om deel te nemen aan deze en toekomstige beperkte previews. U moet een profiel en een privacyverklaring invullen.


## <a name="july-2020"></a>Juli 2020

De updates in juli zijn onder meer:
- [De evaluatie van beveiligingsproblemen voor virtuele machines is nu beschikbaar voor niet-Marketplace-installatiekopieën](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Beveiliging tegen bedreigingen voor Azure Storage is uitgebreid tot Azure Files en Azure Data Lake Storage Gen2 (preview)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Acht nieuwe aanbevelingen voor het inschakelen van beveiligingsfuncties voor bedreigingen](#eight-new-recommendations-to-enable-threat-protection-features)
- [Verbeteringen in de containerbeveiliging - sneller zoeken in het register en vernieuwde documentatie](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Adaptieve toepassingsregelaars bijgewerkt met een nieuwe aanbeveling en ondersteuning voor jokertekens in padregels](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Zes beleidsregels voor Advanced Data Security van SQL afgeschaft](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>De evaluatie van beveiligingsproblemen voor virtuele machines is nu beschikbaar voor niet-Marketplace-installatiekopieën

Bij het implementeren van een oplossing voor de evaluatie van beveiligingsproblemen voerde Security Center eerder een validatiecontrole uit voorafgaand aan implementatie. De controle was om te bevestigen dat de virtuele doelmachine een Marketplace-SKU bevatte. 

Met deze update is de controle verwijderd en kunt u nu hulpprogramma's voor de evaluatie van beveiligingsproblemen implementeren op 'aangepaste' Windows- en Linux-machines. Aangepaste installatiekopieën zijn bestanden die u hebt gewijzigd op basis van de standaardinstellingen voor Marketplace.

Hoewel u nu de geïntegreerde uitbreiding van de evaluatie van beveiligingsproblemen kunt implementeren (mogelijk gemaakt door Qualys) op veel meer computers, is ondersteuning alleen beschikbaar als u een besturingssysteem gebruikt dat wordt vermeld in [De geïntegreerde scanner voor beveiligingsproblemen implementeren op virtuele machines van de Standaard-laag](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-vulnerability-scanner-to-your-vms)

Meer informatie over de [geïntegreerde beveiligingsscanner voor virtuele machines (vereist Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Meer informatie over het gebruik van uw eigen oplossing voor de evaluatie van beveiligingsproblemen met een privélicentie van Qualys of Rapid7 vindt u in [Een oplossing voor het scannen van problemen van een partner implementeren](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Beveiliging tegen bedreigingen voor Azure Storage is uitgebreid tot Azure Files en Azure Data Lake Storage Gen2 (preview)

Beveiliging tegen bedreigingen voor Azure Storage detecteert potentieel schadelijke activiteiten in uw Azure Storage-accounts. Security Center geeft waarschuwingen weer wanneer er wordt geprobeerd toegang te krijgen tot uw opslagaccounts. 

Uw gegevens kunnen worden beschermd, ongeacht of deze zijn opgeslagen als blobcontainers, bestandsshares of data lakes.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Acht nieuwe aanbevelingen voor het inschakelen van beveiligingsfuncties voor bedreigingen

Er zijn acht nieuwe aanbevelingen toegevoegd om een eenvoudige manier te bieden om beveiligingsfuncties van Azure Security Center voor de volgende typen resources in te schakelen: virtuele machines, App Service-abonnementen, Azure SQL Database-servers, SQL-servers op computers, Azure Storage-accounts, Azure Kubernetes Service-clusters, Azure Container Registry-registers en Azure Key Vault-kluizen.

De nieuwe aanbevelingen zijn:

- **Advanced Data Security moet zijn ingeschakeld voor Azure SQL Database-servers**
- **Advanced Data Security moet zijn ingeschakeld voor SQL-servers op computers**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure App Service-plannen**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Container Registry-registers**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Key Vault-kluizen**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Kubernetes Service-clusters**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Storage-accounts**
- **Advanced Thread Protection moet zijn ingeschakeld op virtuele machines**

Deze nieuwe aanbevelingen maken deel uit van het beveiligingsbeheer **Geavanceerde beveiliging tegen bedreigingen inschakelen**.

De aanbevelingen bevatten ook de mogelijkheid voor snelle oplossingen. 

> [!IMPORTANT]
> Als u een van deze aanbevelingen herstelt, worden er kosten in rekening gebracht voor de beveiliging van de relevante resources. Deze kosten worden onmiddellijk van kracht als er gerelateerde resources zijn in het huidige abonnement. Of in de toekomst, als u ze op een later tijdstip toevoegt.
> 
> Als u bijvoorbeeld geen Azure Kubernetes Service-clusters in uw abonnement hebt en u de beveiliging tegen bedreigingen inschakelt, worden er geen kosten in rekening gebracht. Als u in de toekomst een cluster toevoegt aan dit abonnement, wordt dit automatisch beveiligd en worden de kosten op dat moment gestart.

Meer informatie hierover vindt u op de [pagina met naslaginformatie over beveiligingsaanbevelingen](recommendations-reference.md).

Meer informatie over [bescherming tegen bedreigingen in Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Verbeteringen in de containerbeveiliging - sneller zoeken in het register en vernieuwde documentatie

Als onderdeel van de continue investeringen in het domein van containerbeveiliging zijn we blij een aanzienlijke prestatieverbetering in de dynamische scans van Security Center-containerinstallatiekopieën die zijn opgeslagen in Azure Container Registry te kunnen delen. Scans zijn nu doorgaans in ongeveer twee minuten voltooid. In sommige gevallen kunnen ze maximaal 15 minuten duren.

Ter verbetering van de duidelijkheid en richtlijnen met betrekking tot de beveiligingsmogelijkheden van containers van Azure Security Center, hebben we ook de pagina's met documentatie over containerbeveiliging vernieuwd. 

Zie de volgende artikelen voor meer informatie over containerbeveiliging van Security Center:

- [Overzicht van beveiligingsfuncties voor containers van Security Center](container-security.md)
- [Details van de integratie met Azure Container Registry](defender-for-container-registries-introduction.md)
- [Details van de integratie met Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Uw registers scannen en uw Docker-hosts beveiligen](container-security.md)
- [Beveiligingswaarschuwingen van de functies voor beveiliging tegen bedreigingen voor Azure Kubernetes Service clusters](alerts-reference.md#alerts-akscluster)
- [Beveiligingswaarschuwingen van de functies voor beveiliging tegen bedreigingen voor Azure Kubernetes Service-hosts](alerts-reference.md#alerts-containerhost)
- [Beveiligingsaanbevelingen voor containers](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Adaptieve toepassingsregelaars bijgewerkt met een nieuwe aanbeveling en ondersteuning voor jokertekens in padregels

De functie voor adaptieve toepassingsregelaars heeft twee belangrijke updates ontvangen:

* Een nieuwe aanbeveling duidt mogelijk legitiem gedrag aan dat nog niet is toegestaan. De nieuwe aanbeveling, **De Allowlist-regels in uw beleid voor adaptief toepassingsbeheer moeten worden bijgewerkt**, vraagt u om nieuwe regels aan het bestaande beleid toe te voegen om het aantal fout-positieven in adaptieve toepassingsregelaars te verminderen.

* Padregels ondersteunen nu jokertekens. Vanaf deze update kunt u regels voor toegestane paden configureren met behulp van jokertekens. Er zijn twee ondersteunde scenario's:

    * Een jokerteken aan het einde van een pad gebruiken om alle uitvoerbare bestanden in deze map en submappen toe te staan

    * Een jokerteken in het midden van een pad gebruiken om het mogelijk te maken een bekende naam van een uitvoerbaar bestand te gebruiken met een veranderende mapnaam (bijvoorbeeld persoonlijke gebruikersmappen met een bekend uitvoerbaar bestand, automatisch gegenereerde mapnamen, enzovoort).


[Meer informatie over adaptieve toepassingsregelaars](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Zes beleidsregels voor Advanced Data Security van SQL afgeschaft

Er worden zes beleidsregels met betrekking tot geavanceerde gegevensbeveiliging voor SQL-machines afgeschaft:

- Advanced Threat Protection-typen moeten zijn ingesteld op 'Alles' in de Advanced Data Security-instellingen van SQL Managed Instance
- Advanced Threat Protection-typen moeten worden ingesteld op 'Alles' in de Advanced Data Security-instellingen van SQL Server
- De instellingen voor Advanced Data Security voor het beheerde SQL-exemplaar moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- De Advanced Data Security-instellingen voor SQL Server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- E-mailmeldingen aan beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen voor het beheerde SQL-exemplaar
- E-mailmeldingen aan beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen van de SQL-server

Meer informatie over [ingebouwd beleid](security-center-policy-definitions.md).





## <a name="june-2020"></a>Juni 2020

De updates in juni zijn onder meer:
- [Beveiligingsscore-API (preview)](#secure-score-api-preview)
- [Geavanceerde gegevensbeveiliging voor SQL-machines (Azure, andere clouds en on-premises) (preview)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Twee nieuwe aanbevelingen voor het implementeren van de Log Analytics-agent op Azure Arc-machines (preview)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nieuw beleid voor het maken van configuraties voor continue export en werkstroomautomatisering op schaal](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nieuwe aanbeveling voor het gebruik van netwerkbeveiligingsgroepen om niet op internet gerichte virtuele machines te beveiligen](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nieuw beleid voor het inschakelen van beveiliging tegen bedreigingen en geavanceerde gegevensbeveiliging](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Beveiligingsscore-API (preview)

U hebt nu toegang tot uw score via de [Beveiligingsscore-API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (momenteel als preview). De API-methoden bieden de flexibiliteit om query's uit te voeren op de gegevens en uw eigen rapportagemechanisme te bouwen van uw beveiligingsscores in de loop van de tijd. U kunt bijvoorbeeld de **Beveiligingsscore**-API gebruiken om de score voor een specifiek abonnement op te halen. Daarnaast kunt u de API voor **besturingselementen van de beveiligingsscore** gebruiken om de besturingselementen voor beveiliging en de huidige score van uw abonnementen weer te geven.

Zie [het gebied voor beveiligingsscores van onze GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score) voor voorbeelden van externe hulpprogramma's die mogelijk zijn gemaakt met de Beveiligingsscore-API.

Meer informatie over [beveiligingsscore en besturingselementen voor beveiliging in Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Geavanceerde gegevensbeveiliging voor SQL-machines (Azure, andere clouds en on-premises) (preview)

De geavanceerde gegevensbeveiliging van Azure Security Center voor SQL-machines biedt nu beveiliging voor SQL-servers die worden gehost op Azure, in andere cloudomgevingen en zelfs op on-premises machines. Hiermee wordt de beveiliging voor uw systeemeigen SQL-servers van Azure uitgebreid om hybride omgevingen volledig te ondersteunen.

Geavanceerde gegevensbeveiliging biedt evaluatie van beveiligingsproblemen en geavanceerde beveiliging tegen bedreigingen voor uw SQL-machines, waar ze zich ook bevinden.

Het instellen bestaat uit twee stappen:

1. De Log Analytics-agent implementeren op de hostcomputer van uw SQL Server om verbinding te maken met het Azure-account.

1. Het inschakelen van de optionele bundel op de pagina met prijzen en instellingen van Security Center.

Meer informatie over [geavanceerde gegevensbeveiliging voor SQL-machines](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Twee nieuwe aanbevelingen voor het implementeren van de Log Analytics-agent op Azure Arc-machines (preview)

Er zijn twee nieuwe aanbevelingen toegevoegd om de [Log Analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) te implementeren op uw Azure Arc-machines en ervoor te zorgen dat ze worden beveiligd door Azure Security Center:

- **De Log Analytics-agent moet zijn geïnstalleerd op uw Windows Azure Arc-machines (preview)**
- **De Log Analytics-agent moet zijn geïnstalleerd op uw Linux Azure Arc-machines (preview)**

Deze nieuwe aanbevelingen worden weergegeven in dezelfde vier besturingselementen voor beveiliging als de bestaande (gerelateerde) aanbeveling, **De bewakingsagent moet op uw computers worden geïnstalleerd**: beveiligingsconfiguraties herstellen, adaptieve toepassingsregelaars toepassen, systeemupdates toepassen en eindpuntbeveiliging inschakelen.

De aanbevelingen omvatten ook de mogelijkheid voor snelle oplossingen om het implementatieproces te versnellen. 

Meer informatie over deze twee nieuwe aanbevelingen vindt u in de tabel [Compute- en app-aanbevelingen](recommendations-reference.md#recs-computeapp).

Meer informatie over hoe Azure Security Center de agent gebruikt, vindt u in [Wat is de Log Analytics-agent?](faq-data-collection-agents.md#what-is-the-log-analytics-agent).

Meer informatie over [extensies voor Azure Arc-machines](../azure-arc/servers/manage-vm-extensions.md#enable-extensions-from-the-portal).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nieuw beleid voor het maken van configuraties voor continue export en werkstroomautomatisering op schaal

Het automatiseren van de processen voor bewaking en reageren op incidenten van uw organisatie kan de tijd die nodig is om beveiligingsincidenten te onderzoeken en te verhelpen aanzienlijk verbeteren.

Als u uw automatiseringsconfiguraties in uw organisatie wilt implementeren, gebruikt u deze ingebouwde DeployIfdNotExist-beleidsregels van Azure voor het maken en configureren van procedures voor [continue export](continuous-export.md) en [werkstroomautomatisering](workflow-automation.md):

Het beleid is te vinden in Azure Policy:


|Doel  |Beleid  |Beleids-id  |
|---------|---------|---------|
|Continue export naar Event Hub|[Export implementeren in Event Hub voor Azure Security Center-waarschuwingen en -aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Continue export naar Log Analytics-werkruimte|[Export implementeren in Log Analytics-werkruimte voor Azure Security Center-waarschuwingen en -aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Werkstroomautomatisering voor beveiligingswaarschuwingen|[Werkstroomautomatisering implementeren voor Azure Security Center-waarschuwingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Werkstroomautomatisering voor beveiligingsaanbevelingen|[Werkstroomautomatisering implementeren voor Azure Security Center-aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Aan de slag met [sjablonen voor werkstroomautomatisering](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Meer informatie over het gebruik van de twee beleidsregels voor export vindt u in [Azure Security Center-waarschuwingen en -aanbevelingen continu exporteren via Policy](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nieuwe aanbeveling voor het gebruik van netwerkbeveiligingsgroepen om niet op internet gerichte virtuele machines te beveiligen

Het besturingselement voor beveiliging 'aanbevolen procedures voor beveiliging implementeren' bevat nu de volgende nieuwe aanbeveling:

- **Niet-internetgerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen**

In een bestaande aanbeveling, **Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen**, werd geen onderscheid gemaakt tussen op internet gerichte en niet op internet gerichte virtuele machines. Voor beide werd een aanbeveling met een hoge urgentie gegenereerd als een virtuele machine niet aan een netwerkbeveiligingsgroep was toegewezen. In deze nieuwe aanbeveling wordt een onderscheid gemaakt met niet op internet gerichte machines om de fout-positieven te verminderen en onnodige waarschuwingen met hoge urgentie te voorkomen.

Meer informatie vindt u in de tabel [Aanbevelingen voor netwerken](recommendations-reference.md#recs-network).




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nieuw beleid voor het inschakelen van beveiliging tegen bedreigingen en geavanceerde gegevensbeveiliging

De onderstaande nieuwe beleidsregels zijn toegevoegd aan het ASC-standaardinitiatief en zijn ontworpen om te helpen bij het inschakelen van beveiliging tegen bedreigingen of geavanceerde gegevensbeveiliging voor de relevante resourcetypen.

Het beleid is te vinden in Azure Policy:


| Beleid                                                                                                                                                                                                                                                                | Beleids-id                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Advanced Data Security moet zijn ingeschakeld voor Azure SQL Database-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Advanced Data Security moet zijn ingeschakeld voor SQL-servers op computers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Storage-accounts](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Key Vault-kluizen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure App Service-plannen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Container Registry-registers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Kubernetes Service-clusters](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Advanced Thread Protection moet zijn ingeschakeld op virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Meer informatie over [bescherming tegen bedreigingen in Azure Security Center](azure-defender.md).





## <a name="may-2020"></a>Mei 2020

De updates in mei zijn onder meer:
- [Regels voor waarschuwingsonderdrukking (preview)](#alert-suppression-rules-preview)
- [Evaluatie van beveiligingsproblemen van virtuele machines is nu algemeen beschikbaar](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Wijzigingen in de Just-In-Time-toegang van virtuele machines (VM)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Aangepaste aanbevelingen zijn verplaatst naar een afzonderlijk besturingselement voor beveiliging](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Schakeloptie toegevoegd om aanbevelingen weer te geven in besturingselementen of als een platte lijst](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Besturingselement voor beveiliging 'Aanbevolen procedures voor beveiliging implementeren' uitgebreid](#expanded-security-control-implement-security-best-practices)
- [Aangepaste beleidsregels met aangepaste metagegevens zijn nu algemeen beschikbaar](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Mogelijkheden voor crashdumpanalyse migreren naar detectie van bestandsloze aanvallen](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regels voor waarschuwingsonderdrukking (preview)

Met deze nieuwe functie (momenteel in preview) kunt u overbodige waarschuwingen verminderen. Gebruik regels om waarschuwingen waarvan bekend is dat ze onschuldig of gerelateerd zijn aan normale activiteiten in uw organisatie automatisch te verbergen. Zo kunt u zich richten op de meest relevante bedreigingen. 

Waarschuwingen die overeenkomen met uw ingeschakelde onderdrukkingsregels worden nog steeds gegenereerd, maar de status wordt ingesteld op 'Genegeerd'. U kunt de status bekijken in Azure Portal of de manier waarop u uw Security Center beveiligingswaarschuwingen bekijkt.

Met onderdrukkingsregels worden de criteria gedefinieerd waarvoor waarschuwingen automatisch moeten worden genegeerd. Normaal gesproken gebruikt u een onderdrukkingsregel voor het volgende:

- waarschuwingen onderdrukken die u als fout-positieven hebt geïdentificeerd

- waarschuwingen onderdrukken die te vaak worden geactiveerd om nuttig te zijn

Meer informatie over [het onderdrukken van waarschuwingen voor beveiliging tegen bedreigingen van Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Evaluatie van beveiligingsproblemen van virtuele machines is nu algemeen beschikbaar

De Standaard-laag van Security Center bevat nu een geïntegreerde evaluatie van beveiligingsproblemen voor virtuele machines zonder extra kosten. Deze uitbreiding wordt mogelijk gemaakt door Qualys, maar rapporteert de resultaten direct terug naar Security Center. U hebt geen Qualys-licentie of Qualys-account nodig. De scans worden naadloos uitgevoerd in Security Center.

De nieuwe oplossing kan voortdurend uw virtuele machines scannen om beveiligingsproblemen op te sporen en de bevindingen in Security Center weergeven. 

Als u de oplossing wilt implementeren, gebruikt u de nieuwe beveiligingsaanbeveling:

'De ingebouwde oplossing voor evaluatie van beveiligingsproblemen inschakelen op virtuele machines (mogelijk gemaakt door Qualys)'

Meer informatie over [De geïntegreerde evaluatie van beveiligingsproblemen voor virtuele machines van Security Center](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Wijzigingen in de Just-In-Time-toegang van virtuele machines (VM)

Security Center bevat een optionele functie waarmee u de beheerpoorten van uw virtuele machines kunt beveiligen. Dit biedt een verdediging tegen de meest voorkomende vorm van beveiligingsaanvallen.

Met deze update worden de volgende wijzigingen doorgevoerd in deze functie:

- De naam van de aanbeveling waarmee u wordt geadviseerd om JIT in te schakelen, is gewijzigd. Eerder was de naam 'Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines' en nu is de naam: 'Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer'.

- De aanbeveling wordt alleen geactiveerd als er open beheerpoorten zijn.

Meer informatie over [de JIT-toegangsfunctie](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Aangepaste aanbevelingen zijn verplaatst naar een afzonderlijk besturingselement voor beveiliging

Een besturingselement voor beveiliging dat is geïntroduceerd in de verbeterde beveiligingsscore is 'Aanbevolen procedures voor beveiliging implementeren'. Aangepaste aanbevelingen die zijn gemaakt voor uw abonnementen, worden automatisch in dat besturingselement geplaatst. 

Om u te helpen uw aangepaste aanbevelingen gemakkelijker te vinden, zijn deze naar een speciaal besturingselement voor beveiliging, 'Aangepaste aanbevelingen ', verplaatst. Dit besturingselement heeft geen invloed op uw beveiligingsscore.

Meer informatie over besturingselementen voor beveiliging vindt u in [Verbeterde beveiligingsscore (preview) in Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Schakeloptie toegevoegd om aanbevelingen weer te geven in besturingselementen of als een platte lijst

Besturingselementen voor beveiliging zijn logische groepen van gerelateerde beveiligingsaanbevelingen. Ze zijn gebaseerd op gebieden waar u kwetsbaar bent voor aanvallen. Een besturingselement is een reeks beveiligingsaanbevelingen met instructies die u helpen bij het implementeren van deze aanbevelingen.

Als u onmiddellijk wilt zien hoe goed uw organisatie een afzonderlijke kwetsbaarheid beveiligt, controleert u de scores voor elk besturingselement voor beveiliging.

Uw aanbevelingen worden standaard weergegeven in de besturingselementen voor beveiliging. Vanaf deze update kunt u ze ook als een lijst weergeven. Als u ze wilt weergeven als een eenvoudige lijst, gesorteerd op de status van de betrokken resources, gebruikt u de nieuwe schakeloptie 'Groeperen op besturingselementen'. De schakeloptie bevindt zich boven de lijst in de portal.

De besturingselementen voor beveiliging - en deze schakeloptie - maken deel uit van de vernieuwde beveiligingsscore. Vergeet niet om ons uw feedback te sturen vanuit de portal.

Meer informatie over besturingselementen voor beveiliging vindt u in [Verbeterde beveiligingsscore (preview) in Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="De schakeloptie Beveiligingsresultaten insluiten in de configuratie voor continue export":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Besturingselement voor beveiliging 'Aanbevolen procedures voor beveiliging implementeren' uitgebreid 

Een besturingselement voor beveiliging dat is geïntroduceerd in de verbeterde beveiligingsscore is 'Aanbevolen procedures voor beveiliging implementeren'. Wanneer een aanbeveling zich in dit besturingselement bevindt, heeft dit geen invloed op de beveiligingsscore. 

Met deze update zijn drie aanbevelingen uit de besturingselementen waarin deze oorspronkelijk zijn geplaatst naar dit besturingselement voor aanbevolen procedures verplaatst. We hebben deze stap doorgevoerd omdat er is vastgesteld dat het risico van deze drie aanbevelingen lager is dan oorspronkelijk werd aangenomen.

Daarnaast zijn er twee nieuwe aanbevelingen geïntroduceerd en toegevoegd aan dit besturingselement.

De drie aanbevelingen die zijn verplaatst, zijn:

- **MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement** (oorspronkelijk in het besturingselement 'MFA inschakelen')
- **Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement** (oorspronkelijk in het besturingselement 'Toegang en machtigingen beheren')
- **Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement** (oorspronkelijk in het besturingselement 'Toegang en machtigingen beheren')

De twee nieuwe aanbevelingen die aan het besturingselement zijn toegevoegd, zijn:

- **De Gastconfiguratie-extensie moet op virtuele Windows-machines zijn geïnstalleerd (preview)** : het gebruik van [Azure Policy-gastconfiguratie](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) biedt inzicht in de instellingen van de virtuele machines op de server en toepassing (alleen Windows).

- **Windows Defender Exploit Guard moet zijn ingeschakeld op uw computers (preview)** : Windows Defender Exploit Guard maakt gebruik van de Azure Policy-gastconfiguratieagent. Exploit Guard bestaat uit vier onderdelen die zijn ontworpen om apparaten te vergrendelen tegen diverse aanvalsvectoren en blokkeergedrag die in malware-aanvallen worden gebruikt en die bedrijven de mogelijkheid bieden om een goede balans te vinden tussen hun vereisten op het gebied van beveiligingsrisico's en productiviteit (alleen Windows).

Meer informatie over Windows Defender Exploit Guard vindt u in [Een Exploit Guard-beleid maken en implementeren](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Meer informatie over besturingselementen voor beveiliging vindt u in [Verbeterde beveiligingsscore (preview)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Aangepaste beleidsregels met aangepaste metagegevens zijn nu algemeen beschikbaar

Aangepaste beleidsregels maken nu deel uit van de Security Center-aanbevelingen, de beveiligingsscore en het dashboard voor naleving van regelgeving. Deze functie is nu algemeen beschikbaar en biedt u de mogelijkheid om de dekking van de evaluatie van beveiligingsproblemen van uw organisatie in Security Center uit te breiden. 

Maak een aangepaste initiatief in Azure Policy, voeg er beleidsregels aan toe en onboard dit naar Azure Security Center, en visualiseer het vervolgens als aanbevelingen.

We hebben nu ook de optie toegevoegd voor het bewerken van de aangepaste metagegevens voor aanbevelingen. Opties voor metagegevens zijn ernst, herstelstappen, informatie over bedreigingen en meer.  

Meer informatie over [uw aangepaste aanbevelingen verbeteren met gedetailleerde informatie](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Mogelijkheden voor crashdumpanalyse migreren naar detectie van bestandsloze aanvallen 

De detectiemogelijkheden van Windows Crash Dump Analysis (CDA) worden geïntegreerd in [detectie van bestandsloze aanvallen](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). Analyse van detectie van bestandsloze aanvallen biedt verbeterde versies van de volgende beveiligingswaarschuwingen voor Windows-computers: 'Code-injectie gedetecteerd', 'Verdachte Windows-module gedetecteerd', 'Shellcode gedetecteerd' en 'Verdacht codesegment gedetecteerd'.

Enkele voordelen hiervan:

- **Proactieve en tijdige detectie van malware**: bij de CDA-benadering werd gewacht op een crash, en werden vervolgens analyses uitgevoerd om schadelijke artefacten te vinden. Bij het gebruik van detectie van bestandsloze aanvallen worden bedreigingen in het geheugen proactief geïdentificeerd terwijl ze worden uitgevoerd. 

- **Verrijkte waarschuwingen**: de beveiligingswaarschuwingen van detectie van bestandsloze aanvallen bevatten verrijkingen die niet beschikbaar zijn via CDA, zoals informatie over actieve netwerkverbindingen. 

- **Waarschuwingsaggregatie**: wanneer CDA meerdere aanvalspatronen in één crashdump heeft gedetecteerd, worden er meerdere beveiligingswaarschuwingen geactiveerd. Bij detectie van bestandsloze aanvallen worden alle geïdentificeerde aanvalspatronen uit hetzelfde proces gecombineerd tot één waarschuwing, waardoor de noodzaak om meerdere waarschuwingen met elkaar in verband te brengen, wordt weggenomen.

- **Minder vereisten voor uw Log Analytics-werkruimte**: crashdumps met mogelijk gevoelige gegevens worden niet meer geüpload naar uw Log Analytics-werkruimte.



## <a name="april-2020"></a>April 2020

De updates in april zijn onder meer:
- [Dynamische nalevingspakketten zijn nu algemeen beschikbaar](#dynamic-compliance-packages-are-now-generally-available)
- [Identiteitsaanbevelingen nu opgenomen in de gratis laag van Azure Security Center](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamische nalevingspakketten zijn nu algemeen beschikbaar

Het dashboard voor naleving van regelgeving van Azure Security Center bevat nu **dynamische nalevingspakketten** (nu algemeen beschikbaar) om aanvullende regelgevende en bedrijfstakstandaarden bij te houden.

Dynamische nalevingspakketten kunnen worden toegevoegd aan uw abonnement of beheergroep via de pagina met beveiligingsbeleid van Security Center. Wanneer u een standaard of benchmark hebt geïntroduceerd, wordt de standaard weergegeven in het dashboard voor naleving van regelgeving met alle gekoppelde nalevingsgegevens die zijn gekoppeld als evaluaties. Er kan een overzichtsrapport voor alle geïntroduceerde standaarden worden gedownload.

U kunt nu standaarden toevoegen zoals:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK OFFICIAL en UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nieuw)** (een meer volledige weergave van Azure CIS 1.1.0)

Daarnaast hebben we onlangs de **Azure Security-benchmark** toegevoegd, de door Microsoft ontworpen, voor Azure specifieke richtlijnen voor aanbevolen procedures voor beveiliging en naleving op basis van algemene nalevingskaders. Er worden extra standaarden ondersteund in het dashboard zodra deze beschikbaar komen.  
 
Meer informatie over het [aanpassen van de set standaarden in uw dashboard voor naleving van regelgeving](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Identiteitsaanbevelingen nu opgenomen in de gratis laag van Azure Security Center

Beveiligingsaanbevelingen voor identiteit en toegang in de gratis laag van Azure Security Center zijn nu algemeen beschikbaar. Dit maakt deel uit van de inspanningen om de CSPM-functies (Cloud Security Posture Management) gratis te maken. Tot nu toe zijn deze aanbevelingen alleen beschikbaar in de prijscategorie Standaard.

Voorbeelden van aanbevelingen voor identiteit en toegang zijn onder meer:

- 'Meervoudige verificatie moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement.'
- 'Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement.'
- 'Afgeschafte accounts moeten worden verwijderd uit uw abonnement.'

Als u abonnementen hebt in de gratis laag, worden de beveiligingsscores ervan beïnvloed door deze wijziging omdat ze nooit zijn geëvalueerd op hun identiteits- en toegangsbeveiliging.

Meer informatie over [aanbevelingen voor identiteit en toegang](recommendations-reference.md#recs-identity).

Meer informatie over [bewaking van identiteit en toegang](security-center-identity-access.md).