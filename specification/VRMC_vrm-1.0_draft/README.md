# VRMC_vrm

## Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
  - [Format and Extension](#format-and-extension)
  - [Version](#version)
  - [Model's Meta Information](#models-meta-information)
  - [Humanoid](#humanoid)
  - [Model Normalization](#model-normalization)
  - [BlendShape](#blendshape)
  - [First Person](#first-person)
  - [LookAt](#lookat)
  - [SpringBone](#springbone)
  - [Material](#material)
  - [Constraint](#constraint)
  - [Update Order](#update-order)
- [glTF Schema Updates](#gltf-schema-updates)
  - [Naming Restrictions](#naming-restrictions)
  - [Mesh Storage Restrictions](#mesh-storage-restrictions)
- [JSON Schema](#json-schema)
- [Known Implementations](#known-implementations)
  - [VRM-0.0](#vrm-00)
- [Resources](#resources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* Shindo Tatsuro
* Hirose Junmichi
* Po-Chang Su

## Status

* In development

## Dependencies

Written against the glTF 2.0 spec.

* Require VRMC_materials_mtoon extension
* Require KHR_materials_unlit extension
* Require KHR_texture_transform extension

## Overview

Store the information of the humanoid model for VR avatar in the GLTF scene.
By adding more information in the VRM extension along with additional constraints imposing on existing parts in GLTF, humanoids model can be manipulated in unified manners from the program.

### Format and Extension

Save in `.glb` format and use `.vrm` as the extension.

### Version

`extensions.VRMC_vrm`

| Name        | Note |
|:------------|:-----|
| specVersion |      |

### Model's Meta Information

`extensions.VRMC_vrm.meta`

#### Model name, author, etc.

| Name               | Note                                                                                                                           |
|:-------------------|:-------------------------------------------------------------------------------------------------------------------------------|
| title              | Describe the name of the avatar model                                                                                          |
| version            | Describe the version that creates the model                                                                                    |
| author             | Describe the name of the author of the model                                                                                   |
| contactInformation | Describe the contact information of the author                                                                                 |
| reference          | Describe original / related works of the avatar (URL), if any                                                                  |
| thumbnailImage     | The index to access the thumbnail image of the avatar model in gltf.images. The texture resolution of 2048x2048 is recommended |

#### Personation / Characterization Permission
##### A person who can perform with this avatar

`extensions.VRMC_vrm.meta.allowedUser`

| Name                     | Note |
|:-------------------------|:-----|
| OnlyAuthor               |      |
| ExplicitlyLicensedPerson |      |
| Everyone                 |      |

##### Perform violent acts with this avatar

`extensions.VRMC_vrm.meta.violentUsage`

| Name     | Note |
|:---------|:-----|
| Disallow |      |
| Allow    |      |

##### Perform sexual acts with this avatar

`extensions.VRMC_vrm.meta.sexualUsage`

| Name     | Note |
|:---------|:-----|
| Disallow |      |
| Allow    |      |

##### Commercial use

`extensions.VRMC_vrm.meta.commercialUsageName`

| Name     | Note |
|:---------|:-----|
| Disallow |      |
| Allow    |      |

##### extensions.VRMC_vrm.meta.otherPermissionUrl

* Describe the URL links of license with regard to other permissions

#### Redistribution / Modifications License

##### License Type

`extensions.VRMC_vrm.meta.license`

| Name                      | Note                                               |
|:--------------------------|:---------------------------------------------------|
| Redistribution Prohibited | Redistribution Prohibited                          |
| CC0                       | Copyright Wavier (CC0)                             |
| CC_BY                     | Creative Commons CC BY License (CC_BY)             |
| CC_BY_NC                  | Creative Commons CC BY NC License (CC_BY_NC)       |
| CC_BY_SA                  | Creative Commons CC BY SA License (CC_BY_SA)       |
| CC_BY_NC_SA               | Creative Commons CC BY NC SA License (CC_BY_NC_SA) |
| CC_BY_ND                  | Creative Commons CC BY ND License (CC_BY_ND)       |
| CC_BY_NC_ND               | Creative Commons CC BY NC ND License (CC_BY_NC_ND) |
| Other                     | Other                                              |

##### extensions.VRMC_vrm.meta.otherLicenseUrl

Describe the URL links of other license.

### Humanoid

`extensions.VRMC_vrm.humanoid`

VRM defines specifications of the humanoid model.
Here the part which describes the humanoid bone in the GLTF node hierarchy is called Humanoid / Skeleton.

#### Humanoid / Bone (enum)

#### Humanoid / Skeleton Specification

* Each bone is unique
* All required bones are included
* Inserting a node that is not related to humanoid bone is allowable (e.g. LowerLeg’s parent is an object cube and this cube’s parent is UpperLeg, etc.).
* `Orientation` is the recommended positional relationship for TPose. It is not recommended that the same (or near the same) coordinate applies to parent and child as it is likely to cause troubles when judging bone orientations in the application. Please set a valid distance (in floating point) that can separate them. 

##### Torso

| Bone Name  | Required | Parent Bone | Orientation | Estimated Position | Note                                                                  |
|:-----------|:---------|:------------|:------------|--------------------|-----------------------------------------------------------------------|
| hips       | Required |             | Y+          | Crotch             | Usually only this bone moves and the other bones in Torso rotate only |
| spine      | Required | hips        | Y+          | Top of pelvis      |                                                                       |
| chest      |          | spine       | Y+          | Bottom of rib cage | 0.X is required                                                       |
| upperChest |          | chest       | Y+          |                    |                                                                       |
| neck       |          | upperChest  | Y+          | Base of neck       | 0.X is required                                                       |

* The corresponding bone of pelvis towards `Y-` direction from hips is deprecated (inverted pelvis)

##### Head

| Bone Name | Required | Parent Bone | Orientation | Estimated Position | Note                                         |
|:----------|:---------|:------------|-------------|--------------------|----------------------------------------------|
| head      | Required | neck        |             | Top of neck        |                                              |
| leftEye   |          | head        |             |                    | The model's eye movement controlled by bones |
| rightEye  |          | head        |             |                    | The model's eye movement controlled by bones |
| jaw       |          | head        |             |                    |                                              |

##### Leg

| Bone Name     | Required | Parent Bone   | Orientation | Estimated Position | Note |
|:--------------|:---------|:--------------|-------------|--------------------|------|
| leftUpperLeg  | Required | hips          | Y-          | groin              |      |
| leftLowerLeg  | Required | leftUpperLeg  | Y-          | knee               |      |
| leftFoot      | Required | leftLowerLeg  | Y-Z+        | ankle              |      |
| leftToes      |          | leftFoot      |             |                    |      |
| rightUpperLeg | Required | hips          |             | groin              |      |
| rightLowerLeg | Required | rightUpperLeg | Y-          | knee               |      |
| rightFoot     | Required | rightLowerLeg | Y-Z+        | ankle              |      |
| rightToes     |          | rightFoot     |             |                    |      |

##### Arm

| Bone Name     | Required | Parent Bone   | Orientation | Estimated Position | Note |
|:--------------|:---------|:--------------|-------------|--------------------|------|
| leftShoulder  |          | chest         | X-          |                    |      |
| leftUpperArm  | Required | leftShoulder  | X-          | Base of upper arm  |      |
| leftLowerArm  | Required | leftUpperArm  | X-          | elbow              |      |
| leftHand      | Required | leftLowerArm  | X-          | wrist              |      |
| rightShoulder |          | chest         | X+          |                    |      |
| rightUpperArm | Required | rightShoulder | X+          | Base of upper arm  |      |
| rightLowerArm | Required | rightUpperArm | X+          | elbow              |      |
| rightHand     | Required | rightLowerArm | X+          | wrist              |      |

##### Finger

| Bone Name               | Required | Parent Bone             | Orientation | Estimated Position | Note |
|:------------------------|:---------|:------------------------|-------------|--------------------|------|
| leftThumbProximal       |          | leftHand                | +X+Z        |                    |      |
| leftThumbIntermediate   |          | leftThumbProximal       | +X          |                    |      |
| leftThumbDistal         |          | leftThumbIntermediate   | +X          |                    |      |
| leftIndexProximal       |          | leftHand                | +X          |                    |      |
| leftIndexIntermediate   |          | leftIndexProximal       | +X          |                    |      |
| leftIndexDistal         |          | leftIndexIntermediate   | +X          |                    |      |
| leftMiddleProximal      |          | leftHand                | +X          |                    |      |
| leftMiddleIntermediate  |          | leftMiddleProximal      | +X          |                    |      |
| leftMiddleDistal        |          | leftMiddleIntermediate  | +X          |                    |      |
| leftRingProximal        |          | leftHand                | +X          |                    |      |
| leftRingIntermediate    |          | leftRingProximal        | +X          |                    |      |
| leftRingDistal          |          | leftRingIntermediate    | +X          |                    |      |
| leftLittleProximal      |          | leftHand                | +X          |                    |      |
| leftLittleIntermediate  |          | leftLittleProximal      | +X          |                    |      |
| leftLittleDistal        |          | leftLittleIntermediate  | +X          |                    |      |
| rightThumbProximal      |          | rightHand               | -X          |                    |      |
| rightThumbIntermediate  |          | rightThumbProximal      | -X          |                    |      |
| rightThumbDistal        |          | rightThumbIntermediate  | -X+Z        |                    |      |
| rightIndexProximal      |          | rightHand               | -X          |                    |      |
| rightIndexIntermediate  |          | rightIndexProximal      | -X          |                    |      |
| rightIndexDistal        |          | rightIndexIntermediate  | -X          |                    |      |
| rightMiddleProximal     |          | rightHand               | -X          |                    |      |
| rightMiddleIntermediate |          | rightMiddleProximal     | -X          |                    |      |
| rightMiddleDistal       |          | rightMiddleIntermediate | -X          |                    |      |
| rightRingProximal       |          | rightHand               | -X          |                    |      |
| rightRingIntermediate   |          | rightRingProximal       | -X          |                    |      |
| rightRingDistal         |          | rightRingIntermediate   | -X          |                    |      |
| rightLittleProximal     |          | rightHand               | -X          |                    |      |
| rightLittleIntermediate |          | rightLittleProximal     | -X          |                    |      |
| rightLittleDistal       |          | rightLittleIntermediate | -X          |                    |      |

#### TPose Specification

The skeleton must be the T-pose as the initial posture. 
The T-pose specifications are as follows:

* The root bone (topmost bone, hips' ancestor) is at the origin
* The model's heels touch the ground ((Y=0))

| Model's Body Part                                                    | Orientation |
|:---------------------------------------------------------------------|:------------|
| Front                                                                | Z-          |
| Left                                                                 | X+          |
| Up                                                                   | Y+          |
| Torso (hips - spine - chest - upperChest - upperChest - neck - head) | Y+          |
| Left / right leg (upperLeg - lowerLeg)                               | Y-          |
| Left / right foot                                                    | Y-Z-        |
| Left / eight toe                                                     | Z-          |
| Left arm (shoulder - upperArm - lowerArm - hand) and each finger     | X+          |
| Left thumb (Proximal)                                                | X+Z-        |
| Right arm (shoulder - upperArm - lowerArm - hand) and each finger    | X-          |
| Right thumb (Proximal)                                               | X-Z-        |
| Palm                                                                 | Y-          |

### Model Normalization

An algorithm to impose constraints on the GLTF part of the VRM model.

#### Node Normalization

VRM Node is restricted by the followings: 
For initial pose (T-Pose):

* No rotation
* No scaling (1, 1, 1)

#### Mesh Normalization

To achieve node normalization, the mesh needs to be normalized.
The normalized mesh is not skinned and is restricted by the followings:

* Overlapping with initial Humanoid / Skeleton (if skin.root exists, add with skin.root's coordinate)
* The forward direction is Z-
* The right direction is X+
* The upward direction is Y+

As a result, `skin.inverseBindMatrices` includes only negative translation of the target bone relative to the world coordinate without rotation and scaling.

```js
// Inverse Matrices
// [x, y, z] = bone.translation - skin.translation
[1, 0, 0, 0]
[0, 1, 0, 0]
[0, 0, 1, 0]
[-x,-y,-z, 1]
```

The restrictions on skinning are as follows:

* No skin.root (scene root) or the node at the origin

#### Normalization Algorithm

* Make the model's pose as T-Pose
* Skin unavailable
  * Multiply each vertex by Node.matrix
* Skin available
  * If there are vertices without BoneWeight, give BoneWeight to skin.root
  *  Skin the mesh and reimport the result as a mesh (bake, freeze)
* Remove the node's rotation / scaling

### BlendShape

VRM extends MorphTarget for humanoids.
BlendShape means grouping multiple MorphTarget (Blink, mouth shape AIUEO, Joy, Angry, Sorrow, Fun).
Also, you can make a blend shape by changing material values (color, texture offset+scale).

#### BlendShapePreset List

##### Facial expression (enum)

| Name    | Note                                                             |
|:--------|:-----------------------------------------------------------------|
| neutral | Standby state。`TODO: Deprecate and provide the ability to bake` |
| joy     |                                                                  |
| angry   |                                                                  |
| sorrow  |                                                                  |
| fun     |                                                                  |

##### Lip-sync

| Name | Note |
|:-----|:-----|
| a    | あ   |
| i    | い   |
| u    | う   |
| e    | え   |
| o    | お   |

##### Blink

| Name    | Note                 |
|:--------|:---------------------|
| blink   | Blink with both eyes |
| blink_L | Blink with left eye  |
| blink_R | Blink with right eye |

##### BlendShape LookAt

| Name      | Note                                                                                             |
|:----------|:-------------------------------------------------------------------------------------------------|
| lookUp    | For models whose eye gazes move with Morph as opposed to bone. More details in [LookAt](#lookat) |
| lookDown  | For models whose eye gazes move with Morph as opposed to bone. More details in [LookAt](#lookat) |
| lookLeft  | For models whose eye gazes move with Morph as opposed to bone. More details in [LookAt](#lookat) |
| lookRight | For models whose eye gazes move with Morph as opposed to bone. More details in [LookAt](#lookat) |

##### User Definition

| Name   | Note                                                                             |
|:-------|:---------------------------------------------------------------------------------|
| custom | Customize the BlendShape for the application. Please use Name for identification |

#### BlendShape Specification

`extensions.VRMC_vrm.blendshape`

| Name                          | Note                                                                                                            |
|:------------------------------|:----------------------------------------------------------------------------------------------------------------|
| blendShapeGroups[*].preset    | Target BlendShapePreset                                                                                         |
| blendShapeGroups[*].name      | Any. (A character string that is unique and can be used as the file name)                                       |
| blendShapeGroups[*].is_binary | In the case of `True`: value!=0 will be considered as 1                                                         |
| blendShapeGroups[*].values    | BlendShapeBind list (described later)                                                                           |
| blendShapeGroups[*].materials | MaterialValueBind list (described later)                                                                        |
| blendShapeGroups[*].ignoreBlink  | When this BlendShape Weight is not 0, the weight of blink, blink_L, blink_R are forced to be 0               |
| blendShapeGroups[*].ignoreLookAt | When this BlendShape Weight is not 0, the weight of lookUp, lookDown, lookLeft, lookRight are forced to be 0 |
| blendShapeGroups[*].ignoreMouth  | When this BlendShape Weight is not 0, the weight of A, I, U, E, O are forced to be 0                         |

##### BlendShapeBind

`extensions.VRMC_vrm.blendshape[*].binds[*]`

Bind BlendShape to MorphTarget.

| Name   | Note                                                                                                               |
|:-------|:-------------------------------------------------------------------------------------------------------------------|
| mesh   | Index of target mesh                                                                                               |
| index  | Index of target morph (All primitives have the same morph [Mesh Storage Restrictions](#mesh-storage-restrictions)) |
| weight | Applied Morph value                                                                                                |

##### MaterialValueBind

Bind BlendShape to MaterialValue 

| Name        | Note                                             |
|:------------|:-------------------------------------------------|
| material    | Index of target material                         |
| target      | material's items (color, uvScale, uvOffset)      |
| targetValue | Applied material value (float4)                  |

* color: change mainColor
* uvScale: change the UV parameter of the texture
* uvOffset: change the UV parameter of the texture

#### BlendShape Update Algorithm

##### BlendShape Identification

* If preset is not custom, use preset for identification
* If preset is custom, use name for identification

##### MorphTarget

* Set all the morph targets to 0
* The value is accumulated based on input blend shapes' weights `void AccumulateValue(BlendShapeClip clip, float value)`
* The accumulated values are all applied at once

##### Material Value

* Set all the materials to their initial states (not 0)
* The value is accumulated based on input blend shapes' weights `void AccumulateValue(BlendShapeClip clip, float value)`
* The accumulated values are all applied at once `Base + (A.Target - Base) * A.Weight + (B.Target - Base) * B.Weight`

### First Person

VRM defines first-person view settings for VR

`extensions.VRMC_vrm.firstPerson`

| Name                  | Note                                                                                                                                                      |
|:----------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------|
| firstPersonBone       | The bone that corresponds to the headset. Default is Head. TODO: `Probably unnecessary. Besides head, is there any system that can work with other part?` |
| firstPersonBoneOffset | Position offset from the head bone to the headset (tracker)                                                                                               |

#### MeshAnnotation

Control renderings in Mesh units

`extensions.VRMC_vrm.firstPerson.meshAnnotations[*]`

| Name            | Note                  |
|:----------------|:----------------------|
| mesh            | Index for target mesh |
| firstPersonFlag | Described below       |

firstPersonFlag. When using a model in the VR application, the renderings from the headset and the others are separated.

#### MeshAnnotation (enum)

`thirdPersonOnly` is for hiding the rendering of the model's head portion in VR. Since we assumed that the VR camera's viewpoint is at the head position, by setting thirdPersonOnly, the situation where users may inadvertently see the model's face, head and hair can be precented.

`firstPersonOnly` is a setting that objects rendered by the first-person camera can only be seen from the first-person view. Normally `firstPersonOnly` will not be used unless the application side needs it for special purposes.

Set `both` for Objects that do not necessarily being rendered separately so that those objects can be rendered by all the cameras.

`auto` is used to divide the mesh into the head portion (` thirdPersonOnly`) and the others (`both`). See the details in the next section.

| Name            | First Person Camera (VR viewpoint) | Other Cameras     | Example                                                                      |
|:----------------|:-----------------------------------|:------------------|------------------------------------------------------------------------------|
| thirdPersonOnly | Rendering disable                  | Rendering enable  | Objects that block VR view such as face, eyes, head, hair, hat, helmet, etc. |
| firstPersonOnly | Rendering enable                   | Rendering disable | User interface that is only visible from the user's perspective view         |
| both            | Rendering enable                   | Rendering enable  |                                                                              |

#### MeshAnnotation.Auto Algorithm

* Check all the vertices in Mesh and collect vertices with bone weights for Head and its descendant
* Binarize Mesh into the one containing triangles with the above-mentioned vertices and the other one containing triangles with the rest of the vertices 
* Set the mesh containing the above-mentioned vertices as ThirdPersonOnly, and the mesh containing the other vertices as Both

### LookAt

`extensions.VRMC_vrm.lookAt`

VRM defines eye gaze control for Humanoid.

| Name       | Note               |
|:-----------|:-------------------|
| lookAtType | Bone or BlendShape |

| Name            | Note                                                                          |
|:----------------|:------------------------------------------------------------------------------|
| bone            | Control the eye gazes with leftEye bone and rightEye bone                     |
| blendShape      | Control the eye gazes with BlendShape's LookAt, LookDown, LookLeft, LookRight |
| horizontalInner | The movable range of horizontal inward direction                              |
| horizontalOuter | The movable range of horizontal outward direction                             |
| verticalDown    | The movable range of vertical downward direction                              |
| verticalUp      | The movable range of vertical upward direction                                |

#### Horizontal Inward / Outward, Vertical Upward / Downward

Adjust the movable range for the eyes.

##### Horizontal Inward Direction

`extensions.VRMC_vrm.lookAt.horizontalInner`

* The left eye moves right
* The right eye moves left
* Bone type: yRange specifies the maximum rotation angle based on the Euler angle (degree) of the leftEye / rightEye bone
* BlendShape type: yRange specifies the maximum applicable degree of LookLeft / LookRight BlendShape (up to 1.0)

```
Y = clamp(yaw, 0, horizontalInner.xRange)/horizontalInner.xRange * horizontalInner.yRange 
```

##### Horizontal Outward Direction

`extensions.VRMC_vrm.lookAt.horizontalOuter`

* The left eye moves left
* The right eye moves right
* Bone type: yRange specifies the maximum rotation angle based on the Euler angle (degree) of the leftEye / rightEye bone
* BlendShape type: yRange specifies the maximum applicable degree of LookLeft / LookRight BlendShape (up to 1.0)

```
Y = clamp(yaw, 0, horizontalOuter.xRange)/horizontalOuter.xRange * horizontalOuter.yRange 
```

##### Vertical Downward Direction

`extensions.VRMC_vrm.lookAt.verticalDown`

* The left eye moves downwards
* The right eye moves downwards
* Bone type: yRange specifies the maximum rotation angle based on the Euler angle (degree) of the leftEye / rightEye bone
* BlendShape type: yRange specifies the maximum applicable degree of LookLeft / LookRight BlendShape (up to 1.0)

```
Y = clamp(yaw, 0, verticalDown.xRange)/verticalDown.xRange * verticalDown.yRange 
```

##### Vertical Upward Direction

`extensions.VRMC_vrm.lookAt.verticalUp`

* The left eye moves upwards
* The right eye moves upwards
* Bone type: yRange specifies the maximum rotation angle based on the Euler angle (degree) of the leftEye / rightEye bone
* BlendShape type: yRange specifies the maximum applicable degree of LookLeft / LookRight BlendShape (up to 1.0)

```
Y = clamp(yaw, 0, verticalUp.xRange)/verticalUp.xRange * verticalUp.yRange 
```

##### Bone Type

The Yaw and Pitch values (after the movable range adjustment) output as Euler angle (degree) are applied to to the LocalRotation of the leftEye and rightEye bones, respectively.

##### BlendShape Type

The Yaw and Pitch values (after the movable range adjustment) output as BlendShape weight are applied to LookLeft, LookRight, LookDown, LookUp BlendShape, respectively.

#### LookAt Algorithm

* The eye gazes are applied to the Yaw/Pitch angle formed by the `HeadBone` (normally it's FirstPersonBone) forward vector and the vector from `HeadBone + firstPersonBoneOffset` to the eye gaze `LookAtObjectPoint`
* x, y = ClampAndScale(yaw, pitch)
* apply(x, y)

##### Bone type

| bone and yaw, pitch                | Note                                           |
|:-----------------------------------|:-----------------------------------------------|
| leftEye + yaw (left)               | Apply horizontalOuter to output as Euler angle |
| leftEye + yaw (right)              | Apply horizontalInner to output as Euler angle |
| rightEye + yaw (left)              | Apply horizontalOuter to output as Euler angle |
| rightEye + yaw (right)             | Apply horizontalInner to output as Euler angle |
| leftEye or rightEye + pitch (down) | Apply verticalDown to output as Euler angle    |
| leftEye or rightEye + pitch (up)   | Apply verticalUp to output as Euler angle      |

##### BlendShape type

| bone and yaw, pitch               | Note                                                          |
|:----------------------------------|:--------------------------------------------------------------|
| leftEye + yaw(left)               | Apply horizontalOuter to output as BlendShape LookLeft value  |
| leftEye + yaw(right)              | Apply horizontalInner to output as BlendShape LookRight value |
| rightEye + yaw(left)              | Apply horizontalOuter to output as BlendShape LookLeft value  |
| rightEye + yaw(right)             | Apply horizontalInner to output as BlendShape LookRight value |
| leftEye or rightEye + pitch(down) | Apply verticalDown to output as BlendShape LookDown value     |
| leftEye or rightEye + pitch(up)   | Apply verticalUp to output as BlendShape LookDown value       |

LookAt BlendShape has MorphTarget type and TextureUVOffset type. The processing here is the same regardless of which type is going to be used.

### SpringBone

The VRM defines the swaying object that does not rely on the physical engine.
It is assumed to be used for the appearance of swaying hair and clothes.

| Name           | Note               |
|:---------------|:-------------------|
| boneGroups     | SpringBone list    |
| colliderGroups | ColliderGroup list |

#### SpringBone

| Name           | Note                                                                         |
|:---------------|:-----------------------------------------------------------------------------|
| comment        | A Character string (any)                                                     |
| stiffness      | The force to return to the initial pose                                      |
| gravityPower   | Gravitational acceleration                                                   |
| gravityDir     | The direction of gravity. A gravity other than downward direction also works |
| dragForce      | Air resistance. Deceleration force                                           |
| center         | The index of the node to be the local coordinate                             |
| hitRadius      | The radius of the sphere used for the collision detection with colliders     |
| bones          | The node index array to set SpringBone                                       |
| colliderGroups | Process collision detection. colliderGroup index                             |

#### ColliderGroup

| Name      | Note                                  |
|:----------|:--------------------------------------|
| node      | The index of Node with collider setup |
| colliders | Collider list                         |

#### Collider

* For use by SpringBone only
* Only sphere shape is available

| Name   | Note                                      |
|:-------|:------------------------------------------|
| offset | The local position from the Collider Node |
| radius | Collider radius                           |

#### SpringBone Algorithm

TODO:

```cs
// Calculate the next position with Verlet Integration
var nextTail = currentTail
    + (currentTail - prevTail) * (1.0f - dragForce) // Continue moving from the previous frame (with attenuation)
    + ParentRotation * m_localRotation * m_boneAxis * stiffnessForce // Moving target of child bone by parent rotation
    + external // Translation by external force
    ;
```

### Material

Define Toon Shader

* require VRMC_materials_mtoon extension

UNLIT is required.

* require KHR_materials_unlit extension

Texture transform.
There are animation definitions by BlendShape.

* Require KHR_texture_transform extension

### Constraint

TODO:

### Update Order

The followings are the recommended update order:

1. Resolve Humanoid Bone
2. Since the head position has been determined, LookAt can be resolved
  * Bone type
  * BlendShape type
3. Update BlendShape
  * LipSync
  * AutoBlink
  * BlendShape type of LookAt
  * External inputs such as controller
4. Apply BlendShape
5. Resolve constraints
6. Resolve SpringBone

## glTF Schema Updates

### Naming Restrictions

* Make all names unique
* Use characters that can be used for the file name

#### Name Escape Example

```cs
static readonly char[] EscapeChars = new char[]
{
    '\\',
    '/',
    ':',
    '*',
    '?',
    '"',
    '<',
    '>',
    '|',
};
public static string EscapeFilePath(this string path)
{
    foreach(var x in EscapeChars)
    {
        path = path.Replace(x, '+');
    }
    return path;
}
```

### Mesh Storage Restrictions

#### TANGENT

* TANGENT information is not saved in the exported VRM model. If NormalMap exists, TANGENT can be obtained via MIKK method

#### MorphTarget

* Normals and tangents are not saved in MorphTarget

#### morphTarget Name

* Morph target names are saved in meshes[*].primitives[*].extras.targetNames

#### VertexBuffer

Given an input mesh, each primitive comprised by different buffers is prohibited

* each primitive must have the same attributes
* each primitive must have the same indices order

Each primitive is not allowed to have independent morph.
It is assumed that morph target is set for mesh as opposed to primitive.

* Each primitive must have the same target
* Each target must have the same attributes

##### Recommendation (shared buffer method)

A single VertexBuffer (accessor) is refereed by primitive.indices.

```
primitives
[0][1][2]
 +----------------------------------+
 |position, normal, uv...           |attributes
 +----------------------------------+
 +----------------------------------+
 |                                  |targets[0]
 +----------------------------------+
 +----------------------------------+
 |                                  |targets[1]
 +----------------------------------+
      ^         ^         ^
      |         |         |
primitives
  [0]         [1]         [2]
 +-----------+-----------+-----------+
 |indices    |indices    |indices    |
 +-----------+-----------+-----------+
```

* Since there is only one VertexBuffer, we can force all primitives to have the same attributes
* Same as primitive.targets

##### GLTF Standard (divided buffer method)

```
primitives
  [0]         [1]         [2]
 +-----------+-----------+----------+
 |           |           |          |attributes (0, 1, 2 have the same attribute)
 +-----------+-----------+----------+
 +-----------+-----------+----------+
 |           |           |          |targets[0] (0, 1, 2 have the same attribute)
 +-----------+-----------+----------+
 +-----------+-----------+----------+
 |           |           |          |targets[1] (0, 1, 2 have the same attribute)
 +-----------+-----------+----------+
      ^         ^         ^
      |         |         |
 +-----------+-----------+-----------+
 |           |           |           |indices
 +-----------+-----------+-----------+
```

* Make all the primitives have the same attributes
* Same as primitive.targets

## JSON Schema

```js
{
  "extensionsUsed": {
    "VRMC_vrm"
  },
  "extensions": {
    "VRMC_vrm": {
      // The information of VRM extension
      "specVersion": "1.0"
    }
  },
  // The information of general GLTF-2.0
}
```

* https://github.com/vrm-c/UniVRM/tree/master/specification/1.0/schema

GLTF-2.0 JsonSchema

* https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/schema

## Known Implementations

### VRM-0.0

* https://github.com/vrm-c/UniVRM
* https://github.com/virtual-cast/babylon-vrm-loader
* https://github.com/pixiv/three-vrm
* https://github.com/iCyP/VRM_IMPORTER_for_Blender2_8

## Resources

* https://vrm.dev/
