---
title: Teamanalytics en AI-omgeving
titleSuffix: Azure Data Science Virtual Machine
description: Patronen voor het implementeren van de Data Science VM in een enterprise team omgeving.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science proces
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 1f3a24afb2238c2448cb19b3889467a1b1819724
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460542"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Data Science Virtual Machine-gebaseerde teamanalytics en AI-omgeving 
De [Data Science Virtual Machine](overview.md) (DSVM) biedt een rijke omgeving op het Azure-platform, met vooraf gebouwde software voor kunstmatige intelligentie (AI) en data-analyse.

Traditioneel wordt de DSVM gebruikt als een individuele analytics desktop. Individuele data scientists winnen productiviteit met deze gedeelde, vooraf gebouwde analyseomgeving. Terwijl grote analyseteams omgevingen plannen voor hun data scientists en AI-ontwikkelaars, is een van de terugkerende thema's een gedeelde analyse-infrastructuur voor ontwikkeling en experimenten. Deze infrastructuur wordt beheerd in overeenstemming met it-beleid van bedrijven, wat ook samenwerking en consistentie tussen de data science- en analyseteams vergemakkelijkt.

Een gedeelde infrastructuur maakt een beter IT-gebruik van de analyseomgeving mogelijk. Sommige organisaties noemen de teamgebaseerde data science/analytics-infrastructuur een *analytics sandbox.* Het stelt gegevenswetenschappers in staat om toegang te krijgen tot verschillende gegevenselementen om snel gegevens te begrijpen. Deze sandbox-omgeving helpt gegevenswetenschappers ook experimenten uit te voeren, hypothesen te valideren en voorspellende modellen te bouwen zonder de productieomgeving te beïnvloeden.

Omdat de DSVM op Azure-infrastructuurniveau werkt, kunnen IT-beheerders de DSVM eenvoudig configureren om te werken in overeenstemming met het IT-beleid van de onderneming. De DSVM biedt volledige flexibiliteit bij het implementeren van verschillende deelarchitecturen en biedt tegelijkertijd op een gecontroleerde manier toegang tot bedrijfsdataassets.

In dit gedeelte worden enkele patronen en richtlijnen besproken die u gebruiken om de DSVM te implementeren als een teamgebaseerde data science-infrastructuur. Omdat de bouwstenen voor deze patronen afkomstig zijn van Azure infrastructure as a service (IaaS), zijn ze van toepassing op azure VM's. Deze serie artikelen richt zich op het toepassen van deze standaard Azure-infrastructuurmogelijkheden op de DSVM.

Belangrijke bouwstenen van een enterprise team analytics-omgeving zijn:

* [Een automatisch geschaalde groep DSVMs](dsvm-pools.md)
* [Gemeenschappelijke identiteit en toegang tot een werkruimte vanuit een van de DSVMs in de pool](dsvm-common-identity.md)
* [Beveiligde toegang tot gegevensbronnen](dsvm-secure-access-keys.md)


Deze serie biedt richtlijnen en aanwijzingen voor elk van de voorgaande onderwerpen. Het dekt niet alle overwegingen en vereisten voor het implementeren van DSVM's in grote bedrijfsconfiguraties. Hier volgen enkele andere Azure-bronnen die u gebruiken tijdens het implementeren van DSVM-exemplaren in uw onderneming:

* [Netwerkbeveiliging](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Toezicht](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) en [beheer](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Logboekregistratie en bewaking](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Op rollen gebaseerd toegangsbeheer](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Beleidsinstelling en handhaving](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Versleuteling](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview.md)
* [Gegevensdetectie en -governance](https://docs.microsoft.com/azure/data-catalog/)

Tot slot biedt het [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) een gedetailleerde end-to-end architectuur en modellen voor het bouwen en beheren van uw cloudgebaseerde analyse-infrastructuur.