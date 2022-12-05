######
Sample
######

Hey people, this is a sample page of documentation. 

This is a subtitle
******************

* You can make lists like this
* You can format text like this

  * `italics`
  * **bold**
  * ``code``

1. You can make numbered lists like this

If you have a big chunk of code, you can do something like this: 

.. code-block:: cpp

    std::shared_ptr<AsyncHolonomicChassisController> controller = 
        AsyncHolonomicChassisControllerBuilder(std::shared_ptr<okapi::OdomChassisController> ichassis)

You can also have different tabs

.. tabs::

    .. tab:: C++

        .. code-block:: cpp

            int main() {
                std::cout << "Hello World" << std::endl;
            }

    .. tab:: Python

        .. code-block:: python

            print("Hello World")

Images look like this

.. image:: Photos/LogoBW.png
    :scale: 5%

You can also include captions like this

.. figure:: Photos/LogoBW.png
    :scale: 5%
    :alt: Logo

    A caption


Write math equations like this: :math:`e^{i\pi} + 1 = 0`

Make tables like this

+-----------------+-----------------+-----------------+
| Header 1        | Header 2        | Header 3        |
+=================+=================+=================+
| row 1, col 1    | row 1, col 2    | row 1, col 3    |
+-----------------+-----------------+-----------------+
| row 2, col 1    | row 2, col 2    | row 2, col 3    |
+-----------------+-----------------+-----------------+
| row 3, col 1    | row 3, col 2    | row 3, col 3    |
+-----------------+-----------------+-----------------+

Check out other things you can do with sphinx 
`here <https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html>`_