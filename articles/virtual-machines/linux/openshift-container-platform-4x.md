---
title: OpenShift Container Platform 4.x implementeren in Azure
description: Implementeren OpenShift Container Platform 4.x in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759491"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>OpenShift Container Platform 4.x implementeren in Azure

Implementatie van OpenShift Container Platform (OCP) 4.2 wordt nu ondersteund in Azure via het IPI-model (Installer Provisioned Infrastructure).  De bestemmingspagina voor het proberen van OpenShift 4 is [try.openshift.com](https://try.openshift.com/). Als u OCP 4.2 in Azure wilt installeren, gaat u naar de pagina [Red Hat OpenShift Cluster Manager.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  Red Hat referenties zijn vereist om toegang te krijgen tot deze site.


## <a name="notes"></a>Opmerkingen 

 - Er is een Azure Active Directory (AAD) Service Principal (SP) vereist om OCP 4.x in Azure te installeren en uit te voeren
     - De SP moet de API-toestemming van **Application.ReadWrite.OwnedBy** voor Azure Active Directory Graph krijgen
     - Een AAD Tenant Administrator moet beheerderstoestemming verlenen voor deze API-toestemming om van kracht te worden
     - De SP moet bijdragen **de rol van inzender** en **gebruikerstoegangsbeheerder** aan het abonnement krijgen
 - Het installatiemodel voor OCP 4.x is anders dan 3.x en er zijn geen Azure Resource Manager-sjablonen beschikbaar voor het implementeren van OCP 4.x in Azure
 - Als er problemen worden opgetreden tijdens het installatieproces, neemt u contact op met het desbetreffende bedrijf (Microsoft of Red Hat)

| Beschrijving van het probleem | Contactpunt |
|-------------------|---------------|
| Azure-specifieke problemen (AAD, SP, Azure-abonnement, enz.)                              | Microsoft |
| OpenShift-specifieke problemen (Installatiefouten / fouten, Red Hat-abonnement, enz.) |  Red Hat  |




## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met OpenShift Container Platform](https://docs.openshift.com)
