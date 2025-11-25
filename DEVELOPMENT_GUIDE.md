# CIT Resewn Development Guide: CustomModelData Fix

## Quick Start

This guide will help you set up a development environment and implement the CustomModelData fix for CIT Resewn 1.21+.

## Prerequisites

- **Java Development Kit (JDK) 21 or higher**
- **Git**
- **IntelliJ IDEA** (recommended) or **Eclipse**
- **Minecraft 1.21.1** with **Fabric Loader**

## Environment Setup

### 1. Clone the Repository

```bash
git clone https://github.com/ItsCerv/CITResewn.git
cd CITResewn
```

### 2. Import into IntelliJ IDEA

1. Open IntelliJ IDEA
2. File → Open
3. Select the `build.gradle` file in the CITResewn directory
4. Click "Open as Project"
5. Wait for Gradle to sync

### 3. Build the Project

```bash
./gradlew build
```

If successful, you'll see `BUILD SUCCESSFUL` in the output.

### 4. Run Minecraft with the Mod

```bash
./gradlew runClient
```

This will launch Minecraft with your development version of CIT Resewn loaded.

## Implementation Steps

### Step 1: Locate the File to Edit

Navigate to:
```
defaults/src/main/java/shcm/shsupercm/fabric/citresewn/defaults/cit/conditions/ConditionComponents.java
```

### Step 2: Add CustomModelData Legacy Support

Find lines 33-41 (the `if (key.path().equals("nbt"))` block) and modify it:

**BEFORE:**
```java
if (key.path().equals("nbt")) {
    if (metadata.startsWith("display.Name")) {
        metadata = "minecraft:custom_name" + value.keyMetadata().substring("display.Name".length());
        CITResewn.logWarnLoading(properties.messageWithDescriptorOf("Using legacy nbt.display.Name", value.position()));
    } else if (metadata.startsWith("display.Lore")) {
        metadata = "minecraft:lore" + value.keyMetadata().substring("display.Lore".length());
        CITResewn.logWarnLoading(properties.messageWithDescriptorOf("Using legacy nbt.display.Lore", value.position()));
    } else
        throw new CITParsingException("NBT condition is not supported since 1.21", properties, value.position());
}
```

**AFTER:**
```java
if (key.path().equals("nbt")) {
    if (metadata.startsWith("display.Name")) {
        metadata = "minecraft:custom_name" + value.keyMetadata().substring("display.Name".length());
        CITResewn.logWarnLoading(properties.messageWithDescriptorOf("Using legacy nbt.display.Name", value.position()));
    } else if (metadata.startsWith("display.Lore")) {
        metadata = "minecraft:lore" + value.keyMetadata().substring("display.Lore".length());
        CITResewn.logWarnLoading(properties.messageWithDescriptorOf("Using legacy nbt.display.Lore", value.position()));
    } else if (metadata.equals("CustomModelData")) {
        metadata = "minecraft:custom_model_data";
        CITResewn.logWarnLoading(properties.messageWithDescriptorOf("Using legacy nbt.CustomModelData", value.position()));
    } else
        throw new CITParsingException("NBT condition is not supported since 1.21", properties, value.position());
}
```

### Step 3: Rebuild and Test

1. **Rebuild the mod:**
   ```bash
   ./gradlew build
   ```

2. **Run Minecraft:**
   ```bash
   ./gradlew runClient
   ```

3. **Test with a resource pack:**
   - Create a test resource pack with CIT armor using `nbt.CustomModelData`
   - Load it in Minecraft
   - Give yourself armor with CustomModelData: `/give @s netherite_helmet[custom_model_data=17]`
   - Verify the custom texture appears

## Testing

### Test Case 1: Basic Integer Matching

**Create a test resource pack:**

1. **Directory structure:**
   ```
   test_pack/
   ├── pack.mcmeta
   └── assets/
       └── minecraft/
           └── optifine/
               └── cit/
                   └── armor/
                       ├── test_helmet.properties
                       ├── test_helmet_layer_1.png
                       └── test_helmet_layer_2.png
   ```

2. **test_helmet.properties:**
   ```properties
   type=armor
   items=netherite_helmet
   texture.netherite_layer_1=test_helmet_layer_1
   texture.netherite_layer_2=test_helmet_layer_2
   nbt.CustomModelData=17
   ```

3. **In-game test:**
   ```
   /give @s netherite_helmet[custom_model_data=17]
   ```

**Expected Result:** The helmet should display your custom texture when worn.

### Test Case 2: Multiple Values

Test with different CustomModelData values (1, 10, 100, 999) to ensure matching works correctly.

### Test Case 3: Backward Compatibility

Ensure existing CIT packs using `nbt.display.Name` still work after your changes.

## Debugging

### Enable Debug Logging

1. In IntelliJ IDEA, set breakpoints in `ConditionComponents.java`:
   - Line 33 (start of NBT check)
   - Line 68 (start of test method)

2. Run → Debug 'Minecraft Client'

3. Load your test resource pack

4. Give yourself armor with CustomModelData

5. Step through the code to verify:
   - The legacy conversion happens correctly
   - The component is retrieved
   - The matching logic works

### Common Issues

**Issue:** "NBT condition is not supported since 1.21" error still appears

**Solution:** Make sure you added the `else if (metadata.equals("CustomModelData"))` block BEFORE the final `else` that throws the exception.

---

**Issue:** Armor doesn't display custom texture

**Solution:** 
1. Check the logs for warnings about the CIT file
2. Verify the texture files exist and are named correctly
3. Ensure the CustomModelData value matches between the properties file and the give command

---

**Issue:** Build fails

**Solution:**
1. Make sure you're using JDK 21+
2. Run `./gradlew clean build`
3. Check for syntax errors in your code changes

## Next Steps

After successfully implementing and testing:

1. **Commit your changes:**
   ```bash
   git add .
   git commit -m "Add legacy support for nbt.CustomModelData in 1.21+"
   ```

2. **Push to your fork:**
   ```bash
   git push origin master
   ```

3. **Create a pull request** (optional):
   - Go to https://github.com/ItsCerv/CITResewn
   - Click "Pull Request"
   - Describe your changes
   - Submit to the original repository (if you want to contribute back)

4. **Build a release:**
   ```bash
   ./gradlew build
   ```
   The mod JAR will be in `build/libs/`

## Resources

- **CIT Resewn Docs:** https://citresewn.shcm.io
- **Fabric Wiki:** https://fabricmc.net/wiki/
- **Minecraft Wiki - Components:** https://minecraft.wiki/w/Data_component_format
- **Java Documentation:** https://docs.oracle.com/en/java/


