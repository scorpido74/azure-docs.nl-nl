---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060411"
---
### <a name="create-the-react-native-solution"></a>De React Native-oplossing maken

1. Werk in `Terminal` uw omgevingshulpprogramma's bij, die vereist zijn voor het werken met React Native met de volgende opdrachten:

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. Voer in `Terminal` de volgende opdracht uit als u `React Native` CLI hebt geïnstalleerd om de installatie ervan ongedaan te maken. Gebruik `npx` om automatisch toegang te krijgen tot de meest recente React Native CLI-versie die beschikbaar is:

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > React Native heeft een ingebouwde interface voor opdrachtregels. In plaats van een specifieke versie van de CLI wereldwijd te installeren en te beheren, raden we u aan om toegang te krijgen tot de huidige versie tijdens runtime met behulp van `npx`, die wordt geleverd met Node.js. Met `npx react-native <command>` wordt de huidige stabiele versie van de CLI gedownload en uitgevoerd op het moment dat de opdracht wordt uitgevoerd.

1. Navigeer naar de map projecten waarin u de nieuwe toepassing wilt maken. Gebruik de op Typescript gebaseerde sjabloon door de parameter `--template` op te geven:

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. Voer een metroserver uit, waarmee JavaScript wordt gebundeld en de code-updates voor het vernieuwen van de bundels in realtime worden bewaakt:

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. Voer de iOS-app uit om de installatie te controleren. Zorg ervoor dat u een iOS-Simulator hebt gestart of een iOS-apparaat hebt aangesloten voordat u de volgende opdracht uitvoert:

    ```bash
    npx react-native run-ios
    ```

1. Voer de Android-app uit om de installatie te controleren. Er zijn enkele extra stappen nodig om een Android-emulator of- apparaat te configureren voor toegang tot de React Native-metroserver. Met de volgende opdrachten wordt de eerste JavaScript-bundel voor Android gegenereerd en in de map Assets geplaatst.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    Dit script wordt vooraf geïmplementeerd met de eerste versie van de app. Na de implementatie configureert u de emulator of het apparaat voor toegang tot de metroserver door het IP-adres van de server op te geven. Voer de volgende opdracht uit om de Android-toepassing te bouwen en uit te voeren:

    ```bash
    npx react-native run-android
    ```

    Klik in de app op `CMD+M` (emulator) of schud het apparaat om de instellingen voor ontwikkelaars te vullen, navigeer naar `Settings` > `Change Bundle Location` en geef het IP-adres van de metroserver op met de standaardpoort: `<metro-server-ip-address>:8081`.

1. Pas in het bestand `App.tsx` een wijziging toe op de pagina-indeling, sla deze op en breng de wijziging automatisch aan in de iOS- en Android-apps.

    > [!NOTE]
    > Gedetailleerde installatiegids voor ontwikkelomgeving is beschikbaar in de [officiële documentatie](https://reactnative.dev/docs/environment-setup)

### <a name="install-required-packages"></a>De vereiste pakketten installeren

U hebt de volgende drie pakketten nodig om dit voorbeeld te kunnen gebruiken:

1. [React Native-pushmeldingen voor iOS-](https://www.npmjs.com/package/@react-native-community/push-notification-ios) - [Project GitHub](https://github.com/react-native-community/push-notification-ios)

    Dit pakket is gemaakt toen de PushNotificationIOS werd gesplitst van de kern van React Native. Het pakket implementeert pushmeldingen voor iOS en biedt een React Native-interface om toegang te krijgen. Voer de volgende opdracht uit om het pakket te installeren:

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [React Native-pushmeldingen op verschillende platforms](https://www.npmjs.com/package/react-native-push-notification)

    Met dit pakket worden lokale en externe meldingen op iOS en Android geïmplementeerd op verschillende platformen. Voer de volgende opdracht uit om het pakket te installeren:

    ```bash
    yarn add react-native-push-notification
    ```

1. [Apparaatgegevenspakket](https://www.npmjs.com/package/react-native-device-info) Het pakket bevat informatie over een apparaat in runtime. Gebruik het om een apparaat-id te definiëren, die wordt gebruikt om te registreren voor pushmeldingen. Voer de volgende opdracht uit om het pakket te installeren:

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>De platformoverschrijdende onderdelen implementeren

1. `DemoNotificationHandler` maken en implementeren:

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. `DemoNotificationService` maken en implementeren:

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. `DemoNotificationRegistrationService` maken en implementeren:

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. De app configureren. Open `package.json` en voeg de volgende scriptdefinitie toe:

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    Voer dit script uit, waardoor de standaardconfiguratie wordt gekopieerd naar de map `config`.

    ```bash
    yarn configure
    ```

    De laatste stap is het bijwerken van het configuratiebestand dat in de vorige stap is gekopieerd met de API-toegangsgegevens. Geef de parameters `apiKey` en `apiUrl` op:

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>De platformoverschrijdende UI implementeren

1. Pagina-indeling definiëren

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. Stijlen toepassen

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. Het pagina-onderdeel initialiseren

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. Knop op handlers definiëren

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. Door handler ontvangen tokenregistraties en pushmeldingen

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```