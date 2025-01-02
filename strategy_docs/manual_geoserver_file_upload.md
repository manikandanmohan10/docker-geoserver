
# Manual upload.


## Check the field name is correct

Run the below script, 

```
import os
import zipfile
import geopandas as gpd

# Rename the attributes

zip_path = input("Enter the path of the ZIP file ")
file_name = input("File name you are processing ")
extracted_dir = file_name + '_extracted_files'


if not os.path.exists(extracted_dir):
    os.makedirs(extracted_dir)

def process_shapefile(zip_path, extracted_dir, file_name):
    # Extract the zip file
    with zipfile.ZipFile(zip_path, 'r') as zip_ref:
        zip_ref.extractall(extracted_dir)

    extracted_file_path = extracted_dir + "/" + file_name
    output_file_path = "output/" + file_name

    if not os.path.exists(output_file_path):
        os.makedirs(output_file_path)

    for file_name in os.listdir(extracted_file_path):
        if file_name.endswith('.shp'):
            print(".... Processing ", file_name, " ...")
            cur_accessing_file = os.path.join(extracted_file_path, file_name)
            gdf = gpd.read_file(cur_accessing_file)
            gdf.columns = gdf.columns.str.replace(' ', '_')
            cur_accessing_output_file_path = os.path.join(output_file_path, file_name) 
            gdf.to_file(cur_accessing_output_file_path)

    print("Completed file is created")

process_shapefile(zip_path, extracted_dir, file_name)
```

This will change the field name in correct format.

### Move your file to server:
  
/home/ubuntu/docker-geoserver/geoserver_upload_datas

  
### Steps:
1) create workspace
2) create a store using  Directory of spatial files (shapefiles) 
3) Publish layer
4) open layer preview and check
