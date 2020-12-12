---
title: 'GLFP - Github Large Files Packer'
date: 2020-12-05 00:00:00
description: A quick overview of my two original bash scripts, which provide the capability to push large files to GitHub for free.
featured_image: '/images/projects/glfp/project_glfp.png'
---

### Why is it useful?
When using Github, I didn't like this annoying limitation of not being able to push files larger than 100MB to repositories.
The only way around was the Git LFS (Large Files Storage) system, which, in my opinion, is highly overpriced.

As the problem arose, I needed some type of solution without paying extra money (being a broken student is sometimes very tough),
so I've come up with two simple, but at the same time, efficient bash scripts.

### How it works in practise?
In the first place, clone my project repository, which consists of the discussed scripts:

```bash
git clone https://github.com/matthew01lokiet/Github-large-files-packer
```

But how it looks inside?

Let's go see it:

```bash
cd Github-large-files-packer/
ls -hl
```

<div style="text-align: center; color: black;"><b>Output:</b></div>
<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/output_of_ls.png">
</div>

<hr>
#### Quick tip:
If the names of scripts aren't in green color, it means we must make them executable:

```bash
sudo chmod +x pack unpack
```  
<hr>

Content is as it states:
- <span class="inline_text">pack</span> - bash executable script, which packs large file/folder; used before pushing to the repository
- <span class="inline_text">unpack</span> - bash executable script, which reverts packed file/folder to its initial form; used after fetching from the repository 

To make life easier, I will copy the scripts to <span class="file_path">/usr/local/bin/</span> folder, so they will become system-wide accessible:

```bash
sudo cp pack unpack /usr/local/bin/
```

#### Remember to restart the terminal in order to use them, because changes won't be applied to your current terminal session!

<hr>

### Test case
To show some real-world scenario, I will try to push the 4K video sample to the repository:

<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/test_case_mov.png">
</div>

Repo to be used:

<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/test_repo.png">
</div>

So let's clone it:

```bash
git clone https://github.com/matthew01lokiet/GLFP-Test
```

Current folder content:

<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/content_ins.png">
</div>

To include video sample in the repository and be able to push later, we make use of the <span class="inline_text">pack</span> script:

```bash
pack test.mov
```

<div style="text-align: center; color: black;"><b>Output:</b></div>
<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/after_split.png">
</div>

#### How it works underneath?
1. First of all, we are compressing the given file/folder into <span class="inline_text">.tar.gz</span> format.
2. Then it's being split into 100MB parts as binary code.
3. In the end, a new folder with the initial file/folder name is being created,and parts of the code are moved to it.

The next thing I will do is put <span class="inline_text">test.mov</span> in my repo folder and try to push:

<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/after_all.png">
</div>

Do not be afraid of the warnings, 100MB file size is still the largest possible to push without explicit need to use Github LFS.

<hr>
#### Quick tip:
But still, if these warnings make you feel uncomfortable, just change the given line in the <span class="inline_text">pack</span> script:

```bash
split -b 100M -d file.tar.gz file
```  

To:

```bash
split -b 50M -d file.tar.gz file
```
<hr>

How my packed 4K video sample looks like inside the repo:

<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/repo_one.png">
</div>

<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/repo_two.png">
</div>

As you might remember, the initial <span class="inline_text">test.mov</span> file size was 569MB, so it got partitioned
into five files of the size 100MB and one of the size 69MB (even less, if we take compression into the consideration).

Script automatically named partial files in the manner: <span class="inline_text">file + _next_partial_file_number_</span>.
Making use of this naming convention, the <span class="inline_text">unpack</span> script knows in which order it should concatenate partial
files, so they make up our compressed initial file/folder!

The last but not least, let's revert <span class="inline_text">test.mov</span> file, which current location is inside my local repository:

<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/first_step_revert.png">
</div>

<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/second_step_revert.png">
</div>

To revert file, issue the command in a terminal (your relative location should be inside folder, in which file/folder to be reverted exists):

```bash
unpack test.mov
```

<div style="text-align: center; color: black;"><b>Output:</b></div>
<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/unpack_output.png">
</div>

After script finished:

<div class="gallery" data-columns="1">
    <img src="/images/projects/glfp/folder_after_revert.png">
</div>

That's it, the same process goes for any other file type or folder.
You can further modify script, adding functionalities like encryption or recursive packing of files larger than 100MB in a given folder.

This whole project is all about idea, which could be implemented in many different ways, rather than focusing on one hard-coded solution.

I hope you enjoyed this quick overview; if you have any thoughts or ideas you would like to share, please do not hesitate to contact 
me through my <span class="inline_text">Contact</span> page or by using the icons on the bottom of a page.
