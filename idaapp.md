To tackle this problem, we can create a Python class that performs scanning of a directory tree and lists its subfolders. We will then integrate this class with a simple user interface using the `tkinter` module to allow the user to select a folder and view images within it. Below is a complete implementation of the required functionality:

```python
import os
from tkinter import Tk, Label, Button, Listbox, filedialog, Scrollbar, RIGHT, Y, END
from PIL import Image, ImageTk


class FolderScanner:
    def __init__(self, root_path):
        self.root_path = root_path

    def scan_folders(self):
        subfolders = []
        for dirpath, dirnames, _ in os.walk(self.root_path):
            for dirname in dirnames:
                subfolders.append(os.path.join(dirpath, dirname))
        return subfolders

    def get_images(self, folder):
        supported_formats = [".jpg", ".jpeg", ".png", ".bmp", ".gif"]
        images = []
        for item in os.listdir(folder):
            if os.path.splitext(item)[1].lower() in supported_formats:
                images.append(os.path.join(folder, item))
        return images


def display_images(image_paths):
    top = Tk()
    top.title("Image Viewer")

    scrollbar = Scrollbar(top)
    scrollbar.pack(side=RIGHT, fill=Y)

    listbox = Listbox(top, yscrollcommand=scrollbar.set)
    for img_path in image_paths:
        listbox.insert(END, img_path)
    listbox.pack(fill="both", expand=True)

    scrollbar.config(command=listbox.yview)

    if image_paths:
        # Display the first image
        image = Image.open(image_paths[0])
        photo = ImageTk.PhotoImage(image)
        label = Label(top, image=photo)
        label.image = photo  # Keep a reference
        label.pack()

    top.mainloop()


def main():
    root = Tk()
    root.title("Folder Scanner")

    def browse_folder():
        folder_selected = filedialog.askdirectory()
        if folder_selected:
            scanner = FolderScanner(folder_selected)
            subfolders = scanner.scan_folders()
            image_folder = None
            if subfolders:
                for subfolder in subfolders:
                    images = scanner.get_images(subfolder)
                    if images:
                        image_folder = subfolder
                        display_images(images)
                        break
            if not image_folder:
                no_img_label = Label(root, text="No images found in subfolders")
                no_img_label.pack()

    browse_button = Button(root, text="Browse Folder", command=browse_folder)
    browse_button.pack(pady=20)

    root.mainloop()


if __name__ == "__main__":
    main()
```

### Explanation:
- **FolderScanner Class:**
  - `__init__`: Initializes the scanner with a root path.
  - `scan_folders`: Walks through the root path and lists all subfolders.
  - `get_images`: Lists images in a specified folder, checking for common image file extensions.
  
- **Main Program:**
  - The main function initializes a `tkinter` window allowing users to select a folder.
  - A simple UI is created with a button allowing users to browse and select a folder.
  - When a folder is selected, it uses `FolderScanner` to identify subfolders and retrieve image files from them.
  - Images are displayed using `tkinter` and `PIL`.

### Prerequisites:
- Python and the required libraries need to be installed (`Pillow` for handling images).
- Install `Pillow` via: `pip install pillow`.

This code provides a basic GUI to explore folders and view images, making use of Python's file handling and UI capabilities.
