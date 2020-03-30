---
title: Resources beheren in Azure Red Hat OpenShift | Microsoft Documenten
description: Projecten, sjablonen, afbeeldingsstromen beheren in een Azure Red Hat OpenShift-cluster
services: openshift
keywords: red hat openshift projecten verzoeken self-provisioner
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139110"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Projecten, sjablonen, afbeeldingsstromen beheren in een Azure Red Hat OpenShift-cluster 

In een OpenShift Container Platform worden projecten gebruikt om gerelateerde objecten te groeperen en te isoleren. Als beheerder u ontwikkelaars toegang geven tot specifieke projecten, hen in staat stellen hun eigen projecten te maken en hen administratieve rechten verlenen voor afzonderlijke projecten.

## <a name="self-provisioning-projects"></a>Zelfvoorzieningsprojecten

U ontwikkelaars in staat stellen hun eigen projecten te maken. Een API-eindpunt is verantwoordelijk voor het inrichten van een project volgens een sjabloon met de naam projectaanvraag. De webconsole `oc new-project` en de opdracht gebruiken dit eindpunt wanneer een ontwikkelaar een nieuw project maakt.

Wanneer een projectaanvraag wordt ingediend, vervangt de API de volgende parameters in de sjabloon:

| Parameter               | Beschrijving                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | De naam van het project. Vereist.             |
| PROJECT_DISPLAYNAME     | De weergavenaam van het project. Kan leeg zijn. |
| PROJECT_DESCRIPTION     | De beschrijving van het project. Kan leeg zijn.  |
| PROJECT_ADMIN_USER      | De gebruikersnaam van de administrerende gebruiker.       |
| PROJECT_REQUESTING_USER | De gebruikersnaam van de verzoekende gebruiker.           |

Toegang tot de API wordt verleend aan ontwikkelaars met de zelfprovisioners clusterrol binding. Deze functie is standaard beschikbaar voor alle geverifieerde ontwikkelaars.

## <a name="modify-the-template-for-a-new-project"></a>De sjabloon voor een nieuw project wijzigen 

1. Log in als `customer-admin` gebruiker met privileges.

2. Bewerk de standaardsjabloon projectaanvraag.

   ```
   oc edit template project-request -n openshift
   ```

3. Verwijder de standaardprojectsjabloon uit het AZURE Red Hat OpenShift-updateproces (Azure Red Hat OpenShift) door de volgende annotatie toe te voegen:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   De sjabloon voor projectaanvragen wordt niet bijgewerkt door het ARO-updateproces. Hierdoor kunnen klanten de sjabloon aanpassen en deze aanpassingen behouden wanneer het cluster wordt bijgewerkt.

## <a name="disable-the-self-provisioning-role"></a>De rol van zelfvoorziening uitschakelen

U voorkomen dat een geverifieerde gebruikersgroep nieuwe projecten zelf inrichten.

1. Log in als `customer-admin` gebruiker met privileges.

2. Bewerk de clusterrolbinding van zelfprovisioners.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Verwijder de rol uit het ARO-updateproces door `openshift.io/reconcile-protect: "true"`de volgende annotatie toe te voegen: .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Wijzig de clusterrol `system:authenticated:oauth` binding om te voorkomen dat projecten worden gemaakt:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>Standaardsjablonen en imageStreams beheren

In Azure Red Hat OpenShift u updates uitschakelen `openshift` voor standaardsjablonen en afbeeldingsstromen in de naamruimte.
Update voor ALL `Templates` `ImageStreams` en `openshift` in de naamruimte uitschakelen:

1. Log in als `customer-admin` gebruiker met privileges.

2. Naamruimte `openshift` bewerken:

   ```
   oc edit namespace openshift
   ```

3. Verwijder `openshift` naamruimte uit het ARO-updateproces door de volgende annotatie toe te voegen:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Elk afzonderlijk object `openshift` in de naamruimte kan uit het `openshift.io/reconcile-protect: "true"` updateproces worden verwijderd door er een annotatie aan toe te voegen.

## <a name="next-steps"></a>Volgende stappen

Probeer de zelfstudie:
> [!div class="nextstepaction"]
> [Een Azure Red Hat OpenShift-cluster maken](tutorial-create-cluster.md)
