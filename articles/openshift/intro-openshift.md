---
title: Inleiding tot Azure Red Hat OpenShift
description: Ontdek de functies en voordelen van Microsoft Azure Red Hat OpenShift voor het implementeren en beheren van containergebaseerde toepassingen.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 4be388eec3851154fd0f6af37d03a468fc52197b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76276061"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Met de Microsoft *Azure Red Hat OpenShift-service* u volledig beheerde [OpenShift-clusters](https://www.openshift.com/) implementeren.

Azure Red Hat OpenShift breidt [Kubernetes](https://kubernetes.io/)uit. Het uitvoeren van containers in productie met Kubernetes vereist extra tools en resources, zoals een imageregistry, storage management, netwerkoplossingen en logging- en monitoringtools, die allemaal samen moeten worden geversioned en getest. Het bouwen van containergebaseerde toepassingen vereist nog meer integratiewerk met middleware, frameworks, databases en CI/CD-tools. Azure Red Hat OpenShift combineert dit alles in één platform, waardoor IT-teams eenvoudig kunnen worden uitgevoerd en toepassingsteams worden wat ze moeten uitvoeren.

Azure Red Hat OpenShift wordt gezamenlijk ontworpen, beheerd en ondersteund door Red Hat en Microsoft om een geïntegreerde ondersteuningservaring te bieden. Er zijn geen virtuele machines te bedienen, en geen patchen is vereist. Hoofd-, infrastructuur- en toepassingsknooppunten worden namens u gepatcht, bijgewerkt en gecontroleerd door Red Hat en Microsoft. Uw Azure Red Hat OpenShift-clusters worden geïmplementeerd in uw Azure-abonnement en zijn opgenomen op uw Azure-factuur.

U uw eigen register-, netwerk-, opslag- en CI/CD-oplossingen kiezen of de ingebouwde oplossingen gebruiken voor geautomatiseerd broncodebeheer, container- en toepassingsbuilds, implementaties, schalen, gezondheidsbeheer en meer. Azure Red Hat OpenShift biedt een geïntegreerde aanmeldingservaring via Azure Active Directory.

Voer de [zelfstudie van een Azure Red Hat OpenShift-cluster](tutorial-create-cluster.md) aan om aan de slag te gaan.

## <a name="access-security-and-monitoring"></a>Toegang, beveiliging en bewaking

Voor een betere beveiliging en een beter beheer u met Azure Red Hat OpenShift integreren met Azure Active Directory (Azure AD) en gebruik maken van Kubernetes-functiegebaseerde toegangscontrole (RBAC). U kunt ook de status van uw cluster en resources bewaken.

## <a name="cluster-and-node"></a>Cluster en knooppunt

Azure Red Hat OpenShift-knooppunten worden uitgevoerd op virtuele Azure-machines. U kunt opslag met knooppunten en pods verbinden, clusteronderdelen bijwerken en GPU's gebruiken.

## <a name="virtual-networks-and-ingress"></a>Virtual Networks en inkomend verkeer

U [een Azure Red Hat OpenShift-cluster via](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) peering verbinden met een bestaand virtueel netwerk. In deze configuratie kunnen pods verbinding maken met andere services in een gepeerd virtueel netwerk.

Zie [Het virtuele netwerk van een cluster verbinden met een bestaand virtueel netwerk](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) voor meer informatie.

## <a name="kubernetes-certification"></a>Kubernetes-certificering

Azure Red Hat OpenShift-service is CNCF-gecertificeerd als Kubernetes-conformant.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de vereisten voor Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Uw ontwikkelaarsomgeving instellen](howto-setup-environment.md)
