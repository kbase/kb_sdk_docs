Initialize the Module
=====================

.. tip::

   A KBase module contains all of the components for one or more apps. The components are all stored in
   the same directory and are all uploaded to a single github repository. There are pros and cons to 
   bundling several apps in one module versus
   creating one app per module. See |terminology_link| for more information. 


The KBase SDK provides a way to quickly bootstrap a new module by generating most of the required components.

The basic options of the command are:

.. code-block:: bash

    $ kb-sdk init [--example] [--verbose] [--language language] [--user your_kbase_username] module_name

where ``module_name`` must be unique across all SDK modules registered in KBase. You'll be using the bash variables 
``your_kbase_username`` and ``username`` multiple times in this tutorial, so for convenience, define these
variables: ``your_kbase_username=jane.smith`` and ``username=${your_kbase_username}``. You'll also be using the bash variable ``module_name`` 
in this tutorial, but it will change depending on the different examples - first you can define it as ``module_name=HelloWorld``.

Assuming the ``your_kbase_username``, ``username``, and ``module_name`` variables are defined, bootstrapping the HelloWorld module will start with:

.. code-block:: bash

    $ kb-sdk init --language python --user ${your_kbase_username} ${username}${module_name}

This creates a directory called ``{username}HelloWorld`` in your current directory where ``{username}`` is your name/id 
that will make your copy of HelloWorld unique. The newly created directory has all of 
the components needed to start creating apps and is basically a clean slate.  


You must always include the ``-u`` or ``-user`` option with your username to set yourself as the module owner.

You can set your programming language to any of Python, Perl, or Java; for this tutorial, we're using Python.

The ``kb-sdk init`` options are:

.. code:: bash

    -v, --verbose    Show verbose output about which files and directories
                     are being created.
    -u  --user       Set the KBase username of the first module owner
    -e, --example    Populate your repo with an example module in the language
                     set by -l
    -l, --language   Choose a programming language to base your repo on.
                     Currently, we support Perl, Python, and Java. Default is
                     Python

.. warning:: Modules Cannot be Renamed (yet)

	The *rename* feature of kb-sdk only does a partial rename. If you find you need to *rename*, it is easier
	to start over with the 'kb-sdk init' and the new name. The ability to rename is on a list of planned 
	KBase improvements.  

Build the Module
---------------------

.. code-block:: bash

    $ cd ${username}${module_name}
    $ make

The ``make`` command will run some initial code generation.

Module Highlights
---------------------

This module template has the following that you will customize in later steps:

#. A base directory called ``{username}{module_name}`` (see |anatomy_link| for more on the directory structure)
#. A description of the module, its version, and the authors in ``kbase.yml``
#. A specification file that defines the inputs, output, and functions for the module ``{module_name}.spec``. 
#. A script with code for running all the apps in the module called ``lib/{username}{module_name}/{username}{module_name}Impl.py``
#. Specifications for the user interface in the files ``ui/narrative/methods/run_{username}{module_name}/spec.json`` and ``ui/narrative/methods/run_{username}{module_name}/display.yaml``. 

The input/output/function specification is written in an interface definition language |KIDL_link|  
that is specific to KBase. The kbase.yml and display.yaml are in 
YAML format - https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html. YAML format checkers are available online. 
The spec.json is in JSON format - https://www.w3schools.com/js/js_json_syntax.asp. JSON format checkers are available online.

Don't worry about the location of these files; this will be discussed in more detail later in the tutorial.

Create a GitHub Repo
---------------------

You will need to publish your code to a public git repository to make it available for building into a custom 
Docker Image.  Here we'll show a brief example using GitHub.  If following the tutorial step-by-step, you should 
already be working in the correct directory (i.e. the same directory as where you ran the ``make`` command). 
First, commit your codebase into a local git repository. Then, ``git add`` all files created by kb-sdk and commit. 
This creates a git repository locally. Also, ``git commit -m 'Initial commit'`` to write a message describing this 
first commit.

.. code:: bash

    $ cd ${username}${module_name} #only needed if not already in the correct location
    $ git init
    $ git add .
    $ git commit -m 'Initial commit'


Now, create a new GitHub repository on github.com (it can be in your personal GitHub account or in an organization, 
but it must be public). As above, for convenience define ``github_user_name`` as a bash variable, for example
``github_user_name=jsmith10``. Make sure your github repository is initially empty (don't add an initial README.md).

* Direct link to create a repo on github.  |github_link|.
* Github documentation about creating repos: |github_help_link|.

Sync your local codebase to your repository on github:

.. code:: bash

    $ git remote add origin https://github.com/${github_user_name}/${username}${module_name}.git
    $ git push -u origin master


Remember to continuously push your code changes to your github repo by using ``git push``.

Set up your developer credentials
------------------------------------

If you want, this step can wait until you want to test your module. 
However, it is somewhat disruptive to the thought process if you wait until later.
This step can be done any time after the first ``make`` of a module.

The KBase file storage services require authenticated access. During development a dev ``token`` is generated 
and used instead of putting user IDs and passwords in clear text in your module. 
Tokens are good for 90 days and can be used on all modules developed and tested during the 90 days.

Go to |authacct_link|, click **Developer Tokens**, and generate a new token. The
token is only visible on the screen for 5 minutes so make sure you are ready to do the step below.

From the module's root directory, copy and paste that token into ``test_local/test.cfg`` in the value 
for ``test_token``. For example:

.. code:: bash

    test_token=JQGGVCPKCAB2XYHRHZV4H3NF4TN3YEUSA

Where you substitute your own test_token. The above listed token is unauthorized.

.. External links

.. |github_link| raw:: html

   <a href="https://github.com/new" target="_blank">https://github.com/new</a>

.. |github_help_link| raw:: html

   <a href="https://help.github.com/articles/creating-a-new-repository" target="_blank">https://help.github.com/articles/creating-a-new-repository</a>


.. |authacct_link| raw:: html

   <a href="https://narrative.kbase.us/#auth2/account" target="_blank">https://narrative.kbase.us/#auth2/account</a>

.. Internal links

.. |terminology_link| raw:: html

   <a href="../references/terminology.html">terminology</a>

.. |anatomy_link| raw:: html

   <a href="../references/module_anatomy.html">Anatomy of a Module </a>

.. |KIDL_link| raw:: html

   <a href="../references/KIDL_spec.html">(IDL)</a>

