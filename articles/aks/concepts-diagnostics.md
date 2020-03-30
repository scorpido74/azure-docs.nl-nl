---
title: Overzicht van Azure Kubernetes Service (AKS) Diagnostics
description: Meer informatie over zelfdiagnoseclusters in Azure Kubernetes Service.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126604"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Overzicht van Azure Kubernetes Service Diagnostics (preview)

Het oplossen van problemen met azure Kubernetes Service (AKS) clusterproblemen is een belangrijk onderdeel van het onderhouden van uw cluster, vooral als uw cluster bedrijfskritieke workloads uitvoert. AKS Diagnostics is een intelligente, zelfdiagnostische ervaring die u helpt bij het identificeren en oplossen van problemen in uw cluster. AKS Diagnostics is cloud-native en u het gebruiken zonder extra configuratie- of factureringskosten.

Deze functie is nu in openbare preview.

## <a name="open-aks-diagnostics"></a>Aks-diagnose openen

Ga als toegangsvoor AKS Diagnostics:

- Navigeer naar uw Kubernetes-cluster in de [Azure-portal.](https://portal.azure.com)
- Klik op **Diagnosticeren en los problemen op** in de linkernavigatie, die AKS Diagnostics opent.
- Kies een categorie die het probleem van uw cluster het beste beschrijft met behulp van de trefwoorden in de tegel op de startpagina of typ een trefwoord dat het beste uw probleem beschrijft in de zoekbalk, bijvoorbeeld _Clusterknooppuntproblemen._

![Startpagina](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Een diagnostisch rapport weergeven

Nadat u op een categorie hebt geklikt, u een diagnostisch rapport bekijken dat specifiek is voor uw cluster. Diagnostisch rapport roept op intelligente wijze als er een probleem is in uw cluster met statuspictogrammen. U inzoomen op elk onderwerp door op **Meer informatie** te klikken om een gedetailleerde beschrijving van het probleem, aanbevolen acties, koppelingen naar nuttige documenten, gerelateerde statistieken en logboekregistratiegegevens te bekijken. Diagnostische rapporten worden op intelligente wijze gegenereerd op basis van de huidige status van uw cluster na het uitvoeren van verschillende controles. Diagnostische rapporten kunnen een handig hulpmiddel zijn om het probleem van uw cluster te lokaliseren en de volgende stappen te vinden om het probleem op te lossen.

![Diagnostisch rapport](./media/concepts-diagnostics/diagnostic-report.png)

![Uitgebreid diagnostisch rapport](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Clusterinzichten

De volgende diagnostische controles zijn beschikbaar in **Clusterinsights**.

### <a name="cluster-node-issues"></a>Problemen met clusterknooppunt

Clusterknooppuntproblemen controleert op knooppuntgerelateerde problemen waardoor uw cluster zich onverwacht kan gedragen.

- Problemen met de gereedheid van het knooppunt
- Node-fouten
- Onvoldoende middelen
- Knooppunt ontbrekende IP-configuratie
- Node CNI-fouten
- Knooppunt niet gevonden
- Knooppunt uitschakelen
- Node-verificatiefout
- Knooppunt kube-proxy muf

### <a name="create-read-update--delete-operations"></a>Bewerkingen maken, lezen, bijwerken & verwijderen

CRUD Operations controleert op crud-bewerkingen die problemen in uw cluster kunnen veroorzaken.

- Fout bij ingebruikname subnetverwijderbewerking
- Fout bij het verwijderen van de netwerkbeveiligingsgroep
- Fout in gebruik seintabel verwijderen
- Fout bij het inrichten van resources
- Fout bij verwijderen van openbare IP-adres
- Implementatiefout als gevolg van implementatiequotum
- Bewerkingsfout als gevolg van organisatiebeleid
- Registratie van ontbrekende abonnementen
- Fout inhet inrichten van VM-extensie
- Subnetcapaciteit
- Fout overschreden quotum

### <a name="identity-and-security-management"></a>Identiteits- en beveiligingsbeheer

Identiteits- en beveiligingsbeheer detecteert verificatie- en autorisatiefouten die communicatie naar uw cluster kunnen voorkomen.

- Fouten in knooppuntautorisatie
- 401 fouten
- 403-fouten

## <a name="next-steps"></a>Volgende stappen

Verzamel logboeken om u te helpen uw clusterproblemen verder op te lossen met [AKS Periscope.](https://aka.ms/aksperiscope)

Plaats uw vragen of feedback op [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) door "[Diag]" toe te voegen in de titel.
