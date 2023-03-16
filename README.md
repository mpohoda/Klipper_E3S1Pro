# Klipper Install and Config for Ender 3 S1 Pro

Cura Start G-Code
```
SET_GCODE_VARIABLE MACRO=START_PRINT VARIABLE=bed_temp VALUE={material_bed_temperature_layer_0}
SET_GCODE_VARIABLE MACRO=START_PRINT VARIABLE=extruder_temp VALUE={material_print_temperature_layer_0}
START_PRINT
```

Cura End G-Code
```
SET_GCODE_VARIABLE MACRO=END_PRINT VARIABLE=machine_depth VALUE={machine_depth}
END_PRINT
```
