---
layout: post
title:  "Code Signing for Android via Github Action"
date:   2025-02-05 18:00:00 -0400
categories: post
---

*(**TL;DR**: By using [fastlane supply](https://docs.fastlane.tools/actions/supply/) and [Github Action](https://github.com/features/actions) we can compile and publish an Android app automatically)*

*(Part three of my series on code-signing / distributing apps, check [Part 2 on Apple](/post/2025/02/02/code-signing-apple.html))*

<br/>

## Create Github Repository

Create a Github Repository for your project. Feel free to use [this repository](https://github.com/starburst997/android-code-sign) as a template since it already contains all the workflows files.

<br/>

## Installing fastlane

We need to install [fastlane](https://fastlane.tools/) locally on our machine, first makes sure you have [ruby](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller) installed (personally I prefer via [WSL2](https://learn.microsoft.com/en-us/windows/wsl/install)).

Also you need to makes sure [Bundler](https://bundler.io/) is installed:

```console
gem install bundler
```

Now create a file called `Gemfile` in the root:

```ruby
source "https://rubygems.org"
gem "fastlane"
gem 'fastlane-plugin-github_action', git: "https://github.com/starburst997/fastlane-plugin-github_action"
```

Notice that we're using my fork of [joshdholtz/fastlane-plugin-github_action](https://github.com/starburst997/fastlane-plugin-github_action), this is because the published gem is out of date.

Now run:

```console
bundle install
```

<br/>

## Create a Google Play Console Account

[Sign up](https://play.google.com/console/signup) for a **Google Play Console** account, you'll need to pay a one-time fee of $25 and verify your identity. 

You can follow the [instructions here](https://support.google.com/googleplay/android-developer/answer/6112435) but it's pretty straight-forward.

<br/>

## Create a Google Service Account

To programmatically access the [Google Play Console](https://play.google.com/console/?hl=en), you will need a dedicated **Google Service** account with API access.

The [fastlane documentation](https://docs.fastlane.tools/actions/supply/) highlight the steps but here's a summary:

<center class="images borders">
  <div><a href="/assets/posts/2025-02-05-android-publish/google-service-01.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/google-service-01.png" alt="Create Google Project" title="Create Google Project"/></a><br/>1</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/google-service-02.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/google-service-02.png" alt="Enable Google Play Developer API" title="Enable Google Play Developer API"/></a><br/>2</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/google-service-03.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/google-service-03.png" alt="Select your project" title="Select your project" /></a><br/>3</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/google-service-04.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/google-service-04.png" alt="Create a new Service Account" title="Create a new Service Account" /></a><br/>4</div>
</center>

1. Create a [Google Cloud Project](https://console.cloud.google.com/projectcreate) (or use an existing one). Give it a name and click create ([more info](https://cloud.google.com/resource-manager/docs/creating-managing-projects)).

2. Enable the [Google Play Developer API](https://console.developers.google.com/apis/api/androidpublisher.googleapis.com/?hl=en) by clicking the **Enable API** button for your newly created project.

3. Open [Service Accounts](https://console.cloud.google.com/iam-admin/serviceaccounts?hl=en) on Google Cloud and select your project.

4. Create a new **Service Account** by clicking the create button.

<center class="images borders">
  <div><a href="/assets/posts/2025-02-05-android-publish/google-service-05.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/google-service-05.png" alt="Fill form, copy email" title="Fill form, copy email"/></a><br/>5</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/google-service-06.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/google-service-06.png" alt="Select Managed Key" title="Select Managed Key"/></a><br/>6</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/google-service-07.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/google-service-07.png" alt="Create new key" title="Create new key" /></a><br/>7</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/google-service-08.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/google-service-08.png" alt="Save JSON key" title="Save JSON key" /></a><br/>8</div>
</center>

5. Give it a name and ID, copy the resulting **Email address** (we will need it later). Click on **Done**, *NOT* ~Create and Continue~.

6. Click on the vertical **three-dot icon** (under **Actions**) of your newly created account, and select **Manage keys**.

7. Click on **Add key** and select **Create new key**.

8. Make sure to select **JSON**, click **Create** and save the file on your computer.

<br/>

## Invite Service Account to Google Play Console

<center class="images borders">
  <div><a href="/assets/posts/2025-02-05-android-publish/invite-service-01.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/invite-service-01.png" alt="Invite new user" title="Invite new user"/></a><br/>1</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/invite-service-02.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/invite-service-02.png" alt="Paste email address" title="Paste email address"/></a><br/>2</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/invite-service-03.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/invite-service-03.png" alt="Choose permissions" title="Choose permissions" /></a><br/>3</div>
</center>

1. Open the [Google Play Console](https://play.google.com/console/?hl=en) and select **Users and Permissions**. Click **Invite new users**.

2. Paste the **email address** of the **Service Account** you saved for later (`xxxx@yyyy.iam.gserviceaccount.com`).

3. Choose the permissions: **Admin (all permissions)**. Click on **Invite User**.

<br/>

## Test your JSON file

We can now test that the connection is valid using your **JSON file** by running this command:

```sh
bundle exec fastlane run validate_play_store_json_key json_key:/path/to/your/downloaded/file.json
```

Look for `Successfully established connection to Google Play Store`.

<br/>

## Generate an upload key and keystore

You can generate your key using [Android Studio](https://developer.android.com/studio/publish/app-signing#generate-key), but here's how you can do it on the command line using [keytool](https://docs.oracle.com/javase/7/docs/technotes/tools/solaris/keytool.html) (available in your JDK's bin folder):

```sh
keytool -genkey -v -keystore .keystore -keyalg RSA -keysize 2048 -validity 10000
```

You'll be prompt to add some information, keep notes of the **password** (use the same for both) and the **alias**.

For ease of use afterward, we'll save the keystore as **base64** and save it as a secret in our repository.

```sh
base64 .keystore
```

I've also created a [Github Action](https://github.com/starburst997/android-publish/blob/main/.github/workflows/keystore.yml) that you can run in this repository called: **Generate .keystore**. It will also generate the **base64** version available in the artifact (don't forget to **delete** the artifact afterward!).

Either the PKCS12 or JKS variant will works.

<br/>

## Save secrets

We can now add all the necessary secrets to our repository:

- `GOOGLE_PLAY_KEY_FILE`: The **JSON** file content from the **Service Account**.
- `ANDROID_KEYALIAS_NAME`: The **alias** used for the **.keystore**.
- `ANDROID_KEYSTORE_PASS`: The **password** used for the **.keystore**.
- `ANDROID_KEYALIAS_PASS`: The **password** used for the **.keystore** (same as above).
- `ANDROID_KEYSTORE_BASE64`: The **base64** value of the **.keystore** file.
- `ANDROID_PACKAGE_NAME`: The package name of your app.

*(Save those variables inside a Password Manager for re-use in future projects, except for the package name, they won't change)*

<br/>

## Build .aab

Before you can upload your app via [fastlane](https://docs.fastlane.tools/actions/supply/), you need to manually submit your app once (see [fastlane/fastlane#14686](https://github.com/fastlane/fastlane/issues/14686)).

The [android folder](https://github.com/starburst997/android-code-sign/tree/main/android) contains a simple "Hello World" project.

<center class="images borders">
  <div><a href="/assets/posts/2025-02-05-android-publish/action-01.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/action-01.png" alt="Run Build action" title="Run Build action" /></a><br/>1</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/action-02.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/action-02.png" alt="Download artifact" title="Download artifact"/></a><br/>2</div>
</center>

1. **Build** your app using the Github Action (**Build Android**). Go to the **Actions** tab and click **Run Workflow**.
2. **Download** the artifact once the action is done.

<br/>

## Create app in Google Play Console

<center class="images borders">
  <div><a href="/assets/posts/2025-02-05-android-publish/create-app-01.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/create-app-01.png" alt="Create App" title="Create App"/></a><br/>1</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/create-app-02.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/create-app-02.png" alt="Fill form" title="Fill form"/></a><br/>2</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/create-app-03.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/create-app-03.png" alt="Create new release" title="Create new release" /></a><br/>3</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-05-android-publish/create-app-04.png" target="_blank"><img src="/assets/posts/2025-02-05-android-publish/create-app-04.png" alt="Upload .aab" title="Upload .aab" /></a><br/>4</div>
</center>

1. Open the [Google Play Console](https://play.google.com/console/?hl=en) and click **Create app**.

2. Fill out the form and click **Create app**.

3. In the **Test and release** section, select **testing** and then **Internal testing**.Click **Create new release**.

4. Drag and drop your **.aab**, enter release name and notes. Click **Save and publish**

<br/>

## Create Fastfiles

#### fastlane/Appfile
```ruby
for_platform :android do
  package_name(ENV["ANDROID_PACKAGE_NAME"])
  json_key_file(ENV["GOOGLE_PLAY_KEY_FILE_PATH"])
end
```

#### fastlane/Fastfile
```ruby
org, repo = (ENV["GITHUB_REPOSITORY"]||"").split("/")
match_org, match_repo = (ENV["MATCH_REPOSITORY"]||"").split("/")

platform :android do
  desc "Upload a new Android version to the production Google Play Store"
  lane :production do
    upload_to_play_store(track: 'production', release_status: 'completed', aab: "#{ENV['ANDROID_BUILD_FILE_PATH']}")
  end

  desc "Upload a new Android internal version to Google Play"
  lane :internal do
    upload_to_play_store(track: 'internal', release_status: 'completed', aab: "#{ENV['ANDROID_BUILD_FILE_PATH']}")
  end
end
```

<br/>

## Generate a Personal Access Token (PAT)

<center class="images borders">
  <div><a href="/assets/posts/2025-02-02-code-signing-apple/pat_01.png" target="_blank"><img src="/assets/posts/2025-02-02-code-signing-apple/pat_01.png" alt="Generate New Token (Classic)" title="Generate New Token (Classic)" /></a><br/>1</div>&nbsp;&nbsp;
  <div><a href="/assets/posts/2025-02-02-code-signing-apple/pat_02.png" target="_blank"><img src="/assets/posts/2025-02-02-code-signing-apple/pat_02.png" alt="Use repo score" title="Use repo score"/></a><br/>2</div>
</center>

We also need to generate a **Personal Access Token** for Github. 

This will enables us to increment a build number after each build.

1. Visit your [settings page](https://github.com/settings/tokens) and click on **Generate new token** and select **Generate new token (classic)**.

2. We need all the **repo** scope enabled. Set **no expiration**. Click **Generate token** and save the value.

### Save secrets

Save this secret in the github repository for your project:

- `GH_PAT`: The value of your newly generated token

<br/>

## Create workflows

By using `workflow_call` we can simplify the workflow file by referencing an external one, but feel free to copy the original instead to fit your pipeline better.

Save those inside the `.github/workflows` directory of your repository.

#### android.yml ([original](https://github.com/starburst997/android-code-sign/blob/v1/.github/workflows/android.yml))
```yml
name: Build Android

on: 
  workflow_dispatch:

jobs:
  build:
    uses: starburst997/android-code-sign/.github/workflows/android.yml@v1
    secrets: inherit
    with:
      path: 'android'
      module: 'app'
      version: '1.0'
```

#### publish_android.yml ([original](https://github.com/starburst997/android-code-sign/blob/v1/.github/workflows/publish_android.yml))
```yml
name: Publish Android

on: 
  workflow_dispatch:

jobs:
  build:
    uses: ./.github/workflows/android.yml
    secrets: inherit

  publish:
    uses: starburst997/android-code-sign/.github/workflows/publish_android.yml@v1
    secrets: inherit
    with:
      lane: 'internal'
```

#### keystore.yml ([original](https://github.com/starburst997/android-code-sign/blob/v1/.github/workflows/keystore.yml))
```yml
name: Generate .keystore

on:
  workflow_dispatch:
    inputs:
      alias:
        type: string
        description: Alias (anything you want)
      password:
        type: string
        description: Password
      name:
        type: string
        description: Your Name
      organization:
        type: string
        description: Organization Name
      locality:
        type: string
        description: Locality / City
      state:
        type: string
        description: State / Province
      country:
        type: string
        description: Country (2 letter code)

jobs:
  setup:
    uses: starburst997/android-code-sign/.github/workflows/keystore.yml@v1
    needs: [build]
    secrets: inherit
    with:
      alias: ${{ inputs.alias }}
      password: ${{ inputs.password }}
      name: ${{ inputs.name }}
      organization: ${{ inputs.organization }}
      locality: ${{ inputs.locality }}
      state: ${{ inputs.state }}
      country: ${{ inputs.country }}
```

Notice that we need to specify the project's path, module and version.

<br/>

## Publish an update

Now you can call the **Publish Android** action and you should see a new build appears in your internal test lane.

Makes sure your app has been **reviewed** by filling all the necessary steps in [Google Play Console](https://play.google.com/console/?hl=en), otherwise you'll get an error: `Only releases with status draft may be created on draft app.`

The workflow will also automatically increment the build number and save it as a variable in the repository.

I've also included a [release workflow](https://github.com/starburst997/android-code-sign/blob/main/.github/workflows/release.yml) as an example.

<br/>