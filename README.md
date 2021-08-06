
# 7_camera_vejledning

## Slutresultat
https://user-images.githubusercontent.com/48329669/128479609-ad28c1e8-c726-4073-a961-e664b45155eb.mp4


## AppStruktur
1. Opret nu følgende 2 komponenter CameraScreen og ImageScreen med "Hint 1 skabelonen" .
   1. Husk også at komponentnavn skal være ens med filnavnet

## App.js
1. Start med at oprette et nyt projekt.
2. Installér følgende dependencies med expo install eller npm install;
npm install react-native-gesture-handler @react-native-community/masked-view react-native-reanimated react-native-safe-area-context react-native-screens @react-navigation/native @react-navigation/bottom-tabs @react-navigation/stack react-native-vector-icons expo-image-picker expo-camera expo-media-library --force
3. Opret en ny Stack navigator
4. Gå ned i return og lav en Stack navigator med 2 screen home og image. I kan evt fjerne header på home screen med headerShown false i options attributten
5. I skulle nu gerne se jeres home komponent

## CameraScreen.js
1. Opret en reference kaldt cameraRef. ( `const cameraRef = useRef(); `)
2. opret nu 4 states, kaldt hasPermission, imageArr,loading og type
3. Type skal have en initial state ``Camera.Constants.Type.back``, og imageArr skal være et tomt array og permission null
4. Lav nu en useEffect funktion ( hints ) og deri en async funktion.
   1. I denne async funktion lav et object const med status, som sættes lig med `await Camera.requestPermissionAsync()`
   2. Lav nu et if statement med hvis status ikke er granted, lav en alert som advarer der ikke er kamera
   3. Dernæst lav et if statement som sætter hvis ikke Platform.OS er web ( !== 'web')
   4. Lav ligesom Camera persmisson nu en const status object med `` await ImagePicker.requestMediaLibraryPermissionsAsync();`` og lav en allert hvis ikke er granted
   5. Lav du en set state med setHasPermission hvor du sætter status ligmed granted
5. Derefter gå ud af useEffect og lav derefter et if statement med hasPermission er lig med null returner et tomt View
6. Nu skal der laves et return statement hvis hasPermission er false som returnere et View med hhv en tekst og Button inden i
   1. Lav en styling til View med flex 1, så den sidder i midten
   2. I button skal du i din onPress attribut lave en funktion som kalder på ``Linking.openSettings()``
7. I return-statementet start med at lave en Fragment wrapper
   1. Deri vil vi øverst have et element kaldt ``<StatusBar>`` som du kan style efter behov ( se referencer )
   2. Lav derefter et View element som har stylingen flex:1
   3. I View elementet lav et ``<Camera> </Camera>`` element hvori attributterne type skal have type staten og ref skal have camera Ref ( se referencer )
   4. Nu burde du se kameraet på din telefon
8. Nu skal der oprettes TouchableOpacities som kan tage billede, gå til galleriet og vende kameraet. 
   1. I Camera elementet opret nu et View med en flex styling så den udfylder hele kameraet
   2. Deri opret tre touchableOpacities med tekst element som repræsentere hhv Flip, Tag billede og galleri
   3. I Flip TouchableOpacitien lav en funktion som skifter på Camera typen fra front til back, se evt referencer ( setType lav et inline if else statement som kigger på om det er front eller back )
   4. Test nu om du kan skifte imellem for og bag kameraet
   5. Dernæst gå ned i tag billede touchable opacitie og lav en onPress som kalder på en snap funktion, som du derefter oprettet
9. I snap funktionen skal du sørge for at funktionen er asynkron
   1. Først skal du tjekke om cameraRef.current er aktiv ``!cameraRef.current`` og hvis true returner ingenting
   2. Dernæst sæt loading til true
   3. Lav nu et kald `` await cameraRef.current.takePictureAsync()`` som sættes lig med const result
   4. Hvis du gerne vil have at når du tager billeder at den gemmer til din telefon kan du tilføje dette ``await MediaLibrary.saveToLibraryAsync(result.uri)``
   5. Dernæst skal du skal du skrive følgende for at tilføje til ImageArr ``setImagesArr((imagesArr) => [result].concat(imagesArr));``
      1. Det vi gør her er at vi laver en funktion som tager result og ligger det først i det eksisterende array vi har for billeder
   6. Herunder sæt nu loading til false
   7. Gå ned til knappen og lav et if statement for tekst elementet med loading, hvor true siger loading og false siger "tag billede "
   8. Test nu om dit kamera tager billeder ved at lave en console.log på result eller kig i dit galleri om du har taget et billede
10. Ved galleri knappen kald nu på en funktion kaldt pickImage og opret funktionen
    1. Funktionen skal være asynkron og tage udgangspunkt i referencen image picker i bunden
11. Husk styling til alle dine knapper evt se video øverst
12. Nu skal galleriet i image arrayet vises
    1. Opret en funktion kaldt CameraGallery som har et return
    2. I return opret nu en wrapper med View som har flex: 0.4 som styling og alignment center, padding og width på 100%
    3. I Viewet lav først et tekst element som siger antallet af billeder taget. `` imagesArr.length``
    4. Opret nu et ScrollView element som har horizontal attribute til true
    5. Dernæst lav et map funktion af imagesArr som loper igennem de billeder som er taget med TouchableOpacity som wrapper og et billedet i midten ( se evt hint)
    6. Opret også et if else som tjekker om images arr er større en 0
    7. Nu skal du kalde ``<CameraGallery/>`` i return funktionen.
    8. Tjek nu når du tager billeder at dine billeder bliver vist
    9. Hvis du har probler console.log all the way ;) 

## ImageScreen.js
1. Først opret state for image
2. lav en useEffect med en funktion som sætter route.params.image i setImage og husk at lave en return funktion for at nulstille image
3. i return funktionen lav et ``Image`` element som tage image med uri'en og null hvis der ikke er et billede. Og width + height skal bestemmes af enhedens height og width ( Dimensions.get('window'))
4. Se nu om du kan trykke på de taget billeder om de skifter view til ImagesScreen


## Ekstra opgave
1. Ekstra opgave kunne være at lave slet knapper til billederne du har taget


## Hints
### skabalon 1
``` 
const KomponentNavn = (props) => { 
    return (
        <Text>Det er mit KomponentNavn</Text>
    )
}

export default KomponentNavn; 
```
### useEffect 2
```
useEffect(() => {
        (async () => {
            
        })();
    }, []);
```

### hint 3
```
{
   imagesArr.length > 0
       ? imagesArr.map((image,index) => (
               <TouchableOpacity key={index} style={{paddingHorizontal:10}} onPress={() => navigation.navigate('image',{image:image.uri}) } >
                   <Image source={{ uri: image.uri }} style={{ width: 100, height: 200 }} />
               </TouchableOpacity>
           ))
       : <Text style={{color:"white"}}> No images taken </Text>
}

```


## Referencer
https://docs.expo.dev/versions/latest/sdk/camera/
https://docs.expo.dev/versions/latest/sdk/imagepicker/
https://reactnavigation.org/docs/stack-navigator/
https://reactnative.dev/docs/statusbar
