##################
SwerveModule Class
##################

The SwerveModule class initializes the swerve module on the robot. The method ``getState`` returns a new object of the ``SwerveModuleState`` class, 
allowing us to know the current state according to the linear speed of the throttle in meters per second and the degrees the encoder has rotated. The 
method ``setState`` gets the current heading of the drive and takes a ``SwerveModuleState`` object. The method will use a PID controller to turn the 
drive to the desired heading at a desired speed.

The image below shows an example of a swerve module.

.. figure:: swerveModule.jpg
    :alt: swerveModule image

    The blue circle shows the throttle; orange circle represents the rotor; pink circle represents the encoder

Instantiate instance variables
==============================
.. code-block:: java
    
        // Initialize rotor & throttle motors 
        private WPI_TalonFX mRotor;
        private WPI_TalonFX mThrottle;
    
        // Initialize rotor encoder
        private WPI_CANCoder mRotorEncoder; 
    
        // Initialize rotor PID controller
        private PIDController mRotorPID;

Constructor
===========

Creates an instance of SwerveModule object

.. code-block:: java

    public SwerveModule(int throttleID, int rotorID, int rotorEncoderID, double rotorOffsetAngleDeg)

Parameters
==========

* ``int throttleID`` - ID of throttle

* ``int rotorID`` - ID of rotors

* ``int rotorEncoderID`` - ID of rotor encoder

* ``double rotorOffsetAngleDeg`` - Offset value for rotor encoder

Assigning member variables
==========================

.. code-block:: java

    mThrottle = new WPI_TalonFX(throttleID);
    mRotor = new WPI_TalonFX(rotorID);
    mRotorEncoder = new WPI_CANCoder(rotorEncoderID);

Creates new objects of throttle, rotor, and encoder and assigns them to specific IDs that are the parameters from the constructor.

Configure motors and sensors
============================

.. code-block:: java

    mThrottle.configFactoryDefault();
    mRotor.configFactoryDefault();
    mRotorEncoder.configFactoryDefault();

Resets throttle, rotor, and encoder to factory defaults.

Configuring rotors
==================

.. code-block:: java

    // rotor config
    mRotor.setInverted(SwerveConstants.kRotorMotorInversion); 
    mRotor.configVoltageCompSaturation(Constants.kVoltageCompensation);
    mRotor.enableVoltageCompensation(true);
    mRotor.setNeutralMode(NeutralMode.Brake);

1. Sets inversion of rotors (true/false)

2. Configures voltage compensation saturation (double)

    * Voltage compensation saturation: Ensures rotor receives same amount of voltage while battery charge decreases

3. Toggles voltage compensation (true/false)

4. Sets neutral mode of rotors (Coast/Brake)

    * Neutral mode: State of neutral throttle output

Configuring rotor encoder
=========================

.. code-block:: java

    // rotor encoder config
    mRotorEncoder.configAbsoluteSensorRange(AbsoluteSensorRange.Signed_PlusMinus180);
    mRotorEncoder.configMagnetOffset(rotorOffsetAngleDeg);
    mRotorEncoder.configSensorDirection(SwerveConstants.kRotorEncoderDirection); 
    mRotorEncoder.configSensorInitializationStrategy(SensorInitializationStrategy.BootToAbsolutePosition);

1. Configures absolute sensor range for rotor encoder (``AbsoluteSensorRange`` object)

    * Sets desired value range for rotor encoder

2. Configures magnet offset of rotors (``rotorOffsetAngleDeg`` param from constructor)

    * Magnet offset: Adjusts (offsets) zero point of encoder

3. Configures sensor direction or rotor encoder (true/false)

4. Configures initializiation of rotor encoder (``SensorInitializationStrategy`` object)

    * ``BootToZero``
        * Initializes rotor encoder to 0
    * ``BootToAbsolutePosition``
        * Initializes to absolute position of encoder

Configuring rotor PID
=====================

.. code-block:: java

    // Rotor PID config 
    mRotorPID = new PIDController(SwerveConstants.kRotor_kP, SwerveConstants.kRotor_kI, SwerveConstants.kRotor_kD);
    mRotorPID.enableContinuousInput(-180, 180);

1. Creates a new object of PID controller with imported swerve constants:

    * kP - Proportional
    * kI - Integral
    * kD - Derivative

2. Enables continuous input on a range from -180 degrees to 180 degrees, measured on a circular scale.

Configuring throttle
====================

.. code-block:: java

    // throttle config
    mThrottle.configSelectedFeedbackSensor(FeedbackDevice.IntegratedSensor);
    mThrottle.configVoltageCompSaturation(Constants.kVoltageCompensation);
    mThrottle.enableVoltageCompensation(true);
    mThrottle.setNeutralMode(NeutralMode.Brake);

1. Configures remote feedback device for motor controller
2. Configures voltage compensation saturation (double)
3. Toggles voltage compensation saturation (true/false)
4. Sets neutral mode for throttle (Coast/Brake)

Methods
=======

``getState()``
==============

.. code-block:: java
    
    public SwerveModuleState getState() {
        double throttleVelocity = 0.0;
    }

1. ``throttleVelocity`` - Calculates linear speed of swerve module based on configuration in meters per second 
2. Returns a new object of Swerve Module State constructed with the throttle velocity and the degrees of the absolute position of the encoder

``setState()``
==============

.. code-block:: java

    public void setState(SwerveModuleState state) {
        SwerveModuleState optimizedState = SwerveModuleState.optimize(state, getState().angle);
        double rotorOutput = mRotorPID.calculate(getState().angle.getDegrees(), optimizedState.angle.getDegrees());

        mRotor.set(rotorOutput);
        mThrottle.set(optimizedState.speedMetersPerSecond);
    }

1. ``optimizedState`` - Current state (angle) of SwerveModule
2. ``rotorOutput`` - Gets angle of current state & optimized state of SwerveModule, then calculates the values using the rotor PID controller
3. Sets rotor power to rotorOutput
4. Sets throttle to speed of optimized state in meters per second

