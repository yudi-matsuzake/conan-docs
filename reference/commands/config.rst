conan config
============

Manage the Conan configuration in the Conan home.


conan config home
-----------------

.. autohelp::
    :command: conan config home


The ``conan config home`` command returns the path of the Conan home folder.

.. code-block:: text

    $ conan config home


.. _reference_commands_conan_config_install:

conan config install
--------------------

.. autohelp::
    :command: conan config install


The ``conan config install`` command is intended to install in the current home a common shared Conan
configuration, like the definitions of ``remotes``, ``profiles``, ``settings``, ``hooks``, ``extensions``, etc.

The command can use as source any of the following:

- A URL pointing to a zip archive containing the configuration files
- A git repository containing the files
- A local folder
- Just one file

Files in the current Conan home will be replaced by the ones from the installation source.
All the configuration files can be shared and installed this way:

- ``remotes.json`` for the definition of remotes
- Any custom profile files inside a ``profiles`` subfolder
- Custom ``settings.yml``
- Custom ``global.conf``
- All the extensions, including plugins, hooks.
- Custom user commands.

This command reads a ``.conanignore`` file which, if present, filters which files and folders
are copied over to the user's Conan home folder.
This file uses `fnmatch <https://docs.python.org/3/library/fnmatch.html>`_ patterns
to match over the folder contents, excluding those entries that match from the config installation.
See `conan-io/command-extensions's .conanignore <https://github.com/conan-io/command-extensions/blob/main/.conanignore>`_ for an example of such a file.


**Examples**:

- Install the configuration from a URL:

  .. code-block:: text

      $ conan config install http://url/to/some/config.zip


- Install the configuration from a URL, but only getting the files inside a *origin* folder
  inside the zip file, and putting them inside a *target* folder in the local cache:

  .. code-block:: text

      $ conan config install http://url/to/some/config.zip -sf=origin -tf=target

- Install configuration from 2 different zip files from 2 different urls, using different source
  and target folders for each one, then update all:

  .. code-block:: text

      $ conan config install http://url/to/some/config.zip -sf=origin -tf=target
      $ conan config install http://url/to/some/config.zip -sf=origin2 -tf=target2
      $ conan config install http://other/url/to/other.zip -sf=hooks -tf=hooks

- Install the configuration from a Git repository with submodules:

  .. code-block:: text

      $ conan config install http://github.com/user/conan_config/.git --args "--recursive"

  You can also force the git download by using :command:`--type git` (in case it is not deduced from the URL automatically):

  .. code-block:: text

      $ conan config install http://github.com/user/conan_config/.git --type git

- Install from a URL skipping SSL verification:

  .. code-block:: text

      $ conan config install http://url/to/some/config.zip --verify-ssl=False

  This will disable the SSL check of the certificate.

- Install a specific file from a local path:

  .. code-block:: text

      $ conan config install my_settings/settings.yml

- Install the configuration from a local path:

  .. code-block:: text

      $ conan config install /path/to/some/config.zip


conan config list
-----------------

.. autohelp::
    :command: conan config list


Displays all the Conan built-in configurations. There are 2 groups:

- ``core.xxxx``: These can only be defined in ``global.conf`` and are used by Conan internally
- ``tools.xxxx``: These can be defined both in ``global.conf`` and profiles, and will be used by
  recipes and tools used within recipes, like ``CMakeToolchain``


.. include:: ../../common/config_list.inc


.. seealso::

    - :ref:`Conan configuration files <reference_config_files>`


conan config show
-----------------

.. autohelp::
    :command: conan config show


Shows the values of the conf items that match the given pattern.

For a *global.conf* consisting of

.. code-block:: text

    tools.build:jobs=42
    tools.files.download:retry_wait=10
    tools.files.download:retry=7
    core.net.http:timeout=30
    core.net.http:max_retries=5
    zlib*/:tools.files.download:retry_wait=100
    zlib*/:tools.files.download:retry=5

You can get all the values:

.. code-block:: text

    $ conan config show "*"

    core.net.http:max_retries: 5
    core.net.http:timeout: 30
    tools.files.download:retry: 7
    tools.files.download:retry_wait: 10
    tools.build:jobs: 42
    zlib*/:tools.files.download:retry: 5
    zlib*/:tools.files.download:retry_wait: 100

Or just those referring to the ``tools.files`` section:

.. code-block:: text

    $ conan config show "*tools.files*"

    tools.files.download:retry: 7
    tools.files.download:retry_wait: 10
    zlib*/:tools.files.download:retry: 5
    zlib*/:tools.files.download:retry_wait: 100

Notice the first ``*`` in the pattern. This will match all the package patterns.
Removing it will make the command only show global confs:

.. code-block:: text

    $ conan config show "tools.files*"

    tools.files.download:retry: 7
    tools.files.download:retry_wait: 10
