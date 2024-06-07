Using Workspace Metadata
==================================

.. important::

    While you can get objects using the workspace client, it is not the recommended way to do it. We suggest using the  |FileUtil_link| instead.
    
You can download the Workspace client by running the following inside an SDK module: 
    
    ``kb-sdk install https://raw.githubusercontent.com/kbase/workspace_deluxe/master/workspace.spec``
    

Retrieving workspace object metadata
---------------------------------------

If you don't want to retrieve the entire object, and just want information about it, the most efficient way is to use the ``ws.get_object_info3`` function. To read more about the available functions, see the  |Workspace_link|.

*Initialize the workspace client and get an object's information by reference*

.. code-block:: python

    # The config object is passed into the __init__ function for your *Impl.py module
    from Workspace.WorkspaceClient import Workspace as Workspace
    self.ws_url = config["workspace-url"]
    self.token = config["KB_AUTH_TOKEN"]
    self.ws = Workspace(self.ws_url, token=self.token)
    obj_ref = "your/object/reference"
    object_info = self.ws.get_object_info([{"ref": obj_ref}])[0]
    object_type_full = object_info[2] # This could be KBaseGenomes.Genome-8.3
    object_type = object_type_full.split('-')[0] # This would be KBaseGenomes.Genome


How to use the Workspace Client in the narrative
-------------------------------------------------

You can access the workspace client from within narrative code cells. See this `example narrative <https://narrative.kbase.us/narrative/ws.30170.obj.1>`_ |Example_link|.

.. code-block:: python

    ws = biokbase.narrative.clients.get('workspace')
    obj = ws.get_objects2({'objects' : [{'ref' : '30170/2/1'}]})



How to add types to the workspace
------------------------------------

* See the guide at https://ci.kbase.us/services/ws/docs/administrationinterface.html#managing-module-ownership-requests |addTypes_link| 
* Use the administration interface in any of the clients above to register, approve registration of, and to upload typespecs

How to get a given typespec / .spec file?
------------------------------------------

If you can't find a typespec in a repo, you can find it doing the following:

.. code-block:: python

    # Example spec file snippet

    from biokbase.workspace.client import Workspace
    ws = Workspace('https://ci.kbase.us/services/ws')
    print(ws.get_module_info({'mod': 'YourModuleName'})['spec'])


.. External links

.. |Workspace_link| raw:: html

   <a href="https://kbase.us/services/ws/docs/Workspace.html" target="_blank">workspace spec file </a>

.. |Example_link| raw:: html

   <a href="https://narrative.kbase.us/narrative/ws.30170.obj.1" target="_blank">example narrative  </a>

.. |addTypes_link| raw:: html

   <a href="https://ci.kbase.us/services/ws/docs/administrationinterface.html#managing-module-ownership-requests" target="_blank">https://ci.kbase.us/services/ws/docs/administrationinterface.html#managing-module-ownership-requests </a>


.. Internal links

.. |FileUtil_link| raw:: html

   <a href="../howtos/file_utils.html">file utilities </a>

