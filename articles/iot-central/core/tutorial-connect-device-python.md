---
title: Zelfstudie - Een algemene Python-client-app verbinden met Azure IoT Central | Microsoft Documenten
description: In deze zelfstudie ziet u hoe u als apparaatontwikkelaar een apparaat met een Python-client-app verbinden met uw Azure IoT Central-toepassing. U maakt een apparaatsjabloon door een apparaatcapaciteitsmodel te importeren en weergaven toe te voegen waarmee u communiceren met een verbonden apparaat
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d6c44c81db78fa76eeaf4b7181cca34fb8e81523
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758184"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Zelfstudie: Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Dit artikel is van toepassing op oplossingsbouwers en apparaatontwikkelaars.*

In deze zelfstudie ziet u hoe u als apparaatontwikkelaar een Python-clienttoepassing verbinden met uw Azure IoT Central-toepassing. De Python-toepassing simuleert het gedrag van een milieusensorapparaat. U gebruikt een voorbeeldmodel voor _apparaatmogelijkheden_ om een _apparaatsjabloon_ te maken in IoT Central. U voegt weergaven toe aan de apparaatsjabloon om een operator in staat te stellen met een apparaat te communiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Importeer een apparaatcapaciteitsmodel om een apparaatsjabloon te maken.
> * Standaardweergaven en aangepaste weergaven toevoegen aan een apparaatsjabloon.
> * Publiceer een apparaatsjabloon en voeg een echt apparaat toe aan uw IoT Central-toepassing.
> * Maak en voer de Python-apparaatcode uit en zie deze verbinding maken met uw IoT Central-toepassing.
> * Bekijk de gesimuleerde telemetrie die vanaf het apparaat wordt verzonden.
> * Gebruik een weergave om apparaateigenschappen te beheren.
> * Synchrone en asynchrone opdrachten aanroepen om het apparaat te bedienen.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing die is gemaakt met de **sjabloon Aangepaste toepassings.** Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
* Een ontwikkelmachine met [Python](https://www.python.org/) versie 3.7 of hoger geïnstalleerd. U kunt `python3 --version` bij de opdrachtregel worden uitgevoerd om uw versie te controleren. Python is beschikbaar voor een breed scala aan besturingssystemen. De instructies in deze zelfstudie gaan ervan uit dat u de opdracht **python3** uitvoert op de opdrachtprompt van Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Een Python-toepassing maken

In de volgende stappen ziet u hoe u een Python-clienttoepassing maakt die verbinding maakt met het echte apparaat dat u aan de toepassing hebt toegevoegd. Deze Python-toepassing simuleert het gedrag van een echt apparaat.

1. Navigeer in de opdrachtregelomgeving `environmental-sensor` naar de map die u eerder hebt gemaakt.

1. Voer de volgende opdrachten uit om de vereiste bibliotheken te installeren:

    ```cmd
    pip install azure-iot-device
    ```

1. Maak een bestand met de `environmental-sensor` naam **environmental_sensor.py** in de map.

1. Voeg de `import` volgende instructies toe aan het begin van het **bestand environmental_sensor.py:**

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Voeg de volgende `main` asynchrone functie- en variabele declaratietoe aan het bestand toe:

    ```python
    async def main():
      # In a production environment, don't store
      # connection information in the code.
      provisioning_host = 'global.azure-devices-provisioning.net'
      id_scope = '{your Scope ID}'
      registration_id = '{your Device ID}'
      symmetric_key = '{your Primary Key}'

      delay = 2

      # All the remaining code is nested within this main function

    if __name__ == '__main__':
    asyncio.run(main())
    ```

    Werk de `{your Scope ID}`tijdelijke `{your Device ID}`aanduidingen en `{your Primary Key}` met de waarden waar u eerder een notitie van hebt gemaakt. In een echte toepassing, niet hard code deze informatie in de toepassing.

    Alle volgende functiedefinities en -code `main` zijn genest binnen de functie.

1. Voeg de volgende twee `main` functies in de functie toe om het apparaat te registreren en aan te sluiten op uw IoT Central-toepassing. Registratie maakt gebruik van de Azure Device Provisioning Service:

    ```python
      async def register_device():
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
          provisioning_host=provisioning_host,
          registration_id=registration_id,
          id_scope=id_scope,
          symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print(f'Registration result: {registration_result.status}')

        return registration_result

      async def connect_device():
        device_client = None
        try:
          registration_result = await register_device()
          if registration_result.status == 'assigned':
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
              symmetric_key=symmetric_key,
              hostname=registration_result.registration_state.assigned_hub,
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. Voeg de volgende `main` functie in de functie toe om telemetrie naar uw IoT Central-toepassing te verzenden:

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    De namen van de`temp` telemetrie-items (en) `humid`moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

1. Voeg de volgende `main` functies in de functie toe om opdrachten te verwerken die vanuit uw IoT Central-toepassing worden aangeroepen:

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

      async def turnon_command(request):
        print('Turning on the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      async def turnoff_command(request):
        print('Turning off the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      commands = {
        'blink': blink_command,
        'rundiagnostics': diagnostics_command,
        'turnon': turnon_command,
        'turnoff': turnoff_command,
      }

      # Define behavior for handling commands
      async def command_listener():
        while True:
          method_request = await device_client.receive_method_request()  # Wait for commands
          await commands[method_request.name](method_request)
    ```

    De namen van de`blink` `turnon`opdrachten `turnoff`( `rundiagnostics`, , en ) moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

    Momenteel maakt IoT Central geen gebruik van het responsschema dat is gedefinieerd in het apparaatcapaciteitsmodel. Voor een synchrone opdracht kan de responspayload elke geldige JSON zijn. Voor een asynchrone opdracht moet het apparaat onmiddellijk een 202-antwoord retourneren, gevolgd door gerapporteerde eigenschapsupdate wanneer het werk is voltooid. De indeling van de gerapporteerde eigenschapsupdate is:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Een operator kan de reactiepayload in de opdrachtgeschiedenis bekijken.

1. Voeg de volgende `main` functies in de functie toe om eigenschapsupdates te verwerken die vanuit uw IoT Central-toepassing worden verzonden:

    ```python
        async def name_setting(value, version):
          await asyncio.sleep(1)
          print(f'Setting name value {value} - {version}')
          await device_client.patch_twin_reported_properties({'name' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        async def brightness_setting(value, version):
          await asyncio.sleep(5)
          print(f'Setting brightness value {value} - {version}')
          await device_client.patch_twin_reported_properties({'brightness' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        settings = {
          'name': name_setting,
          'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
          while True:
            patch = await device_client.receive_twin_desired_properties_patch() # blocking
            to_update = patch.keys() & settings.keys()
            await asyncio.gather(
              *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
            )
    ```

    Wanneer de operator een schrijfbare eigenschap instelt in de IoT Central-toepassing, gebruikt de toepassing een gewenste eigenschap van apparaattwee om de waarde naar het apparaat te verzenden. Het apparaat reageert vervolgens met behulp van een apparaat twin gemeld eigenschap. Wanneer IoT Central de gerapporteerde eigenschapswaarde ontvangt, wordt de eigenschapsweergave bijgewerkt met een status van **gesynchroniseerd**.

    De namen van`name` de `brightness`eigenschappen (en) moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

1. Voeg de volgende `main` functies in de functie toe om de toepassing te beheren:

    ```python
      # Define behavior for halting the application
      def stdin_listener():
        while True:
          selection = input('Press Q to quit\n')
          if selection == 'Q' or selection == 'q':
            print('Quitting...')
            break

      device_client = await connect_device()

      if device_client is not None and device_client.connected:
        print('Send reported properties on startup')
        await device_client.patch_twin_reported_properties({'state': 'true'})
        tasks = asyncio.gather(
          send_telemetry(),
          command_listener(),
          twin_patch_listener(),
        )

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)

        # Wait for user to indicate they are done listening for method calls
        await user_finished

        # Cancel tasks
        tasks.add_done_callback(lambda r: r.exception())
        tasks.cancel()
        await device_client.disconnect()

      else:
        print('Device could not connect')
    ```

1. Sla het **environmental_sensor.py-bestand op.**

## <a name="run-your-python-application"></a>Uw Python-toepassing uitvoeren

Als u de apparaatclienttoepassing wilt starten, voert u de volgende opdracht uit in de opdrachtregelomgeving:

```cmd
python3 environmental_sensor.py
```

U zien dat het apparaat verbinding maakt met uw Azure IoT Central-toepassing en telemetrie gaat verzenden:

![De clienttoepassing uitvoeren](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

U zien hoe het apparaat reageert op opdrachten en eigenschapsupdates:

![De clienttoepassing observeren](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>Volgende stappen

Als apparaatontwikkelaar, nu u de basisprincipes hebt geleerd van het maken van een apparaat met Behulp van Node.js, zijn enkele voorgestelde volgende stappen:

- Meer informatie over het verbinden van een echt apparaat met IoT Central in het [Connect a MXChip IoT DevKit-apparaat met uw azure IoT Central-toepassingshow-to-artikel.](./howto-connect-devkit.md)
- Lees [Verbinding maken met Azure IoT Central](./concepts-get-connected.md) voor meer informatie over het registreren van apparaten met IoT Central en hoe IoT Central apparaatverbindingen beveiligt.

Als u liever door de set iot central-zelf-zelfstudies wilt gaan en meer te weten wilt komen over het bouwen van een IoT Central-oplossing, raadpleegt u:

> [!div class="nextstepaction"]
> [Een gateway-apparaatsjabloon maken](./tutorial-define-gateway-device-type.md)