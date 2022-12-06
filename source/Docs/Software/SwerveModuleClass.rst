##################
SwerveModule Class
##################

The SwerveModule class initializes the swerve module on the robot. The method ``getState`` returns a new object of the ``SwerveModuleState`` class, 
allowing us to know the current state according to the linear speed of the throttle in meters per second and the degrees the encoder has rotated. The 
method ``setState`` gets the current heading of the drive and takes a ``SwerveModuleState`` object. The method will use a PID controller to turn the 
drive to the desired heading at a desired speed.

Private Members
****************
.. code-block:: java
    
        // Initialize rotor & throttle motors 
        private WPI_TalonFX mRotor;
        private WPI_TalonFX mThrottle;
    
        // Initialize rotor encoder
        private WPI_CANCoder mRotorEncoder; 
    
        // Initialize rotor PID controller
        private PIDController mRotorPID;

Constructors
**********
Parameters
=========
.. code-block:: java
    public SwerveModule(int throttleID, int rotorID, int rotorEncoderID, double rotorOffsetAngleDeg)

* ``int throttleID`` - ID of throttle

* ``int rotorID`` - ID of rotors

* ``int rotorEncoderID`` - ID of rotor encoder

* ``double rotorOffsetAngleDeg`` - Offset value for rotor encoder

Assigning member variables
=================

.. code-blocK:: java

    mThrottle = new WPI_TalonFX(throttleID);
    mRotor = new WPI_TalonFX(rotorID);
    mRotorEncoder = new WPI_CANCoder(rotorEncoderID);

Creates new objects of throttle, rotor, and encoder and assigns them to specific IDs that are the parameters from the constructor.

Setting factory default
========

.. code-block:: java

    mThrottle.configFactoryDefault();
    mRotor.configFactoryDefault();
    mRotorEncoder.configFactoryDefault();

Resets throttle, rotor, and encoder to factory defaults.

Configuring rotors
========

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
=========

.. code-block:: java

    // rotor encoder config
    mRotorEncoder.configAbsoluteSensorRange(AbsoluteSensorRange.Signed_PlusMinus180);
    mRotorEncoder.configMagnetOffset(rotorOffsetAngleDeg);
    mRotorEncoder.configSensorDirection(SwerveConstants.kRotorEncoderDirection); 
    mRotorEncoder.configSensorInitializationStrategy(SensorInitializationStrategy.BootToAbsolutePosition);

Configuring rotor PID
============

.. code-block:: java

    // Rotor PID config 
    mRotorPID = new PIDController(SwerveConstants.kRotor_kP, SwerveConstants.kRotor_kI, SwerveConstants.kRotor_kD);
    mRotorPID.enableContinuousInput(-180, 180);

Configuring throttle
=========

.. code-block:: java

    // throttle config
    mThrottle.configSelectedFeedbackSensor(FeedbackDevice.IntegratedSensor);
    mThrottle.configVoltageCompSaturation(Constants.kVoltageCompensation);
    mThrottle.enableVoltageCompensation(true);
    mThrottle.setNeutralMode(NeutralMode.Brake);

Methods
******

``getState()``
============

.. code-block:: java
    
    public SwerveModuleState getState() {
        double throttleVelocity = 0.0;
    }

``setState()``
=============

.. code-block:: java

    public void setState(SwerveModuleState state) {
        SwerveModuleState optimizedState = SwerveModuleState.optimize(state, getState().angle);
        double rotorOutput = mRotorPID.calculate(getState().angle.getDegrees(), optimizedState.angle.getDegrees());

        mRotor.set(rotorOutput);
        mThrottle.set(optimizedState.speedMetersPerSecond);
    }

