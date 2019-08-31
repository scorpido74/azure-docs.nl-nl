---
title: Team analyse en AI-omgeving
titleSuffix: Azure Data Science Virtual Machine
description: Patronen voor het implementeren van de Data Science VM in een bedrijfsomgeving team.
keywords: deep learning, AI, hulpprogramma's voor data science, virtuele machine voor datatechnologie, georuimtelijke analyses, team data science process
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 06d05d6d410af13bfbe85f3cb66523c1d48cb77c
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195665"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Op basis van een virtuele Machine voor Datatechnologie team gegevensanalyse en AI-omgeving 
De [Data Science virtual machine](overview.md) (DSVM) biedt een uitgebreide omgeving op het Azure-platform, met vooraf ontwikkelde software voor kunst matige intelligentie (AI) en gegevens analyse.

De DSVM is traditioneel gebruikt als een afzonderlijke analysebureaublad. Individuele gegevens wetenschappers winnen productiviteit met deze gedeelde, vooraf ontwikkelde analyse omgeving. Als grote analyse teams plannen omgevingen voor hun gegevens wetenschappers en AI-ontwikkel aars, is een van de terugkerende Thema's een gedeelde analyse-infra structuur voor ontwikkeling en experimenten. Deze infra structuur wordt beheerd in overeenstemming met de IT-beleids regels van ondernemingen, waarmee de samen werking en consistentie van de gegevens wetenschap en analyse teams worden vereenvoudigd.

Een gedeelde infra structuur maakt het gebruik van de analyse omgeving beter. Sommige organisaties bellen de op het team gebaseerde data Science/Analytics-infra structuur van een *analytische sandbox*. Hiermee kunnen gegevens wetenschappers toegang krijgen tot verschillende gegevensassets om gegevens snel te begrijpen. Deze sandbox-omgeving helpt gegevens wetenschappers ook bij het uitvoeren van experimenten, het valideren van hypo thesen en het bouwen van voorspellende modellen zonder dat dit van invloed is op de productie omgeving.

Omdat de DSVM op het niveau van de Azure-infrastructuur werkt, kunnen IT-beheerders gemakkelijk de DSVM functioneren in overeenstemming met de IT-beleid van de onderneming configureren. De DSVM biedt volledige flexibiliteit bij het implementeren van verschillende architecturen voor delen, terwijl ook toegang tot zakelijke gegevensassets op een gecontroleerde manier wordt geboden.

Deze sectie wordt besproken sommige patronen en richtlijnen die u gebruiken kunt voor het implementeren van de DSVM als een op basis van team data science-infrastructuur. Omdat de bouw stenen voor deze patronen afkomstig zijn van Azure Infrastructure as a Service (IaaS), zijn deze van toepassing op alle virtuele machines van Azure. Deze reeks artikelen richt zich op het Toep assen van deze standaard Azure-infrastructuur mogelijkheden op het DSVM.

De belangrijkste bouw stenen van een Enter prise team Analytics-omgeving zijn:

* [Een automatisch geschaalde pool met Dsvm](dsvm-pools.md)
* [Algemene identiteit en toegang tot een werkruimte uit een van de Dsvm in de groep](dsvm-common-identity.md)
* [Beveiligde toegang tot gegevensbronnen](dsvm-secure-access-keys.md)


Deze reeks bevat richt lijnen en verwijzingen voor elk van de voor gaande onderwerpen. Dit geldt niet voor alle overwegingen en vereisten voor het implementeren van Dsvm in grote bedrijfs configuraties. Hier volgen enkele andere Azure-bronnen die u kunt gebruiken tijdens het implementeren van DSVM-exemplaren in uw onderneming:

* [Netwerkbeveiliging](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Bewaking](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) en [management](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Logboekregistratie en bewaking](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Op rollen gebaseerd toegangsbeheer](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Beleid instellen en afdwingen](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Versleuteling](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Gegevensdetectie en -beheer](https://docs.microsoft.com/azure/data-catalog/)

Ten slotte biedt de [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) een uitgebreide end-to-end architectuur en modellen voor het maken en beheren van uw Cloud infrastructuur.