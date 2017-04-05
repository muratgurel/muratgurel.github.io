---
layout: post
title:  "GameObject Querying"
categories: unity
---
## Goal
To separate view and implementation details.

## Why?
The layout of a GameObject or its children are subject to a lot of change during development. The system should be flexible enough to accommodate changes to layouts. It shouldn't break the project.

## Example
Let's say you have a GameObject without any children. The collider and the mesh renderer are attached to this GameObject. Later in development, you realize that you need to separate them into different GameObjects. That might happen if you want to manipulate them differently, maybe its scale, rotation or position, or whatever. Removing them from the root and placing them to different GameObject as the original one's child will break the code if the code uses GetComponent to access those components. Now the code needs to use either GetComponentInChildren or get the reference from a custom script which has the reference dragged to the field from the editor. Instead, I propose a generic way to query the components with ids and not worry about the layout or creating custom scripts all the time.

## Advantages
- Separate view and implementation details.
- Easier iteration on views.
- Easily replaceable prefabs (for example, push new content from remote which has a slightly different layout).
- Less clutter by reducing script count.

## API
```csharp
gameObject.GetComponent<Collider2D>("collider");
gameObject.GetComponent<Collider2D>("rightArtilleryCollider");
gameObject.GetComponent<MeshRenderer>("model");
gameObject.GetComponent<SpriteRenderer>("face");
gameObject.GetComponent<SpriteRenderer>("mouth");
```

## How?
Add the script "Queryable" to the root GameObject and set the references with their ids.
