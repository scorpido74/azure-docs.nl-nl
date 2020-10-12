---
title: Een kaart stijl instellen met behulp van Azure Maps Android SDK | Microsoft Azure kaarten
description: Meer informatie over twee manieren om de stijl van een kaart in te stellen. Zie How to use the Azure Maps Android SDK in het indelings bestand of de klasse activity om de stijl aan te passen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4e37ae82b46ce06162d0a67d74af54cddaf3f6eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88030942"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Kaart stijl instellen met Azure Maps Android SDK

In dit artikel ziet u twee manieren om kaart stijlen in te stellen met behulp van de Azure Maps Android SDK. Azure Maps heeft zes verschillende kaarten stijlen waaruit u kunt kiezen. Zie [ondersteunde kaart stijlen in azure Maps](./supported-map-styles.md)voor meer informatie over ondersteunde kaart stijlen.


## <a name="prerequisites"></a>Vereisten

Om het proces in dit artikel te volt ooien, moet u [Azure Maps ANDROID SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) installeren om een kaart te laden.


## <a name="set-map-style-in-the-layout"></a>Kaart stijl instellen in de lay-out

U kunt een kaart stijl instellen in het indelings bestand voor uw activiteiten klasse. Wijzig de **indeling res > > activity_main.xml**, zodat deze er als volgt uitziet:

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style`Met het bovenstaande kenmerk stelt u de stijl van de kaart in op **grayscale_dark**. 

<center>

![stijl-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Kaart stijl instellen in de klasse activiteit

Kaart stijl kan worden ingesteld in de klasse activity. Kopieer het volgende code fragment in de methode **onCreate ()** van uw `MainActivity.java` klasse. Met deze code wordt de kaart stijl ingesteld op **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![stijl-satelliet-Road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>