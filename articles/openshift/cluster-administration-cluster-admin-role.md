---
title: Azure Red Hat open Shift cluster beheerdersrol | Microsoft Docs
description: Functie toewijzing en gebruik van Azure Red Hat open Shift Cluster Administrator
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709955"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat open Shift-klant beheerdersrol

Als ARO (Azure Red Hat open Shift) cluster beheerder van een open Shift-cluster heeft uw account verhoogde machtigingen en toegang tot alle door de gebruiker gemaakte projecten.

Als voor uw account de rol OSA-klant-Administrators is gebonden, kan deze automatisch een project beheren.

> [!Note] 
> OSA-klant-admin clusterrole is niet hetzelfde als cluster-beheer clusterrole


U kunt bijvoorbeeld acties uitvoeren die zijn gekoppeld aan een set termen (`create`) om te kunnen worden uitgevoerd op een set resource namen (`templates`). Voer de volgende opdracht uit om de details van deze rollen en hun sets met werk woorden en resources weer te geven:

`$ oc describe clusterrole/osa-customer-admin`

De namen van woorden worden niet noodzakelijkerwijs allemaal rechtstreeks aan OC-opdrachten toegewezen, maar in plaats daarvan moet u meer over de typen CLI-bewerkingen die u kunt uitvoeren. Als u bijvoorbeeld de opdracht `list` instelt, kunt u een lijst weer geven met alle objecten van een bepaalde resource naam (`oc get`), terwijl de `get` werk woord betekent dat u de details van een specifiek object kunt weer geven als u de naam weet (`oc describe`).

## <a name="how-to-configure-customer-administrator-role"></a>De rol klant beheerder configureren

De rol klant beheerder kan alleen worden geconfigureerd tijdens het maken van het cluster door een vlag `--customer-admin-group-id` op te geven. Instructies voor het configureren van Azure Active Directory en de groep Administrators volgen: [Azure Active Directory integratie voor Azure Red Hat open Shift](howto-aad-app-configuration.md)

## <a name="next-steps"></a>Volgende stappen

OSA-klant-beheerdersrol configureren:
> [!div class="nextstepaction"]
> [Azure Active Directory integratie voor Azure Red Hat open Shift](howto-aad-app-configuration.md)
