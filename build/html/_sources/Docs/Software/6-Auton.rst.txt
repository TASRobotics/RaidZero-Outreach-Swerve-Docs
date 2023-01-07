##################
Autonomous Pathing
##################

There are many different ways to implement pathing. In this guide, we will
use `PathPlanner <https://github.com/mjansen4857/pathplanner>`_ to generate
and follow paths. 

Installation
************

.. note:: 

    Offical documentation can be found `here <https://github.com/mjansen4857/pathplanner/wiki>`_.

To start, install PathPlannerLib using this link:

.. code-block:: bash

    https://3015rangerrobotics.github.io/pathplannerlib/PathplannerLib.json

Next, download PathPlanner from the `Microsoft <https://www.microsoft.com/en-us
/p/frc-pathplanner/9nqbkb5dw909?cid=storebadge&ocid=badge&rtc=1&activetab=pivo
t:overviewtab>`_ or `Mac App <https://apps.apple.com/us/app/frc-pathplanner/id1593046876>`_
Store.

Path Generation
***************

1. Open PathPlanner, click "Switch Project" in the menu, and select the root 
   of your project. 

.. image:: ../Photos/PathPlanner1.PNG
    :scale: 50%

2. Go to "Settings" in the menu, and configure everything according to your robot. 
   Make sure to also select "Holonomic Mode". 

.. image:: ../Photos/PathPlanner2.PNG
    :scale: 50%

3. Finally, draw the paths you want the robot to follow. 

Path Following
**************

We will now extract the path made in PathPlanner and use it in our robot code.

In ``RobotContainer.java``, create an instance of ``PathPlannerTrajectory`` by 
extracting the path you made in PathPlanner. 

.. code-block:: java
    :linenos:

    private PathPlannerTrajectory mTrajectory = PathPlanner.loadPath(
        "PATH NAME", 
        SwerveConstants.kMaxVelocityMetersPerSecond, 
        SwerveConstants.kMaxAccelerationMetersPerSecond
    );

Next, create 3 different PID controllers that will be used to control robot
heading and movement in the x and y directions.

.. code-block:: java
    :linenos:

    // PID controller for movement in the X direction
    private PIDController mXController = new PIDController(
        SwerveConstants.kPathingX_kP, 
        SwerveConstants.kPathingX_kI, 
        SwerveConstants.kPathingX_kD
    );

    // PID controller for movement in the Y direction
    private PIDController mYController = new PIDController(
        SwerveConstants.kPathingY_kP, 
        SwerveConstants.kPathingY_kI, 
        SwerveConstants.kPathingY_kD
    );

    // PID controller for robot heading
    private PIDController mThetaController = new PIDController(
        SwerveConstants.kPathingTheta_kP, 
        SwerveConstants.kPathingTheta_kI, 
        SwerveConstants.kPathingTheta_kD
    );

.. warning::

    All constants used in the PID controllers must be tuned. X and Y controllers
    usually have the same constants, but the theta controller usually has different
    constants. Tune the X and Y controller constants by making the robot follow long
    straight paths. Once the X and Y controllers are tuned, tune the theta controller
    by making the robot follow a short path with a sharp turn.

Finally, create a ``SwerveControllerCommand`` that will be used to follow the path.

.. code-block:: java
    :linenos:

    private PPSwerveControllerCommand mCommand = new PPSwerveControllerCommand(
        mTrajectory, 
        mSwerve::getPose, 
        SwerveConstants.kSwerveKinematics, 
        mXController, 
        mYController, 
        mThetaController, 
        mSwerve::setModuleStates, 
        mSwerve
    );

Return the command in ``getAutonomousCommand()``.

.. code-block:: java
    :linenos:

    @Override
    public Command getAutonomousCommand() {
        // `andThen...` is used to stop the robot after the path is finished
        return command.andThen(() -> mSwerve.drive(0, 0, 0, false));
    }

This allows the robot to follow the path you made in PathPlanner in autonomous.

.. note::

    If the path following is inaccurate or violent, try changing some of the 
    PID controller constants. 

.. note::
    Check out the code we use in these docs on our `Github <https://github.com/
    TASRobotics/RaidZero-Swerve-Template>`_!