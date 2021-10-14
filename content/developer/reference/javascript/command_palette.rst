===============
Command Palette
===============

Overview
========

The Command Palette is a convenient tool to quickly access and trigger things in Odoo.

.. image:: images/command_palette.png
   :align: center
   :alt: the command palette


It can display available shortcuts and commands. 

A command is an piece of code to execute when the command is triggered. 

.. code-block:: js

    type Command = { name: string, action: () => void, category?: string, hotkey?: string }

.. code-block:: js

    // example of a command
    const sayHelloCommand = {
        "Say Hello Command",
        () => console.log("hello"),
        "useless category", 
        "ctrl-h"
    };

A Command Palette can be displayed by pressing the hotkey ``Control+K``.

This palette displays a list including :

* the commands registered in the service
* any visible elements in the ``ui.activeElement`` that are accessible through an ``[data-hotkey]`` attribute.

.. _command Service:

Command Palette Service
=======================

.. list-table::
   :header-rows: 1

   * - Technical name
     - Dependencies
   * - ``command``
     - ``dialog`` , ``hotkey`` , ``ui``

The ``command`` service offers a way to register commands.

API
---

.. code-block:: js

    registerCommand(command: Command): number;

Register a command to the command palette. It returns a token used to identify the registered command. 
Usefull to unregister it later.

.. code-block:: js

    unregisterCommand(token): void;

Unregister a command by providing its idenfier token.

Usage
-----

.. code-block:: js

    const commandService = useService("command");
    const commandToken = commandService.registerCommand({ "My Command 1", () => { /* do stuff */ } });
    // when you want to unregister it
    commandService.unregisterCommand(commandToken);

.. _useCommand hook:

Hook: useCommand 
================

A hook that ensures your registration exists only when your component is mounted by registering it 
and unregistering it at the right lifecycle time.

API
---

.. code-block:: js

    useCommand(command: Command): void

Register a command.

Usage
-----

.. code-block:: js

   class MyComponent extends Component {
       setup() {
           useCommand({
               name: "My Command 1",
               action: () => {
                   // code when command 1 is executed
               }
           });
           useCommand({
               name: "My Super Command",
               hotkey: "shift-home",
               action: () => {
                   // code when super command is executed
                   // note that the super command can also get executed with the hotkey "shift-home"
               }
           });
       }
   }

.. _commandCategory Registry:

Registry: Command Category 
==========================

The ``command_categories`` gathers the command categories.

The keys in this registry can be used in two differents ways in order to organize the command palette:

* 
  when registering a new command: ``useCommand({ category: "key", ... })``.

* 
  applied as an attribute in the document: ``[data-command-category="key"]``.
  N.B.: if an element should appear in the command palette
  (e.g. it has a ``[data-hotkey]`` attribute), the closest parent (including itself)
  having a ``[data-command-category]`` will provide the category key to seek for in the registry.

API
---
!! does it still exist ? !!
!! there seem to be a namespace !!

``{ label?: string }`` where ``label`` is the displayed name of the category in the command palette. Can be undefined.

Usage
------

.. code-block:: js

    import { registry } from "@web/core/registry";
    registry.category("command_categories").add("new_category")
    registry.category("command_categories").add("another_new_category", { label: "Name of the category" })

Available Categories
--------------------

.. list-table::
   :header-rows: 1

   * - Key
     - Sequence
     - Description
   * - ``main``
     - 10
     - Main Commands
   * - ``app``
     - 20
     - Current App Commands
   * - ``actions``
     - 30
     - More Actions
   * - ``navbar``
     - 40
     - NavBar
   * - ``default``
     - 100
     - Other commands

.. note::
   The sequence is a registry option, to define an order.

.. _commandProvider Registry:

Registry: Command Provider 
==========================

API
---

Usage
-----

.. _commandEmptyList Registry:

Registry: Command Empty List 
============================

API
---

Usage
-----