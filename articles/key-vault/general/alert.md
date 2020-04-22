---
title: Azure Key Vault-bewaking en -waarschuwingen | Microsoft Documenten
description: Maak een dashboard om de status van uw sleutelkluis te controleren en waarschuwingen te configureren.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726941"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Bewaken en waarschuwen voor Azure Key Vault

## <a name="overview"></a>Overzicht

Zodra u bent begonnen met het gebruik van sleutelkluis om uw productiegeheimen op te slaan, is het belangrijk om de status van uw sleutelkluis te controleren om ervoor te zorgen dat uw service werkt zoals bedoeld. Als u begint met het schalen van uw service zal het aantal aanvragen naar uw sleutelkluis stijgen. Dit heeft een potentieel om de latentie van uw verzoeken te verhogen en in extreme gevallen, waardoor uw verzoeken worden beperkt, wat van invloed is op de prestaties van uw service. U moet ook worden gewaarschuwd als uw sleutelkluis een ongebruikelijk aantal foutcodes verzendt, zodat u snel op de hoogte worden gesteld van eventuele problemen met het toegangsbeleid of de configuratie van de firewall. Dit document behandelt de volgende onderwerpen:

+ Basisstatistieken van Key Vault om te controleren
+ Statistieken configureren en een dashboard maken 
+ Waarschuwingen maken op opgegeven drempelwaarden 

## <a name="basic-key-vault-metrics-to-monitor"></a>Basisstatistieken van Key Vault om te controleren

+ Beschikbaarheid van kluizen  
+ Gewaarverzadiging 
+ Latentie van service-API 
+ Total Service API-hits (filter op activiteitstype) 
+ Foutcodes (filter op statuscode) 

**Beschikbaarheid van kluizen** - Deze statistiek moet altijd op 100% dit is een belangrijke statistiek te controleren, omdat het snel kan laten zien of uw sleutel kluis ervaren een storing. 

**Vault Saturation** – Het aantal aanvragen per seconde dat een sleutelkluis kan serveren is gebaseerd op het type bewerking dat wordt uitgevoerd. Sommige kluisbewerkingen hebben een lagere drempelwaarde voor aanvragen per seconde. Met deze statistiek wordt het totale gebruik van uw sleutelkluis samengevoegd voor alle bewerkingstypen om een percentagewaarde te krijgen die uw huidige sleutelkluisgebruik aangeeft. Zie het volgende document voor een volledige lijst met limieten voor belangrijke vaultservice. [Limieten van Azure Key Vault-service](service-limits.md)

**Latentie van service-API** - Deze statistiek toont de gemiddelde latentie van een call-to-key vault. Hoewel uw sleutelkluis zich binnen de servicelimieten bevindt, kan een hoog gebruik van key vault latentie introduceren waardoor toepassingen downstream mislukken. 

**Total API Hits** - Deze statistiek toont alle oproepen naar uw sleutelkluis. Zo u bepalen welke toepassingen uw sleutelkluis bellen. 

**Foutcodes** : deze statistiek toont u of uw sleutelkluis een ongebruikelijke hoeveelheid fouten ondervindt. Zie het volgende document voor een volledige lijst met foutcodes en richtlijnen voor het oplossen van problemen. [Foutcodes azure Key Vault REST API](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Statistieken configureren en een dashboard maken

1. Meld u aan bij de Azure-portal
2. Navigeer naar uw sleutelkluis
3. **Statistieken selecteren** onder **Controleren** 

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-1.png)

4. Werk de titel van de grafiek bij naar wat u op uw dashboard wilt zien. 
5. Selecteer het bereik. In dit voorbeeld selecteren we één sleutelkluis. 
6. Selecteer de algemene beschikbaarheid en aggregatie **avg** voor metrische **kluizen** 
7. Werk het tijdsbereik bij naar de laatste 24 uur en werk de tijdgranulariteit bij tot 1 minuut. 

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-2.png)

8. Herhaal de bovenstaande stappen voor de latentiestatistieken Vault Saturation and Service API. Selecteer **Vastmaken aan dashboard** om uw statistieken op te slaan in een dashboard. 

> [!IMPORTANT]
> Selecteer 'Vastmaken aan dashboard' en sla elke statistiek op die u configureert. Als u de pagina verlaat en deze zonder op te slaan weer opslaat, gaan uw configuratiewijzigingen verloren. 

9. Als u alle typen bewerkingen op de sleutelkluis wilt controleren, gebruikt u de statistiek **Total Service API Hits** en Selecteert u Splitsen toepassen op **activiteitstype**

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-3.png)

10. Als u wilt controleren op foutcodes in de sleutelkluis, gebruikt u de statistiek **Total Service API-resultaten** en Selecteert u **Splitsen toepassen op activiteitstype**

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-4.png)

Nu heb je een dashboard dat er zo uitziet. U klikken op de 3 stippen rechtsboven van elke tegel en u de tegels opnieuw rangschikken en het formaat ervan wijzigen als u dat nodig hebt. 

Zodra u het dashboard opslaat en publiceert, wordt er een nieuwe bron in uw Azure-abonnement gemaakt. U het op elk gewenst moment zien door te zoeken naar gedeeld dashboard. 

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Waarschuwingen configureren op uw Key Vault 

In dit gedeelte ziet u hoe u waarschuwingen op uw sleutelkluis configureert, zodat u uw team waarschuwen om onmiddellijk actie te ondernemen als uw sleutelkluis in een ongezonde staat verkeert. U waarschuwingen configureren die een e-mail verzenden, bij voorkeur naar een team-DL, een melding van het gebeurtenisraster afvuren of een telefoonnummer bellen of sms'en. U ook statische waarschuwingen kiezen op basis van een vaste waarde of een dynamische waarschuwing die u waarschuwt als een bewaakte statistiek de gemiddelde limiet van uw sleutelkluis een bepaald aantal keren binnen een bepaald tijdsbereik overschrijdt. 

> [!IMPORTANT]
> Houd er rekening mee dat het tot 10 minuten kan duren voordat nieuw geconfigureerde waarschuwingen worden verzonden. 

### <a name="configure-an-action-group"></a>Een actiegroep configureren 

Een actiegroep is een configureerbare lijst met meldingen en eigenschappen.

1. Meld u aan bij de Azure-portal
2. Zoeken naar **waarschuwingen** in het zoekvak
3. Acties **beheren selecteren**

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-6.png)

4. Selecteren **+ Actiegroep toevoegen**

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-7.png)

5. Kies het **actietype** voor uw actiegroep. In dit voorbeeld maken we een e-mailwaarschuwing.

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-9.png)

6. Klik onder aan de pagina op **OK**. U hebt een actiegroep gemaakt. 

Nu u een actiegroep hebt geconfigureerd, configureren we de drempelwaarden voor de waarschuwingen voor de sleutelkluis. 

### <a name="configure-alert-thresholds"></a>Waarschuwingsdrempels configureren 

1. Selecteer uw sleutelkluisbron in de Azure-portal en selecteer **Waarschuwingen** onder **Bewaken**

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-10.png)

2. **Nieuwe waarschuwingsregel selecteren**

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-11.png)

3. Selecteer het bereik van uw waarschuwingsregel. U één kluis of meerdere selecteren. 

> [!IMPORTANT]
> Houd er rekening mee dat wanneer u meerdere kluizen selecteert voor het bereik van uw waarschuwingen, alle geselecteerde kluizen zich in dezelfde regio moeten bevinden. U moet afzonderlijke waarschuwingsregels configureren voor kluizen in verschillende regio's. 

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-12.png)

4. Selecteer de voorwaarden voor uw waarschuwingen. U een van de volgende signalen kiezen en uw logica definiëren om te waarschuwen. Het Key Vault-team raadt aan de volgende waarschuwingsdrempels te configureren. 

    + Beschikbaarheid van key vault daalt tot onder de 100% (statische drempelwaarde)
    + Key Vault Latency is groter dan 500 ms (Statische drempel) 
    + Totale vaultverzadiging is groter dan 75% (statische drempelwaarde) 
    + Totale vaultverzadiging overschrijdt het gemiddelde (dynamische drempelwaarde)
    + Totaal foutcodes hoger dan gemiddeld (dynamische drempelwaarde) 

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Voorbeeld 1: Een statische waarschuwingsdrempel voor latentie configureren

Algemene **service-API-latentie selecteren** als de signaalnaam
> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-14.png)

Zie de volgende configuratieparameters.

+ De drempelwaarde instellen op **Statisch** 
+ De operator instellen op **groter dan**
+ Het aggregatietype instellen op **Gemiddeld**
+ De drempelwaarde instellen op **500**
+ Aggregatieperiode instellen op **5 minuten**
+ Stel de evaluatiefrequentie in op **1 minuut**
+ Gereed **doen selecteren**  

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Voorbeeld 2: Een dynamische waarschuwingsdrempel configureren voor gevolraken 

Wanneer u een dynamische waarschuwing gebruikt, u historische gegevens zien van de sleutelkluis die u hebt geselecteerd. Het blauwe gebied vertegenwoordigt het gemiddelde gebruik van uw sleutelkluis. Het rode gebied toont pieken die een waarschuwing zouden hebben geactiveerd, mits aan andere criteria in de waarschuwingsconfiguratie is voldaan. De rode stippen geven gevallen van schendingen weer waarbij tijdens het geaggregeerde tijdvenster is voldaan aan de criteria voor de waarschuwing. U een waarschuwing instellen om te vuren na een bepaald aantal overtredingen binnen een bepaalde tijd. Als u gegevens uit het verleden niet wilt opnemen, is er een optie om oude gegevens hieronder in geavanceerde instellingen uit te sluiten. 

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-16.png)

Zie de volgende configuratieparameters.

+ De drempel instellen op **Dynamisch** 
+ De operator instellen op **groter dan**
+ Het aggregatietype instellen op **Gemiddeld**
+ De drempelgevoeligheid instellen op **Gemiddeld**
+ Aggregatieperiode instellen op **5 minuten**
+ Stel de evaluatiefrequentie in op **1 minuut**
+ **Optioneel** Geavanceerde instellingen configureren 
+ Gereed **doen selecteren**

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-17.png)

5. De groep toevoegen die u hebt geconfigureerd

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-18.png)

6. De waarschuwing inschakelen en een ernst toewijzen

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-19.png)

7. De waarschuwing maken 


## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt nu met succes een monitoring dashboard en geconfigureerde waarschuwingen voor uw sleutel kluis! Zodra u alle bovenstaande stappen hebt gevolgd, ontvangt u e-mailwaarschuwingen wanneer uw sleutelkluis voldoet aan de waarschuwingscriteria die u hebt geconfigureerd. Hieronder kunt u een voorbeeld bekijken. Gebruik de tools die u in dit artikel hebt ingesteld om de status van uw sleutelkluis actief te controleren. 

### <a name="example-email-alert"></a>Voorbeeld e-mailwaarschuwing 

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal](../media/alert-20.png)
