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


.. code-block:: java

    // Instance variables

    // Initialize rotor & throttle motors 
    private WPI_TalonFX mRotor;
    private WPI_TalonFX mThrottle;

    // Initialize rotor encoder
    private WPI_CANCoder mRotorEncoder; 

    // Initialize rotor PID controller
    private PIDController mRotorPID; 

    // Creates instance of SwerveModule
    public SwerveModule(int throttleID, int rotorID, int rotorEncoderID, double rotorOffsetAngleDeg) 
    {

        // Instantiate instance variables
        // Creates new objects of throttle, rotor, and encoder and assigns 
        // them to specific IDs that are the parameters from the constructor.
        mThrottle = new WPI_TalonFX(throttleID);
        mRotor = new WPI_TalonFX(rotorID);
        mRotorEncoder = new WPI_CANCoder(rotorEncoderID);

        //Resets throttle, rotor, and encoder to factory defaults.
        mThrottle.configFactoryDefault();
        mRotor.configFactoryDefault();
        mRotorEncoder.configFactoryDefault();

        // rotor config
        // Sets inversion of rotors (true/false)
        mRotor.setInverted(SwerveConstants.kRotorMotorInversion);
        // Configures voltage compensation saturation (double)
        mRotor.configVoltageCompSaturation(Constants.kVoltageCompensation);
        // Toggles voltage compensation (true/false)
        mRotor.enableVoltageCompensation(true);
        // Sets neutral mode of rotors (Coast/Brake)
        mRotor.setNeutralMode(NeutralMode.Brake); 

        // rotor encoder config
        // Configures absolute sensor range for rotor encoder (AbsoluteSensorRange object)
        mRotorEncoder.configAbsoluteSensorRange(AbsoluteSensorRange.Signed_PlusMinus180);
        // Configures magnet offset of rotors (rotorOffsetAngleDeg param from constructor)
        mRotorEncoder.configMagnetOffset(rotorOffsetAngleDeg);
        // Configures sensor direction or rotor encoder (true/false)
        mRotorEncoder.configSensorDirection(SwerveConstants.kRotorEncoderDirection); 
        // Configures initializiation of rotor encoder (SensorInitializationStrategy object)
        mRotorEncoder.configSensorInitializationStrategy(SensorInitializationStrategy.BootToAbsolutePosition); 

        // Rotor PID config
        // Creates a new object of PID controller with imported swerve constants
        mRotorPID = new PIDController(SwerveConstants.kRotor_kP, SwerveConstants.kRotor_kI, SwerveConstants.kRotor_kD);
        // Enables continuous input on a range from -180 degrees to 180 degrees, measured on a circular scale
        mRotorPID.enableContinuousInput(-180, 180);
        
        // throttle config
        // Configures remote feedback device for motor controller
        mThrottle.configSelectedFeedbackSensor(FeedbackDevice.IntegratedSensor);
        // Configures voltage compensation saturation (double)
        mThrottle.configVoltageCompSaturation(Constants.kVoltageCompensation);
        // Toggles voltage compensation saturation (true/false)
        mThrottle.enableVoltageCompensation(true);
        // Sets neutral mode for throttle (Coast/Brake)
        mThrottle.setNeutralMode(NeutralMode.Brake);
    }



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

