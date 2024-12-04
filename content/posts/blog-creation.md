---
title: My first blog with Obsidian, Hugo, GitHub and Hostinger
date: 2024-12-01
tags:
  - setup
  - general
---
# Hi there, welcome to my first blog post.

Today we will create a pipeline for a blog written in Obsidian and will be hosted on Hostinger using GitHub. I use Arch Linux and the setup will be mainly for Linux/Mac but it can adapted for the Windows easily.

# Outline
1. Set up my blog file structure in Obsidian
2. Install Hugo and selecting a theme for the blog
3. Create script for copying the images from Obsidian to the blog
4. Host the blog with the Hostinger

It will have two sets of scripts because the file paths are different:
1. For my laptop;
2. For my phone with Termux.

This setup was based from [Network Chuck ](https://youtu.be/dnE7c0ELEH8?si=hidXJO-ob1gs29Nm)

## The tools that will be used:
* [Obsidian](https://obsidian.md/)
* [Hugo](https://gohugo.io/)
* [GitHub](https://github.com/)
* [Hostinger - 20% off referal link](https://cart.hostinger.com/pay/ab55defd-71cc-48cc-a6d6-b7b64a7c0b10?_ga=GA1.3.942352702.1711283207)
* Python and bash scripts for automation

## Obsidian

In a Obsidian vault create a "posts" folder that will be used to for the blog posts.
In my Obsidian vault I will create a *`posts`* folder for my blog posts and an *`attachments`*
folder for my images.
Consider to create a specific vault for this project. 

## Hugo

### First install Hugo using the following command from the Hugo installation guide [page](https://gohugo.io/installation/linux/):

```sh
sudo pacman -S hugo
```

### Create a new site:

```sh
# Verify if Hugo works
hugo version

# Create a new site
hugo new site websitename
```

### Download a Hugo theme
- Find themes from this link: [https://themes.gohugo.io/](https://themes.gohugo.io/)
	-  follow the theme instructions  on how to download. The BEST option is to install as a git submodule;
	-  configure the site how you like it using the instructions from the theme page.
- For me I will use [Beautiful Hugo theme](https://github.com/halogenica/beautifulhugo)
### Test Hugo

```bash
## Verify Hugo works with your theme by running this command

hugo server -t themename
```


## Syncing Obsidian to Hugo using rsync

```bash
rsync -av --delete "sourcepath" "destinationpath"
```

## Transfer images from Obsidian to Hugo

Because the images saved in Obsidian Vault are saved in a separate folder I need to copy the images from that folder to the Hugo blog. For this I will use a Python script named *"images.py"*.

```python
import os
import re
import shutil

# Paths
posts_dir = "/home/uam/Sync/CodingNinjaJourney/content/posts/" # My blog posts directory
attachments_dir = "/home/uam/Sync/ObsidianVaults/Personal/attachments/" # My Obsidian images folder
static_images_dir = "/home/uam/Sync/CodingNinjaJourney/static/images/" # My blog images folder

# Step 1: Process each markdown file in the posts directory
for filename in os.listdir(posts_dir):
    if filename.endswith(".md"):
        filepath = os.path.join(posts_dir, filename)

        with open(filepath, "r") as file:
            content = file.read()

        # Step 2: Find all image links in the format ![Image Description](/images/Pasted%20image%20...%20.png)
        images = re.findall(r"\[\[([^]]*\.jpg)\]\]", content)

        # Step 3: Replace image links and ensure URLs are correctly formatted
        for image in images:
            # Prepare the Markdown-compatible link with %20 replacing spaces
            markdown_image = f"[Image Description](/images/{image.replace(' ', '%20')})"
            content = content.replace(f"[[{image}]]", markdown_image)

            # Step 4: Copy the image to the Hugo static/images directory if it exists
            image_source = os.path.join(attachments_dir, image)
            if os.path.exists(image_source):
                shutil.copy(image_source, static_images_dir)

        # Step 5: Write the updated content back to the markdown file
        with open(filepath, "w") as file:
            file.write(content)

print("Markdown files processed and images copied successfully.")
```

To Do:
[ ] Save the blog to the *'Documents'* folder so that I don't have errors with the Syncthing app
[ ] Redo the scripts to reflect the changes 
