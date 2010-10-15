---
layout: article
uuid: D56AD3F5-DBF4-401F-A7B1-A99BDEEDBD31
title: Notes on UTOSC2010 How to build android apps
name: notes-on-utosc2010-how-to-build-android-apps
created_at: 2010-10-09
updated_at: 2010-10-09
categories: scratchpad
---

Eclipse

  * http://developer.android.com/sdk/eclipse-adt.html#installing
  * https://dl-ssl.google.com/android/eclipse/

SDK

  * http://developer.android.com/sdk/installing.html#Installing

  * Install the SDK
  * Install Eclipse
  * Install ADT
  * Point ADT to SDK
  * Add /local/android-*/tools to $PATH
    * `export PATH="local/android-sdk-mac_86/tools:${PATH}"` in .bash_profile
  * Install components (SDK, docs, APIs)
  * Create AVD
  * Create Project
  * Add text
  * Boot emulator

keytool

      keytool -list -alias androiddebugkey -keystore .android/debug.keystore -storepass android -keypass android
      /.android/<keyfilename>

