---
title: Open Shift container platform 4. x in azure implementeren
description: Open Shift container platform 4. x in azure implementeren.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 213c02b76f822d134729ebc4c0e6bff40f62089f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035439"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Open Shift container platform 4. x in azure implementeren

De implementatie van open Shift container platform (OCP) 4,2 wordt nu ondersteund in azure via het IPI-model (installatie-ingerichte infra structuur).  De landings pagina voor het proberen van open Shift 4 is [try.openshift.com](https://try.openshift.com/). Als u wilt installeren OCP 4,2 in azure, gaat u naar de pagina [Red Hat open Shift cluster manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Er zijn referentie gegevens voor Red Hat vereist voor toegang tot deze site.


## <a name="notes"></a>Opmerkingen 

 - Een Azure Active Directory (AAD) Service Principal (SP) is vereist om OCP 4. x te installeren en uit te voeren in azure
     - Aan de SP moet de API-machtiging van **Application. readwrite. OwnedBy** voor Azure Active Directory Graph worden verleend
     - Een AAD-Tenant Administrator moet toestemming van de beheerder verlenen om deze API-machtiging toe te passen
     - Aan de SP moeten beheerders rollen voor **mede** werkers en **gebruikers toegang** worden verleend voor het abonnement
 - Het installatie model voor OCP 4. x wijkt af van 3. x en er zijn geen Azure Resource Manager sjablonen beschikbaar voor het implementeren van OCP 4. x in azure
 - Als er problemen optreden tijdens het installatie proces, neemt u contact op met het betreffende bedrijf (micro soft of Red Hat)

| Beschrijving van probleem | Contact punt |
|-------------------|---------------|
| Specifieke Azure-problemen (AAD, SP, Azure-abonnement, enz.)                              | Microsoft |
| Open Shift-specifieke problemen (installatie fouten/fouten, Red Hat-abonnement, enz.) |  Red Hat  |




## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met open Shift container platform](https://docs.openshift.com)
