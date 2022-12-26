######
TeleOp
######

This section will be explaining manual robot control (Teleop). Teleop is short for teleoperation, or remote operation. 
It indicates the operation of a system or machine at a distance. This documentation will be referring to the manual drive class, and specific parts of the RobotContainer class.
More information about RobotContainer can be found `here <https://docs.wpilib.org/en/stable/docs/software/commandbased/structuring-command-based-project.html#robotcontainer>`_.

Driving
=======

Field oriented driving involves an omnidirectional drive system that provides strafing motion in all directions and rotations with respect to the field. 
When the driving joystick is pushed forward, the robot moves forward with respect to the field. 
For example, if the robot is facing east and the joystick is pushed forward, the robot would move north.

Compared to robot oriented driving, the joystick's movement would be relative to the robot instead of the field.
For example, if the robot was facing east and the joystick is pushed forward, the robot would move east.

Joystick inputs
===============

The X-axis of the bot controls the robot's left and right position relative to the field, while the Y-axis controls movement to and away from the driver. 
The Z-axis rotates the robot.
Here's an example of a `swerve drive <https://www.youtube.com/watch?v=50ZRrYFWPIc&ab_channel=FirstUpdatesNow>`_ and how it's controlled.


Constructor
===========

.. code-block:: java

    private final Swerve mSwerve;
    // Initializes Swerve object
    private final XboxController mController;
    // Initializes Controller object
    
    public ManualDrive(Swerve drive, XboxController controller) {
        mSwerve = drive;
        // Assigns Swerve object to parameter
        mController = controller;
        // Assigns Controller object to parameter

        addRequirements(mSwerve);
        // Adds requirement of Swerve argument
    }

**Parameters:**
---------------

1. ``drive`` - a swerve object for the drive
2. ``controller`` - a XboxController method for the controller


Methods
=======

.. code-block:: java

    @Override 
    public void execute() {
        mSwerve.drive(mController.getLeftY(), mController.getLeftX(), mController.getRightX(), true);
        // Drives with XSpeed, YSpeed, zSpeed
        // True/false for field oriented driving
    }

   
The ManualDrive class is also used in the RobotContainer class, where the structure of the robot is declared.


In ``RobotContainer.java``
==========================

.. code-block:: java

    private final ManualDrive mManualDriveCommand = new ManualDrive(mSwerve, mController);
    // Creates a new instance of ManualDrive passing Swerve and Controller as parameters.
    
    public RobotContainer() {
        // Configure the button bindings
        configureButtonBindings();

        mSwerve.setDefaultCommand(mManualDriveCommand);
        //set ManualDrive to be executed when in manual control
    }
    