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
    - Location where indices (References to vertices) are stored.
- ## VertexBufferPool
    - Stores raw vertex data, holding the actual geometrical points that make up the model.
- ## IndexStreamPool
    - Defines the interpretation of index buffers using primitive types and index formats (uint16, int24, etc).
    - Usually an indicator of specific meshes
- ## VertexStreamPool
    - Defines the interpretation of vertex buffer data from `VertexBufferPool`. (explains what each piece of vertex data means and how to read it.)
    - Describes which attributes exist on a mesh (`Position`, `Normal`, `UV`, `ColorPrelight`, etc.)

<font size="5"><p align="center">All of these 4 pools come together to create a pipeline that ultimately results the final, rendered model. If you modify any one of these it will destroy the look of a model. These 4 pools are usually modified in tandem when custom models are implemented.</font></p>