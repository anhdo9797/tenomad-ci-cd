# [TENOMAD](https://tenomad.com/)

## HOW TO BUILD FLUTTER WITH GITHUB ACTIONS & JENKINS

#### Code base: [flutter_getx_boilerplate](https://github.com/KevinZhang19870314/flutter_getx_boilerplate)

### Setting Fastlane

#### Step 1: Init fastlane
Navigate your terminal to your project's directory and run
```
fastlane init
```
#### Step 2: Edit Appfile
##### iOS
Example code
```
# The bundle id
app_identifier "<your bundle>"

# The apple id 
apple_id "<email apple>"

team_id "<your team_id>"

itc_team_id "<your itc_team_id>" 

```

- `apple_id`: The email 
- `app_identifier`: App Identifier (Bundle ID, e.g. com.krausefx.app)
- `team_id`: [Login developer apple](https://developer.apple.com), select `Account`, select `Membership` and get the feild `Team Id`
- `itc_team_id`: The ID of your App Store Connect team if you're in multiple teams. [Get now](https://stackoverflow.com/a/58424010/14539746)

#### Step 3: Create lane
In the `fastfile` ios create new lane `build_local` flow code
````
lane :build_local do | options |
    increment_build_number() 

    build_app(
      configuration: "Release",
      scheme: "Runner",
      workspace: "Runner.xcworkspace",
      export_method: "app-store",
    )
    if options[:system] == 'firebase' 
      firebase_app_distribution(
        app: "<your app>",
        groups: "<your group>",
        release_notes_file: "../release-notes.txt"
      )
      else upload_to_testflight(skip_waiting_for_build_processing: true)
    end
  end
````
This lane has functions:
- Increment the build number of your project: [increment_build_number](https://docs.fastlane.tools/actions/increment_build_number/)
- Build ipa: [build_app](http://docs.fastlane.tools/actions/build_app/#build_app)
- Deploy to firebase([firebase_app_distribution](https://firebase.google.com/docs/app-distribution/ios/distribute-fastlane)) if if lane is passed parameter `"system":"firebase"` elese push to tesflight([upload_to_testflight](http://docs.fastlane.tools/actions/upload_to_testflight/#upload_to_testflight))

#### Step 4: Environment variables to set
- `FASTLANE_PASSWORD`: Your App Store Connect / Apple Developer Portal password, usually only needed if you also set the FASTLANE_USER variable
- `LANG` and `LC_ALL`: These set up the locale your shell and all the commands you execute run at. fastlane needs these to be set to an UTF-8 locale to work correctly, for example `en_US.UTF-8`.
- `FASTLANE_SESSION`: run in terminal `fastlane spaceauth -u user@email.com`
- `FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD`: If you have 2-factor enabled and use pilot or deliver to upload a binary to App Store Connect. [Get value](https://medium.com/@derek_39555/tip-setting-up-fastlane-deployment-script-with-2-factor-authentication-for-fastlanes-spaceship-53457c871fa2)

#### Step 5: Done
##### Navigate your terminal to your project's directory and run
- run in terminal: `fastlane build_local "system":"firebase"` and test in Firebase App Distribute.
- run in terminal: `fastlane build_local "system":"testflight"` and test in TestFlight.

### Build with Jenkins
### Build with Github Actions


