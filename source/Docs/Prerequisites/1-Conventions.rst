###########
Conventions
###########

Here are the conventions used in these docs

Variable Naming
***************

* Instance Variables: ``m[VARIABLE_NAME]``

  * ex. ``mMotorController``

* Constants: ``k[VARIABLE_NAME]``

  * ex. ``kMotorControllerID``

Swerve Part Naming
******************

.. figure:: ../Photos/Hardware/AnnotatedMK4.png
    :alt: Swerve Parts
    :target: https://www.swervedrivespecialties.com/collections/kits/products/mk4-swerve-module
    :scale: 50%

    Swerve Parts; `source <https://www.swervedrivespecialties.com/collections/kits/products/mk4-swerve-module>`_

1. **Throttle**: Controls the rotation of the wheel
2. **Rotor**: Controls the direction of the wheel (**Throttle Encoder** measures the angle of the rotor)

SwerveModule Order
******************

The order of the swerve modules will always be as follows:

1. Left Front
2. Right Front
3. Left Rear
4. Right Rear

.. figure:: ../Photos/AnnotatedSwerveDrive.png
    :alt: Swerve Module Order
    :target: https://www.reddit.com/r/FRC/comments/mrhzks/the_mk2_swerve_drive_from_swerve_drive/
    :scale: 50%
    
    Swerve Module Order; source
    