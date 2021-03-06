# Frequently Asked Questions

## Scripting Runtime Environment not setup correctly

If you haven't switched your scripting runtime version from .NET 3.5 to .NET 4.6
or .NET 4.x, you will see such error message:

```console
error CS1061: Type `System.Text.StringBuilder' does not contain a definition for `Clear' and no extension method `Clear' of type `System.Text.StringBuilder' could be found. Are you missing an assembly reference?
```

This is because .NET 3.5 doesn't support method Clear() for StringBuilder, refer
to [Setting Up The ML-Agents Toolkit Within
Unity](Installation.md#setting-up-ml-agent-within-unity) for solution.

## TensorFlowSharp flag not turned on

If you have already imported the TensorFlowSharp plugin, but haven't set
ENABLE_TENSORFLOW flag for your scripting define symbols, you will see the
following error message:

```console
You need to install and enable the TensorFlowSharp plugin in order to use the Internal Brain.
```

This error message occurs because the TensorFlowSharp plugin won't be usage
without the ENABLE_TENSORFLOW flag, refer to [Setting Up The ML-Agents Toolkit
Within Unity](Installation.md#setting-up-ml-agent-within-unity) for solution.

## Instance of CoreBrainInternal couldn't be created

If you try to use ML-Agents in Unity versions 2017.1 - 2017.3, you might
encounter an error that looks like this:

```console
Instance of CoreBrainInternal couldn't be created. The the script
class needs to derive from ScriptableObject.
UnityEngine.ScriptableObject:CreateInstance(String)
```

You can fix the error by removing `CoreBrain` from CoreBrainInternal.cs:16,
clicking on your Brain Gameobject to let the scene recompile all the changed
C# scripts, then adding the `CoreBrain` back. Make sure your brain is in
Internal mode, your TensorFlowSharp plugin is imported and the
ENABLE_TENSORFLOW flag is set. This fix is only valid locally and unstable.

## Tensorflow epsilon placeholder error

If you have a graph placeholder set in the Internal Brain inspector that is not
present in the TensorFlow graph, you will see some error like this:

```console
UnityAgentsException: One of the TensorFlow placeholder could not be found. In brain <some_brain_name>, there are no FloatingPoint placeholder named <some_placeholder_name>.
```

Solution: Go to all of your Brain object, find `Graph placeholders` and change
its `size` to 0 to remove the `epsilon` placeholder.

Similarly, if you have a graph scope set in the Internal Brain inspector that is
not correctly set, you will see some error like this:

```console
UnityAgentsException: The node <Wrong_Graph_Scope>/action could not be found. Please make sure the graphScope <Wrong_Graph_Scope>/ is correct
```

Solution: Make sure your Graph Scope field matches the corresponding Brain
object name in your Hierarchy Inspector when there are multiple Brains.

## Environment Permission Error

If you directly import your Unity environment without building it in the
editor, you might need to give it additional permissions to execute it.

If you receive such a permission error on macOS, run:

```sh
chmod -R 755 *.app
```

or on Linux:

```sh
chmod -R 755 *.x86_64
```

On Windows, you can find
[instructions](https://technet.microsoft.com/en-us/library/cc754344(v=ws.11).aspx).

## Environment Connection Timeout

If you are able to launch the environment from `UnityEnvironment` but then
receive a timeout error, there may be a number of possible causes.

* _Cause_: There may be no Brains in your environment which are set to
  `External`.  In this case, the environment will not attempt to communicate
  with python. _Solution_: Set the Brains(s) you wish to externally control
  through the Python API to `External` from the Unity Editor, and rebuild the
  environment.
* _Cause_: On OSX, the firewall may be preventing communication with the
  environment. _Solution_: Add the built environment binary to the list of
  exceptions on the firewall by following
  [instructions](https://support.apple.com/en-us/HT201642).
* _Cause_: An error happened in the Unity Environment preventing communication.
  _Solution_: Look into the [log
  files](https://docs.unity3d.com/Manual/LogFiles.html) generated by the Unity
  Environment to figure what error happened.

## Communication port {} still in use

If you receive an exception `"Couldn't launch new environment because
communication port {} is still in use. "`, you can change the worker number in
the Python script when calling

```python
UnityEnvironment(file_name=filename, worker_id=X)
```

## Mean reward : nan

If you receive a message `Mean reward : nan` when attempting to train a model
using PPO, this is due to the episodes of the Learning Environment not
terminating. In order to address this, set `Max Steps` for either the Academy or
Agents within the Scene Inspector to a value greater than 0. Alternatively, it
is possible to manually set `done` conditions for episodes from within scripts
for custom episode-terminating events.
