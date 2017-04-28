.. _tutorial03:

Object Recognition Using LineMOD
#################################

:ref:`Linemod <orklinemod:line_mod>` is a pipeline that implements a robust
best methods for generic rigid object recognition and it proceeds using very
fast template matching. For more information on LineMOD, please read the papers
written by
`Stefan Hinterstoisser <http://ar.in.tum.de/Main/StefanHinterstoisser>`_.

This tutorial assumes that you are using ROS Through this tutorial, you will:

   * learn how to use the ``linemod`` pipeline to learn objects
   * learn how to use the ``linemod`` pipeline to detect objects
   * use the ORK RViz plugins


Set Up Your Environment
***********************

Hardware
========

To see Linemod in action, you will need the following:
  * A 3D camera (such as a Kinect or Asus Xtion)
  * A PC with ROS installed
  * A can of Coke (12 fl. oz./355 mL)

Software
========

You need to have ORK installed on the computer. If you have completed the
:ref:`Getting Started Guide <orkcore:getting_started>`_, then you are good to go.
You can also find detailed installation instructions at the
:ref:`Installation page <orkcore:installation>`_.

Configuring the 3D camera and ``RViz`` parameters
=================================================

This step is similar to steps 7 and 8 of the Getting Started Guide.
At first, launch the OpenNI driver:

.. code-block:: sh

    roslaunch openni2_launch openni2.launch

If you are using the Orbbec Astra, replace ``openni2`` with ``astra``.

Run RViz

.. code-block:: sh

    rosrun rviz rviz

Set the Fixed Frame (in Global Options, ``Displays`` window) to
``/camera_depth_optical_frame``. Add a PointCloud2 display and set the topic to
``/camera/depth/points``.  This is the unregistered point cloud in the frame of
the depth (IR) camera and it is not matched with the RGB camera images.
For visualization of the registered point cloud, the depth data could be
aligned with the RGB data. To do it, launch the dynamic reconfigure GUI:

.. code-block:: sh

    rosrun rqt_reconfigure rqt_reconfigure

Select ``/camera/driver`` from the drop-down menu and enable the
``depth_registration`` checkbox. In ``RViz``, change the PointCloud2
topic to ``/camera/depth_registered/points`` and set the Color Transformer
to ``RGB8`` to see both color and 3D point cloud of your scene.
For details on how the OpenNI camera drivers work, please read the documentation
at http://wiki.ros.org/openni2_launch.


Object detection
****************

Setup the object database
=========================

Your database must have an object loaded into it to perform detection. If you've
done the Getting Started guide, you already have the soda can model in your
database, and you can skip this step. If not, go to the
:ref:`Getting Started Guide <orkcore:getting_started>`_ and complete steps 4 and
5.

Training
========

Now, you can learn objects models from the database. The following command
will start LineMOD in training mode, with the configuration file specified by
the ``-c`` option. The configuration file defines a pipeline that reads
data from the database and computes object models.

.. code-block:: sh

    rosrun object_recognition_core training -c `rospack find object_recognition_linemod`/conf/training.ork


Detection
=========

Once learned, objects can be detected from the input point cloud. In order to
detect object continuously, execute the following command to start LineMOD in
detection mode. The configuration file defines a source, a sink, and a pipeline,
as explained in
http://wg-perception.github.io/object_recognition_core/detection/detection.html.

.. code-block:: sh

    rosrun object_recognition_core detection -c  `rospack find object_recognition_linemod`/conf/detection.ros.ork

Visualization with RViz
=======================

Now, go to ``RViz`` and add the ``OrkObject`` in the ``Displays`` window. Select
the ``OrkObject`` topic and the parameters to display: object id, name, and
confidence.
Here, we show an example of detecting two objects (a coke and a head of NAO)
and the outcome visualized in RViz:

.. image:: Screenshot_2014_11_07_13_24_46.png
   :width: 100%

For each recognized object, you can visualize its point cloud and also a point
cloud of the matching object from the database. For this, compile the package
with the CMake option ``-DLINEMOD_VIZ_PCD=ON``. Once an object is recognized,
its point cloud from the sensor 3D data is visualized as shown in the following
image (check blue color). The cloud is published under the ``/real_icpin_ref``
topic.

.. image:: Screenshot_pc_ref.png
   :width: 100%

For the same recognized object, you can visualize the point cloud of the
matching object from the database as shown in the following image (check yellow
color). The point cloud is created from the mesh stored in the database by
visualizing at a pose returned by Linemod and refined by ICP. The cloud is
published under the ``/real_icpin_model`` topic.

.. image:: Screenshot_pc_model.png
   :width: 100%



