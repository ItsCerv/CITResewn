# Technical Specification: Restoring CustomModelData Support in CIT Resewn 1.21+

## Executive Summary

This document outlines the technical changes required to restore `nbt.CustomModelData` matching functionality in CIT Resewn for Minecraft 1.21+. The feature was intentionally removed when Minecraft switched from NBT tags to the component system in version 1.20.5+.

## Problem Statement

### What Broke
In Minecraft 1.20.5+, the item data system fundamentally changed:
- **Old System (pre-1.20.5):** Items used NBT tags like `{CustomModelData:17}`
- **New System (1.20.5+):** Items use components like `[custom_model_data=17]`

### Current Behavior
CIT Resewn 1.21+ throws an error when encountering `nbt.CustomModelData` in properties files:
```
NBT condition is not supported since 1.21
```

Only `nbt.display.Name` and `nbt.display.Lore` have legacy support (converted to `minecraft:custom_name` and `minecraft:lore` components).

## Technical Analysis

### Key Files Involved

1. **ConditionComponents.java** (`defaults/src/main/java/.../conditions/ConditionComponents.java`)
   - Handles component-based matching in 1.21+
   - Currently only supports `display.Name` and `display.Lore` legacy conversions
   - Line 41: Throws exception for all other NBT conditions

2. **ConditionNBT.java** (`defaults/src/main/java/.../conditions/ConditionNBT.java`)
   - Original NBT matching implementation (disabled in 1.21+)
   - Line 96: Throws AssertionError in 1.21+
   - Contains all the matching logic that needs to be adapted

### How Components Work in Minecraft 1.21+

According to the Minecraft Wiki, `custom_model_data` is now a component:

```json
{
  "components": {
    "minecraft:custom_model_data": {
      "floats": [4.0, 5.6, 99.1],
      "flags": [true, false],
      "strings": ["foo:bar"],
      "colors": [8323327]
    }
  }
}
```

However, for simple integer values (the most common use case), it can be simplified to just an integer.

### Component Registry

In Minecraft's code, `custom_model_data` is registered as:
- **Component ID:** `minecraft:custom_model_data`
- **Type:** `DataComponentType<CustomModelData>`
- **Codec:** Handles serialization/deserialization

## Proposed Solution

### Approach 1: Add Legacy Support for CustomModelData (Recommended)

Modify `ConditionComponents.java` to add legacy support for `nbt.CustomModelData`, similar to how `display.Name` and `display.Lore` are handled.

**Changes Required:**

1. **In ConditionComponents.java, line 33-41:**

```java
if (key.path().equals("nbt")) {
    if (metadata.startsWith("display.Name")) {
        metadata = "minecraft:custom_name" + value.keyMetadata().substring("display.Name".length());
        CITResewn.logWarnLoading(properties.messageWithDescriptorOf("Using legacy nbt.display.Name", value.position()));
    } else if (metadata.startsWith("display.Lore")) {
        metadata = "minecraft:lore" + value.keyMetadata().substring("display.Lore".length());
        CITResewn.logWarnLoading(properties.messageWithDescriptorOf("Using legacy nbt.display.Lore", value.position()));
    } else if (metadata.equals("CustomModelData")) {
        // NEW CODE: Add legacy support for CustomModelData
        metadata = "minecraft:custom_model_data";
        CITResewn.logWarnLoading(properties.messageWithDescriptorOf("Using legacy nbt.CustomModelData", value.position()));
    } else
        throw new CITParsingException("NBT condition is not supported since 1.21", properties, value.position());
}
```

2. **Handle Integer Matching:**

The `custom_model_data` component can be complex (with floats, flags, strings, colors), but for armor matching, we only need to match the integer value.

In the `test()` method (line 68-84), add special handling for `custom_model_data`:

```java
@Override
public boolean test(CITContext context) {
    Object stackComponent = context.stack.getComponents().get(this.componentType);
    if (stackComponent != null) {
        // Special handling for custom_model_data
        if (this.componentType == DataComponentTypes.CUSTOM_MODEL_DATA) {
            // CustomModelData is stored as a CustomModelData object
            // We need to extract the integer value and compare it
            // The fallbackNBTCheck will handle the integer comparison
            NbtElement componentNBT = ((ComponentType<Object>) this.componentType).getCodec()
                .encodeStart(context.world.getRegistryManager().getOps(NbtOps.INSTANCE), stackComponent)
                .getOrThrow();
            return this.fallbackNBTCheck.testPath(componentNBT, 0, context);
        }
        
        // Existing code for Text components
        if (stackComponent instanceof Text text) {
            if (this.fallbackNBTCheck.testString(null, text, context))
                return true;
        }
        
        // Fallback to NBT-based check
        NbtElement fallbackComponentNBT = ((ComponentType<Object>) this.componentType).getCodec()
            .encodeStart(context.world.getRegistryManager().getOps(NbtOps.INSTANCE), stackComponent)
            .getOrThrow();
        return this.fallbackNBTCheck.testPath(fallbackComponentNBT, 0, context);
    }
    return false;
}
```


