## Initialising

```
npx react-native init ReactNativeDebugAddressExemplar
cd ReactNativeDebugAddressExemplar

npm i @nick-bull/react-native-debug-address
npm i -D babel-plugin-transform-inline-environment-variables
```

Modify your babel configuration file to add the plugin:

```
// babel.config.js
module.exports = {
  plugins: [
    "transform-inline-environment-variables",
  ],
  presets: ['module:metro-react-native-babel-preset'],
};
```

## Usage

If you are simply setting the debug address via environment variables, then import the package only:

```
import '@nick-bull/react-native-debug-address';
```

In this exemplar app, `App.js` has been modified as follows:

```
- import React from 'react';
+ import React, {
+   useEffect,
+   useState,
+ } from 'react';
+
+ import {
+   getDebugAddress,
+   setDebugAddress
+ } from '@nick-bull/react-native-debug-address';

...

 const App: () => React$Node = () => {
+  const [address, setAddress] = useState('');
+
+  useEffect(() => {
+    onAddressChanged(({debugAddress}) => {
+      console.log(`onAddressChanged: ${debugAddress}`);
+      setAddress(debugAddress)
+    });
+    const getDebugAddressAsync = async () => setAddress(await getDebugAddress());
+    getDebugAddressAsync();
+  }, []);
return (
  <>      
    <StatusBar barStyle="dark-content" />
      <SafeAreaView>
        <ScrollView
          contentInsetAdjustmentBehavior="automatic"
          style={styles.scrollView}>
          <Header />
+          <Text style={{marginBottom: 12, textAlign: 'center', ...styles.sectionTitle}}>
+              Debug Address: {JSON.stringify(address)}
+          </Text>
```

## Running

Note that environment variables are pretty persistent in React Native, so the `--reset-cache` flag is needed.

```
DEBUG_HOST=127.0.0.1:7777 npx react-native start --reset-cache --port 7777
# Both of these are equivalent, as 127.0.0.1 is the default host
# DEBUG_PORT=7777 npx react-native start --reset-cache --port 7777

npx react-native run-android --port 7777
```

The following scripts are defined in `package.json`:

```
"start:port": "f() { DEBUG_PORT=$1 npx react-native start --reset-cache --port $1; }; f",
"start:host": "f() { DEBUG_HOST=$1 npx react-native start --reset-cache --port ${1##*:}; }; f",
"android": "npx react-native run-android --port",
```

So alternatively you can run the server and app respectively by:

```
npm run start:port 7777
npm run android 7777
```

The app will unset the address too, when the server has been closed
