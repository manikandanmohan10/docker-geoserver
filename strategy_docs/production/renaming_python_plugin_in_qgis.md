To rename multiple fields at once in a QGIS vector layer, you can use the Python Console in QGIS with a script to rename each field in the attribute table. Here’s how you can do it:

# Open the Python Console:

Go to Plugins > Python Console to open the console in QGIS.
## Run a Python Script to Rename Fields:

Use the following script to rename multiple fields. Update the field_mapping dictionary with your current field names as keys and the desired new names as values.


```
# Get the active layer in QGIS
layer = iface.activeLayer()

# Start editing the layer
layer.startEditing()

# Define a dictionary with old field names as keys and new names as values
field_mapping = {
    'old_field_name1': 'new_field_name1',
    'old_field_name2': 'new_field_name2',
    'old_field_name3': 'new_field_name3'
}

# Loop through the mapping and rename fields
for old_name, new_name in field_mapping.items():
    # Get the index of the field to rename
    field_index = layer.fields().indexOf(old_name)
    
    # Check if the field exists
    if field_index != -1:
        # Rename the field
        layer.dataProvider().renameAttributes({field_index: new_name})
    else:
        print(f"Field '{old_name}' not found in the layer")

# Commit the changes to save renaming
layer.commitChanges()

print("Field renaming complete.")
```

## Adjust and Run:

Replace 'old_field_name1', 'new_field_name1', etc., with your actual field names.
Run the script in the Python Console. It will rename the specified fields in one go.

## Save Changes:

This script will automatically commit the changes after renaming. Your fields should now have the new names.
This approach is efficient for renaming multiple fields without having to manually edit each field name.
