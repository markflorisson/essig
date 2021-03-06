Architecture
============

Requirements
------------

...

Architecture Description
------------------------

Essig is roughly composed out of three components.

First we have the input language, which can be used to describe the micro controller a simulator should be generated for. 

Than we have a generator, which creates an implementation of the private API (see VM) that the VM can use in simulating the micro controller.

Than we have our VM, in which the micro controller will be simulated. It exposes a public API to a client which can then simulate programs like they were running on the simulator

The following diagram illustrates how the components relate to each other.

.. image:: diagrams/Model.png

Micro Controller Definition language
------------------------------------

...

Generator
---------

...

VM
--

The VM is a library in which functions are gathered that most simulators have in common. These functions are defined in terms of a private API for which implementations can be generated using the generator and the definition language. Linked together they form a library that can run programs like they were running on the simulated micro controller. It is state driven in that most functions are parameterised with a state which is then manipulated to the desired state by the function. This design gives the client full freedom in manipulating and reading the state, which when used properly gives interesting possibilities when debugging a program. 

Micro Controller state
----------------------

To be able to let our VM execute any code for any microcontroller we defined a structure that could represent any micro controller state. It has all things that all micro controllers have in common. A RAM, a ROM, some registers and pins. Our specification has all information needed to generate this state and that is exactly what our VM does. Taking compiled micro controller code it creates an initial state for the micro controller specified in it generated component. This state is then passed into every function that needs it to function (either reading from it or manipulating it). This means that our VM can for example manage and run several programs at the same time. We also have a structure for diffs of this state. These diffs can be used to backtrack the program execution. This relatively loose structure gives a lot of interesting capabilities to the client (e.g. save a diffs structure and state to the disk and resume exectution later on (with full backtracking)).

Generated code in the VM
------------------------

The generated portion of the VM works over a private API that the generated code exports. This contains a lot of information (Like the micro controller architecture information for the state), but more importantly the opcode information. This is stored in small structures that contains an instruction handler, a name and a mask. The instruction handlers are private functions of the generated component, but they all have the same signature as defined by the VM. The VM passes execution on to this handlers when an instruction needs to be executed (in the step or cont methods for example)and they then manipulate the state through functions exported by the VM.
