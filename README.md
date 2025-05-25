# Week 12 -- Cameras --

Learning Outcomes:

* Become familiar with the default camera system in Unity
* Become familiar with Cinemachine and the unique features it offers
* Understand the usage of confiners for cameras
* Learn how to manipulate the target of the camera

## Notice - Bug Found in WK9 Prac

When writing this prac, we noticed that there was an obvious bug with the recommended solution found in wk9's "**Adding hazards for the player**" section. It is a bug you will run into when doing today's prac, or when doing your assignment. Images that contain the specific bug we are referring to are pictured below (image 17 and 19 of the wk9 practical):

<img src="PracResources\images\wk9_17_TriggerLogicHealth.png">
<img src="PracResources\images\wk9_19_HazardFunction.png">

Bug: For the `IsOnLayer` node to be implemented properly, you must also take the output `result` into an If node (and then use the `True` output of the If node). Otherwise, the `IsOnLayer` node will not do anything (and your code will run when you are on triggering **any** layer).

Solution: pictured below, but essentially:

1. Add an If node after your IsOnLayer node
2. Pipe in both the **Output Trigger** and the **Result** of your IsOnLayer node, into the `If` node
3. Only use the True output from the If Node

<img src="PracResources\images\Bug_WK9Prac.png">

## Foreword

With us approaching the end of semester, we've nearly covered everything we need for the final assignment of the unit. Today is essentially the last "major" prac for the unit, however, next week there will be some brief content regarding how to implement audio into your game.

While we are covering Cameras specifically today, we're mainly going to be focusing on a specific package Unity provides us with, which is used to create interesting and dynamic cameras, called Cinemachine. While the basic implementation of Cinemachine *does* count towards implementation marks (outlined in the **Cinemachine Tracking Targets** section), Cinemachine has the capability out of the box to provide some cool and interesting camera tracking options, which could prove useful for any type of game you're building

For now, though, let's do a quick recap and go over how the default Unity camera works.

## The Unity Camera

While the focus of this prac is on Cinemachine, the Unity default camera is fine for a lot of simple/basic usage. The Unity default camera comes with a lot of fields/variables you can change, as you've likely noticed before:

<img src="PracResources\images\01_DefaultCamera.png">

From the lecture, you should already understand the purpose of **Size** and **Projection** (if you do not, quickly go and revise the lecture slides). Otherwise, let's quickly look at how the camera field of view (**Size**) changes in real-time.

In your **Game** scene (the one we originally made in **Tiled**), un-child your "`Main Camera`" from your Player, and try and centre it in the middle of your main room. Once you've done this, **Play** your game. Typically, you should observe something like this:

<img src="PracResources\images\02_DefaultGameView.png">

There are a few key things to notice here:

* As we've un-childed the camera from the player, moving the player feels a bit different (compared to when it was following the player)
* We can see "out of bounds" - by default, the out of bounds colour is the "Unity blue" colour, which is an eyesore for the average seasoned Unity developer
* We can see quite far out of bounds when we don't need to. The camera could simply be more zoomed in.

Exit **Play** mode, we're going to tweak some of these default settings.

Firstly, change the `Background` so that it isn't this ugly default blue colour. This could be whatever you want it to be (e.g. Black). **Play** your game again, and you will notice that it feels a bit different, but seeing out of bounds isn't ideal either.

While it may be impossible to not show out of bounds sometimes, depending on your game (e.g. this is inevitable in some of the 2D Pokémon games when you're inside houses), a trick you might like to do is simply zoom the camera in - although note that this also changes the feeling/sense of scale.

When working with 2D, we typically want to edit the `Size` (field of view) of the camera. Decrease this value in your `Main Camera` so that only your main room is visible (evident by the white rectangle shown in the **Scene** view when selecting your camera) - you may need to reposition your camera during this step.

Ideally, you should end up with something like this, although you might notice it doesn't quite work even with these changes:

<img src="PracResources\images\03_EditedGameView.png">

* Because of how I've designed my level, the corners present show a visible out-of-bounds area. If I were to zoom further in to hide these, I would lose the ability to see the walls of my level.
* The north-east corner has a pathway to another room. With this setup, I won't be able to follow my player into that other room
* Zooming in this much makes my game look blurry, and may not be ideal

When designing for 2D, these are definitely choices you'll have to consider when designing "the look" of your game. However, camera restrictions can also lead to interesting gameplay. There may be times where you as a designer want to lock and zoom in the camera, for example, like the old school "Legend of Zelda" titles or the "The Binding of Isaac", where sometimes the camera "snapped" to show the player the entire play arena, or you may also just want to do this during a cutscene. Additionally, while you can simply add more sprites to the edge/background of your world to hide the "out of bounds", it's also not the end of the world if the player sees this sometimes, either (e.g. as mentioned above, Pokémon has done this countless times).

## Cinemachine - Setup

Now that we have a better understanding of tradeoffs when working with cameras, let's move on to implementing more advanced camera tricks.

As mentioned above, we will be using Cinemachine today, specifically Cinemachine v3. In your own time, you may want to research Cinemachine (e.g. when attempting today's challenges), however, please keep in mind that most of the online tutorials will be discussing Cinemachine v2; some of the solutions for problems might be slightly different when using this new version.

If you are doing your own research, it is recommended to briefly go over the newly named components/renamed components, [which can be found in the "Upgrading from Cinemachine 2.x" page in the official Cinemachine Documentation](https://docs.unity3d.com/Packages/com.unity.cinemachine@3.1/manual/CinemachineUpgradeFrom2.html#new-components-with-clearer-names)

### Installing Cinemachine v3

Before continuing, please make sure Cinemachine is actually installed. To verify this, open up the **Package Manager** (Top Menu bar -> `Window > Package Manager`), select the `Unity Registry` tab (the icon that looks like a block of chocolate), and simply install Cinemachine if it is not installed already.

<img src="PracResources\images\11_CinemachinePackageInstall.png">

If v2 is installed for whatever reason, instead, please remove the package (the `Install` button above should simply say `Remove`) and re-install - simply doing the above steps will install the latest (v3) version.

## Exploring Cinemachine

Cinemachine has a ton of features, but to keep things simple, we will only be exploring a few of them today. Cinemachine offers several more features out of the box which you may like to use in future, such as:

* Free-look cameras (i.e. fly-through cameras)
  * Note: In v3 this is simply the `CinemachineCamera`
* State-Driven cameras
* Dolly Cameras (i.e. with a dolly "Track")
  * Note: Some UI/documentation may refer to a "dolly cart", but in v3 this is known as the "Spline Cart"
* Collision avoidance systems or "Confiners"
* Camera "blending" (think of this like transitioning PowerPoint or Canva slides)
  * It typically does this through the use of "virtual cameras", which are essentially preconfigured points (these preconfigured points can also be dynamic, such as existing as children of objects)
* Alongside **Timeline** (another Unity feature/system) and the systems mentioned above, it can be used as a cutscene creator/editor

As you can probably guess, it's quite advantageous to learn this early on. While these are good examples of system features, practical usage of Cinemachine could be:

* Making a movie in Unity - this has been done before
  * "The Heretic", by Unity's demo team, is a good example of this, even though it's a tad old
* Handling all of the cutscenes in your game
* Making a First-person shooter with transitions between aiming down the sights, a third-person mode, etc
* A dynamic/physical main menu with camera transitions to different areas in a room, as well as camera shakiness
* Similar to "The Binding of Isaac", a system where the camera can transition between static/fixed positions, or a "free" camera.

## The Cinemachine "Brain"

As you might imagine from the above list of possible implementations & out-of-the-box features, it's quite easy to get overwhelmed when looking at all of the various options. The first step of learning how to use Cinemachine is learning how to use the actual camera itself - the "Cinemachine Brain".

As mentioned above, Cinemachine uses "Virtual Cameras", which are essentially preconfigured points to track various objects/points.


|   | Note: a key difference between v2 and v3                                                                                                                                            |   |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
|   | In Cinemachine v2 documentation, the "Virtual Camera" component was literally called`Virtual Camera`. However, in Cinemachine v3, they are now simply called `Cinemachine Cameras`. |   |
|   |                                                                                                                                                                                     |   |

These "Virtual Cameras" help the `Cinemachine Brain` (i.e. the **Main Camera**) compose its shot by:

* Positioning the Unity Camera in the scene
* Aiming the Unity camera at the desired target/object
* Adding noise - this noise could emulate vehicle shakes, or give the impression of a camera "feeling" handheld

Additionally, all the **Cinemachine Brain** does is simply control the `Main Camera`, however it also helps handle the logic for handling the various "Virtual Cameras". If you dig deeper you might notice that it does a *bit* more than this, but this is enough for a general understanding.

### Adding "The Brain"

Let's have a go at setting this up. In your **Hierarchy**, right-click and select `Cinemachine > Cinemachine Camera` to add a "Virtual Camera" to your scene.

Upon doing this, you should notice your Main Camera will slightly change in appearance (within the **Hierarchy**). This icon indicates which object has the `Cinemachine Brain` component - only one of these brains can exist in a scene.

<img src="PracResources\images\12_CinemachineBrain.png">

Before continuing, ensure that the `Main Camera` is not a child of your Player object.

## Cinemachine Tracking Targets

Now that we have a virtual camera, you'll notice that if we press play, it currently doesn't quite do what we want it to do. If the position of the object is correctly set to `0,0,0`, it will also simply cause the `Main Camera` to simply show a black screen.

Let's have a go at implementing the logic for having this virtual camera follow the player. To implement this, do the following:

1. Rename our newly created "`CinemachineCamera`" to something a bit more obvious, like "`CinemachinePlayerFollowCam`"
2. In the Hierarchy, select our "`CinemachinePlayerFollowCam`", and in the **Inspector**, navigate to **Procedural Components** and select `Position Control > Position Composer` - doing this will add a new component to our "`CinemachinePlayerFollowCam`"
   * Upon adding your Position Composer component, Cinemachine will throw some warnings as it does not yet have a "Tracking target"
3. Verify that the `Camera Distance` in the `Cinemachine Position` is set to `10` (this is to get rid of the "black screen" issue)
4. Scroll back up to the `Cinemachine Camera` component, and set the `Tracking Target` to be the Player.
5. In the `Cinemachine Camera` component, change the `Lens > Orthographic Size` to a sensible number, so that the camera is appropriately zoomed out again
   * Note that this controls the default camera/brains `Size` variable, which we tweaked before

When you're done, your Cinemachine camera should look something like this:

<img src="PracResources\images\13_CinemachineBasicImplementation.png">

This is the basic implementation of Cinemachine, and is how you would go about mimicking the behaviour of simply attaching the Main Camera as a child object of the player. However, this is a bit boring. Let's have a go at personalising this a bit.

## Customising Your Camera "Feel"

Select your **Game** view, and you should notice that there are some visual elements that now appear when selecting your "`CinemachinePlayerFollowCam`". These may be a bit hard to see depending on your layout, so they have been circled in the accompanying image:

<img src="PracResources\images\21_CameraGizmoHighlight.png">

As suggested in the annotated image, clicking and dragging these blue lines can directly affect the `Screen Position` variable in the `Cinemachine Position Composer` component. Have a go at changing this if you want.

### Target Offset

Additionally, the yellow dot indicates the exact point our camera is focusing on. You may notice that in the above image (and possibly for you), it is not exactly in the middle of the Player.

While looking at your **Game** view, try and reposition the `Target Offset` in the `Cinemachine Position Composer` so that it is directly in the middle of your Player, like this:

<img src="PracResources\images\22_VCamOnPlayer.png">

### Target Damping

You may notice that the camera lags slightly behind the player by default, which is intentional. This is due to the `Damping` property, which is found in the `Cinemachine Position Composer` component.

Damping controls how strictly the camera follows the Player (or target object), with smaller numbers being more responsive, and larger numbers tracking the targets more slowly.

Have a go at tweaking these - note that you do not need to adjust the Z target offset at all  (since we are in 2D). Try the following:

* A Target offset with `X: 0.5` `Y: 0.5`
* A Target offset with `X: 2` `Y: 2`
* A Target offset with `X: 0` `Y: 0`

Feel free to keep whichever one you prefer. You may also want to do a custom Target offset that is different for both the X and Y axes. This might be a good choice depending on the type of game you are making (e.g. a platformer might want some slight Y damping when jumping outside of a "confiner" or "Dead Zone")

### Dead Zone

You may be familiar with cameras that allow the player to move slightly before beginning to follow the avatar. This is easy to implement in **Cinemachine**, and is referred to as a `Dead Zone`.

To create a `Dead Zone`, simply toggle it on in the `Cinemachine Position Composer`, and you're essentially done - You can play your Game and this will immediately work.

You will notice that the camera gizmo in your Game view will also slightly change because of this - you should see that the camera only starts moving when the yellow target dot pushes into the `Dead Zone` boundary, as seen below:

<img src="PracResources\images\23_DeadZonePreview.png">

In our case, once the **Tracking Target** (our Player) is outside of the **Dead Zone**, **Damping** kicks in, and tries to follow the player at whatever `Damping` parameter/speed we have chosen - note that it does not try to center on the Player, but instead tries to simply put the **Tracking Target** back inside the **Dead Zone**.

As a result of this behaviour, it is recommended to always carefully tweak your **Damping** when implementing a **Dead Zone**.

## Cinemachine "Confiners"

Now let's have a go at implementing a camera confiner with Cinemachine. This feature is somewhat similar to our static/unchilded camera from before, where we simply want to keep the camera focused (and possibly zoomed in/out of) a specific point in space - essentially limiting the camera's view of an area.

Thankfully, in 2D this is quite easy to implement (in 3D you need to take depth into account), but we'll also need to do some scripting in order for us to seamlessly switch between the two.

### Creating a New Cinemachine Camera

Since we are making multiple Cinemachine cameras (and eventually, a **Camera Manager**), it would be sensible to group all of our **Cinemachine Cameras** together:

1. Make a new Empty Game Object that will house our **Cinemachine Cameras**, and give it a sensible name (like "Cameras")
   * It is recommended you **do not** have your `Main Camera` inside of this group, since it makes sense to keep these objects separate
2. Create a new Cinemachine Camera, and give it a sensible name, like "`CinemachineConfinerCam`"

We'll come back to setting up our confiner/targeting logic for our new camera, but for now, do the following:

1. Check the `Priority` tickbox in each of our `Cinemachine Cameras`, and set their priority to the following:
   * "`CinemachinePlayerFollowCam`": `Priority 0` - this should be the lower priority
   * "`CinemachineConfinerCam`": `Priority 1` - this should be the higher priority camera, since we want it to override our 'PlayerFollowCam'
2. Disable the `Cinemachine Camera` component in our `CinemachineConfinerCam` object - we will be enabling/disabling this with a script later

### Setting up a "Confiner" Trigger

While you could get away with making a collider, today we're instead going to have a go at implementing this with a trigger volume instead.

1. Make a 2D square sprite, and change its colour so that it is slightly transparent (e.g. transparent and pink). Give this an appropriate name, like "`ConfinerCollider`"
   * You can make it completely invisible if you wish, but making it easy to see will help during testing
2. Add a `BoxCollider2D` to the sprite & set it as a trigger
3. In the newly created `ConfinerCollider`, navigate to the  `Transform` component and change its `Position` and `Scale` so that it covers a chunk of your screen.
   * If your map is made up of multiple rooms, I would suggest having it cover a single room
4. Give this object a new layer, called "`CameraConfiner`". Check that the `Player` and `CameraConfiner` can collide in our `LayerCollisionMatrix`
5. Select our `CinemachineConfinerCam` that we previously made.

After following these steps, you have one of two options to go about tracking here:

#### Option 1 - Use a "Tracking Target"/Fixed Point in the Collider Itself

Simply give our **Cinemachine Camera** a "target", that is simply an empty GameObject:

1. Create an empty game object that is a child of our ConfinerCollider object
   * Give it an appropriate name, like "`ConfinerColliderTarget`"
2. Make the position of `ConfinerColliderTarget` inside your ConfinerCollider object (e.g. slightly above the centre of the parent object), like the image below:
3. In our `CinemachineConfinerCam`, set the `Tracking Target` to be our `ConfinerColliderTarget` object
4. Give the `CinemachineConfinerCam` a `Position Composer`, check that the tickbox for `CenterOnActivate` is checked

<img src="PracResources\images\31_FixedPointInConfinerExample.png">

#### Option 2 - Track the Entire Collider

Cinemachine has some logic for handling confiners already, with the `CinemachineConfiner2D` extension:

1. In our `CinemachineConfinerCam`, scroll down past the **Procedural Components** section, and select `Add Extension > CinemachineConfiner2D`
2. Set the `BoundingShape2D` to be your `ConfinerCollider` object

Note: `Damping` and `Slowing Distance` will not do anything with the implementation we're doing, but you may want to set `Oversize Window` if Unity prompts you to the `Damping`

### Setting up Visual Scripting With Cinemachine

Before we are able to start adding Cinemachine nodes into our visual scripts, we need to first add an 'Assembly' to our '**Node Library**' - think of it like adding our custom nodes, but we're adding in a package that is already built into Unity. This is unnecessary for most use cases of Unity (i.e. like everything else we've done so far this semester), but without this step, we will not be able to find the correct variable type (`Cinemachine Camera`), and the nodes will not appear in the script editor.

In the top menu bar, navigate to `Edit > Project Settings`, and select the `Visual Scripting` tab. Click the `Node Library` dropdown, and navigate to the bottom of the list.

There will be a `+` button at the bottom of the list. This will add a new (empty) field. Click the new field, and type in `Cinemachine`. There will be two results, select `Unity/Cinemachine`

<img src="PracResources\images\31_FixedPointInConfinerExample.png">

Once you have selected this option, the assembly should appear in the list (ignore the ⚠️warning icon next to it). Press `Regenerate Nodes`, and you're good to go.

### Visual Scripting With Cinemachine

To get started, create a new **Script Graph** called `CameraManager` and attach it to the parent of our cameras (by adding a `Script Machine` component), and give it the following variables:

* `followCam` - Type: `Cinemachine Camera`
* `confinerCam` - Type: `Cinemachine Camera`

However, we have a problem: it isn't really feasible to have our camera be hitting this trigger - our camera will never be touching the camera since it isn't sharing the collider with the player.

As a result, it would be sensible for our Player to cause this event instead (when it walks into our trigger), but how would we tell the manager what to do?

#### Trigger Events

We will be using **Trigger Events**. To do this, we require two nodes (one that causes the event, and the other that receives the event):

<img src="PracResources\images\32_TriggerCustomEventNode.png">

`TriggerCustomEvents` takes an input event and sends the event throughout the program that is picked up by the receiver (a "listener" node). To have this function correctly, you need to give it an object to send the event to (in our case, the manager) and the name of the event you are trying to call.

<img src="PracResources\images\33_CustomEventNode.png">

`CustomEvents` functions similarly to our `OnTriggerEnter/Stay/Exit` nodes in theory. Upon "hearing" an event, it begins whatever code sequence has been hooked up to this. This practice of creating events and listeners is arguably an advanced topic, but today we're going to be doing a very simple implementation of it.

In our `Player` Script, we need to expand our `OnTriggerEnter2D` sequence and create a new `OnTriggerExit2D` sequence. Make a new variable that references our `CameraManager` script graph (Type: `Script Machine`), and change the following in your current `OnTriggerEnter2D` functionality:

1. Verify you have implemented the bug fix outlined at the top of this prac
2. Take the `False` argument of the initial `IsOnLayer` node sequence, and pipe it into another `IsOnLayer` node (as pictured below). Feed the Object input to be the `Collider` output (from the `OnTriggerEnter2D` node) and the LayerMask to be the `CameraConfiner` layer
3. Hook this into an If node, and set the output of `True` to feed into a `TriggerCustomEvent` node. The `Target` input of this `TriggerCustomEvent` node should be the CameraManager script machine - make a new variable for this if you have not done so already
   * The name of the event should be called `confinerEntered`

<img src="PracResources\images\34_NewOnTriggerEnter.png">

Repeat this logic for `OnTriggerExit2D`, however, change the name of the event to instead be called `confinerExited`

<img src="PracResources\images\35_NewOnTriggerExit.png">

Once these have been made, navigate to our `CameraManager` script graph:

1. Add a `Custom Event` node and a `Cinemachine Camera Set Enabled` node.
   * The `Cinemachine Camera Set Enabled` node is quite obvious in terms of its purpose, and doesn't really need a thorough introduction
2. Make the output `Trigger` flow of the `Custom Events` node flow into the `Cinemachine Camera Set Enabled`
3. Set the `Name` of the event to be "`confinerEntered`"
4. Make a `GetVariable` node, set it to your `confinerCam` variable, and feed it into the `Cinemachine Camera Set Enabled` node
5. Repeat the above steps (copy/paste the nodes if you wish), but change the event name to instead be `confinerExited`
6. Check the boolean value for each `Cinemachine Camera Set Enabled` appropriately

You should end up with something like this:

<img src="PracResources\images\36_CameraManagerImplementation.png">

...and if everything is done correctly, **Play** your game and you should have something like this:

<img src="PracResources\images\GIF_FinalImplementation.gif">


|   | But what about the other camera variable...                                                                                                                                                                                                                                                               |   |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
|   | You may notice that we didn't even end up using the`followCam` variable. It is still good practice to implement things that you will likely use during development, and remove them later if you feel it is necessary. You may want to use this variable for other activities, but remove it if you wish. |   |
|   |                                                                                                                                                                                                                                                                                                           |   |

You are required to complete at least one challenge today. These are quite simple compared to the above task, however, so if these are too easy, have a go at the advanced task.

## Suggested Challenge: Tracking Between Two Targets

There are two approaches you would typically use for this. One is technically challenging, but is transferable to other engines/mediums. The other is Cinemachine specific, and is simpler in theory, but will take a bit of exploring to figure out *exactly* how to implement:

1. Create a "midpoint" object as a child of the Player. This midpoint (calculated with `OnUpdate`, in a script) would constantly require a position check between the player and the target object (or nothing if there is no target). This is the technical implementation, but it is usually how this sort of system is implemented
2. Create a `Cinemachine Camera` on two objects (ideally the player and the target), and simply **blend** between the two objects.
   * You could probably implement this easily using a `Cinemachine Mixing Camera`


|   | Why not simply select`Follow` instead of `Position Composer`                                                                                                                         |   |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
|   | For 2D,`Position Composer` is suggested as you will need to tell Unity to offset the camera's Z position. For more information about this issue/behaviour, please check the lecture. |   |
|   |                                                                                                                                                                                      |   |

## Suggested Challenge: Add Jumping to the Physics_Player, and Add Appropriate Camera Logic

Revise the material from last week if you are unsure how you should go about this, but you should have a good idea from the `AddForce` sections of the previous prac

Consider the following:
* You may want to tweak the `Dead Zones` on your camera
* You may want to interpolate between a second `Cinemachine Camera`, or code some interactions if you want the jumping to "feel" different from simply moving up on the Y-axis
  * e.g. you might want the camera to follow if the player moves up/down the Y axis (e.g. when falling), but not follow the player when jumping
* Think if anything should change when jumping in/out of a confiner

## Moderate Challenge: Activating/Deactivating Camera "Noise"

|   |                                                                                                                                                        Link to Assignment                                                                                                                                                        |   |
| --- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | --- |
|   | Note that a**good** (deliberate & carefully designed) implementation of this is quite novel/high level, and would easily accomplish several features required for the assignment (e.g. Trigger, Cinemachine usage). Additionally, a clever design/implementation of this could push your assignment grade into higher categories |   |
|   |                                                                                                                                                                                                                                                                                                                                 |   |

In **Tiled**, make a new trigger tile (on a new Layer), and place this in one of your scenes. Then, implement functionality so that camera noise/shake is turned on (either temporarily or until triggered again) when the Player steps over that tile.

You could either affect an existing camera, or dynamically change to a new camera entirely (using trigger events/visual scripting).

## Completed Tasks

To verify that you've completed all of the content in this prac, check you have done the following:

* Implemented a Cinemachine Camera, alongside a Cinemachine Brain
* Implemented a tracking camera using Cinemachine
* Implemented a static/confiner camera using Cinemachine
* Implemented a transition between the two Cinemachine Cameras, using Event Triggers

### Show your demonstrator

To show your understanding of this weeks content to your prac demonstrator, you may wish to show:

* The transition between the two Cinemachine cameras
* One of the challenges you have completed
* Your implementation of Cinemachine in your assignment