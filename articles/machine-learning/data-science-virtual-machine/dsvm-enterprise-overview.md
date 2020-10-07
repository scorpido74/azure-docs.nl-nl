---
title: Teamanalyse en AI-omgeving
titleSuffix: Azure Data Science Virtual Machine
description: Patronen voor het implementeren van de Data Science VM in een bedrijfsteamomgeving.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science process
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: b1357c9bb125cb881ac4aa6dd31c9dcaf53954f0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87919936"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Teamanalyse en AI-omgeving op basis van de Data Science Virtual Machine 
De [Data Science Virtual Machine](overview.md) (DSVM) biedt een uitgebreide omgeving op het Azure-platform, met vooraf ontwikkelde software voor kunstmatige intelligentie (AI) en gegevensanalyse.

De DSVM werd oorspronkelijk als afzonderlijk analysebureaublad gebruikt. Individuele gegevenswetenschappers komen tot een grotere productiviteit met deze gedeelde, vooraf ontwikkelde analyseomgeving. Wanneer grote analyseteams omgevingen plannen voor hun gegevenswetenschappers en AI-ontwikkelaars, is een van de terugkerende thema's een gedeelde analyse-infrastructuur voor ontwikkeling en experimenten. Deze infrastructuur wordt beheerd in overeenstemming met het IT-bedrijfsbeleid, waarmee de samenwerking en consistentie voor de gegevenswetenschap en binnen analyseteams worden vereenvoudigd.

Een gedeelde infrastructuur zorgt voor een beter IT-gebruik van de analyseomgeving. Sommige organisaties noemen de op het team gebaseerde gegevenswetenschap/analyse-infrastructuur een *analysesandbox*. Hiermee kunnen gegevenswetenschappers toegang krijgen tot verschillende gegevensassets voor een snel inzicht in gegevens. In deze sandbox-omgeving kunnen gegevenswetenschappers ook experimenten uitvoeren, hypothesen valideren en voorspellende modellen maken zonder dat dit van invloed is op de productieomgeving.

Omdat de DSVM werkt op het niveau van de Azure-infrastructuur, kunnen IT-beheerders de DSVM gemakkelijk zo configureren dat deze conform het IT-bedrijfsbeleid werkt. De DSVM biedt volledige flexibiliteit bij het implementeren van verschillende architecturen voor delen, waarbij deze ook op een gecontroleerde manier toegang biedt tot zakelijke gegevensassets.

In deze sectie worden enkele patronen en richtlijnen besproken die u kunt gebruiken om de DSVM te implementeren als een op een team gebaseerde infrastructuur voor gegevenswetenschap. Omdat de bouwstenen voor deze patronen afkomstig zijn van de Azure-infrastructuur als een dienst (IaaS), zijn deze van toepassing op alle virtuele machines van Azure. Deze reeks artikelen richt zich op het toepassen van deze standaardfuncties van de Azure-infrastructuur op de DSVM.

De belangrijkste bouwstenen van een zakelijke teamanalyse-omgeving zijn onder meer:

* [Een automatisch geschaalde pool met DSVM's](dsvm-pools.md)
* [Algemene identiteit en toegang tot een werkruimte vanuit elke DSVM in de pool](dsvm-common-identity.md)
* [Toegang tot de gegevensbronnen beveiligen](dsvm-secure-access-keys.md)


Deze reeks bevat richtlijnen voor en verwijzingen naar alle voorgaande onderwerpen. Dit geldt niet voor alle overwegingen en vereisten voor het implementeren van DSVM's in grote bedrijfsconfiguraties. Hier volgen enkele andere Azure-resources die u kunt gebruiken tijdens het implementeren van DSVM-exemplaren in uw onderneming:

* [Netwerkbeveiliging](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Controle](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) en [beheer](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Logboekregistratie en bewaking](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Azure RBAC (op rollen gebaseerd toegangsbeheer)](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Beleidsinstelling en -afdwinging](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Versleuteling](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)
* [Gegevensdetectie en -beheer](https://docs.microsoft.com/azure/data-catalog/)

Ten slotte biedt het [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) een uitgebreide end-to-end architectuur en modellen voor het maken en beheren van uw infrastructuur voor analyses in de cloud.