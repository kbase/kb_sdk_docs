******************************
Narrative App UI Specification
******************************

.. contents::

This document describes how the UI components of Narrative Apps are specified via fields in ``spec.json`` and ``display.yaml`` files. These spec files include input and output parameters for the app.

Structure of the folders and files
----------------------------------

In an SDK app's repo, all UI method specifications live in the ``ui/narrative/methods/`` directory. Each method has its own subfolder with the name corresponding to the app or method ID. Inside this folder, a typical method has a ``spec.json`` file defining input/output parameters, a ``display.yaml`` file defining display text, and an ``img`` subfolder containing icon and screenshot files. 

Structural file: ``spec.json``
------------------------------
This is a file in JSON format containing these top-level properties:

:ver: version of each UI method-spec
:authors: list of GlobusOnline accounts of the contributors
:contact: e-mail of the contact person
:categories: list of tags (from fixed set of supported tags) categorizing this method into
  method should be made invisible, then the ``inactive`` category should be added to this list (in
  place of the ``active`` category, if it’s present)
:widgets: structure defining **input** and **output** widgets. Typically the **input** widget is
          set to null, meaning that the standard one is used. The **output** widget is frequently
          the name of JavaScript viewer for the output object. See the |widgets_link| 
          and its subdirectories for more widgets.
:parameters: list of structures (described below) defining each parameter type and other features, except
:behavior: block of settings for mapping UI parameters to service function input parameters. Also
           used for mapping output results returned from the service function to the input of the
           visualizing widget that shows the results.
:job_id_output_field: field for support of legacy code. For an SDK method, it should always be set
                      to the ``docker`` value

Parameters in ``spec.json``
^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each item in the ``parameters`` array defines one parameter shown in the UI input panel of this method. The properties of each item are:

:id: ID of a parameter, allowing you to reference it in ``display.yaml`` and from the mapping
     settings of the ``behavior`` block
:optional: flag defining whether the UI interface allows an empty value for this parameter or
           requires the field to be set to something non-empty
:advanced: flag defining whether the UI interface should place this field in the "Advanced
           Parameters" group, collapsed by default
:allow_multiple: flag defining whether the UI interface allows you to define more than one value
                 for this parameter, treating these values as a list rather than a single
                 textual/numeric/boolean value
:default_values: if set to any non-empty string, this field will be preset with the string
:valid_file_types: a list of staging area file types that are valid for the method
                   parameter. This might apply to a text box, dropdown, dynamic dropdown, etc.
                   depending on the context. The file type is available in the fileinfo key of
                   the json response from staging service importer mappings endpoint. Each mapping
                   has a file_type_ext key containing the type.
:field_type: type of the parameter; could be one of ``text``, ``dropdown``, ``checkbox``,
             ``textarea``, ``textsubdata``, or ``dynamic_dropdown``
:text_options: optional block defining details of the ``text`` type
:dropdown_options: optional block defining details of the ``dropdown`` type -  |dropdown_link| 
:checkbox_options: optional block defining details of the ``checkbox`` type -  |checkbox_link| 
:textarea_options: optional block defining details of the ``textarea`` type -  |textarea_link| 
:textsubdata_options: optional block defining details of the ``textsubdata`` type -  |textsubdata_link| 


Options for the text parameter type in ``spec.json``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Options in the ``text_options`` block allow you to specify many different types of validation and behavior. Fields named ``valid_ws_types`` connect a parameter with one or more types of objects stored in the Workspace. In this mode, the narrative will show the available objects as drop-down options. For instance, the following is an example of ``text_options``,
allowing you to choose one of the Genome objects stored in the Workspace:

.. code-block:: js

    "text_options" : {
        "valid_ws_types" : [ "KBaseGenomes.Genome" ]
    }

If you would like to mark this parameter as output, meaning the UI interface shouldn’t require
the chosen object to be present in Workspace storage, then you can set the ``is_output_name`` sub-option to
true:

.. code-block:: js

    "text_options" : {
        "valid_ws_types" : [ "KBaseGenomes.Genome" ],
        "is_output_name" : true
    }

Another sub-option is ``validate_as``, allowing you to validate a value entered in the UI as an ``int`` or ``float``. If
you want a parameter to be an integer with minimum and/or maximum limits you can use additional properties, as in this example:

.. code-block:: js

    "text_options" : {
        "valid_ws_types" : [ ],
        "validate_as": "int",
        "min_int" : 1,
        "max_int" : 200
    }

And similarly for float types:

.. code-block:: js

    "text_options" : {
        "valid_ws_types" : [ ],
        "validate_as": "float",
        "min_float" : 1,
        "max_float" : 200
    }

Options for the drop-down parameter type in ``spec.json``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

There is only one sub-option available inside the ``dropdown_options`` block: the ``options`` property whose value should be set to a list of objects defining drop-down items. Each object should have two properties: ``value`` defining an internal item ID (sent to the back-end function when the given item is selected); and the ``display`` property, which defines text shown for this item in the UI. The following is an example of the "dropdown_options" block:

.. code-block:: js

    "dropdown_options":{
        "options": [{
            "value": "lloyd",
            "display": "Lloyd"
        }, {
            "value": "hartigan_wong",
            "display": "Hartigan-Wong"
        }, {
            "value": "forgy",
            "display": "Forgy"
        }, {
            "value": "mac_queen",
            "display": "MacQueen"
        }]
    }

Options for the checkbox parameter type in ``spec.json``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following is the list of sub-options available inside ``checkbox_options`` block:

:checked_value: defines the value to be sent to a service function when the checkbox is selected
:unchecked_value: defines the value to be sent to a service function when the checkbox is not
                  selected

Options for the textarea parameter type in ``spec.json``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

There is only one sub-option available inside the ``textarea_options`` block:

:n_rows: defines the number of lines shown for this textarea in the UI.

Options for the textsubdata parameter type in ``spec.json``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This parameter type allows you to select items that are parts of the workspace object (let’s call them
sub-objects). The following is the list of sub-options available inside the ``textsubdata_options`` block:

:multiselection: flag (boolean) allowing to have more than one selected object
:show_src_obj: flag (boolean) shows the name of a workspace object where we are selecting
               sub-objects
:allow_custom: flag (boolean) allow the user to enter values which are not present in the source
               object
:subdata_selection: main block with selection options (see below)

Options under ``subdata_section``:

:path_to_subdata: JSON-path leading to the level of a of sub-object (this should be an array of
                  property names). To access a second workspace object that is referenced from the
                  primary object, use the special term "<WSREF>" in this path. The path following
                  this point will be in the second object. For an example see the |runFBA_link|
:subdata_included: list of string JSON-paths to be loaded (in case the JSON-path leads to a certain
                   field inside the sub-objects, then the level of array of sub-objects is denoted
                   as [*])
:constant_ref: static reference to some object in the public workspace (alternative to the
               **parameter_id**)
:parameter_id: points to the ID of another UI parameter used to select a workspace object where we
               are selecting sub-objects
:selection_id: name of the field of the sub-object which will be sent as a selected value
:selection_description: list of fields of the sub-object to be shown for each selectable item
:description_template: optional template defining the representation of fields from
                       ``selection_description`` (placeholders for the fields are defined as
                       {{field-name}})

The following is an example of the ``textsubdata_options`` block for the model reactions in the ``KBaseFBA.FBAModel`` object:

.. code-block:: js

    "textsubdata_options" : {
       "subdata_selection": {
          "parameter_id" : "input_model",
          "subdata_included" : ["modelcompounds/[*]/id",
          "modelcompounds/[*]/name","modelcompounds/[*]/formula"],
          "path_to_subdata": ["modelcompounds"],
          "selection_id" : "id",
          "selection_description" : ["name","formula"],
          "description_template" :"- {{name}} ({{formula}})"
      },
      "multiselection":true,
      "show_src_obj":false,
      "allow_custom":false
    }

Options for the dynamic_dropdown in ``spec.json``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``dynamic_dropdown`` parameter type defines a field that gives the user an autocomplete
dropdown, where the options in the dropdown can be dynamic (usually based on the results of
a service call). For instance, you might have a selection of files where the options are from
the staging_service or from kbase_search. The parameter appears as a text field with a dropdown
similar to the selection of other WS data objects.

:data_source: One of ``ftp_staging``, ``search``, or ``custom``. Provides sensible defaults for the
              following parameters for a common type of dropdown that can be overwritten.

:service_function: Name of the SDK method, including an SDK module prefix, started up as a dynamic
                   service (this needs to be the fully qualified method name, such as
                   ``"ModuleName.method_name"``).

:service_version: Optional version of the module used in the service_function (the default value is
                  'release').

:service_params: The parameters supplied to the dynamic service call as JSON. The special text
                 ``"{{dynamic_dropdown_input}}"`` will be replaced by the value from the user input
                 at call time.

:selection_id: The value of this key will be extracted from the item selected by the
               user. The item is expected to be represented as a map.

:description_template: Defines how the description of items are rendered using Handlebar templates
                       (use the keys in items as variable names).

:multiselection: If true, then multiple selections are allowed in a single input field. This will
                 override the ``allow_multiple`` option (which allows user addition) of additional
                 fields. If true, then this parameter will return a list. Defaults to false.

:query_on_empty_input: ``true``, the default, to send a request to the dynamic service even
                       if there is no input.

:result_array_index: The index of the result array returned from the dynamic service
                     from where the selection items will be extracted. Default 0.

:path_to_selection_items: The path into the result data object to the list of
                          selection items. If missing, the data at the specified result array
                          index is used (defaulting to the first returned value in the list).

The selection items data structure must be a list of mappings or structures.

As an example of correctly specifying where the selection items are within the
data structure returned from the dynamic service, if the data structure is:

.. code-block:: json

    [
        "return-array-element-0",
        {
         "interesting_data":
            [
                "baz",
                "boo",
                [
                    {"id": 1, "name": "foo"},
                    {"...more items": "go here..."},
                    {"id": 42, "name": "wowbagger"}
                ],
                "bat"
             ]
         },
         "return-array-element-2"
     ]

Note that KBase dynamic services all return an array of values, even for single-value
returns, as the KIDL spec allows specifying multiple return values per function.

In this case:

* result_array_index would be ``1``
* path_to_selection_items would be ``["interesting_data", "2"]``
* selection_id would be ``name``

The selection items would be the 42 items represented by

.. code-block:: json

  [
    {"id": 1, "name": "foo"},
    {"...more items": "go here..."},
    {"id": 42, "name": "wowbagger"}
  ]

Selection items must always be a list of maps.

The final value returned when the user selects a value would be the ``name`` field -
``foo`` if the first item is selected, and ``wowbagger`` if the last item is selected.

Here is an example for taxon search:

.. code-block:: json

  {
    "id": "scientific_name",
    "optional": false,
    "advanced": false,
    "allow_multiple": false,
    "default_values": [""],
    "field_type": "dynamic_dropdown",
    "dynamic_dropdown_options": {
      "data_source": "custom",
      "service_function": "taxonomy_re_api.search_taxa",
      "service_version": "dev",
      "service_params": [
        {
          "search_text": "prefix:{{dynamic_dropdown_input}}",
          "ns": "ncbi_taxonomy",
          "limit": 1000
        }
      ],
      "query_on_empty_input": 0,
      "result_array_index": 0,
      "path_to_selection_items": ["results"],
      "selection_id": "scientific_name",
      "description_template": "Tax ID {{ncbi_taxon_id}}:&nbsp<strong>{{scientific_name}}</strong>",
      "multiselection": false
     }
   }

Parameter groups in ``spec.json``
---------------------------------

Parameter groups combine a set of individually specified parameters into logical sets. This can be used for something as simple as visually grouping related input (i.e. distinguishing a set of parameters passed to a wrapped tool from kbase related parameters), but it's most often used to allow users to specify multiple items described by a more than one parameter. 

It is also possible to have an optional parameter group with required parameters. If the parameter group is present, all the required parameters must be provided. The default resulting structure is a mapping (or list of mappings if ``allow_multiple`` is 1) with the parameter_ids as keys (e.g. ``{id: [{parameter_id_1: value_1, parameter_id_2: value_2 ...}]}``) but this can be modified with the ``id_mapping`` option.

:id: id of the parameter group. Must be unique within the method among all parameters and groups
:parameter_ids: IDs of parameters included in this group
:ui_name: short name that is displayed to the user
:short_hint: short phrase or sentence describing the parameter group
:description: longer and more technical description of the parameter group (long-hint)
:allow_mutiple: allows entry of a list instead of a single structure, default is 0. If set, the
                number of starting boxes will be either 1 or the number of elements in the
                default_values list.
:optional: set to 1 to make the group optional, default is 0
:advanced: set to 1 to make this an advanced option, default is 0. If an option is advanced, it
           should also be optional or have a default value
:id_mapping: optional mapping which connects parameter IDs (as keys) to a desired name in the
             output object (as values) (e.g. ``{"parameter_id":"output_key"}``). This provides
             similar functionality to the ``kb_service_input_mapping`` and
             ``kb_service_output_mapping`` described in the behavior section below for these nested
             objects.
:with_border: set to 1 to wrap this group with border.

Here is an example of a ``parameter-groups`` block for from the |editMediaUI_link| inside the ``fba_tools`` app.

.. code-block:: js

    "parameter-groups": [
        {
            "id": "compounds_to_change",
            "parameters": [
                "change_id",
                "change_concentration",
                "change_minflux",
                "change_maxflux"
            ],
            "optional": true,
            "advanced": false,
            "allow_multiple": true,
            "with_border": true
        },
        {
            "id": "compounds_to_add",
            "parameters": [
                "add_id",
                "add_concentration",
                "add_minflux",
                "add_maxflux"
            ],
            "optional": true,
            "allow_multiple": true,
            "advanced": false,
            "with_border": true
        }
    ]

Behavior in ``spec.json``
-------------------------

There are three alternative sub-blocks available inside the ``behavior`` block:

:service-mapping: defines mapping rules for the input and output data for a typical SDK method
                  (described below)
:none: used in case the UI method is not supposed to run any service function (for instance, when
       input parameters should be passed into the widget directly) - |none_link| 
:script-mapping: support for legacy software -- not recommended for use in SDK repos

In most cases, the ``service-mapping`` sub-block should be used. Here is the list of sub-elements available inside ``service-mapping``:

:url: defines the URL end-point of a deployed service (for SDK repos, this parameter should be
      empty)
:name: module name of an SDK repo registered in the catalog (refer to the module name in the KIDL
       specification)
:method: name of the service function to be called (see the funcdef in the KIDL specification)
:input_mapping: defines rules for mapping UI parameters onto service function input arguments
:output_mapping: defines rules for mapping output results returned from service functions to input
                 options of widgets that display these results

Both the ``input_mapping`` and ``output_mapping`` sub-blocks are arrays of items, where each item has the following properties:

:input_parameter: ID of a UI input parameter or parameter group to be used as a source of mapping
:constant_value: constant value to be used as a source of mapping - |constant_link| 
:narrative_system_variable: system variable in the narrative back-end to be used as a source of
                            mapping (only the ``workspace`` variable is currently supported)
:target_property: name of the structure field to be set as a target of mapping
:target_argument_position: (allowed for input mapping items only, default value is 0) position of
                           the input argument of a service function to be set as a target of
                           mapping
:target_type_transform: optional rule allowing you to modify the passed value. See below for the
                        list of allowed transformations.
:service_method_output_path: (allowed for output mapping items only) - defines the JSON-path into
                             the output prepared for the widget as a place for a target value; if
                             this path is an empty array, it corresponds to the root point, and all
                             the data returned from the service function will be captured - |service_link| 


The following is a list of allowed transformations that can be used for the
``target_type_transform``:

:none: (default value in case it is not defined) - no modification
:ref: changes the object name into a workspace reference by adding a prefix of the workspace name followed by ``/``
:int: treats a text value as an integer
:list<inner-transformation>: tries to prepare a list of items (or just iterate over items if it’s a
                             list already), applying inner-transformation to each element

In a group of source properties (``input_parameter``, ``constant_value``, ``narrative_system_variable``), only one property can be used. For target properties, both ``target_property`` and ``target_argument_position`` can be used at the same time. This means that the service function will receive an argument with the position from ``target_argument_position`` and an object with property having a name from the ``target_property`` with target value.

Example of mappings in ``spec.json``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let’s consider some example mappings defined in the ``service-mapping`` sub-block of the ``behavior`` section. Suppose we have a function named ``func1`` in the module called ``module1``, where we expect to get two arguments: a string and an object with the internal field ``input_prop`` (such as ``{"input_prop": "..."}``). We also have two UI parameters of the type ``text`` with the IDs ``param1`` and ``param2``. Output returned from the function is an array containing only one object which has an internal field called ``output_prop``. The value of this field should be mapped to the ``option1`` option in the UI widget. In this case, we'll have following mappings:

.. code-block:: js

    "behavior" : {
        "service-mapping" : {
        "url" : "",
        "name" : "module1",
        "method" : "func1",
        "input_mapping" : [
            {
                "input_parameter": "param1"
                "target_argument_position": 0
            }, {
                "input_parameter": "param2",
                "target_argument_position": 1,
                "target_property": "input_prop"
            }
        ],
        "output_mapping" : [
            {
                "service_method_output_path": [0, "output_prop"],
                "target_property": "option1"
            }
        ]
    }

Display Text file (``display.yaml``)
------------------------------------

The ``display.yaml`` file controls how information is displayed in the narrative and in the app catalog.

.. figure:: ../images/View_flux_network_narr.png
    :align: center
    :figclass: align-center

    View Flux Network App in a narrative.

.. figure:: ../images/ViewFluxNetwork_cat.png
    :align: center
    :width: 90%
    :figclass: align-center

    App Catalog for View Flux Network.

This file uses the YAML format with the following top-level fields:

:name: name of the method listed in the UI
:tooltip: more detailed explanation of the method shown on a mouse-over event
:screenshots: list of names of screenshot files from the ``img`` sub-folder
:icon: (optional) name of an icon file from the ``img`` sub-folder.  |icon_link| 
:method-suggestions: list of objects defining a set of other methods that are suggested to the user
                     as related methods. There are two sub-elements -- ``related`` and ``next`` --
                     pointing to arrays of method IDs
:parameters: parameter IDs (defined in ``spec.json``) mapped to objects to objects that define
             textual information for these parameters (see details below)
:description: very detailed explanation of what this method does, appearing on a separate page
:publications: (optional) list of objects describing related publications. Each object includes two
               fields: ``display-text``, containing a reference to a scientific journal; and
               ``link``, which has the URL to an online resource.  |publications_link| 

Each field in the ``parameters`` section can have the following properties:

:ui-name: name of the parameter used to show the field in the UI
:short-hint: short description shown in front of each parameter on the right side of the method
             input panel in the narrative
:long: a more detailed explanation available by mouse-over
:placeholder: (optional) if the parameter type is textual (one of ``text``, ``textarea``, ``textsubdata``), then this defines the placeholder text for the field.  |placeholder_link| 



.. External links

.. |editMediaUI_link| raw:: html

   <a href="https://github.com/cshenry/fba_tools/blob/4e9001c3547388eb70da6c07229f54c4aac23af2/ui/narrative/methods/edit_media/spec.json" target="_blank">Edit Media UI</a>

.. |runFBA_link| raw:: html

   <a href="https://github.com/cshenry/fba_tools/blob/9e7daee7bdea79961a64c2d981036354e6ba8c45/ui/narrative/methods/run_flux_balance_analysis/spec.json#L183-L187" target="_blank">Run FBA UI</a>

.. |widgets_link| raw:: html

   <a href="https://github.com/kbase/narrative/tree/develop/kbase-extension/static/kbase/js/widgets" target="_blank">Narrative module widgets</a>

.. |dropdown_link| raw:: html

   <a href="https://github.com/kbaseapps/fba_tools/blob/master/ui/narrative/methods/build_metabolic_model/spec.json" target="_blank">Example</a>


.. |checkbox_link| raw:: html

   <a href="https://github.com/kbaseapps/fba_tools/blob/master/ui/narrative/methods/simulate_growth_on_phenotype_data/spec.json" target="_blank">Example</a>


.. |textarea_link| raw:: html

   <a href="https://github.com/kbaseapps/fba_tools/blob/master/ui/narrative/methods/build_multiple_metabolic_models/spec.json" target="_blank">Example</a>


.. |textsubdata_link| raw:: html

   <a href="https://github.com/kbaseapps/fba_tools/blob/master/ui/narrative/methods/compare_flux_with_expression/spec.json" target="_blank">Example</a>


.. |none_link| raw:: html

   <a href="https://github.com/kbaseapps/kb_cummerbund/blob/master/ui/narrative/methods/view_volcano_plot/spec.json" target="_blank">Example</a>


.. |constant_link| raw:: html

   <a href="https://github.com/kbaseapps/taxonomy_service/blob/master/ui/narrative/methods/create_taxonomy/spec.json" target="_blank">Example</a>


.. |service_link| raw:: html

   <a href="https://github.com/kbaseapps/FeatureSetUtils/blob/master/ui/narrative/methods/upload_featureset_from_diff_expr/spec.json" target="_blank">Example</a>


.. |icon_link| raw:: html

   <a href="https://github.com/kbaseapps/fba_tools/blob/master/ui/narrative/methods/build_metabolic_model/display.yaml" target="_blank">Example</a>


.. |publications_link| raw:: html

   <a href="https://github.com/kbaseapps/fba_tools/blob/master/ui/narrative/methods/build_metabolic_model/display.yaml" target="_blank">Example</a>


.. |placeholder_link| raw:: html

   <a href="https://github.com/kbaseapps/fba_tools/blob/master/ui/narrative/methods/build_metabolic_model/display.yaml" target="_blank">Example</a>


