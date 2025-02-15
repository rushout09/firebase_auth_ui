# firebase_auth_ui

Flutter plugin of Firebase UI which allows to add login/sign-up quickly.

**NOTE:** This plugin is under development. Please provide [Feedback](https://github.com/sam0829/firebase_auth_ui/issues) and [Pull Requests](https://github.com/sam0829/firebase_auth_ui/pulls) in order to have your feature(s) integrated.

<img src="https://github.com/sam0829/firebase_auth_ui/blob/master/assets/preview.jpg" height="400" alt="Screenshot"/> 

## Usage
#### Import
```
import 'package:firebase_auth_ui/firebase_auth_ui.dart';
import 'package:firebase_auth_ui/providers.dart';
```
#### Use of the plugin

##### Present the auth screen
```
FirebaseAuthUi.instance()
        .launchAuth(
          [
            AuthProvider.email(), // Login/Sign up with Email and password
            AuthProvider.google(), // Login with Google
            AuthProvider.facebook(), // Login with Facebook
	        AuthProvider.twitter(), // Login with Twitter
          ],
          tosUrl: "https://my-terms-url", // Optional
          privacyPolicyUrl: "https://my-privacy-policy", // Optional,
        )
        .then((firebaseUser) =>
            print("Logged in user is ${firebaseUser.displayName}"))
        .catchError((error) => print("Error $error"));
```
##### Logout
```
void logout() async {
	final result = await FirebaseAuthUi.instance().logout();
	// ...
}
```

Plugin returns `FirebaseUser` with following details:

Field | Description |
 --- | --- |
 uid | UID of authenticated user |
 displayName | Display name of user |
 email | Email of user |
 phoneNumber | Phone number of user |
 photoUri | URI of user's photo |
 providerId | Indicates through which provider user was authenticated. |

Please note that above details may be null depending on the provider user used to sign and user's privacy settings on respective provider.

If you want to have full `FirebaseUser` object then please add [firebase_auth](https://pub.dev/packages/firebase_auth) dependency. You can then use `FirebaseAuth.instance.currentUser()`.

## Firebase Configuration
Create a project on Firebase console (Don't forget to provide SHA1 to use Google Sign-In feature) and add Android and iOS platform in **Settings > Your apps**
- Open the **Authentication** section and then navigate to **Sign in method**. Please enable Email/Password, Google, Facebook and Twitter method depending on your need and click save.
- Navigate back to **Settings > Your apps**
- Download "google-services.json" for Android
- Download "GoogleService-Info.plist"for iOS

## Flutter Configuration
You should create or migrate the project to support androidX libraries or there might be errors.
- To create a project using androidx libraries use: ```flutter create --androidx [APP_NAME]```
- Also, you need to make changes mentioned in this answer if there are errors during your build process: https://stackoverflow.com/a/58655615/7710105

### Android
Open **project's build.gradle** ([flutter_project]/android/build.gradle) and add following in `dependencies{ ... }`:
```
classpath 'com.google.gms:google-services:4.3.2'
```
Open **app module's build.gradle** ([flutter_project]/android/app/build.gradle) and add following at the end of file, i.e as a last line:
```
apply plugin: 'com.google.gms.google-services'
```
Copy the downloaded **google-services.json** in [flutter_project]/android/app directory.

### iOS
Copy the downloaded **GoogleService-Info.plist** in [[flutter_project]/ios/Runner directory.

## Additional setup for Google, Facebook sign-in
### Google
#### Android
No additional setup required.
#### iOS
- Open the **GoogleService-Info.plist** and copy **REVERSED_CLIENT_ID**. It should look like **com.googleusercontent.apps.[APP_ID-somevalue]**
- Now open "Info.plist" ([flutter_project]/ios/Runner and paste following by replacing [REVERSED_CLIENT_ID] with yours copied in above step:
```
<key>CFBundleURLTypes</key>
	<array>
		<dict>
			<key>CFBundleURLSchemes</key>
			<array>
				<string>[REVERSED_CLIENT_ID]</string>
			</array>
		</dict>
	</array>
```
### Facebook
#### Android
Add following lines in **strings.xml** [flutter_project]/android/app/src/main/res/values
```
<string name="facebook_application_id" translatable="false">[YOUR_FACEBOOK_APP_ID]</string>
<string name="facebook_login_protocol_scheme" translatable="false">fb[YOUR_FACEBOOK_APP_ID]</string>
```
#### iOS
- Add fbFACEBOOK_APP_ID as a URL scheme
Open open **Info.plist** ([flutter_project]/ios/Runner and paste following by replacing [YOUR_FACEBOOK_APP_ID] with yours:
```
<key>CFBundleURLTypes</key>
	<array>
		<dict>
			<key>CFBundleURLSchemes</key>
			<array>
				<string>fb[YOUR_FACEBOOK_APP_ID]</string>
			</array>
		</dict>
	</array>
```
**NOTE:** If you added **Google** by following above steps, you already have **CFBundleURLSchemes** key defined with **[REVERSE_CLIENT_ID]**. In that case, just add
```
<string>fb[YOUR_FACEBOOK_APP_ID]</string>
```
below **[REVERSE_CLIENT_ID]**

- Add facebook app id and app's name in Info.plist
```
<key>FacebookAppID</key>
	<string>[YOUR_FACEBOOK_APP_ID]</string>
	<key>FacebookDisplayName</key>
	<string>[YOUR_FACEBOOOK_APP_NAME]</string>
```
### Twitter
#### Android
Add following lines in **strings.xml** [flutter_project]/android/app/src/main/res/values
```
<string name="twitter_consumer_key" translatable="false">[YOUR_CONSUMER_KEY]</string>
<string name="twitter_consumer_secret" translatable="false">fb[YOUR_CONSUMER_SECRET]</string>
```
#### iOS
If you have already configured Google login for iOS following above steps, then you don't need to do anything else. If not, please follow the exact same step.

## Customization
#### General
- The order of login buttons depends on the sequence of `AuthProviders` you passed in `launchAuth()`. For example if you have Email, Google and Facebook auth configured and would like the Facebook to appear first, you can simply pass the `AuthProvider.facebook()` as first element in the providers list.
#### Android
- Actionbar title:
Define your app's name in `strings.xml` and that'll appear as title in action bar.
- Actionbar and status bar color:
Add following in your `colors.xml` [flutter_project]/android/app/src/main/res/values
```
<color name="colorPrimary">#[ACTIONBAR_COLOR]</color>
<color name="colorPrimaryDark">#[STATUSBAR_COLOR]</color>
```
**Note:** If you don't have `colors.xml`, please create one.
