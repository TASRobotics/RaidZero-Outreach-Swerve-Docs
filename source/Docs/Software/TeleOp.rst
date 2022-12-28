######
TeleOp
######

This section will be explaining manual robot control (Teleop), which lasts 2 minutes and 15 seconds of the game.
This documentation will be referring to the manual drive class, and specific parts of the RobotContainer class.
More information about RobotContainer can be found `here <https://docs.wpilib.org/en/stable/docs/software/commandbased/structuring-command-based-project.html#robotcontainer>`_.

Driving
=======

Field oriented driving involves a drive system that provides strafing motion in all directions and rotations with respect to the field. 
For example, if the robot is facing east and the joystick is pushed forward, the robot would move north.

Compared to robot oriented driving, the joystick's movement would be relative to the robot instead of the field.
For example, if the robot was facing east and the joystick is pushed forward, the robot would move east.

.. figure:: ../Photos/Software/Field-robot-oriented.png
    :scale: 50%
    :alt: Field/robot oriented driving comparison


.. list-table:: **Field vs robot oriented driving**
   :widths: 25 50 50
   :header-rows: 1

   * - 
     - Field oriented
     - Robot oriented
   * - Requires IMU
     - Yes (IMU drift*)
     - No
   * - Joystick movement
     - Relative to field
     - Relative to robot


\* IMU drift may cause slight inaccuracy in field oriented driving.


Here's a demonstration of field oriented control:

.. raw:: html

    <iframe width="560" height="315" src="https://www.youtube.com/embed/50ZRrYFWPIc?start=21" title="Field oriented control" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


Drive command
=============

The code below shows the constructor for the ManualDrive class.

.. code-block:: java

    // Initializes Swerve object
    private final Swerve mSwerve;
    // Initializes Controller object
    private final XboxController mController;
    
    public ManualDrive(Swerve drive, XboxController controller) {
        // Assigns Swerve object to parameter
        mSwerve = drive;
        // Assigns Controller object to parameter
        mController = controller;

        // Adds requirement of Swerve argument
        addRequirements(mSwerve);
    }



**Parameters:**
---------------

1. ``drive`` - a swerve object for the drive
2. ``controller`` - a XboxController method for the controller

A drive command has to be created to manually operate the robot. Calling the constructor will create the drive command, where it will then be used in RobotContainer.


Calling drive command
=====================

.. code-block:: java

    @Override 
    public void execute() {
        // Drives with XSpeed, YSpeed, zSpeed
        // True/false for field oriented driving
        mSwerve.drive(mController.getLeftY(), mController.getLeftX(), mController.getRightX(), true);
    }

   
The ManualDrive class is also used in the RobotContainer class, where the structure of the robot is declared.


In ``RobotContainer.java``
--------------------------

.. code-block:: java

    // Creates a new instance of ManualDrive passing Swerve and Controller as parameters
    private final ManualDrive mManualDriveCommand = new ManualDrive(mSwerve, mController);

    public RobotContainer() {
        // Configure the button bindings
        configureButtonBindings();

        //set ManualDrive to be executed when in manual control
        mSwerve.setDefaultCommand(mManualDriveCommand);
    }

A new instance of ManualDrive is created, which also creates the drive command.
To use the drive command in RobotContainer, the default command of the Swerve object is set to the instance created. 
When Teleop starts, the ManualDrive command will be automatically executed.