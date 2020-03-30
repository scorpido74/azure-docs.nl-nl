---
title: Een kaartstijl instellen met Azure Maps Android SDK| Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over microsoft Azure Maps-stijlgerelateerde functionaliteiten voor de Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a4d761abf54682ed0263922d0a118debc9eccf0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334354"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Kaartstijl instellen met Azure Maps Android SDK

In dit artikel ziet u twee manieren om kaartstijlen in te stellen met de Azure Maps Android SDK. Azure Maps heeft zes verschillende kaartstijlen om uit te kiezen. Zie [ondersteunde kaartstijlen in Azure Maps](./supported-map-styles.md)voor meer informatie over ondersteunde kaartstijlen.


## <a name="prerequisites"></a>Vereisten

Als u het proces in dit artikel wilt voltooien, moet u [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) installeren om een kaart te laden.


## <a name="set-map-style-in-the-layout"></a>Kaartstijl instellen in de indeling

U een kaartstijl instellen in het indelingsbestand voor uw activiteitsklasse. Bewerk **opnieuw > lay-out > activity_main.xml,** zodat het lijkt op de onderstaande:

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

In `mapcontrol_style` het kenmerk hierboven wordt de kaartstijl ingesteld op **grayscale_dark**. 

<center>

![stijl-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Kaartstijl instellen in de activiteitsklasse

De kaartstijl kan worden ingesteld in de activiteitsklasse. Kopieer het volgende codefragment naar de methode `MainActivity.java` **onCreate()** van uw klasse. Met deze code wordt de kaartstijl ingesteld op **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![stijl-satelliet-weg-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>