---
title: Een gebruiker op aanvraag inrichten met Azure Active Directory
description: Synchronisatie afdwingen
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 78a56b6a848139c47d7934a47decb126afe00b7a
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85297525"
---
# <a name="on-demand-provisioning"></a>Inrichting op aanvraag
Met inrichten op aanvraag kunt u binnen enkele seconden een gebruiker inrichten in een toepassing. U kunt de mogelijkheid gebruiken om snel configuratie problemen op te lossen, expressies te valideren die u hebt gedefinieerd, filters voor bereik te testen en nog veel meer. 

## <a name="how-to-use-on-demand-provisioning"></a>Instructies voor het gebruik van on-demand inrichten 

1. Meld u aan bij de **Azure Portal**.
2. Navigeer naar **bedrijfs toepassingen**.
3. Selecteer uw toepassing en navigeer naar de pagina inrichtings configuratie.
4. Configureer inrichting door uw beheerders referenties op te geven.
5. Klik op **aanvraag inrichten**.
6. Zoek naar een gebruiker op voor naam, achternaam, weergave naam, user principal name of e-mail adres.
7. Selecteer aan de onderkant van de pagina inrichten.

## <a name="understanding-the-provisioning-steps"></a>Informatie over de inrichtings stappen
De functie voor inrichting op aanvraag probeert de stappen weer te geven die de inrichtings service onderneemt bij het inrichten van een gebruiker. Er zijn meestal vijf stappen voor het inrichten van een gebruiker en een of meer van de onderstaande stappen worden weer gegeven in de ervaring op aanvraag inrichten.

### <a name="step-1-test-connection"></a>Stap 1: de verbinding testen
De inrichtings service probeert toegang te verlenen tot de doel toepassing door een aanvraag voor een test gebruiker te maken. De inrichtings service verwacht een antwoord dat aangeeft dat het toestemming heeft om door te gaan met de inrichtings stappen. Deze stap wordt alleen weer gegeven als er een fout is opgetreden in de stap. Het wordt niet weer gegeven in de inrichtings ervaring op aanvraag wanneer de stap is geslaagd. 

**Tips voor probleemoplossing**
* Zorg ervoor dat u geldige referenties hebt ingevoerd voor de doel toepassing, zoals het geheim token en de Tenant-URL. De vereiste referenties variëren per toepassing. Gedetailleerde zelf studies voor configuratie vindt u [hier](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 
* Zorg ervoor dat de doel toepassing filteren ondersteunt op de overeenkomende kenmerk (en) die zijn gedefinieerd op de Blade kenmerk toewijzingen. Mogelijk moet u de API-documentatie van de ontwikkelaar van de toepassing controleren om inzicht te krijgen in de filters die ze ondersteunen.  
* Voor SCIM-toepassingen kunt u een hulp programma zoals postman gebruiken om ervoor te zorgen dat de toepassing reageert op de autorisatie aanvragen wanneer de Azure AD-inrichtings service verwacht. [Hier](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3)vindt u een voor beeld van een aanvraag.

### <a name="step-2-import-user"></a>Stap 2: gebruiker importeren
Vervolgens haalt de inrichtings service de gebruiker op uit het bron systeem. De gebruikers kenmerken die door de service worden opgehaald, worden later gebruikt om te evalueren of de gebruiker binnen het bereik is van het inrichten, het doel systeem controleren op een bestaande gebruiker en om te bepalen welke gebruikers kenmerken naar het doel systeem moeten worden geëxporteerd. 

**Details weergeven**

In het gedeelte weergave Details worden de eigenschappen van de gebruiker weer gegeven die zijn geïmporteerd uit het bron systeem (bijvoorbeeld Azure AD).

**Tips voor probleemoplossing**
* Het importeren van de gebruiker kan mislukken als het overeenkomende kenmerk ontbreekt voor het gebruikers object in het bron systeem. U kunt deze fout oplossen door het gebruikers object bij te werken met een waarde voor het overeenkomende kenmerk of door het overeenkomende kenmerk in uw inrichtings configuratie te wijzigen.  
* Als een kenmerk dat u verwacht, ontbreekt in de lijst die is geïmporteerd, moet u ervoor zorgen dat het kenmerk een waarde heeft voor het gebruikers object in het bron systeem. De inrichtings service biedt momenteel geen ondersteuning voor het inrichten van Null-kenmerken. 
* Zorg ervoor dat de toewijzings pagina van uw inrichtings configuratie kenmerk het kenmerk bevat dat u verwacht. 

### <a name="step-3-determine-if-user-is-in-scope"></a>Stap 3: bepalen of de gebruiker binnen het bereik ligt
Vervolgens bepaalt de inrichtings service of de gebruiker binnen het [bereik](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping) van de inrichting valt. De service houdt rekening met aspecten zoals of de gebruiker aan de toepassing is toegewezen, of het bereik is ingesteld op synchroniseren of alles synchroniseren, en de bereik filters die zijn gedefinieerd in uw inrichtings configuratie.  

**Details weergeven**

De sectie Details weer geven toont de scope voorwaarden die zijn geëvalueerd. U ziet mogelijk een van de volgende eigenschappen:
* **Actief in bron systeem** geeft aan dat de eigenschap actief is ingesteld op True in azure AD.
* **Toegewezen aan toepassing** geeft aan dat de gebruiker is toegewezen aan de toepassing in azure AD
* **Scope Sync All** geeft aan dat de bereik instelling alle gebruikers en groepen in de Tenant toestaat.
* **Gebruiker heeft vereiste rol** geeft aan dat de gebruiker de benodigde rollen heeft die moeten worden ingericht in de toepassing. 
* **Bereik filters** worden ook weer gegeven als u bereik filters hebt gedefinieerd voor uw toepassing. Het filter wordt weer gegeven met de volgende indeling: {bereik filter titel} {filter kenmerk bereik} {bereik filter operator} {waarde filter bereik}. 

**Tips voor probleemoplossing**
* Zorg ervoor dat u een geldige bereik functie hebt gedefinieerd. Vermijd het gebruik van de operator [' groter dan '](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter) met een niet-geheel getal. 
* Als de gebruiker niet de vereiste rol heeft, raadpleegt u de [hier](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role)beschreven tips. 

### <a name="step-4-match-user-between-source-and-target"></a>Stap 4: identieke gebruikers tussen de bron en het doel
In deze stap. de service probeert te voldoen aan de gebruiker die in de import stap is opgehaald met een gebruiker in het doel systeem. 

**Details weergeven**

Op de pagina's details weer geven ziet u de eigenschappen van de gebruiker (s) die overeenkomen met het doel systeem. De eigenschappen die u in het context deel venster ziet, verschillen als volgt:
* Als er geen gebruikers overeenkomen in het doel systeem, worden er geen eigenschappen weer geven.
* Als er sprake is van een gebruiker die overeenkomt met het doel systeem, worden de eigenschappen van die overeenkomende gebruiker van het doel systeem weer geven.
* Als er meerdere gebruikers overeenkomen, worden de eigenschappen van beide overeenkomende gebruikers weer geven.
* Als meerdere overeenkomende kenmerken deel uitmaken van uw kenmerk toewijzingen, wordt elk kenmerk dat overeenkomt sequentief geëvalueerd en worden de overeenkomende gebruikers weer gegeven. 

**Details voor probleem oplossing**
* De inrichtings service kan geen unieke id hebben die overeenkomt met een gebruiker in de bron met een gebruiker in het doel. Dit kan worden opgelost door ervoor te zorgen dat het overeenkomende kenmerk uniek is. 
* Zorg ervoor dat de doel toepassing filteren ondersteunt op het kenmerk dat is gedefinieerd als het overeenkomende kenmerk.  

### <a name="step-5-perform-action"></a>Stap 5: actie uitvoeren
Ten slotte neemt de inrichtings service een actie, zoals het maken, bijwerken, verwijderen of overs laan van de gebruiker. 

**Details weergeven**

In het gedeelte weer gave Details worden de kenmerken weer gegeven die zijn gewijzigd in de doel toepassing. Dit vertegenwoordigt de uiteindelijke uitvoer van de inrichtings service activiteit en de kenmerken die zijn geëxporteerd. Als deze stap mislukt, vertegenwoordigen de weer gegeven kenmerken de kenmerken die de inrichtings service heeft geprobeerd te wijzigen.  

**Tips voor probleemoplossing**
* De fouten voor het exporteren van wijzigingen kunnen aanzienlijk verschillen. Bekijk de [documentatie](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes) voor inrichtings logboeken voor veelvoorkomende fouten.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen
**Moet u het inrichten uitschakelen voor het gebruik van on-demand inrichting?** Voor toepassingen die een Long-Bearer-token of-gebruikers naam en-wacht woord gebruiken voor autorisatie, zijn geen extra stappen vereist. Voor toepassingen waarvoor OAuth voor autorisatie wordt gebruikt, moet de inrichtings taak worden gestopt voordat het inrichten op aanvraag wordt gebruikt. Toepassingen zoals G suite, box, werk plek op Facebook en toegestane vertraging vallen in deze categorie. Het werk wordt uitgevoerd om het inrichten op aanvraag uit te voeren voor alle toepassingen, zonder dat het inrichten hoeft te worden gestopt. 

**Hoe lang duurt het inrichten op aanvraag?** Het duurt over het algemeen minder dan 30 seconden. 

## <a name="known-limitations"></a>Bekende beperkingen
Er zijn momenteel enkele bekende beperkingen. Post op [UserVoice](https://aka.ms/appprovisioningfeaturerequest) zodat we beter kunnen bepalen welke verbeteringen er nu moeten worden gemaakt. Houd er rekening mee dat deze beperkingen specifiek zijn voor de inrichtings mogelijkheid op aanvraag. Raadpleeg de zelf studie over toepassingen voor specifieke informatie over of een toepassing inrichtings groepen, verwijderingen, enzovoort ondersteunt. 

* De toepassingen workday, AWS en SuccessFactors bieden geen ondersteuning voor inrichting op aanvraag.
* Inrichtings groepen en rollen op aanvraag worden niet ondersteund.
* Het uitschakelen of verwijderen van gebruikers en groepen wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* [Probleem oplossing voor het inrichten](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
