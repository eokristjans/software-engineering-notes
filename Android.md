# HBV602 - Notes

#### Android Basics

****

## Android Basics

### Activities and Layouts

* An **activity** is responsible for managing user interaction  
with a screen of information, i.e. to implement a part of  
the functionality of your app.
* A **layout** defines a set of UI objects and their screen positions
* The activity `MainActivity` manages the UI
that the layout activity_main.xml defines.

### Widgets

* A **widget** is a user interface component, i.e. a **building block of a layout**.
  * Widgets can show text or graphics, interact with the user, or arrange other widgets on screen
* The widgets of each layout form a hierarchy of `View` objects (“view hierarchy”)
* Elements of the view hierarchy can be
  * visible UI elements
    * e.g. `TextViews`, `Buttons` etc.
* `ViewGroups` (i.e. widgets containing and arranging other widgets in particular ways)
  * e.g. `LinearLayout`, `FrameLayout`, `TableLayout`, `RelativeLayout`
  * e.g. `LinearLayout` places the contained widgets vertically or horizontally next to each other
* Attributes of the XML elements indicate how the widgets shall be configured.
* `text` attributes of widgets can contain
  * literal text
  * References to string resources
    * Strings are defined in a strings file
* `setContentView(R.layout.activity_main)` “inflates” the layout
  * i.e. parses the XML layout file and creates according Java objects for all widgets


### Resoucres

* A resource is any piece of an app that is not code
  * e.g. images, XML files etc.
* stored in subdirectories of `app/src/main/res`


### Quiz 3

* An **activity** manages the user’s interaction with the UI defined by a layout.
* A **layout** defines a set of **widgets** and their screen positions.
* A view hierarchy contains the **widgets** of one **layout**.
* The `setContentView` method of an **activity** inflates the given **layout**.
* Inflating a **layout** means creating Java objects for all **widgets** declared in it.
* A **resource** is any piece of an app that is not executable code.
* The auto-generated `R` class contains references to all **resources** and **widgets**
  defined in XML files under `app/res/`.
* In different device configurations (i.e. orientation or language),
an app may require different resources,
* “Configuration qualifiers” are suffixes added to resource directories
to indicate in which configuration the resources should be used
  * e.g. `layout-land/`, and `drawable-mdpi/`, `drawable-hdpi/` for images
  * * Android will pick the most appropriate image at runtime and scale it as needed



### Android Build Process

![android-build-process](notes-screens/android-build-process.PNG)


## The Model-View-Controller Pattern in Android Apps

* Model: Objects responsible for holding the app’s data and business logic
* View: Objects that know how to draw themselves on the screen and how to respond to user input
* Controller: Objects that respond to events triggered by views, invoke logic on  
  the model, and thus manage the data and control flow between model and view

![mvc](notes-screens/mvc.PNG)


## The Activity Lifecycle

* Activities may transition between three states during their lifecycle.

* To work optimally under the changed configuration, an activity may require  
  different resources (e.g. a different layout or different graphics)
  * To re-initialize the resources, the activity depends on onCreate being called again

![activity-lifecycle](notes-screens/activity-lifecycle.PNG)

![activity-lifecycle-1](notes-screens/activity-lifecycle-1.PNG)

* Call order: `onPause`, `onStop`, `onDestroy`, `onCreate`, `onStart`, `onResume`.

* Android provides a way to save and retrieve activity state at time of transitions:
  * `onSaveInstanceState(Bundle)` saves current instance state in an activity record
  * `onCreate(Bundle)` reconstitutes the activity’s state from the saved activity record
* An activity record is kept even if the activity instance is removed from memory
  * `onSaveInstanceState` : Method called by Android when activity becomes killable
    * Default implementation lets all view objects store their state in a Bundle  
      (a data structure mapping keys to values, typically primitive types).
    * We can extend the method to store additional data in the same Bundle
```java
if (savedInstanceState != null) {
  mCurrentIndex = savedInstanceState.getInt(KEY_INDEX, 0);
}
```

* `onCreate` is typically overridden to prepare the activity’s user interface:
  * Inflating widgets and putting them on screen (with setContentView)
  * Getting references to inflated widgets, in order to work with them later
  * Setting listeners on widgets to handle user interaction
  * Retrieving saved instance state
  * Connecting to external model data
* `onSaveInstanceState` is typically overridden to store small, transient items
* `onPause` is typically overridden for larger save/cleanup tasks upon loss of focus


## Communicating With Intents

* With Intents we can 
  * invoke another screen (i.e. activity) 
  * and pass information to it; 
  * then return to the previous activity 
  * and return information to it. 

* An activity communicates with the Android OS by exchanging intents.
* `Extras` are arbitrary data (key-value pairs) included with the intent.
* **Explicit** intents invoke activities in the same app, but **implicit** intents can invoke activities in other apps.
* To get a result back, invoke an activity with `startActivityForResult(Intent i, int requestCode)`.
* Android will call the `onActivityResultt(int requestCode, int resultCode, Intent data)`  
  event handler on the parent activity in order to send the result back to it:

![intents](notes-screens/intents.PNG)


## Android Threading

### The Main/UI Thread

* The Activities of each Android app are executed in the app’s main thread
* The main thread (also called UI thread) is an infinite loop that
* waits for events from the user or system (e.g. Activity startups, button taps etc.)
* and calls the respective event handlers
* If execution of any event handler takes too long, the UI becomes unresponsive
* If an app doesn’t respond to a UI event within 5 seconds, the Android OS will offer to close it

Any operations that are not instantaneous must **not** be performed on the main UI thread,  
but separate “background” or “worker” threads.

### Background Threads
* There are several mechanisms for executing work in background threads
(i.e. other threads than the app’s UI thread)

#### `AsyncTasks` (deprecated lol)

* ... for light, short-lived infrequent tasks
* All `AsyncTasks` are executed sequentially in the app’s same background thread
* [Dis]advantage: You cannot/need not manage the tasks’ threading mechanics
* If you need to cancel an `AsyncTask` before it has begun or completed, you can
call its `cancel(boolean mayInterrupt)` method
* When and where cancellation of an AsyncTask makes sense may vary
  * Ensure it doesn’t invoke methods on an activity that may already be in an invalid state

#### `HandlerThread`

* You can create your own background threads for “heavy”, long-running tasks
that are tied to a particular activity (so called `HandlerThreads`)
* Each `HandlerThread` is executed in a background thread of its own, controlled by an activity
* [Dis]advantage: You can/need to manage the threading mechanics yourself

#### `IntentServices` (deprecated lol)

* You can create your own background threads for any tasks that are executed independently
from activities (so called `IntentServices`)
* Each IntentService is executed in a thread of its own, started via an intent
* Advantages: Completely independent from an app’s UI; can be scheduled

* An `IntentService` is a component treated in some ways like an activity, i.e…
  * it can be started and destroyed
  * it is invoked via an intent
* ... but differing in some important ways from activities, namely:
  * it has no associated user interface (view)
  * it runs in its own (background) thread
* This means that unlike `AsyncTasks` and `HandlerThreads`, `IntentServices`
can perform background work that is completely independent from activities:
  * A service can be active even when none of the app’s activities are alive
  * A service can be automatically (e.g. periodically) invoked by the Android OS

* An intent addressed at an `IntentService` is called a command.
* Upon the first command, the `IntentService` starts up and puts the command in the queue.
* Further commands are added to the queue in the order they arrive.
* Meanwhile, the `IntentService` takes one command after another from the  
queue and calls `onHandleIntent` for each of it.
* When the queue is empty, the `IntentService` is destroyed.
* When new commands arrive, a new instance of the `IntentService` is created,
and its lifecycle starts from the beginning.

* An `IntentService` can be invoked automatically by the Android OS even  
when none of your activities are alive, i.e. your app’s process is shut down
* Using an `AlarmManager`, you can let this happen periodically or at a specific point in time.
  * Upon each scheduled alarm, a predefined intent will be fired to invoke your service
    * Caution: Consumes a lot of battery.

### Quiz 6

a) Executed in app’s background thread - `AsyncTasks`
b) Executed in background thread of its own - `IntentServices`, - `HandlerTasks`
c) Scheduling controlled by Android OS - `AsyncTasks`, [- `IntentServices`]
d) Scheduling controlled by developer - `IntentServices`, - `HandlerTasks`
e) Started via intent - `IntentServices`
f) Started via method invocation - `AsyncTasks`, - `HandlerTasks`
g) Independent of particular activities - `IntentServices`
h) Tied to particular activity - `AsyncTasks`, - `HandlerTasks`



## Android Storage

### Overview: Where to Store Your App’s Data?

* `savedInstanceState` bundle is only appropriate for (volatile) short-term data saving while an
activity is inactive, and may be destroyed at any time to free memory
* **More persistent alternatives**:
  * Static media for integration in your app’s user interface (e.g. images)
    * Resource directory of your project
  * Persistent storage of simple data that only your app works with (e.g. game state)
    * `SharedPreferences` file with key/value map in your app’s private internal storage directory
  * Persistent storage of large data that only your app works with (e.g. cached data)
    * Files in your app’s private internal storage directory
  * Dynamic, unstructured data that you exchange with other apps (e.g. documents)
    * Files in the user’s external storage directories
  * Dynamic, structured data that only your app works with (e.g. a to-do list)
    * Local SQLite database in your app’s internal storage directory
  * Data that you want to share with other users (e.g. messages)
    * Central data storage on a remote server


### `SharedPreferences`

* A savedInstanceState bundle is a volatile, activity-specific data storage
  * Can only be accessed by the activity that created it
  * May be destroyed by Android at any time to free up memory
  * **Only suitable for maintaining activity state through a destroy-recreate lifecycle transition**
* For reliable storage of small pieces of app state…
  * that shall be accessible by different activities of your app
  * that shall be guaranteed to persist independently of any activity’s lifecycle
  * that shall be deleted only upon uninstalling your app
* ... use `SharedPreferences` instead.
* `SharedPreferences` are simple key/value pairs  
  residing in a persistent file that is accessible only by your app.

```java
// Getting an instance
Context context = getActivity();
SharedPreferences sharedPref = context.getSharedPreferences(
          getString(R.string.preference_file_key), Context.MODE_PRIVATE);

// Writing to
sharedPref.edit()
          .putInt(getString(R.string.saved_high_score), newHighScore)
          .putFloat(getString(R.string.saved_balance), newBalance)
          .apply();

// Reading from
int highScore = sharedPref.getInt(getString(R.string.saved_high_score), 0);
```


### File Storage

#### Internal Storage

* Every app has a “sandbox” directory that is only accessible by the app itself
  * Not by other apps, and not by the user (unless the device is rooted or in an emulator)
* Context (i.e. Activity) objects provide methods for internal storage access:

* Built-in, always available
* Files are accessible only by your app
* Does not require special permission
* All files deleted upon uninstalling app
* **Suitable for files that should be access-restricted**
  * e.g. app state, sensitive data

```java
String filename = "myfile";
String s = "Hello world!";
FileOutputStream outputStream;
try {
  outputStream = openFileOutput(filename, Context.MODE_PRIVATE);
  outputStream.write(s.getBytes());
  outputStream.close();
} catch (Exception e) {
  e.printStackTrace();
}
```

##### Cache Directory in Internal Storage

* Besides files that are intended for persistent local storage, an app may choose
to store data on the device for caching purposes
  * e.g. to avoid having to repeatedly request it from a remote server
* Cached data is special insofar as
  * it typically gets stale (and thus useless) after some time
  * it is not an authoritative information source
    * i.e. if it is stale or missing, the original source can simply be queried again
* To reflect these properties, cache data should be stored in a special directory:
  * File `getCacheDir()` returns handle of the app’s cache directory in internal storage
* Android can then delete cache data automatically if file storage space runs low
  * Yet, **the app should ensure** the cache directory does not grow massively


#### External Storage

* Might be SD card unmounted by user
  * highly unlikely in modern devices
* Files accessible by all of user’s apps
* Requires permission upon installation
* “Private” files deleted upon uninstalling app, “public” files remain
* **Suitable for files that are intended for sharing/outliving your app**
  * e.g. created media/documents
* It’s good practice to check whether external storage is available before attempting to use it:
  * `Environment.getExternalStorageState()` should return `MEDIA_MOUNTED`
* In order to be able to access external storage, your app must **obtain permission**
from the user upon installation.
* To ask for the required permission, include one of the following lines in your
manifest file (`app/src/main/AndroidManifest.xml`):
  * `<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />` (also implies `READ`)
  * `<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />`

##### Private vs. Public External Storage

* **“Private” directories in external storage**
  * `File` handle for directory obtained through `getExternalFilesDir(String type)`  
  instance method of a `Context object` (typically, an Activity)
  * Files created here are readable/writable by other apps, but **your app is in charge of the folder**
    * This could e.g. be data that you publish for consumption by other apps, or vice versa
  * The **directory is deleted when your app is uninstalled**
* **“Public” directories in external storage**
  * File handle for directory obtained through static  
  `getExternalStoragePublicDir(String type)` method of the Environment class
  * Files created here are readable/writable by other apps, and **the user is in charge of the folder**
    * This is typically data “owned” by the user, i.e. the user’s photos, music, documents etc.
  * The **directory remains on the device when your app is uninstalled**
* The String type parameter passed to the previously shown methods
  indicates the type of file you want to access/store, 
  determines which external storage directory will be provided: 
  * `DIRECTORY_PICTURES` for photos
  * `DIRECTORY_MOVIES` for video files
  * `DIRECTORY_MUSIC` for audio files containing music (for use by media players)
  * `DIRECTORY_RINGTONES` for audio files containing ringtones (not for use by media players)
  * `DIRECTORY_DOCUMENTS` for documents


### Local Databases

* Use a server-side database for any structured data that
  * is shared between users
  * is too large for storage on the device
* Use a **local (client-side)** database for easy access to any structured data that
  * can be kept in its entirety on the client device
  * needs to be accessed only by that device’s user
* Technology for client-side database:
  * SQLite, a relational database storing data in simple files
  * **Room, a persistence library** providing an abstraction layer over SQLite
  * Android includes the SQLite library in its standard library
  * Room can easily be added to an app

#### Room Persistence Library Architecture

* `@Database` provides an abstraction of the underlying SQLite database  
  containing the app’s persisted relational data
* `@Dao` instances contain methods for getting entities from the database  
  and saving changes back to the database
* `@Entity` instances represent tables within the database  
  and provide access to its columns

Contrary to other object-relational mappers, Room forbids  object references between entities for performance reasons.

Room does not allow database access on the main thread, as it may delay user interface responses



## Android Fragments

* An **activity** is the controller for one particular
screen with a static layout
* A **fragment** is the controller for a quite
independent user interface “module” that
  * is nested into (“hosted by”) a host activity
  * can be added to or deleted from
  the host activity at runtime
  * has its own layout and behavior
  * receives its own input and lifecycle events
  * can be shown in combination with
  other fragments inside one activity
  * can be shown as a pop-up or tabbed dialog
* **Fragments enable dynamic UI changes.**

A **fragment's lifecycle** methods (`onCreate`, `onPause` etc.) mirror an activity’s.
* When an activity’s lifecycle method is called, the same method is also called on
the fragment(s) hosted by the activity.
  * In addition, the methods `onAttach` and `onDetach` are called
  when a fragment is associated/dissociated with its host activity.

```java
public class ArticleFragment extends Fragment {
  @Override
  public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    return inflater.inflate(R.layout.article_view, container, false);
  }
}
```

#### Read more about Fragments elsewhere (e.g. long example on the professor's slides)

### Quiz 8

* An activity can start another activity...
  * by **creating an intent and passing it to the ActivityManager**,  
    who can then start the desired activity
* An activity can start a fragment...
  * by **telling the FragmentManager to start the desired fragment**  
    in a container in the activity’s layout.
* A fragment can start an activity...
  * by sending a **message to the fragment’s host activity**,  
    who can then start the desired activity.
* An activity can send a message to a fragment it hosts...
  * by calling a **method of the fragment** via the reference the activity holds to it
* A fragment can send a message to the activity hosting it...
  * by calling a method of a **callback interface defined  
    by the fragment and implemented by the activity**.
* A fragment can send a message to another fragment...
  * by sending a **message to the fragment’s host activity**,  
    who can then send a message to the other fragment



