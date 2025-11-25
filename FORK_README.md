# CIT Resewn Fork - CustomModelData Fix for 1.21+

## About This Fork

This is a fork of [CIT Resewn](https://github.com/SHsuperCM/CITResewn) that restores support for `nbt.CustomModelData` matching in Minecraft 1.21+.

### Why This Fork Exists

The original CIT Resewn mod removed support for `nbt.CustomModelData` in version 1.21+ when Minecraft switched from NBT tags to the component system. This broke many existing CIT resource packs that relied on CustomModelData for custom armor textures.

This fork adds legacy support for `nbt.CustomModelData`, allowing it to work alongside the new component system.

## What's Changed

### Added Features
- ✅ **Legacy `nbt.CustomModelData` support** - Properties files can now use `nbt.CustomModelData=17` syntax
- ✅ **Backward compatible** - Existing CIT packs using `display.Name` and `display.Lore` continue to work
- ✅ **Warning messages** - Logs a warning when legacy NBT syntax is used, encouraging migration to component syntax

### Technical Changes
- Modified `ConditionComponents.java` to add `CustomModelData` to the legacy NBT conversion list
- The component `minecraft:custom_model_data` is now recognized and matched correctly
- Integer matching works as expected (e.g., `nbt.CustomModelData=17`)

## Installation

### For Players

1. Download the latest release from the [Releases](https://github.com/ItsCerv/CITResewn/releases) page
2. Install Fabric Loader and Fabric API for Minecraft 1.21.1+
3. Place the downloaded JAR file in your `mods` folder
4. Launch Minecraft

### For Developers

See [DEVELOPMENT_GUIDE.md](DEVELOPMENT_GUIDE.md) for setup instructions.

## Usage

### Example CIT Properties File

```properties
type=armor
items=netherite_helmet netherite_chestplate netherite_leggings netherite_boots
texture.netherite_layer_1=custom_armor_layer_1
texture.netherite_layer_2=custom_armor_layer_2
nbt.CustomModelData=17
```

### Give Command

```
/give @s netherite_helmet[custom_model_data=17]
```

When you equip the helmet, it will display your custom texture.

## Documentation

- **[CUSTOM_MODEL_DATA_FIX_SPECIFICATION.md](CUSTOM_MODEL_DATA_FIX_SPECIFICATION.md)** - Technical specification of the fix
- **[DEVELOPMENT_GUIDE.md](DEVELOPMENT_GUIDE.md)** - Guide for setting up a development environment
- **[TESTING_PLAN.md](TESTING_PLAN.md)** - Comprehensive testing plan

## Compatibility

- **Minecraft:** 1.21.1+
- **Fabric Loader:** 0.16.5+
- **Fabric API:** Latest version
- **Java:** 21+

## Known Issues

None currently. Please report any issues on the [Issues](https://github.com/ItsCerv/CITResewn/issues) page.

## Contributing

Contributions are welcome! Please:
1. Fork this repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## Credits

- **Original CIT Resewn:** [SHsuperCM](https://github.com/SHsuperCM)
- **CustomModelData Fix:** ItsCerv
- **Documentation:** AI Assistant

## License

This project inherits the license from the original CIT Resewn project.

## Support

If you encounter issues:
1. Check the [TESTING_PLAN.md](TESTING_PLAN.md) to verify your setup
2. Review the [DEVELOPMENT_GUIDE.md](DEVELOPMENT_GUIDE.md) for troubleshooting
3. Open an issue on GitHub with:
   - Minecraft version
   - CIT Resewn version
   - Resource pack structure
   - Game logs (latest.log)
   - Screenshots

## Roadmap

- [x] Add legacy `nbt.CustomModelData` support
- [ ] Test with various resource packs
- [ ] Optimize performance
- [ ] Add support for complex CustomModelData (floats, strings, colors)
- [ ] Create example resource packs
- [ ] Submit pull request to original repository (if desired)

## Acknowledgments

Thanks to:
- The Fabric modding community
- The original CIT Resewn developers
- Everyone who uses and supports this mod

---

**Note:** This is an unofficial fork. For the official CIT Resewn mod, visit [https://github.com/SHsuperCM/CITResewn](https://github.com/SHsuperCM/CITResewn)


