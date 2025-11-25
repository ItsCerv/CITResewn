# Testing Plan: CustomModelData Fix for CIT Resewn 1.21+

## Overview

This document outlines the comprehensive testing plan for the CustomModelData fix in CIT Resewn 1.21+.

## Test Environment

### Required Software
- **Minecraft:** 1.21.1
- **Fabric Loader:** Latest compatible version
- **Fabric API:** Latest compatible version
- **CIT Resewn:** Development build with CustomModelData fix

### Test Resource Pack

Create a test resource pack with the following structure:

```
test_cit_pack/
├── pack.mcmeta
└── assets/
    └── minecraft/
        ├── optifine/
        │   └── cit/
        │       └── armor/
        │           ├── nexus_armor.properties
        │           ├── nexus_armor_layer_1.png
        │           ├── nexus_armor_layer_2.png
        │           ├── spartan_armor.properties
        │           ├── spartan_armor_layer_1.png
        │           ├── spartan_armor_layer_2.png
        │           ├── spectral_armor.properties
        │           ├── spectral_armor_layer_1.png
        │           └── spectral_armor_layer_2.png
        └── textures/
            └── (texture files)
```

## Test Cases

### Test 1: Basic CustomModelData Matching

**Objective:** Verify that `nbt.CustomModelData` correctly matches armor with the specified value.

**Properties File (nexus_armor.properties):**
```properties
type=armor
items=netherite_helmet netherite_chestplate netherite_leggings netherite_boots
texture.netherite_layer_1=nexus_armor_layer_1
texture.netherite_layer_2=nexus_armor_layer_2
nbt.CustomModelData=17
```

**Test Steps:**
1. Load the test resource pack
2. Give yourself armor: `/give @s netherite_helmet[custom_model_data=17]`
3. Equip the helmet
4. Verify the custom texture appears

**Expected Result:** ✅ Custom Nexus armor texture displays

**Actual Result:** _[To be filled during testing]_

---

### Test 2: Multiple CustomModelData Values

**Objective:** Verify that different CustomModelData values match different armor sets.

**Properties Files:**
- `nexus_armor.properties` → `nbt.CustomModelData=17`
- `spartan_armor.properties` → `nbt.CustomModelData=18`
- `spectral_armor.properties` → `nbt.CustomModelData=19`

**Test Steps:**
1. Give yourself three helmets with different CustomModelData values:
   - `/give @s netherite_helmet[custom_model_data=17,custom_name='{"text":"Nexus Helmet"}']`
   - `/give @s netherite_helmet[custom_model_data=18,custom_name='{"text":"Spartan Helmet"}']`
   - `/give @s netherite_helmet[custom_model_data=19,custom_name='{"text":"Spectral Helmet"}']`
2. Equip each helmet one at a time
3. Verify each displays the correct custom texture

**Expected Result:** ✅ Each helmet displays its unique texture

**Actual Result:** _[To be filled during testing]_

---

### Test 3: All Armor Slots

**Objective:** Verify CustomModelData matching works for all armor slots.

**Test Steps:**
1. Give yourself a full set of Nexus armor:
   ```
   /give @s netherite_helmet[custom_model_data=17]
   /give @s netherite_chestplate[custom_model_data=17]
   /give @s netherite_leggings[custom_model_data=17]
   /give @s netherite_boots[custom_model_data=17]
   ```
2. Equip the full set
3. Verify all pieces display custom textures

**Expected Result:** ✅ All armor pieces display custom textures

**Actual Result:** _[To be filled during testing]_

---

### Test 4: Range Matching

**Objective:** Verify that CustomModelData ranges work (if supported by ConditionNBT).

**Properties File:**
```properties
type=armor
items=netherite_helmet
texture.netherite_layer_1=nexus_armor_layer_1
texture.netherite_layer_2=nexus_armor_layer_2
nbt.CustomModelData=17-20
```

**Test Steps:**
1. Give yourself helmets with CustomModelData 16, 17, 18, 19, 20, 21
2. Equip each helmet
3. Verify only 17-20 display custom textures

**Expected Result:** ✅ Only CustomModelData 17-20 match

**Actual Result:** _[To be filled during testing]_

---

### Test 5: Hex Value Matching

**Objective:** Verify that hex CustomModelData values work.

**Properties File:**
```properties
type=armor
items=netherite_helmet
texture.netherite_layer_1=nexus_armor_layer_1
texture.netherite_layer_2=nexus_armor_layer_2
nbt.CustomModelData=0x11
```

**Test Steps:**
1. Give yourself helmet: `/give @s netherite_helmet[custom_model_data=17]` (0x11 = 17 in decimal)
2. Equip the helmet
3. Verify custom texture appears

**Expected Result:** ✅ Hex value 0x11 matches decimal 17

**Actual Result:** _[To be filled during testing]_

---

### Test 6: Backward Compatibility - display.Name

**Objective:** Ensure existing CIT packs using `nbt.display.Name` still work.

**Properties File:**
```properties
type=armor
items=netherite_helmet
texture.netherite_layer_1=named_armor_layer_1
texture.netherite_layer_2=named_armor_layer_2
nbt.display.Name=ipattern:*Nexus*
```

**Test Steps:**
1. Give yourself helmet: `/give @s netherite_helmet[custom_name='{"text":"Nexus Helmet"}']`
2. Equip the helmet
3. Verify custom texture appears

**Expected Result:** ✅ display.Name matching still works

**Actual Result:** _[To be filled during testing]_

---

### Test 7: Backward Compatibility - display.Lore

**Objective:** Ensure existing CIT packs using `nbt.display.Lore` still work.

**Properties File:**
```properties
type=armor
items=netherite_helmet
texture.netherite_layer_1=lore_armor_layer_1
texture.netherite_layer_2=lore_armor_layer_2
nbt.display.Lore.0=ipattern:*Legendary*
```

**Test Steps:**
1. Give yourself helmet: `/give @s netherite_helmet[lore=['{"text":"Legendary Armor"}']]`
2. Equip the helmet
3. Verify custom texture appears

**Expected Result:** ✅ display.Lore matching still works

**Actual Result:** _[To be filled during testing]_

---

### Test 8: Error Handling - Invalid CustomModelData

**Objective:** Verify proper error messages for invalid CustomModelData values.

**Properties File:**
```properties
type=armor
items=netherite_helmet
texture.netherite_layer_1=test_layer_1
texture.netherite_layer_2=test_layer_2
nbt.CustomModelData=invalid
```

**Test Steps:**
1. Load the resource pack
2. Check the game logs

**Expected Result:** ✅ Clear error message about invalid CustomModelData value

**Actual Result:** _[To be filled during testing]_

---

### Test 9: No CustomModelData

**Objective:** Verify that armor without CustomModelData doesn't match.

**Properties File:**
```properties
type=armor
items=netherite_helmet
texture.netherite_layer_1=nexus_armor_layer_1
texture.netherite_layer_2=nexus_armor_layer_2
nbt.CustomModelData=17
```

**Test Steps:**
1. Give yourself helmet without CustomModelData: `/give @s netherite_helmet`
2. Equip the helmet
3. Verify default texture appears (not custom)

**Expected Result:** ✅ Default netherite texture displays

**Actual Result:** _[To be filled during testing]_

---

### Test 10: Performance Test

**Objective:** Verify that the fix doesn't cause performance issues.

**Test Steps:**
1. Create a resource pack with 50+ different armor sets using CustomModelData
2. Load the pack
3. Give yourself all armor pieces
4. Monitor FPS and game performance

**Expected Result:** ✅ No significant performance degradation

**Actual Result:** _[To be filled during testing]_

## Test Results Summary

| Test # | Test Name | Status | Notes |
|--------|-----------|--------|-------|
| 1 | Basic CustomModelData Matching | ⏳ Pending | |
| 2 | Multiple CustomModelData Values | ⏳ Pending | |
| 3 | All Armor Slots | ⏳ Pending | |
| 4 | Range Matching | ⏳ Pending | |
| 5 | Hex Value Matching | ⏳ Pending | |
| 6 | Backward Compatibility - display.Name | ⏳ Pending | |
| 7 | Backward Compatibility - display.Lore | ⏳ Pending | |
| 8 | Error Handling | ⏳ Pending | |
| 9 | No CustomModelData | ⏳ Pending | |
| 10 | Performance Test | ⏳ Pending | |

**Legend:**
- ⏳ Pending
- ✅ Passed
- ❌ Failed
- ⚠️ Partial Pass

## Bug Reporting

If any tests fail, document the following:
1. Test number and name
2. Expected result
3. Actual result
4. Steps to reproduce
5. Game logs (latest.log)
6. Screenshots (if applicable)

## Conclusion

Once all tests pass, the CustomModelData fix is ready for release.


