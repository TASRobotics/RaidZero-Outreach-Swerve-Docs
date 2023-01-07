##################
SwerveModule Class
##################

The SwerveModule class initializes the swerve module on the robot. The method 
``getState`` returns a new object of the ``SwerveModuleState`` class, allowing us to 
know the current state according to the linear speed of the throttle in meters 
per second and the degrees the encoder has rotated. The method ``setState`` gets 
the current heading of the drive and takes a ``SwerveModuleState`` object. The 
method will use a PID controller to turn the drive to the desired heading at a desired 
speed.

.. note:: 

    For API specific issues, please see the `WPILib API <https://www.youtube.com/watch?v=dQw4w9WgXcQ>`_
    , `CTRE API <https://api.ctr-electronics.com/phoenix/release/java/>`_, and the `REV Robotics API 
    <https://codedocs.revrobotics.com/java/com/revrobotics/package-summary.html>`_.

Constructor
***********

.. tabs::

    .. tab:: Talon FX & CANCoder

        .. code-block:: java
            :linenos:

            // Initialize rotor & throttle motors 
            private WPI_TalonFX mRotor;
            private WPI_TalonFX mThrottle;

            // Initialize rotor encoder
            private WPI_CANCoder mRotorEncoder; 

            // Initialize rotor PID controller
            private PIDController mRotorPID; 

            /**
             * Construct new SwerveModule
             * 
             * @param throttleID CAN ID of throttle motor
             * @param rotorID CAN ID of rotor motor
             * @param rotorEncoderID CAN ID of rotor encoder
             * @param rotorOffsetAngleDeg rotor encoder offset
             */
            public SwerveModule(int throttleID, int rotorID, int rotorEncoderID, double rotorOffsetAngleDeg) 
            {
                // Instantiate throttle motor
                mThrottle = new WPI_TalonFX(throttleID);

                // Instantiate rotor motor 
                mRotor = new WPI_TalonFX(rotorID);

                // Instantiate rotor absolute encoder
                mRotorEncoder = new WPI_CANCoder(rotorEncoderID);

                // Reset all configuration 
                // (technically optional, but good practice in case there are old 
                // configurations that can mess with the code)
                mThrottle.configFactoryDefault();
                mRotor.configFactoryDefault();
                mRotorEncoder.configFactoryDefault();

                // Configures rotor motor according to previously defined constants
                mRotor.setInverted(SwerveConstants.kRotorMotorInversion); 
                mRotor.configVoltageCompSaturation(Constants.kVoltageCompensation);
                mRotor.enableVoltageCompensation(true);
                mRotor.setNeutralMode(NeutralMode.Brake);

                // Configures rotor encoder according to previously defined constants
                mRotorEncoder.configAbsoluteSensorRange(AbsoluteSensorRange.Signed_PlusMinus180);
                mRotorEncoder.configMagnetOffset(rotorOffsetAngleDeg);
                mRotorEncoder.configSensorDirection(SwerveConstants.kRotorEncoderDirection); 
                mRotorEncoder.configSensorInitializationStrategy(
                    SensorInitializationStrategy.BootToAbsolutePosition
                );

                // Configures rotor PID controller according to previously defined constants
                mRotorPID = new PIDController(
                    SwerveConstants.kRotor_kP, 
                    SwerveConstants.kRotor_kI, 
                    SwerveConstants.kRotor_kD
                );

                // Continuous input considers the min & max to be the same point and 
                // automatically calculates the shortest route to the setpoint
                mRotorPID.enableContinuousInput(-180, 180);

                // Configures throttle motor according to previously defined constants
                mThrottle.configSelectedFeedbackSensor(FeedbackDevice.IntegratedSensor);
                mThrottle.configVoltageCompSaturation(Constants.kVoltageCompensation);
                mThrottle.enableVoltageCompensation(true);
                mThrottle.setNeutralMode(NeutralMode.Brake);
            }
        
    .. tab:: SPARK MAX & CANCoder

        .. code-block:: java
            :linenos:

            // Initialize rotor & throttle motors 
            private CANSparkMax mRotor;
            private CANSparkMax mThrottle;

            // Initialize throttle encoder
            private RelativeEncoder mThrottleEncoder;

            // Initialize rotor encoder
            private WPI_CANCoder mRotorEncoder; 

            // Initialize rotor PID controller
            private PIDController mRotorPID; 

            /**
             * Construct new SwerveModule
             * 
             * @param throttleID CAN ID of throttle motor
             * @param rotorID CAN ID of rotor motor
             * @param rotorEncoderID CAN ID of rotor encoder
             * @param rotorOffsetAngleDeg rotor encoder offset
             */
            public SwerveModule(int throttleID, int rotorID, int rotorEncoderID, double rotorOffsetAngleDeg) 
            {
                // Instantiate throttle motor & respective encoder
                mThrottle = new CANSparkMax(throttleID, MotorType.kBrushless);
                mThrottleEncoder = mThrottle.getEncoder();

                // Instantiate rotor motor 
                mRotor = new CANSparkMax(rotorID, MotorType.kBrushless);

                // Instantiate rotor absolute encoder
                mRotorEncoder = new WPI_CANCoder(rotorEncoderID);

                // Reset all configuration 
                // (technically optional, but good practice in case there are old 
                // configurations that can mess with the code)
                mThrottle.restoreFactoryDefaults();
                mRotor.restoreFactoryDefaults();
                mRotorEncoder.configFactoryDefault();

                // Configures rotor motor according to previously defined constants
                mRotor.setInverted(SwerveConstants.kRotorMotorInversion); 
                mRotor.enableVoltageCompensation(Constants.kVoltageCompensation);
                mRotor.setIdleMode(IdleMode.kBrake);

                // Configures rotor encoder according to previously defined constants
                mRotorEncoder.configAbsoluteSensorRange(AbsoluteSensorRange.Signed_PlusMinus180);
                mRotorEncoder.configMagnetOffset(rotorOffsetAngleDeg);
                mRotorEncoder.configSensorDirection(SwerveConstants.kRotorEncoderDirection); 
                mRotorEncoder.configSensorInitializationStrategy(
                    SensorInitializationStrategy.BootToAbsolutePosition
                );

                // Configures rotor PID controller according to previously defined constants
                mRotorPID = new PIDController(
                    SwerveConstants.kRotor_kP, 
                    SwerveConstants.kRotor_kI, 
                    SwerveConstants.kRotor_kD
                );

                // Continuous input considers the min & max to be the same point and 
                // automatically calculates the shortest route to the setpoint
                mRotorPID.enableContinuousInput(-180, 180);

                // Configures throttle motor according to previously defined constants
                mThrottle.enableVoltageCompensation(Constants.kVoltageCompensation);
                mThrottle.setIdleMode(IdleMode.kBrake);

                // Sets conversion factor to throttle encoder so that it reads 
                // velocity in meters per second instead of RPM
                mThrottleEncoder.setVelocityConversionFactor(
                    SwerveConstants.kThrottleVelocityConversionFactor
                );
            }

    .. tab:: SPARK MAX & Analog Absolute Encoder

        .. code-block:: java
            :linenos:

            // Initialize rotor & throttle motors 
            private CANSparkMax mRotor;
            private CANSparkMax mThrottle;

            // Initialize throttle encoder
            private RelativeEncoder mThrottleEncoder;

            // Initialize rotor encoder
            private AnalogPotentiometer mRotorEncoder;

            // Initialize rotor PID controller
            private PIDController mRotorPID; 

            /**
             * Construct new SwerveModule
             * 
             * @param throttleID CAN ID of throttle motor
             * @param rotorID CAN ID of rotor motor
             * @param rotorEncoderID analog ID of rotor encoder
             * @param rotorOffsetAngleDeg rotor encoder offset
             */
            public SwerveModule(int throttleID, int rotorID, int rotorEncoderID, double rotorOffsetAngleDeg) 
            {
                // Instantiate throttle motor & respective encoder
                mThrottle = new CANSparkMax(throttleID, MotorType.kBrushless);
                mThrottleEncoder = mThrottle.getEncoder();

                // Instantiate rotor motor 
                mRotor = new CANSparkMax(rotorID, MotorType.kBrushless);

                // Instantiate rotor absolute encoder
                // - Full range = 360 because that should be the max possible value the 
                //   encoder should return
                mRotorEncoder = new AnalogPotentiometer(rotorEncoderID, 360, rotorOffsetAngleDeg);

                // Reset all configuration 
                // (technically optional, but good practice in case there are old 
                // configurations that can mess with the code)
                mThrottle.restoreFactoryDefaults();
                mRotor.restoreFactoryDefaults();

                // Configures rotor motor according to previously defined constants
                mRotor.setInverted(SwerveConstants.kRotorMotorInversion); 
                mRotor.enableVoltageCompensation(Constants.kVoltageCompensation);
                mRotor.setIdleMode(IdleMode.kBrake);

                // Configures rotor PID controller according to previously defined constants
                mRotorPID = new PIDController(
                    SwerveConstants.kRotor_kP, 
                    SwerveConstants.kRotor_kI, 
                    SwerveConstants.kRotor_kD
                );

                // Continuous input considers the min & max to be the same point and 
                // automatically calculates the shortest route to the setpoint
                mRotorPID.enableContinuousInput(-180, 180);

                // Configures throttle motor according to previously defined constants
                mThrottle.enableVoltageCompensation(Constants.kVoltageCompensation);
                mThrottle.setIdleMode(IdleMode.kBrake);

                // Sets conversion factor to throttle encoder so that it reads 
                // velocity in meters per second instead of RPM
                mThrottleEncoder.setVelocityConversionFactor(
                    SwerveConstants.kThrottleVelocityConversionFactor
                );
            }

.. warning:: 
    
    When using brushless motors, it is highly recommended to have liberal current limits
    to prevent damage to the motors. Checkout the `CTRE <https://api.ctr-electronics.com
    /phoenix/release/java/com/ctre/phoenix/motorcontrol/can/TalonFX.html#configSupplyCu
    rrentLimit(com.ctre.phoenix.motorcontrol.SupplyCurrentLimitConfiguration)>`_ and 
    `REV <https://codedocs.revrobotics.com/java/com/revrobotics/cansparkmax#
    setSmartCurrentLimit(int)>`_ documentation for more information regarding current limits. 

Methods
*******

getState
--------

Outputs the current state of the swerve module.

.. tabs::
    
    .. tab:: Talon FX

        .. code-block:: java
            :linenos:

            public SwerveModuleState getState() {
                double throttleVelocity = 
                    mThrottle.getSelectedSensorVelocity() * SwerveConstants.kThrottleVelocityConversionFactor; 

                return new SwerveModuleState(
                    throttleVelocity, 
                    Rotation2d.fromDegrees(mRotorEncoder.getAbsolutePosition())
                );
            }

    .. tab:: SPARK MAX

        .. code-block:: java
            :linenos:

            public SwerveModuleState getState() {
                return new SwerveModuleState(
                    mThrottleEncoder.getVelocity(),
                    Rotation2d.fromDegrees(mRotorEncoder.getAbsolutePosition())
                );
            }

**Return:**
"""""""""""

    New `SwerveModuleState <https://first.wpi.edu/wpilib/allwpilib/docs/release/java
    /edu/wpi/first/math/kinematics/SwerveModuleState.html>`_ representing the current
    throttle velocity and rotor angle. 

setState
--------

Sets the state of the swerve module.

.. code-block:: java
    :linenos:

    public void setState(SwerveModuleState state) {
        // Optimize the desired state so that the rotor doesn't have to spin more than
        // 90 degrees to get to the desired angle
        SwerveModuleState optimizedState = SwerveModuleState.optimize(state, getState().angle);

        // Calculate rotor output using rotor PID controller by comparing the current
        // angle with the desired angle
        double rotorOutput = mRotorPID.calculate(
            getState().angle.getDegrees(), 
            optimizedState.angle.getDegrees()
        );

        mRotor.set(rotorOutput);
        mThrottle.set(optimizedState.speedMetersPerSecond);
    }

Parameters:
"""""""""""

1. ``state`` - desired `SwerveModuleState <https://first.wpi.edu/wpilib/allwpilib/docs/release/java
   /edu/wpi/first/math/kinematics/SwerveModuleState.html>`_ (angle & speed) of SwerveModule

.. note::
    Check out the code we use in these docs on our `Github <https://github.com/
    TASRobotics/RaidZero-Swerve-Template>`_!