# Pick-and-Place Tutorial: Part 1

This part includes downloading and installing the Unity Editor, setting up a basic Unity scene, and importing a robot using the URDF Importer.
> Note: If you are familiar with Unity and the URDF Importer, or just want to skip directly to setting up ROS, you can do so by opening Assets/Scenes/Part1Done and going directly to [Part 2](2_ros_tcp.md).

**Table of Contents**
  - [Setting up the Unity Scene](#setting-up-the-unity-scene)
  - [Create Unity scene with imported URDF](#setting-up-the-robot)
  - [Troubleshooting](#troubleshooting)
  - [Resources](#resources)
  - [Proceed to Part 2](#proceed-to-part-2)

---

## Setting up the Unity Scene
1. Clone this repo to a location on your local machine:
    ```bash
    git clone --recurse-submodules https://github.com/Unity-Technologies/Unity-Robotics-Hub.git
    ```

1. Install [Unity Hub](https://unity3d.com/get-unity/download).
  
1. Go to the [Unity 2020.2 Beta website](https://unity3d.com/unity/beta/2020.2.0b9) to install this project's version of Unity: **2020.2.0b9**. 
   
1. Click the "Add" button in the top right of the "Projects" tab on Unity Hub, and navigate to and select the PickAndPlaceProject directory (`./Unity-Robotics-Hub/tutorials/pick_and_place/PickAndPlaceProject/`) to add the tutorial project to your Hub.

   ![](img/hub_addproject.png)

1. Click the newly added project to open it.

5. Unity should open the project to a scene titled `EmptyScene`.  If it did not, you can find it in the Project browser in the Assets/Scenes directory, and double-click to open it now.
    > Note: If you have some experience with Unity and would like to skip the scene setup portion, you can open the scene named `TutorialScene` now and skip ahead to [Setting up the robot](#setting-up-the-robot).

    > Note: Only one Unity scene should be open at a time. If you see multiple scenes open in the Hierarchy view, double-click the desired scene, e.g. `Assets/Scenes/EmptyScene`, to open it and close the other scenes.

6. In the Unity Project window, navigate to `Assets/Prefabs`. Select the Table prefab, and click and drag it into the Hierarchy window. The table should appear in the Scene view. Then, select and drag the Target into the Hierarchy window, as well as the TargetPlacement. They should appear to sit on the table.

    ![](img/1_cube.png) 

7. Select the `Main Camera` in the Hierarchy. Move the camera to a more convenient location for viewing the robot by assigning the `Main Camera`'s Position to `(0, 1.4, -0.7)`, and the Rotation to `(45, 0, 0)` in the Inspector.

## Setting Up the Robot 

> Note: Presumably when you opened this project, the Package Manager automatically checked out and built the URDF-Importer package for you. You can double-check this now by looking for `Packages/URDF-Importer` in the Project Browser or by opening the Package Manager window.

1. Open the Physics Project Settings (Edit > Project Settings > Physics) and ensure the `Solver Type` is set to `Temporal Gauss Seidel`. This prevents erratic behavior in the joints that may be caused by the default solver.

    ![](img/1_physics.png)

1. Find and select the URDF file in the Project window (`Assets/URDF/niryo_one/niryo_one.urdf`). From the menu, click `Assets -> Import Robot from URDF`, or in the Project window, right click on the selected file and click `Import Robot from URDF`.
    > Note: The file extension may not appear in the Project window. The niryo_one.urdf file will appear in the root of the `Assets/URDF/niryo_one` directory.
  
1. Keep the default Y Axis type and VHACD mesh decomposer in the Import menu and click `Import URDF`.
  
    > Note: Default mesh orientation is Y-up, which is supported by Unity, but some packages often use Z-up and X-up configuration.

    > Note: VHACD algorithm produces higher quality convex hull for collision detection than the default algorithm.

    > Note: The world-space origin of the robot is defined in its URDF file. In this sample, we have assigned it to sit on top of the table, which is at `(0, 0.63, 0)` in Unity coordinates.

    ```xml
    <joint name="joint_world" type="fixed">
        <parent link="world" />
            <child link="base_link" />
        <origin xyz="0 0 0.63" rpy="0 0 0" />
    </joint>
    ```

    > Note: Going from Unity world space to ROS world space requires a conversion. Unity's `(x,y,z)` is equivalent to the ROS `(z,-x,y)` coordinate.

1. On the Controller script of the top-level `niryo_one` object, set the Stiffness to `10000` ,the Damping to `100` and `Force Limit` to `1000`. Set the Speed to `30` and the Acceleration to `10`.
    > Note: You can find information on how these parameters are used in calculations by articulation bodies by referencing [this](https://github.com/Unity-Technologies/Unity-Robotics-Hub/blob/master/tutorials/urdf_importer/urdf_appendix.md#guide-to-write-your-own-controller) technical guide for writing a custom controller. For our purposes, these settings will allow the robot to stay in position without the joints slipping.
  
    ![](img/1_controller.png)
  
1. In the Hierarchy window, click the arrow to the left of the name to expand the GameObject tree, down to `niryo_one/world/base_link`. Toggle on `Immovable` for the `base_link`.

    ![](img/1_base.png)

    > Note: A controller is pre-built in the Unity URDF Importer to help showcase the movement of the Niryo. The Controller script is added to the imported URDF by default. This will add FKrobot and Joint Control components at runtime. The Controller script can be found in the project at `Assets/Packages/URDF Importer/Runtime/Controller/Controller.cs`.

1. Press Play. If everything imported correctly, no errors should appear in the Console window. The robot arm should stay “mounted” to the table, and nothing should fall through the floor.
  
    > Note: Using the Controller, joints can be selected using the arrow keys. Use the left/right arrow keys to navigate through the joints, where the selected index will be highlighted in red. Use the up/down arrow keys to control the selected joint movement. The Controller script on the niryo_one object will describe the actively `Selected Index` as well as the `Joint Name`.

   ![](img/1_end.gif)

---

## Troubleshooting
- If you are not seeing `Import Robot from URDF` in the `Assets` menu, check the console for compile errors. The project must compile correctly before the editor tools become available. 
- If the robot appears loose/wiggly or is not moving with no console errors, ensure that the Stiffness and Damping values on the Controller script of the `niryo_one` object are set to `10000` and `100`, respectively.

---

## Resources

- More on the URDF Importer tool [here](https://github.com/Unity-Technologies/URDF-Importer)
- Unity [Articulation Body Documentation](https://docs.unity3d.com/2020.1/Documentation/ScriptReference/ArticulationBody.html)
- For an introduction to Unity, check out the [Roll-a-Ball tutorial](https://learn.unity.com/project/roll-a-ball)

---


### Proceed to [Part 2](2_ros_tcp.md).
