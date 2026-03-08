# agentic-ai-learning

## To start juypter notebook

```
cd C:\dev\agentic-ai-learning
jupyter notebook
```

## Create env & requirements 
```
conda create -n venv python=3.10
conda activate venv
```
### commands
```
conda env list
```

## ollama local model list 
```
ollama list

```

```
ollama run llama3.2
```

```
ollama pull llama3.2
```
```
ollama pull moondream    # try first — 1.1GB. did not work with code
ollama pull gemma3:4b   # upgrade if needed — 2.7GB

```

```
ollama rm moondream
```

### code to downlad the zip file from deep learning notebook when downalod is not avaialble 

```
import zipfile
import os
# ─────────────────────────────────────────────────────────────
# HOW TO FIND THE RIGHT PATHS:
#
# Run this first in a Jupyter cell to find your working directory:
#   import os; print(os.getcwd())
#
# In this lab, it returned: /home/jovyan/work/M2/ungraded_labs/M2_UGL_1
# That means the Jupyter file browser root is: /home/jovyan/work/
# ─────────────────────────────────────────────────────────────
# The folder you want to zip (full absolute path)
# Change this to the folder you want to download
folder_to_zip = '/home/jovyan/work/M2/ungraded_labs/M2_UGL_1'
# Where to save the zip file.
# IMPORTANT: Save it inside the Jupyter root (/home/jovyan/work/)
# so it shows up in the file browser and can be downloaded.
# You can name the .zip file whatever you like.
output_zip = '/home/jovyan/work/M2_UGL_1.zip'
# ─────────────────────────────────────────────────────────────
# The "arcname base" controls the folder structure INSIDE the zip.
# os.path.relpath() strips the base path so you get clean relative paths.
# Example: if arcname_base = '/home/jovyan/work/M2/ungraded_labs'
#   then the file '/home/jovyan/work/M2/ungraded_labs/M2_UGL_1/utils.py'
#   becomes 'M2_UGL_1/utils.py' inside the zip.
# Set it to the PARENT of the folder you're zipping.
# ─────────────────────────────────────────────────────────────
arcname_base = '/home/jovyan/work/M2/ungraded_labs'
# Create the zip file
count = 0
with zipfile.ZipFile(output_zip, 'w', zipfile.ZIP_DEFLATED) as zipf:
    # os.walk() recursively visits every subfolder inside folder_to_zip
    for root, dirs, files in os.walk(folder_to_zip):
        for file in files:
            file_path = os.path.join(root, file)          # full path to file
            arcname = os.path.relpath(file_path, arcname_base)  # relative path inside zip
            zipf.write(file_path, arcname)
            count += 1
print(f'Files zipped: {count}')
print(f'ZIP saved to: {output_zip}')
print(f'ZIP size: {os.path.getsize(output_zip) / 1024:.1f} KB')

```

### instructions 
To adapt this for a different folder, just change 3 things:
#### 
1. folder_to_zip → the absolute path of the folder you want to zip (use os.getcwd() to orient yourself)
2. output_zip → where to save the zip, must be inside the Jupyter-visible root so you can download it
3. arcname_base → the parent of folder_to_zip, controls the folder structure inside the zip
To download after running:

#####
- Go to the Jupyter file browser root (/tree in the URL)
- Find your .zip file, click its checkbox
- A Download button will appear in the toolbar — click it