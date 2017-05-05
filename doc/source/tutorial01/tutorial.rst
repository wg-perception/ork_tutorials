.. _tutorial01:

Database Management
###################

In the Object Recognition Kitchen, everything is stored in a database (DB):
objects, models, training data. We'll walk you through the basics of the DB in
this tutorial. You will learn how to:

   * Prepare an object's mesh to add to the DB
   * Manually add an object to the DB
   * Visualize data in the ORK DB

Introduction
************

Make sure you followed the steps in the core
:ref:`DB instructions <orkcore:object_recognition_core_db>`, especially to get
the 3d visualizer in the DB.

In this tutorial, the object you will use is a can of Coke (12 fl. oz./355 mL),
since it's somewhat universal. For real life experiments, just get the iconic
red can and there should not be too many appearance changes.

Prepare object's mesh
*********************
An object's mesh is important for object detection in ORK. The object's mesh
must be in ``.stl`` or ``.obj`` format.

ORK's :ref:`capture <orkcapture:ork_capture>`
capability is designed to automatically create an ``.stl`` or
``.obj`` file from many images of your object. In this way, you can create a
mesh from an object without having to model the object in a CAD or modeling
program. Finally, you can also use a mesh that you find on the internet. Read
the :ref:`capture <orkcapture:ork_capture>` page for details.

If you are using a mesh that you made yourself or downloaded from the internet,
make sure that it is the the right size (the default units are meters). Also,
note its origin point before you upload it onto the DB. As can be seen in the
following screenshot from Blender (a mesh modeling program), the can's mesh has
a different position relative to the origin point than the bottle's mesh.

.. image:: blender_coke_bottle_pos.png
   :width: 100%

When ORK publishes an object's position, that position is the origin point
of the object's mesh.

Creating an object in the DB
****************************

ORK is about recognizing objects so you need to store objects in the DB first.
Some pipelines, like :ref:`ORK 3d capture <orkcapture:ork_capture>`, have an
interface to create objects for you. But you can also do it with the scripts
from the ``object_recognition_core``.

.. toggle_table::
   :arg1: ROS
   :arg2: Without ROS

.. toggle:: ROS

   .. code-block:: sh

      rosrun object_recognition_core object_add.py -n coke -d "A can of Coca-Cola"

.. toggle:: Without ROS

   .. code-block:: sh

      ./ork_core/apps/dbscripts/object_add.py -n coke -d "A can of Coca-Cola"

You can then check this object is loaded into the DB by going to
http://localhost:5984/_utils/database.html?object_recognition/_design/objects/_view/by_object_name

.. image:: db_screenshot01.png
   :width: 100%

If you click on it, you can see the info you entered about the object,
including the object id:

.. image:: db_screenshot02.png
   :width: 100%

Manually adding a mesh for the object
*************************************

First, find you object's ID using the DB interface. Each
element in the DB (objects included) has its own hash as a unique identifier
(in case you give the same name to different objects), and that is how you
should refer to objects. To upload the mesh (use an .stl/.obj one):

.. toggle_table::
   :arg1: ROS
   :arg2: Without ROS

.. toggle:: ROS

   .. code-block:: sh

      rosrun object_recognition_core mesh_add.py YOUR_OBJECT_ID `rospack find object_recognition_tutorials`/data/coke.obj --commit

.. toggle:: Without ROS

   .. code-block:: sh

      ./ork_core/apps/dbscripts/mesh_add.py YOUR_OBJECT_ID YOUR_COKE_BLEND_PATH --commit

Visualizing the object
**********************

Now, if you want to visualize the object in the db, you can visit the
visualization URL at http://localhost:5984/or_web_ui/_design/viewer/meshes.html.
You should something similar to the following:

.. image:: db_screenshot03.png
   :width: 100%


Deleting an object
******************

You also have a method to delete an object (it will delete all other elements in the database like models/training data from it too).


.. toggle_table::
   :arg1: ROS
   :arg2: Without ROS
.. toggle:: ROS

   .. code-block:: sh

      rosrun object_recognition_core object_delete.py OBJECT_ID

.. toggle:: Without ROS

   .. code-block:: sh

      ./ork_core/apps/dbscripts/object_delete.py OBJECT_ID
