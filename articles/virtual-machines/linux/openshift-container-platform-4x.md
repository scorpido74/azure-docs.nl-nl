---
title: Open Shift container platform 4. x in azure implementeren
description: Open Shift container platform 4. x in azure implementeren.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f2fb1657c26fce3e1fdd67f36b0c6511b78dd216
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373535"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Open Shift container platform 4. x in azure implementeren

De implementatie van open Shift container platform (OCP) 4,2 wordt nu ondersteund in azure via het model van de Installer-Provisioned Infrastructure (IPI).  De landings pagina voor het proberen van open Shift 4 is [try.openshift.com](https://try.openshift.com/). Als u wilt installeren OCP 4,2 in azure, gaat u naar de pagina [Red Hat open Shift cluster manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Er zijn referentie gegevens voor Red Hat vereist voor toegang tot deze site.


## <a name="notes"></a>Notities 

 - Een Azure Active Directory (AAD) Service Principal (SP) is vereist om OCP 4. x te installeren en uit te voeren in azure
     - Aan de SP moet de API-machtiging van **Application. readwrite. OwnedBy** voor Azure Active Directory Graph worden verleend
     - Een AAD-Tenant Administrator moet toestemming van de beheerder verlenen om deze API-machtiging toe te passen
     - Aan de SP moeten beheerders rollen voor **mede** werkers en **gebruikers toegang** worden verleend voor het abonnement
 - Het installatie model voor OCP 4. x wijkt af van 3. x en er zijn geen Azure Resource Manager sjablonen beschikbaar voor het implementeren van OCP 4. x in azure
 - Als er problemen optreden tijdens het installatie proces, neemt u contact op met het betreffende bedrijf (micro soft of Red Hat)

| Probleembeschrijving | Contact punt |
|-------------------|---------------|
| Specifieke Azure-problemen (AAD, SP, Azure-abonnement, enz.)                              | Microsoft |
| Open Shift-specifieke problemen (installatie fouten/fouten, Red Hat-abonnement, enz.) |  Red Hat  |




## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met open Shift container platform](https://docs.openshift.com)
