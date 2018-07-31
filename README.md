# cordova-plugin-document-scanner

This plugin defines a global `scan` object, which provides an API for scan the document from taking pictures and choosing image from the system's library. 

Although the object is attached to the global scoped `window`, it is not available until after the `deviceready` event.

    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
        console.log(scan);
    }

## Installation


This requires cordova 7.1.0+
npm link :- https://www.npmjs.com/package/cordova-plugin-document-scanner

    cordova plugin add cordova-plugin-document-scanner
    
*Please read issues and fixes section of readme for Ionic installation and Android Oreo support*    

### scan.scanDoc(sourceType, successCallback, errorCallback)
Takes a photo using the scan, or retrieves a photo from the device's
image gallery.  The image is passed to the document scanner and the scanned image passed to success callback as the URI for the image file.

The `scan.scanDoc` function opens the device's default scan
application that allows users to snap pictures by default - this behavior occurs,
when `sourceType` equals `1`.
Once the user snaps the photo, the scan application closes and the application is restored.

If `sourceType` is `0`, then a dialog displays
that allows users to select an existing image.

The return value is sent to the [`scanSuccess`](#module_scan.onSuccess) callback function, in
the fileUri formats.

You can do whatever you want with the URI, for
example:

- Render the image in an `<img>` tag.

__Supported Platforms__

- Android (For Android Oreo support please read Issues and Fixes section)
- iOS

**Example**  
```js
scan.scanDoc(sourceType, scanSuccess, scanError);
```

## `scan.scanDoc`

Take a photo and retrieve the image's file location:

    scan.scanDoc(0, onSuccess, onFail);

    function onSuccess(imageURI) {
        var image = document.getElementById('myImage');
        image.src = imageURI;
    }

    function onFail(message) {
        alert('Failed because: ' + message);
    }

## iOS Quirks

NOTE :- iOS has only document scan via camera for now (Any argument passed will start the camera scan). Document Scan from gallery will be available in future version.

An example file URI obtained from success call back of scanDoc function looks like this  file:///var/mobile/Containers/Data/Application/8376778A-983B-4FBA-B21C-A4CFDD047AAA/Documents/image.png

## Issues and Fixes

- Error:Execution failed for task ':app:transformNativeLibsWithStripDebugSymbolForDebug' <br/>
    Delete local ndk-bundle folder. Example location :- C:\Users\Administrator\AppData\Local\Android\sdk\ndk-bundle
    
- CropViewController fails in Xcode due to Incompatible Swift Versions <br/>
    Refer issue [13](https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner/issues/13)
    
- Couldn't find "libopencv_java3.so" [Problem mainly with 64 bit build devices]<br/>
    Refer issue [8](https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner/issues/8)
    
- iOS scan UI buttons documentation <br/>
    Refer issue [15](https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner/issues/15)

- Adding plugin in Ionic <br/> 
    Refer 6th response in issue [17](https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner/issues/17)

- Android Oreo Support / Failure delivering result ResultInfo <br/>
    Refer issue [19](https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner/issues/19) & [17](https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner/issues/17) <br/>
    
## Credits / Native library links

Android :- https://github.com/jhansireddy/AndroidScannerDemo <br/>
iOS :- https://github.com/charlymr/IRLDocumentScanner

Huge thanks to these authors for making their document scanning native libraries public.

## More about us!

Find out more or contact us directly here :- http://www.neutrinos.co/

Facebook :- https://www.facebook.com/Neutrinos.co/ <br/>
LinkedIn :- https://www.linkedin.com/company/25057297/ <br/>
Twitter :- https://twitter.com/Neutrinosco <br/>
Instagram :- https://www.instagram.com/neutrinos.co/


## For Integrating with JobProgress and Having Successfull AppStore Build :-
We faced issue while validating Appstore Build <br/>
Issue with plugin - https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner/issues/18

Steps to Solve - 
1. Visit - http://ikennd.ac/blog/2015/02/stripping-unwanted-architectures-from-dynamic-libraries-in-xcode/ <br/>
 OR
2. Copy 
```
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"

# This script loops through the frameworks embedded in the application and
# removes unused architectures.
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
    FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
    FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
    echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"

    EXTRACTED_ARCHS=()

    for ARCH in $ARCHS
    do
        echo "Extracting $ARCH from $FRAMEWORK_EXECUTABLE_NAME"
        lipo -extract "$ARCH" "$FRAMEWORK_EXECUTABLE_PATH" -o "$FRAMEWORK_EXECUTABLE_PATH-$ARCH"
        EXTRACTED_ARCHS+=("$FRAMEWORK_EXECUTABLE_PATH-$ARCH")
    done

    echo "Merging extracted architectures: ${ARCHS}"
    lipo -o "$FRAMEWORK_EXECUTABLE_PATH-merged" -create "${EXTRACTED_ARCHS[@]}"
    rm "${EXTRACTED_ARCHS[@]}"

    echo "Replacing original executable with thinned version"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_EXECUTABLE_PATH-merged" "$FRAMEWORK_EXECUTABLE_PATH"

done

```
and paste in Run Script section of Build Phases in Xcode with shell - `/bin/sh`.

### StackOverflow References-
1. https://stackoverflow.com/questions/44701586/submit-to-app-store-issues-unsupported-architecture-x86-64-i386-ibmmobilefirst
2. https://stackoverflow.com/questions/42641806/check-and-remove-unsupported-architecture-x86-64-i386-in-ipa-archive
