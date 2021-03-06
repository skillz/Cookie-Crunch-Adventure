# Cookie-Crunch-Adventure

An implementation of [Ray Wenderlich's 'Cookie Crunch Adventure'](http://www.raywenderlich.com/75270/make-game-like-candy-crush-with-swift-tutorial-part-1) Swift app tutorial game.

This fork integrates the [Skillz](https://www.skillz.com) framework with the game, as an example of how to integrate Swift games. The integration steps are identical to an Objective-C project, but with the added step of creating a bridging header ("CookieCrunchAdventure/CookieCrunchAdventure-Bridging-Header.h") that allows the Skillz SDK (an Objective-C plugin) to be used in Swift.


## Code Layout

### Helper Classes

The following are some helper classes added for this game:

* **Array2D<T>**: A 2D array, stored under the hood as a normal 1D array.
* **Set<T>**: A collection of unique elements, stored under the hood as a dictionary.
* **Extensions.swift**: A file that provides a way to parse JSON data into a dictionary.

The game flow is designed using the Model-View-Controller (MVC) pattern.

### Model

The game data is represented with the following classes:

* **Cookie**: An item with a "CookieType" enum, sprite, and grid coordinates.
* **Tile**: A spot on the level where a cookie may be located. Level grids can have holes in them where cookies cannot move to.
* **Swap**: Represents two cookies that can be swapped.
* **Chain**: Represents a horizontal or vertical line of similar cookies that can be cleared at once.
* **Level**: The tiles on the game grid, the cookies that sit in them, and a collection of behaviors for modifying said cookies.

### View

The visual representation of the game is primarily handled by the `GameScene` class. It contains a `Level` instance as well as `SKNode` layers to place various sprites onto.

### Controller

The game flow is primarily handled by the `GameViewController` class.

System callbacks are handled by the `AppDelegate` class.


## Skillz Integration

This project is setup to work with the latest version of the Skillz iOS SDK, which can be found at https://skillz.com/developer/downloads.

Once the SDK is downloaded, the Skillz embedded framework will need to be added to the project. For additional SDK installation details refer to the [Skillz documentation](https://developers.skillz.com/developer/docs/install_framework_ios).

### Integration Steps

* A main menu was added to the game, providing a single button to load the Skillz UI.
* The code that starts the background music was moved to start up as soon as the app loads, so that the music isn't started multiple times.
* The Skillz framework was brought in, and the various integation steps were followed (setting linker flags, adding a Run script build phase, etc).
* A bridging header was added ("CookieCrunchAdventure/CookieCrunchAdventure-Bridging-Header.h") to allow the project to use the SDK.
* The `AppDelegate` class was made to implement the `SkillzDelegate` interface, providing callbacks to handle important events in the Skillz UI:
     * `tournamentWillBegin(gameParameters: [NSObject : AnyObject]!)`: Called when the Skillz UI is about to exit and a match should be started.
     * `preferredSkillzInterfaceOrientation() -> SkillzOrientation`: Returns the orientation the game will use -- the app must be locked to one orientation.
* Skillz API function calls were added to the game:
     * `Skillz.skillzInstance().initWithGameId()`: Called at the start of the app to initialize Skillz.
     * `Skillz.skillzInstance().launchSkillz()`: Called when the "Start Skillz" button in the main menu was pressed.
     * `Skillz.skillzInstance().displayTournamentResultsWithScore()`: Called when a game is finished; tells Skillz what the player's 'score was.