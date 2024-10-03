# Arbejde med Kamera i React Native ᕦ(⌐■ ͜ʖ■)ᕥ 📷

I denne guide skal vi arbejde med kameraet i en React Native-app. Vi bruger Expo's indbyggede `expo-camera` og `expo-image-picker` til at få adgang til kamera og billeder.

## Sådan starter du et nyt "blank" React Native projekt

1. Opret et nyt blankt projekt:
    ```bash
    npx create-expo-app CameraApp --template blank
    ```

2. Naviger ind i din projektmappe:
    ```bash
    cd CameraApp
    ```

3. Åbn din `package.json` fil, og erstat indholdet af "dependencies" sektionen med følgende:

    ```json
    "dependencies": {
      "@react-navigation/bottom-tabs": "^6.6.1",
      "@react-navigation/stack": "^6.4.1",
      "expo": "~51.0.28",
      "expo-camera": "~15.0.16",
      "expo-image-picker": "^15.0.7",
      "expo-status-bar": "~1.12.1",
      "react": "18.2.0",
      "react-native": "0.74.5",
      "react-native-vector-icons": "^10.2.0"
    }
    ```

4. Kør herefter følgende kommando for at installere alle nødvendige pakker:
    ```bash
    npm install
    ```

## app.json

Indsæt følgende i din app.json:

```json
"plugins": [
      [
        "expo-camera",
        {
          "cameraPermission": "Allow $(PRODUCT_NAME) to access your camera",
          "microphonePermission": "Allow $(PRODUCT_NAME) to access your microphone",
          "recordAudioAndroid": true
        }
      ]
    ]
```

Nu er du klar til at begynde at arbejde med kameraet i dit projekt! Start med at læse dokumentationen på følgende: 

> **Links til dokumentation:**
> - [Expo Camera](https://docs.expo.dev/versions/latest/sdk/camera/)
> - [Expo Image Picker](https://docs.expo.dev/versions/latest/sdk/imagepicker/)

<br>
<br>

# Oprettelse af skærme og navigation i React Native

Nu skal vi oprette to skærme, der tester navigationen i vores app. Dette gøres ved at oprette en mappe og tilføje to filer, hvor hver fil returnerer navnet på den respektive skærm.

## Trin 1: Opret en mappe og to filer

1. Opret en ny mappe i roden af dit projekt ved navn `screens`.
2. Inde i mappen `screens` skal du oprette to filer:

   - `CameraTest.js`
   - `ImageScreen.js`

3. I hver af disse filer kan du returnere deres skærmnavn for at teste navigationen:

    **CameraTest.js:**
    ```javascript
    import React from 'react';
    import { Text, View } from 'react-native';

    export default function CameraTest() {
      return (
        <View>
          <Text>Camera Test Screen</Text>
        </View>
      );
    }
    ```

    **ImageScreen.js:**
    ```javascript
    import React from 'react';
    import { Text, View } from 'react-native';

    export default function ImageScreen() {
      return (
        <View>
          <Text>Image Screen</Text>
        </View>
      );
    }
    ```

## Trin 2: Opret en Stack Navigator i App.js

1. Åbn din `App.js`-fil og opdater den til at inkludere en Stack Navigator, som navigerer mellem de to skærme. Din `App.js` skal se sådan ud:

    ```javascript
    import React from 'react';
    import { NavigationContainer } from '@react-navigation/native';
    import { createStackNavigator } from '@react-navigation/stack';
    import CameraTest from './screens/CameraTest';
    import ImageScreen from './screens/ImageScreen';

    export default function App() {
      const Stack = createStackNavigator();
      return (
        <NavigationContainer>
          <Stack.Navigator>
            <Stack.Screen name={'home'} component={???} options={{headerShown: false}} />
            <Stack.Screen name={'image'} component={???} />
          </Stack.Navigator>
        </NavigationContainer>
      );
    }
    ```

## Test af navigationen

Start din app med:
```bash
npx expo start
```

Hvis din navigation ikke virker, så sikre dig at dine imports er rigtige og at du ikke har kopirert dem direkte fra vores kode. <B>Imports er altid bedst at gøre manuelt og uden Copilot!!!</B> 

<br>
<br>

# CameraTest.js

Du vil nu oprette et kamera, tage billeder og vise dem i et simpelt galleri.

## Trin 1: Importer nødvendige moduler

Først og fremmest skal du importere de nødvendige moduler. Sørg for at have følgende i toppen af din `CameraTest.js`-fil:

```javascript
// Vi bruger Legacy Camera API, da den ny Camera API kun virker med TypeScript
import { Camera, CameraType } from 'expo-camera/legacy';
import { useState, useRef } from 'react';
import { StatusBar } from 'expo-status-bar';
import { Button, Text, TouchableOpacity, View, ScrollView, Image, SafeAreaView } from 'react-native';
import Ionicons from 'react-native-vector-icons/Ionicons';
```

Herefter sæt din export function således: 
```javaScript
export default function CameraTest({ navigation }) {
``` 

## Trin 2: Insæt vores styling:

Indsæt følgende styling nederest på din `CameraTest.js`.
Vi kommer til at bruge denne styling løbende så være obs på det.

```javascript
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    width: '100%',
    marginTop: 0,
    borderRadius: 20,
    backgroundColor: 'black',
    overflow: 'hidden',
    
  },
    camera: {
    flex: 1,
    overflow: 'hidden',
    width: '100%',
    flexDirection: 'column',
    justifyContent: 'flex-end',
  },
  buttonContainer: {
    flexDirection: 'row',
    backgroundColor: 'transparent',
    margin: 32,
    alignSelf: 'center',
  },
  
  text: {
    fontSize: 16,
    fontWeight: 'semibold',
    color: 'white',
    alignSelf: 'center',
  },
  buttonGallery: {
    fontSize: 15,
    color:"white",
    padding: 10,
    borderRadius:10,
    alignSelf: 'center',
    },
    gallery:{
        flex: 0.2,
        paddingTop:10,
        width:"100%",
        alignItems: 'center',
        justifyContent: 'center',
        backgroundColor: 'black',
    },
    safeview:{
        backgroundColor: 'black',
        flex: 1,
        justifyContent: 'center',
        width: '100%',
    },
    snapbtn:{
        backgroundColor: 'rgba(255, 255, 255, 0.25)',
        height: 80,
        width: 80,
        borderRadius: 100,
        padding: 10,
        margin: 5,
        alignSelf: 'center',
        borderWidth: 4,
        borderColor: 'white',
        justifyContent: 'center',
    },
    flipbtn: {
        backgroundColor: 'rgba(255, 255, 255, 0.25)',
        borderRadius: 100,
        
        padding: 5,
        alignSelf: 'baseline',
        justifyContent: 'center',
    },
    gallerybtn: {
        backgroundColor: 'rgba(255, 255, 255, 0.25)',
        borderRadius: 100,
        padding: 5,
        alignSelf: 'flex-end',
        justifyContent: 'center',
    },
});
```

## Permission

For at give din app adgang til din telefons kamera skal vi bruge nogle permissions. Start med at oprette en `const` for permission

```javaScript
const [permission, requestPermission] = Camera.useCameraPermissions();
``` 

Lav nu 2 if statements der checker efter permission.

Hvis `!permission` - alså permissions er ved at load, så return et tomt `<View>`. 

Hvis `!permission.granted` - permissions ikke er granted, så skal vi returnere et `<View>` hvori vi har en knap der beder om `requestPermission`.

Hint:
```javaScript
return (
      <View style={styles.container}>
        <Text style={{ textAlign: 'center' }}>We need your permission to show the camera</Text>
        <???? onPress={????} title="grant permission" />
      </View>
    );
```

## cameraRef snapshot

Du skal nu implementere en funktion, der tager et billede (snapshot) med kameraet og gemmer det i en billedliste.

Lav endnu en useState const (oppe sammen med de forrige) hvori du skriver følgende:

```javaScript
const [loading,setLoading] = useState(false);
// Camera reference
const cameraRef = useRef();
```

### Trin 1: Tjek om kameraet er klar

Først skal du sikre dig, at der er en reference til kameraet (`cameraRef`), før du forsøger at tage et billede. Hvis der ikke er en reference, skal vi stoppe funktionen og vise en besked i konsollen.

```javascript
if (!????.current) {
    console.log("No camera ref");
    return; // Stop funktionen, hvis der ikke er et kamera tilgængeligt
}
```


### Trin 2: Opret snapshot-funktionen

Nu skal du oprette funktionen `snap`, som vil tage et billede og gemme det i en liste over billeder. Vi bruger `takePictureAsync()` for at tage billedet. Husk at færdigører logikken i din `setLoading`.

```javascript
// Making a snapshot
    const snap = async () => {
        if (!cameraRef.current) {
            console.log("No camera ref");
            
            return;
        }
        setLoading(???);
        const result = await cameraRef.current.takePictureAsync();
        setImagesArr([...imagesArr,result]);
        setLoading(???);
    }
```
 **Links til dokumentation:** - [Expo Camera takePictureAsync](https://docs.expo.dev/versions/latest/sdk/camera/#takepictureasyncoptions)

### Trin 3: toggleCameraType

Lav nu en `useState` `const` der sætter din `CameraType` til initally bruge dit `back` camera. Indsæt denne sammen med dine andre konstanter.

```javascript
const [type, setType] = useState(???.???);
```

Lav nu en function der hedder `toggleCameraType` som bruger `setType` til at skifte dit camera.

```javascript
  // Toggles the camera type
  function toggleCameraType() {
    setType(current => (??? === ???.back ? ???.front : ???.back));
  }
```

## Gallery-komponent i CameraTest.js

I denne del af guiden skal du implementere en galleri-komponent, der viser de billeder, du har taget med kameraet. Galleriet vil være rulleligt og vise en besked, hvis der endnu ikke er taget nogen billeder.

### Trin 1: lav din const

Insæt følgende som endnu en `const`: 
```javascript
const [imagesArr, setImagesArr] = useState([]);
```

### Trin 2: Opret en funktion for CameraGallery

Vi starter med at oprette `CameraGallery`-funktionen. Denne funktion returnerer en `View`, der indeholder de billeder, vi har taget, ved hjælp af en `ScrollView` til at gøre galleriet vandret rulleligt. Husk jeres styles!

```javascript
const CameraGallery = () => {
    return (
        <View style={styles.???}>
            <Text style={styles.???}>Billeder taget: {imagesArr.length}</Text>
            <???>

            </???>
        </View>
    );
};
```


### Trin 3: Visning af billeder

Inde i `ScrollView` bruger vi en simpel conditional rendering. Hvis der findes billeder i `imagesArr`, vil vi bruge `map()` til at vise hvert billede som en `Image`-komponent inde i en `TouchableOpacity`. Hvis der ikke er nogen billeder, vil der i stedet blive vist en besked, der siger "No images taken".

```javascript
<ScrollView>
    {
        imagesArr.length > 0
            ? imagesArr.map((image,index) => (
                <TouchableOpacity key={index} style={{paddingHorizontal:10}} onPress={() => navigation.navigate('image',{image:image.uri}) } >
                    <Image source={{ uri: image.uri }} style={{ width: 80, height: 80, borderRadius:10 }} />
                </TouchableOpacity>
            ))
            : <Text style={{color:"white"}}> No images taken </Text>
    }
</ScrollView>
``` 

Tilføje `horizontal={true}` til dit `ScrollView` så vi kan se billederne horizontalt i galleriet. 

### toggleGallery

Lav nu en constant kaldt `setGallery` og en function der skifter denne konstants `current` fra `false` til `true` og omvendt hver gang den kaldes. 

```javascript
const [gallery, setGallery] = useState(???);
```

```javascript
// Toggles the gallery
  function toggleGallery() {
    setGallery(??? => !???);
  }
```

## return

Indsæt til sidst denne return function og færdigør den både med logik og styling. 

```javascript
return (
    <SafeAreaView style={styles.safeview}>
    <View style={styles.container}>
      <Camera style={styles.???} type={type} ref={??? her vil vi gerne ref til kamera}>
        <View style={styles.buttonContainer}>
            <View style={{flex: 1, alignSelf: 'flex-end'}}> 
                <TouchableOpacity style={styles.???} onPress={??? her vil vi gerne skifte kamera}>
                    <Ionicons name = "camera-reverse-outline" size= {32} color = "#fff" />
                </TouchableOpacity>
            </View>
            <View style={{flex: 1, alignSelf: 'flex-end',}}> 
                <TouchableOpacity style={styles.???} onPress={??? her vil vi gerne tage et foto}>
                    <Text style={styles.text}>{loading ? "Loading...":""}</Text>
                </TouchableOpacity>
            </View>
            <View style={{flex: 1, alignSelf: 'flex-end',}}> 
                <TouchableOpacity style={styles.???} onPress={??? her vil vi gerne komme ind på gallery}>
                    <Ionicons name = "copy-outline" size= {32} color = "#fff" />
                </TouchableOpacity>
            </View>
        </View>
      </Camera>
      {
        gallery ? <??? /> : null
      }
        
    </View>
    <StatusBar style="light" />
    </SafeAreaView>
  );
```

Du skulle nu gerne være færdig med din `CameraTest.js`

# ImageScreen.js

Start med at opsætte en `const` i din function: 

```javascript
export default function ImageScreen({route}) {
    
        const [image,setImage] = useState('')
```

## useEffect

Lav nu en `useEffect` der peger på din image const.

```javascript
/* du sætter billedet fra dine parameter og i return fjerne du val*/
    useEffect(() => {
        setImage(route.params.???);

        return () => {
            setImage('')
        }
    },[]);
```
## return

I din return vil vi gerne returnere `Dimensions` på din skræm (`window`) så vi har et fuldscreen picture mens vi bruger kameraet. 

```javascript
return(
    <View>
        <Image
            source={{ uri: image ?  image : null }}
            width={???.get('???').width}
            height={???.get('???').height}
        />
    </View>
)
```

Din camera app skulle nu gerne være done!  ヾ(⌐■_■)ノ
