---
title: Inleiding tot Azure Red Hat open Shift
description: Meer informatie over de functies en voor delen van Microsoft Azure Red Hat open Shift voor het implementeren en beheren van op containers gebaseerde toepassingen.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 4be388eec3851154fd0f6af37d03a468fc52197b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276061"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Met de micro soft *Azure Red Hat open Shift* -service kunt u volledig beheerde open [SHIFT](https://www.openshift.com/) -clusters implementeren.

Azure Red Hat open Shift breidt [Kubernetes](https://kubernetes.io/)uit. Voor het uitvoeren van containers in productie met Kubernetes zijn extra hulp middelen en bronnen vereist, zoals een installatie kopie register, opslag beheer, netwerk oplossingen en hulpprogram ma's voor logboek registratie en controle, die allemaal moeten worden geversiond en getest. Voor het bouwen van op containers gebaseerde toepassingen is nog meer integratie mogelijk met middleware, frameworks, data bases en CI/CD-hulpprogram ma's. Azure Red Hat open Shift combineert alles samen met een enkel platform, waardoor de IT-teams op eenvoudige manier bewerkingen kunnen uitvoeren, terwijl ze toepassings teams kunnen voorzien van wat ze nodig hebben om uit te voeren.

Azure Red Hat open Shift is gezamenlijk ontworpen, geëxploiteerd en ondersteund door Red Hat en micro soft om een geïntegreerde ondersteunings ervaring te bieden. Er zijn geen virtuele machines die kunnen worden uitgevoerd en er is geen patches vereist. Master-, infra structuur-en toepassings knooppunten worden door Red Hat en micro soft gerepareerd, bijgewerkt en gecontroleerd. Uw Azure Red Hat open Shift-clusters worden geïmplementeerd in uw Azure-abonnement en zijn opgenomen in uw Azure-factuur.

U kunt uw eigen REGI ster-, netwerk-, opslag-en CI/CD-oplossingen kiezen of de ingebouwde oplossingen gebruiken voor geautomatiseerd broncode beheer, container-en toepassings builds, implementaties, schalen, status beheer en meer. Azure Red Hat open Shift biedt een geïntegreerde aanmeldings ervaring via Azure Active Directory.

Voltooi de zelf studie [een Azure Red Hat open Shift-cluster maken](tutorial-create-cluster.md) om aan de slag te gaan.

## <a name="access-security-and-monitoring"></a>Toegang, beveiliging en bewaking

Voor een betere beveiliging en meer beheer kunt u met Azure Red Hat open Shift integreren met Azure Active Directory (Azure AD) en gebruikmaken van Kubernetes op rollen gebaseerd toegangs beheer (RBAC). U kunt ook de status van uw cluster en resources bewaken.

## <a name="cluster-and-node"></a>Cluster en knooppunt

Azure Red Hat open Shift-knoop punten worden uitgevoerd op virtuele machines van Azure. U kunt opslag met knooppunten en pods verbinden, clusteronderdelen bijwerken en GPU's gebruiken.

## <a name="virtual-networks-and-ingress"></a>Virtual Networks en inkomend verkeer

U kunt [een Azure Red Hat open Shift-cluster verbinden met een bestaand virtueel netwerk](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) via peering. In deze configuratie kan het Peul verbinding maken met andere services in een gekoppeld virtueel netwerk.

Zie [verbinding maken met een virtueel netwerk van een cluster met een bestaand virtueel netwerk](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) voor meer informatie.

## <a name="kubernetes-certification"></a>Kubernetes-certificering

De Azure Red Hat open Shift-service is CNCF gecertificeerd als Kubernetes, conform de conformiteit.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de vereisten voor Azure Red Hat open Shift:

> [!div class="nextstepaction"]
> [Uw ontwikkelaarsomgeving instellen](howto-setup-environment.md)
