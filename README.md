<h1 align="center">Rtag</h1>

<h4 align="center">The "readable tag" library, an easy way to handle NBTTagCompounds.</h4>

<p align="center">
    <a href="https://www.codefactor.io/repository/github/saicone/rtag">
        <img src="https://www.codefactor.io/repository/github/saicone/rtag/badge?style=flat-square"/>
    </a>
    <a href="https://github.com/saicone/rtag">
        <img src="https://img.shields.io/github/languages/code-size/saicone/rtag?style=flat-square"/>
    </a>
    <a href="https://github.com/saicone/rtag">
        <img src="https://img.shields.io/tokei/lines/github/saicone/rtag?style=flat-square"/>
    </a>
    <a href="https://jitpack.io/#com.saicone/rtag">
        <img src="https://jitpack.io/v/com.saicone/rtag.svg?style=flat-square"/>
    </a>
</p>

Rtag convert NBT tags to known objects and viceversa for better readability.

```java
RtagItem itemTag = new RtagItem(item);

// Set values
itemTag.set("CustomValue", "deep", "path");
itemTag.set(40, "somekey");
itemTag.set("Item name!", "display", "name");
itemTag.set(new ArrayList(), "list", "path");
// Add value to list
itemTag.add((short) 3, "list", "path");

// Get values
String value = itemTag.get("deep", "path");
// Safe get
int intValue = itemTag.getOptional("somekey").or(-1);
String name = itemTag.get("display", "name");
List<Short> list = itemTag.get("list", "path");
// List value at index 0
short listValue = itemTag.getOptional("list", "path", 0).or((short) 0);

// Remove values
itemTag.remove("deep", "path");
String newValue = itemTag.get("deep", "path"); // return null

// Get item copy with changes
ItemStack newItem = itemTag.loadCopy();
// Or load changes into original item
itemTag.load();
```

## Get Rtag

### Requirements
*  **At least Minecraft 1.8.8:** Rtag is made to be used in latest Minecraft versions, old versions support is only for commercial purposes.
*  Minimum Java 11

### Project build
For Gradle Groovy project (build.gradle)
```groovy
repositories {
    maven { url 'https://jitpack.io' }
}

dependencies {
    compileOnly 'com.saicone.rtag:rtag:VERSION'
    // Other modules
    compileOnly 'com.saicone.rtag:rtag-block:VERSION'
    compileOnly 'com.saicone.rtag:rtag-entity:VERSION'
    compileOnly 'com.saicone.rtag:rtag-item:VERSION'
}
```

<details>
  <summary>For Gradle Kotlin project (build.gradle.kts)</summary>
  
  ```kotlin
  repositories {
      maven("https://jitpack.io")
  }

  dependencies {
      compileOnly("com.saicone.rtag:rtag:VERSION")
      // Other modules
      compileOnly("com.saicone.rtag:rtag-block:VERSION")
      compileOnly("com.saicone.rtag:rtag-entity:VERSION")
      compileOnly("com.saicone.rtag:rtag-item:VERSION")
  }
  ```
</details>

<details>
  <summary>For Maven project (pom.xml)</summary>
  
  ```xml
  <repositories>
      <repository>
          <id>Jitpack</id>
          <url>https://jitpack.io</url>
      </repository>
  </repositories>
    
  <dependencies>
      <dependency>
          <groupId>com.saicone.rtag</groupId>
          <artifactId>rtag</artifactId>
          <version>VERSION</version>
          <scope>provided</scope>
      </dependency>
      <!-- Other modules -->
      <dependency>
          <groupId>com.saicone.rtag</groupId>
          <artifactId>rtag-block</artifactId>
          <version>VERSION</version>
          <scope>provided</scope>
      </dependency>
      <dependency>
          <groupId>com.saicone.rtag</groupId>
          <artifactId>rtag-entity</artifactId>
          <version>VERSION</version>
          <scope>provided</scope>
      </dependency>
      <dependency>
          <groupId>com.saicone.rtag</groupId>
          <artifactId>rtag-item</artifactId>
          <version>VERSION</version>
          <scope>provided</scope>
      </dependency>
  </dependencies>
  ```
</details>


# Why Rtag
There are other libraries to edit NBT tags, why should Rtag be used over the others?

## Really fast
Rtag abuses of static final MethodHandles to convert the use of reflected methods as if they were direct calls, so it works to edit NBT tags in non-async operations without producing a bad performance impact on big servers.

## Easy to understand
You don't need to be an expert with NBT tags, just with simple methods you can set and get normal Java objects.
```java
Rtag rtag = new Rtag();
rtag.set(compound, "Normal string", "CustomTagPath");
String string = rtag.get(compound, "CustomTagPath");
```

## Store custom objects
You can register (de)serializers in Rtag instance to set and get custom objects with automatic conversion.
```java
public class MyObjectSerializer implements RtagSerializer<MyObject>, RtagDeserializer<MyObject> {
    
    public MyObjectSerializer(Rtag rtag) {
        rtag.putSerializer(MyObject.class, this);
        rtag.putDeserializer(this);
    }
    
    // MyObject -> Map
    @Override
    public String getInID() {
        // It's suggested to use a unique namespaced key
        return "myplugin:MyObject";
    }
    
    // Map -> MyObject
    @Override
    public String getOutID() {
        return "myplugin:MyObject";
    }

    @Override
    public Map<String, Object> serialize(MyObject object) {
        // Convert your custom object into map
    }
    
    @Override
    public MyObject deserialize(Map<String, Object> compound) {
        // Convert compound into you custom object
    }
}
```

## TagStream instances

### ItemTagStream
With ItemTagStream instance you can convert items into Base64|File|Bytes and viceversa.

Including **cross-version support**! Save an item on any version and get on any version without compatibility problems. Materials, enchantments, potions... etc, all will be converted!
```java
ItemTagStream tag = ItemTagStream.INSTANCE;

String string = tag.toBase64(item);
ItemStack sameItem = tag.fromBase64(string)[0];
```

## Additional utilities

### Textured heads
With SkullTexture class you can get textured heads from base64, url, texture ID, player name or uuid.
```java
// Base64
ItemStack head = SkullTexture.getTexturedHead("eyJ0ZXh0dXJlcyI6eyJTS0lOIjp7InVybCI6Imh0dHA6Ly90ZXh0dXJlcy5taW5lY3JhZnQubmV0L3RleHR1cmUvZmVkZmEyZTBmZGVhMGMwNDIzODA0Y2RiNWI2MmFkMDVhNmU5MTRjMDQ2YzRhM2I3ZTM1NWJmODEyNjkxMjVmZCJ9fQ==");
// URL
ItemStack head = SkullTexture.getTexturedHead("http://textures.minecraft.net/texture/fedfa2e0fdea0c0423804cdb5b62ad05a6e914c046c4a3b7e355bf81269125fd");
// Texture ID
ItemStack head = SkullTexture.getTexturedHead("fedfa2e0fdea0c0423804cdb5b62ad05a6e914c046c4a3b7e355bf81269125fd");
// Player name
ItemStack head = SkullTexture.getTexturedHead("Rubenicos");
// Player UUID
ItemStack head = SkullTexture.getTexturedHead("7ca003dc-175f-4f1f-b490-5651045311ad");
```

### Chat Component
With ChatComponent class you can convert (json) strings into chat components!
```java
Object component = ChatComponent.fromJson("{\"bold\":true,\"italic\":false,\"underlined\":false,\"strikethrough\":false,\"obfuscated\":false,\"color\":\"dark_purple\",\"text\":\"Colored text!\"}");

Object otherComponent = ChatComponent.fromString("§5§lColored text!");
```
