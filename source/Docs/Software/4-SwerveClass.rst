############
Swerve Class
############

The swerve class combines 4 swerve modules to control the swerve as a whole. 

.. note:: 

    For API specific issues, please see the `WPILib API <https://www.youtube.com/watch?v=dQw4w9WgXcQ>`_
    , `CTRE API <https://api.ctr-electronics.com/phoenix/release/java/>`_, and the `REV Robotics API 
    <https://codedocs.revrobotics.com/java/com/revrobotics/package-summary.html>`_.

Constructor
***********

.. code-block:: java
    :linenos:

    // Initialize IMU
    private final WPI_Pigeon2 mImu = new WPI_Pigeon2(SwerveConstants.kImuID);

    private final SwerveModule mLeftFrontModule, mRightFrontModule, mLeftRearModule, mRightRearModule;
    private SwerveDriveOdometry mOdometry;

    public Swerve() {
        // Instantiate swerve modules - each representing unique module on the robot
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

        // Instantiate odometry - used for tracking position
        mOdometry = new SwerveDriveOdometry(SwerveConstants.kSwerveKinematics, mImu.getRotation2d());
    }

Methods
*******

Updates odometry with current module state.

periodic
========

.. code-block:: java
    :linenos:

    @Override
    public void periodic() {
        // Updates odometry with current module state
        mOdometry.update(
            mImu.getRotation2d(), 
            getModuleStates()[0], 
            getModuleStates()[1],
            getModuleStates()[2],
            getModuleStates()[3]
        );
    }

drive
=====

Moves chassis in desired direction & rotation.

.. code-block:: java
    :linenos:

    public void drive(double xSpeed, double ySpeed, double zSpeed, boolean fieldOriented) {
        SwerveModuleState[] states = null;
        if(fieldOriented) {
            states = SwerveConstants.kSwerveKinematics.toSwerveModuleStates(
                // IMU used for field oriented control
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
"""""""""""""""

    1. ``xSpeed`` - percent power in the X direction
    2. ``ySpeed`` - percent power in the Y direction
    3. ``zSpeed`` - percent power for rotation
    4. ``fieldOriented`` - configure robot movement style (field or robot oriented)


getModuleStates
===============

Outputs the current state of the 4 drive swerve modules.

.. code-block:: java
    :linenos:

    public SwerveModuleState[] getModuleStates() {
        return new SwerveModuleState[]{
            mLeftFrontModule.getState(), 
            mRightFrontModule.getState(), 
            mLeftRearModule.getState(), 
            mRightRearModule.getState()
        };
    }


**Return:**
"""""""""""

    Return states of all modules in a SwerveModuleState array (Order: 
    [leftFront, rightFront, leftRear, rightRear]).

setModuleStates
===============

Sets the state of the 4 drive swerve modules.

.. code-block:: java
    :linenos:

    // Swerve module order: [leftFront, leftRear, rightFront, rightRear]
    public void setModuleStates(SwerveModuleState[] desiredStates) {
        SwerveDriveKinematics.desaturateWheelSpeeds(desiredStates, 1);
        mLeftFrontModule.setState(desiredStates[0]);
        mRightFrontModule.setState(desiredStates[1]);
        mLeftRearModule.setState(desiredStates[2]);
        mRightRearModule.setState(desiredStates[3]);
    }

**Parameters:**
"""""""""""""""

    1. ``desiredStates`` - Array of desired `SwerveModuleState <https://first.wpi.edu/wpilib/allwpilib/docs/release/java
       /edu/wpi/first/math/kinematics/SwerveModuleState.html>`_

getPose
=======

Gets the current position of the robot.

.. code-block:: java
    :linenos:

    public Pose2d getPose() {
        return mOdometry.getPoseMeters();
    }

**Return:**
"""""""""""

    New `Pose2d <https://first.wpi.edu/wpilib/allwpilib/docs/release/java
    /edu/wpi/first/math/geometry/Pose2d.html>`_ representing robot position on the field in meters.


setPose
=======

Sets odometry position to a given x, y, position, and angle.

.. code-block:: java
    :linenos:

    public void setPose(Pose2d pose) {
        mOdometry.resetPosition(pose, mImu.getRotation2d());
    }

**Parameters:**
"""""""""""""""

    1. ``pose`` - A `Pose2d <https://first.wpi.edu/wpilib/allwpilib/docs/release/java/edu/wpi/first/math/geometry/Pose2d.html>`_ 
       object with the robot position and angle