---
title: Bedrijfsbeleid en naleving in Azure DevTest Labs
description: In dit artikel vindt u richtlijnen voor het bedrijfsbeleid en de naleving van de Azure DevTest Labs-infrastructuur.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 81ca2a90b1940d70e170cab3f8d18144a5d5e5a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74560506"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Governance van Azure DevTest Labs-infrastructuur - Bedrijfsbeleid en compliance
In dit artikel vindt u richtlijnen voor het bedrijfsbeleid en de naleving van de Azure DevTest Labs-infrastructuur. 

## <a name="public-vs-private-artifact-repository"></a>Openbare versus private artefact repository

### <a name="question"></a>Vraag
Wanneer moet een organisatie een openbare artefactrepository gebruiken versus private artefact-opslagplaats en in DevTest Labs?

### <a name="answer"></a>Antwoord
De [openbare artefact repository](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) biedt een eerste set van software pakketten die het meest worden gebruikt. Het helpt bij een snelle implementatie zonder tijd te hoeven investeren om gangbare ontwikkeltools en add-ins te reproduceren. U ervoor kiezen om hun eigen private repository te implementeren. U een openbare en een privéopslagplaats parallel gebruiken. U er ook voor kiezen om de openbare opslagplaats uit te schakelen. De criteria voor het implementeren van een privé-opslagplaats moeten worden bepaald door de volgende vragen en overwegingen:

- Heeft de organisatie een vereiste om corporate gelicentieerde software als onderdeel van hun DevTest Labs aanbod hebben? Als het antwoord ja is, moet er een privé-opslagplaats worden gemaakt.
- Ontwikkelt de organisatie aangepaste software die een specifieke bewerking biedt, die nodig is als onderdeel van het algehele inrichtingsproces? Als het antwoord ja is, moet een privé-opslagplaats worden geïmplementeerd.
- Als het governancebeleid van de organisatie isolatie vereist en externe repositories niet onder direct configuratiebeheer door de organisatie staan, moet een private artefact-opslagplaats worden geïmplementeerd. Als onderdeel van dit proces kan een eerste kopie van de openbare repository worden gekopieerd en geïntegreerd met de private repository. Vervolgens kan de openbare repository worden uitgeschakeld, zodat niemand binnen de organisatie er meer toegang toe heeft. Deze aanpak dwingt alle gebruikers binnen de organisatie om slechts één repository te hebben die is goedgekeurd door de organisatie en configuratiedrift te minimaliseren.

### <a name="single-repository-or-multiple-repositories"></a>Eén repository of meerdere repositories 

### <a name="question"></a>Vraag
Moet een organisatie plannen voor een enkele repository of meerdere repositories toestaan?

### <a name="answer"></a>Antwoord
Als onderdeel van de algemene governance- en configuratiebeheerstrategie van uw organisatie raden we u aan een gecentraliseerde repository te gebruiken. Wanneer u meerdere repositories gebruikt, kunnen ze in de loop van de tijd silo's van onbeheerde software worden. Met een centrale repository kunnen meerdere teams artefacten uit deze repository gebruiken voor hun projecten. Het dwingt standaardisatie, beveiliging, gemak van beheer, en elimineert de verdubbeling van de inspanningen. Als onderdeel van de centralisatie worden de volgende acties aanbevolen praktijken voor langetermijnbeheer en duurzaamheid:

- Koppel de Azure Repos aan dezelfde Azure Active Directory-tenant die het Azure-abonnement gebruikt voor verificatie en autorisatie.
- Maak een groep met de naam **All DevTest Labs Developers** in Azure Active Directory die centraal wordt beheerd. Elke ontwikkelaar die bijdraagt aan de ontwikkeling van artefacten moet in deze groep worden geplaatst.
- Dezelfde Azure Active Directory-groep kan worden gebruikt om toegang te bieden tot de Azure Repos-opslagplaats en tot het lab.
- In Azure Repos moet vertakking of forking worden gebruikt om een in-development repository te scheiden van de primaire productieopslagplaats. Inhoud wordt alleen toegevoegd aan de master branch met een pull-aanvraag na een goede codecontrole. Zodra de coderevisor de wijziging goedkeurt, voegt een leadontwikkelaar, die verantwoordelijk is voor het onderhoud van de hoofdbranch, de bijgewerkte code samen. 

## <a name="corporate-security-policies"></a>Bedrijfsbeveiligingsbeleid

### <a name="question"></a>Vraag
Hoe kan een organisatie ervoor zorgen dat er bedrijfsbeveiligingsbeleid is ingevoerd?

### <a name="answer"></a>Antwoord
Een organisatie kan dit bereiken door de volgende acties uit te voeren:

1. Ontwikkelen en publiceren van een uitgebreid beveiligingsbeleid. Het beleid verwoordt de regels van aanvaardbaar gebruik in verband met het gebruik van software, cloud-assets. Het bepaalt ook wat duidelijk in strijd is met het beleid. 
2. Ontwikkel een aangepaste afbeelding, aangepaste artefacten en een implementatieproces dat orkestratie mogelijk maakt binnen de beveiligingsrealm die is gedefinieerd met active directory. Deze aanpak dwingt de bedrijfsgrens af en stelt een gemeenschappelijke set milieucontroles vast. Deze besturingselementen tegen de omgeving die een ontwikkelaar kan overwegen bij het ontwikkelen en volgen van een veilige ontwikkelingslevenscyclus als onderdeel van hun algehele proces. Het doel is ook om een omgeving te bieden die niet al te restrictief is en die de ontwikkeling kan belemmeren, maar een redelijke reeks controles. Het groepsbeleid op de organisatie-eenheid (OU) die virtuele laboratoriummachines bevat, kan een subset zijn van het totale groepsbeleid dat in de productie wordt gevonden. Als alternatief kunnen ze een extra set zijn om geïdentificeerde risico's goed te beperken.

## <a name="data-integrity"></a>Gegevensintegriteit

### <a name="question"></a>Vraag
Hoe kan een organisatie de integriteit van gegevens waarborgen om ervoor te zorgen dat remoting-ontwikkelaars geen code kunnen verwijderen of malware of niet-goedgekeurde software kunnen introduceren?

### <a name="answer"></a>Antwoord
Er zijn verschillende controlelagen om de dreiging van externe consultants, aannemers of werknemers die samenwerken in DevTest Labs te beperken. 

Zoals eerder vermeld, moet de eerste stap een aanvaardbaar gebruiksbeleid hebben opgesteld en gedefinieerd dat duidelijk de gevolgen schetst wanneer iemand het beleid schendt. 

De eerste laag van besturingselementen voor externe toegang is het toepassen van een beleid voor externe toegang via een VPN-verbinding die niet rechtstreeks is verbonden met het lab. 

De tweede besturingselementenlaag is het toepassen van een set groepsbeleidsobjecten die kopiëren en plakken via extern bureaublad voorkomen. Er kan een netwerkbeleid worden geïmplementeerd om uitgaande services uit de omgeving, zoals FTP- en RDP-services, niet uit de omgeving toe te staan. Door de gebruiker gedefinieerde routering kan al het Azure-netwerkverkeer weer vrijmaken, maar de routering kan geen rekening houden met alle URL's waarmee het uploaden van gegevens mogelijk is, tenzij deze via een proxy wordt beheerd om inhoud en sessies te scannen. Openbare IP's kunnen worden beperkt binnen het virtuele netwerk ter ondersteuning van DevTest Labs om het overbruggen van een externe netwerkbron niet toe te staan.

Uiteindelijk moeten dezelfde soorten beperkingen worden toegepast in de hele organisatie, die ook rekening moeten houden met alle mogelijke methoden van verwijderbare media of externe URL's die een bericht van inhoud kunnen accepteren. Overleg met uw beveiligingsprofessional om een beveiligingsbeleid te controleren en uit te voeren. Zie [Microsoft Cyber Security voor](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)meer aanbevelingen.


## <a name="next-steps"></a>Volgende stappen
Zie [Toepassingsmigratie en -integratie](devtest-lab-guidance-governance-application-migration-integration.md).
