############
Swerve Class
############

This article will be explaining the swerve class. The Swerve class initializes 4 swerve modules together to work as the base of the robot. 
This class will utilize the swerve module class to create the 4 objects used for the 4 wheels of the robot base.

.. note:: 

    For API specific issues, please see the `WPILib API <https://www.youtube.com/watch?v=dQw4w9WgXcQ>`_
    , `CTRE API <https://api.ctr-electronics.com/phoenix/release/java/>`_, and the `REV Robotics API 
    <https://codedocs.revrobotics.com/java/com/revrobotics/package-summary.html>`_.


IMU Selection
=============

The image below is a Pigeon 2.0 sensor.

.. figure:: ../Photos/Software/Pigeon2_CTRE.webp
    :scale: 30%
    :alt: Pigeon 2.0 sensor

mImu is the Inertial Measurement Unit, which contains an accelerometer measure acceleration and the angle of the robot. 
For this documentation, the `Pigeon2 <https://store.ctr-electronics.com/pigeon-2/>`_ (`API <https://api.ctr-electronics.com/phoenix/release/java/com/ctre/phoenix/sensors/WPI_Pigeon2.html>`_)
will be used. 
There are also other inertials that are commonly used:
`navX <https://www.andymark.com/products/navx-mxp-robotics-navigation-sensor>`_ (`API <https://www.kauailabs.com/public_files/navx-mxp/apidocs/java/index.html>`_)
, `navX2 <https://www.andymark.com/products/navx2-mxp-robotics-navigation-sensor>`_ (`API <https://www.kauailabs.com/public_files/navx-mxp/apidocs/java/index.html>`_)
, `PigeonIMU <https://store.ctr-electronics.com/gadgeteer-pigeon-imu>`_ (`API <https://api.ctr-electronics.com/phoenix/release/java/com/ctre/phoenix/sensors/WPI_PigeonIMU.html>`_).

Constructor
===========

.. code-block:: java

    // Initialize IMU
    private final WPI_Pigeon2 mImu = new WPI_Pigeon2(SwerveConstants.kImuID);

    // Initializes the 4 swerve modules
    private final SwerveModule mLeftFrontModule, mRightFrontModule, mLeftRearModule, mRightRearModule;

    // Initializes odometry
    private SwerveDriveOdometry mOdometry;

    public Swerve() {
        
        // Initializes the 4 swerve modules with SwerveConstants      
        mLeftFrontModule = new SwerveModule(
            SwerveConstants.kLeftFrontThrottleID, 
            SwerveConstants.kLeftFrontRotorID, 
            SwerveConstants.kLeftFrontCANCoderID, 
            SwerveConstants.kLeftFrontRotorOffset
        );

        mRightFrontModule = new SwerveModule(
            SwerveConstants.kRightFrontThrottleID, 
            SwerveConstants.kRightFrontRotorID, 
            SwerveConstants.kRightFrontCANCoderID, 
            SwerveConstants.kRightFrontRotorOffset
        );

        mLeftRearModule = new SwerveModule(
            SwerveConstants.kLeftRearThrottleID, 
            SwerveConstants.kLeftRearRotorID, 
            SwerveConstants.kLeftRearCANCoderID, 
            SwerveConstants.kLeftRearRotorOffset
        );

        mRightRearModule = new SwerveModule(
            SwerveConstants.kRightRearThrottleID, 
            SwerveConstants.kRightRearRotorID, 
            SwerveConstants.kRightRearCANCoderID, 
            SwerveConstants.kRightRearRotorOffset
        );

        // Initializes SwerveDriveOdometry object with SwerveKinematics constant and robot heading
        mOdometry = new SwerveDriveOdometry(SwerveConstants.kSwerveKinematics, mImu.getRotation2d());
    }

Methods
=======

``drive()``
***********

Updates Chassis speeds based on controller XYZ values.

.. code-block:: java

    public void drive(double xSpeed, double ySpeed, double zSpeed, boolean fieldOriented) {
        SwerveModuleState[] states = null;
        if(fieldOriented) {
            states = SwerveConstants.kSwerveKinematics.toSwerveModuleStates(
                //gyro rotation value needed if using field orientation
                ChassisSpeeds.fromFieldRelativeSpeeds(xSpeed, ySpeed, zSpeed, mImu.getRotation2d())
            );
        } else {
            states = SwerveConstants.kSwerveKinematics.toSwerveModuleStates(
                new ChassisSpeeds(xSpeed, ySpeed, zSpeed)
            );
        }
        setModuleStates(states);
    }

**Parameters:**
---------------

1. ``xSpeed`` - x-axis controlled by left stick to move the robot in 2d space
2. ``ySpeed`` - y-axis controlled by left stick to move the robot in 2d space
3. ``zSpeed`` - Desired speed of rotation, controlled by right stick
4. ``fieldOriented`` - Determines if bot's movement is oriented to the field


``getModuleStates()``
*********************

Outputs the current state of the 4 drive swerve modules.

.. code-block:: java

    public SwerveModuleState[] getModuleStates() {
        return new SwerveModuleState[]{
            mLeftFrontModule.getState(), 
            mRightFrontModule.getState(), 
            mLeftRearModule.getState(), 
            mRightRearModule.getState()
        };
    }


**Return:**
-----------
Returns SwerveModuleState array with rotor throttle & position


``setModuleStates()``
*********************

Sets the state of the 4 drive swerve modules.

.. code-block:: java

    public void setModuleStates(SwerveModuleState[] desiredStates) {
        SwerveDriveKinematics.desaturateWheelSpeeds(desiredStates, 1);
        mLeftFrontModule.setState(desiredStates[0]);
        mRightFrontModule.setState(desiredStates[1]);
        mLeftRearModule.setState(desiredStates[2]);
        mRightRearModule.setState(desiredStates[3]);
    }

**Parameters:**
---------------

1. ``desiredStates`` - Array of desired `SwerveModuleState <https://first.wpi.edu/wpilib/allwpilib/docs/release/java
   /edu/wpi/first/math/kinematics/SwerveModuleState.html>`_, order: [leftFront, leftRear, rightFront, rightRear]

``getPose()``
*************

Gets the current position of the robot.

.. code-block:: java

    public Pose2d getPose() {
        return mOdometry.getPoseMeters();
    }

**Return:**
-----------

    New `Pose2d <https://first.wpi.edu/wpilib/allwpilib/docs/release/java
    /edu/wpi/first/math/geometry/Pose2d.html>`_ representing robot position on the field in meters


``setPose()``
*************

Sets odometry position to a given x y position and angle

.. code-block:: java

    public void setPose(Pose2d pose) {
        mOdometry.resetPosition(pose, mImu.getRotation2d());
    }

**Parameters:**
---------------

1. ``pose`` - A `Pose2d <https://first.wpi.edu/wpilib/allwpilib/docs/release/java/edu/wpi/first/math/geometry/Pose2d.html>`_ object with the robot position and angle