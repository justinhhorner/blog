I'm working on the setup for a new indie game studio at the moment, and as part of that, I'm creating several core tools and templates that will save me a lot of time during the development of our first game.

First up is the default Unity script templates that furnished with new installs. While in the beginning it can be valuable to see the `Start` and `Update` methods with comments telling you when they are called, I no longer need that to be my starting point for every MonoBehaviour script I create.

Of course, that's not the only template you'll see in Unity, and we can change all of them to be whatever we want. Here's how I do it.

## Template Scripts Directory

On Windows, you should be able to find all the script templates at `C:\Program Files\Unity\Hub\Editor\6000.0.43f1\Editor\Data\Resources\ScriptTemplates`.

Every editor version of Unity ships with a set of templates, so replace the `<version>` so your path will be different if you're not using `6000.0.43f1` like me.

Here you will find a similar set of files:

```bash
Script-NewMonoBehaviourScript.cs.txt
Scripting__Playables__Playable Behaviour Script-NewPlayableBehaviour.cs.txt
Surface Shader-NewSurfaceShader.shader.txt
Template Pipeline-NewSceneTemplatePipeline.cs.txt
Scripting__Playables__Playable Asset Script-NewPlayableAsset.cs.txt
Scripting__ScriptableObject Script-NewScriptableObjectScript.cs.txt
Shader-NewUnlitShader.shader.txt
Definition-NewEditModeTestAssembly.asmdef.txt
Definition-NewTestAssembly.asmdef.txt
Definition-NewAssembly.asmdef.txt
Definition Reference-NewAssemblyReference.asmref.txt
Script-NewEmptyCSharpScript.cs.txt
Scripting__MonoBehaviour Script-NewTestScript.cs.txt
Shader-NewImageEffectShader.shader.txt
Shader-NewComputeShader.compute.txt
Shader-NewRayTracingShader.raytrace.txt
Scripting__MonoBehaviour Script-NewStateMachineBehaviourScript.cs.txt
Scripting__MonoBehaviour Script-NewSubStateMachineBehaviourScript.cs.txt
```

## Modifying New MonoBehaviour Script

Let's take a look at `1-Scripting__MonoBehaviour Script-NewMonoBehaviourScript.cs.txt`.

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class #SCRIPTNAME# : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        #NOTRIM#
    }

    // Update is called once per frame
    void Update()
    {
        #NOTRIM#
    }
}
```

I customized this template to have just an `Awake` method; no comments. Additionally, I'll always use a root namespace for the studio, so I can easily include that in the template.

Also, I don't need to include the collections namespaces, so I removed those as well. This is the resulting template:

```csharp
using UnityEngine;

    #ROOTNAMESPACEBEGIN#
public class #SCRIPTNAME# : MonoBehaviour
{
    void Awake()
    {
        #NOTRIM#
    }
}
#ROOTNAMESPACEEND#
```

Now when I create a new MonoBehaviour script, this is typically what it will look like:

```csharp
using UnityEngine;

namespace StudioName.Game.Namespace
{
    public class #SCRIPTNAME# : MonoBehaviour
    {
        void Awake()
        {
            #NOTRIM#
        }
    }
}
```

## Summary

That's how easy it is to customize the default script templates for a given Unity editor version!

For the studio, I'm automating this a bit further so that templates are hosted on GitHub and can be downloaded then placed in the correct directory via a menu item from one of my core studio tools.

I hope you found this helpful. Until next time...

Take care.
Stay awesome.
