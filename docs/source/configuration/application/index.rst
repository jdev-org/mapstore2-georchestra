Application Configuration
=========================

The ``mapstore`` folder inside the geOrchestra configuration directory contains several files dedicated to the configuration of various parts of MapStore.
This sections contain information about the configuration files contained and how they are managed by MapStore.

Back End services
-----------------

MapStore includes some back-end services that provides some special functionalities (printing, proxy). They can be configured by editing some special files the the ``mapstore`` directory.

Printing
^^^^^^^^
MapStore provides a printing module, that is an extension of `mapfish print (v2) <http://www.mapfish.org/doc/print/>`__ with many `customizations <https://github.com/geosolutions-it/mapfish-print/wiki>`__, included in geOrchestra by default. The directory ``mapstore/printing`` is dedicated to the setup of this module.
More information about how to configure these files is available `here <https://mapstore.readthedocs.io/en/latest/developer-guide/printing-module/#print-settings>`__.

Proxy
^^^^^

MapStore brings an internal secure proxy to give support request to external services (WMS, WMTS ...).
The file ``proxy.properties`` contains all the configuration for this file.
More information about configuring this file is available `here <https://github.com/geosolutions-it/http-proxy/wiki/Configuring-Http-Proxy>`__.

Map Viewer
----------

To configure the map viewer(plugins, context editor...), the administrator can edit different files dedicated to the different parts of the application.
If some of these files are not present, the application will take automatically the files from the original `mapstore` webapp.

The files are:

``localConfig.json``
^^^^^^^^^^^^^^^^^^^^

This is the main frontend configuration file.
It contains the main settings of the whole MapStore viewer, including the list of plugins shown in the main viewer, and the tools of the administration page.
You can edit the ``plugins`` section of this file to customize the plugins inside the main viewer, as well as you do from the context-editor UI for other contexts.

See `here <https://mapstore.readthedocs.io/en/latest/developer-guide/local-config/>`__ for more detail about the configuration of this file.

``config.json``
^^^^^^^^^^^^^^^
This configuration file contains the settings of the default map. The administrator can edit this file to change the default layers (e.g. backgrounds) or initial map position.

See `here <https://mapstore.readthedocs.io/en/latest/developer-guide/maps-configuration/#map-options>`__ to learn more about  the format of this file.

``new.json``
^^^^^^^^^^^^
This is the initial map configuration used for new contexts in the context editor.

See `here <https://mapstore.readthedocs.io/en/latest/developer-guide/maps-configuration/#map-options>`__ to learn more about the format of this file.

``pluginsConfig.json``
^^^^^^^^^^^^^^^^^^^^^^
This is the registry of the available plugins in JSON format.
The administrator can edit this file to change the default list of plugins in the context editor plugin selection section.
See `here <https://mapstore.readthedocs.io/en/latest/developer-guide/context-editor-config/>`__ to learn more about the format of  this file).

Advanced Map Viewer Configuration
---------------------------------

Next sections describe some advanced functionalities that can be applied **only** to the Map Viewer settings (``json`` files) and some information about the extension system and its relation with the configuration directory of MapStore.

Patch Files
^^^^^^^^^^^
Configuration files with plugins like ``localConfig.json`` and ``pluginsConfig.json`` may change a lot from one update to another (introducing new plugins, deprecating other, adding or removing old settings).
To maintain changes (manually applied by administrator or automatically applied by the application) across different versions of the application, MapStore provides a *patch system* that allows to preserve configuration files and keep modification in a separate one.

For this reason in the data directory you may add or find some ``.patch`` files (generated by the application, but administrators can edit them on their own) containing only the differences between the original file and the final configuration we want to obtain ( see `here <https://mapstore.readthedocs.io/en/latest/developer-guide/externalized-configuration/#patching-front-end-configuration>`__ for more information about patching system in MapStore).
The final version of the file is provided directly by the application via ``http`` when the configuration file is required.

Dynamic Files
^^^^^^^^^^^^^
MapStore allows to install extensions from the UI (administration section, when editing a new context).
When a new plugin is installed, several files will be written in the data-directory.

* ``extensions.json``: dynamic registry of currently installed extensions, in JSON format
* ``dist`` subfolder: will contain all the dynamically uploaded extensions, one folder for each of them, with all the extension assets (javascript bundles, translations, etc.)
* ``pluginsConfig.json.patch``: a **patch** file for new plugins installed from the UI.

These files are all updated and managed by the extensions upload functionality.

To set a different folder for these files, you have to set the ``georchestra.extensions`` JVM option to the desired path.
If not set, also dynamic files will be stored in the standard configuration directory.

Read/Write configuration directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Since MapStore needs to write in the data directory and the administrator may want to edit configuration files on its own, MapStore provides also a system of **multiple configuration directories**, that makes possible to separate the manual changes from the automatic ones.

In particular an administrator can configure more then one ``georchestra.datadir`` values, separated by comma (see `here <https://mapstore.readthedocs.io/en/latest/developer-guide/externalized-configuration/#multiple-data-directory-locations>`_ for specific MapStore implementation details about this part).

The configuration files read/write rules are the following:

* Reading the configuration, MapStore will search for the first file found, looking in order in every directory provided. If the file is not found in any directory, MapStore will take the one present in the webapp.
* Writing operation will be applied **only** in the **first** directory of the list.

geOrchestra can be configured to have a **write** and a **read-only** configuration directory simply by giving 2 directories in the ``georchestra.datadir`` value.
MapStore will write only in the first, the second one will be a read-only configuration dir, that can be edited manually by the administrator.
