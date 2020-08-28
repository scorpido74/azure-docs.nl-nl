---
title: 'Referentie: uittredingen van Data Science Virtual Machinee afbeelding'
titleSuffix: Azure Data Science Virtual Machine
description: Details over de buiten gebruiks telling van de Azure Data Science Virtual Machine
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: d5f541dec14eebc944e4eac11dbe569b38cb277e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001617"
---
# <a name="reference-retirements-of-dsvm-images"></a>Naslag informatie: buiten gebruik stellen van DSVM-installatie kopieën

Hieronder bespreken we pensionering (afschaffing) van afbeeldingen en suggesties voor het oplossen van aanstaande pensioenten op de Azure-Data Science Virtual Machine.

## <a name="why-we-retire-dsvm-images"></a>Waarom we DSVM-installatie kopieën buiten gebruik stellen

De Data Science Virtual Machine heeft momenteel twee versies:

* Ubuntu
* Windows Server

De DSVM-installatie kopie voor deze edities is gebaseerd op een specifieke versie van het besturings systeem, bijvoorbeeld Ubuntu 18,04 LTS. Na verloop van tijd wordt het onderhouds venster voor de _versie_ van het besturings systeem beëindigd en/of worden oudere versies van besturings systemen niet meer ondersteund door de data Science-hulpprogram ma's. Om de DSVM-installatie kopie up-to-date te houden met de meest recente besturings systemen en data wetenschappen, wordt een nieuwe DSVM-installatie kopie uitgebracht op basis van nieuwere versies van het besturings systeem.

## <a name="how-we-retire-dsvm-images"></a>Hoe we DSVM-installatie kopieën buiten gebruik stellen

Er wordt een _pensioen melding_ verzonden waarin de bestaande DSVM-gebruikers worden gewaarschuwd (via e-mail) van een aanstaande DSVM-installatie kopie. In de aankondiging van de buiten gebruiks telling wordt de _pensionerings datum_ (na deze datum waarop de installatie kopie niet beschikbaar is), en de aanbevelingen voor het beperken (bijvoorbeeld upgraden naar een nieuwere DSVM-installatie kopie) gedetailleerd beschreven.

Op de _aankondigings_ datum gaan we de afbeelding in de Marketplace verbergen, zodat:

1. nieuwe gebruikers kunnen voor komen dat er per ongeluk een buiten gebruik gestelde DSVM-installatie kopie wordt ingericht.
2. bestaande gebruikers kunnen ARM-implementaties gebruiken tot de datum van beëindiging.

De verborgen installatie kopieën ontvangen besturingssysteem patches tot de _datum van buiten_ gebruik, maar ontvangt __geen__ updates voor de data Science-hulpprogram ma's en frameworks. Op de _datum van pensionering_is de installatie kopie niet beschikbaar voor arm-implementaties.

Alle ingerichte DSVM-installatie kopieën in uw abonnement blijven werken na de datum van beëindiging. We raden u echter aan om een upgrade uit te voeren naar de meest recente DSVM-installatie kopie, zodat u beschikt over de meest recente besturings systemen en data Science-hulp middelen.

## <a name="impact"></a>Impact

Bestaande installatie kopieën van DSVM die in uw abonnement zijn ingericht, blijven werken na de datum van pensionering. We raden gebruikers echter aan hun DSVM-installatie kopie bij te werken naar de nieuwere versie met behulp van de Azure Portal-of ARM-sjabloon.

> [!WARNING]
> Buiten gebruik gestelde DSVM-installatie kopieën die zijn ingericht met Virtual Machine Scale Sets, kunnen niet worden geschaald na de pensionerings datum.
>
> ARM-sjablonen die niet zijn bijgewerkt met de nieuwe Details van de DSVM-installatie kopie, kunnen niet worden geïmplementeerd na de pensionerings datum.

