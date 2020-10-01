# Create SVG Icon with path info

A search on google did not provide the svg path information for the [YourAcclaim](https://www.youracclaim.com/) logo.
And I wanted the svg icon for that logo to add to my [github website](https://vidyabhandary.github.io/). 

So had to get the path information from an exisiting image.

## Following are the steps to extract the svg 'd' path from an image.

### 1. Export the logo image as a `.svg` file using any tool of your choice

### 2. Import the `.svg` file in GIMP and when the following dialog box appears - check these two options
    
- Import Paths
- Merge imported paths options

    ![](/images/svg_ImportPath.png)


### 3. Go to the 'Paths' Dockable Dialogue Box via Windows-> Dockable Dialogues-> Paths

### 4. Right-click on 'Imported Path'. In the dialog box that appears, choose 'Export Path'

![](/images/svg_right_click.png)

### 5. Save the path information into a text editor

### 6. Extract the path within the `<path d="path info" />` and copy it into the final icon svg within the `path` tag.

### 7. Clean up the svg path
- Clean the svg path for e.g. remove the background or make minor changes to the svg path using this [online free site](https://www.freecodeformat.com/svg-editor.php)
 
### 8. The final svg icon on my github website

![](/images/svg_icon.png)


**Ref :**
* [Stackoverflow](https://stackoverflow.com/questions/11529470/is-there-a-tool-to-create-svg-paths-from-an-svg-file)
* [SVG Viewer/Editor](https://www.freecodeformat.com/svg-editor.php) 
