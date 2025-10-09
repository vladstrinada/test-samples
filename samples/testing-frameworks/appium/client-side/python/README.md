# Appium Client Side Python Samples

BitBar can be used to run Appium tests against real devices to test
native Android or iOS applications, hybrid (Android & iOS) or for web
testing (Safari on iOS and Chrome on Android).

You'll find here all steps you need to start running your mobile tests
against real devices in BitBar. Before continuing with running these
scripts you should register with [BitBar
service](https://cloud.bitbar.com/).

For more detailed guides on Appium please refer to their
[documentation online](https://appium.github.io/python-client-sphinx/).

## Uploading Your App To Cloud

There are few ways to upload an app to BitBar.

### Using upload.py
Uploading an app is easiest using the `upload.py` script.

To use `upload.py`, there have to be `requests` package installed.
The best way to do so, it to install all packages from `python3-requirements.txt` file, 
as they will be needed in the next steps.

```bash
$ python3 -m pip install -r python3-requirements.txt
```

Next you can run `upload.py` script and using apiKey identification. 
The apiKey is found under "My Account" in BitBar cloud. 
For the upload to be successful the full path to the
app (apk or ipa) needs to be provided.
If no app is provided on command line, then the BitBar Sample Android app is
uploaded.

Upload a mobile app to BitBar cloud and get a handle to it

```bash
$ python upload.py -h
usage: upload.py [-h] [-k APIKEY] [-a APP_PATH] [-u URL]
```

Optional arguments:

```bash
  -h, --help            show this help message and exit
  -k APIKEY, --apikey APIKEY
                        User apiKey to identify to cloud, or set environment
                        variable BITBAR_APIKEY
  -a APP_PATH, --app_path APP_PATH
                        Path to app to upload or set environment variable
                        BITBAR_APP_PATH. Current value is:
                        '../../../../../apps/android/testdroid-sample-app.apk'
  -u URL, --url URL     BitBar cloud url to upload app or set environment
                        variable BITBAR_UPLOAD_URL. Current value is:
                        'https://cloud.bitbar.com/api/v2/me/files'
```

The below example shows how to upload an Android demo app to BitBar Cloud.

```bash
$ python upload.py -k api123key456to789bitbar0 -a ../../../../../apps/android/bitbar-sample-app.apk -u https://cloud.bitbar.com/api/v2/me/files

File id to use in BitBar capabilities in your test: 127314812
```

The response message provides the application's cloud ID that should be used with `bitbar_app` capability.

### Use curl method:

This method does not support any default BitBar url or default application
so stick to the example as close as possible.

Needed arguments:

```bash
  -u                    User apiKey to identify to cloud. 
                        The colon ':' indicates an empty password, keep it.
  -F                    Uploads the file from given path.
                        Paste path to application after '@'.
  jq '.id'              Parses the JSON response and extracts the ID of the uploaded file.
```

The below example shows how to upload an Android demo app to BitBar Cloud.

```bash
$ curl -X POST \
  -u "api123key456to789bitbar0:" \
  -F 'file=@./bitbar-sample-app.apk' \
  "https://cloud.bitbar.com/api/v2/me/files" | jq '.id'

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 1559k    0   518  100 1558k    441  1328k  0:00:01  0:00:01 --:--:-- 1329k
127314812
```

### Upload file through the website

Go to https://cloud.bitbar.com, 
click the folder icon on the right side of the header to go to the Files Library, 
and upload files in there.

ID will be visible in the application tile.

## Common Settings in Example Tests

There are some common settings needed to run any of the example tests,
regardless of the app type being tested. Each `bitbar_*.py` file
needs to have the following values set as environment variables or set
in the files. Values can also be given as command line parameters to
`run-test.py` script.

### `run-test.py` required parameters:
```
  -k APIKEY, --apikey APIKEY
                        User's apiKey to identify to cloud
  -s SCREENSHOT_DIR, --screenshot_dir SCREENSHOT_DIR
                        Path to screenshot directory
  -t, --test            The test file to be run, choose one of the below:
                        bitbar_chrome, bitbar_android, bitbar_safari, bitbar_ios, bitbar_biometrics_android, bitbar_biometrics_ios
```
### `run-test.py` parameters required in some cases:
```
  -a APP, --app APP     Mandatory for app testing, both android native and iOS native.  
                        Id of app uploaded to cloud (instruction on how to get the ID in Uploading Your App To Cloud part),
                        or path for downloading an app.
  -u URL, --url URL     Mandatory for environments different than https://cloud.bitbar.com
                        BitBar url where test project and devices are found
  -i APPIUM_URL, --appium_url APPIUM_URL
                        Mandatory for environments different than https://cloud.bitbar.com
                        May be used to find devices from certain localisations.
                        BitBar Appium url
```
### `run-test.py` optional parameters:
```
  -h, --help            show help message and exit
  --device DEVICE       Full name of device to use
  --device_group_id DEVICE_GROUP_ID
                        The ID of the BitBar device group from where available devices are to be searched from
  -p PROJECT, --project PROJECT
                        The name of the cloud project
  -r RUN_NAME, --run_name RUN_NAME
                        The name of the test run
  --app_package APP_PACKAGE
                        May be used for native Android tests.
                        App package path.
                        Default app_package corresponds with sample application bitbar-sample-app.apk
  --app_activity APP_ACTIVITY
                        May be used for native Android tests,
                        Main activity.
                        Default app_activity corresponds with sample application bitbar-sample-app.apk
  --bundle_id BUNDLE_ID
                        May be used when running iOS native tests.
                        Default bundleID corresponds with sample application bitbar-ios-sample.ipa
  --cmd_timeout CMD_TIMEOUT
                        New command timeout value, default is 60s
  --test_timeout TEST_TIMEOUT
                        Maximum test duration, defaults to 600s
```

## Example Run

Remember to download pip packages listed in `python3-requirements.txt`:

```bash
$ python3 -m pip install -r python3-requirements.txt
```

An example run of a native iOS application. Using only required parameters and ID of already uploaded application.

```bash
$ python run-test.py -k xYY5...PeOA6 -s ./screenshots -p "iOS" -t bitbar_ios -a "127314812"
testSample (bitbar_ios.TestdroidIOS) ... Searching Available Free iOS Device...
Found device 'Apple iPad Mini A1432 9.2.1'

Starting Appium test using device 'Apple iPad Mini A1432 9.2.1'
15:59:00: WebDriver request initiated. Waiting for response, this typically takes 2-3 mins
15:59:58: WebDriver response received
15:59:58: view1: Finding buttons
15:59:59: view1: Clicking button [0] - RadioButton 1
16:00:00: view1: Typing in textfield[0]: Testdroid user
16:00:08: view1: Tapping at position (384, 0.95) - Estimated position of SpaceBar
16:00:10: view1: Taking screenshot screenshot1.png
16:00:14: view1: Hiding Keyboard
16:00:17: view1: Taking screenshot screenshot2.png
16:00:21: view1: Clicking button[6] - OK  Button
16:00:22: view2: Taking screenshot screenshot3.png
16:00:26: view2: Finding buttons
16:00:28: view2: Clicking button[0] - Back/OK button
16:00:29: view1: Finding buttons
16:00:30: view1: Clicking button[2] - RadioButton 2
16:00:31: view1: Clicking button[6] - OK Button
16:00:32: view1: Taking screenshot screenshot4.png
16:00:36: view1: Sleeping 3 before quitting webdriver.
16:00:39: Quitting
ok

```

### Native iOS Example

Example script: `bitbar_ios.py`

To run iOS native app tests additional parameter is required to be provided:

* **bundleId** - this is your application's unique name

This parameter is not needed if running against the sample BitbarIOSSample.ipa application, as it's set inside the sample script.

```bash
$ python run-test.py -k api123key456to789bitbar0 -s ./screenshots -t bitbar_ios -a "127314812" -u https://cloud.bitbar.com -i https://eu-mobile-hub.bitbar.com/wd/hub -p "iOS Native" -r `date +%R`
```

### Native Android Example

Example script: `bitbar_android.py`

To run an Appium test against a native Android application Appium needs to the
following additional information:

* **app_package** - Java package of the Android app you want to run

* **app_activity** - activity name for the Android activity you want to
  launch from your package. Typically, this is the main activity.

For running the sample applications and tests these do not need to be set as they are set inside the sample scripts if no parameter is given.

```bash
$ python run-test.py -k api123key456to789bitbar0 -s ./screenshots -t bitbar_android -a '127314812' -u https://cloud.bitbar.com -i https://eu-mobile-hub.bitbar.com/wd/hub -p "Android Native" -r  `date +%R`
```

### Safari Browser Testing

Does not need any specific settings.

Example: `bitbar_safari.py`

```bash
$ python run-test.py -k api123key456to789bitbar0 -s ./screenshots -t bitbar_safari -u https://cloud.bitbar.com -i https://eu-mobile-hub.bitbar.com/wd/hub -p "Safari browser"  -r `date +%R`
```

### Chrome Browser Testing

Does not need any special settings.

Example: `bitbar_chrome.py`

```bash
$ python run-test.py -k api123key456to789bitbar0 -s ./screenshots -t bitbar_chrome -u https://cloud.bitbar.com -i https://eu-mobile-hub.bitbar.com/wd/hub -p "Chrome browser"  -r `date +%R`
```
