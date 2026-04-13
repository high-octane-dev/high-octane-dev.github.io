# OCT Format Documentation

## What Is An `.oct` File?
Files with the `.oct` extension are Tupperware container files for model metadata and textures. An .oct file is not just a model file or model format, it is a complete rigging and behavior system including the following:
- FX (e.g. weapon and turbo) attachment points (typically character-only)
- Physics and Constraint systems
- Animation logic (ClipData)
- Bone list and transformation hierarchies (typically character-only)
- Scene object placement and grouping of objects (typically map-only)
    - Includes objects such as drift strips, streetlamps, weapon pickups, neg-bashables, and bashables
- Navigation and gameplay markers (typically map-only)
- Spawn locations (typically map-only)
- And more!

## What Is Contained Within An `.oct` File?
When using `oct decode` with the offsetting tool to convert them into JSON or YAML, the decoded file can be opened in notepad or any notepad equivalent. Upon opening the file you'll find several **"pools."** These **"pools"** (*SubNetworkPool, TexturePool, MaterialPool, SceneTreeNodePool, VertexStreamPool, etc*) all contain **nodes** within them. Every pool has a node-based system which interconnects all data that references each other.

(Examples below show what is contained within `mcqueen.oct`. Please keep in mind that character `.oct` files may differ and contain some differences/alterations from ones contained in map or choreography files.)

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
  },
```
**Pool example 1**


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
```
**Pool example 2** 


Depending on the `.oct` file you are decoding, there may be differences with which pools and data are present, but regardless most of it all works much the same.

## Important Sections Within An `.oct`
While there are various **pools** within an `.oct` file, there are some that are crucial which will be listed here:
- ## SubNetworkPool
    - While there isn't a ton of documentation or research behind `SubNetworkPool`, there are some things we know and can assume from this location. The `"Scene"` listed within `SubNetworkPool` is the main network containing nearly everything. In other words, `SubNetworkPool` is the "main container" holding all objects, controls, and logic for the model.
    ```JSON
    "SubNetworkPool": {
  "SubNetwork#0": {
    "Name": "Scene",
    "Type": "Scene",
    ```
- ## HeaderStrings
    - `HeaderStrings` is a string table referencing everything deeper within the various sections of the `SubNetworkPool` such as `HeaderLayout`, `HeaderStringIndices` and `DataNodePool`.
    - Essentially a dictionary of all named and utilized parts of the model such as controls, bones, facial features, and other systems like weapons.
```JSON
    "HeaderStrings": [
  "Anchor",
  "Lazy_MouthConstraintGroup",
  "Transform",
  "KinematicState",
  "Body_Ctrl",
  ...
]
```
- ## DataNodePool
    - `DataNodePool` contains a lot of the actual data for various parts of the model in various nodes.
    - There are multiple node types in `DataNodePool`, but the most commonly seen types are `Bool`, `Dilation`, `Float`, `Transform`, `ClipData` and `UberConstraintData`. So far the uses and importance of many of these nodes are unknown.
```JSON
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
        },
```
- ## CollisionShapePool
    - The `CollisionShapePool` contains all the collision geometry used by the model.
    - These "Collision Shapes" are simplified shapes used for physics, hit detection, and interaction, not rendering.
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
```

- ## MaterialPool
    - `MaterialPool` is the location where material-related data is stored.
    - Here you can find various data on what shaders, textures, and effects parts of a model can use. Modifying values and entries within a material node can change the look of how a model appears.
```JSON
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
```
<font size="5"><p align="center">Here's an example on how a model can change appearance by modifying the materials.</font></p>
<p align="center"><img src="https://media.discordapp.net/attachments/1097615996128342206/1474063596836098171/Cars_2__Arcade_19_02_2026_15_09_09.png?ex=69dd0a34&is=69dbb8b4&hm=c3f6332fa6f3fdf575a2c022b7be685a5d9d365205cbe6f7d6b68ad956fd8ed3&=&format=webp&quality=lossless&width=1794&height=1009" width="500">
<p align="center"><img src="https://media.discordapp.net/attachments/1097615996128342206/1474063596249157763/Cars_2__Arcade_19_02_2026_15_09_03.png?ex=69dd0a34&is=69dbb8b4&hm=f894a8b5f11f6dc8c9246ab38c284c5c47fcb5dfea0fab1b169c7c8608931024&=&format=webp&quality=lossless&width=1794&height=1009" width="500">

<p align="center"><font size="5">Credit to I Guess for the images</font></p>
-

- ## IndexBufferPool
    - Location where indices (References to vertices) are stored.
- ## VertexBufferPool
    - Stores raw vertex data, holding the actual geometrical points that make up the model.
- ## IndexStreamPool
    - Defines the interpretation of index buffers using primitive types and index formats (uint16, int24, etc).
    - Usually an indicator of specific meshes
- ## VertexStreamPool
    - Defines the interpretation of vertex buffer data from `VertexBufferPool`. (explains what each piece of vertex data means and how to read it.)
    - Describes which attributes exist on a mesh (position, normal, UV, ColorPrelight, etc)

<font size="5"><p align="center">**All of these 4 pools come together to create a pipeline that ultimately results the final, rendered model. If you modify any one of these it will destroy the look of a model. These 4 pools are typically modified in tandem when custom models are implemented.**</font></p>