Preference Settings
===================

When you run Phoebus, you may find that it cannot connect to your control system
because for example the EPICS Channel Access address list is not configured.

To locate available preferences, refer to the appendix
:ref:`preference_settings`
or check the source code for files named ``*preferences.properties``,
for example in the ``core-pv`` sources::

   # ----------------------------------------
   # Package org.phoebus.applications.pvtable
   # ----------------------------------------

   # Show a "Description" column that reads xxx.DESC?
   show_description=true
   
   # -------------------------
   # Package org.phoebus.pv.ca
   # -------------------------
   
   # Channel Access address list
   addr_list=


Create a file ``settings.ini`` that lists the settings you want to change::

   # Format:
   #
   #  package_name/setting=value
   org.phoebus.pv.ca/addr_list=127.0.0.1 my_ca_gateway.site.org:5066


Start Phoebus like this to import the settings from your file::

  phoebus.sh -settings /path/to/settings.ini

Conceptually, preference settings are meant to hold critical configuration
parameters like the control system network configuration.
They are configured by system administrators, and once they are properly adjusted
for your site, there is usually no need to change them.

Most important, these are not settings that an end user would need to see
and frequently adjust during ordinary use of the application.
For such runtime settings, each applicaition needs to offer user interface options
like context menus or configuration dialogs.

When you package phoebus for distribution at your site, you can also place
a file ``settings.ini`` in the installation location (see :ref:`locations`).
At startup, Phoebus will automatically load the file ``settings.ini``
from the installation location, eliminating the need for your users
to add the ``-settings ..`` command line option.


.. _preferences-notes:

Developer Notes
---------------

In your code, create a file with a name that ends in ``*preferences.properties``.
In that file, list the available settings, with explanatory comments::

   # ---------------------------------------
   # Package org.phoebus.applications.my_app
   # ---------------------------------------

   # Explain what this setting means,
   # what values are allowed etc.
   my_setting=SomeValue

Load that as the default, then read the ``java.util.prefs.Preferences`` like this::

    package org.phoebus.applications.my_app
    
    import org.phoebus.framework.preferences.PreferencesReader;

    final PreferencesReader prefs = new PreferencesReader(getClass(), "/my_app_preferences.properties");
    
    String pref1 = prefs.get("my_setting");
    Boolean pref2 = prefs.getBoolean("my_other_setting");
    // .. use getInt, getDouble as needed

The ``PreferencesReader`` loads defaults from the property file,
then allows overrides via the ``java.util.prefs.Preferences`` API.
By default, the user settings are stored in a ``.phoebus`` folder
in the home directory.
This location can be changed by setting the Java property ``java.util.prefs.userRoot``.

In the future, a preference UI might be added, but as mentioned
the preference settings are not meant to be adjusted by end users.
