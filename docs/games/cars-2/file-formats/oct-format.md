# OCT Format Documentation

**Disclaimer**: If you're a non-programmer, please don't be intimidated by random code snippets showing up on this page. You don't need to be a genius to mod this game! The details are just there for the select few people that need them.

## What are `.oct` files?
Files with the `.oct` extension are Tupperware files that describe a scene/game object, and often contain model metadata and textures. An `.oct` file doesn't just describe a 3D model, it typically contains a complete rigging and behavior system encompassing the following:

- FX (e.g. weapon and turbo) attachment points (typically character-only)
- Physics and Constraint systems
- Animation logic (ClipData)
- Bone list and transformation hierarchies (typically character-only)
- Scene object placement and grouping of objects (typically map-only)
    - Includes objects such as drift strips, streetlamps, weapon pickups, neg-bashables, and bashables.
- Navigation and gameplay markers (typically map-only)
- Spawn locations (typically map-only)
- And more!

## What is inside an `.oct` file?
When using `oct decode` with the offsetting tool to convert an `.oct` file into `.json` or `.yaml`, the decoded file can be opened in any text editor. Upon opening the file, you'll find several **pools**. These **pools** (*SubNetworkPool, TexturePool, MaterialPool, SceneTreeNodePool, VertexStreamPool, etc*) all contain **nodes** within them. Every pool has a node-based system which interconnects all data that references each other.

(Examples below show what is contained within `mcqueen.oct`. Please keep in mind that character `.oct` files may differ and contain some differences/alterations from ones contained in map or choreography files.)

**Ex. IndexBufferPool from `mcqueen.oct`:**

```JSON
"IndexBufferPool": {
  "IndexBuffer#0": {
    "Width": 2,
    "Name": "Static",
    "Flags": 91,
    "Size": 52872,
    "FileName": "mcqueen_0.ibuf"
  },
  "IndexBuffer#1": {
    "Width": 2,
    "Name": "Dynamic",
    "Flags": 127,
    "Size": 0
  },
  "IndexBuffer#2": {
    "Width": 4,
    "Name": "Static",
    "Flags": 91,
    "Size": 0
  },
  "IndexBuffer#3": {
    "Width": 4,
    "Name": "Dynamic",
    "Flags": 127,
    "Size": 0
  },
  "IndexBuffer#4": {
    "Width": 1,
    "Name": "Static",
    "Flags": 91,
    "Size": 0
  },
  "IndexBuffer#5": {
    "Width": 1,
    "Name": "Dynamic",
    "Flags": 127,
    "Size": 0
  }
}
```
**Ex. TexturePool from `mcqueen.oct`:**

```JSON
"TexturePool": {
  "Texture#0": {
    "Name": "errorsky",
    "DataCRC": 6919142,
    "SourceFilePath": "prebuilt\\data_dx\\errortextures\\errorsky.dds",
    "Type": "Cube",
    "Data": "file:prebuilt\\data_dx\\errortextures\\errorsky.dds"
  },
  "Texture#1": {
    "Name": "mcqueen_eyealpha",
    "DataCRC": -947753952,
    "SourceFilePath": "characters\\mcqueen\\maps\\mcqueen_eyealpha.png",
    "Type": "2d",
    "Data": "file:characters\\mcqueen\\maps\\mcqueen_eyealpha.dds"
  },
  "Texture#2": {
    "Name": "mcqueen_body_d",
    "DataCRC": -339561918,
    "SourceFilePath": "characters\\mcqueen\\maps\\mcqueen_body_d.png",
    "Type": "2d",
    "Data": "file:characters\\mcqueen\\maps\\mcqueen_body_d.dds"
  },
  "Texture#3": {
    "Name": "mcqueen_body_s",
    "DataCRC": -495403757,
    "SourceFilePath": "characters\\mcqueen\\maps\\mcqueen_body_s.png",
    "Type": "2d",
    "Data": "file:characters\\mcqueen\\maps\\mcqueen_body_s.dds"
  },
}
```

Depending on the `.oct` file you are decoding, there may be differences with which pools and data are present, but regardless most of it all works the same way.

## Important sections within an `.oct`:
While there are various sections within an `.oct` file, there are some that are especially important for modding, which will be listed here:

- ## SubNetworkPool
    - While there isn't a ton of documentation or research behind `SubNetworkPool`, there are some things we know and can derive from context clues. The `"Scene"` listed within `SubNetworkPool` is the main network containing nearly everything. In other words, `SubNetworkPool` is the 'main container' holding all objects, controls, and logic for the scene.

    ```JSON
    "SubNetworkPool": {
      "SubNetwork#0": {
        "Name": "Scene",
        "Type": "Scene",
        ...
      }
    }
    ```

- ## HeaderStrings / HeaderStringIndices / HeaderLayout

    ```JSON
    "SubNetwork#0": {
      "Name": "Scene",
      "Type": "Scene",
      "HeaderStrings": [
        "Anchor",
        "Lazy_MouthConstraintGroup",
        "Transform",
        "KinematicState",
        "Body_Ctrl",
        ...
      ],
      "HeaderStringIndices": [0, 1, 2, 3, 4, 6, 7, ...],
      "HeaderLayout": [0, 1, 1, 197, 6, 1, ...],
      ...
    }
    ```

    These three keys inside a `SubNetwork` definition describe the heirarchy of `Headers`, which are referenced within the nodes inside the network's `DataPool`. `HeaderLayout` is where it all starts; to interpret it correctly, you'll go through this array and process two elements at a time. The first element is an index that will eventually point back to a parent, and the second element will state the number of children. `HeaderStringIndices` maps the indices of headers into indices into the `HeaderStrings` array. (**If you're a non-programmer, you can just think of `HeaderStrings` as an immutable dictionary of all named and utilized parts of the model such as controls, bones, facial features, and other systems like weapons.**)

    ```py
    import json

    # Loading an `.oct` file's `.json` representation into a Python `dict`.
    with open("mcqueen.oct.json", "r") as f:
      tup = json.load(f)

    # Pulling out `HeaderStrings`, `HeaderStringIndices`, and `HeaderLayout`.
    strings = tup["SubNetworkPool"]["SubNetwork#0"]["HeaderStrings"]
    stringIndices = tup["SubNetworkPool"]["SubNetwork#0"]["HeaderStringIndices"]
    layout = tup["SubNetworkPool"]["SubNetwork#0"]["HeaderLayout"]

    # Reconstructing the Header heirarchy:
    headerCount = len(stringIndices) + 1
    headers = [None] * headerCount

    headers[0] = {"Name": "SNAnchor"}

    numCreated = 0
    for i in range(0, len(layout), 2):
      parentIndex = layout[i]
      childCount = layout[i + 1]
      assert(parentIndex < numCreated + 1)
      assert(numCreated + childCount <= len(stringIndices))
      
      for j in range(childCount):
        nameIndex = stringIndices[numCreated]
        numCreated += 1
        headers[numCreated] = {"Name" : strings[nameIndex], "ParentIndex": parentIndex}

    # Using the newly-created map to pull header information from a `DataNode` inside `DataNodePool`:
    for snpNodeKey in tup["SubNetworkPool"]["SubNetwork#0"]["DataNodePool"]:
      snpNode = tup["SubNetworkPool"]["SubNetwork#0"]["DataNodePool"][snpNodeKey]
      _header = headers[snpNode["Header"]]
    ```

- ## DataNodePool
    - A `SubNetwork`'s `DataNodePool` contains a lot of the actual data for various parts of the scene in various `DataNodes`.
    - There are multiple node types in `DataNodePool`, but the most commonly seen types are `Bool`, `Dilation`, `Float`, `Transform`, `ClipData` and `UberConstraintData`. So far the uses and importance of many of these nodes are unknown.

      ```JSON
      "DataNodePool": {
        "DataNode#274": {
          "Header": 216,
          "Type": "ClipData",
          "Static": 1,
          "ClipDataBlock": "base64:AAAAAAAAgD8BAAEAVAAAABgAAAAsAAAAAQABAAAAEgABACEAAAAZAAAAAAAABBAAAAAAAAAAgD8AAAAAAABAQQAAwECAAUA5AAABgFRVVFVUVe7u"
        },
        "DataNode#275": {
          "Header": 209,
          "Type": "Float",
          "Data": 0.0
        },
        "DataNode#276": {
          "Header": 543,
          "Type": "Transform",
          "BasisDataNodeRef": 19,
          "Data": {
            "Translation": [
              0.0,
              0.745424,
              0.43498504
            ],
            "Orientation#Rotor": [
              0.37173793,
              -0.0,
              -0.0,
              0.9283377
            ],
            "Scale": [
              0.70347464,
              0.27266786,
              1.0
            ]
          }
        }
      }
      ```

- ## CollisionShapePool
    - The `CollisionShapePool` contains all the collision geometry used by the model.
    - These "Collision Shapes" are simplified shapes used for physics, hit detection, and interaction, *not* rendering.
    - They are described with a `ShapeType` which contain coordinates indicating length, width, and height. Modifying these is largely unexplored.

      ```JSON
      "CollisionShapePool": {
        "Shape#0": {
          "ShapeType": "ConvexHull",
          "SurfaceType": 0,
          "CollisionLayer": 0,
          "Points": [
            0.6534011,
            0.0,
            -1.9657959,
            -0.6534011,
            0.0,
            1.2484527,
            -0.6534011,
            0.0,
            -1.9657959,
            0.0,
            0.08910293,
            1.9657959,
            0.6534011,
            0.0,
            1.2484527
          ],
          "CollisionMargin": 0.4
        },
        "Shape#1": {
          "ShapeType": "Compound",
          "Shapes": {
            "Shape#0": {
              "ShapeRef": 0,
              "Transform": [
                1.0,
                0.0,
                0.0,
                0.0,
                0.0,
                1.0,
                0.0,
                0.0,
                0.0,
                0.0,
                1.0,
                0.0,
                0.0,
                0.3709195,
                0.0,
                1.0
              ]
            }
          }
        }
      },
      ```

- ## TexturePool
    - Contains raw texture-related data to used by the model.
    - Contains texture entries which state the names of textures and their corresponding file paths as well as the `Type` of texture it is.
    - The purpose of `DataCRC` is unknown, however, `SourceFilePath` is used by the engine for caching purposes; meaning that you'll need to change it if you replace that texture with a new one and it's `SourceFilePath` is non-unique.
    
      ```JSON
      "TexturePool": {
        "Texture#0": {
          "Name": "errorsky",
          "DataCRC": 6919142,
          "SourceFilePath": "prebuilt\\data_dx\\errortextures\\errorsky.dds",
          "Type": "Cube",
          "Data": "file:prebuilt\\data_dx\\errortextures\\errorsky.dds"
        },
        "Texture#1": {
          "Name": "mcqueen_eyealpha",
          "DataCRC": -947753952,
          "SourceFilePath": "characters\\mcqueen\\maps\\mcqueen_eyealpha.png",
          "Type": "2d",
          "Data": "file:characters\\mcqueen\\maps\\mcqueen_eyealpha.dds"
        },
        "Texture#2": {
          "Name": "mcqueen_body_d",
          "DataCRC": -339561918,
          "SourceFilePath": "characters\\mcqueen\\maps\\mcqueen_body_d.png",
          "Type": "2d",
          "Data": "file:characters\\mcqueen\\maps\\mcqueen_body_d.dds"
        },
        "Texture#3": {
          "Name": "mcqueen_body_s",
          "DataCRC": -495403757,
          "SourceFilePath": "characters\\mcqueen\\maps\\mcqueen_body_s.png",
          "Type": "2d",
          "Data": "file:characters\\mcqueen\\maps\\mcqueen_body_s.dds"
        }
      }
      ```

- ## MaterialPool
    - `MaterialPool` is the where material information is stored.
    - Here you can find various data on what shaders, textures, and effects parts of a model can use. Modifying values and entries within a material node can change the look of how a model appears.
    
      ```JSON
      "MaterialPool": {
        "Material#2": {
          "Name": "McQueen_Body",
          "Uuid": "8f0d01c1-d143-4141-98c4-89d5e14b059d",
          "Type": "Shader",
          "Effect": "a_1_dtmmsmqdrqcuceismm_skin",
          "RenderCaps": -2147477440,
          "TextureEntries": {
            "TextureEntry#0": {
              "TextureReference": 2,
              "Usage": "diffuseMapA1"
            },
            "TextureEntry#1": {
              "TextureReference": 3,
              "Usage": "specularMapA1"
            },
            "TextureEntry#2": {
              "TextureReference": 0,
              "Usage": "envMapInstance"
            },
            "TextureEntry#3": {
              "TextureReference": 4,
              "Usage": "environmentReflectivityMapA1"
            }
          },
          "PropertyEntries": {
            "PropertyEntry#0": {
              "Name": "MaxAnisotropy",
              "Type": "Int",
              "DataType": 10,
              "Value": 1
            },
            "PropertyEntry#1": {
              "Name": "scrollU1",
              "Type": "Float",
              "DataType": 0,
              "Value": 0.0
            },
            "PropertyEntry#2": {
              "Name": "scrollV1",
              "Type": "Float",
              "DataType": 0,
              "Value": 0.0
            },
            "PropertyEntry#3": {
              "Name": "diffuseColorA1",
              "Type": "Float",
              "DataType": 2,
              "Value": [
                1.0,
                1.0,
                1.0
              ]
            },
            "PropertyEntry#4": {
              "Name": "diffuseColorTertiary",
              "Type": "Float",
              "DataType": 2,
              "Value": [
                0.5,
                0.5,
                0.5
              ]
            },
            "PropertyEntry#5": {
              "Name": "specularShineA1",
              "Type": "Float",
              "DataType": 0,
              "Value": 100.0
            },
            "PropertyEntry#6": {
              "Name": "specularFresnelA1",
              "Type": "Float",
              "DataType": 0,
              "Value": 0.43
            },
            "PropertyEntry#7": {
              "Name": "environmentReflectivityMapAmountA1",
              "Type": "Float",
              "DataType": 0,
              "Value": 1.0
            },
            "PropertyEntry#8": {
              "Name": "environmentFresnelA1",
              "Type": "Float",
              "DataType": 0,
              "Value": 0.85
            },
            "PropertyEntry#9": {
              "Name": "g_AlphaModifier",
              "Type": "Float",
              "DataType": 0,
              "Value": 1.0
            },
            "PropertyEntry#10": {
              "Name": "CullBackFaces",
              "Type": "Bool",
              "DataType": 10,
              "Value": 1
            }
          }
        },
      }
      ```

<font size="5"><p align="center">Here's an example on how a model can change appearance by modifying the materials.</font></p>

![Fire Shu image 2](../../../assets/fire_shu_2.png)

![Fire Shu image 1](../../../assets/fire_shu_1.png)
<p align="center"><font size="5">Credit to I Guess for the images</font></p>

- ## IndexBufferPool
    - Location where vertex indices are stored.
- ## VertexBufferPool
    - Stores raw vertex data, holding the actual geometrical points that make up the model.
- ## IndexStreamPool
    - Defines the interpretation of index buffers using primitive types and index formats (uint16, int24, etc).
    - Usually an indicator of specific meshes
- ## VertexStreamPool
    - Defines the interpretation of vertex buffer data from `VertexBufferPool`. (explains what each piece of vertex data means and how to read it.)
    - Describes which attributes exist on a mesh (`Position`, `Normal`, `UV`, `ColorPrelight`, etc.)
- ## SceneComponentPool
    - Describes `SceneComponent`s which, as their name suggests, are external components that can be attached to nodes inside `SceneTreeNodePool`. There are two main types of `SceneComponents` that you'll see in Cars 2: `SkinRemap` and `CarEyes`.

      ```json
      "SceneComponentPool": {
        "SceneComponent#0": {
            "Type": "SkinRemap",
            "RemapData": [32, 33, 34, 35, 28, 29, 30, 31, ...]
        },
        "SceneComponent#1": {
            "Type": "CarEyes"
        }
      }
      ```

      For characters, `CarEyes` simply just needs to *be* there. No actual scene tree nodes need to explicitly reference it, instead, it just needs to be attached to an `ComponentLink` inside `AssociationPool`. `SkinRemap`, on the other hand, *must* be there for the game's skinning system to work.

      `SkinRemap` exists because many rigs in this game often have a lot of bones, and as a result, there are not enough vertex shader constant registers on DirectX 9 on desktop Windows to fit 4x4 transformation matrices for every single bone. So, the developers work around this by limiting the number of bones that each mesh can use to 42, and split up the meshes such that this rule is followed. Usually, every mesh has its own `SkinRemap`, but this isn't a hard rule, so if the scene's skeleton has less than 42 bones, you can assign every mesh the same `SkinRemap`.

      Interpreting `SkinRemap` correctly is crucial to importing bone influences. When processing a mesh's `VertexStream`, you interpret the `BoneIndices` element as follows:

      ```py
      # i hate this stupid engine
      boneIndices = struct.unpack("BBBB", vBuf.read(4))
      remapData = tup["SceneComponentPool"][f"SceneComponent{sceneComponentIndex}"]["RemapData"]
      # these now actually match the `BoneID`s listed in each bone's definition in `SceneTreeNodePool`. 
      boneIndices = remapData[boneIndices[0]], remapData[boneIndices[1]], remapData[boneIndices[2]], remapData[boneIndices[3]]
      ```

      (This is pseudocode. `vBuf` represents a handle to an actual `.vbuf` file, seeked to the offset of some `VertexStream` and the inner offset of the `BoneIndices` element. `sceneComponentIndex` represents a `SceneComponentReference` from a mesh listed in `SceneTreeNodePool`.)

      `SkinRemap` doesn't exist on the console versions of Cars 2, ~~because of course it doesn't. Nothing is ever as simple as it should be.~~ because there are more than enough vertex shader constant registers on both PlayStation 3 and Xbox 360 to fit every bone's transform without needing to remap things. ~~I don't know who to blame here. Microsoft? NVIDIA? AMD? I'm not smart enough to figure this one out.~~

- ## SceneTreeNodePool
    - Describes `SceneTreeNode`s. As their name suggests, these describe nodes in the scene tree; bones, meshes, and everything in between are listed here:

      Meshes are either typed as `SubGeometryLit` or `SubGeometry`. Either way, they have vertex streams, scene components, and an index stream attached to them. Usually, the number of vertex streams attached to a mesh is exactly two on the Windows ports of Cars 2, and only one scene component is attached. **However, it is extremely important to note that meshes are not allowed to have three vertex streams attached in Cars 2: Arcade. That only works on Win32_Wii. Trying to use a mesh with three vertex streams on Arcade will result in a game crash.**

      ```json
      "SceneTreeNodePool": {
        "Node#40": {
          "Type": "SubGeometryLit",
          "ParentNodeReferences": [39],
          "LocalToParentMatrix": [
            1.0, 0.0, 0.0, 0.0,
            0.0, 1.0, 0.0, 0.0,
            0.0, 0.0, 1.0, 0.0,
            0.0, 0.0, 0.0, 1.0
          ],
          "BoundingCenter": [-2.3841858e-7, 0.7533521, -0.12268567],
          "NodeName": "bodyMesh_McQueen_Body",
          "RenderCaps": 2363407,
          "MaterialReference": 2,
          "BillboardType": -1,
          "VertexStreamReferences": [0, 1],
          "IndexStreamReference": 0,
          "SceneComponentReferences": [0]
        }
      }
      ```

      Bones are simply typed as `Bone`s. The data inside each bone definition is mostly self-explanatory.

      ```json
      "SceneTreeNodePool": {
        "Node#4": {
          "NodeName": "Body",
          "Uuid": "9b3797f9-b527-45cc-b0bd-158f09a8f5cc",
          "DisplayLayer": 0,
          "Type": "Bone",
          "ParentGeometryReference": 1,
          "BillboardType": -1,
          "BoneID": 1,
          "BoneToModelMatrix": [
            1.0, -0.0, 4.371139e-8, 0.0,
            0.0, 1.0, 0.0, 0.0, 
            -4.371139e-8, 0.0, 1.0, 0.0,
            0.0, 0.43998054, 0.0, 1.0
          ],
          "BoneToStandardMatrix": [
            1.0, 0.0, 0.0, 0.0,
            0.0, 1.0, 0.0, 0.0,
            0.0, 0.0, 1.0, 0.0,
            0.0, -0.43998054, 0.0, 1.0
          ],
          "PivotPoint": [0.0, 0.43998054, 0.0],
          "BoneEndPoint": [-4.371139e-9, 0.43998054, 0.1],
          "BoundingBox": [-1.1486998, -0.3883578, -2.4852846, 1.1486993, 1.015101, 1.7133833],
          "NumAssignedVerts": 2691,
          "VizCullingBound": 1,
          "ParentNodeReferences": [3],
          "LocalToParentMatrix": [
            1.0, 0.0, 0.0, 0.0,
            0.0, 1.0, 0.0, 0.0,
            0.0, 0.0, 1.0, 0.0,
            0.0, 0.0, 0.0, 1.0
          ]
        }
      }
      ```

- ## AssociationPool
    - Describes `Associaton`s. Very little is known about these, unfortunately.

      ```json
      "AssociationPool": {
        "Association#0": {
          "NodeName": "ComponentLink_0",
          "Type": "ComponentLink",
          "NodeRef": 1,
          "ComponentRef": 1
        }
      }
      ```

      One thing we *do* know, is that the first `Association` in a character's scene file will allways be a `ComponentLink`, and will always have the `ComponentRef` point to the `CarEyes` entry in `SceneComponentPool`, no matter what.

<font size="5"><p align="center">IndexBufferPool, IndexStreamPool, VertexBufferPool, and VertexStreamPool come together to create a pipeline that ultimately results in the final, rendered model. If you modify any one of these it will destroy the look of a model. These four pools are usually modified in tandem when custom models are created.</font></p>