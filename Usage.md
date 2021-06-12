# Usage

Small little tutorial on export and parse files

- Create your File Provider

```csharp
string PaksLocation = @"C:\Program Files\Epic Games\Fortnite\FortniteGame\Content\Paks";//Default Paks Location, can be found in C:\ProgramData\Epic\UnrealEngineLauncher\LauncherInstalled.dat
var Provider = new DefaultFileProvider(PaksLocation, SearchOption.TopDirectoryOnly);
Provider.Initialize();
```
- Define your AES Key (Can be retrieved from the `mainKey` in the endpoint [here](https://benbot.app/api/v1/aes))
```csharp
string aes = "0x840A3C61B7BA7FDC58EAB092AC9F29D23229DA63C417F2F0ADD69F30F1B6980D";
```

- ###### If you want to retrive assets from an Encrypted pak file with a different key you have to parse it through the loop.

```csharp
IReadOnlyCollection<IAesVfsReader> vfs = Provider.UnloadedVfs;//Get Information of the files provided.
foreach (IAesVfsReader file in vfs)//Make a loop foreach file
{
//Make a loop here specifying file.Name if you want to also support encrypted files
Provider.SubmitKey(file.EncryptionKeyGuid, new FAesKey(aes));//Submit the GUID of the file & using main key by default.
}
Provider.LoadLocalization(ELanguage.English);//It's english by default but if you want to change later
```
<details>
<summary>Exporting/Saving Assets</summary>

First define a string of the asset you want to export.
```csharp
string Asset = "FortniteGame/Content/Athena/Items/Cosmetics/Characters/CID_001_Athena_Commando_F_Default";// '/Game/Athena/Items/Cosmetics/Characters/CID_001_Athena_Commando_F_Default' is also works
```


- Exporting JSON
```csharp
//Export asset as JSON
UExport export = Provider.LoadObject(Asset);//Load the asset and make it a UExport object
string json = JsonConvert.SerializeObject(export, Formatting.Indented);//Use Newtonsoft.Json and Serialize the object as json
```

- Saving Package

```csharp
if (Provider.TrySaveAsset(Asset, out byte[] asset))//Trying to save the asset, if successful will continue if statement
            {
                File.WriteAllBytes(Path.GetFileName(Asset) + ".uasset", asset);//Write out the `asset` byte array
    // Also just using the Filename of the current asset and adding .uasset to it
            }

```
  </details>
