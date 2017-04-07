---
layout: post
title:  "Context for Storing/Accessing Data"
categories: games unity
---
## Why?
- It's easy to have automatic data loading from resource files.
- It's easy to override data from remote.
- The above point also goes with ab tests. You ab tests can partially or completely override the data for specific users.
- You can easily dump your context to log for debugging purposes and see the data easily.
- You can write a custom editor script to view the context and/or allow modification if you are into using the Unity Editor that way.

## API
A simple API would look like:

```csharp
// This is just a plain data class
Player player;

// To put data inside context:
context.Put(Constants.PLAYER, player);

// To get it:
Player player = (Player)context.Get(Constants.PLAYER);

// Or if you want automatic type casting, maybe you can
// add a generic method that does it for you, like
// GetComponent<T>();
Player player = context.Get<Player>(Constants.PLAYER);

// The one we ended up using allows subcategories
// inside the context. It helps group things and
// avoids key clashes.
// In addition to a key, we also have a context name
// parameter that you pass.
context.Put(Constants.Context.CONFIG, Constants.PLAYER, player);
context.Get(Constants.Context.CONFIG, Constants.PLAYER);
context.Get(Constants.Context.GAME, Constants.SOMETHING);
```

## Examples

### What's in there?
What I end up storing in context is usually:
- Config
- Player
- SaveData

### Initialization
```csharp
// A simple initialization could look like:
context = new Context();

// Load default data from a JSON file. This can be anything really. You can for example export designer excels sheets to JSON and put them into the project.
LoadDefaultData(context);

// Then you can override the defaults with data you got from a remote server.
OverrideWithRemoteData(context);

// Override ab test data
OverrideWithAbTest(context);

// Load save data
OverrideWithSaveData(context);
```
### Defaults
```csharp
// - Resources/Data
//   - player.json
//   - game.json

public void LoadDefaultData(Context context)
{
  TextAsset[] dataAssets = Resources.LoadAll<TextAsset>("Data");

  foreach (var asset in dataAssets)
  {
    object dataObj = ParseFromJson(asset.text);
    context.put(asset.name.ToLower(), dataObj);
  }  
}

// player.json = "player" -> Player
// game.json = "game" -> Game
(Player)context.Get("player");
(Game)context.Get("game");
```
### Ab Test
An example json file could look like:

```json
{
  "key": "game",
  "data": {
    "enable_shield": true,
    "show_x_popup": false
  }
}
```

You would use key as the context key, and than use Unity's override with JSON method to override the data inside the context.

```csharp
string key = "game"; // Read from file
string dataJson = "{\"enable_shield\": true}" // Read from file

object dataObj = context.Get(key);
JsonUtility.FromJsonOverwrite(dataJson, dataObj);
```

Or you can also return a remote link to a resource file from you server when an ab test is required. You'd then download the resource file in the client and use that as data JSON.

```json
{
  "key": "game",
  "data_link": "http://somewhere.com/game.json"
}
```

### Remote Configuration
An example json file could look like:

```json
{
  "key": "player",
  "data": {
    "gold": 100,
    "diamond": 10
  }
}
```

You would override the data as we did in the ab test example.
```csharp
string key = "player"; // Read from file
string dataJson = "{\"gold\": 100}" // Read from file

object dataObj = context.Get(key);
JsonUtility.FromJsonOverwrite(dataJson, dataObj);
```
### Save
You can for example tag your classes with attribute or register them somewhere as persisted to automatically persist them to disk.

```csharp
// You can get these either by hardcoding, from attributes or by registering them somewhere.
string[] persistedDataKeys = ["player"];

public void Save()
{
  foreach (var key in persistedDataKeys)
  {
    object dataObj = context.Get(key);
    string json = SerializeIntoJSON(dataObj);
    WriteToDisk(json);
  }
}
```

You can load the same way as ab test or remote config, by overriding the default data with a json.
