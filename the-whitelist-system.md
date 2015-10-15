## Cordova/Phonegap the white-list system ##

Can you spell disaster? 

Why are you reading this? You've come here because you've suddenly found that *things* have stopped working in your mobile app. Your AJAX calls no longer work, you can't access anything on the net. You haven't changed any code, but you have upgraded to Cordova 4.0 (or higher) from Cordova 3.x (or lower). Cordova 4.0 introduces a new security system, like it or loathe it, you *will* have to use it. The new security framework is *not* trivial. You will *not* read a few lines of something and have your working again. You *will* need to spend some time reading and trying things out to understand them. You *will* need to keep doing this as you understand more about what needs to be done. Sorry, but thats the way it is.

- **FOREWARNED.** *This document is NOT all inclusive, nor does  it cover every exception. This blog entry is a road map to understanding. It is intended for professionals. If you are new to programming and mobile in particular, you will find this confusing and frustrating.* **FOREWARNED.**


[Multiple security bugs](security-issues.md) have perhaps influenced this `white-list`. It is a result of this, or because of this, that the `white-list` system is not completely documented. Add to that the back to back *unannounced changes* to the Cordova system, and I feel like I am catching a falling knife &ndash; without a handle!!

** What Reading is Below**

- Security bugs
- Unannounced changes
- Documentation issues
- How it Works
- What you should watch for

###Security bugs###

-[draft](security-issues.md)
- Android, iOS, Window

###Unannounced changes###

- *Need add stories to these*, **maybe**
- *Phonegap Build* changes the default compiler from *2.7.0* to *cli-5.2.0* the last week of Sept.
- *Cordova* moves the *plugin respository* on the week of Oct 5.

###Documentation issues###
- The white-list guides are incomplete and have errors.
- The documentation contradicts itself
- It is hard to find versions for things
  - [Cordova/PhoneGap Version Confusion](http://devgirl.org/2014/11/07/cordovaphonegap-version-confusion/) - Outdated, does not reflect new system with dashes
  - The online NPM version are not up to date and documentation is sparse and contradictory.
  - Here is an example. [npm](https://www.npmjs.com/package/cordova-plugin-whitelist) says 1.0.0 , [blog](http://cordova.apache.org/news/2015/06/22/plugins-release.html) - June 22, 2015) says 1.1.0
  - Note: the author is the same for both


### How it Works ###

- *To explain this clearly, I'm going to have to do a simple list.[DN: What is this really? Its not a table and its not really a list @)]*

1. Version controls when you need to use it
  - Starting with Cordova 4.0.0 the white-list is required. 
  - The alternative is this quick fix &ndash; but please understand that this *quick fix* removes all needs for a `white-list`. This creates a [security issue](http://www.androidauthority.com/google-webview-security-582363/) which you may not want to bypass.
  -  *QUICK FIX** Add this to your `config.xml` for **PHONEGAP BUILD ONLY**<br />
  - `<preference name="phonegap-version" value="3.7.0" />`
  - For *Cordova/Phonegap CLI* see [Cordova/PhoneGap Version Confusion](http://devgirl.org/2014/11/07/cordovaphonegap-version-confusion/) - Outdated, but useful
2. white-list
  - `<allow-navigation (...) />`
  - Controls which URLs the WebView itself can be navigated to. Applies to top-level navigations only.
  - `<allow-intent (...) />`
  - Controls which URLs the app is allowed to ask the system to open. By default, no external URLs are allowed.
  - `<access origin=(...) />`
  - Controls which network requests (images, XHRs, etc) are allowed to be made (via Cordova native hooks).
  - **DANGEROUS, BUT WORKING** This is not the recommended way to do it. It may be useful to prove something works **BUT** should not be left in a production system.
  - `<allow-navigation href="*" />`
  - `<allow-intent href="*" />`
  - `<access origin="*" />`
3. Content Security Policy (CSP) in the webpage
  - Filters at the webpage level
  - `<meta http-equiv="Content-Security-Policy" content=(...) />`
  - Controls which network requests (images, XHRs, etc) are allowed to be made (via webview directly).
  - **DANGEROUS, BUT WORKING**
  - `<meta http-equiv="Content-Security-Policy" content="default-src *; style-src 'self' 'unsafe-inline'; script-src 'self' 'unsafe-inline' 'unsafe-eval'">`
4. Use a [plugin](https://www.npmjs.com/package/cordova-plugin-whitelist)
  - The one you want is `cordova-plugin-whitelist`
  - *Phonegap CLI* `cordova add plugin cordova-plugin-whitelist@1.1.0`
  - *Phonegap Build* `<gap:plugin name=cordova-plugin-whitelist version=1.0.1 source=npm>`
5. Use the legacy [plugin] (https://github.com/apache/cordova-plugin-legacy-whitelist)
  - This provides a backwards compatible way of using your current security system (older) in Cordova with the new security policy system in Cordova 4. This plugin is meant as a temporary bridge to keep things going. Use it for a week to get things working and then move to the correct whitelist plugin above.

### What you should watch for ###

1. webview
  - each platform has it's own library, as should be expected
  - each has it's own bugs, we need to be proactive
  - we draw clear barrier between webview and the internet 
2. inappbrowser
  - does NOT have access to the hardware on the mobile device
  - The InAppBrowser is not subject to the whitelist, nor is opening links in the system browser.
  - *conflicting* when using the `target` = `_self`: Opens in the Cordova WebView if the URL is in the white list, otherwise it opens in the InAppBrowser.
  - ?it is affect by `white-list` sometimes, but mostly by `CSP`?
3. openwindow
  - this can be a major security issues, use it carefully





