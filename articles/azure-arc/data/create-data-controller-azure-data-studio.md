---
title: Gegevens controller maken in Azure Data Studio
description: Gegevens controller maken in Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f6fd8169c587e928da9946d74335ddc758889144
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273140"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Gegevens controller maken in Azure Data Studio

U kunt een gegevens controller maken met behulp van Azure Data Studio via de implementatie wizard en notebooks.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

- U hebt toegang tot een Kubernetes-cluster nodig en het kubeconfig-bestand moet zijn geconfigureerd om te verwijzen naar het Kubernetes-cluster waarnaar u wilt implementeren.
- U moet [de client hulpprogramma's installeren](install-client-tools.md) , met inbegrip van **Azure Data Studio** de Azure Data Studio extensies **Azure Arc** en **Azure data cli**.
- U moet zich aanmelden bij Azure in Azure Data Studio.  Ga hiervoor als volgt te werk: Typ CTRL/Command + SHIFT + P om het opdracht tekst venster te openen en typ **Azure**.  Kies **Azure: Meld u aan**.   In het deel venster klikt u op het pictogram + in de rechter bovenhoek om een Azure-account toe te voegen.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Gebruik de implementatie wizard om een Azure Arc-gegevens controller te maken

Volg deze stappen om een Azure-Arc-gegevens controller te maken met behulp van de implementatie wizard.

1. Klik in Azure Data Studio op het tabblad verbindingen in de linkernavigatiebalk.
2. Klik op de knop **...** boven aan het deel venster verbindingen en kies **nieuwe implementatie...**
3. Kies in de wizard nieuwe implementatie de optie **Azure Arc-gegevens controller**, schakel het selectie vakje licentie acceptatie in en klik vervolgens op de knop **selecteren** onderaan.
4. Gebruik het standaard kubeconfig-bestand of selecteer een andere.  Klik op **Volgende**.
5. Kies een Kubernetes-cluster context. Klik op **Volgende**.
6. Kies een configuratie profiel bestand voor de implementatie, afhankelijk van uw doel-Kubernetes-cluster. **Klik op volgende**.
8. Kies het gewenste abonnement en de resource groep.
9. Voer een naam in voor de gegevens controller en voor de naam ruimte waarin de gegevens controller wordt gemaakt.  

> [!NOTE]
> Als de naam ruimte al bestaat, wordt deze gebruikt als de naam ruimte nog geen andere Kubernetes-objecten bevat: Peul, enzovoort.  Als de naam ruimte niet bestaat, wordt er een poging gedaan om de naam ruimte te maken.  Voor het maken van een naam ruimte in een Kubernetes-cluster zijn Kubernetes-cluster beheerder bevoegdheden vereist.  Als u geen Kubernetes hebt, vraagt u uw Kubernetes-cluster beheerder de eerste paar stappen uit te voeren in het artikel [een gegevens controller maken met Kubernetes-systeem eigen hulpprogram ma's](./create-data-controller-using-kubernetes-native-tools.md) die moeten worden uitgevoerd door een Kubernetes-beheerder voordat u deze wizard voltooit.

> [!NOTE]
> Opmerking: de gegevens controller en naam van de naam ruimte worden gebruikt voor het maken van een aangepaste resource in het Kubernetes-cluster, zodat ze moeten voldoen aan de [Kubernetes-naamgevings conventies](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).

10. Selecteer een Azure-locatie.
   
> [!NOTE]
> De Azure-locatie die u hier selecteert, is de locatie in azure waar de *meta gegevens* van de gegevens controller en de data base-exemplaren die worden beheerd, worden opgeslagen.  De gegevens controller-en data base-instanties worden in werkelijkheid crewted in uw Kubernetes-cluster waar dit mogelijk is.

11.  Voer een gebruikers naam en wacht woord in en bevestig het wacht woord voor het gebruikers account van de gegevens controller beheerder.

> [!NOTE]
> Het wacht woord moet ten minste acht tekens lang zijn.

1.  Klik op **Volgende**.
2.  Bekijk en klik op **script naar notebook**.
3.  **Bekijk het gegenereerde notitie blok**.  Breng de gewenste wijzigingen aan, zoals namen van opslag klassen of service typen.
4.  Klik boven aan het notitie blok op **alles uitvoeren** .

## <a name="monitoring-the-creation-status"></a>De aanmaak status bewaken

Het duurt enkele minuten voordat de controller is gemaakt. U kunt de voortgang in een ander Terminal venster bewaken met de volgende opdrachten:

> [!NOTE]
>  In de onderstaande voor beelden wordt ervan uitgegaan dat u een gegevens controller en Kubernetes-naam ruimte hebt gemaakt met de naam ' Arc '.  Als u de naam van een andere naam ruimte/gegevens controller hebt gebruikt, kunt u ' Arc ' vervangen door uw naam.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

U kunt ook de status van de aanmaak van een bepaalde pod controleren door een opdracht als hieronder uit te voeren.  Dit is met name nuttig voor het oplossen van problemen.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemen bij het maken oplossen

Raadpleeg de [hand leiding](troubleshoot-guide.md)voor het oplossen van problemen als u problemen ondervindt bij het maken.
