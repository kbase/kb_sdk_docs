Anatomy of a Module
===================

KBase modules consist of a set of code, configurations, and
specifications that describe how a set of related apps are shown in the
Narrative interface and define the implementation of a function that is
run when the Narrative app is executed. Modules can also include
components that define visualization widgets, data types, and data
uploaders/downloaders/converters. This guide provides a brief overview
of these key components and how they are related.

Overview
--------

To explore the contents of a module yourself, you can use the kb-sdk
tool to create an example module. (Please see |instructions_link|
on how to install kb-sdk.)

::

    kb-sdk init --example -l python --user YOURUSERNAME MyModule
    cd MyModule
    make

This command will initialize a new Module named MyModule with a prebuilt
implementation of an example app. You should use your own GitHub
username instead of YOURUSERNAME. The generated directory structure will
look something like this:

::

    MyModule
    ├── README.md
    ├── LICENSE
    ├── kbase.yml
    ├── Dockerfile
    ├── Makefile
    ├── deploy.cfg
    ├── MyModule.spec
    ├── .travis.yml
    ├── data
    │   └── README.md
    ├── lib
    │   ├── MyModule
    │   │   ├── MyModuleImpl.py
    │   │   └── __init__.py
    │   └── README.md
    ├── scripts
    │   ├── entrypoint.sh
    │   ├── prepare_deploy_cfg.py
    │   ├── run_async.sh
    │   └── start_server.sh
    ├── test
    │   ├── MyModule_server_test.py
    │   ├── README.md
    │   └── run_tests.sh
    ├── test_local
    │   ├── build_run_tests.sh
    │   ├── readme.txt
    │   ├── run_bash.sh
    │   └── test.cfg
    └── ui
        ├── README.md
        └── narrative
            └── methods
                └── count_contigs_in_set
                    ├── display.yaml
                    ├── img
                    └── spec.json

Let's break this down and examine each component.

The Basics
----------

::

    MyModule
    ├── README.md
    ├── LICENSE
    ├── kbase.yml

All KBase modules are required to have a kbase.yml file, which is a
simple |yaml_link| file that includes basic information
and documentation about your Module. This is where you define your
Module Name, the set of users that have permission to register/edit this
Module, and descriptions of what your Module does. This information is
important for giving you credit for building this module, and is used
throughout KBase for describing to users why they should use your
Module.

README.md and LICENSE files are also required. README.md should contain
basic information about your module, primarily targeted towards other
developers or users who want to browse your code. A LICENSE file is also
required. Your code will not be approved for release on KBase without a
license that is compatible with KBase's  |openSource_link|.

Dockerfile
----------

::

    MyModule
    ├── Dockerfile

One of the central components of a KBase module is the |dockerFile_link|. Nearly
all KBase apps are executed within  |docker_link|
containers so that you can precisely manage your system dependencies and
ensure that code that you are testing locally will be run exactly the
same way in the KBase system. Docker images also act like snapshots that
allow KBase to maintain and run old versions of your module. To
effectively develop modules in KBase that execute code, you should
install Docker locally and familiarize yourself with Docker tools.

Therefore, there are no dependencies required except for a Dockerfile
that can be used to create a Docker image. Instead, in your Dockerfile,
you will define a set of commands that installs any system or package
dependencies beyond what is provided in the KBase base image.

KBase Interface Description Language (KIDL) Specification File
--------------------------------------------------------------

::

    MyModule
    ├── MyModule.spec


The KIDL specification file, often referred to as your "KBase spec 
file", defines the interface of your module. This interface is a set
of function definitions defining what types of parameters they can
accept and what type of data they can return. Using this interface,
the KBase platform will know how to call any function in your module
in a generic way and search the KBase Catalog for your apps.

The ``kb-sdk`` tool compiles your spec file into a set of implementation
stubs in either Python, Perl, or Java that you will use to execute your
code. Technical documentation should also be added to spec files, and
can be used with the kb-sdk to generate nice looking html documentation
for you.

In this simple example of a spec file, there is a single function
defined for counting the number of contigs in a contig set. (Note that a
"workspace" is like a directory that contains particular data objects.)

::

    module MyModule {
        /*
        A string representing a ContigSet id.
        */
        typedef string contigset_id;

        /*
        A string representing a workspace name.
        */
        typedef string workspace_name;

        typedef structure {
            int contig_count;
        } CountContigsResults;

        /*
        Count contigs in a ContigSet
        contigset_id - the ContigSet to count.
        */
        funcdef count_contigs(workspace_name,contigset_id) returns (CountContigsResults)
                    authentication required;
    };

If you initialize an app without the ``-e`` flag, the KIDL file will contain a default spec that
accepts any number of parameters and returns a HTML report.

Files for building and testing the Module
-----------------------------------------
    ├── Makefile
    ├── deploy.cfg
    ├── .travis.yml

KBase modules are currently all built using ``make``, with targets that
can rebuild components of your module and start tests. You can explore
the Makefile directly and add additional targets as needed, but you
should not have to edit significantly the basic Makefile targets
generated by kb-sdk.

Data
----

::

    MyModule
    ├── data
    │   └── README.md

Reference data that is smaller than 100 MB can be stored in this directory.
Larger files and databases cannot be checked into github directly and thus
will have to use the `versioned reference data system <../howtos/work_with_reference_data>`_

App (Method) Implementation
---------------------------

::

    MyModule
    ├── lib
    │   ├── MyModule
    │   │   ├── MyModuleImpl.py
    │   │   └── __init__.py
    │   └── README.md

The lib directory is where the actual implementation code of your app is
defined. In this example, your code consists of a single Python module
with a kb-sdk generated Implementation file, which includes stubs that
you can fill in. In this example there is a single count\_contigs
method. When you run ``make``, this file is updated and recompiled using
``kb-sdk compile`` based on any changes in your spec file. For each
function you define in the KIDL spec file, you will see a corresponding
stub that you can fill in. For example:

::

    def count_contigs(self, ctx, workspace_name, contigset_id):
        # ctx is the context object
        # return variables are: returnVal
        #BEGIN count_contigs
        token = ctx['token']
        wsClient = workspaceService(self.workspaceURL, token=token)
        contigSet = wsClient.get_objects([{'ref': workspace_name+'/'+contigset_id}])[0]['data']
        returnVal = {'contig_count': len(contigSet['contigs'])}
        #END count_contigs
        
        # At some point might do deeper type checking...
        if not isinstance(returnVal, object):
            raise ValueError('Method count_contigs return value ' +
                             'returnVal is not type object as required.')
        # return the results
        return [returnVal]

Note that your implementation code will be defined between
``#BEGIN contig_counts`` and ``#END contig_counts``. Any code written
outside of these ``#BEGIN`` and ``#END`` directives will be overwritten
when the implementation file is rebuilt. The exact code generated by
``kb-sdk compile`` and structure of the lib directory will of course
depend on the programming language you indicated when running
``kb-sdk init``.

It is good practice to limit the amount of code you place directly in
the implementation files. Instead, create your own modules and packages
that perform most of the logic, and only include calls to those
libraries from within the generated Implementation file.

Scripts Directory for Utility/Docker Scripts
--------------------------------------------

::

    MyModule
    ├── scripts
    │   ├── entrypoint.sh
    │   ├── prepare_deploy_cfg.py
    │   ├── run_async.sh
    │   └── start_server.sh

Your module will include by default a few autogenerated scripts to aid
in deployment and to define how your Docker container is run. For the
most part, you can ignore these files. If you need additional utility
scripts, for instance to aid in system dependency installations, fetch a
reference data file that needs to be stored in the Docker image, or
other methods for testing or validation, you should place them in the
scripts directory.

Test Framework
--------------

::

    MyModule
    ├── test
    │   ├── MyModule6_server_test.py
    │   ├── README.md
    │   └── run_tests.sh
    ├── test_local
    │   ├── build_run_tests.sh
    │   ├── readme.txt
    │   ├── run_bash.sh
    │   └── test.cfg

The test directory contains a basic template for performing unit tests
of the code in your module implementation. This is useful for both
debugging and ensuring your module is robust and operates well on a
range of input data. The test_local directory is created by ``make`` to
create a scratch space for running tests locally. It is important that
you do not include any passwords in configuration files that you are
committing to public git repositories.

Narrative Method Specifications
-------------------------------

::

    MyModule
    └── ui
        ├── README.md
        └── narrative
            └── methods
                └── count_contigs_in_set
                    ├── display.yaml
                    ├── img
                    └── spec.json

Apps in the Narrative interface are defined by method specifications
that consist of a JSON specification file and a YAML file for
documentation and display labels. In this example, this module has only
a single Narrative method defined in a folder named
count\_contigs\_in\_set. This folder name also serves as the method ID.
Method IDs must therefore be unique within a module. You can add more
apps by simply adding another directory in the methods folder.

These method specifications indicate which parameters are exposed to the
user, how those parameters are selected (e.g., dropdown, text field,
checkbox) and how those parameters map to your implementation. An
optional ``img`` directory allows you to attach screenshots or other
images that will automatically be included in the app detail page for
your Narrative method.

 `More information about UI specification <../howtos/add_ui_elements.html>`_

.. External links

.. |openSource_link| raw:: html

   <a href="https://github.com/kbase/project_guides/blob/master/LICENSE" target="_blank">open source license</a>

.. |yaml_link| raw:: html

   <a href="http://yaml.org" target="_blank">YAML</a>

.. |INI_link| raw:: html

   <a href="https://en.wikipedia.org/wiki/INI_file" target="_blank">INI</a>

.. |TravisCI_link| raw:: html

   <a href="https://travis-ci.org" target="_blank">Travis-CI </a>

.. |dockerFile_link| raw:: html

   <a href="http://docs.docker.com/engine/reference/builder" target="_blank">Dockerfile</a>

.. |docker_link| raw:: html

   <a href="http://docker.com" target="_blank">Docker</a>

.. |contactUs_link| raw:: html

   <a href="http://kbase.us/contact-us/" target="_blank">contact us</a>

.. Internal links

.. |instructions_link| raw:: html

   <a href="../tutorial/install.html">these instructions </a>
