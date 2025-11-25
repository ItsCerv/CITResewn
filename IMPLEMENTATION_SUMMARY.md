# Implementation Summary: CustomModelData Fix for CIT Resewn 1.21+

## Quick Reference

This document provides a quick summary of what needs to be done to implement the CustomModelData fix.

## The Problem

CIT Resewn 1.21+ throws this error when encountering `nbt.CustomModelData`:
```
NBT condition is not supported since 1.21
```

This breaks existing CIT resource packs that use CustomModelData for custom armor.

## The Solution

Add 3 lines of code to `ConditionComponents.java` to enable legacy support for `nbt.CustomModelData`.

## File to Edit

**Path:** `defaults/src/main/java/shcm/shsupercm/fabric/citresewn/defaults/cit/conditions/ConditionComponents.java`

**Lines:** 33-41

## Code Change

### BEFORE (Lines 33-41):
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

### AFTER (Lines 33-44):
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

## What This Does

1. **Detects** when a properties file uses `nbt.CustomModelData`
2. **Converts** it to the new component format `minecraft:custom_model_data`
3. **Logs a warning** to encourage users to update their packs
4. **Allows matching** to proceed using the existing component matching logic

## Testing

After making the change:

1. **Build the mod:**
   ```bash
   ./gradlew build
   ```

2. **Test with this command:**
   ```
   /give @s netherite_helmet[custom_model_data=17]
   ```

3. **Create a test properties file:**
   ```properties
   type=armor
   items=netherite_helmet
   texture.netherite_layer_1=test_layer_1
   texture.netherite_layer_2=test_layer_2
   nbt.CustomModelData=17
   ```

4. **Expected result:** The helmet displays your custom texture when worn.

## Why This Works

The existing code already has a fallback mechanism that:
1. Retrieves the component from the item
2. Serializes it to NBT format
3. Uses the existing `ConditionNBT` matching logic

By adding `CustomModelData` to the legacy conversion list, we're simply telling the code to look for the `minecraft:custom_model_data` component instead of throwing an error.

## Next Steps

1. ✅ Fork the repository (DONE)
2. ✅ Clone the repository (DONE)
3. ✅ Create documentation (DONE)
4. ⏳ Make the code change
5. ⏳ Build and test
6. ⏳ Commit and push
7. ⏳ Create a release

## Resources

- **Technical Spec:** [CUSTOM_MODEL_DATA_FIX_SPECIFICATION.md](CUSTOM_MODEL_DATA_FIX_SPECIFICATION.md)
- **Dev Guide:** [DEVELOPMENT_GUIDE.md](DEVELOPMENT_GUIDE.md)
- **Testing Plan:** [TESTING_PLAN.md](TESTING_PLAN.md)
- **Fork README:** [FORK_README.md](FORK_README.md)

## Estimated Time

- **Code change:** 5 minutes
- **Build:** 2-5 minutes
- **Testing:** 15-30 minutes
- **Total:** ~30-40 minutes

## Support

If you need help:
1. Read the [DEVELOPMENT_GUIDE.md](DEVELOPMENT_GUIDE.md)
2. Check the [TESTING_PLAN.md](TESTING_PLAN.md)
3. Review the [CUSTOM_MODEL_DATA_FIX_SPECIFICATION.md](CUSTOM_MODEL_DATA_FIX_SPECIFICATION.md)
4. Open an issue on GitHub

---

**Good luck with the implementation!** 🚀


