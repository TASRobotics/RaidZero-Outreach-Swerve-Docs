####################
Swerve Configuration
####################

Before you can start programming the swerve, you need to collect constants 
specific to your robot. These constants will be used to configure the swerve. 
All constants will be located in the ``Constants.java`` file. 

Motor & Sensor IDs
******************

Record the CAN IDs of the rotor and throttle motors. 

.. code-block:: java

    // Rotor IDs
    public static final int kLeftFrontRotorID = 0;
    public static final int kRightFrontRotorID = 0;
    public static final int kLeftRearRotorID = 0;
    public static final int kRightRearRotorID = 0;

    // Throttle IDs
    public static final int kLeftFrontThrottleID = 0;
    public static final int kRightFrontThrottleID = 0;
    public static final int kLeftRearThrottleID = 0;
    public static final int kRightRearThrottleID = 0;

Record the CAN or analog IDs of the rotor encoders and IMU (not applicable for navX).

.. code-block:: java

    // Rotor Encoder IDs
    public static final int kLeftFrontRotorEncoderID = 0;
    public static final int kRightFrontRotorEncoderID = 0;
    public static final int kLeftRearRotorEncoderID = 0;
    public static final int kRightRearRotorEncoderID = 0;

    // IMU ID
    public static final int kImuID = 0;

Rotor Encoder Offsets
*********************

In order for the angle of the swerve modules to be in sync, you must
compensate for potential encoder offsets. 

First, rotate the modules so that all wheels are facing in the same direction. 
Additionally, when positive power is applied to the throttle motors, all 
wheels should spin in the forward direction. 

.. figure:: ../Photos/UpsidedownSwerve.jpg
    :scale: 35%

    `source <https://www.youtube.com/watch?v=wCakzMfRPKs&ab_channel=Orbit1690>`_

Record the rotor encoder values for each module in the ``Constants.java`` file.

.. code-block:: java
    
    public static final double kLeftFrontRotorOffset = -1 * LEFT_FRONT_ANGLE;
    public static final double kRightFrontRotorOffset = -1 * RIGHT_FRONT_ANGLE;
    public static final double kLeftRearRotorOffset = -1 * LEFT_REAR_ANGLE;
    public static final double kRightRearRotorOffset = -1 * RIGHT_REAR_ANGLE;

.. note::
    
    The rotor offset will be added to the rotor encoder value. This is why
    the rotor offset is multiplied by -1. 

Rotor Motor & Encoder Inversion
*******************************

The rotor motor and encoder may need to be inverted. When positive power is applied 
to the rotor motor, the rotor encoder should increase in value. If this is not the
case, you will need to invert the rotor motor or encoder in the ``Constants.java`` file
by changing the boolean value of the motor/encoder to ``true``.

.. code-block:: java

    public static final boolean kRotorEncoderDirection = false;
    public static final boolean kRotorMotorInversion = false;

Swerve Kinematics
*****************

.. figure:: ../Photos/AnnotatedSwerve2.PNG
    :alt: Width & length of swerve
    :scale: 50%

Record the width and length of the robot (units in meters). Since 
positive X is forward, and positive Y is left, the configuration should 
look something like this. 

.. code-block:: java

    // Swerve module order: front left, front right, rear left, rear right
    public static final SwerveDriveKinematics kSwerveKinematics = new SwerveDriveKinematics(
        new Translation2d(LENGTH/2, WIDTH/2), 
        new Translation2d(LENGTH/2, -WIDTH/2), 
        new Translation2d(-LENGTH/2, WIDTH/2),
        new Translation2d(-LENGTH/2, -WIDTH/2)
    );

Extra Constants
***************

Max Speed/Acceleration
----------------------

Record the maximum speed and acceleration of the robot (meters).

.. code-block:: java

    public static final double kMaxVelocityMetersPerSecond = 0.0;
    public static final double kMaxAccelerationMetersPerSecond = 0.0;

Wheel Diameter
--------------

Record the diameter of the swerve wheels (meters).

.. code-block:: java

    public static final double kWheelDiameterMeters = 0.0;

Throttle Gear Ratio
-------------------

Record the throttle gear ratio (number of turns it takes the motor to rotate 
the wheel one revolution)

.. code-block:: java

    public static final double kThrottleGearRatio = 0.0; 

Throttle Velocity Conversion Constant
-------------------------------------

Finally, with the constants above, you can calculate the conversion constant that will 
be used to convert from throttle encoder velocity to linear velocity (meters/second).

.. tabs::

    .. tab:: Spark Max (Neo)

        :math:`conversion = \frac{1}{gear ratio} \times \frac{1}{60} \times {wheel diameter} \times \pi`

        .. code-block:: java

            public static final double kThrottleVelocityConversionFactor = 
                1/kThrottleGearRatio/60*kWheelDiameterMeters*Math.PI;

    .. tab:: Talon FX (Falcon 500)

        :math:`conversion = \frac{1}{gear ratio} \times \frac{1}{2048} \times {wheel diameter} \times \pi \times 10`

        .. note::

            The :math:`10` is used to convert from 100ms to 1s, since the Talon FX 
            uses 100ms as the time unit for velocity. The :math:`\frac{1}{2048}` is 
            used to convert from Falcon encoder ticks to rotations.

        .. code-block:: java

            public static final double kThrottleVelocityConversionFactor = 
                1/kThrottleGearRatio/2048*kWheelDiameterMeters*Math.PI*10;