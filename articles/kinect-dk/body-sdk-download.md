---
title: Azure Kinect Body tracking SDK downloaden
description: Meer informatie over het downloaden van elke versie van de Azure Kinect sensor SDK op Windows of Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, kinect, SDK, update downloaden, nieuwste, beschikbaar, installeren, hoofd tekst, bijhouden
ms.openlocfilehash: e3f8233d208e2a45c1af9a52a76b6064b15bfe4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277350"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>De Azure Kinect Body tracking-SDK downloaden

Dit document bevat koppelingen voor het installeren van elke versie van de Azure Kinect Body tracking-SDK.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Inhoud bijhouden SDK Azure Kinect Body

- Kopteksten en bibliotheken om een toepassing voor het bijhouden van hoofd tekst te maken met behulp van Azure Kinect DK.
- Herdistribueerbare dll-bestanden die nodig zijn voor het bijhouden van hoofd toepassingen met behulp van Azure Kinect DK.
- Voorbeeld toepassingen voor het bijhouden van hoofd tekst.

## <a name="windows-download-links"></a>Koppelingen voor het downloaden van Windows

Versie       | Downloaden
--------------|----------
1.0.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)
0.9.5 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100636) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5)
0.9.4 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100415) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4)
0.9.3 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100307) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3)
0.9.2 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100128) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2)
0.9.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100063) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1)
0.9.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=58402) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0)

## <a name="linux-installation-instructions"></a>Linux-installatie-instructies

Momenteel is de enige ondersteunde distributie Ubuntu 18,04. Zie [Deze pagina](https://aka.ms/azurekinectfeedback)als u ondersteuning voor andere distributies wilt aanvragen.

Eerst moet u de [opslag plaats van micro soft-pakket](https://packages.microsoft.com/)configureren. Volg hiervoor de instructies [hier](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software).

Het `libk4abt<major>.<minor>-dev` pakket bevat de kop-en cmake-bestanden die moeten worden gebouwd `libk4abt` .
Het `libk4abt<major>.<minor>` pakket bevat de gedeelde objecten die nodig zijn voor het uitvoeren van uitvoer bare bestanden die afhankelijk zijn van `libk4abt` en de voorbeeld viewer.

Voor de basis zelf studies is het `libk4abt<major>.<minor>-dev` pakket vereist. Voer uit om de installatie uit te voeren.

`sudo apt install libk4abt1.0-dev`

Als de opdracht is geslaagd, is de SDK klaar voor gebruik.

> [!NOTE]
> Onthoud bij het installeren van de SDK het pad dat u wilt installeren. Bijvoorbeeld: "C:\Program Files\Azure Kinect Body tracking SDK 1.0.0". U vindt de voor beelden waarnaar in de artikelen in dit pad wordt verwezen.
> Voor beelden van het bijhouden van hoofd tekst bevinden zich in de map [Body-tracking-samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) in de opslag plaats Azure-Kinect-samples. U vindt hier de voor beelden waarnaar in artikelen wordt verwezen.

## <a name="change-log"></a>Wijzigingenlogboek

### <a name="v101"></a>v 1.0.1
* [Fout oplossing] Probleem oplossen dat de SDK vastloopt bij het laden van onnxruntime.dll van pad op Windows Build 19025 of hoger: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Hulp Voeg C#-wrapper toe aan het MSI-installatie programma.
* [Fout oplossing] Probleem oplossen dat de hoofd draaiing niet correct kan worden gedetecteerd: [koppeling](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Fout oplossing] Probleem oplossen dat het CPU-gebruik tot 100% op Linux-machine aanloopt: [koppeling](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Voor beelden Voeg twee steek proeven toe aan het voor beeld-opslag plaats. Voor beeld 1 ziet u hoe u de resultaten van het bijhouden van hoofd tekst kunt transformeren van de diepte ruimte naar de [koppeling](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)naar de kleur ruimte. voor beeld 2 ziet u hoe u een [koppeling](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) naar het vloer vlak detecteert

### <a name="v095"></a>v 0.9.5
* Hulp C#-ondersteuning. C#-wrapper is verpakt in het nuget-pakket.
* Hulp Ondersteuning voor meerdere tracker. Het maken van meerdere traceerers is toegestaan. De gebruiker kan nu meerdere traceerers maken om de hoofd tekst van de verschillende Azure Kinect-apparaten bij te houden.
* Hulp Ondersteuning voor multi thread-verwerking voor de CPU-modus. Wanneer de CPU-modus wordt uitgevoerd, worden alle kern geheugens gebruikt om de snelheid te maximaliseren.
* Hulp Toevoegen `gpu_device_id` aan `k4abt_tracker_configuration_t` struct. Gebruikers toestaan om het GPU-apparaat op te geven dat niet de standaard is voor het uitvoeren van het algoritme voor het bijhouden van de hoofd tekst.
* [Wijziging/herstel van fouten] Corrigeer de type fout in een gezamenlijke naam. Wijzig de gezamenlijke naam van `K4ABT_JOINT_SPINE_NAVAL` in `K4ABT_JOINT_SPINE_NAVEL` .

### <a name="v094"></a>v 0.9.4
* Hulp Voeg ondersteuning voor hand gewrichten toe. De SDK bevat informatie voor drie extra gewrichten voor elke hand: HAND, HANDTIP, duim.
* Hulp Voeg het betrouwbaarheids niveau voor voor spellingen voor elke gedetecteerde gewricht toe.
* Hulp Ondersteuning voor CPU-modus toevoegen. `cpu_only_mode`Als u de waarde in wijzigt `k4abt_tracker_configuration_t` , kan de SDK worden uitgevoerd op de CPU-modus waarvoor de gebruiker geen krachtige grafische kaart nodig heeft.

### <a name="v093"></a>v 0.9.3
* Hulp Publiceer een nieuw DNN-model dnn_model_2_0. onnx, waarmee de stabiliteit van het bijhouden van de hoofd tekst grotendeels wordt verbeterd.
* Hulp Schakel standaard tijdelijk effenen uit. De getraceerde gewrichten reageren sneller.
* Hulp Verbeter de nauw keurigheid van de index toewijzing van de hoofd tekst.
* [Fout oplossing] Los de fout op die de instelling voor de richting van de sensor niet effectief is.
* [Fout oplossing] Wijzig het body_index_map type van K4A_IMAGE_FORMAT_CUSTOM in K4A_IMAGE_FORMAT_CUSTOM8.
* [Bekend probleem] Twee close-instanties kunnen samen voegen tot een segment met één exemplaar.

### <a name="v092"></a>v 0.9.2
* [Laatste wijziging] Update die afhankelijk is van de nieuwste Azure Kinect sensor SDK 1.2.0.
* [API-wijziging] `k4abt_tracker_create` de functie begint met het uitvoeren van een `k4abt_tracker_configuration_t` invoer. 
* [API-wijziging] Wijzig `k4abt_frame_get_timestamp_usec` de API zodat `k4abt_frame_get_device_timestamp_usec` deze specifiek en consistent is met de sensor SDK 1.2.0.
* Hulp Gebruikers toestaan om de richting van de sensor montage op te geven bij het maken van de tracker om nauw keurigere traceer resultaten te krijgen bij het koppelen aan verschillende hoeken.
* Hulp Een nieuwe API bieden `k4abt_tracker_set_temporal_smoothing` om de hoeveelheid tijdelijke afvlakking te wijzigen die de gebruiker wil Toep assen.
* Hulp C++ wrapper k4abt. hpp toevoegen.
* Hulp De versie definitie-header k4abtversion. h toevoegen.
* [Fout oplossing] Los de fout op die een extreem hoog CPU-gebruik heeft veroorzaakt.
* [Fout oplossing] Fout bij het vastlopen van logboek registratie oplossen.

### <a name="v091"></a>v 0.9.1 tot en
* [Fout oplossing] Geheugenlek herstellen bij het vernietigen van tracker
* [Fout oplossing] Betere fout berichten voor ontbrekende afhankelijkheden
* [Fout oplossing] Mislukken zonder te zijn vastgelopen bij het maken van een tweede tracker-exemplaar
* [Fout oplossing] De registratie van omgevings variabelen werkt nu correct
* Linux Support

### <a name="v090"></a>v 0.9.0

* [Laatste wijziging] De SDK-afhankelijkheid is gedowngraded naar CUDA 10,0 (van CUDA 10,1). ONNX-runtime officieel ondersteunt alleen Maxi maal CUDA 10,0.
* [Laatste wijziging] Wordt overgeschakeld naar ONNX runtime in plaats van tensor flow runtime. Hiermee worden de eerste start tijd en het geheugen gebruik van het frame verminderd. Het vermindert ook de binaire grootte van de SDK.
* [API-wijziging] De naam is gewijzigd `k4abt_tracker_queue_capture()` in `k4abt_tracker_enqueue_capture()`
* [API-wijziging] `k4abt_frame_get_body()` In twee afzonderlijke functies afgebroken `k4abt_frame_get_body_skeleton()` : en `k4abt_frame_get_body_id()` . U kunt nu een query uitvoeren op de code van de hoofd tekst zonder dat u altijd de volledige skelet structuur hoeft te kopiëren.
* [API-wijziging]  `k4abt_frame_get_timestamp_usec()` De functie is toegevoegd om de stappen voor de gebruikers om de tijds tempel van het hoofd frame te controleren.
* De nauw keurigheid van het bijhouden van de hoofd tekst is verbeterd en de betrouw baarheid bijhouden

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Kinect DK](about-azure-kinect-dk.md)

- [Azure Kinect DK installeren](set-up-azure-kinect-dk.md)

- [Bijhouden van de hoofd tekst van Azure Kinect instellen](body-sdk-setup.md)
