---
title: 'Quickstart: Een Azure Kinect-toepassing voor het volgen van een lichaam bouwen'
description: Stapsgewijze instructies voor het bouwen van uw eerste Azure Kinect-toepassing voor het volgen van lichamen
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, sensor, sdk, body, tracking, joint, application, first
ms.openlocfilehash: bdf8ee7a14bf59a151dfa316b11159830b4f63b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277798"
---
# <a name="quickstart-build-an-azure-kinect-body-tracking-application"></a>Quickstart: Een Azure Kinect-toepassing voor het volgen van een lichaam bouwen

Gaat u aan de slag met de Body Tracking-SDK? In deze quickstart leert u hoe het volgen van lichamen werkt. Meer voorbeelden vindt u in de [opslagplaats Azure-Kinect-Sample](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="prerequisites"></a>Vereisten

- [Azure Kinect DK installeren](set-up-azure-kinect-dk.md)
- [De Body Tracking-SDK instellen](body-sdk-setup.md)
- De quickstart [Uw eerste Azure Kinect-toepassing bouwen](build-first-app.md) doornemen.
- Zorg ervoor dat u bekend bent met de volgende sensor-SDK-functies:
  - [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
  - [k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
  - [k4a_device_stop_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  - [k4a_device_close()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)
- Raadpleeg de documentatie voor de volgende Body Tracking-SDK-functies:
  - [k4abt_tracker_create()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1aa71481b8441def94de11b29e0e3cbc.html#ga1aa71481b8441def94de11b29e0e3cbc)
  - [k4abt_tracker_enqueue_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)
  - [k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)
  - [k4abt_tracker_shutdown()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga94036969ef94cbc414c78b3f6d04bfa5.html#ga94036969ef94cbc414c78b3f6d04bfa5)
  - [k4abt_tracker_destroy()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gab2c9afca092130976cd66077c0557ed1.html#gab2c9afca092130976cd66077c0557ed1)

## <a name="headers"></a>Headers

Bij het volgen van lichamen wordt één header gebruikt: `k4abt.h`. Gebruik deze header naast `k4a.h`. Zorg ervoor dat de gewenste compiler is ingesteld voor de mappen `lib` en `include` van de Sensor- en de Body Tracking-SDK. U moet ook een koppeling maken naar de bestanden `k4a.lib` en `k4abt.lib`. Voor het uitvoeren van de toepassing zijn `k4a.dll`, `k4abt.dll`, `onnxruntime.dll`en `dnn_model.onnx` nodig in het uitvoerpad van de toepassing.

```C
#include <k4a/k4a.h>
#include <k4abt.h>
```

## <a name="open-device-and-start-the-camera"></a>Het apparaat openen en de camera starten

In uw eerste toepassing voor het volgen van lichamen wordt ervan uitgegaan dat er één Azure Kinect-apparaat is verbonden met de pc.

Bij het volgen van lichamen wordt gebruikgemaakt van de Sensor-SDK. Als u lichamen wilt volgen, moet u eerst het apparaat openen en configureren. Gebruik de functie [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) om het apparaat te openen. Configureer het vervolgens met het object [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html). Voor de beste resultaten stelt u de dieptemodus in op `K4A_DEPTH_MODE_NFOV_UNBINNED` of `K4A_DEPTH_MODE_WFOV_2X2BINNED`. De lichaamstracker kan niet worden gebruikt als de dieptemodus is ingesteld op `K4A_DEPTH_MODE_OFF` of `K4A_DEPTH_MODE_PASSIVE_IR`.

U vindt meer informatie over het zoeken en openen van het apparaat op [deze pagina](find-then-open-device.md).

U vindt meer informatie over de Azure Kinect-dieptemodi op deze pagina's: [hardwarespecificaties](hardware-specification.md) en [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d)-opsommingen.

```C
k4a_device_t device = NULL;
k4a_device_open(0, &device);

// Start camera. Make sure depth camera is enabled.
k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
k4a_device_start_cameras(device, &deviceConfig);
```

## <a name="create-the-tracker"></a>De tracker maken

Als u lichamen wilt volgen, moet u om te beginnen een lichaamstracker maken. De structuur [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) is nodig voor sensorkalibratie. De kalibratie van de sensor kan worden opgevraagd met behulp van de functie [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78).

```C
k4a_calibration_t sensor_calibration;
k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration);

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker);
```

## <a name="get-captures-from-the-azure-kinect-device"></a>Opnamen ophalen van het Azure Kinect-apparaat

U kunt meer informatie over het ophalen van beeldgegevens vinden op [deze pagina](retrieve-images.md).

```C
// Capture a depth frame
k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS);
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>De opname in de wachtrij plaatsen en de resultaten oproepen

De tracker beheert intern een invoerwachtrij en een uitvoerwachtrij om de Azure Kinect DK-opnamen efficiënter asynchroon te verwerken. De volgende stap is het gebruik van de functie `k4abt_tracker_enqueue_capture()` om een nieuwe opname toe te voegen aan de invoerwachtrij. Gebruik de functie `k4abt_tracker_pop_result()` om een resultaat op te roepen uit de uitvoerwachtrij. De time-outwaarde is afhankelijk van de toepassing en bepaalt de wachttijd voor de wachtrij.

In uw eerste toepassing voor het volgen van lichamen wordt gebruikgemaakt van het patroon voor realtimeverwerking. Raadpleeg [Resultaten voor het volgen van een lichaam ophalen](get-body-tracking-results.md) voor een gedetailleerde uitleg van de andere patronen.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

## <a name="access-the-body-tracking-result-data"></a>De gegevens over het volgen van lichamen bekijken

De resultaten van het volgen van lichamen worden voor elke sensor opgeslagen in een lichaamskader ([k4abt_frame_t](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/structk4abt__frame__t.html)-structuur). Elk lichaamskader bevat drie belangrijke onderdelen: een verzameling van lichaamsstructuren, een lichaamsindex in 2D en de vastgelegde invoer.

Uw eerste toepassing voor het volgen van lichamen heeft alleen toegang tot het aantal gedetecteerde lichamen. Raadpleeg [Gegevens in kader openen](access-data-body-frame.md) voor een gedetailleerde uitleg bij de gegevens in een lichaamskader.

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
printf("%zu bodies are detected!\n", num_bodies);
```

## <a name="clean-up"></a>Opruimen

De laatste stap bestaat uit het afsluiten van de lichaamstracker en het vrijgeven van het object voor lichaamstracering. U moet ook het apparaat stoppen en sluiten.

```C
k4abt_tracker_shutdown(tracker);
k4abt_tracker_destroy(tracker);
k4a_device_stop_cameras(device);
k4a_device_close(device);
```

## <a name="full-source"></a>Volledige bron

```C
#include <stdio.h>
#include <stdlib.h>

#include <k4a/k4a.h>
#include <k4abt.h>

#define VERIFY(result, error)                                                                            \
    if(result != K4A_RESULT_SUCCEEDED)                                                                   \
    {                                                                                                    \
        printf("%s \n - (File: %s, Function: %s, Line: %d)\n", error, __FILE__, __FUNCTION__, __LINE__); \
        exit(1);                                                                                         \
    }                                                                                                    \

int main()
{
    k4a_device_t device = NULL;
    VERIFY(k4a_device_open(0, &device), "Open K4A Device failed!");

    // Start camera. Make sure depth camera is enabled.
    k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
    deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
    VERIFY(k4a_device_start_cameras(device, &deviceConfig), "Start K4A cameras failed!");

    k4a_calibration_t sensor_calibration;
    VERIFY(k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration),
        "Get depth camera calibration failed!");

    k4abt_tracker_t tracker = NULL;
    k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
    VERIFY(k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker), "Body tracker initialization failed!");

    int frame_count = 0;
    do
    {
        k4a_capture_t sensor_capture;
        k4a_wait_result_t get_capture_result = k4a_device_get_capture(device, &sensor_capture, K4A_WAIT_INFINITE);
        if (get_capture_result == K4A_WAIT_RESULT_SUCCEEDED)
        {
            frame_count++;
            k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
            k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
            if (queue_capture_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                // It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Add capture to tracker process queue timeout!\n");
                break;
            }
            else if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
            {
                printf("Error! Add capture to tracker process queue failed!\n");
                break;
            }

            k4abt_frame_t body_frame = NULL;
            k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
            if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
            {
                // Successfully popped the body tracking result. Start your processing

                size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
                printf("%zu bodies are detected!\n", num_bodies);

                k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
            }
            else if (pop_frame_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                //  It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Pop body frame result timeout!\n");
                break;
            }
            else
            {
                printf("Pop body frame result failed!\n");
                break;
            }
        }
        else if (get_capture_result == K4A_WAIT_RESULT_TIMEOUT)
        {
            // It should never hit time out when K4A_WAIT_INFINITE is set.
            printf("Error! Get depth frame time out!\n");
            break;
        }
        else
        {
            printf("Get depth capture returned error: %d\n", get_capture_result);
            break;
        }

    } while (frame_count < 100);

    printf("Finished body tracking processing!\n");

    k4abt_tracker_shutdown(tracker);
    k4abt_tracker_destroy(tracker);
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Resultaten van het volgen van een lichaam ophalen](get-body-tracking-results.md)
