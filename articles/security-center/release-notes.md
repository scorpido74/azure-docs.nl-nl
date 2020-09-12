---
title: Release opmerkingen voor Azure Security Center
description: Een beschrijving van wat er nieuw is en gewijzigd in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: memildin
ms.openlocfilehash: f3aeccd30a9c89c2a43dfb85d4a57274037ec05f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569251"
---
# <a name="whats-new-in-azure-security-center"></a>Wat is er nieuw in Azure Security Center?

Azure-beveiliging is in actieve ontwikkeling en ontvangt voortdurend verbeteringen. Om op de hoogte te blijven van de meest recente ontwikkelingen, biedt deze pagina informatie over:

- Nieuwe functies
- Opgeloste fouten
- Afgeschafte functionaliteit

Deze pagina wordt regel matig bijgewerkt. Ga daarom vaak opnieuw te werk. Als u op zoek bent naar items die ouder zijn dan zes maanden, kunt u deze vinden in het [Archief voor wat er nieuw is in azure Security Center](release-notes-archive.md).


## <a name="september-2020"></a>September 2020

De updates in september zijn onder andere:

- [Conclusies voor evaluatie van beveiligings problemen zijn nu beschikbaar in continue export](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Beveiligings onjuiste configuraties voor komen door aanbevelingen af te dwingen bij het maken van nieuwe resources](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Aanbevelingen voor de netwerk beveiligings groep zijn verbeterd](#network-security-group-recommendations-improved)
- [Afgeschafte aanbeveling voor AKS ' pod-beveiligings beleid moet worden gedefinieerd op Kubernetes Services '](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [E-mail meldingen van Azure Security Center verbeterd](#email-notifications-from-azure-security-center-improved)
- [De beveiligde Score bevat geen preview-aanbevelingen](#secure-score-doesnt-include-preview-recommendations)
- [Aanbevelingen bevatten nu een Ernst-indicator en het interval voor versheid](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)

### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Conclusies voor evaluatie van beveiligings problemen zijn nu beschikbaar in continue export

Gebruik continue export om uw waarschuwingen en aanbevelingen in realtime te streamen naar Azure Event Hubs, Log Analytics werk ruimten of Azure Monitor. Daar kunt u deze gegevens integreren met Siem's (zoals Azure Sentinel, Power BI, Azure Data Explorer en meer.

De hulpprogram ma's voor evaluatie van beveiligings problemen van Security Center retour neren informatie over uw resources als aanbevelingen voor het uitvoeren van acties in een ' bovenliggende ' aanbeveling, zoals ' beveiligings problemen in uw virtuele machines, moeten worden hersteld '. 

De beveiligings bevindingen zijn nu beschikbaar om te worden geëxporteerd via continue export wanneer u aanbevelingen selecteert en de optie **beveiligings resultaten toevoegen** inschakelt.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Beveiligings resultaten toevoegen in-/uitschakelen configuratie voor continue export" :::

Gerelateerde pagina's:

- [De oplossing voor het evalueren van geïntegreerde beveiligings problemen voor Azure virtual machines Security Center](deploy-vulnerability-assessment-vm.md)
- [De oplossing voor de evaluatie van geïntegreerde beveiligings problemen van Security Center voor Azure Container Registry installatie kopieën](monitor-container-security.md)
- [Continue export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Beveiligings onjuiste configuraties voor komen door aanbevelingen af te dwingen bij het maken van nieuwe resources

Onjuiste beveiligings configuraties zijn een grote oorzaak van beveiligings incidenten. Security Center heeft nu de mogelijkheid om onjuiste configuratie van nieuwe resources met betrekking tot specifieke aanbevelingen te helpen *voor komen* . 

Deze functie kan u helpen uw workloads veilig te houden en uw veilige score te stabiliseren.

Het afdwingen van een veilige configuratie op basis van een specifieke aanbeveling wordt aangeboden in twee modi:

- Met het **weigeren** van het effect van Azure Policy kunt u stoppen met het maken van beschadigde resources

- Met de optie **afdwingen** kunt u profiteren van het **DeployIfNotExist** -effect van het Azure-beleid en niet-compatibele resources automatisch herstellen wanneer deze worden gemaakt
 
Dit is beschikbaar voor geselecteerde beveiligings aanbevelingen en is te vinden boven aan de pagina Resource Details.

Meer informatie over het [voor komen van onjuiste configuraties met het afdwingen/weigeren van aanbevelingen](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Aanbevelingen voor de netwerk beveiligings groep zijn verbeterd

De volgende beveiligings aanbevelingen met betrekking tot netwerk beveiligings groepen zijn verbeterd om sommige instanties van valse positieven te verminderen.

- Alle netwerk poorten moeten worden beperkt op NSG die zijn gekoppeld aan uw virtuele machine
- Beheerpoorten moeten gesloten zijn op uw virtuele machines
- Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen
- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Afgeschafte aanbeveling voor AKS ' pod-beveiligings beleid moet worden gedefinieerd op Kubernetes Services '

De preview-aanbeveling ' pod Security Policies ' moet worden gedefinieerd op Kubernetes Services ' wordt afgeschaft zoals beschreven in de documentatie van de [Azure Kubernetes-service](https://docs.microsoft.com/azure/aks/use-pod-security-policies) .

De functie beveiligings beleid voor pod (preview) is ingesteld voor afschaffing en is na 15 oktober 2020 niet langer beschikbaar voor Azure Policy voor AKS.

Nadat pod-beveiligings beleid (preview) is afgeschaft, moet u de functie op alle bestaande clusters uitschakelen met behulp van de afgeschafte functie om toekomstige cluster upgrades uit te voeren en de ondersteuning van Azure te blijven gebruiken.


### <a name="email-notifications-from-azure-security-center-improved"></a>E-mail meldingen van Azure Security Center verbeterd

De volgende gebieden van de e-mail berichten met betrekking tot beveiligings waarschuwingen zijn verbeterd: 

- De mogelijkheid voor het verzenden van e-mail meldingen over waarschuwingen voor alle ernst niveaus is toegevoegd
- De mogelijkheid om gebruikers op de hoogte te stellen van verschillende RBAC-rollen in het abonnement
- Er worden proactief geabonneerd op abonnements eigenaren op waarschuwingen met hoge urgentie (die een hoge kans hebben op legitieme inbreuken)
- Het veld telefoon nummer is verwijderd van de pagina configuratie van e-mail meldingen

Meer informatie vindt [u in e-mail meldingen instellen voor beveiligings waarschuwingen](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>De beveiligde Score bevat geen preview-aanbevelingen 

Met Security Center worden uw resources, abonnementen en organisaties doorlopend beoordeeld op beveiligings problemen. Vervolgens worden alle bevindingen in één enkele score geaggregeerd, zodat u in een oogopslag uw huidige beveiligings situatie kunt zien: hoe hoger de score, hoe lager het geïdentificeerde risico niveau is.

Als er nieuwe bedreigingen worden gedetecteerd, wordt nieuw beveiligings advies beschikbaar gesteld in Security Center via nieuwe aanbevelingen. Om te voor komen dat uw beveiligde Score plotseling wordt gewijzigd en u een respijt periode wilt bieden waarin u nieuwe aanbevelingen kunt verkennen voordat deze van invloed zijn op uw scores, worden de aanbevelingen die als **Preview** zijn gemarkeerd, niet meer opgenomen in de berekeningen van uw beveiligde Score. Ze moeten, waar mogelijk, nog steeds worden hersteld, zodat wanneer de preview-periode afloopt dat ze bijdragen aan uw score.

Daarnaast genereren **Preview** -aanbevelingen geen resource ' beschadigd '.

Een voor beeld van een voor beeld van een preview-aanbeveling:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Aanbeveling met de vlag preview":::

Meer [informatie over beveiligde scores](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Aanbevelingen bevatten nu een Ernst-indicator en het interval voor versheid

De pagina Details voor aanbevelingen bevat nu een interval voor versheid (indien van toepassing) en een duidelijke weer gave van de ernst van de aanbeveling.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Pagina aanbeveling met versheid en ernst":::



## <a name="august-2020"></a>Augustus 2020

De updates in augustus zijn onder andere:

- [Inventaris van activa-krachtige nieuwe weer gave van de beveiligings postuur van uw assets](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Ondersteuning toegevoegd voor Azure Active Directory standaard instellingen voor beveiliging (voor multi-factor Authentication)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Aanbeveling voor service-principals toegevoegd](#service-principals-recommendation-added)
- [Evaluatie van beveiligings problemen op Vm's-aanbevelingen en beleids regels geconsolideerd](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nieuwe beveiligings beleidsregels voor AKS toegevoegd aan ASC_default Initiative: alleen voor gebruik door privé preview-klanten](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventaris van activa-krachtige nieuwe weer gave van de beveiligings postuur van uw assets

De Asset Inventory van Security Center (momenteel als preview-versie) biedt een manier om de beveiligings postuur weer te geven van de resources waarmee u verbinding hebt gemaakt met Security Center.

Security Center regel matig de beveiligings status van uw Azure-resources analyseren om mogelijke beveiligings problemen te identificeren. Vervolgens krijgt u aanbevelingen voor het oplossen van deze beveiligings problemen. Wanneer een resource openstaande aanbevelingen heeft, worden deze weer gegeven in de inventarisatie.

U kunt de weer gave en de filters gebruiken om uw beveiligings postuur te verkennen en verdere acties uit te voeren op basis van uw bevindingen.

Meer informatie over [inventarisatie van activa](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Ondersteuning toegevoegd voor Azure Active Directory standaard instellingen voor beveiliging (voor multi-factor Authentication)

Security Center heeft volledige ondersteuning toegevoegd voor de [standaard instellingen](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)van de beveiliging, de gratis identiteits beveiliging van micro soft.

Standaard waarden voor beveiliging bieden vooraf geconfigureerde identiteits beveiligings instellingen om uw organisatie te beschermen tegen algemene identiteits-gerelateerde aanvallen. Beveiligings standaards beveiligen al meer dan 5.000.000 tenants in het algemeen; 50.000-tenants worden ook beveiligd door Security Center.

Security Center biedt nu een beveiligings aanbeveling wanneer een Azure-abonnement wordt geïdentificeerd zonder dat de standaard instellingen voor beveiliging zijn ingeschakeld. Tot nu toe Security Center aanbevolen multi-factor Authentication in te scha kelen met behulp van voorwaardelijke toegang, dat onderdeel is van de Azure Active Directory (AD) Premium-licentie. Voor klanten die Azure AD gratis gebruiken, raden we u aan om de standaard instellingen voor beveiliging in te scha kelen. 

Ons doel is om meer klanten te stimuleren om hun Cloud omgevingen met MFA te beveiligen en een van de grootste Risico's te beperken die ook het meest van invloed zijn op uw [beveiligde Score](https://docs.microsoft.com/azure/security-center/secure-score-security-controls).

Meer informatie over de [standaard instellingen](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)van de beveiliging.


### <a name="service-principals-recommendation-added"></a>Aanbeveling voor service-principals toegevoegd

Er is een nieuwe aanbeveling toegevoegd waarmee Security Center klanten die beheer certificaten gebruiken om hun abonnementen switch te beheren naar service-principals.

De aanbeveling, **service-principals moeten worden gebruikt voor het beveiligen van uw abonnementen in plaats van beheer certificaten** , adviseert u service-principals of Azure Resource Manager te gebruiken om uw abonnementen veiliger te beheren. 

Meer informatie over [toepassings-en Service-Principal-objecten in azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Evaluatie van beveiligings problemen op Vm's-aanbevelingen en beleids regels geconsolideerd

Security Center inspecteert uw Vm's om te detecteren of ze een oplossing voor de evaluatie van beveiligings problemen uitvoeren. Als er geen oplossing voor de evaluatie van beveiligings problemen wordt gevonden, biedt Security Center een aanbeveling om de implementatie te vereenvoudigen.

Als er beveiligings problemen worden gevonden, wordt in Security Center een aanbeveling gegeven waarin de resultaten worden beschreven die u zo nodig kunt onderzoeken en herstellen.

Om ervoor te zorgen dat alle gebruikers een consistente ervaring hebben, ongeacht het type scanner dat ze gebruiken, hebben we vier aanbevelingen ondergaan in de volgende twee:

|Uniforme aanbeveling|Beschrijving wijzigen|
|----|:----|
|**Er moet een oplossing voor de evaluatie van beveiligings problemen worden ingeschakeld op uw virtuele machines**|Vervangt de volgende twee aanbevelingen:<br> **•** De ingebouwde oplossing voor de evaluatie van beveiligings problemen inschakelen op virtuele machines (mogelijk gemaakt door Qualys (nu afgeschaft) (inbegrepen bij de laag standaard)<br> **•** De oplossing voor de evaluatie van beveiligings problemen moet worden geïnstalleerd op uw virtuele machines (nu afgeschaft) (standaard en gratis lagen)|
|**Beveiligings problemen in uw virtuele machines moeten worden hersteld**|Vervangt de volgende twee aanbevelingen:<br>**•** Beveiligings problemen op uw virtuele machines herstellen (mogelijk gemaakt door Qualys) (nu afgeschaft)<br>**•** Beveiligings problemen moeten worden opgelost door een oplossing voor beveiligings evaluatie (nu afgeschaft)|
|||

U gaat nu dezelfde aanbeveling gebruiken om de uitbrei ding voor de evaluatie van beveiligings problemen van Security Center of een privé-oplossing ("BYOL") te implementeren van een partner zoals Qualys of Rapid7.

Wanneer er beveiligings lekken worden gevonden en gerapporteerd aan Security Center, wordt u door één aanbeveling gewaarschuwd voor de bevindingen, ongeacht de oplossing voor de evaluatie van beveiligings problemen die ze hebben geïdentificeerd.

#### <a name="updating-dependencies"></a>Afhankelijkheden bijwerken

Als u scripts, query's of Automatiseringen hebt die verwijzen naar de vorige aanbevelingen of beleids sleutels/-namen, gebruikt u de onderstaande tabellen om de verwijzingen bij te werken:

##### <a name="before-august-2020"></a>Vóór augustus 2020

|Aanbeveling|Bereik|
|----|:----|
|**De ingebouwde oplossing voor de evaluatie van beveiligings problemen inschakelen op virtuele machines (aangedreven door Qualys)**<br>Sleutel: 550e890b-e652-4d22-8274-60b3bdb24c63|Ingebouwd|
|**Beveiligings problemen op uw virtuele machines herstellen (aangedreven door Qualys)**<br>Sleutel: 1195afff-c881-495e-9bc5-1486211ae03f|Ingebouwd|
|**De oplossing voor de evaluatie van beveiligings problemen moet worden geïnstalleerd op uw virtuele machines**<br>Sleutel: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen**<br>Sleutel: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Beleid|Bereik|
|----|:----|
|**Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines**<br>Beleids-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Ingebouwd|
|**Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken**<br>Beleids-ID: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Van 2020 augustus

|Aanbeveling|Bereik|
|----|:----|
|**Er moet een oplossing voor de evaluatie van beveiligings problemen worden ingeschakeld op uw virtuele machines**<br>Sleutel: ffff0522-1e88-47fc-8382-2a80ba848f5d|Ingebouwde en BYOL|
|**Beveiligings problemen in uw virtuele machines moeten worden hersteld**<br>Sleutel: 1195afff-c881-495e-9bc5-1486211ae03f|Ingebouwde en BYOL|
||||

|Beleid|Bereik|
|----|:----|
|[**De evaluatie van beveiligings problemen moet worden ingeschakeld op virtuele machines**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Beleids-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Ingebouwde en BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nieuwe beveiligings beleidsregels voor AKS toegevoegd aan ASC_default Initiative: alleen voor gebruik door privé preview-klanten

Om ervoor te zorgen dat Kubernetes-werk belastingen standaard veilig zijn, voegt Security Center beleids regels op Kubernetes niveau toe en aanbevelingen voor beveiliging, met inbegrip van handhavings opties met Kubernetes Admission Control.

De vroege fase van dit project bevat een persoonlijke preview en het toevoegen van nieuwe beleids regels (uitgeschakeld door standaard) aan het ASC_default-initiatief.

U kunt dit beleid negeren en er is geen invloed op uw omgeving. Als u deze wilt inschakelen, meldt u zich aan voor de preview-versie https://aka.ms/SecurityPrP en selecteert u een van de volgende opties:

1. **Eén preview** – alleen toevoegen aan deze persoonlijke preview. Vermeld expliciet ' ASC doorlopende scan ' als het voor beeld dat u wilt toevoegen.
1. **Doorlopend programma** : dit wordt toegevoegd aan deze en toekomstige persoonlijke voor beelden. U moet een profiel en een privacyverklaring volt ooien.


## <a name="july-2020"></a>Juli 2020

De updates in juli zijn onder andere:
- [De evaluatie van beveiligings problemen voor virtuele machines is nu beschikbaar voor niet-Marketplace-installatie kopieën](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Beveiliging tegen bedreigingen voor Azure Storage uitgebreid tot Azure Files en Azure Data Lake Storage Gen2 bevatten (preview)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Acht nieuwe aanbevelingen voor het inschakelen van beveiligings functies voor bedreigingen](#eight-new-recommendations-to-enable-threat-protection-features)
- [Verbeteringen in de container beveiliging-sneller zoeken in het REGI ster en de vernieuwde documentatie](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Adaptieve toepassings besturings elementen bijgewerkt met een nieuwe aanbeveling en ondersteuning voor joker tekens in padregels](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Zes beleids regels voor de verouderde beveiliging van SQL Advanced Data](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>De evaluatie van beveiligings problemen voor virtuele machines is nu beschikbaar voor niet-Marketplace-installatie kopieën

Bij het implementeren van een oplossing voor de evaluatie van beveiligings problemen heeft Security Center eerder een validatie controle uitgevoerd voordat deze wordt geïmplementeerd. De controle was het bevestigen van een Marketplace-SKU van de virtuele doel machine. 

Op basis van deze update is de controle verwijderd en kunt u nu hulpprogram ma's voor evaluatie van beveiligings problemen implementeren op aangepaste Windows-en Linux-machines. Aangepaste installatie kopieën zijn bestanden die u hebt gewijzigd op basis van de standaard instellingen voor Marketplace.

Hoewel u nu de geïntegreerde uitbrei ding van het beveiligingslek kunt implementeren (mogelijk gemaakt door Qualys) op veel meer computers, is ondersteuning alleen beschikbaar als u een besturings systeem gebruikt dat wordt vermeld in [de geïntegreerde scanner voor beveiligings problemen implementeren op vm's van de Standard-laag](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-vulnerability-scanner-to-standard-tier-vms) .

Meer informatie over de [geïntegreerde scanner voor beveiligings problemen voor virtuele machines (alleen de laag standaard)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Meer informatie over het gebruik van uw eigen, privé-beoordelings oplossing voor beveiligings problemen van Qualys of Rapid7 bij [het implementeren van een oplossing voor het scannen van beveiligings problemen](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Beveiliging tegen bedreigingen voor Azure Storage uitgebreid tot Azure Files en Azure Data Lake Storage Gen2 bevatten (preview)

Bedreigings beveiliging voor Azure Storage detecteert mogelijk schadelijke activiteiten op uw Azure Storage-accounts. Security Center geeft een waarschuwing weer wanneer er wordt geprobeerd toegang te krijgen tot uw opslag accounts. 

Uw gegevens kunnen worden beveiligd, ongeacht of deze zijn opgeslagen als blob-containers, bestands shares of gegevens-meren. 

Meer informatie over [beveiliging tegen bedreigingen voor Azure Storage](threat-protection.md#threat-protection-for-azure-storage-).




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Acht nieuwe aanbevelingen voor het inschakelen van beveiligings functies voor bedreigingen

Er zijn acht nieuwe aanbevelingen toegevoegd om een eenvoudige manier te bieden om beveiligings functies van Azure Security Center voor de volgende bron typen in te scha kelen: virtual machines, App Service-plannen, Azure SQL Database servers, SQL-servers op computers, Azure Storage accounts, Azure Kubernetes-Service clusters, Azure Container Registry registers en Azure Key Vault-kluizen.

De nieuwe aanbevelingen zijn:

- **Advanced Data Security moet zijn ingeschakeld voor Azure SQL Database-servers**
- **Advanced Data Security moet zijn ingeschakeld voor SQL-servers op computers**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure App Service-plannen**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Container Registry-registers**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Key Vault-kluizen**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Kubernetes Service-clusters**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Storage-accounts**
- **Geavanceerde beveiliging tegen bedreigingen moet zijn ingeschakeld op virtuele machines**

Deze nieuwe aanbevelingen maken deel uit van het beveiligings beheer **Geavanceerd bedreigings beveiliging inschakelen** .

De aanbevelingen bevatten ook de mogelijkheid om snel te herstellen. 

> [!IMPORTANT]
> Als u een van deze aanbevelingen herstelt, worden er kosten in rekening gebracht voor de beveiliging van de relevante resources. Deze kosten worden onmiddellijk gestart als er gerelateerde resources zijn in het huidige abonnement. Of in de toekomst kunt u ze op een later tijdstip toevoegen.
> 
> Als u bijvoorbeeld geen Azure Kubernetes-Service clusters in uw abonnement hebt en u de bedreigings beveiliging inschakelt, worden er geen kosten in rekening gebracht. Als u in de toekomst een cluster toevoegt aan hetzelfde abonnement, wordt het automatisch beveiligd en worden de kosten op dat moment gestart.

Meer informatie vindt u in de [naslag pagina met aanbevelingen voor beveiliging](recommendations-reference.md).

Meer informatie over [beveiliging tegen bedreigingen vindt u in azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Verbeteringen in de container beveiliging-sneller zoeken in het REGI ster en de vernieuwde documentatie

Als onderdeel van de continue investeringen in het domein container beveiliging zijn we blij met het delen van een aanzienlijke prestatie verbetering in de dynamische scans van Security Center container installatie kopieën die zijn opgeslagen in Azure Container Registry. Scans zijn nu doorgaans in ongeveer twee minuten voltooid. In sommige gevallen kan het tot vijf tien minuten duren.

Ter verbetering van de duidelijkheid en richt lijnen met betrekking tot de beveiligings mogelijkheden van de container van Azure Security Center, hebben we ook de pagina's met documentatie over de container beveiliging vernieuwd. 

Meer informatie over de beveiliging van de container van Security Center in de volgende artikelen:

- [Overzicht van de beveiligings functies van de container van Security Center](https://docs.microsoft.com/azure/security-center/container-security)
- [Details van de integratie met Azure Container Registry](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [Details van de integratie met de Azure Kubernetes-service](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [Uw registers scannen en uw docker-hosts beveiligen](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [Beveiligings waarschuwingen van de functies voor beveiliging tegen bedreigingen voor Azure Kubernetes-Service clusters](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [Beveiligings waarschuwingen van de functies voor beveiliging tegen bedreigingen voor Azure Kubernetes-service-hosts](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [Beveiligings aanbevelingen voor containers](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Adaptieve toepassings besturings elementen bijgewerkt met een nieuwe aanbeveling en ondersteuning voor joker tekens in padregels

De functie voor besturings elementen voor adaptieve toepassingen heeft twee belang rijke updates ontvangen:

* Een nieuwe aanbeveling duidt mogelijk legitiem gedrag aan dat nog niet is toegestaan. De nieuwe aanbeveling, **Allowlist regels in uw adaptieve toepassings beheer beleid moeten worden bijgewerkt**. u wordt gevraagd nieuwe regels aan het bestaande beleid toe te voegen om het aantal fout-positieven in adaptieve toepassings besturings elementen schendings waarschuwingen te verminderen.

* Padregels ondersteunen nu Joker tekens. Vanuit deze update kunt u regels voor toegestane paden configureren met behulp van joker tekens. Er zijn twee ondersteunde scenario's:

    * Een Joker teken aan het einde van een pad gebruiken om alle uitvoer bare bestanden in deze map en submappen toe te staan

    * Een Joker teken gebruiken in het midden van een pad om een bekende naam voor een uitvoerbaar bestand met een veranderende mapnaam (bijvoorbeeld persoonlijke gebruikers mappen met een bekend uitvoerbaar bestand, automatisch gegenereerde mapnamen enz.) in te scha kelen.


Meer [informatie over adaptieve toepassings besturings elementen](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Zes beleids regels voor de verouderde beveiliging van SQL Advanced Data

Zes beleids regels met betrekking tot geavanceerde gegevens beveiliging voor SQL-machines worden afgeschaft:

- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Managed instance
- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Server
- De instellingen voor Advanced Data Security voor het beheerde SQL-exemplaar moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- De Advanced Data Security-instellingen voor SQL Server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- E-mailmeldingen aan beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen voor het beheerde SQL-exemplaar
- E-mailmeldingen aan beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen van de SQL-server

Meer informatie over [ingebouwde beleids regels](security-center-policy-definitions.md).





## <a name="june-2020"></a>Juni 2020

De updates in juni omvatten:
- [Secure Score-API (preview-versie)](#secure-score-api-preview)
- [Geavanceerde gegevens beveiliging voor SQL-machines (Azure, andere Clouds en on-premises) (preview)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Twee nieuwe aanbevelingen voor het implementeren van de Log Analytics agent op Azure Arc-machines (preview)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nieuw beleid voor het maken van continue export-en werk stroom automatiserings configuraties op schaal](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nieuwe aanbeveling voor het gebruik van Nsg's om niet-Internet gerichte virtuele machines te beveiligen](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nieuw beleid voor het inschakelen van bedreigings beveiliging en geavanceerde gegevens beveiliging](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Secure Score-API (preview-versie)

U hebt nu toegang tot uw score via de [API voor beveiligde scores](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (momenteel als preview-versie). De API-methoden bieden de flexibiliteit om query's uit te voeren op de gegevens en uw eigen rapportage mechanisme te bouwen van uw beveiligde scores in de loop van de tijd. U kunt bijvoorbeeld de API **beveiligde scores** gebruiken om de score voor een specifiek abonnement op te halen. Daarnaast kunt u de API **besturings elementen voor beveiligde scores** gebruiken om de beveiligings controles en de huidige Score van uw abonnementen weer te geven.

Voor voor beelden van externe hulpprogram ma's die mogelijk zijn gemaakt met de API voor beveiligde scores, raadpleegt u [het beveiligde Score gebied van onze github-Community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Meer informatie over [beveiligde scores en beveiligings controles in azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Geavanceerde gegevens beveiliging voor SQL-machines (Azure, andere Clouds en on-premises) (preview)

De geavanceerde gegevens beveiliging van Azure Security Center voor SQL-machines beveiligt nu SQL-servers die worden gehost in azure, op andere Cloud omgevingen en zelfs on-premises machines. Hiermee worden de beveiligingen voor uw Azure-systeem eigen SQL-servers uitgebreid om hybride omgevingen volledig te ondersteunen.

Geavanceerde gegevens beveiliging biedt evaluatie van beveiligings problemen en geavanceerde beveiliging tegen bedreigingen voor uw SQL-machines, waar ze zich ook bevinden.

Setup bestaat uit twee stappen:

1. De Log Analytics-agent implementeren op de hostcomputer van uw SQL Server om verbinding te maken met het Azure-account.

1. Het inschakelen van de optionele bundel op de pagina prijzen en instellingen van Security Center.

Meer informatie over [geavanceerde gegevens beveiliging voor SQL-machines](security-center-iaas-advanced-data.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Twee nieuwe aanbevelingen voor het implementeren van de Log Analytics agent op Azure Arc-machines (preview)

Er zijn twee nieuwe aanbevelingen toegevoegd om de Log Analytics- [agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) te implementeren op uw Azure-Arc-machines en ervoor te zorgen dat ze worden beveiligd door Azure Security Center:

- **Log Analytics agent moet worden geïnstalleerd op uw op Windows gebaseerde Azure Arc-machines (preview)**
- **Log Analytics agent moet worden geïnstalleerd op uw op Linux gebaseerde Azure-Arc-machines (preview)**

Deze nieuwe aanbevelingen worden weer gegeven in dezelfde vier beveiligings controles als de bestaande (verwante) aanbeveling, de **bewakings agent moet op uw computers worden geïnstalleerd**: beveiligings configuraties herstellen, adaptief toepassings beheer Toep assen, systeem updates Toep assen en Endpoint Protection inschakelen.

De aanbevelingen omvatten ook de mogelijkheid tot snel oplossen om het implementatie proces te versnellen. 

Meer informatie over deze twee nieuwe aanbevelingen vindt u in de tabel met [berekenings-en app-aanbevelingen](recommendations-reference.md#recs-computeapp) .

Meer informatie over hoe Azure Security Center de agent gebruikt in [Wat is de log Analytics-agent?](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent).

Meer informatie over [uitbrei dingen voor Azure Arc-machines](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nieuw beleid voor het maken van continue export-en werk stroom automatiserings configuraties op schaal

Het automatiseren van de bewakings-en reactie processen van uw organisatie kan de tijd die nodig is om beveiligings incidenten te onderzoeken en te verhelpen, aanzienlijk verbeteren.

Als u uw automatiserings configuraties in uw organisatie wilt implementeren, gebruikt u deze ingebouwde DeployIfdNotExist Azure-beleids regels voor het maken en configureren van [doorlopende export](continuous-export.md) -en [werk stroom automatiserings](workflow-automation.md) procedures:

Het beleid is te vinden in azure Policy:


|Doel  |Beleid  |Beleids-ID  |
|---------|---------|---------|
|Doorlopend exporteren naar Event Hub|[Export implementeren in Event Hub voor Azure Security Center-waarschuwingen en -aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Doorlopend exporteren naar Log Analytics werk ruimte|[Export implementeren in Log Analytics-werkruimte voor Azure Security Center-waarschuwingen en -aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Werk stroom automatisering voor beveiligings waarschuwingen|[Werkstroomautomatisering implementeren voor Azure Security Center-waarschuwingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Werk stroom automatisering voor beveiligings aanbevelingen|[Werkstroomautomatisering implementeren voor Azure Security Center-aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Aan de slag met sjablonen voor het [automatiseren van werk stromen](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Meer informatie over het gebruik van de twee export beleidsregels in [voortdurend exporteren Azure Security Center waarschuwingen en aanbevelingen via beleid](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nieuwe aanbeveling voor het gebruik van Nsg's om niet-Internet gerichte virtuele machines te beveiligen

Het beveiligings beheer voor best practices voor beveiliging bevat nu de volgende nieuwe aanbeveling:

- **Niet-internetgerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen**

Een bestaande aanbeveling, **Internet gerichte virtuele machines moeten worden beveiligd met netwerk beveiligings groepen**, heeft geen onderscheid gemaakt tussen virtuele machines met Internet gerichte en niet-Internet. Voor beide is een aanbeveling met een hoge urgentie gegenereerd als een virtuele machine niet aan een netwerk beveiligings groep is toegewezen. Deze nieuwe aanbeveling scheidt de niet-Internet gerichte machines van elkaar om de fout-positieven te reduceren en onnodige waarschuwingen met hoge urgentie te voor komen.

Meer informatie vindt u in de tabel met [netwerk aanbevelingen](recommendations-reference.md#recs-network) .




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nieuw beleid voor het inschakelen van bedreigings beveiliging en geavanceerde gegevens beveiliging

De onderstaande nieuwe beleids regels zijn toegevoegd aan het ASC-standaard initiatief en zijn ontworpen om te helpen bij het inschakelen van bedreigings beveiliging of geavanceerde gegevens beveiliging voor de relevante resource typen.

Het beleid is te vinden in azure Policy:


| Beleid                                                                                                                                                                                                                                                                | Beleids-ID                            |
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

Meer informatie over [beveiliging tegen bedreigingen vindt u in azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection).





## <a name="may-2020"></a>Mei 2020

Updates in kunnen onder andere het volgende omvatten:
- [Regels voor waarschuwings onderdrukking (preview-versie)](#alert-suppression-rules-preview)
- [De evaluatie van de beveiligings problemen van de virtuele machine is nu algemeen beschikbaar](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Wijzigingen in de just-in-time-toegang van virtuele machines (VM)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Aangepaste aanbevelingen zijn verplaatst naar een afzonderlijk beveiligings beheer](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Toevoegen/verbergen om aanbevelingen in besturings elementen of als een platte lijst weer te geven](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Uitgebreide beveiliging, aanbevolen procedures voor het implementeren van beveiliging](#expanded-security-control-implement-security-best-practices)
- [Aangepaste beleids regels met aangepaste meta gegevens zijn nu algemeen beschikbaar](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Crash dump analyse mogelijkheden migreren naar aanval op bestandsloze detectie](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regels voor waarschuwings onderdrukking (preview-versie)

Met deze nieuwe functie (momenteel in Preview) kunt u de duurzaamheid van waarschuwingen verlagen. Gebruik regels om waarschuwingen automatisch te verbergen waarvan bekend is dat ze worden Innocuous of gerelateerd aan normale activiteiten in uw organisatie. Zo kunt u zich richten op de meest relevante bedreigingen. 

Waarschuwingen die overeenkomen met uw ingeschakelde onderdrukkings regels worden nog steeds gegenereerd, maar de status wordt ingesteld op genegeerd. U kunt de status bekijken in de Azure Portal of u hebt echter toegang tot uw Security Center beveiligings waarschuwingen.

Met onderdrukkings regels worden de criteria gedefinieerd waarvoor waarschuwingen automatisch moeten worden genegeerd. Normaal gesp roken gebruikt u een onderdrukkings regel voor het volgende:

- waarschuwingen onderdrukken die u als foutieve positieven hebt geïdentificeerd

- waarschuwingen onderdrukken die te vaak worden geactiveerd om nuttig te zijn

Meer informatie over [het onderdrukken van waarschuwingen van de bedreigings beveiliging van Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>De evaluatie van de beveiligings problemen van de virtuele machine is nu algemeen beschikbaar

De Standard-laag van Security Center bevat nu een geïntegreerde evaluatie van beveiligings problemen voor virtuele machines zonder extra kosten. Deze uitbrei ding wordt mogelijk gemaakt door Qualys, maar rapporteert de resultaten direct terug naar Security Center. U hebt geen Qualys-licentie nodig of zelfs een Qualys-account: alles wordt naadloos verwerkt binnen Security Center.

De nieuwe oplossing kan voortdurend uw virtuele machines scannen om beveiligings problemen op te sporen en de bevindingen in Security Center presen teren. 

Als u de oplossing wilt implementeren, gebruikt u de nieuwe beveiligings aanbeveling:

"De ingebouwde oplossing voor de evaluatie van beveiligings problemen inschakelen op virtuele machines (aangedreven door Qualys)"

Meer informatie over [de evaluatie van het geïntegreerde beveiligings risico van Security Center voor virtuele machines](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Wijzigingen in de just-in-time-toegang van virtuele machines (VM)

Security Center bevat een optionele functie waarmee u de beheer poorten van uw virtuele machines kunt beveiligen. Dit biedt een verdediging tegen de meest voorkomende vorm van beveiligings aanvallen.

Met deze update worden de volgende wijzigingen aangebracht in deze functie:

- De aanbeveling waarmee u wordt geadviseerd om JIT in te scha kelen voor een VM, is hernoemd. Voorheen moet ' just-in-time-netwerk toegangs beheer worden toegepast op virtuele machines ' nu: ' beheer poorten van virtuele machines moeten worden beveiligd met Just-in-time-netwerk toegangs beheer '.

- De aanbeveling wordt alleen geactiveerd als er open beheer poorten zijn.

Meer informatie over [de JIT-toegangs functie](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Aangepaste aanbevelingen zijn verplaatst naar een afzonderlijk beveiligings beheer

Een beveiligings controle dat is geïntroduceerd in de verbeterde beveiligde Score is ' Implementeer beveiligings best practices '. Aangepaste aanbevelingen die zijn gemaakt voor uw abonnementen, worden automatisch in dat besturings element geplaatst. 

Om u te helpen uw aangepaste aanbevelingen gemakkelijker te vinden, zijn deze naar een speciaal beveiligings beheer, ' aangepaste aanbevelingen ', verplaatst. Dit besturings element heeft geen invloed op uw beveiligde Score.

Meer informatie over beveiligings controles in [Enhanced Secure Score (preview) in azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Toevoegen/verbergen om aanbevelingen in besturings elementen of als een platte lijst weer te geven

Beveiligings controles zijn logische groepen van gerelateerde beveiligings aanbevelingen. Ze zijn gebaseerd op uw kwets bare aanvals oppervlakken. Een besturings element is een reeks beveiligings aanbevelingen met instructies die u helpen bij het implementeren van deze aanbevelingen.

Als u onmiddellijk wilt zien hoe goed uw organisatie een afzonderlijke kwets baarheid beveiligt, controleert u de scores voor elk beveiligings beheer.

Uw aanbevelingen worden standaard weer gegeven in de beveiligings controles. Vanuit deze update kunt u ze ook weer geven als een lijst. Als u deze wilt weer geven als een eenvoudige lijst, gesorteerd op de status van de betrokken resources, gebruikt u de nieuwe wissel knop Group by Controls. De wissel knop bevindt zich boven de lijst in de portal.

De beveiligings besturings elementen-en deze wissel knop maken deel uit van de nieuwe beveiligde Score ervaring. Vergeet niet om ons uw feedback te sturen vanuit de portal.

Meer informatie over beveiligings controles in [Enhanced Secure Score (preview) in azure Security Center](secure-score-security-controls.md).

![Wissel knop "groeperen op besturings elementen" voor aanbevelingen](\media\secure-score-security-controls\recommendations-group-by-toggle.gif)

### <a name="expanded-security-control-implement-security-best-practices"></a>Uitgebreide beveiliging, aanbevolen procedures voor het implementeren van beveiliging 

Een beveiligings controle dat is geïntroduceerd in de verbeterde beveiligde Score is ' Implementeer beveiligings best practices '. Wanneer een aanbeveling zich in dit besturings element bevindt, heeft dit geen invloed op de beveiligde Score. 

Met deze update zijn drie aanbevelingen verplaatst van de besturings elementen waarin deze oorspronkelijk zijn geplaatst, en in dit best practices-besturings element. We hebben deze stap doorgevoerd omdat er is vastgesteld dat het risico van deze drie aanbevelingen lager is dan oorspronkelijk werd aangenomen.

Daarnaast zijn er twee nieuwe aanbevelingen geïntroduceerd en toegevoegd aan dit besturings element.

De drie aanbevelingen die worden verplaatst zijn:

- **MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement** (oorspronkelijk in het besturings element MFA inschakelen)
- **Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement** (oorspronkelijk in het besturings element toegang en machtigingen beheren)
- **Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement** (oorspronkelijk in het besturings element toegang en machtigingen beheren)

De twee nieuwe aanbevelingen die aan het besturings element worden toegevoegd, zijn:

- De **gast configuratie-extensie moet worden geïnstalleerd op virtuele Windows-machines (preview)** : het gebruik van [Azure Policy-gast configuratie](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) biedt zicht baarheid in virtuele machines naar server-en toepassings instellingen (alleen Windows).

- **Windows Defender exploit Guard moet zijn ingeschakeld op uw computers (preview)** : Windows Defender exploit Guard maakt gebruik van de Azure Policy-gast configuratie agent. Exploit Guard heeft vier onderdelen die zijn ontworpen voor het vergren delen van apparaten tegen een groot aantal aanvals vectoren en voor het blok keren van gedrag dat vaak wordt gebruikt voor aanvallen op schadelijke software, terwijl ondernemingen hun beveiligings Risico's en productiviteits vereisten kunnen afstemmen (alleen Windows).

Meer informatie over Windows Defender exploit Guard in [een exploit Guard-beleid maken en implementeren](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Meer informatie over beveiligings controles in [Enhanced Secure Score (preview)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Aangepaste beleids regels met aangepaste meta gegevens zijn nu algemeen beschikbaar

Aangepaste beleids regels maken nu deel uit van de Security Center aanbevelingen, beveiligde scores en het dash board nalevings normen voor regelgeving. Deze functie is nu algemeen beschikbaar en biedt u de mogelijkheid om de beveiligings beoordelings dekking van uw organisatie in Security Center uit te breiden. 

Maak een aangepaste initiatief in azure Policy, voeg er beleids regels aan toe en bewaart dit voor Azure Security Center, en visualiseer het als aanbevelingen.

We hebben nu ook de optie toegevoegd voor het bewerken van de aangepaste aanbevelings-meta gegevens. Meta gegevens opties zijn ernst, herstel stappen, informatie over bedreigingen en meer.  

Meer informatie over [het verbeteren van uw aangepaste aanbevelingen met gedetailleerde informatie](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Crash dump analyse mogelijkheden migreren naar aanval op bestandsloze detectie 

De detectie mogelijkheden van Windows Crash Dump Analysis (CDA) worden geïntegreerd in de [detectie van aanvalen bestanden](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless). Detectie van conflicten met een aanval op een bestand biedt verbeterde versies van de volgende beveiligings waarschuwingen voor Windows-computers: code-injectie gedetecteerd, er is een verwerkte shell code gedetecteerd, gedetecteerde en verdacht code segment gedetecteerd.

Enkele van de voor delen van deze overgang:

- **Proactieve en tijdige detectie van malware** : de CDA-benadering die is betrokken bij het wachten op een crash, en het uitvoeren van analyses om schadelijke artefacten te vinden. Bij het gebruik van een aanval zonder bestanden wordt er tijdens de uitvoering een proactieve identificatie van de bedreigingen in het geheugen gebruikt. 

- **Verrijkte waarschuwingen** : de beveiligings waarschuwingen van aanval via bestanden bevatten verrijkingen die niet beschikbaar zijn via CDA, zoals de actieve netwerk verbindings gegevens. 

- **Aggregatie van waarschuwingen** : wanneer CDA meerdere aanvals patronen in één crash dump heeft gedetecteerd, worden er meerdere beveiligings waarschuwingen geactiveerd. Bij het detecteren van een aanval met een bestand worden alle geïdentificeerde aanvals patronen uit hetzelfde proces gecombineerd tot één waarschuwing, waardoor de nood zaak om meerdere waarschuwingen te correleren, wordt verwijderd.

- **Gereduceerde vereisten op uw log Analytics werk ruimte** : crash dumps die mogelijk gevoelige gegevens bevatten, worden niet meer geüpload naar uw log Analytics-werk ruimte.



## <a name="april-2020"></a>April 2020

De updates in april zijn onder andere:
- [Dynamische nalevings pakketten zijn nu algemeen beschikbaar](#dynamic-compliance-packages-are-now-generally-available)
- [Aanbevelingen voor de identiteit nu opgenomen in Azure Security Center gratis laag](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamische nalevings pakketten zijn nu algemeen beschikbaar

Het dash board voor nalevings vereisten van Azure Security Center bevat nu **dynamische nalevings pakketten** (nu algemeen beschikbaar) voor het bijhouden van aanvullende industrie-en regelgevings normen.

Dynamische nalevings pakketten kunnen worden toegevoegd aan uw abonnement of beheer groep op de pagina Security Center beveiligings beleid. Wanneer u een Standard-of Bench Mark hebt voor bereid, wordt de standaard weer gegeven in het dash board nalevings beleid met alle gekoppelde nalevings gegevens die zijn toegewezen als evaluaties. Een samenvattings rapport voor een van de beschik bare standaarden kan worden gedownload.

U kunt nu standaarden toevoegen zoals:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK-officiële en UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nieuw)** (dit is een meer volledige weer gave van Azure CIS 1.1.0)

Daarnaast hebben we onlangs de **Azure Security-Bench Mark**toegevoegd, de door micro soft geschreven specifieke Azure-richt lijnen voor de aanbevolen procedures voor beveiliging en naleving op basis van algemene nalevings kaders. Er worden extra standaarden ondersteund in het dash board, zodra deze beschikbaar komen.  
 
Meer informatie over [het aanpassen van de set normen in uw nalevings dashboard](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Aanbevelingen voor de identiteit nu opgenomen in Azure Security Center gratis laag

Beveiligings aanbevelingen voor identiteit en toegang op de laag Azure Security Center gratis zijn nu algemeen beschikbaar. Dit maakt deel uit van de inspanningen om de CSPM-functies (Cloud Security postuur Management) gratis te maken. Tot nu toe zijn deze aanbevelingen alleen beschikbaar in de prijs categorie Standard.

Voor beelden van aanbevelingen voor identiteits-en toegangs rechten zijn:

- "Multi-factor Authentication moet worden ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement."
- "Maxi maal drie eigen aren moeten worden opgegeven voor uw abonnement."
- "Afgeschafte accounts moeten worden verwijderd uit uw abonnement."

Als u abonnementen hebt op de prijs categorie gratis, worden hun beveiligde scores beïnvloed door deze wijziging omdat ze nooit werden beoordeeld voor hun identiteits-en toegangs beveiliging.

Meer informatie over [aanbevelingen voor identiteit en toegang](recommendations-reference.md#recs-identity).

Meer informatie over het [bewaken van identiteit en toegang](security-center-identity-access.md).