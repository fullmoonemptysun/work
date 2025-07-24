# XML Crash Course

1. XML is like a UI structure language for android (how HTML is for web.)
2. It tells the UI component that are on the screen.

basically when i do <Button, android:something = "hehe'> it translates to the following:  
android.widget.Button btn = new Button()  
btn.setSomething = hehe  

  

1. There is a software component written in java that translates this xml into a binary and then maps the tags to classes and attributes to functions.

> [!important]
> 
> ### 📝 XML in Android
> 
> - **Role of XML**
>     - Declarative layer: describes UI structure and app resources.
>     - Separates presentation (XML) from behaviour (Java/Kotlin).
> - **Namespace Declaration**
>     - Every layout’s root tag contains `xmlns:android="http://schemas.android.com/apk/res/android"`.
>     - Binds the `android:` prefix to the Android framework’s attribute library.
>     - Ensures the parser treats the file as Android-specific rather than generic XML/HTML.
> - **Attribute Definitions**
>     - All legal `android:` attributes are listed in the framework’s `attrs.xml` (inside the Android SDK).
>     - During compilation, the build tools validate that attributes used in layouts exist in these definitions.
>     - At runtime, attributes are mapped to setter methods (e.g., `android:text` → `setText()`).
> - **Tag → Class Mapping**
>     - Tag names (`Button`, `TextView`, `LinearLayout`, etc.) map to classes in `android.widget.*` or `android.view.*`.
>     - Mapping is resolved via reflection when the layout is inflated.
> - **Compilation & Inflation Flow**
>     1. XML is compiled into a compact binary format by AAPT.
>     2. `setContentView()` reads the binary XML, inflates it, and instantiates the corresponding view objects.
> - `**R**` **Class Linkage**
>     - Build system generates `R` constants for every resource (layouts, IDs, strings, drawables).
>     - Code references resources via `R`, creating a type-safe bridge between XML and Java/Kotlin.

1. XML Resources

- UI and values like colors, strings, layouts are defined in XML files under the res/ directory
- Example: `<color name="red">#FF0000</color>`

1. The R Class

- Automatically generated during build
- Groups resources by type: R.color, R.string, R.layout, etc.
- Each resource becomes a unique int ID, like `R.color.red = 0x7f060001`




>[!ERROR] What is actually happening
>When you build an Android app, **aapt (Android Asset Packaging Tool)** or **aapt2** and the **Gradle build system**:
>1. **Parse all XML files** — including `AndroidManifest.xml`, `res/layout/*.xml`, `res/values/*.xml`, etc.
>2. **Generate Java/Kotlin bindings** — like the `R.java` (or `R.class`), so you can access resources in code (e.g., `R.layout.activity_main`, `R.string.app_name`).
>3. **Compile and merge everything** — the manifest, resources, and code — into an APK.
>So you don’t work with XMLs directly at runtime — they’re compiled into a format the Android system can use. You write XML for structure, and the tools turn it into usable code and binary resources


## WTF IS R??
### What is `R`?

`R` is a **class that holds IDs** for **every resource** in your Android project (layouts, strings, images, etc.) so you can use them in Java/Kotlin code.
### Why does it exist?

Because XML and Java/Kotlin are separate worlds. `R` connects them.  
You **write your UI in XML**, but you **control it in code** — so `R` gives you a bridge.
### What does it look like?

If you have `res/layout/activity_main.xml`, then `R.layout.activity_main` is just an **integer ID** (like `0x7f0a0001`) that points to it.

If you have `res/values/strings.xml` with:

`<string name="app_name">MyApp</string>`

Then in code, you do:

`String name = getString(R.string.app_name);`
### Who makes `R`?

**The build tools do** (Gradle + aapt2). They scan your `res/` folder, assign IDs to everything, and auto-generate a Java file called `R.java`.

---

## What is the androidmanifest?
Every project has an `AndroidManifest.xml` file that provides essential information about the app to the Android build tools, operating system, etc.

```xml
<?xml version="1.0" encoding="utf-8"?>  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:tools="http://schemas.android.com/tools">  
  
    <application        android:allowBackup="true"  
        android:dataExtractionRules="@xml/data_extraction_rules"  
        android:fullBackupContent="@xml/backup_rules"  
        android:icon="@mipmap/ic_launcher"  
        android:label="@string/app_name"  
        android:roundIcon="@mipmap/ic_launcher_round"  
        android:supportsRtl="true"  
        android:theme="@style/Theme.AndroidHunt"  
        tools:targetApi="31">  
        <!-- [E] -->  
        <activity  
            android:name=".MainActivity"  
            android:exported="true">  
            <intent-filter>                <action android:name="android.intent.action.MAIN" />  
  
                <category android:name="android.intent.category.LAUNCHER" />  
            </intent-filter>  
            <meta-data                android:name="android.app.lib_name"  
                android:value="" />  
        </activity>    </application>  
</manifest>
```

Analysis: 
1. The first line defines the namespace. It makes sure that this xml file is taken as an android xml file.
2. After that, everything inside the application tags are used to define information about the app.
3. Then the activity tags declare a screen that the system should know about. 
4. Here we define a screen (.MainActivity) and set the intent filter.
5. What is intent filter: How you tell android: This screen in my app is able to handle this kind of message.
6. and then the .MAIN and .LAUNCHER define the intents that this activity can take. (it becomes the entry point)

---

## What are all these build files?
There are mainly 3.
### 1. **`build.gradle` files**

These are **scripts** written by you (or generated by Android Studio) to **tell Gradle what to do.**

There are usually two:

#### a) **Top-level (project) `build.gradle`:**

- Applies global plugins like `com.android.application`
- Sets build script repositories, etc.
#### b) **Module-level (`app/build.gradle`):**
- This one matters most to you.
- Defines:
    - app ID
    - min/max SDK
    - dependencies
    - build types
    - etc.

### 2. **`BuildConfig.java`**

This is **generated** by Gradle based on your `build.gradle` file.
You don’t write this yourself.

---

# What are @something/somethingelse?
android resource references like @color/aqua are not file paths. they refer to a resource of type 'color' with the name 'aqua' that must be defined in some xml file under res/values/

at build time, aapt (android asset packaging tool) parses all xml resource files under res/. it finds color resources like aqua in any res/values/*.xml file. it assigns a unique integer ID to each resource and generates entries in the R class (e.g., R.color.aqua = 0x7f06....). it also compiles all values into a binary resource table called resources.arsc and replaces @color/aqua with the assigned ID in all xml files

at runtime, the app does not know the name 'aqua'. it only knows the resource ID (e.g., 0x7f06....). if a view background was set to @color/aqua in xml, the compiled layout now contains the resource ID. the android framework calls resources.getColor(id) or similar methods to load the actual color value. this lookup happens within the app using the compiled resources.arsc. no xml parsing happens at runtime

the os itself (linux kernel) is not involved in this process. it just loads and runs the app. resource lookup is handled by the android runtime environment in the app process

summary:
- @color/aqua means a color resource named 'aqua'
- aapt resolves it at build time to an integer ID
- the compiled ID is used at runtime
- resource values are stored in a binary table (resources.arsc)
- runtime lookup uses the ID, not the name
- the os does not participate in this process


## You see this in your XML:

`<item name="android:statusBarColor">?attr/colorPrimaryVariant</item>`

### What does it mean?

- `android:statusBarColor` is just saying: **"I want to set the color of the status bar (the top bar on your phone)"**.
- The part on the right, `?attr/colorPrimaryVariant`, is saying:  
    **"Use whatever color the current theme says for `colorPrimaryVariant`."**





# STYLE? THEMES? etc.
**Styles (for individual widgets):**

- Defined as `<style>` in XML (usually `res/values/styles.xml`).
- Contain attribute sets like colors, text size, padding, fonts.
- Applied to a single widget via `style="@style/StyleName"` in layout XML.
- Used to customize appearance of buttons, text views, etc., without affecting whole app.

**Example:**

`<style name="RoundedButtonStyle">     <item name="android:backgroundTint">#FF6200EE</item>     <item name="android:textColor">#FFFFFF</item>     <item name="android:padding">12dp</item>     <item name="android:textSize">18sp</item> </style>`

Use in layout:

`<Button     style="@style/RoundedButtonStyle"     android:text="Click Me" />`

---

**Themes (for app or activity-wide styling):**

- Special styles applied to entire activity or application.
    
- Define default colors, fonts, status bar color, and more.
    
- Applied in `AndroidManifest.xml` in `<application>` or `<activity>` via `android:theme`.
    
- Allow consistent look and easy switching (e.g., light/dark mode).
    

**Example:**

`<style name="Theme.MyApp.Light" parent="Theme.MaterialComponents.Light.NoActionBar">     <item name="colorPrimary">@color/purple_500</item>     <item name="android:statusBarColor">?attr/colorPrimaryVariant</item> </style>`

In manifest:

`<application     android:theme="@style/Theme.MyApp.Light" >     ... </application>`

Or per activity:

`<activity     android:name=".MainActivity"     android:theme="@style/Theme.MyApp.Dark" />`

---

**Key points:**

- Styles = reusable UI attributes for individual views.
    
- Themes = styles applied broadly to many views automatically.
    
- Themes often prefix style names with `Theme.` by convention.
    
- Themes enable dynamic look changes like dark mode easily.


# What is an intent and an intent object?

**Intent** is basically an action *object*. It defines an action that is to be performed.

Examples of intents:
### 1. **Explicit Intent** (Start a specific Activity)

`Intent(Context context, Class<?> cls)`

- Use this when **you know exactly which screen (Activity)** you want to start.
- Common in your own app.

`Intent intent = new Intent(this, SecondActivity.class);`

---
### 2. **Implicit Intent** (Ask the system to find a match)

`Intent(String action)`

- Use when you want to **do something generic**, like view a webpage, send an email, etc.
- The system will find an app that can handle it.

`Intent intent = new Intent(Intent.ACTION_VIEW);`

You often add more info later:
`intent.setData(Uri.parse("https://google.com"));`

---
### 3. **Intent with Action and Data**

`Intent(String action, Uri uri)`

- Combines action and data in one step.

`Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("https://google.com"));`

Same as doing:

`Intent intent = new Intent(Intent.ACTION_VIEW); intent.setData(Uri.parse("https://google.com"));`

---
## 📦 Optional: Add Extras

No matter which constructor you use, you can always add **extra data** to the Intent:

`intent.putExtra("username", "vinnie"); intent.putExtra("age", 22);`

In the receiving Activity, you'd get it like this:
`String name = getIntent().getStringExtra("username");`


## So what is intent filter?

Tells android my app can perform an intent (action) \[Web search, Dial, etc.]
### Typical Intent Filter (used to "handle" actions):

Used to say:

> "My app can handle links, images, emails, etc."

xml

CopyEdit

`<intent-filter>     <action android:name="android.intent.action.VIEW" />     <data android:mimeType="image/*" /> </intent-filter>`

This **matches incoming implicit Intents** (like `ACTION_VIEW`) from other apps.

---

### 🔹 This special one: <span style="color:green"><strong>MUST HAVE</strong></span>

xml

CopyEdit

`<intent-filter>     <action android:name="android.intent.action.MAIN" />     <category android:name="android.intent.category.LAUNCHER" /> </intent-filter>`

This one is **not about handling external requests.**  
Instead, it tells Android:

> "When the user taps the app icon, this is the screen (Activity) to start."

It’s a **marker for the Android system**, not for other apps.



